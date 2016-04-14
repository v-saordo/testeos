<properties
   pageTitle="Tablas temporales en el Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para usar las tablas temporales en el Almacenamiento de datos SQL Azure para desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Tablas temporales en el Almacenamiento de datos SQL
Las tablas temporales existen a nivel de sesión en el Almacenamiento de datos SQL. Se definen como tablas temporales locales y, a diferencia de las tablas de SQL Server, se puede tener acceso a ellas desde cualquier lugar dentro de la sesión.

Las tablas temporales son muy útiles al procesar datos, especialmente durante la transformación donde los resultados intermedios son transitorios.

En este artículo se resaltan algunos métodos específicos para utilizar las tablas temporales para ayudarlo a modularizar el código.

## Modularización de código

Puede aprovechar el hecho de que las tablas temporales se puedan ver en cualquier parte en una sesión de usuario para ayudarlo a modularizar el código de la aplicación.

Vamos a exponer un ejemplo práctico.

El siguiente procedimiento almacenado genera el archivo DDL necesario para actualizar las estadísticas de todas las columnas de la base de datos:

```
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
	,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
```

Observe que no se ha hecho nada realmente con estos datos. El procedimiento simplemente ha generado el DDL y lo ha almacenado en una tabla temporal.

Sin embargo, en el Almacenamiento de datos SQL es posible usar la tabla temporal fuera del procedimiento creado. Esto es diferente de SQL Server. De hecho se puede utilizar la tabla temporal **en cualquier parte** dentro de la sesión.

Esto puede generar código más modular y fácil de administrar.

```
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

En algunos casos, las funciones en línea y de varias instrucciones también pueden reemplazarse con esta técnica.

> [AZURE.NOTE]También puede extender esta solución. Si solo quería actualizar una única tabla, por ejemplo, lo único que tiene que hacer es filtrar la tabla #stats\_ddl.

## Limitaciones de tablas temporales
El Almacenamiento de datos SQL impone algunas limitaciones al implementar las tablas temporales.

Las principales limitaciones son:

- No se admiten tablas temporales globales.
- No se pueden crear vistas en las tablas temporales.


## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0114_2016-->