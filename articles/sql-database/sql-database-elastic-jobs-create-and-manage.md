<properties
	pageTitle="Creación y administración de trabajos de Base de datos elástica | Micosoft Azure"
	description="Siga los pasos necesarios de los procesos de creación y administración de un trabajo de base de datos elástica."
	services="sql-database"
	documentationCenter=""
	manager="jhubbard"
	authors="ddove"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="ddove; sidneyh"/>

# Creación y administración de trabajos elásticos de Base de datos SQL (vista previa)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-jobs-create-and-manage.md)
- [PowerShell](sql-database-elastic-jobs-powershell.md)


Los **trabajos de base de datos elástica** simplifican la administración de grupos de bases de datos al ejecutar operaciones administrativas, como cambios de esquemas, administración de credenciales, actualizaciones de datos de referencias, recopilación de datos de rendimiento o recopilación de telemetría del inquilino (cliente). Trabajos de base de datos elástica está actualmente disponible a través del portal de Azure y los cmdlets de PowerShell. Sin embargo, la funcionalidad reducida del Portal de Azure se limita la ejecución transversal en todas las bases de datos de un [grupo de bases de datos elásticas (vista previa)](sql-database-elastic-pool.md). Para tener acceso a otras características y a la ejecución transversal de scripts en un grupo de bases de datos, que puede incluir una colección de bases de datos personalizada definida por el usuario o un conjunto de particiones (creado con la [biblioteca cliente de base de datos elástica](sql-database-elastic-scale-introduction.md)), vea [Creación y administración de trabajos mediante PowerShell](sql-database-elastic-jobs-powershell.md). Para obtener más información, vea [Información general sobre Trabajos de base de datos elástica](sql-database-elastic-jobs-overview.md).

## Requisitos previos

* Una suscripción de Azure. Para obtener una prueba gratuita, vea [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
* Grupo de bases de datos elásticas. Vea [Acerca de los grupos de bases de datos elásticas](sql-database-elastic-pool.md).
* Instalación de componentes del servicio de trabajo de bases de datos elásticas. Vea [Instalación del servicio de trabajo de bases de datos elásticas](sql-database-elastic-jobs-service-installation.md).

## Creación de trabajos

1. Mediante el [Portal de Azure](https://portal.azure.com), en un grupo de trabajos de base de datos elástica existente, haga clic en**Crear trabajo**.
2. Escriba el nombre de usuario y la contraseña del administrador de base de datos (creados al instalar los trabajos) para la base de datos de control de trabajos (almacenamiento de metadatos de los trabajos).

	![Asigne un nombre al trabajo, escríbalo o péguelo en el código y haga clic en Ejecutar.][1]
2. En la hoja **Crear trabajo**, escriba un nombre para el trabajo.
3. Escriba el nombre de usuario y la contraseña para conectarse a las bases de datos de destino con los permisos necesarios para que la ejecución de script sea correcta.
4. Péguelo o escríbalo en la secuencia de comandos T-SQL.
5. Haga clic en **Guardar** y, a continuación, haga clic en **Ejecutar**.

	![Creación y ejecución de trabajos][5]

## Ejecución de trabajos idempotentes

Al ejecutar una secuencia de comandos en un conjunto de bases de datos, debe asegurarse de que la secuencia de comandos sea idempotente. Es decir, la secuencia de comandos debe poder ejecutarse varias veces, incluso si se produce un error antes en un estado incompleto. Por ejemplo, cuando se produce un error en una secuencia de comandos, el trabajo se reintentará de forma automática hasta que se procese correctamente (dentro de los límites, ya que la lógica de reintentos finalmente dejará de reintentar la operación). La manera de hacerlo es usar la una cláusula "IF EXISTS" y eliminar cualquier instancia encontrada antes de crear un nuevo objeto. A continuación se muestra un ejemplo:

	IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
	GO
	CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

De manera alternativa, puede usar una cláusula "IF NOT EXISTS" antes de crear una nueva instancia:

	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
	 CREATE TABLE TestTable(
	  TestTableId INT PRIMARY KEY IDENTITY,
	  InsertionTime DATETIME2
	 );
	END
	GO

	INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
	GO

Esta secuencia de comandos, a continuación, actualizará la tabla creada anteriormente.

	IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
	BEGIN

	ALTER TABLE TestTable

	ADD AdditionalInformation NVARCHAR(400);
	END
	GO

	INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
	GO


## Comprobación del estado del trabajo

Tras iniciar un trabajo, puede comprobar su progreso.

1. Desde la página del grupo bases de datos elásticas, haga clic en **Administrar trabajos**.

	![Haga clic en "Administrar trabajos".][2]

2. Haga clic en el nombre (a) de un trabajo. El **ESTADO** puede ser "Completado" o "Error". Los detalles del trabajo aparecerán (b) con su fecha y hora de creación y ejecución. La lista (c) que se muestra debajo indica el progreso de la secuencia de comandos en cada base de datos del grupo y proporciona información de fecha y hora.

	![Comprobación de un trabajo finalizado][3]


## Comprobación de trabajos con errores

Si se produce un error en un trabajo, puede encontrar un registro de su ejecución. Haga clic en el nombre del trabajo con error para ver sus detalles.

![Comprobación de un trabajo con error][4]


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png

 

<!---HONumber=AcomDC_0218_2016-->