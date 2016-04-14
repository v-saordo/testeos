<properties
   pageTitle="Carga de datos de ejemplo en Almacenamiento de datos SQL | Microsoft Azure"
   description="Obtenga información sobre cómo cargar datos de ejemplo en Almacenamiento de datos SQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Carga de datos de ejemplo en Almacenamiento de datos SQL

Mientras crea una instancia de Almacenamiento de datos SQL, puede cargar fácilmente algunos datos de ejemplo en ella. Si se saltó este paso durante el aprovisionamiento, también puede [cargar manualmente los datos de ejemplo][].

A continuación se proporciona una vista resumida de cómo se puede cargar AdventureWorksDW en la base de datos. Este conjunto de datos modela una estructura de almacenamiento de datos de ejemplo para una compañía ficticia llamada AdventureWorks con datos que representan las ventas y los clientes de la compañía.

## Agregar datos de ejemplo durante la creación
Para garantizar que los datos de ejemplo se cargan en Almacenamiento de datos SQL durante la implementación, siga estos pasos:

1. Inicie el proceso de creación; para ello, vaya al [Portal de Azure clásico][], haga clic en "+Nuevo", luego en "Datos y almacenamiento" y busque Almacenamiento de datos SQL. También puede buscar "Almacenamiento de datos SQL" en Marketplace. 
 
2. Una vez iniciado el proceso, asegúrese de hacer clic en la opción "Seleccionar origen" y configurarla como "Ejemplo". Si no va a crear un nuevo servidor, también se le pedirá que proporcione el inicio de sesión del servidor que se usará para la creación.


> [AZURE.NOTE]Para cargar datos de ejemplo en la instancia, debe permitir que los servicios de Azure tengan acceso al servidor (esta opción debe estar activada de forma predeterminada al crear un nuevo servidor). En caso contrario, la carga dará error, pero puede [cargar datos de ejemplo manualmente][].


## Uso de Power BI para analizar Adventureworks

El uso del conjunto de datos de ejemplo puede ser una buena manera de comenzar con Power BI. Después de cargar los datos de ejemplo, puede abrir una conexión con Almacenamiento de datos SQL. Para ello, puede hacer clic en el botón "Abrir en Power BI" en el Portal de Azure clásico o ir a [Power BI][] y [conectarse a Almacenamiento de datos SQL][]. Después de conectarse, se debe crear un nuevo conjunto de datos con el mismo nombre que el del almacenamiento de datos. Para facilitar el análisis, se ha creado una vista denominada "AggregateSales" con algunas de las métricas que son clave para analizar las ventas de la compañía. Puede hacer clic en el nombre de esta vista para expandirla y ver las columnas que contiene, y puede crear algunas visualizaciones rápidas siguiendo estos pasos:

1. Para empezar, podemos crear fácilmente un mapa de todas nuestras ventas; para ello, hacemos clic en las columnas "PostalCode" y "SalesAmount". Power BI reconoce incluso automáticamente estos datos como geográficos y los coloca en un mapa. 

2. Ahora, si quisiera crear un gráfico de barras de ventas simplemente puede hacer clic en la columna "SalesAmount" y Power BI lo creará automáticamente. Puede agregar profundidad adicional arrastrando el gráfico "CustomerIncome" al campo "Axis" a la izquierda de "AggregateSales" para mostrar los ingresos de ventas por cliente entre corchetes.

3. Por último, si desea crear una escala de tiempo de ventas, todo lo que tiene que hacer es clic en "SalesAmount", "OrderDate" y "Line Chart" (el primer icono de la segunda línea debajo de "Visualizations" (Visualizaciones)).

En cualquier momento puede guardar el progreso haciendo clic en el botón SAVE (GUARDAR) en la esquina superior izquierda y guardar las visualizaciones como un informe.

## Conexión a su ejemplo y consulta

También puede analizar los datos de ejemplo con los medios tradicionales. Como se describe en la documentación de [conexión y consulta][], puede conectarse a esta base de datos mediante SQL Server Data Tools en Visual Studio. Ahora que ha cargado algunos datos de ejemplo en el Almacenamiento de datos SQL, puede ejecutar rápidamente algunas consultas para empezar.

Podemos ejecutar una instrucción select simple para obtener toda la información de los empleados:

	SELECT * FROM DimEmployee;

También podemos ejecutar una consulta más compleja mediante construcciones como GROUP BY para ver la cantidad total de todas las ventas de cada día:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

Incluso podemos usar la cláusula WHERE para filtrar órdenes desde antes de una fecha determinada:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

De hecho, Almacenamiento de datos SQL admite casi todas las construcciones de T-SQL que SQL Server realiza, y se pueden encontrar algunas de las diferencias en nuestra documentación para [migrar código][].



## Pasos siguientes
Ahora que le hemos dado algún tiempo para familiarizarse con los datos de ejemplo, consulte como [desarrollar][], [cargar][] o [migrar][].

<!--Image references-->

<!--Article references-->
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desarrollar]: ./sql-data-warehouse-overview-develop.md
[cargar]: ./sql-data-warehouse-overview-load.md
[conexión y consulta]: ./sql-data-warehouse-get-started-connect.md
[migrar código]: ./sql-data-warehouse-migrate-code.md
[cargar datos de ejemplo manualmente]: ./sql-data-warehouse-get-started-manually-load-samples.md
[cargar manualmente los datos de ejemplo]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Portal de Azure clásico]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[conectarse a Almacenamiento de datos SQL]: ./sql-data-warehouse-integrate-power-bi.md

<!--MSDN references-->
[Microsoft Command Line Utilities for SQL Server]: http://www.microsoft.com/download/details.aspx?id=36433/

<!--Other Web references-->
[Sample Data Scripts]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip/

<!---HONumber=AcomDC_0114_2016-->