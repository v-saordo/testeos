<properties
   pageTitle="Diseñar decisiones y técnicas de codificación para el desarrollo del Almacenamiento de datos SQL | Microsoft Azure"
   description="Conceptos de desarrollo, decisiones de diseño, recomendaciones y técnicas de codificación para el Almacenamiento de datos SQL."
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

# Decisiones de diseño y técnicas de codificación para el Almacenamiento de datos SQL

Eche un vistazo a estos artículos de desarrollo para comprender mejor las decisiones de diseño clave, las recomendaciones y las técnicas de codificación para el Almacenamiento de datos SQL.

## Decisiones de diseño clave
En los artículos siguientes se abordan algunos de los conceptos y las decisiones de diseño clave que es preciso comprender para el desarrollo de un almacenamiento de datos distribuido con Almacenamiento de datos SQL:

- [conexiones][]
- [simultaneidad][]
- [transacciones][]
- [esquemas definidos por el usuario][]
- [diseño de tablas][]
- [claves de distribución hash][]
- [particiones de tablas][]
- [CTAS][]
- [estadísticas][]

## Recomendaciones de desarrollo y técnicas de codificación
En estos artículos se abordan técnicas de codificación, sugerencias y recomendaciones específicas para el desarrollo de su Almacenamiento de datos SQL:

- [procedimientos almacenados][]
- [etiquetas][]
- [vistas][]
- [tablas temporales][]
- [SQL dinámico][]
- [bucle][]
- [cambio de nombre de objetos][]
- [datos de tablas dinámicas][]
- [opciones de agrupar por][]
- [asignación de variables][]

## Pasos siguientes
Una vez leídos los artículos sobre desarrollo, eche un vistazo a la página [Referencia de Transact-SQL][] para obtener más detalles sobre la sintaxis admitida en Almacenamiento de datos SQL.

<!--Image references-->

<!--Article references-->
[simultaneidad]: sql-data-warehouse-develop-concurrency.md
[conexiones]: sql-data-warehouse-develop-connections.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[SQL dinámico]: sql-data-warehouse-develop-dynamic-sql.md
[opciones de agrupar por]: sql-data-warehouse-develop-group-by-options.md
[claves de distribución hash]: sql-data-warehouse-develop-hash-distribution-key.md
[etiquetas]: sql-data-warehouse-develop-label.md
[bucle]: sql-data-warehouse-develop-loops.md
[datos de tablas dinámicas]: sql-data-warehouse-develop-pivot-unpivot.md
[cambio de nombre de objetos]: sql-data-warehouse-develop-rename.md
[estadísticas]: sql-data-warehouse-develop-statistics.md
[procedimientos almacenados]: sql-data-warehouse-develop-stored-procedures.md
[diseño de tablas]: sql-data-warehouse-develop-table-design.md
[particiones de tablas]: sql-data-warehouse-develop-table-partitions.md
[tablas temporales]: sql-data-warehouse-develop-temporary-tables.md
[transacciones]: sql-data-warehouse-develop-transactions.md
[esquemas definidos por el usuario]: sql-data-warehouse-develop-user-defined-schemas.md
[asignación de variables]: sql-data-warehouse-develop-variable-assignment.md
[vistas]: sql-data-warehouse-develop-views.md

[Referencia de Transact-SQL]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0114_2016-->