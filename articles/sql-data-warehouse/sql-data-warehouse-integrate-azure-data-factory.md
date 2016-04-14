<properties
   pageTitle="Uso de Factoría de datos de Azure con Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para usar Factoría de datos de Azure (ADF) con Almacenamiento de datos SQL para el desarrollo de soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Uso de Factoría de datos de Azure con Almacenamiento de datos SQL

Factoría de datos de Azure ofrece un método completamente administrado para organizar la transferencia de datos y la ejecución de los procedimientos almacenados en Almacenamiento de datos SQL. Esto le permitirá configurar y programar con mayor facilidad los complejos procedimientos de extracción, transformación y carga (ETL) con Almacenamiento de datos SQL. Para obtener una introducción más completa de Factoría de datos de Azure, consulte la [documentación sobre Factoría de datos de Azure][].

## Movimiento de datos 

Factoría de datos de Azure permite el movimiento entre orígenes locales y los distintos servicios de Azure. En general, la integración actual con Factoría de datos de Azure es compatible con el movimiento de datos desde y hacia las ubicaciones siguientes:

+ Almacenamiento de blobs de Azure
+ Base de datos SQL de Azure
+ SQL Server local
+ SQL Server en IaaS

Para obtener información sobre cómo configurar una actividad de copia de datos, consulte [Copia de datos con Factoría de datos de Azure](../data-factory/data-factory-data-movement-activities.md).

## Procedimientos almacenados
 De la misma manera en que se puede programar la transferencia de datos, Factoría de datos de Azure también se puede usar para organizar la ejecución de procedimientos almacenados. Esto permite que se creen canalizaciones más complejas y extiende la capacidad de Factoría de datos de Azure para aprovechar la potencia de cálculo de Almacenamiento de datos SQL.

## Pasos siguientes
Para obtener información general sobre la integración, consulte [Información general de la integración de Almacenamiento de datos SQL](sql-data-warehouse-overview-integrate.md). Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo de Almacenamiento de datos SQL](sql-data-warehouse-overview-develop.md).

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: https://azure.microsoft.com/es-ES/documentation/articles/data-factory-azure-sql-connector/
[SQL Data Warehouse development overview]: https://azure.microsoft.com/es-ES/documentation/articles/sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]: https://azure.microsoft.com/es-ES/documentation/articles/sql-data-warehouse-overview-integrate/

<!--MSDN references-->

<!--Other Web references-->
[documentación sobre Factoría de datos de Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[Copy data with Azure Data Factory]: https://azure.microsoft.com/es-ES/documentation/articles/data-factory-data-movement-activities/

<!---HONumber=AcomDC_0114_2016-->