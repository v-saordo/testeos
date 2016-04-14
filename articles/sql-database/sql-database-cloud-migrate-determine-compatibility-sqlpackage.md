<properties
   pageTitle="Determinar la compatibilidad de la Base de datos SQL mediante SqlPackage.exe"
   description="Base de datos SQL de Microsoft Azure, migración de bases de datos, importación de bases de datos, compatibilidad de Base de datos SQL, SqlPackage"
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

# Determinar la compatibilidad de la Base de datos SQL mediante SqlPackage.exe

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

En este artículo aprenderá a determinar si una base de datos de SQL Server es compatible para migrar a la Base de datos SQL mediante la utilidad del símbolo del sistema [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).

## Uso de SqlPackage.exe

1. Abra un símbolo del sistema y cambie a un directorio que contiene la versión más reciente de sqlpackage.exe. Esta utilidad se incluye con Visual Studio y SQL Server. Descargue la [versión más reciente de SQL Server Data Tools para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) para obtener la versión más reciente de la utilidad SqlPackage.
2. Ejecute el siguiente comando SqlPackage con los argumentos siguientes para su entorno:

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1'

	| Argumento | Descripción |
	|---|---|
	| < server_name > | nombre de servidor de origen |
	| < database_name > | nombre de base de datos de origen |
	| < target_file > | nombre y ubicación del archivo BACPAC |
	| < schema_name.table_name > | tablas para las que se enviarán datos al archivo de destino |
	| < output_file > | nombre y ubicación del archivo de salida con los errores, si existen |

	La razón para el argumento /p:TableName es que solo deseamos comprobar la compatibilidad de la base de datos para la exportación a la Base de datos SQL de Azure V12, en lugar de exportar los datos desde todas las tablas. Lamentablemente, el argumento de exportación para sqlpackage.exe no admite no extraer ninguna tabla, por lo que necesitará especificar una única tabla pequeña. < output_file > contendrá el informe de los errores. La cadena "> 2>&1" une la salida estándar y el error estándar resultado de la ejecución del comando en el archivo de salida especificado.

	![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Abra el archivo de salida y revise los errores de compatibilidad, si los hay.

	![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Siguiente paso: solucionar problemas de compatibilidad, si existen

[Solución de problemas de compatibilidad de bases de datos](sql-database-cloud-migrate-fix-compatibility-issues.md),si existen.

<!---HONumber=AcomDC_1223_2015-->