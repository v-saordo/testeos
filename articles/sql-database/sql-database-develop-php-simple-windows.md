<properties
	pageTitle="Conexión a la base de datos SQL mediante PHP en Windows"
	description="Presenta un programa PHP de ejemplo que se conecta a la base de datos SQL de Azure desde un cliente de Windows y proporciona vínculos a los componentes de software necesarios que necesita el cliente."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="meetb"/>


# Conexión a la base de datos SQL mediante PHP en Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tema muestra cómo puede conectarse a la base de datos SQL de Azure desde una aplicación cliente escrita en PHP que se ejecuta en Windows.


[AZURE.INCLUDE [sql-database-develop-includes-prerequisites-php-windows](../../includes/sql-database-develop-includes-prerequisites-php-windows.md)]

### Base de datos SQL

Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos de ejemplo. Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**.


## Paso 1: Obtención de detalles de la conexión

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## Paso 2: Conexión


La función **OpenConnection** se llama casi al principio de todas las demás funciones siguientes.


	function OpenConnection()
	{
		try
		{
			$serverName = "tcp:myserver.database.windows.net,1433";
			$connectionOptions = array("Database"=>"AdventureWorks",
				"Uid"=>"MyUser", "PWD"=>"MyPassword");
			$conn = sqlsrv_connect($serverName, $connectionOptions);
			if($conn == false)
				die(FormatErrors(sqlsrv_errors()));
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Paso 3: Ejecución de una consulta

La función [sqlsrv\_query()](http://php.net/manual/en/function.sqlsrv-query.php) puede usarse para recuperar un conjunto de resultados de una consulta realizada a la Base de datos SQL. Esta función acepta básicamente cualquier consulta y el objeto de conexión, y devuelve un conjunto de resultados que se puede iterar mediante el uso de [sqlsrv\_fetch\_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php).

	function ReadData()
	{
		try
		{
			$conn = OpenConnection();
			$tsql = "SELECT [CompanyName] FROM SalesLT.Customer";
			$getProducts = sqlsrv_query($conn, $tsql);
			if ($getProducts == FALSE)
				die(FormatErrors(sqlsrv_errors()));
			$productCount = 0;
			while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
			{
				echo($row['CompanyName']);
				echo("<br/>");
				$productCount++;
			}
			sqlsrv_free_stmt($getProducts);
			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Paso 4: Inserción de una fila

En este ejemplo se muestra cómo ejecutar la instrucción [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) de forma segura, pasar parámetros que protejan la aplicación ante vulnerabilidad de [inyección de código SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) y recuperar el valor [Clave principal](https://msdn.microsoft.com/library/ms179610.aspx) generado automáticamente.


	function InsertData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT 			INSERTED.ProductID VALUES ('SQL Server 1', 'SQL Server 2', 0, 0, getdate())";
			//Insert query
			$insertReview = sqlsrv_query($conn, $tsql);
			if($insertReview == FALSE)
				die(FormatErrors( sqlsrv_errors()));
			echo "Product Key inserted is :";
			while($row = sqlsrv_fetch_array($insertReview, SQLSRV_FETCH_ASSOC))
			{   
				echo($row['ProductID']);
			}
			sqlsrv_free_stmt($insertReview);
			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}

## Paso 5: Reversión de una transacción


Este ejemplo de código muestra el uso de transacciones con las que podrá realizar lo siguiente:

- Iniciar una transacción

- Insertar una fila de datos, actualizar otra fila de datos

- Confirmar la transacción si la inserción y actualización se realizaron correctamente y revertir la transacción si uno de ellos no lo ha sido


		function Transactions()
		{
			try
			{
			$conn = OpenConnection();

			if (sqlsrv_begin_transaction($conn) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			$tsql1 = "INSERT INTO SalesLT.SalesOrderDetail (SalesOrderID,OrderQty,ProductID,UnitPrice)
			VALUES (71774, 22, 709, 33)";
			$stmt1 = sqlsrv_query($conn, $tsql1);

			/* Set up and execute the second query. */
			$tsql2 = "UPDATE SalesLT.SalesOrderDetail SET OrderQty = (OrderQty + 1) WHERE ProductID = 709";
			$stmt2 = sqlsrv_query( $conn, $tsql2);

			/* If both queries were successful, commit the transaction. */
			/* Otherwise, rollback the transaction. */
			if($stmt1 && $stmt2)
			{
			       sqlsrv_commit($conn);
			       echo("Transaction was commited");
			}
			else
			{
			    sqlsrv_rollback($conn);
			    echo "Transaction was rolled back.\n";
			}
			/* Free statement and connection resources. */
			sqlsrv_free_stmt( $stmt1);
			sqlsrv_free_stmt( $stmt2);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
		}


## Pasos siguientes


Para obtener más información sobre el uso y la instalación de PHP, vea [Acceso a bases de datos de SQL Server con PHP](http://technet.microsoft.com/library/cc793139.aspx).

<!---HONumber=AcomDC_0107_2016-->