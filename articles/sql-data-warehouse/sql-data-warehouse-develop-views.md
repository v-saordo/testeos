<properties
   pageTitle="Vistas en el Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para usar las vistas Transact-SQL en el Almacenamiento de datos SQL Azure para desarrollar soluciones."
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

 
# Vistas en el Almacenamiento de datos SQL

Las vistas son especialmente útiles en el Almacenamiento de datos SQL. Se pueden usar de formas diferentes para mejorar la calidad de la solución.

En este artículo se destacan algunos ejemplos de cómo enriquecer su solución mediante la implementación con vistas. Existen algunas limitaciones que también deben considerarse.

## Abstracción de arquitectura
Se trata de un patrón de aplicación muy común para volver a crear tablas con la característica CREATE TABLE AS SELECT (CTAS) seguida de un patrón de cambio de nombre de objetos mientras se cargan los datos.

En el ejemplo siguiente se agregan registros de fecha nuevos a una dimensión de fecha. Observe cómo un nuevo objeto, DimDate\_New, se crea por primera vez y luego cambia de nombre para reemplazar la versión original del objeto.

```
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS 
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Sin embargo, esto puede traducirse en objetos de tabla que aparecen y desaparecen de la vista de un usuario en el Explorador de objetos de SQL Server (SSDT). Las vistas pueden utilizarse para proporcionar una capa de presentación coherente a los consumidores de datos de almacenamiento mientras se cambia el nombre de los objetos subyacentes. Proporcionar acceso a los datos mediante una vista significa que los usuarios no necesitan tener visibilidad de las tablas subyacentes. Esto proporciona una experiencia de usuario coherente, al tiempo que garantiza que los diseñadores de almacenamiento de datos puedan desarrollar el modelo de datos y maximizar también el rendimiento mediante el uso de CTAS durante el proceso de carga de datos.

## Optimización del rendimiento
Las vistas son una manera inteligente de aplicar combinaciones de rendimiento optimizado entre tablas. Por ejemplo, la vista puede incorporar una clave de distribución redundante como parte de los criterios de combinación para minimizar el movimiento de datos. Otra razón podría ser forzar una consulta específica o una sugerencia de combinación. Esto garantiza que la combinación siempre se realice de manera óptima y que no dependa de que el usuario tenga que acordarse de crear la combinación correctamente.

## Limitaciones
Las vistas en el almacenamiento de datos SQL son solo metadatos.

En consecuencia, las siguientes opciones no se encuentran disponibles: - No hay opción de enlace de esquemas. - Las tablas de base no se pueden actualizar a través de la vista. - Las vistas no pueden crearse sobre tablas temporales. - No se admiten las sugerencias EXPAND / NOEXPAND. - No hay vistas indexadas en el Almacenamiento de datos SQL.


## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo de Almacenamiento de datos SQL][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo de Almacenamiento de datos SQL]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0114_2016-->