<properties
	pageTitle="Grupo de bases de datos elásticas para bases de datos SQL | Microsoft Azure"
	description="Descubra cómo puede dominar el crecimiento explosivo de sus bases de datos SQL mediante los grupos de bases de datos elásticas; una manera de compartir los recursos disponibles en gran cantidad de bases de datos."
	keywords="base de datos elástica, bases de datos SQL"	
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jeffreyg"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/11/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Domine el crecimiento explosivo de las Bases de datos SQL, gracias a bases de datos elásticas que le permitirán compartir recursos

Un desarrollador de SaaS debe crear y administrar decenas, cientos o incluso miles de bases de datos SQL. Los grupos elásticos simplifican la creación, el mantenimiento y la administración del rendimiento entre estas bases de datos dentro de un presupuesto controlado. Agregue o quite bases de datos del grupo a voluntad. [Cree un grupo de bases de datos elásticas](sql-database-elastic-pool-portal.md) para sus bases de datos SQL en cuestión de minutos mediante el Portal de Microsoft Azure, [PowerShell](sql-database-elastic-pool-powershell.md) o [C#](sql-database-elastic-pool-csharp.md).

Para ver información detallada sobre las API y los errores, consulte [Referencia de grupos de bases de datos elásticas de Base de datos SQL](sql-database-elastic-pool-reference.md).

## Cómo funciona

Un patrón común de la aplicación SaaS es dar a cada cliente una base de datos. Cada cliente (base de datos) tiene requisitos impredecibles de recursos de memoria, E/S y CPU. Con estas subidas y bajadas en la demanda, ¿cómo asigna recursos? Tradicionalmente, había dos opciones: aprovisionar en exceso los recursos según el uso máximo, y pagar de más, o bien aprovisionar por defecto para ahorrar costos, a costa de rendimiento y satisfacción del cliente durante las horas de actividad máxima. Los grupos de bases de datos elásticas resuelven este problema al garantizar que las bases de datos obtienen los recursos de rendimiento que necesitan, cuando los necesitan, al tiempo que proporcionan un mecanismo sencillo de asignación de recursos dentro de un presupuesto predecible.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

En Base de datos SQL, la medida relativa de la capacidad de una base de datos para administrar demandas de recursos se expresa en unidades de transacción de base de datos (DTU) para bases de datos únicas y DTU elásticas (eDTU) para grupos de bases de datos elásticas. Consulte [Introducción a Base de datos SQL](sql-database-technical-overview.md#understand-dtus) para aprender más sobre las DTU y las eDTU.

A un grupo se le asigna un número fijo de eDTU, por un precio fijo. Dentro del grupo, a las bases de datos individuales se les proporciona la flexibilidad de escalarse automáticamente dentro de unos parámetros establecidos. Con cargas elevadas, una base de datos puede consumir más eDTU para satisfacer la demanda. Las bases de datos con cargas ligeras consumen menos y las bases de datos sin carga no consumen ninguna eDTU. El aprovisionamiento de recursos para el grupo entero en lugar de para bases de datos únicas simplifica las tareas de administración. Además, cuenta con un presupuesto predecible para el grupo.

Se pueden agregar eDTU adicionales a un grupo existente sin que la base de datos experimente tiempo de inactividad o su rendimiento se vea afectado de forma negativa. De manera similar, si ya no se necesitan eDTU adicionales, se pueden quitar de un grupo existente en cualquier momento dado.

Y puede agregar bases de datos al grupo o quitar bases de datos del grupo. Si una base de datos infrautiliza recursos de forma predecible, sáquela del grupo.

## ¿Qué bases de datos se incluyen en un grupo?

![Bases de datos SQL que comparten elementos eDTU en un grupo de bases de datos elásticas.][1]

Las bases de datos que son buenos candidatos para grupos de bases de datos elásticas tienen normalmente períodos de actividad y otros períodos de inactividad. En el ejemplo anterior, puede ver la actividad de una base de datos única, cuatro bases de datos y, por último, un grupo de bases de datos elásticas con 20 bases de datos. Las bases de datos con actividad variable con el tiempo son excelentes candidatos para grupos elásticos porque no están activas al mismo tiempo y pueden compartir eDTU. No todas las bases de datos se ajustan a este patrón. Las bases de datos que tienen una demanda de recursos más constante son las más idóneas para los niveles de servicio Basic, Standard y Premium, donde los recursos se asignan por separado.

[Consideraciones de precio y rendimiento para un grupo de bases de datos elásticas](sql-database-elastic-pool-guidance.md).


> [AZURE.NOTE] Los grupos de bases de datos elásticas están actualmente en vista previa y solo estarán disponibles en servidores con bases de datos SQL V12.

## Trabajos de base de datos elástica

Con un grupo, se simplifican las tareas de administración al ejecutarse los scripts en **[trabajos elásticos](sql-database-elastic-jobs-overview.md)**. Un trabajo de base de datos elástica elimina la mayoría de las tediosas tareas asociadas con un gran número de bases de datos. Para comenzar, consulte [Introducción a Trabajos de base de datos elástica](sql-database-elastic-jobs-getting-started.md).

Para más información sobre otras herramientas, consulte el [mapa de aprendizaje de herramientas de bases de datos elásticas](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/).

## Características de continuidad del negocio para bases de datos de un grupo

Actualmente en la vista previa, las bases de datos elásticas admiten la mayoría de las [características de continuidad empresarial](sql-database-business-continuity.md) que están disponibles para las bases de datos únicas en los servidores V12.

### Restauración a un momento dado

El sistema realiza automáticamente copias de seguridad de las bases de datos del grupo de bases de datos elásticas y la directiva de retención de copias de seguridad es la misma que el nivel de servicio correspondiente de la de las bases de datos únicas. En resumen, las bases de datos de cada nivel tienen un intervalo de restauración diferente:

* **Grupo Basic**: posibilidad de restauración en cualquier punto dentro de los últimos 7 días. 
* **Grupo Standard**: posibilidad de restauración en cualquier punto dentro de los últimos 14 días.
* **Grupo Premium**: posibilidad de restauración en cualquier punto dentro de los últimos 35 días. 

Durante la vista previa, se restaurarán las bases de datos de un grupo a una nueva base de datos del mismo grupo. Las bases de datos quitadas siempre se restaurarán como bases de datos independientes fuera del grupo en el nivel de rendimiento más bajo de ese nivel de servicio. Por ejemplo, una base de datos elástica de un grupo estándar que se quita se restaurará como base de datos de S0. Las operaciones de restauración de bases de datos pueden realizarse a través del Portal de Azure o mediante programación con la API de REST. Los cmdlet de PowerShell podrán utilizarse próximamente.

### Restauración geográfica

La restauración geográfica permite recuperar una base de datos de un grupo en un servidor en una región distinta. Durante la vista previa, para restaurar una base de datos de un grupo en un servidor diferente, el servidor de destino deberá tener un grupo con el mismo nombre que el grupo de origen. Si es necesario, cree un nuevo grupo en el servidor de destino y asígnele el mismo nombre antes de restaurar la base de datos. Si no existe un grupo con el mismo nombre en el servidor de destino, se producirá un error en la operación de restauración geográfica. Para obtener más información, consulte [Recuperación mediante la restauración geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore).


### Replicación geográfica

La replicación geográfica está disponible para cualquier base de datos en un grupo de bases de datos elásticas estándar o premium. Una o todas las bases de datos de una asociación de replicación geográfica pueden estar en un grupo de bases de datos elásticas siempre que los niveles de servicio sean los mismos. Puede configurar la replicación geográfica para los grupos de bases de datos elásticas mediante el [Portal de Azure](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md) o [Transact-SQL](sql-database-geo-replication-transact-sql.md).

### importación y exportación

No se admite la exportación de una base de datos desde un grupo. Actualmente, no se admite la importación de una base de datos directamente en un grupo, pero puede importar en una base de datos única y luego mover la base de datos a un grupo.


<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

<!---HONumber=AcomDC_0302_2016-->