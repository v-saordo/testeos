<properties
   pageTitle="Solucionar problemas de compatibilidad de bases de datos de SQL Server antes de la migración a la Base de datos SQL"
   description="Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad, Asistente para migración de Microsoft Azure"
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

# Solucionar problemas de compatibilidad de bases de datos de SQL Server antes de la migración a la Base de datos SQL

Si determina que la Base de datos SQL Server de origen no es compatible, tiene varias opciones para corregir los problemas de compatibilidad de bases de datos identificados anteriormente.

> [AZURE.SELECTOR]
- Use [SQL Azure Migration Wizard](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Use [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Use [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

## Uso del Asistente para migración a SQL de Azure

Use la herramienta de CodePlex del [Asistente para migración de SQL Azure](http://sqlazuremw.codeplex.com/) para generar un script T-SQL desde una base de datos de origen que luego se transforma por el Asistente para que sea compatible con la Base de datos SQL y después se conecte a la Base de datos SQL de Azure para ejecutar el script. Esta herramienta también analizará los archivos de seguimiento para determinar problemas de compatibilidad. El script puede generarse solo con el esquema o puede incluir datos en formato BCP. Hay documentación adicional, incluidas instrucciones paso a paso, disponible en CodePlex en el [Asistente para migración de SQL de Azure](http://sqlazuremw.codeplex.com/).

 ![Diagrama de migración de SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  >[AZURE.NOTE]Tenga en cuenta que no todos los esquemas incompatibles que se pueden detectar por el asistente se pueden corregir por sus transformaciones integradas. El script incompatible que no se pueda resolver se notificará como un error, con comentarios insertados en el script generado. Si se detectan numerosos errores, use Visual Studio o SQL Server Management Studio para analizar y corregir cada error que no se pudo corregir con el Asistente para migración de SQL Server.

## Siguiente paso: seleccionar el método de migración y realizar la migración

[Seleccione el método de migración](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_1223_2015-->