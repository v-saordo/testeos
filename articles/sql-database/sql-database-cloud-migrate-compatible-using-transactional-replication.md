<properties
   pageTitle="Migrar a la Base de datos SQL con la replicación transaccional"
   description="Base de datos SQL de Microsoft Azure, migración de bases de datos, importación de bases de datos, replicación transaccional"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# Migrar base de datos de SQL Server a la Base de datos SQL con la replicación transaccional

Si no se puede permitir quitar la base de datos de SQL Server de producción mientras se lleva a cabo la migración, puede usar la replicación transaccional de SQL Server como solución de migración. Con la replicación transaccional, todos los cambios en los datos o el esquema que se producen entre el momento en que se inicia la migración y el momento en que finaliza aparecerán en Base de datos SQL de Azure. Una vez completada la migración, basta con cambiar la cadena de conexión de las aplicaciones para que apunte a Base de datos SQL de Azure en lugar de apuntar a la base de datos local. Cuando la replicación transaccional recupera todos los cambios pendientes en la base de datos local y todas las aplicaciones apuntan a la base de datos de Azure, ya se puede desinstalar con seguridad la replicación y dejar Base de datos SQL de Azure como sistema de producción.

 ![Diagrama de SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)


La replicación transaccional es una tecnología integrada con SQL Server desde SQL Server 6.5. Es una tecnología muy madura y comprobada, que conocen la mayoría de los administradores de base de datos y con la que tienen experiencia. Con la [vista previa de SQL Server 2016](http://www.microsoft.com/server-cloud/products/sql-server-2016/), ahora es posible configurar Base de datos SQL de Azure como un [suscriptor de replicación transaccional](https://msdn.microsoft.com/library/mt589530.aspx) a la publicación local. La experiencia que obtiene configurándolo desde Management Studio es exactamente la misma que si configura un suscriptor de replicación transaccional en un servidor local. Se admite la compatibilidad con este escenario cuando el publicador y el distribuidor es al menos de una de las siguientes versiones de SQL Server:

 - SQL Server 2016 CTP3 (vista previa) y versiones posteriores 
 - SQL Server 2014 SP1 CU3 y versiones posteriores
 - SQL Server 2014 RTM CU10 y versiones posteriores
 - SQL Server 2012 SP2 CU8 y versiones posteriores
 - SQL Server 2012 SP3 

También puede usar la replicación transaccional para migrar un subconjunto de la base de datos local. La publicación que se replica en Base de datos SQL de Azure puede limitarse a un subconjunto de las tablas de la base de datos que se replica. Además, para cada tabla que se replica, puede limitar los datos a un subconjunto de filas o un subconjunto de columnas.

<!---HONumber=AcomDC_0302_2016-->