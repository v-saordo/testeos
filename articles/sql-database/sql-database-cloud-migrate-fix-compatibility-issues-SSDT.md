<properties
   pageTitle="Solucionar problemas de compatibilidad de bases de datos de SQL Server antes de la migración a la Base de datos SQL"
   description="Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad, Asistente para migración de Microsoft Azure, SSDT"
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
   ms.date="02/22/2016"
   ms.author="carlrab"/>

# Solucionar problemas de compatibilidad de bases de datos de SQL Server con SSDT antes de la migración a la Base de datos SQL

Si determina que la Base de datos SQL Server de origen no es compatible, tiene varias opciones para corregir los problemas de compatibilidad de bases de datos identificados anteriormente.

> [AZURE.SELECTOR]
- Use [SQL Azure Migration Wizard](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Use [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Use [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-SSMS.md)

## Uso de SQL Server Data Tools para Visual Studio

Use SQL Server Data Tools para Visual Studio ("SSDT") para importar el esquema de base de datos a un proyecto de base de datos de Visual Studio para análisis. Para efectuar el análisis, especifique la plataforma de destino del proyecto como Base de datos SQL V12 y, a continuación, compile el proyecto. Si la compilación se realiza correctamente, esto significa que la base de datos es compatible. Si se produce un error en la compilación, puede resolver los errores de SSDT (o una de las demás herramientas descritas en este tema). Una vez que el proyecto se compila correctamente, puede publicarlo como una copia de la base de datos de origen y, a continuación, usar la función de comparación de datos de SSDT para copiar los datos de la base de datos de origen en la base de datos compatible con Azure SQL V12. Luego podrá migrar esta base de datos actualizada. Para usar esta opción, descargue la [versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx).

  ![Diagrama de migración de VSSSDT](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] Si solo es necesaria la migración del esquema, es posible publicar el esquema directamente desde Visual Studio a la base de datos SQL de Azure. Use este método cuando el esquema de la base de datos requiera más cambios de los que se pueden controlar mediante el asistente para la migración por sí solo.

## Siguiente paso: seleccionar el método de migración y realizar la migración

[Seleccione el método de migración](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database).

<!---HONumber=AcomDC_0224_2016-->