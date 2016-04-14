<properties
   pageTitle="Supervisión de Base de datos SQL de Azure con vistas de administración dinámica | Microsoft Azure"
   description="Obtenga información sobre cómo detectar y diagnosticar problemas comunes de rendimiento con vistas de administración dinámica para supervisar Base de datos SQL de Microsoft Azure."
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
   ms.date="01/22/2016"
   ms.author="rickbyh"/>

# Supervisión de Base de datos SQL de Azure con vistas de administración dinámica

Base de datos de SQL de Microsoft Azure habilita un subconjunto de vistas de administración dinámica para diagnosticar problemas de rendimiento, que pueden deberse a consultas bloqueadas o de ejecución prolongada, cuellos de botella de recursos, planes de consulta deficientes, etc. En este tema se ofrece información sobre cómo detectar problemas comunes de rendimiento con vistas de administración dinámica.

Base de datos SQL admite parcialmente tres categorías de vistas de administración dinámica:

- Vistas de administración dinámica relacionadas con bases de datos.
- Vistas de administración dinámica relacionadas con ejecuciones.
- Vistas de administración dinámica relacionadas con transacciones.

Para obtener información detallada sobre las vistas de administración dinámica, vea [Vistas y funciones de administración dinámica (Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) en los libros en pantalla de SQL Server.

## Permisos

En Base de datos SQL, para realizar consultas en una vista de administración dinámica se requiere disponer de permisos **VIEW DATABASE STATE**. El permiso **VIEW DATABASE STATE** devuelve información sobre todos los objetos de la base de datos actual. Para conceder el permiso **VIEW DATABASE STATE** a un usuario de base de datos en concreto, ejecute la consulta siguiente:

```GRANT VIEW DATABASE STATE TO database_user; ```

En una instancia de SQL Server local, las vistas de administración dinámica devuelven la información de estado del servidor. En Base de datos SQL, devuelven información relativa únicamente a la base de datos lógica actual.

## Calculando tamaño de la base de datos

La siguiente consulta devuelve el tamaño de la base de datos en megabytes:

```
-- Calcula el tamaño de la base de datos. SELECT 
SELECT SUM(reserved\_page\_count)*8.0/1024
FROM sys.dm\_db\_partition\_stats;
GO
```

La consulta siguiente devuelve el tamaño de objetos individuales (en megabytes) de la base de datos:

```
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## Supervisión de conexiones

Puede usar la vista [sys.dm\_exec\_connections](https://msdn.microsoft.com/library/ms181509.aspx) para recuperar información sobre las conexiones establecidas con un servidor concreto de Base de datos SQL de Azure y los detalles de cada conexión. Además, la vista [sys.dm\_exec\_sessions](https://msdn.microsoft.com/library/ms176013.aspx) resulta útil para recuperar información sobre todas las conexiones de usuario activas y las tareas internas. La siguiente consulta recupera información sobre la conexión actual:

```
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [AZURE.NOTE] Al ejecutar las vistas **sys.dm\_exec\_requests** y **sys.dm\_exec\_sessions**, si el usuario tiene permiso **VIEW DATABASE STATE** en la base de datos, verá todas las sesiones en ejecución en la base de datos; en caso contrario, el usuario solo verá la sesión actual.

## Supervisión del rendimiento de las consultas

Las consultas de ejecución lenta o prolongada pueden consumir una cantidad significativa de recursos del sistema. En esta sección se muestra cómo usar vistas de administración dinámica para detectar algunos problemas comunes de rendimiento de las consultas. Una referencia anterior pero todavía útil para solucionar problemas, es el artículo de Microsoft TechNet, [Solución de problemas de rendimiento en SQL Server 2008](http://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx).

### Búsqueda de las N mejores consultas

El siguiente ejemplo devuelve información acerca de las cinco consultas principales clasificadas en función del tiempo promedio de CPU. Este ejemplo agrega las consultas conforme a sus hash de consulta, por lo que las consultas lógicamente equivalentes se agrupan por sus consumos de recursos acumulativos.

```
SELECT TOP 5 query_stats.query_hash AS "Query Hash",
    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
    MIN(query_stats.statement_text) AS "Statement Text"
FROM
    (SELECT QS.*,
    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
    ((CASE statement_end_offset
        WHEN -1 THEN DATALENGTH(ST.text)
        ELSE QS.statement_end_offset END
            - QS.statement_start_offset)/2) + 1) AS statement_text
     FROM sys.dm_exec_query_stats AS QS
     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
GROUP BY query_stats.query_hash
ORDER BY 2 DESC;
```

### Supervisión de consultas bloqueadas

Las consultas lentas o de larga ejecución pueden contribuir al consumo excesivo de recursos y ser la consecuencia de consultas bloqueadas. La causa del bloqueo puede ser un diseño deficiente de las aplicaciones, los planes de consulta incorrectos, la falta de índices útiles, etc. Puede usar la vista sys.dm\_tran\_locks para obtener información sobre la actividad de bloqueo actual en el servidor de Base de datos SQL de Azure. Para obtener un ejemplo de código, consulte [sys.dm\_tran\_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx) en los Libros en pantalla de SQL Server.

### Supervisión de planes de consulta

Un plan de consulta ineficaz también puede aumentar el consumo de CPU. En el ejemplo siguiente, se usa la vista [sys.dm\_exec\_query\_stats](https://msdn.microsoft.com/library/ms189741.aspx) para determinar qué consulta emplea la mayor cantidad acumulativa de CPU.

```
SELECT
    highest_cpu_queries.plan_handle,
    highest_cpu_queries.total_worker_time,
    q.dbid,
    q.objectid,
    q.number,
    q.encrypted,
    q.[text]
FROM
    (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
ORDER BY highest_cpu_queries.total_worker_time DESC;
```

## Consulte también

[Introducción a Base de datos SQL](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0128_2016-->