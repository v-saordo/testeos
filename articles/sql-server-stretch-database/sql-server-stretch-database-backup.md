<properties
	pageTitle="Copia de seguridad y restauración de bases de datos habilitadas para Stretch | Microsoft Azure"
	description="Aprenda a realizar una copia de seguridad y restaurar bases de datos habilitadas para Stretch."
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


# Copia de seguridad y restauración de bases de datos habilitadas para Stretch 

Para realizar la copia de seguridad y la restauración de bases de datos habilitadas para Stretch, puede seguir usando los métodos que utiliza actualmente. Para obtener más información acerca de la copia de seguridad y la restauración de SQL Server, consulte [Copias de seguridad y restauración de Bases de datos de SQL Server](https://msdn.microsoft.com/library/ms187048.aspx).

Una copia de seguridad de una base de datos habilitada para Stretch es una copia de seguridad superficial que no incluye los datos migrados al servidor remoto.

Stretch Database proporciona compatibilidad total con la restauración a un momento dado. Después de restaurar la Base de datos de SQL Server a un momento dado y autorizar de nuevo la conexión a Azure, Stretch Database concilia los datos remotos en el mismo momento dado. Para obtener más información sobre la restauración a un momento dado en SQL Server, vea [Restauración de Base de datos de SQL Server a un momento dado (modelo de recuperación completa)](https://msdn.microsoft.com/library/ms179451.aspx). Para obtener información sobre el procedimiento almacenado que debe ejecutar después de una restauración para autorizar de nuevo la conexión a Azure, vea [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx).

## <a name="Reconnect"></a>Restauración de una base de datos habilitada para Stretch desde una copia de seguridad

1.  Restaure la base de datos desde una copia de seguridad.

2.  Cree una clave maestra de base de datos para la base de datos habilitada para Stretch. Para obtener más información, vea [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx).

3.  Cree una credencial con ámbito de base de datos para la base de datos habilitada para Stretch. Para obtener más información, vea [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

4.  Ejecute el procedimiento almacenado [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx) para volver a conectar la Base de datos habilitada para Stretch habilitada local a Azure. Proporcione la credencial con ámbito de base de datos creada en el paso anterior como un valor sysname o varchar (128). (No se varchar(max).)

    ```tsql
    Declare @credentialName nvarchar(128);
    SET @credentialName = N’<database_scoped_credential_name_created_previously>’;
    EXEC sp_rda_reauthorize_db @credentialName;
    ```

## <a name="MoreInfo"></a>Más información sobre copias de seguridad y restauración
Las copias de seguridad en bases de datos con Stretch Database habilitado contienen únicamente datos locales y datos elegibles en un momento dado cuando se ejecuta la copia de seguridad. Estas copias de seguridad también contienen información sobre el punto de conexión remoto en el que residen los datos remotos de la base de datos. A esto se le conoce como "copia de seguridad superficial". No son compatibles las copias de seguridad en profundidad que contienen todos los datos de la base de datos , locales y remotas.

![Diagrama de copia de seguridad de Stretch Database][StretchBackupImage1]

Cuando se restaura una copia de seguridad de una base de datos con Stretch Database habilitado, esta operación restaura los datos locales y elegibles en la base de datos según lo esperado. (Los datos elegibles son datos que aún no se han movido, pero que se moverán a Azure según la configuración de Stretch Database de las tablas). Después de ejecutar la operación de restauración, la base de datos contiene datos locales y elegibles a partir del momento en el que se ejecutó la copia de seguridad, pero no tiene las credenciales necesarias y los artefactos para conectarse al punto de conexión remoto.

![Stretch Database después de la copia de seguridad][StretchBackupImage2]

Debe ejecutar el procedimiento almacenado **sys.sp\_rda\_reauthorize\_db** para volver a establecer la conexión entre la base de datos local y el punto de conexión remoto. Solo db\_owner puede realizar esta operación. Este procedimiento almacenado también requiere la el nombre de usuario administrador y la contraseña del punto de conexión remoto. Esto significa que tendrá que proporcionar los datos de inicio de sesión del administrador y la contraseña para el servidor de Azure de destino.

![Stretch Database después de la copia de seguridad][StretchBackupImage3]

Después de volver a establecer la conexión, Stretch Database intenta conciliar los datos elegibles en la base de datos local con datos remotos mediante la creación de una copia de los datos remotos en el punto de conexión remoto y su vinculación con la base de datos local. Este proceso es automático y no requiere la intervención del usuario. Después de que se ejecute la conciliación, la base de datos local y el punto de conexión remoto se encuentran en un estado coherente.

![Stretch Database después de la copia de seguridad][StretchBackupImage4]

## Consulte también
[Administración y solución de problemas de Stretch Database](sql-server-stretch-database-manage.md) [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx) [Copia de seguridad y restauración de Bases de datos de SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)

<!--Image references-->
[StretchBackupImage1]: ./media/sql-server-stretch-database-backup/StretchDBBackup1.png
[StretchBackupImage2]: ./media/sql-server-stretch-database-backup/StretchDBBackup2.png
[StretchBackupImage3]: ./media/sql-server-stretch-database-backup/StretchDBBackup3.png
[StretchBackupImage4]: ./media/sql-server-stretch-database-backup/StretchDBBackup4.png

<!---HONumber=AcomDC_0302_2016-->