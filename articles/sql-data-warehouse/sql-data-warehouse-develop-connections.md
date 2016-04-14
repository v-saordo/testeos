<properties
   pageTitle="Conexión a Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para conectarse a Almacenamiento de datos SQL para el desarrollo de soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Conexión a Almacenamiento de datos SQL 
Para conectarse a Almacenamiento de datos SQL, deberá pasar las credenciales de seguridad para realizar la autenticación. Después de establecer una conexión, también encontrará que determinados valores de conexión se configuran como parte del establecimiento de la sesión de la consulta.

En este artículo se describen los aspectos siguientes en relación con la conexión a Almacenamiento de datos SQL:

- Autenticación
- Configuración de conexión
- Sesiones e identificadores de solicitud


## Autenticación
Para conectarse a Almacenamiento de datos SQL, deberá proporcionar la siguiente información:

- Nombre de servidor completo 
- Especificar la autenticación de SQL
- Nombre de usuario 
- Password
- Base de datos predeterminada (opcional)

Es importante tener en cuenta que los usuarios deben autenticarse mediante autenticación de SQL. No se admite la autenticación de confianza en este momento.

De forma predeterminada, su conexión se realizará a la base de datos principal y no a su base de datos de usuario. Para conectarse a la base de datos de usuario puede hacer dos cosas:

1. Especificar la base de datos predeterminada al registrar el servidor con el Explorador de objetos de SQL Server en SSDT o en la cadena de conexión de la aplicación. Por ejemplo, incluyendo el parámetro InitialCatalog para una conexión ODBC.
2. En primer lugar, resalte la base de datos de usuario antes de crear una sesión en SSDT.

> [AZURE.NOTE]Para obtener instrucciones sobre cómo conectarse a Almacenamiento de datos SQL con SSDT, vuelva al artículo de introducción a [conexiones y consultas][].

De nuevo, es importante tener en cuenta que la instrucción de Transact-SQL **USE <your DB>** no se admite para cambiar la base de datos en una conexión

## Protocolos de conexión de aplicación
Puede conectarse a Almacenamiento de datos SQL mediante cualquiera de los siguientes protocolos:

- ADO.NET
- ODBC
- PHP
- JDBC

### Cadena de conexión ADO.NET de ejemplo

```
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### Cadena de conexión ODBC de ejemplo

```
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### Cadena de conexión PHP de ejemplo

```
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### Cadena de conexión JDBC de ejemplo

```
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## Configuración de conexión
Almacenamiento de datos SQL estandariza algunas opciones de configuración durante la creación de conexiones y objetos. Estas opciones no se pueden invalidar.

| Configuración de base de datos | Valor |
| :----------------- | :--------------------------- |
| ANSI\_NULLS | ACTIVAR |
| QUOTED\_IDENTIFIERS | ACTIVAR |
| NO\_COUNT | DESACTIVAR |
| DATEFORMAT | mdy |
| DATEFIRST | 7 |
| Intercalación de base de datos | SQL\_Latin1\_General\_CP1\_CI\_AS |

## Sesiones y solicitudes
Una vez que se ha realizado una conexión y se ha establecido una sesión, está listo para escribir y enviar consultas a Almacenamiento de datos SQL.

Cada consulta se representa mediante uno o varios identificadores de solicitud. Todas las consultas enviadas en esa conexión forman parte de una sola sesión y, por lo tanto, estarán representadas por un identificador de sesión único.

Sin embargo, como Almacenamiento de datos SQL es un sistema MPP distribuido, tanto los identificadores de sesión como los de solicitud se exponen de manera algo diferente en comparación con SQL Server.

Las sesiones y las solicitudes se representan con sus identificadores respectivos.
	
| Identificador | Valor de ejemplo |
| :--------- | :------------ |
| Id. de sesión | SID123456 |
| Id. de solicitud | QID123456 |

Observe que el Id. de sesión está precedido por SID, la forma corta de Session ID, y que las solicitudes están precedidas por QID, la forma corta de Query ID.

Necesitará esta información para ayudarle a identificar la consulta al supervisar el rendimiento de las consultas. Puede supervisar el rendimiento de las consultas mediante el [Portal de Azure] y las vistas de administración dinámica.

Para identificar qué sesión usa actualmente la siguiente función:

```
SELECT SESSION_ID()
;
```

Para ver todas las consultas que están en ejecución o que se han ejecutado recientemente en el almacenamiento de datos, puede usar una consulta como la siguiente:

```
CREATE VIEW dbo.vSessionRequests
AS
SELECT 	 s.[session_id]									AS Session_ID
		,s.[status]										AS Session_Status
		,s.[login_name]									AS Session_LoginName
		,s.[login_time]									AS Session_LoginTime
        ,r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
		,r.[end_compile_time]							AS Request_EndCompileTime
		,r.[end_time]									AS Request_EndTime
		,r.[total_elapsed_time]							AS Request_TotalElapsedDuration_ms
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,DATEDIFF(ms,[start_time],[end_compile_time])	AS Request_CompileDuration_ms
        ,DATEDIFF(ms,[end_compile_time],[end_time])		AS Request_ExecDuration_ms
		,[label]										AS Request_QueryLabel
		,[command]										AS Request_Command
		,[database_id]									AS Request_Database_ID
FROM    sys.dm_pdw_exec_requests r
JOIN    sys.dm_pdw_exec_sessions s	ON	r.[session_id] = s.[session_id]
WHERE   s.[session_id] <> SESSION_ID()
;
```

Tenga en cuenta que esta consulta se han encapsulado en una vista para que pueda incorporarla a su solución. No obstante, para ver los resultados deberá crear la vista y ejecutarla.

## Pasos siguientes
Una vez conectado, puede empezar a diseñar las tablas. Consulte el artículo sobre el [diseño de tablas] para obtener más detalles.

<!--Image references-->

<!--Azure.com references-->
[conexiones y consultas]: ./sql-data-warehouse-get-started-connect.md
[diseño de tablas]: ./sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!--Other references-->

<!---HONumber=AcomDC_0114_2016-->