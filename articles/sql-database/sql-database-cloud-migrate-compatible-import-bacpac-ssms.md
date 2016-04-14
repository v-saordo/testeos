<properties
   pageTitle="Migrar una base de datos a la Base de datos SQL de Azure"
   description="Base de datos SQL de Microsoft Azure, implementación de base de datos, migración de base de datos, importación de base de datos, exportación de base de datos, asistente para migración"
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

# Importar desde BACPAC a la Base de datos SQL con SSMS

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

En este artículo se muestra cómo importar desde un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) a la Base de datos SQL mediante el Asistente para exportar aplicaciones de capa de datos en SQL Server Management Studio.

> [AZURE.NOTE]En los pasos siguientes se da por supuesto que la instancia lógica SQL de Azure está ya aprovisionada y que tiene a mano la información de conexión.

1. Compruebe que dispone de la versión más reciente de SQL Server Management Studio. Las nuevas versiones de Management Studio se actualizan mensualmente a fin de que sigan sincronizadas con las actualizaciones para el Portal de Azure.

	 >[AZURE.IMPORTANT]Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra Management Studio y conéctese a la base de datos de origen en el Explorador de objetos.

	![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Una vez creado el archivo BACPAC, conéctese al servidor de Base de datos SQL de Azure, haga clic con el botón secundario en la carpeta **Bases de datos** y haga clic en **Importar aplicación de capa de datos...**.

    ![Importar elemento de menú de aplicación de capa de datos](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

4.	En el Asistente para importación, elija el archivo BACPAC que acaba de exportar para crear la nueva base de datos en Base de datos SQL de Azure.

    ![Importar configuración](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

5.	Proporcione el **Nuevo nombre de base de datos** para la base de datos en Base de datos SQL de Azure, establezca **Edición de Base de datos SQL de Microsoft Azure** (nivel de servicio), **Tamaño máximo de la base de datos** y **Objetivo de servicio** (nivel de rendimiento).

    ![Configuración de base de datos](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

6.	Haga clic en **Siguiente** y, luego, en **Finalizar** para importar el archivo BACPAC a una nueva base de datos en el servidor de Base de datos SQL de Azure.

7. Con el Explorador de objetos, conéctese a la base de datos migrada en el servidor de Base de datos SQL de Azure.

8.	Mediante el Portal de Azure, vea la base de datos y sus propiedades.

<!---HONumber=AcomDC_1223_2015-->