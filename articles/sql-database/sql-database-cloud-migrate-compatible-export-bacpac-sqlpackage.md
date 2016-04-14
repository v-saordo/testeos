<properties
   pageTitle="Exportación de una Base de datos SQL Server a un archivo BACPAC mediante SqlPackage"
   description="Base de datos SQL de Microsoft Azure, migración de bases de datos, exportación de bases de datos,exportación de archivo BACPAC, sqlpackage"
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

# Exportación de una Base de datos SQL Server a un archivo BACPAC mediante SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

En este artículo se muestra cómo exportar una base de datos de SQL Server a un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) mediante la utilidad del símbolo del sistema [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Esta utilidad se incluye con Visual Studio y SQL Server. También puede [descargar](https://msdn.microsoft.com/library/mt204009.aspx) la versión más reciente de SQL Server Data Tools para obtener esta utilidad.

1. Abra un símbolo del sistema y cambie a un directorio que contiene la utilidad de línea de comandos sqlpackage.exe; esta utilidad se incluye con Visual Studio y SQL Server. Use la búsqueda en el equipo para encontrar la ruta de acceso en su entorno.
2. Ejecute el siguiente comando sqlpackage.exe con los argumentos siguientes para su entorno:

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	| Argumento | Descripción |
	|---|---|
	| < server_name > | nombre de servidor de origen |
	| < database_name > | nombre de base de datos de origen |
	| < target_file > | nombre y ubicación del archivo BACPAC |

	![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Siguiente paso: Importar a la Base de datos SQL desde un archivo BACPAC

- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portal de Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

<!---HONumber=AcomDC_1223_2015-->