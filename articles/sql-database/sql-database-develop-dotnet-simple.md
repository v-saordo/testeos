<properties
	pageTitle="Conexión a Base de datos SQL mediante .NET (C#)"
	description="Use el código de ejemplo de este inicio rápido para crear una aplicación moderna con C# con el respaldado de una base de datos relacional eficaz en la nube con la base de datos SQL de Azure."
	services="sql-database"
	documentationCenter=""
	authors="tobbox"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="tobiast"/>


# Uso de bases de datos SQL de .NET (C#)


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


## Requisitos previos

### .NET Framework

.NET framework está previamente instalado con Windows. Para Linux y Mac OS X, puede descargar .NET Framework desde [Mono Project](http://www.mono-project.com/).

### Base de datos SQL

Vea la [página de introducción](sql-database-get-started.md) para aprender a crear una base de datos de ejemplo. Es importante seguir las directrices para crear una **plantilla de base de datos de AdventureWorks**. Los ejemplos que se muestran a continuación solo funcionan con el **esquema de AdventureWorks**.

## Paso 1: Obtención de la cadena de conexión

[AZURE.INCLUDE [sql-database-include-connection-string-dotnet-20-portalshots](../../includes/sql-database-include-connection-string-dotnet-20-portalshots.md)]

## Paso 2: Conexión

La clase [System.Data.SqlClient.SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx) se usa para conectarse a la base de datos SQL.


```
using System.Data.SqlClient;

class Sample
{
  static void Main()
  {
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={yourpassword};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
	  {
		  conn.Open();
	  }
  }
}
```

## Paso 3: Ejecución de una consulta

Las clases [System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) y [SqlDataReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqldatareader.aspx) pueden usarse para recuperar un conjunto de resultados de una consulta realizada a la base de datos SQL. Tenga en cuenta que System.Data.SqlClient también admite la recuperación de datos en [System.Data.DataSet](https://msdn.microsoft.com/library/system.data.dataset.aspx) sin conexión.

```
using System;
using System.Data.SqlClient;

class Sample
{
	static void Main()
	{
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={yourpassword};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
		{
			var cmd = conn.CreateCommand();
			cmd.CommandText = @"
					SELECT
						c.CustomerID
						,c.CompanyName
						,COUNT(soh.SalesOrderID) AS OrderCount
					FROM SalesLT.Customer AS c
					LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID
					GROUP BY c.CustomerID, c.CompanyName
					ORDER BY OrderCount DESC;";

			conn.Open();

			using(var reader = cmd.ExecuteReader())
			{
				while(reader.Read())
				{
					Console.WriteLine("ID: {0} Name: {1} Order Count: {2}", reader.GetInt32(0), reader.GetString(1), reader.GetInt32(2));
				}
			}					
		}
	}
}

```  

## Paso 4: Inserción de una fila

En este ejemplo se muestra cómo ejecutar la instrucción [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) de forma segura, pasar parámetros que protejan la aplicación ante vulnerabilidad de [inyección de código SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) y recuperar el valor [Clave principal](https://msdn.microsoft.com/library/ms179610.aspx) generado automáticamente.

```
using System;
using System.Data.SqlClient;

class Sample
{
    static void Main()
    {
		using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={yourpassword};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
        {
            var cmd = conn.CreateCommand();
            cmd.CommandText = @"
                INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate)
                OUTPUT INSERTED.ProductID
                VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP)";

            cmd.Parameters.AddWithValue("@Name", "SQL Server Express");
            cmd.Parameters.AddWithValue("@Number", "SQLEXPRESS1");
            cmd.Parameters.AddWithValue("@Cost", 0);
            cmd.Parameters.AddWithValue("@Price", 0);

            conn.Open();

            int insertedProductId = (int)cmd.ExecuteScalar();

            Console.WriteLine("Product ID {0} inserted.", insertedProductId);
        }
    }
}
```

<!---HONumber=AcomDC_0107_2016-->