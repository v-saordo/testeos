<properties
	pageTitle="Conexión a Base de datos SQL mediante Node.Js con Tedious en Mac OS X"
	description="Este tema muestra un ejemplo de código Node.js que puede usar para conectarse a la base de datos SQL de Azure. El ejemplo usa el controlador Tedious para la conexión."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="meetb"/>


# Conexión a Base de datos SQL mediante Node.Js con Tedious en Mac OS X


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tema presenta un ejemplo de código de Node.js que se ejecuta en Mac OS X. El ejemplo se conecta a Base de datos SQL de Azure mediante el controlador de Tedious.


## Requisitos previos


Instale **node**, a menos que ya esté instalado en el equipo.


Para instalar node.js en OSX 10.10 Yosemite puede descargar un paquete binario precompilado que realice una instalación sencilla y sin problemas. [Diríjase a nodejs.org](http://nodejs.org/) y haga clic en el botón de instalación para descargar el paquete más reciente.

Instale el paquete desde el archivo .dmg siguiendo el asistente de instalación que instalará tanto **node** como **npm**. npm son las siglas en inglés de Node Package Manager (Administrador de paquetes de node), que facilita la instalación de paquetes adicionales para node.js.


Cuando el equipo esté configurado con **node** y **npm**, navegue hasta el directorio donde piensa crear el proyecto Node.js y escriba los comandos siguientes.


	npm init
	npm install tedious


**init npm** para crear un proyecto de nodo. Para conservar los valores predeterminados durante la creación del proyecto, presione Entrar hasta que se cree el proyecto. Ahora verá el archivo **package.json** en el directorio del proyecto.

### Base de datos SQL

Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos de ejemplo. Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**.

## Paso 1: Obtención de detalles de la conexión

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Paso 2: Conexión

La función [new Connection](http://pekim.github.io/tedious/api-connection.html) se utiliza para conectarse a la base de datos SQL.

	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Microsoft Azure, you need this:
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
	// If no error, then good to proceed.
		console.log("Connected");
	});


## Paso 3: Ejecución de una consulta


Todas las instrucciones SQL se ejecutan utilizando la función [new Request()](http://pekim.github.io/tedious/api-request.html). Si la instrucción devuelve filas, como una instrucción select, se podrán recuperar mediante la función [request.on()](http://pekim.github.io/tedious/api-request.html). Si no hay ninguna fila, la función [request.on()](http://pekim.github.io/tedious/api-request.html) devuelve listas vacías.


	var Connection = require('tedious').Connection;
	var Request = require('tedious').Request;
	var TYPES = require('tedious').TYPES;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// When you connect to Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement();
	});


	function executeStatement() {
		request = new Request("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function(err) {
	  	if (err) {
	   		console.log(err);}
		});
		var result = "";
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        result+= column.value + " ";
		      }
		    });
		    console.log(result);
		    result ="";
		});

		request.on('done', function(rowCount, more) {
		console.log(rowCount + ' rows returned');
		});
		connection.execSql(request);
	}


## Paso 4: Inserción de una fila

En este ejemplo se muestra cómo ejecutar la instrucción [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) de forma segura, pasar parámetros que protejan la aplicación ante vulnerabilidad de [inyección de código SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) y recuperar el valor [Clave principal](https://msdn.microsoft.com/library/ms179610.aspx) generado automáticamente.


	var Connection = require('tedious').Connection;
	var Request = require('tedious').Request
	var TYPES = require('tedious').TYPES;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement1();
	});


	function executeStatement1() {
		request = new Request("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP);", function(err) {
		 if (err) {
		 	console.log(err);}
		});
		request.addParameter('Name', TYPES.NVarChar,'SQL Server Express 2014');
		request.addParameter('Number', TYPES.NVarChar , 'SQLEXPRESS2014');
		request.addParameter('Cost', TYPES.Int, 11);
		request.addParameter('Price', TYPES.Int,11);
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        console.log("Product id of inserted item is " + column.value);
		      }
		    });
		});		
		connection.execSql(request);
	}


## Pasos siguientes

Para más información, vea el [Centro para desarrolladores de Node.js](/develop/nodejs/).

<!---HONumber=AcomDC_0107_2016-->