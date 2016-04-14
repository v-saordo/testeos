<properties
	pageTitle="Lógica de reintentos de PHP para la conexión a Base de datos SQL | Microsoft Azure"
	description="Presenta un programa PHP de ejemplo que se conecta a la Base de datos SQL de Azure desde un cliente de Windows mediante el control de errores transitorios y proporciona vínculos a los componentes de software necesarios que necesita el cliente."
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


# Conexión a la Base de datos SQL mediante PHP en Windows mediante el control de errores transitorios


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tema muestra cómo puede conectarse a la base de datos SQL de Azure desde una aplicación cliente escrita en PHP que se ejecuta en Windows.


[AZURE.INCLUDE [sql-database-develop-includes-prerequisites-php-windows](../../includes/sql-database-develop-includes-prerequisites-php-windows.md)]

### Base de datos SQL

Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos de ejemplo. Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**.


## Paso 1: Obtención de detalles de la conexión

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Paso 2: Conexión y consulta

El programa de demostración está diseñado para que un error transitorio producido durante un intento de conexión provoque un reintento. Pero un error transitorio durante un comando de consulta provoca que el programa descarte la conexión y cree una nueva antes de reintentar el comando de consulta. No se recomienda ni lo contrario esta opción de diseño. El programa de demostración muestra la flexibilidad del diseño que se encuentra disponible.

<br>La longitud de este ejemplo de código se debe principalmente a la lógica de excepción catch. Hay disponible una versión más corta de este archivo Program.cs [aquí](sql-database-develop-php-simple-windows.md). <br>El método Main se encuentra en Program.cs. La pila de llamadas se ejecuta del modo indicado a continuación: * Main llama a ConnectAndQuery. * ConnectAndQuery llama a EstablishConnection. * EstablishConnection llama a IssueQueryCommand.

La función [sqlsrv\_query()](http://php.net/manual/en/function.sqlsrv-query.php) puede usarse para recuperar un conjunto de resultados de una consulta realizada a la Base de datos SQL. Esta función acepta básicamente cualquier consulta y el objeto de conexión, y devuelve un conjunto de resultados que se puede iterar mediante el uso de [sqlsrv\_fetch\_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php).

	<?php
		// Variables to tune the retry logic.  
		$connectionTimeoutSeconds = 30;  // Default of 15 seconds is too short over the Internet, sometimes.
		$maxCountTriesConnectAndQuery = 3;  // You can adjust the various retry count values.
		$secondsBetweenRetries = 4;  // Simple retry strategy.
		$errNo = 0;
		$serverName = "tcp:yourdatabase.database.windows.net,1433";
		$connectionOptions = array("Database"=>"AdventureWorks",
		   "Uid"=>"yourusername", "PWD"=>"yourpassword", "LoginTimeout" => $connectionTimeoutSeconds);
		$conn;
		$errorArr = array();
		for ($cc = 1; $cc <= $maxCountTriesConnectAndQuery; $cc++)
		{
		    $errorArr = array();
		    $ctr = 0;
		    // [A.2] Connect, which proceeds to issue a query command.
		    $conn = sqlsrv_connect($serverName, $connectionOptions);  
		    if( $conn == true)
		    {
		        echo "Connection was established";
		        echo "<br>";

		        $tsql = "SELECT [CompanyName] FROM SalesLT.Customer";
		        $getProducts = sqlsrv_query($conn, $tsql);
		        if ($getProducts == FALSE)
		            die(FormatErrors(sqlsrv_errors()));
		        $productCount = 0;
		        $ctr = 0;
		        while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
		        {   
		            $ctr++;
		            echo($row['CompanyName']);
		            echo("<br/>");
		            $productCount++;
		            if($ctr>10)
		                break;
		        }
		        sqlsrv_free_stmt($getProducts);
		        break;
		    }
		    // Adds any the error codes from the SQL Exception to an array.
		    else {  
		        if( ($errors = sqlsrv_errors() ) != null) {
		            foreach( $errors as $error ) {
		                $errorArr[$ctr] = $error['code'];
		                $ctr = $ctr + 1;
		            }
		        }
		        $isTransientError = TRUE;
		        // [A.4] Check whether sqlExc.Number is on the whitelist of transients.
		        $isTransientError = IsTransientStatic($errorArr);
		        if ($isTransientError == TRUE)  // Is a static persistent error...
		        {
		            echo("Persistent error suffered, SqlException.Number==". $errorArr[0].". Program Will terminate.");
		            echo "<br>";
		            // [A.5] Either the connection attempt or the query command attempt suffered a persistent SqlException.
		            // Break the loop, let the hopeless program end.
		            exit(0);
		        }
		        // [A.6] The SqlException identified a transient error from an attempt to issue a query command.
		        // So let this method reloop and try again. However, we recommend that the new query
		        // attempt should start at the beginning and establish a new connection.
		        if ($cc >= $maxCountTriesConnectAndQuery)
		        {
		            echo "Transient errors suffered in too many retries - " . $cc ." Program will terminate.";
		            echo "<br>";
		            exit(0);
		        }
		        echo("Transient error encountered.  SqlException.Number==". $errorArr[0]. " . Program might retry by itself.");  
		        echo "<br>";
		        echo $cc . " Attempts so far. Might retry.";
		        echo "<br>";
		        // A very simple retry strategy, a brief pause before looping. This could be changed to exponential if you want.
		        sleep(1*$secondsBetweenRetries);
		    }
		    // [A.3] All has gone well, so let the program end.
		}
		function IsTransientStatic($errorArr) {
		    $arrayOfTransientErrorNumbers = array(4060, 10928, 10929, 40197, 40501, 40613);
		    $result = array_intersect($arrayOfTransientErrorNumber, $errorArr);
		    $count = count($result);
		    if($count >= 0) //change to > 0 later.
		        return TRUE;
		}
	?>

## Pasos siguientes

Para obtener más información sobre el uso y la instalación de PHP, vea [Acceso a bases de datos de SQL Server con PHP](http://technet.microsoft.com/library/cc793139.aspx).

<!---HONumber=AcomDC_0107_2016-->