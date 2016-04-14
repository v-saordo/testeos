<properties
   pageTitle="Migrar la base de datos de SQL Server a Base de datos SQL con el Asistente para implementar base de datos en Base de datos de Microsoft Azure"
   description="Base de datos SQL de Microsoft Azure, migración de bases de datos, Asistente para bases de datos de Microsoft Azure"
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

# Migrar la base de datos de SQL Server a Base de datos SQL con el Asistente para implementar base de datos en Base de datos de Microsoft Azure

El Asistente para implementar bases de datos en base de datos SQL de Microsoft Azure en SQL Server Management Studio migra una [bases de datos de SQL Server compatible](../sql-database-cloud-migrate.md) directamente en un servidor de Base de datos SQL de Azure.

## Uso del Asistente de implementación de bases de datos en la Base de datos de Microsoft Azure

> [AZURE.NOTE]En los pasos siguientes se supone que tiene un [servidor de Base de datos SQL aprovisionado](../sql-database-get-started.md).

1. Compruebe que dispone de la versión más reciente de SQL Server Management Studio. Las nuevas versiones de Management Studio se actualizan mensualmente a fin de que sigan sincronizadas con las actualizaciones para el Portal de Azure.

    > [AZURE.IMPORTANT]Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra Management Studio y conéctese a la base de datos de SQL Server que se va a migrar en el Explorador de objetos.
3. Haga clic con el botón derecho en la base de datos del Explorador de objetos, seleccione **Tareas** y haga clic en **Implementar base de datos en Base de datos SQL de Microsoft Azure...**

	![Implementación en Azure desde el menú Tareas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	En el asistente para la implementación, haga clic en **Siguiente** y en **Conectar** para configurar la conexión con el servidor de Base de datos SQL.

	![Implementación en Azure desde el menú Tareas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. En el cuadro de diálogo Conectar con el servidor, escriba la información de conexión para conectarse a su servidor de Base de datos SQL.

	![Implementación en Azure desde el menú Tareas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.	Proporcione el **Nuevo nombre de base de datos** para el nuevo nombre de base de datos, establezca **Edición de Base de datos SQL de Microsoft Azure** ([nivel de servicio](sql-database-service-tiers.md)), **Tamaño máximo de la base de datos**, **Objetivo de servicio** (nivel de rendimiento) y **Nombre de archivo temporal** para el archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) que crea este asistente durante el proceso de migración.

	![Exportar configuración](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	Complete el asistente para migrar la base de datos. Según el tamaño y la complejidad de la base de datos, es posible que la implementación tarde desde unos minutos hasta varias horas. Si el asistente detecta problemas de compatibilidad, se mostrarán errores en la pantalla y la migración no continuará. Para instrucciones sobre cómo solucionar problemas de compatibilidad de bases de datos, vaya a [Solución de problemas de compatibilidad de bases de datos](sql-database-cloud-migrate-fix-compatibility-issues).

7.	Con el Explorador de objetos, conéctese a la base de datos migrada en el servidor de Base de datos SQL de Azure.
8.	Mediante el Portal de Azure, vea la base de datos y sus propiedades.

## Siguiente paso: solucionar problemas de compatibilidad, si existen

[Solución de problemas de compatibilidad de bases de datos](sql-database-cloud-migrate-fix-compatibility-issues.md),si existen.

<!---HONumber=AcomDC_1223_2015-->