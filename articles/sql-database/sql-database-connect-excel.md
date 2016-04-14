<properties
	pageTitle="Conexión de Excel a Base de datos SQL | Microsoft Azure"
	description="Más información acerca de cómo conectar Microsoft Excel a Base de datos SQL de Azure en la nube. Importación de datos en Excel para la generación de informes y la exploración de datos."
	services="sql-database"
	keywords="conectar excel a sql, importar datos en excel"
	documentationCenter=""
	authors="joseidz"
	manager="jeffreyg"
	editor="jeffreyg"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/04/2016"
	ms.author="joseidz"/>


# Conexión de Excel a una Base de datos SQL de Azure y creación de un informe 

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Aprenda a conectar Excel a una Base de datos SQL con el fin de importar datos en Excel. A continuación, cree un informe sobre los datos.

En primer lugar necesitará una Base de datos SQL. Si no tiene una, consulte [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md) para tener una base de datos con datos de ejemplo en funcionamiento en unos minutos. En este artículo se importarán los datos de ejemplo en Excel de dicho artículo, pero puede seguir los pasos con sus propios datos.

También necesitará una copia de Excel. Este artículo usa [Microsoft Excel 2016](https://products.office.com/es-ES/).

## Conexión de Excel a una Base de datos SQL y creación de un informe

1.	Para conectar Excel a Base de datos SQL, abra Excel y, a continuación, cree un libro nuevo. O bien, abra el libro de Excel existente que desee conectar a Base de datos SQL.

2.	En la barra de menús de la parte superior de la página, haga clic sucesivamente en **Datos**, **De otros orígenes** y **De SQL Server**.

	![Selección de origen de datos: conexión de Excel a Base de datos SQL.](./media/sql-database-connect-excel/excel_data_source.png)

	Se abre el Asistente para la conexión de datos.

3.	En el cuadro de diálogo **Conectar con el servidor de la base de datos**, escriba el **nombre del servidor** que hospeda el servidor lógico al que quiere conectarse con el formato **<*nombreDeServidor*>.database.windows.net**. Por ejemplo, **adventureserver.database.windows.net**.

4.	En la sección **Credenciales de inicio de sesión**, haga clic en **Usar el nombre de usuario y la contraseña siguientes**, escriba el **Nombre de usuario** y la **Contraseña** que haya configurado para el servidor de Base de datos SQL cuando la creó y, finalmente, haga clic en **Siguiente**.

	> [AZURE.TIP] Los complementos de Excel [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) y [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) ofrecen experiencias similares.

5. En el cuadro de diálogo **Seleccionar base de datos y tabla**, seleccione la base de datos **AdventureWorks** en el menú desplegable y seleccione **vGetAllCategories** en la lista de tablas y vistas; y, a continuación, haga clic en **Siguiente**.

	![Selección de una base de datos y una tabla.][5]

6. En el cuadro de diálogo **Guardar archivo de conexión de datos y finalizar**, haga clic en **Finalizar**.

7. En el cuadro de diálogo **Importar datos**, seleccione **Gráfico dinámico** y haga clic en **Aceptar**.

	![Importación de datos en Excel: en el cuadro de diálogo Importar datos, seleccione Gráfico dinámico.][2]

8. En el cuadro de diálogo **Campos de gráfico dinámico**, seleccione la configuración siguiente para crear un informe del recuento de productos por categoría.

	![Configuración de informe de base de datos.][3]

	Un resultado correcto tiene este aspecto:

	![Correcto: Excel conectado a Base de datos SQL.][4]

## Pasos siguientes

Si es un desarrollador de software como servicio (SaaS), obtenga información sobre los [grupos de bases de datos elásticas](sql-database-elastic-pool.md). Puede administrar fácilmente grandes colecciones de bases de datos mediante [Trabajos de base de datos elástica](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=AcomDC_0204_2016-->