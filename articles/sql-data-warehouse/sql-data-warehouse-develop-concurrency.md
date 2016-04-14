<properties
   pageTitle="Simultaneidad y administración de cargas de trabajo en Almacenamiento de datos SQL | Microsoft Azure"
   description="Obtenga información sobre la simultaneidad y la administración de cargas de trabajo en Almacenamiento de datos SQL de Azure para el desarrollo de soluciones."
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
   ms.date="01/25/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Simultaneidad y administración de cargas de trabajo en Almacenamiento de datos SQL
Para proporcionar rendimiento predecible a escala, Almacenamiento de datos SQL implementa mecanismos para administrar la simultaneidad de cargas de trabajo y la asignación de recursos computacionales.

En este artículo se presentan los conceptos de simultaneidad y administración de cargas de trabajo; se explica cómo se han implementado ambas características y cómo puede controlarlas en su almacenamiento de datos.

## Simultaneidad
Es importante comprender que la simultaneidad en Almacenamiento de datos SQL se rige por dos conceptos; **consultas simultáneas** y **ranuras de simultaneidad**.

Las consultas simultáneas son iguales al número de consultas que se ejecutan al mismo tiempo. Almacenamiento de datos SQL admite hasta 32 **consultas simultáneas**. Cada ejecución de consulta se cuenta como una sola consulta independientemente de si es una consulta en serie (un único subproceso) o una consulta en paralelo (multiproceso). Este es un límite fijo y se aplica a todos los niveles de servicio y a todas las consultas.

Las ranuras de simultaneidad son un concepto más dinámico y se relacionan con el objetivo de nivel de servicio de Unidad de almacenamiento de datos (DWU) para el almacenamiento de datos. A medida que aumenta el número de DWU asignadas a Almacenamiento de datos SQL más recursos de proceso se asignan. Sin embargo, también aumenta el número de **ranuras de simultaneidad** disponibles.

Como regla general, cada consulta que se ejecuta de forma simultánea consume una o varias ranuras de simultaneidad. El número exacto de ranuras depende de tres factores:

1. El valor de DWU para Almacenamiento de datos SQL
2. La **clase de recurso** a la que pertenezca el usuario
3. Si la consulta o la operación se rige por el modelo de ranura de simultaneidad 

> [AZURE.NOTE] Merece la pena tener en cuenta que no todas las consultas se rigen por la regla de consulta de ranura de simultaneidad. Sin embargo, sí lo hacen la mayoría de las consultas de usuario. Algunas consultas y operaciones no consumen ranuras de simultaneidad en absoluto. Estas consultas y operaciones todavía están limitadas por el límite de consultas simultáneas que es por lo que se describen ambas reglas. Consulte la siguiente sección de [excepciones de clases de recursos](#exceptions) para obtener más detalles.

En la siguiente tabla se describen tanto los límites de consultas simultáneas como las ranuras de simultaneidad, suponiendo que la consulta se rige por recursos.

<!--
| Concurrency Slot Consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
-->

| Consumo de ranuras de simultaneidad | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | 
| N.º máximo de consultas simultáneas | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 
| N.º máximo de ranuras de simultaneidad | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 |

Las cargas de trabajo de consultas del Almacenamiento de datos SQL tienen que encontrarse dentro de estos umbrales. Si hay más de 32 consultas simultáneas o se supera el número de ranuras de simultaneidad, la consulta se pondrá en cola hasta que puedan satisfacerse ambos umbrales.

## Administración de cargas de trabajo

Almacenamiento de datos SQL expone cuatro clases de recursos diferentes en forma de **roles de base de datos** como parte de su implementación de la administración de cargas de trabajo.

Los roles son:

- smallrc
- mediumrc
- largerc
- xlargerc

Las clases de recursos son una parte esencial de la administración de cargas de trabajo del Almacenamiento de datos SQL. Rigen los recursos informáticos asignados a la consulta.

De forma predeterminada, cada usuario es miembro de la clase de recurso pequeña: smallrc. Sin embargo, cualquier usuario se puede agregar a una o varias de las clases de recursos más altas. Como regla general, el Almacenamiento de datos SQL tomará la pertenencia al rol más alto para la ejecución de consultas. El hecho de agregar un usuario a una clase de recursos más alta aumenta los recursos para ese usuario pero también consume una mayor cantidad de ranuras de simultaneidad, lo que limitará posiblemente la posibilidad de simultaneidad. Esto se debe a que a medida que se asignan más recursos a una consulta, el sistema debe limitar los recursos que otras consumen. No hay comida gratis.

El recurso más importante que se rige por las clases de recursos superiores es la memoria. La mayoría de tablas de almacenamiento de datos de cualquier tamaño significativo usará índices de almacén de columnas agrupados. Si bien esto normalmente proporciona el mejor rendimiento para cargas de trabajo de almacenamiento de datos, su mantenimiento es una operación que consume mucha memoria. A menudo resulta beneficioso usar las clases de recursos más altas en operaciones de administración de datos, por ejemplo, en las regeneraciones de índices.

El Almacenamiento de datos SQL ha implementado las clases de recursos mediante el uso de roles de bases de datos. Para convertirse en miembro de una clase de recurso superior y aumentar la memoria simplemente y la prioridad, agregue su usuario de base de datos a una de las clases de recursos o roles mencionados anteriormente.

### Pertenencia de clase de recurso

Puede agregarse y quitarse usted mismo del rol de base de datos de administración de cargas de trabajo con los procedimientos `sp_addrolemember` y `sp_droprolemember`. Tenga en cuenta que necesitará el permiso `ALTER ROLE` para hacerlo. No podrá usar la sintaxis ALTER ROLE DDL. Debe usar los procedimientos almacenados anteriormente mencionados. Se ofrece un ejemplo completo de cómo crear inicios de sesión y usuarios en la sección[administración de usuarios) [#managing-usuarios] al final de este artículo.

> [AZURE.NOTE] En lugar de entrar y sacar un usuario en un grupo de administración de cargas de trabajo, con frecuencia resulta más sencillo iniciar esas operaciones de mayor carga a través de un inicio de sesión o usuario independiente que se asigna permanentemente a la clase de recurso más alta.

### Asignación de memoria

En la tabla siguiente se detalla el aumento de memoria disponible para cada consulta, sujeto a la clase de recurso aplicada al usuario que la ejecuta:

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  | DW3000  | DW6000   |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------ | :------- |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100  MB | 100   MB |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 3200  MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 6400  MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB | 6400 MB | 12800 MB |
-->

<!--
| Memory Available (per dist) | Priority | DW100  | DW200  | DW300  | DW400   | DW500   | DW600   | DW1000  | DW1200  | DW1500  | DW2000  |
| :-------------------------- | :------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(default) (s)        | Medium   | 100 MB | 100 MB | 100 MB | 100  MB | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  | 100 MB  |
| mediumrc (m)                | Medium   | 100 MB | 200 MB | 200 MB | 400  MB | 400 MB  | 400 MB  | 800 MB  | 800 MB  | 800 MB  | 1600 MB |
| largerc (l)                 | High     | 200 MB | 400 MB | 400 MB | 800  MB | 800 MB  | 800 MB  | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc (xl)               | High     | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |
-->

| Memoria disponible (por dist) | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :-------------------------- | :----  | :----- | :----- | :------ | :------ | :------ | :------ | :------ | :------ | :------ |
| smallrc(default) (s) | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB | 100 MB |
| mediumrc (m) | 100 MB | 200 MB | 200 MB | 400 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB |
| largerc (l) | 200 MB | 400 MB | 400 MB | 800 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB |
| xlargerc (xl) | 400 MB | 800 MB | 800 MB | 1600 MB | 1600 MB | 1600 MB | 3200 MB | 3200 MB | 3200 MB | 6400 MB |

### Consumo de ranuras de simultaneidad

Además, como se mencionó anteriormente, cuanto mayor es la clase de recurso asignada al usuario mayor es el consumo de ranuras de simultaneidad. En la siguiente tabla se documenta el consumo de ranuras de simultaneidad por las consultas en una clase de recurso especificada.

<!--
| Consumption | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 | DW3000 | DW6000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- | :----- | :----- |
| Max Concurrent Queries       | 32    | 32    | 32    | 32    | 32    | 32    | 32     | 32     | 32     | 32     | 32     | 32     |
| Max Concurrency Slots        | 4     | 8     | 12    | 16    | 20    | 24    | 40     | 48     | 60     | 80     | 120    | 240    |
| smallrc(default) (s)         | 1     | 1     | 1     | 1     | 1     | 1     | 1      | 1      | 1      | 1      | 1      | 1      |
| mediumrc (m)                 | 1     | 2     | 2     | 4     | 4     | 4     | 8      | 8      | 8      | 16     | 16     | 32     |
| largerc (l)                  | 2     | 4     | 4     | 8     | 8     | 8     | 16     | 16     | 16     | 32     | 32     | 64     |
| xlargerc (xl)                | 4     | 8     | 8     | 16    | 16    | 16    | 32     | 32     | 32     | 64     | 64     | 128    |
-->

| Consumo | DW100 | DW200 | DW300 | DW400 | DW500 | DW600 | DW1000 | DW1200 | DW1500 | DW2000 |
| :--------------------------- | :---- | :---- | :---- | :---- | :---- | :---- | :----- | :----- | :----- | :----- |
| N.º máximo de consultas simultáneas | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 | 32 |
| N.º máximo de ranuras de simultaneidad | 4 | 8 | 12 | 16 | 20 | 24 | 40 | 48 | 60 | 80 |
| smallrc(default) (s) | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
| mediumrc (m) | 1 | 2 | 2 | 4 | 4 | 4 | 8 | 8 | 8 | 16 |
| largerc (l) | 2 | 4 | 4 | 8 | 8 | 8 | 16 | 16 | 16 | 32 |
| xlargerc (xl) | 4 | 8 | 8 | 16 | 16 | 16 | 32 | 32 | 32 | 64 |

### Excepciones

Hay ocasiones donde la pertenencia de una clase de recurso superior no altera los recursos asignados a la consulta o a la operación. Esto suele ocurrir cuando los recursos necesarios para llevar a cabo la acción son bajos. En estos casos siempre se usa la clase de recurso pequeña o predeterminada (smallrc) con independencia de la clase de recurso asignada al usuario. Por ejemplo, siempre se ejecutará `CREATE LOGIN` en el smallrc. Los recursos necesarios para llevar a cabo esta operación son muy bajos y por lo tanto no tendría sentido incluir la consulta en el modelo de ranura de simultaneidad. Sería una pérdida de tiempo asignar previamente grandes cantidades de memoria para esta acción. Al excluir `CREATE LOGIN` del modelo de ranura de simultaneidad el Almacenamiento de datos SQL puede ser mucho más eficaz.

A continuación se muestra una lista de instrucciones y operaciones que **se** rigen por las clases de recursos:

- INSERT-SELECT
- UPDATE
- DELETE
- SELECT (al consultar las tablas de usuario)
- ALTER INDEX REBUILD
- ALTER INDEX REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT 
- Carga de datos 
- Operaciones de movimiento de datos llevadas a cabo por el servicio de movimiento de datos (DMS)

Las siguientes instrucciones **no** respetan las clases de recursos:

- CREATE TABLE
- ALTER TABLE ... SWITCH PARTITION 
- ALTER TABLE ... SPLIT PARTITION 
- ALTER TABLE ... MERGE PARTITION 
- DROP TABLE
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE STATISTICS
- UPDATE STATISTICS
- DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE USER
- ALTER USER
- DROP USER
- CREATE PROCEDURE
- ALTER PROCEDURE
- DROP PROCEDURE
- CREATE VIEW
- DROP VIEW
- INSERT VALUES
- SELECT (desde vistas del sistema y DMV)
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE 
-->
> [AZURE.NOTE] Merece la pena resaltar que las consultas de `SELECT` que se ejecutan exclusivamente contra vistas de catálogo y vistas de administración dinámicas **no** se rigen por las clases de recursos.

Es importante recordar que es probable que la mayoría de las consultas de usuario final se rijan por clases de recursos. La regla general es que la carga de trabajo de consultas activas debe tener cabida tanto en el umbral de ranuras simultáneas como en el de consultas simultáneas a menos que se haya excluido específicamente por la plataforma. Como usuario final no puede elegir excluir una consulta del modelo de ranura de simultaneidad. Una vez que se ha superado el umbral, las consultas comenzarán a ponerse en cola. Las consultas en cola se tratarán en orden de prioridad, seguido por la hora de envío.

### Datos internos 

En el nivel más profundo de la carga de trabajo del Almacenamiento de datos SQL, las cuestiones de administración son algo más complicadas. Las clases de recursos se asignan dinámicamente a un conjunto genérico de grupos de administración de cargas de trabajo dentro del regulador de recursos. Los grupos usados dependerán del valor de DWU para el almacenamiento. Sin embargo, Almacenamiento de datos SQL usa un total de ocho grupos de cargas de trabajo. Son las siguientes:

- SloDWGroupC00
- SloDWGroupC01
- SloDWGroupC02
- SloDWGroupC03
- SloDWGroupC04
- SloDWGroupC05
- SloDWGroupC06
- SloDWGroupC07

Estos 8 grupos se asignan al consumo de ranuras de simultaneidad

| Grupo de cargas de trabajo | Asignación de la ranura de simultaneidad | Asignación de prioridad |
| :------------  | :----------------------- | :--------------- |
| SloDWGroupC00 | 1 | Mediano |
| SloDWGroupC01 | 2 | Mediano |
| SloDWGroupC02 | 4 | Mediano |
| SloDWGroupC03 | 8 | Mediano |
| SloDWGroupC04 | 16 | Alto |
| SloDWGroupC05 | 32 | Alto |
| SloDWGroupC06 | 64 | Alto |
| SloDWGroupC07 | 128 | Alto |

Por lo tanto, si DW500 es el valor actual de DWU para Almacenamiento de datos SQL, los grupos de cargas de trabajo activos se asignarían a las clases de recursos de la siguiente manera:

| Clase de recurso | Grupo de cargas de trabajo | Ranuras de simultaneidad usadas | Importancia |
| :------------- | :------------- | :---------------------   | :--------- |
| smallrc | SloDWGroupC00 | 1 | Mediano |
| mediumrc | SloDWGroupC02 | 4 | Mediano |
| largerc | SloDWGroupC03 | 8 | Mediano |
| xlargerc | SloDWGroupC04 | 16 | Alto |

Para ver con detalle las diferencias en la asignación de recursos de memoria desde la perspectiva del regulador de recursos, use la siguiente consulta:

```
WITH rg
AS
(   SELECT  pn.name									AS node_name
	,		pn.[type]								AS node_type
	,		pn.pdw_node_id							AS node_id
	,		rp.name									AS pool_name
    ,       rp.max_memory_kb*1.0/1024				AS pool_max_mem_MB
    ,       wg.name									AS group_name
    ,       wg.importance							AS group_importance
    ,       wg.request_max_memory_grant_percent		AS group_request_max_memory_grant_pcnt
    ,       wg.max_dop								AS group_max_dop
    ,       wg.effective_max_dop					AS group_effective_max_dop
	,		wg.total_request_count					AS group_total_request_count
	,		wg.total_queued_request_count			AS group_total_queued_request_count
	,		wg.active_request_count					AS group_active_request_count
	,		wg.queued_request_count					AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
															        AND wg.pool_id      = rp.pool_id
	JOIN	sys.dm_pdw_nodes pn										ON	wg.pdw_node_id	= pn.pdw_node_id
	WHERE   wg.name like 'SloDWGroup%'
	AND     rp.name = 'SloDWPool'
) 
SELECT	pool_name
,		pool_max_mem_MB
,		group_name
,		group_importance
,		(pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,		node_name
,		node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM	rg
ORDER BY 
	node_name
,	group_request_max_memory_grant_pcnt
,	group_importance
;
```

> [AZURE.NOTE] La consulta anterior también puede usarse para analizar el uso activo e histórico de los grupos de cargas de trabajo en la solución de problemas.

## Ejemplos de administración de cargas de trabajo

En esta sección se ofrecen algunos ejemplos adicionales para procesar la administración de usuarios y para detectar consultas que están en cola.

### Administración de usuarios

Para conceder acceso a un usuario a Almacenamiento de datos SQL, primero necesitan iniciar sesión.

Abra una conexión con la base de datos maestra para Almacenamiento de datos SQL y ejecute los siguientes comandos:

```
CREATE LOGIN newperson WITH PASSWORD = 'mypassword'

CREATE USER newperson for LOGIN newperson
```

> [AZURE.NOTE] Es una buena idea crear usuarios para los inicios de sesión en la base de datos maestra de la Base de datos SQL de Azure y del Almacenamiento de datos SQL de Azure. Hay dos roles de servidor disponibles en este nivel que requieren que el inicio de sesión tenga un usuario en la base de datos maestra para poder concederle la pertenencia. Los roles son `Loginmanager` y `dbmanager`. Tanto en Base de datos SQL como en Almacenamiento de datos SQL de Azure, estos roles conceden derechos para administrar los inicios de sesión y crear bases de datos. Esto es diferente de SQL Server. Para obtener más detalles, consulte el artículo [Administrar bases de datos, inicios de sesión y usuarios en Base de datos SQL de Microsoft Azure].

Una vez creado el inicio de sesión, hay que agregar una cuenta de usuario.

Abra una conexión con la base de datos de Almacenamiento de datos SQL y ejecute el siguiente comando:

```
CREATE USER newperson FOR LOGIN newperson
```

Una vez finalizado, deberá conceder al usuario permisos completos. El ejemplo siguiente concede `CONTROL` sobre la base de datos de Almacenamiento de datos SQL. En el nivel de base de datos, `CONTROL` es el equivalente de db\_owner en SQL Server.

```
GRANT CONTROL ON DATABASE::MySQLDW to newperson
```

Para ver los roles de administración de cargas de trabajo, use la siguiente consulta:

```
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

Para agregar un usuario a un rol de administración de cargas de trabajo con privilegios elevados, use la consulta siguiente:

``` 
EXEC sp_addrolemember 'largerc', 'newperson' 
```

Para quitar un usuario de un rol de administración de cargas de trabajo, use la consulta siguiente:

``` 
EXEC sp_droprolemember 'largerc', 'newperson' 
```

> [AZURE.NOTE] No se puede quitar un usuario de smallrc.

Para ver qué usuarios son miembros de un rol determinado, use la siguiente consulta:

```
SELECT	r.name AS role_principal_name
,		m.name AS member_principal_name
FROM	sys.database_role_members rm
JOIN	sys.database_principals AS r			ON rm.role_principal_id		= r.principal_id
JOIN	sys.database_principals AS m			ON rm.member_principal_id	= m.principal_id
WHERE	r.name IN ('mediumrc','largerc', 'xlargerc')
;
```

### Detección de consulta en cola
Para identificar las consultas que se mantienen en una cola de simultaneidad, siempre puede consultar la `sys.dm_pdw_exec_requests` DMV.

```
SELECT 	 r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,r.resource_class                               AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

Almacenamiento de datos SQL tiene tipos de espera específicos para medir la simultaneidad.

Son las siguientes:
 
- LocalQueriesConcurrencyResourceType
- UserConcurrencyResourceType
- DmsConcurrencyResourceType
- BackupConcurrencyResourceType

LocalQueriesConcurrencyResourceType se refiere a consultas que residen fuera el marco de la ranura de simultaneidad. Las funciones del sistema y las consultas DMV como `SELECT @@VERSION` son ejemplos de consultas locales.

UserConcurrencyResourceType se refiere a consultas que residen dentro del marco de la ranura de simultaneidad. Las consultas en tablas de usuario final representan ejemplos que usarían este tipo de recurso.

DmsConcurrencyResourceType se refiere a esperas que son el resultado de operaciones de movimiento de datos.

BackupConcurrencyResourceType puede verse cuando se hace una copia de seguridad de una base de datos. El valor máximo para este tipo de recurso es 1. Si varias copias de seguridad se han solicitado al mismo tiempo, las demás se pondrán en cola.


Para realizar análisis de las consultas actualmente en cola a fin de averiguar qué recursos espera una solicitud, consulte la `sys.dm_pdw_waits` DMV.

```
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]											AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,		SESSION_ID()										AS Current_session
,		s.[status]											AS Session_status
,		s.[login_name]
,		s.[query_count]
,		s.[client_id]
,		s.[sql_spid]
,		r.[command]											AS Request_command
,		r.[label]
,		r.[status]											AS Request_status
,		r.[submit_time]
,		r.[start_time]
,		r.[end_compile_time]
,		r.[end_time]
,		DATEDIFF(ms,r.[submit_time],r.[start_time])			AS Request_queue_time_ms
,		DATEDIFF(ms,r.[start_time],r.[end_compile_time])	AS Request_compile_time_ms
,		DATEDIFF(ms,r.[end_compile_time],r.[end_time])		AS Request_execution_time_ms
,		r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE	w.[session_id] <> SESSION_ID()
;
```

Para ver solo las esperas de recursos usadas por una consulta determinada, puede consultar la `sys.dm_pdw_resource_waits` DMV. El tiempo de espera del recurso solo mide el tiempo que se espera a que se proporcionen los recursos, por oposición al tiempo de espera de la señal, que es el tiempo que tarda el servidor SQL Server subyacente en programar la consulta en la CPU.

```
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE	[session_id] <> SESSION_ID()
;
```

Por último, para analizar las tendencias históricas de esperas, Almacenamiento de datos SQL proporciona la `sys.dm_pdw_wait_stats` DMV.

```
SELECT	w.[pdw_node_id]
,		w.[wait_name]
,		w.[max_wait_time]
,		w.[request_count]
,		w.[signal_time]
,		w.[completed_count]
,		w.[wait_time]
FROM	sys.dm_pdw_wait_stats w
;
```

## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[Administrar bases de datos, inicios de sesión y usuarios en Base de datos SQL de Microsoft Azure]: https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0128_2016-->