<properties
   pageTitle="Dinamización y anulación de dinamización de datos en Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para dinamizar y anular la dinamización de datos en Almacenamiento de datos SQL de Azure para el desarrollo de soluciones."
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

# Dinamización y anulación de la dinamización de los datos en Almacenamiento de datos SQL

Puede dinamizar los datos en Almacenamiento de datos SQL mediante el uso de una instrucción CASE.

Este artículo contiene dos sencillos ejemplos sobre cómo dinamizar y anular la dinamización de una tabla sin usar la sintaxis de dinamización y anulación de dinamización encontrada en SQL Server.

## Dinamización

```
CREATE TABLE AnnualSales_pvt
WITH    (   DISTRIBUTION = ROUND_ROBIN
        )
AS
WITH SalesPVT 
AS
(   SELECT  [EnglishProductCategoryName]
    ,       CASE WHEN [CalendarYear] = 2001 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2001'
    ,       CASE WHEN [CalendarYear] = 2002 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2002'
    ,       CASE WHEN [CalendarYear] = 2003 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2003'
    ,       CASE WHEN [CalendarYear] = 2004 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2004'
    FROM    [dbo].[FactInternetSales] s
    JOIN    [dbo].[DimDate] d               ON s.[OrderDateKey]          = d.[DateKey]
    JOIN    [dbo].[DimProduct] p            ON s.[ProductKey]            = p.[ProductKey]
    JOIN    [dbo].[DimProductSubCategory] u ON p.[ProductSubcategoryKey] = u.[ProductSubcategoryKey]
    JOIN    [dbo].[DimProductCategory] c    ON u.[ProductCategoryKey]    = c.[ProductCategoryKey]
    GROUP BY 
            [EnglishProductCategoryName]
    ,       [CalendarYear]
)
SELECT  [EnglishProductCategoryName]
,       SUM([CY_2001])  AS 'CY_2001'
,       SUM([CY_2002])  AS 'CY_2002'
,       SUM([CY_2003])  AS 'CY_2003'
,       SUM([CY_2004])  AS 'CY_2004'
FROM    SalesPVT
GROUP BY 
        [EnglishProductCategoryName]
;
```

## Anulación de dinamización

La operación para anular la dinamización es un poco más compleja. Sin embargo, se puede lograr mediante el uso de `CASE`. Para ello, necesitará también determinar primero para cuántas columnas se va a anular la dinamización. En el ejemplo anterior se han dinamizado cuatro columnas. Vamos a ceñirnos a eso. Para llevar a cabo la anulación de la dinamización, necesitamos ampliar el conjunto de datos temporalmente en un factor de 4. El resultado es un proceso de dos pasos:

En primer lugar, cree una tabla temporal que contenga cuatro filas. La usaremos para ampliar los datos:

```
CREATE TABLE #Nmbr
WITH 
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION     = USER_DB
)
AS
SELECT 1 AS 'Number'
UNION ALL
SELECT 2
UNION ALL
SELECT 3
UNION ALL
SELECT 4
OPTION (LABEL = 'CTAS : #Nmbr : CREATE')
;
```

El segundo paso consiste en usar CASE para anular la dinamización de los datos de forma condicional y convertir de nuevo el conjunto en filas. Para lograr esto, debemos crear un producto cartesiano de la unión a la tabla temporal #Nmbr creada en el primer paso:

```
SELECT  [EnglishProductCategoryName]
,       CASE    c.[Number]
                WHEN 1 THEN [CY_2001]
                WHEN 2 THEN [CY_2002]
                WHEN 3 THEN [CY_2003]
                WHEN 4 THEN [CY_2004]
        END as Sales
FROM AnnualSales_pvt
JOIN #Nmbr c ON 1=1
WHERE   CASE    c.[Number]
                WHEN 1 THEN CY_2001
                WHEN 2 THEN CY_2002
                WHEN 3 THEN CY_2003
                WHEN 4 THEN CY_2004
        END IS NOT NULL
OPTION (LABEL = 'Unpivot :  : SELECT')
;
```

Finalmente, no olvide eliminar la tabla temporal para que todo quede limpio.

```
DROP TABLE #Nmbr
;
```

## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0114_2016-->