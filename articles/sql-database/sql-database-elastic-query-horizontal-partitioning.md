<properties
    pageTitle="Consultas de bases de datos elásticas para particionamiento horizontal | Microsoft Azure"
    description="Cómo configurar consultas elásticas en particiones horizontales"    
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/28/2016"
    ms.author="torsteng;sidneyh" />

# Consultas de bases de datos elásticas para particionamiento horizontal

En este documento, se explica cómo configurar consultas elásticas para escenarios de particionamiento horizontal y cómo realizar las consultas. Para obtener una definición del escenario de particionamiento horizontal, consulte [Información general sobre consulta de bases de datos elásticas de Base de datos SQL de Azure (vista previa)](sql-database-elastic-query-overview.md).

![Consultas entre particiones][1]

La funcionalidad es una parte del [conjunto de características de bases de datos elásticas de Base de datos ](sql-database-elastic-scale-introduction.md) SQL de Azure.
 
## Creación de objetos de base de datos

La consulta de bases de datos elásticas amplía la sintaxis de T-SQL para hacer referencia a las capas de datos que usan el particionamiento horizontal para distribuir datos entre varias bases de datos. Esta sección proporciona información general sobre las instrucciones de DDL asociadas a la consulta elástica en tablas particionadas. Estas instrucciones crean la representación de los metadatos de la capa de datos particionada en la base de datos de consulta elástica. Un requisito previo para la ejecución de estas instrucciones es crear un mapa de particiones mediante la biblioteca de clientes de bases de datos elásticas. Para obtener más información, consulte [Administración de mapas de particiones](sql-database-elastic-scale-shard-map-management.md), o bien, use el ejemplo del tema siguiente para crear uno: [Introducción a las herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md).

La definición de los objetos de base de datos para la consulta de bases de datos elásticas se basa en las siguientes instrucciones T-SQL que se explican con más detalle para el escenario de particionamiento horizontal siguiente:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) 

* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)

* [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)

* [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx)

### 1\.1 Clave maestra y credenciales con ámbito de base de datos 

Una credencial representa el identificador y la contraseña de usuario que usará la consulta elástica para conectarse a sus bases de datos remotas en Base de datos SQL de Azure. Para crear la clave maestra y la credencial necesarias, use la sintaxis siguiente:

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = ’password’;
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = ‘<username>’,  
    SECRET = ‘<password>’
    [;]

O bien, para anular la credencial y la clave:

    DROP DATABASE SCOPED CREDENTIAL <credential_name>;  
    DROP MASTER KEY;   

 
**Nota** Asegúrese de que *< username>* no incluya ningún sufijo *“@servername”*.

### 1\.2 Orígenes de datos externos

Proporcione la información sobre el mapa de particiones y la capa de datos mediante la definición de un origen de datos externo. El origen de datos externo hace referencia al mapa de particiones. Una consulta elástica usa el origen de datos externo y el mapa de particiones subyacente para enumerar las bases de datos que participan en la capa de datos. La sintaxis para crear un origen de datos externo se define como sigue:

	<External_Data_Source> ::=    
	CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                               	           
			(TYPE = SHARD_MAP_MANAGER,
                   	LOCATION = '<fully_qualified_server_name>',
			DATABASE_NAME = ‘<shardmap_database_name>',
			CREDENTIAL = <credential_name>, 
			SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 
 
O bien, para anular un origen de datos externo:

	DROP EXTERNAL DATA SOURCE <data_source_name>[;] 

#### Permisos para CREATE/DROP EXTERNAL DATA SOURCE 

El usuario debe poseer el permiso ALTER ANY EXTERNAL DATA SOURCE. Este permiso está incluido en el permiso ALTER DATABASE.

**Ejemplo**

En el ejemplo siguiente se ilustra el uso de la instrucción CREATE para orígenes de datos externos.

	CREATE EXTERNAL DATA SOURCE MyExtSrc 
	WITH 
	( 
		TYPE=SHARD_MAP_MANAGER,
		LOCATION='myserver.database.windows.net', 
		DATABASE_NAME='ShardMapDatabase', 
		CREDENTIAL= SMMUser, 
		SHARD_MAP_NAME='ShardMap' 
	);
 
Puede recuperar la lista de orígenes de datos externos actuales a partir de la vista de catálogo siguiente:

	select * from sys.external_data_sources; 

Tenga en cuenta que se usan las mismas credenciales para leer el mapa de particiones y para tener acceso a los datos de las particiones durante el procesamiento de una consulta elástica.

### 1\.3 Tablas externas 
 
Las consultas elásticas amplían el DDL de tabla externa para hacer referencia a tablas externas que se particionan horizontalmente en varias bases de datos. La definición de tablas externas trata los siguientes aspectos:

* **Esquema**: el DDL de tabla externa define un esquema que las consultas pueden usar. El esquema proporcionado en la definición de tablas externas debe coincidir con el de las tablas de las particiones donde se almacenan los datos en sí. 

* **Distribución de datos**: el DDL de tabla externa define la distribución de datos usados para distribuir los datos en la capa de datos. Tenga en cuenta Base de datos SQL de Azure no valida la distribución que se define en la tabla externa en la distribución real en las particiones. Usted es responsable para garantizar que la distribución de datos real en las particiones coincida con la definición de tabla externa.

* **Referencia de capa de datos**: el DDL de tabla externa hace referencia a un origen de datos externo. El origen de datos externo especifica un mapa de particiones que proporciona a la tabla externa la información necesaria para localizar todas las bases de datos en la capa de datos.

Mediante el uso de un origen de datos externo como se describe en la anterior sección, la sintaxis para crear y anular tablas externas es la siguiente:

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
	    { WITH ( <sharded_external_table_options> ) }
	) [;]  
	
	<sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
	  [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

La cláusula DATA\_SOURCE define el origen de datos externo (un mapa de asignaciones en el caso del particionamiento horizontal) que se usa para la tabla externa.

Las cláusulas SCHEMA\_NAME y OBJECT\_NAME proporcionan la capacidad de asignar la definición de la tabla externa a una tabla en un esquema diferente de la partición horizontal, o bien a una tabla con un nombre diferente, respectivamente. Si se omite, se considera que el esquema del objeto remoto es "dbo" y que su nombre es idéntico al nombre de la tabla externa que se está definiendo.

Las cláusulas SCHEMA\_NAME y OBJECT\_NAME son especialmente útiles si el nombre de la tabla remota ya existe en la base de datos donde desea crear la tabla externa. Un ejemplo de este problema es cuando desea definir una tabla externa para obtener una vista agregada de las vistas de catálogo o DMV en la capa de datos con escala horizontal. Puesto que las vistas de catálogo y DMV ya existen localmente, no se pueden usar sus nombres para la definición de la tabla externa. En su lugar, use un nombre diferente y el nombre de la vista de catálogo o la DMV en las cláusulas SCHEMA\_NAME y/o OBJECT\_NAME. (Consulte el ejemplo siguiente).

La cláusula DISTRIBUTION especifica la distribución de datos que se usa en esta tabla:

* SHARDED significa que los datos de esta tabla se particionan horizontalmente en las bases de datos del mapa de particiones. La clave de partición para la distribución de datos se captura en el parámetro <sharding_column_name>.  

* REPLICATED significa que copias idénticas de la tabla están presentes en cada base de datos del mapa de particiones. La base de datos SQL de Azure no mantiene las copias de la tabla. Es responsabilidad suya asegurarse de que las réplicas son idénticas en las bases de datos.

* ROUND\_ROBIN significa que la tabla se distribuye con la partición horizontal. Sin embargo, se ha usado una distribución dependiente de la aplicación.

El procesador de consultas usa la información proporcionada en la cláusula DISTRIBUTION para crear los planes de consulta más eficaces.

Use la instrucción siguiente para anular las tablas externas:

	DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]  

**Permisos para CREATE/DROP EXTERNAL TABLE**: se necesitan permisos ALTER ANY EXTERNAL DATA SOURCE que también son necesarios para hacer referencia al origen de datos subyacente.

**Consideraciones de seguridad**: los usuarios con acceso a la tabla externa obtienen automáticamente acceso a las tablas remotas subyacentes con la credencial proporcionada en la definición del origen de datos externo. Debe administrar con cuidado el acceso a la tabla externa para evitar la elevación de privilegios no deseada por medio de la credencial del origen de datos externo. Se pueden usar permisos SQL normales para conceder o revocar el acceso a una tabla externa como si fuera una tabla normal.

**Ejemplo**: en el ejemplo siguiente se muestra cómo crear una tabla externa:

	CREATE EXTERNAL TABLE [dbo].[order_line]( 
		 [ol_o_id] int NOT NULL, 
		 [ol_d_id] tinyint NOT NULL,
		 [ol_w_id] int NOT NULL, 
		 [ol_number] tinyint NOT NULL, 
		 [ol_i_id] int NOT NULL, 
		 [ol_delivery_d] datetime NOT NULL, 
		 [ol_amount] smallmoney NOT NULL, 
		 [ol_supply_w_id] int NOT NULL, 
		 [ol_quantity] smallint NOT NULL, 
		 [ol_dist_info] char(24) NOT NULL 
	) 
	
	WITH 
	( 
		DATA_SOURCE = MyExtSrc, 
	 	SCHEMA_NAME = 'orders', 
	 	OBJECT_NAME = 'order_details', 
		DISTRIBUTION=SHARDED(ol_w_id)
	); 

En el ejemplo siguiente se muestra cómo recuperar la lista de tablas externas de la base de datos actual:

	select * from sys.external_tables; 

## Consultas 

### 2\.1 Consultas T-SQL con plena fidelidad 

Una vez que defina el origen de datos externo y las tablas externas, puede usar el T-SQL completo en las tablas externas.

**Ejemplo de particionamiento horizontal**: la consulta siguiente realiza una combinación en tres direcciones entre almacenes, pedidos y líneas de pedido y usa varios agregados y un filtro selectivo. Asume (1) la partición horizontal y (2) que los almacenes, pedidos y líneas de pedido se particionan por la columna del identificador de almacén y que la consulta elástica puede colocar las combinaciones en las particiones y procesar la parte cara de la consulta en las particiones en paralelo.

	select  
		 w_id as warehouse,
		 o_c_id as customer,
		 count(*) as cnt_orderline,
		 max(ol_quantity) as max_quantity,
		 avg(ol_amount) as avg_amount, 
		 min(ol_delivery_d) as min_deliv_date
	from warehouse 
	join orders 
	on w_id = o_w_id
	join order_line 
	on o_id = ol_o_id and o_w_id = ol_w_id 
	where w_id > 100 and w_id < 200 
	group by w_id, o_c_id 
 
### 2\.2 Procedimiento almacenado SP\_ EXECUTE\_FANOUT 

La consulta elástica también incluye un procedimiento almacenado que proporciona acceso directo a las particiones. El procedimiento almacenado se llama sp\_execute\_fanout y admite los siguientes parámetros:

* Nombre del servidor (nvarchar): nombre completo del servidor lógico que hospeda el mapa de particiones. 
* Nombre de la base de datos del mapa de particiones (nvarchar): nombre de la base de datos del mapa de particiones. 
* Nombre del usuario (nvarchar): nombre del usuario para iniciar sesión en la base de datos del mapa de particiones. 
* Contraseña (nvarchar): contraseña del usuario. 
* Nombre del mapa de particiones (nvarchar): nombre del mapa de particiones que se va a usar para la consulta. El nombre se encuentra en la tabla \_ShardManagement.ShardMapsGlobal, que es el nombre predeterminado que se utiliza al crear bases de datos con la aplicación de ejemplo que se encuentra en [Introducción a las herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md). El nombre predeterminado que se encuentra en la aplicación es "CustomerIDShardMap".
*  Consulta: la consulta T-SQL que se va a ejecutar en cada partición. 
*  Declaración de parámetro (nvarchar) - opcional: cadena con definiciones de tipos de datos de los parámetros usados en el parámetro Query (como sp\_executesql). 
*  Lista de valores de los parámetros - opcional: lista separada por comas de valores de los parámetros (por ejemplo, sp\_executesql)  

sp\_execute\_fanout usa la información del mapa de particiones proporcionada en los parámetros de invocación para ejecutar la instrucción T-SQL especificada en todas las particiones registradas con el mapa de particiones. Los resultados se combinan con la semántica UNION ALL. El resultado también incluye la columna ‘virtual’ adicional con el nombre de la partición.

Tenga en cuenta que se usan las mismas credenciales para conectarse a la base de datos del mapa de particiones y para las particiones.

Ejemplo:

	sp_execute_fanout 
		N'myserver.database.windows.net', 
		N'ShardMapDb', 
		N'myuser', 
		N'MyPwd', 
		N'ShardMap', 
		N'select count(w_id) as foo from warehouse' 

## Conectividad para herramientas  

Use cadenas de conexión de SQL Server normales para conectar su aplicación, sus herramientas de integración de datos o de BI a bases de datos con sus definiciones de tablas externas. Asegúrese de que SQL Server se admite como origen de datos para la herramienta. A continuación, haga referencia a la base de datos de consulta elástica como cualquier otra base de datos de SQL Server conectada a la herramienta y use las tablas externas desde su herramienta o aplicación como si fueran tablas locales.

## Prácticas recomendadas 

* Asegúrese de que se ha concedido acceso a la base de datos de puntos de conexión de consulta elástica para la base de datos del mapa de particiones y todas las particiones a través de los firewalls de la base de datos SQL.  

* La consulta elástica no valida ni exige la distribución de datos definida por la tabla externa. Si la distribución de datos real es diferente de la distribución especificada en la definición de tabla, las consultas pueden arrojar resultados inesperados.

* La consulta elástica actualmente no realiza la eliminación de particiones cuando los predicados de la clave de particiones permitirían excluir de forma segura determinadas bases de datos remotas del procesamiento.

* Una consulta elástica funciona mejor para consultas en que la mayor parte del cálculo se puede realizar en las particiones. Normalmente el máximo rendimiento de consultas se obtiene con predicados de filtros selectivos que se puede evaluar en las particiones o combinaciones sobre las claves de particiones que se pueden realizar en consonancia con la partición en todas las particiones. Otros patrones de consulta pueden necesitar cargar grandes cantidades de datos desde las particiones al nodo principal y pueden experimentar un rendimiento deficiente


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->

<!---HONumber=AcomDC_0204_2016-->