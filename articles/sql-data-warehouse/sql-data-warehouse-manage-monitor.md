<properties
   pageTitle="Supervisión de la carga de trabajo mediante DMV | Microsoft Azure"
   description="Obtenga información sobre cómo supervisar la carga de trabajo mediante DMV."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Supervisión de la carga de trabajo mediante DMV

En este artículo se describe cómo usar las vistas de administración dinámica (DMV) para supervisar la carga de trabajo e investigar la ejecución de la consulta en Almacenamiento de datos SQL de Azure.



## Supervisión de conexiones

Puede usar la vista *sys.dm\_pdw\_nodes\_exec\_connections* para recuperar información sobre las conexiones establecidas con la base de datos de Almacenamiento de datos SQL de Azure. Además, la vista *sys.dm\_exec\_sessions* resulta útil para recuperar información sobre todas las conexiones de usuario activas.

```

SELECT * FROM sys.dm_pdw_nodes_exec_connections;
SELECT * FROM sys.dm_pdw_nodes_exec_sessions;

```


Use la siguiente consulta para recuperar la información sobre la conexión actual.

```

SELECT * 
FROM sys.dm_pdw_nodes_exec_connections AS c 
   JOIN sys.dm_pdw_nodes_exec_sessions AS s 
   ON c.session_id = s.session_id 
WHERE c.session_id = @@SPID;

```





## Investigación de la ejecución de la consulta
Pueden producirse situaciones donde la consulta no se completa o se ejecuta durante más tiempo del que se espera. En estos casos, puede usar los pasos siguientes para recopilar datos y concretar el problema.



### PASO 1: Buscar la consulta que quiera investigar

```

-- Monitor running queries
SELECT * FROM sys.dm_pdw_exec_requests WHERE status = 'Running';

-- Find the longest running queries
SELECT * FROM sys.dm_pdw_exec_requests ORDER BY total_elapsed_time DESC;

```

Guarde el identificador de solicitud de la consulta.


  
### PASO 2: Comprobar si la consulta espera recursos

```

-- Find waiting tasks for your session.
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status, 
      requests.start_time,  
      waits.type,  
      waits.object_type, 
      waits.object_name,  
      waits.state  
FROM   sys.dm_pdw_waits waits 
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id 
WHERE waits.request_id = 'QID33188'
ORDER BY waits.object_name, waits.object_type, waits.state;

```


Los resultados de la consulta anterior mostrarán el estado de espera de la solicitud.

- Si la consulta espera recursos de otra consulta, el estado será **AcquireResources**.
- Si la consulta tiene todos los recursos necesarios y no está en espera, el estado será **Granted**. En este caso, continúe para observar los pasos de la consulta.




### PASO 3: Buscar el paso de ejecución más larga de la consulta

Use el identificador de solicitud para recuperar una lista de todos los pasos de la consulta distribuida. Para encontrar el paso de larga ejecución, observe el tiempo total transcurrido.

```

-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.
 
SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID33209'
ORDER BY step_index;

```

Guarde el índice de paso del paso de larga ejecución.

Compruebe la columna *operation\_type* del paso de consulta de larga ejecución:

- Continúe con el paso 4a para **operaciones SQL**: OnOperation, RemoteOperation, ReturnOperation.
- Continúe con el paso 4b para **Operaciones de movimiento de datos**: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation.




### PASO 4a: Buscar el progreso de la ejecución de un paso de SQL

Use el identificador de solicitud y el índice de paso para recuperar información sobre la distribución de consultas de SQL Server como parte del paso de SQL en la consulta. Guarde el id. de distribución y SPID.

```

-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID33209' AND step_index = 2;

```


Use la consulta siguiente para recuperar el plan de ejecución de SQL Server para el paso de SQL en un nodo determinado.

```

-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node. 
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);

```



### PASO 4b: Buscar el progreso de la ejecución de un paso de DMS

Use el identificador de solicitud y el índice de paso para recuperar información sobre el paso de movimiento de datos que se ejecuta en cada distribución.

```

-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.
 
SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID33209' AND step_index = 2;

```

- Compruebe la columna *total\_elapsed\_time* para ver si una distribución determinada tarda bastante más que otras en el movimiento de datos. 
- Para la distribución de larga ejecución, compruebe la columna *rows\_processed* para ver si el número de filas que se mueven desde esa distribución es mucho mayor que para las demás. Esto muestra que la consulta tiene asimetría de datos.





## Investigación de la asimetría de datos

```

-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED("dbo.FactInternetSales");

```


El resultado de esta consulta mostrará el número de filas de tabla que se almacenan en cada una de las 60 distribuciones de la base de datos. Para obtener un rendimiento óptimo, las filas de la tabla distribuida se deben repartir uniformemente entre todas las distribuciones. Para obtener más información, vea [diseño de tablas][].



## Pasos siguientes
Para obtener más sugerencias sobre cómo administrar el Almacenamiento de datos SQL, vea [información general sobre administración][].

<!--Image references-->

<!--Article references-->
[información general sobre administración]: sql-data-warehouse-overview-manage.md
[diseño de tablas]: sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!---HONumber=AcomDC_0114_2016-->