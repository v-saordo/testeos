<properties
   pageTitle="Visualización de datos de Almacenamiento de datos SQL con Power BI | Microsoft Azure"
   description="Visualización de datos de Almacenamiento de datos SQL con Power BI"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="" />

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;sonyama" />

# Visualización de datos con Power BI


> [AZURE.NOTE] Para completar este tutorial, necesita una base de datos de Almacenamiento de datos SQL que se haya cargado previamente con la base de datos de ejemplo AdventureWorksDW. [Creación de una nueva base de datos de Almacenamiento de datos SQL](sql-data-warehouse-get-started-provision.md) le muestra cómo crear una.
>
> Si ya tiene una base de datos de Almacenamiento de datos SQL pero no tiene datos de ejemplo, puede [cargar manualmente los datos de ejemplo][].

Este tutorial muestra cómo usar Power BI para conectar con Almacenamiento de datos SQL y crear unas visualizaciones básicas.

> [AZURE.VIDEO azure-sql-data-warehouse-sample-data-and-powerbi]

## Conexión a AdventureWorksDW

Para abrir Power BI y conectarse a la base de datos AdventureWorksDW:

1. Inicie sesión en el [Portal de Azure][].
2. Haga clic en **Bases de datos SQL** y elija su base de datos de Almacenamiento de datos SQL de AdventureWorks.

    ![Buscar la base de datos][1]

3. Haga clic en el botón "Abrir en Power BI".

    ![Botón Power BI][2]

4. Deberá ver la página de conexión de Almacenamiento de datos SQL que muestra la dirección web de la base de datos. Haga clic en Siguiente.

    ![Conexión de Power BI][3]

6. Escriba el nombre de usuario y la contraseña de Azure SQL Server y se conectará completamente a la base de datos de Almacenamiento de datos SQL.

    ![Inicio de sesión de Power BI][4]

1. Una vez que haya iniciado sesión en Power BI, haga clic en el conjunto de datos AdventureWorksDW en la hoja izquierda. Se abrirá la base de datos.

    ![Apertura de AdventureWorksDW en Power BI][5]



## Creación de un informe de Power BI para analizar los datos de ejemplo

Ahora está listo para usar Power BI para analizar los datos de ejemplo AdventureWorksDW. Para realizar el análisis, AdventureWorksDW tiene una vista denominada AggregateSales. Esta vista contiene algunas de las métricas clave para analizar las ventas de la empresa.

1. Para crear una asignación del importe de ventas según el código postal, en los campos de la derecha, haga clic en la vista AggregateSales para expandirla. A continuación, haga clic en las columnas PostalCode y SalesAmount para seleccionarlas.

    ![Selección de AggregateSales en Power BI][6]

    Power BI reconoce automáticamente estos datos como geográficos y los coloca en un mapa.

    ![Mapa Power BI][7]

2. Este paso crea un gráfico de barras que muestra la cantidad de ventas por los ingresos del cliente. Para crear esto, vaya a la vista expandida de AggregateSales. Haga clic en el campo SalesAmount. Arrastre el campo de ingresos de los clientes hacia la izquierda y colóquelo en el eje.

    ![Selección del eje en Power BI][8]

    Pasamos el gráfico de barras a la izquierda.

    ![Barra Power BI][9]

3. Este paso crea un gráfico de líneas que muestra el importe de ventas por fecha de pedido. Para crear esto, vaya a la vista expandida de AggregateSales. Haga clic en SalesAmount y OrderDate. En la columna de visualizaciones, haga clic en el icono de gráfico de líneas, que es el primer icono en la segunda línea bajo las visualizaciones.

	![Selección del gráfico de líneas de Power BI][10]

    Ahora tiene un informe que muestra tres visualizaciones de los datos diferentes.

    ![LíneaPower BI][11]

Para guardar el progreso en cualquier momento, haga clic en **Archivo** y seleccione **Guardar**.

## Pasos siguientes
Ahora que le hemos dado algún tiempo para familiarizarse con los datos de ejemplo, consulte cómo puede [desarrollar][], [cargar][] o [migrar][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: ./media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desarrollar]: ./sql-data-warehouse-overview-develop.md
[cargar]: ./sql-data-warehouse-overview-load.md
[cargar manualmente los datos de ejemplo]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Portal de Azure]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[connecting to SQL Data Warehouse]: ./sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!---HONumber=AcomDC_0309_2016-->