<properties
	pageTitle="Introducción a consultas entre bases de datos (particionamiento vertical) | Microsoft Azure"	
	description="cómo usar la consulta de base de datos elástica con bases de datos con particiones verticales"
	services="sql-database"
	documentationCenter=""  
	manager="jeffreyg"
	authors="sidneyh"/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="torsteng" />

# Introducción a consultas entre bases de datos (particiones verticales) 

La consulta de base de datos elástica (vista previa) para Base de datos SQL de Azure le permite ejecutar consultas T-SQL que distribuyen varias bases de datos con un único punto de conexión. Este tema se aplica a [bases de datos con particiones verticales](sql-database-elastic-query-vertical-partitioning.md).

Una vez completado, sabrá: cómo configurar y usar una Base de datos SQL de Azure para realizar consultas que distribuyan múltiples bases de datos relacionadas.

Para obtener más información sobre la característica de la consulta de base de datos elástica, consulte la [Información general sobre consulta de bases de datos elásticas de Base de datos SQL de Azure](sql-database-elastic-query-overview.md).

## Crear las base de datos de ejemplo

Para empezar, debemos crear dos bases de datos, **Clientes** y **Pedidos**, ya sea en el mismo servidor o en diferentes servidores lógicos.

Ejecute las siguientes consultas en la base de datos **Pedidos** para crear la tabla **OrderInformation** e introducir los datos de ejemplo.

	CREATE TABLE [dbo].[OrderInformation]( 
		[OrderID] [int] NOT NULL, 
		[CustomerID] [int] NOT NULL 
		) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Ahora, ejecute la siguiente consulta en la base de datos Clientes para crear la tabla CustomerInformation e introducir los datos de ejemplo.

	CREATE TABLE [dbo].[CustomerInformation]( 
		[CustomerID] [int] NOT NULL, 
		[CustomerName] [varchar](50) NULL, 
		[Company] [varchar](50) NULL 
		CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
	) 
	INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
	INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
	INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## Creación de objetos de base de datos
### Clave maestra y credenciales de ámbito de base de datos


Se usan para conectarse al administrador de mapas de particiones y particiones:

1. Abra SQL Server Management Studio o SQL Server Data Tools en Visual Studio.
2. Conéctese a la base de datos Pedidos y ejecute los siguientes comandos de T-SQL:

		CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
		CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
		WITH IDENTITY = '<username>', 
		SECRET = '<password>';  

	"username" y "contraseña" deben ser el nombre de usuario y la contraseña usados para iniciar sesión en la base de datos Clientes.

### Orígenes de datos externos
Para crear un origen de datos externo, ejecute el siguiente comando en la base de datos Pedidos:

	CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
		(TYPE = RDBMS, 
		LOCATION = '<server_name>.database.windows.net', 
		DATABASE_NAME = 'Customers', 
		CREDENTIAL = ElasticDBQueryCred, 
	) ;

### Tablas externas
Cree una tabla externa en la base de datos Pedidos, que coincida con la definición de la tabla CustomerInformation:

	CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
	( [CustomerID] [int] NOT NULL, 
	  [CustomerName] [varchar](50) NOT NULL, 
	  [Company] [varchar](50) NOT NULL) 
	WITH 
	( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## Ejecución de la consulta de T-SQL de la base de datos elástica de ejemplo

Una vez que haya definido su origen de datos externo y las tablas externas, puede usar el T-SQL para consultar sus las tablas externas. Ejecute esta consulta en la base de datos Pedidos:

	SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
	FROM OrderInformation 
	INNER JOIN CustomerInformation 
	ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## Coste

En la actualidad, la característica de consulta de base de datos elástica se incluye en el coste de la base de datos SQL de Azure.

Para obtener información de precios, vea [Precio de Base de datos SQL](/pricing/details/sql-database).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->

<!--anchors-->

<!---HONumber=AcomDC_0128_2016-->