<properties
	pageTitle="Conexión a la base de datos SQL mediante Python en Windows"
	description="Este tema muestra un ejemplo de código Pyton que puede usar para conectarse a la base de datos SQL de Azure desde un cliente Windows. El ejemplo usa el controlador pymssql."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="meetb"/>


# Conexión a la base de datos SQL mediante Python en Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tema presenta un ejemplo de código escrito en Python. El ejemplo se ejecuta en un equipo Windows. El ejemplo se conecta a una base de datos SQL de Azure mediante el controlador **pymssql**.


## Requisitos previos


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)


### Instalación de los módulos necesarios


Instale [pymssql](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql).

Asegúrese de elegir el archivo whl correcto.

Por ejemplo: si va a usar Python 2.7 en un equipo de 64 bits, elija pymssql‑2.1.1‑cp27‑none‑win\_amd64.whl. Una vez descargado el archivo .whl, colóquelo en la carpeta C:/Python27.

A continuación, instale el controlador pymssql con pip desde la línea de comandos. Use el comando cd para tener acceso al directorio C:/Python27 y ejecute lo siguiente

	pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

Puede encontrar instrucciones para habilitar el uso de pip [aquí](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).


### Base de datos SQL

Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos de ejemplo. Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**.

## Paso 1: Obtención de detalles de la conexión

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## Paso 2: Conexión


La función [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) se usa para conectarse a la base de datos SQL.

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## Paso 3: Ejecución de una consulta

La función [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) puede usarse para recuperar un conjunto de resultados de una consulta realizada a la base de datos SQL. Esta función acepta cualquier consulta y devuelve un conjunto de resultados que se puede iterar mediante el uso de [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## Paso 4: Inserción de una fila

En este ejemplo se muestra cómo ejecutar la instrucción [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) de forma segura, pasar parámetros que protejan la aplicación ante vulnerabilidad de [inyección de código SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) y recuperar el valor [Clave principal](https://msdn.microsoft.com/library/ms179610.aspx) generado automáticamente.


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## Paso 5: Reversión de una transacción


Este ejemplo de código muestra el uso de transacciones con las que podrá realizar lo siguiente:


- Iniciar una transacción

- Insertar una fila de datos

- Revertir la transacción para deshacer la inserción


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("BEGIN TRANSACTION")
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
	conn.rollback()

## Pasos siguientes

Para más información, vea el [Centro para desarrolladores de Python](/develop/python/).

<!---HONumber=AcomDC_0204_2016-->