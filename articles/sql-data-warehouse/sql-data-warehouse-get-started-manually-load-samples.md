<properties
   pageTitle="Carga de datos de ejemplo en Almacenamiento de datos SQL | Microsoft Azure"
   description="Carga de datos de ejemplo en Almacenamiento de datos SQL"
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

#Carga de datos de ejemplo en Almacenamiento de datos SQL

Una vez que [creó una instancia de base de datos de Almacenamiento de datos SQL][create a SQL Data Warehouse database instance], el paso siguiente es crear y cargar algunas tablas. Puede usar los scripts de ejemplo de Adventure Works que creamos para Almacenamiento de datos SQL a fin de crear y cargar tablas para una empresa ficticia llamada Adventure Works. Estos scripts usa sqlcmd para ejecutar SQL y bcp para cargar datos. Si todavía no tiene instaladas estas herramientas, siga estos vínculos para [instalar bcp][] e [instalar sqlcmd][].

Siga estos pasos sencillos para cargar la base de datos de ejemplo de Adventure Works en Almacenamiento de datos SQL:

1. Descargue los [scripts de ejemplo de Adventure Works para Almacenamiento de datos SQL][].

2. Extraiga los archivos del zip que descargó en un directorio de la máquina local.

3. Edite el archivo extraído aw\_create.bat y defina las siguientes variables que se encuentran en la parte superior del archivo. No deje espacios en blanco entre "=" y el parámetro. A continuación, puede ver ejemplos del aspecto que deberían tener las modificaciones.

    	server=mylogicalserver.database.windows.net
    	user=mydwuser
    	password=Mydwpassw0rd
    	database=mydwdatabase

4. En un símbolo del sistema de Windows, ejecute el archivo aw\_create.bat editado. Asegúrese de estar en el directorio en el que guardó la versión editada de aw\_create.bat. Este script le permitirá hacer lo siguiente:
	* Anular cualquier tabla o vista de Adventure Works que ya exista en la base de datos.
	* Crear las vistas y tablas de Adventure Works.
	* Cargar cada tabla de Adventure Works con bcp.
	* Validar los recuentos de fila de cada tabla de Adventure Works.
	* Recopilar estadísticas en cada columna de cada tabla de Adventure Works.


##Consultar los datos de ejemplo

Una vez que carga algunos datos de ejemplo en Almacenamiento de datos SQL, puede ejecutar rápidamente algunas consultas. Para ejecutar una consulta, conéctese a la base de datos de Adventure Works que acaba de crear en Almacenamiento de datos SQL de Azure con Visual Studio y SSDT, tal como se describe en el documento de [conexión][].

Un ejemplo de una instrucción select simple para obtener toda la información de los empleados:

	SELECT * FROM DimEmployee;

Un ejemplo de una consulta más compleja con construcciones como GROUP BY para ver la cantidad total de todas las ventas de cada día:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

Ejemplos de una instrucción SELECT con una cláusula WHERE para filtrar pedidos desde antes de una fecha determinada:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

Almacenamiento de datos SQL admite casi todas las construcciones T-SQL compatibles con SQL Server. Todas las diferencias existentes se registran en nuestra documentación sobre la [migración del código][].

## Pasos siguientes
Ahora que tuvo la oportunidad de probar algunas consultas con datos de ejemplo, revise cómo [desarrollar][], [cargar][] o [migrar][] a Almacenamiento de datos SQL.

<!--Image references-->

<!--Article references-->
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desarrollar]: ./sql-data-warehouse-overview-develop.md
[cargar]: ./sql-data-warehouse-overview-load.md
[conexión]: ./sql-data-warehouse-get-started-connect.md
[migración del código]: ./sql-data-warehouse-migrate-code.md
[create a SQL Data Warehouse database instance]: ./sql-data-warehouse-get-started-provision.md
[instalar bcp]: ./sql-data-warehouse-load-with-bcp.md
[instalar sqlcmd]: ./sql-data-warehouse-get-started-connect-query-sqlcmd.md

<!--Other Web references-->
[scripts de ejemplo de Adventure Works para Almacenamiento de datos SQL]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip

<!---HONumber=AcomDC_0114_2016-->