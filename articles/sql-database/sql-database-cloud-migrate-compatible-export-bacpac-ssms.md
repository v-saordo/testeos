
<properties
   pageTitle="Exportación de una Base de datos SQL Server a un archivo BACPAC mediante SSMS"
   description="Base de datos SQL de Microsoft Azure, migración de bases de datos, exportación de bases de datos, exportación de archivo BACPAC, asistente para exportación de aplicaciones de capa de datos"
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

# Exportación de una Base de datos SQL Server a un archivo BACPAC mediante SSMS

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

 
En este artículo se muestra cómo exportar una base de datos de SQL Server a un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) mediante el Asistente para exportar aplicaciones de capa de datos en SQL Server Management Studio.

1. Compruebe que dispone de la versión más reciente de SQL Server Management Studio. Las nuevas versiones de Management Studio se actualizan mensualmente a fin de que sigan sincronizadas con las actualizaciones para el Portal de Azure.

	 >[AZURE.IMPORTANT]Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra Management Studio y conéctese a la base de datos de origen en el Explorador de objetos.

	![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Haga clic con el botón secundario en la base de datos de origen del Explorador de objetos, seleccione **Tareas** y haga clic en **Exportar aplicación de capa de datos...**

	![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. En el asistente para la exportación, configure la exportación para guardar el archivo BACPAC en una ubicación de disco local o en un blob de Azure. El archivo BACPAC exportado siempre incluye el esquema de la base de datos completa y, de manera predeterminada, los datos de todas las tablas. Si desea excluir los datos de algunas o todas las tablas, use la pestaña de opciones avanzadas. Por ejemplo, puede exportar únicamente los datos para las tablas de referencia en lugar de desde todas las tablas.

	![Exportar configuración](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

## Siguiente paso: Importar a la Base de datos SQL desde un archivo BACPAC

- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portal de Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

<!---HONumber=AcomDC_1223_2015-->