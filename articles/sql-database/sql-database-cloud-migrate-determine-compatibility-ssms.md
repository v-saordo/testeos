<properties
   pageTitle="Determinar la compatibilidad de la Base de datos SQL mediante SSMS"
   description="Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad de Base de datos SQL, Asistente para exportación de aplicaciones de capa de datos"
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

# Determinar la compatibilidad de la Base de datos SQL mediante SSMS

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
 
En este artículo aprenderá a determinar si una base de datos de SQL Server es compatible para migrar a la Base de datos SQL mediante el Asistente para exportación de aplicaciones de capa de datos en SQL Server Management Studio.

## Uso de SQL Server Management Studio

1. Compruebe que dispone de la versión más reciente de SQL Server Management Studio. Las nuevas versiones de Management Studio se actualizan mensualmente a fin de que sigan sincronizadas con las actualizaciones para el Portal de Azure.

 	 >[AZURE.IMPORTANT]Le recomendamos usar siempre la versión más reciente de Management Studio para que pueda estar siempre al día de las actualizaciones de Microsoft Azure y Base de datos SQL. [Actualice SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra Management Studio y conéctese a la base de datos de origen en el Explorador de objetos.
3. Haga clic con el botón secundario en la base de datos de origen del Explorador de objetos, seleccione **Tareas** y haga clic en **Exportar aplicación de capa de datos...**

	![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. En el asistente para la exportación, haga clic en **Siguiente** y, en la pestaña **Configuración**, configure la exportación para guardar el archivo BACPAC en una ubicación de disco local o en un blob de Azure. Solo se guardará un archivo BACPAC si no tiene problemas de compatibilidad de bases de datos. Si hay problemas de compatibilidad, se mostrarán en la consola.

	![Exportar configuración](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Haga clic en la pestaña **Avanzadas** y desactive la casilla **Seleccionar todo** para omitir la exportación de datos. En este caso, nuestro objetivo es solo comprobar la compatibilidad.

	![Exportar configuración](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Haga clic en **Siguiente** y, luego, en **Finalizar**. Los problemas de compatibilidad de bases de datos, si los hay, aparecerán después de que el asistente valide el esquema.

	![Exportar configuración](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Si no aparece ningún error, la base de datos es compatible y estará listo para la migración. Si se producen errores, deberá corregirlos. Para ver los errores, haga clic en **Error** de **Validando esquema**. ![Exportar configuración](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.	Si el archivo *.BACPAC se generó correctamente, indica que la base de datos es compatible con la Base de datos SQL; ya puede migrar.

## Siguiente paso: solucionar problemas de compatibilidad, si existen

[Solución de problemas de compatibilidad de bases de datos](sql-database-cloud-migrate-fix-compatibility-issues.md),si existen.

<!---HONumber=AcomDC_1223_2015-->