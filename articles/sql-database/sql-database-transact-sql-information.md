<properties
   pageTitle="Incompatible en T-SQL de Base de datos SQL de Azure | Microsoft Azure"
   description="Instrucciones de Transact-SQL que no son totalmente compatibles con la Base de datos SQL de Azure"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="02/18/2016"
   ms.author="rick.byham@microsoft.com"/>

# Diferencias de Transact-SQL de Base de datos SQL de Azure


La mayoría de las características de Transact-SQL de que las aplicaciones dependen se admiten en Microsoft SQL Server y Base de datos SQL de Azure. A continuación se muestra una lista parcial de las características admitidas para las aplicaciones:

- Tipos de datos.
- Operadores.
- Funciones de cadena, aritméticas, lógicas y de cursor.

Sin embargo, Base de datos SQL de Azure es una utilidad diseñada para aislar las características de cualquier dependencia de la base de datos **maestra**. Como consecuencia, muchas actividades de nivel de servidor no son apropiadas para la Base de datos SQL y no son compatibles. En este tema se detallan las características que no se admiten totalmente en Base de datos SQL.

Además, las características obsoletas de SQL Server por lo general no se admiten en Base de datos SQL.

## Actualización a Base de datos SQL V12

En este tema se describen las características que están disponibles con Base de datos SQL cuando se actualiza a la V12 gratuita de Base de datos de SQL. Para obtener más información sobre V12, consulte [Novedades de la Base de datos de SQL V12](sql-database-v12-whats-new.md). Base de datos SQL V12 agrega mejoras de facilidad de uso y rendimiento, así como compatibilidad con características adicionales. Las características agregadas se enumeran a continuación, divididas entre características que son totalmente compatibles y características que son parcialmente compatibles.

## Características parcialmente admitidas en la Base de datos SQL V12

Base de datos SQL V12 es compatible con algunos pero no todos los argumentos que existen en las instrucciones de SQL Server 2016 Transact-SQL correspondientes. Por ejemplo, la instrucción CREATE PROCEDURE está disponible aunque lo está la opción WITH ENCRYPTION de CREATE PROCEDURE. Consulte los temas de sintaxis vinculados para obtener más información sobre las áreas compatibles de cada instrucción.

- Bases de datos: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- Generalmente hay vistas de administración dinámica disponibles para las características que están disponibles
- Funciones: [CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)
- [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 
- Inicios de sesión: [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
- Procedimientos almacenados: [CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- Tablas: [CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- Tipos (personalizados): [CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)
- Usuarios: [CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
- Vistas: [CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)

## Características no compatibles con Base de datos SQL

- Intercalación de objetos del sistema
- Conexión relacionada: instrucciones de punto de conexión, ORIGINAL\_DB\_NAME. Autenticación de Windows no está disponible para inicios de sesión o usuarios de bases de datos independientes.
- Consultas entre bases de datos con tres o cuatro nombres de partes. (Las consultas entre bases de datos de solo lectura se admiten mediante el uso de [consultas de base de datos elástica](sql-database-elastic-query-overview.md)).
- Encadenamiento de propiedad entre bases de datos, ajuste TRUSTWORTHY
- Recopilador de datos
- Diagramas de base de datos
- Correo electrónico de base de datos
- DATABASEPROPERTY (usar en su lugar DATABASEPROPERTYEX)
- Inicios de sesión EXECUTE AS
- Cifrado: Administración extensible de claves
- Eventos: eventos, las notificaciones de eventos, notificaciones de consulta
- Características relacionadas con la ubicación del archivo de base de datos, el tamaño y los archivos de base de datos que Microsoft Azure administra automáticamente.
- Características relacionadas con la alta disponibilidad que se administra a través de la cuenta de Microsoft Azure: copia de seguridad, restauración, AlwaysOn, creación de reflejo de la base de datos, trasvase de registros, modos de recuperación. Para obtener más información, vea Copia de seguridad y restauración de Base de datos SQL de Azure.
- Características que se basan en el registro del LOG: replicación, captura de datos modificados.
- Características que se basan en el Agente SQL Server o la base de datos MSDB: trabajos, alertas, operadores, administración basada en directivas, correo electrónico de base de datos, servidores de administración central.
- FILESTREAM
- Funciones: fn\_get\_sql, fn\_virtualfilestats, fn\_virtualservernodes
- Tablas temporales globales
- Configuración del servidor relacionado con el hardware: memoria, subprocesos de trabajo, afinidad de CPU, marcas de seguimiento, etc. Usar en su lugar niveles de servicio.
- HAS\_DBACCESS
- KILL STATS JOB
- Servidores vinculados, OPENQUERY, OPENROWSET, OPENDATASOURCE, BULK INSERT, nombres de tres y cuatro partes
- Servidores de destino o maestros
- [Integración de CLR de .NET Framework con SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
- Regulador de recursos
- Búsqueda semántica
- Credenciales de servidor
- Elementos de nivel de servidor: roles de servidor, IS\_SRVROLEMEMBER, sys.login\_token. No hay permisos de nivel de servidor disponibles, aunque algunos se sustituyen por permisos de nivel de base de datos. Algunas vistas de administración dinámica no están disponibles, aunque algunas se sustituyen por vistas de administración dinámica de nivel de base de datos.
- Express sin servidor: localdb, instancias de usuario
- Service Broker
- SET REMOTE\_PROC\_TRANSACTIONS
- SHUTDOWN
- sp\_addmessage
- Opciones de sp\_configure y RECONFIGURE
- sp\_helpuser
- sp\_migrate\_user\_to\_contained
- Auditoría de SQL Server (usar en su lugar auditoría de Base de datos SQL)
- Analizador de SQL Server
- Seguimiento de SQL Server
- Marcas de seguimiento
- Depuración de Transact-SQL
- Desencadenadores: desencadenadores LOGON o con ámbito de servidor
- Instrucción USE


## Referencia completa de Transact-SQL

Para obtener más información sobre la gramática de Transact-SQL, su uso y ejemplos, consulte [Referencia de Transact-SQL (motor de base de datos)](https://msdn.microsoft.com/library/bb510741.aspx) en Libros en pantalla de SQL Server.

### Acerca de las etiquetas "Se aplica a"

La referencia de Transact-SQL incluye temas relacionados con las versiones de SQL Server que abarcan desde la de 2008 hasta la actual. En el título del tema suele haber una línea "Se aplica a", que enumera las versiones de SQL Server y quizás también otros nombres de productos. A menudo las mismas etiquetas "Se aplica a" también muestran la Base de datos SQL de Azure. Si una etiqueta "Se aplica a" no muestra la Base de datos SQL de Azure, el contenido del tema no se aplica a la Base de datos SQL de Azure. A veces, puede que vea una línea "Se aplica a" que muestra varios productos, cada uno con un pequeño icono para indicar si el tema se aplica a cada producto.

 Por ejemplo, los grupos de disponibilidad se introdujeron en SQL Server 2012. El tema **CREACIÓN DE UN GRUPO DE DISPONIBILIDAD** indica que se aplica a **SQL Server (SQL Server 2012 hasta la versión actual)** porque no se aplica a SQL Server 2008, SQL Server 2008 R2 ni a la Base de datos SQL de Azure.

En algunos casos, la línea general de un tema se puede utilizar en un producto, pero existen diferencias poco significativas entre los productos. Las diferencias se indican en los puntos medios del tema según corresponda.

<!---HONumber=AcomDC_0224_2016-->