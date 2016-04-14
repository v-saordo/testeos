<properties
	pageTitle="Habilitación de Stretch Database para una base de datos | Microsoft Azure"
	description="Obtenga información acerca de cómo configurar una base de datos para Stretch Database."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Habilitación de Stretch Database para una base de datos

Para configurar una base de datos para Stretch Database, seleccione **Tareas | Stretch | Habilitar** en una base de datos en SQL Server Management Studio para abrir el asistente para **habilitar la base de datos para Stretch**. También puede utilizar Transact-SQL para habilitar Stretch Database para una base de datos.

Si selecciona **Tareas | Stretch | Habilitar** para una tabla y no ha habilitado todavía la base de datos para Stretch Database, el asistente configura la base de datos para Stretch Database y le permite configurar tablas como parte del proceso. Siga los pasos de este tema en lugar de los pasos de [Habilitación de Stretch Database para una tabla](sql-server-stretch-database-enable-database.md).

La habilitación de Stretch Database en una base de datos o una tabla requiere permisos db\_owner. La habilitación de Stretch Database en una base de datos también requiere permisos CONTROL DATABASE.

## Antes de comenzar

-   Antes de configurar una base de datos para Stretch, se recomienda ejecutar el Asesor de Stretch Database para identificar las bases de datos y las tablas que son elegibles para el ajuste. El Asesor de Stretch Database también identifica los problemas de bloqueo. Para obtener más información, vea [Identificación de bases de datos y tablas para Stretch Database](sql-server-stretch-database-identify-databases.md).

-   Consulte [Limitaciones de área expuesta y problemas de bloqueo de Stretch Database](sql-server-stretch-database-limitations.md).

-   Stretch Database migra los datos a Azure. Por lo tanto, es necesario tener una cuenta de Azure y una suscripción para la facturación. Para obtener una cuenta de Azure, [haga clic aquí](http://azure.microsoft.com/pricing/free-trial/).

-   Tenga la información que necesita para crear una nueva base de datos remoto o seleccione una base de datos remota, y para crear una regla de firewall que permita que el servidor local se comunique con el servidor remoto.

## <a name="EnableTSQLServer"></a>Requisito previo: Permiso para habilitar Stretch Database en el servidor
Para poder habilitar Stretch Database en una base de datos o una tabla, tendrá que habilitarlo en el servidor local. Esta operación requiere permisos sysadmin o serveradmin.

-   Si tiene los permisos administrativos necesarios, el asistente para **habilitar la base de datos para Stretch** configura el servidor para Stretch.

-   Si no tiene los permisos necesarios, un administrador debe habilitar manualmente la opción ejecutando **sp\_configure** para ejecutar el asistente, o un administrador debe ejecutar el asistente.

Para habilitar Stretch Database en el servidor manualmente, ejecute **sp\_configure** y active la opción de **archivo de datos remotos**. En el ejemplo siguiente se habilita la opción de **archivo de datos remotos** estableciendo su valor en 1.

```
EXEC sp_configure 'remote data archive' , '1';
GO
RECONFIGURE;
GO
```
Para obtener más información, vea [Establecimiento de la opción de configuración del servidor del archivo de datos remotos](https://msdn.microsoft.com/library/mt143175.aspx) y [sp\_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx).

## <a name="Wizard"></a>Uso del asistente para habilitar Stretch Database en una base de datos
Para obtener información acerca del asistente para habilitar la base de datos para Stretch, incluida la información que se debe especificar y las opciones que se deben realizar, vea [Asistente para habilitar la base de datos para Stretch ](sql-server-stretch-database-wizard.md).

## <a name="EnableTSQLDatabase"></a>Uso de Transact-SQL para habilitar Stretch Database en una base de datos
Para poder habilitar Stretch Database en tablas individuales, tendrá que habilitarlo en la base de datos.

La habilitación de Stretch Database en una base de datos o una tabla requiere permisos db\_owner. La habilitación de Stretch Database en una base de datos también requiere permisos CONTROL DATABASE.

1.  Antes de comenzar, elija un servidor de Azure existente para los datos que migra Stretch Database, o cree un nuevo servidor.

2.  En el servidor de Azure, cree una regla de firewall con la dirección IP (o el intervalo de direcciones IP) de SQL Server que permita a SQL Server comunicarse con el servidor remoto.

3.  Para configurar una base de datos de SQL Server para Stretch Database, la base de datos debe tener una clave maestra de base de datos. La clave maestra de base de datos protege las credenciales que Stretch Database usa para conectarse a la base de datos remota. Para crear la clave maestra de la base de datos manualmente, vea [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) y [Creación de una clave maestra de base de datos](https://msdn.microsoft.com/library/aa337551.aspx).

    ```tsql
    USE <database>
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD='<password>'
    ```

4.  Cuando configure una base de datos para Stretch Database, tendrá que proporcionar una credencial para Stretch Database use para la comunicación entre el servidor de Azure remoto y SQL Server local. Tiene dos opciones:

    -   Puede proporcionar una credencial de administrador.

        -   Si habilita Stretch Database ejecutando el asistente, puede crear la credencial en ese momento.

        -   Si habilita Stretch Database ejecutando **ALTER DATABASE**, tendrá que crear manualmente la credencial para habilitar Stretch Database.

        Para crear la credencial manualmente, consulte [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx). La creación de la credencial requiere permisos ALTER ANY CREDENTIAL.

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>'
        GO
        ```

    -   Puede usar una cuenta de servicio federado para que SQL Server se comunique con el servidor remoto de Azure cuando se cumplan todas las condiciones siguientes.

        -   La cuenta de servicio en la que se ejecuta la instancia de SQL Server es una cuenta de dominio.

        -   La cuenta de dominio pertenece a un dominio cuyo Active Directory está federado con Azure Active Directory.

        -   El servidor remoto de Azure está configurado para admitir la autenticación de Azure Active Directory.

        -   La cuenta de servicio en la que se ejecuta la instancia de SQL Server debe configurarse como una cuenta dbmanager o sysadmin en el servidor remoto de Azure.

5.  Para configurar una base de datos para Stretch Database, ejecute el comando ALTER DATABASE.

    1.  Para el argumento SERVER, proporcione el nombre de un servidor existente de Azure, incluida la `.database.windows.net` parte del nombre; por ejemplo, `MyStretchDatabaseServer.database.windows.net`.

    2.  Proporcione una credencial de administrador existente con el argumento CREDENTIAL o especifique FEDERATED\_SERVICE\_ACCOUNT \\ = ON. En el ejemplo siguiente se proporciona una credencial existente.

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = <server_name> ,
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO;
    ```

## Pasos siguientes
Habilitación tablas adicionales para Stretch Database Supervisión de la migración de datos y administración de tablas y bases de datos habilitadas para Stretch

-   [Habilitación de Stretch Database para una tabla](sql-server-stretch-database-enable-table.md) para habilitar las tablas adicionales

-   [Supervisión de Stretch Database](sql-server-stretch-database-monitor.md) para ver el estado de la migración de datos

-   [Pausa y reanudación Stretch Database](sql-server-stretch-database-pause.md)

-   [Administración y solución de problemas de Stretch Database](sql-server-stretch-database-manage.md)

-   [Copia de seguridad y restauración de bases de datos habilitadas para Stretch](sql-server-stretch-database-backup.md)

## Consulte también
[Identificación de bases de daos y tablas para Stretch Database](sql-server-stretch-database-identify-databases.md) [ALTER DATABASE SET Options (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

<!---HONumber=AcomDC_0302_2016-->