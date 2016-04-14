<properties
	pageTitle="Ejecución del asistente para habilitar la base de datos para Stretch | Microsoft Azure"
	description="Obtenga información sobre la configuración de una base de datos para Stretch Database ejecutando el asistente para habilitar la base de datos para Stretch."
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

# Ejecución del asistente para habilitar la base de datos para Stretch

Para configurar una base de datos para Stretch Database, ejecute el asistente para habilitar la base de datos para Stretch. Este tema describe la información que tiene que especificar y las elecciones que debe tomar en el asistente.

Para obtener más información sobre Stretch Database, vea [Stretch Database](sql-server-stretch-database-overview.md).

## Inicio del asistente

1.  En SQL Server Management Studio, en el Explorador de objetos, seleccione la base de datos en la que desee habilitar Stretch.

2.  Haga clic con el botón derecho y seleccione **Tareas**, **Stretch** y **Habilitar**para iniciar el asistente.

## <a name="Intro"></a>Introducción
Consulte el propósito del asistente y los requisitos previos.

## <a name="Tables"></a>Selección de tablas
Seleccione las tablas que desee habilitar para Stretch.

|Columna|Descripción|
|----------|---------------|
|(sin título)|Active la casilla de esta columna para habilitar la tabla seleccionada para Stretch.|
|**Name**|Especifica el nombre de la columna en la tabla.|
|(sin título)|Un símbolo en esta columna indica normalmente que no puede habilitar la tabla seleccionada para Stretch debido a un problema de bloqueo. Esto puede deberse a que la tabla usa un tipo de datos no compatible. Mantenga el mouse sobre el símbolo para mostrar más información en una información sobre herramientas. Para obtener más información, vea [Limitaciones de área expuesta y problemas de bloqueo de Stretch Database](sql-server-stretch-database-limitations.md).|
|**Extendida**|Indica si la tabla ya está habilitada.|
|**Filas**|Especifica el número de filas de la tabla.|
|**Tamaño (KB)**|Especifica el tamaño de la tabla en KB.|
|**Migrar**|En CTP 3.1 a través de RC0, solo puede migrar una tabla completa mediante el asistente. Si desea especificar un predicado para seleccionar las filas que migrar desde una tabla que contenga los datos históricos y actuales, ejecute la instrucción ALTER TABLE para especificar un predicado después de salir del asistente. Para obtener más información, vea [Habilitación de Stretch Database para una tabla](sql-server-stretch-database-enable-table.md) o [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|

## <a name="Configure"></a>Configuración de la implementación de Azure

1.  Inicie sesión en Microsoft Azure con una cuenta de Microsoft.

2.  Seleccione la suscripción de Azure que usar para Stretch Database.

3.  Seleccione una región de Azure. Si crea un nuevo servidor, el servidor se crea en esta región.

    Para minimizar la latencia, seleccione la región de Azure en la que se encuentra SQL Server. Para obtener más información acerca de las regiones, consulte [Regiones de Azure](https://azure.microsoft.com/regions/).

4.  Especifique si desea usar un servidor existente o crear un nuevo servidor de Azure.

    Si Active Directory en SQL Server está federado con Azure Active Directory, también puede usar una cuenta de servicio federado para SQL Server para comunicarse con el servidor remoto de Azure. Para obtener más información sobre los requisitos para esta opción, consulte [ALTER DATABASE SET Options (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

    -   **Servidor existente**

        1.  Seleccione o escriba el nombre del servidor de Azure existente.

        2.  Seleccione el método de autenticación

            -   Si selecciona **Autenticación de SQL Server**, cree un nuevo inicio de sesión y contraseña.

            -   Seleccione **Autenticación integrada de Active Directory** para usar una cuenta de servicio federado para que SQL Server se comunique con el servidor remoto de Azure.

    -   **Creación de un servidor nuevo**

        1.  Cree un inicio de sesión y una contraseña para el administrador del servidor.

        2.  Opcionalmente, use una cuenta de servicio federado para que SQL Server se comunique con el servidor remoto de Azure.

## <a name="Credentials"></a>Protección de credenciales
Escriba una contraseña segura para crear una clave maestra de base de datos, o si ya existe una clave maestra de base de datos, escriba la contraseña para ella.

Debe tener una clave maestra de base de datos para proteger las credenciales que Stretch Database usa para conectarse a la base de datos remota.

Para obtener más información sobre la clave maestra de base de datos, vea [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) y [Creación de una clave maestra de base de datos](https://msdn.microsoft.com/library/aa337551.aspx). Para obtener más información sobre la credencial que crea el asistente, consulte [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Selección de la dirección IP
Use la dirección IP pública de SQL Server, o escriba un intervalo de direcciones IP, para crear una regla de firewall en Azure que permita a SQL Server comunicarse con el servidor remoto de Azure.

## <a name="Summary"></a>Resumen
Consulte los valores que escribió y las opciones que seleccionó en el asistente. A continuación, seleccione **Finalizar** para habilitar Stretch.

## <a name="Results"></a>Resultados
Consulte los resultados.

Opcionalmente, seleccione **Monitor** para iniciar el monitor del estado de la migración de datos en Stretch Database Monitor. Para obtener más información, vea [Supervisión y solución de problemas de migración de datos (Stretch Database)](sql-server-stretch-database-monitor.md).

## <a name="KnownIssues"></a>Solución de problemas del asistente
**Error en el asistente de Stretch Database** Si Stretch Database aún no se ha habilitado aún en el nivel de servidor y ejecuta el asistente sin los permisos del administrador del sistema para habilitarlo, se produce un error en el asistente. Solicite al administrador del sistema que habilite Stretch Database en la instancia del servidor local y, a continuación, ejecute de nuevo el asistente. Para obtener más información, vea [Requisito previo: Permiso para habilitar Stretch Database en el servidor](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## Pasos siguientes
Habilitación tablas adicionales para Stretch Database Supervisión de la migración de datos y administración de tablas y bases de datos habilitadas para Stretch

-   [Habilitación de Stretch Database para una tabla](sql-server-stretch-database-enable-table.md) para habilitar las tablas adicionales

-   [Supervisión de Stretch Database](sql-server-stretch-database-monitor.md) para ver el estado de la migración de datos

-   [Pausa y reanudación Stretch Database](sql-server-stretch-database-pause.md)

-   [Administración y solución de problemas de Stretch Database](sql-server-stretch-database-manage.md)

-   [Copia de seguridad y restauración de bases de datos habilitadas para Stretch](sql-server-stretch-database-backup.md)

## Consulte también
[Habilitación de Stretch Database para una base de datos](sql-server-stretch-database-enable-database.md) [Habilitación de Stretch Database para una tabla](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0302_2016-->