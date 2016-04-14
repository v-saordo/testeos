<properties
	pageTitle="Introducción a consultas elásticas para particionamiento horizontal | Microsoft Azure"
	description="cómo utilizar consultas de bases de datos cruzadas"
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
	ms.date="01/22/2016"
	ms.author="SilviaDoomra" />

# Introducción a consultas elásticas para particionamiento horizontal

La consulta de base de datos elástica (vista previa) para Base de datos SQL de Azure le permite ejecutar consultas T-SQL que distribuyen varias bases de datos con un único punto de conexión. Para obtener más información sobre la característica de base de datos elástica, consulte la [página de introducción de la característica](sql-database-elastic-query-overview.md).

Este tema amplía el ejemplo que aparece en [Introducción a las herramientas de la base de datos elástica](sql-database-elastic-scale-get-started.md). Una vez completado, sabrá: cómo configurar y usar una Base de datos SQL de Azure para realizar consultas que distribuyan muchas bases de datos relacionadas.
## Requisitos previos

Descargue [Introducción al ejemplo de herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md).

## Creación de un administrador de mapas de particiones con la aplicación de ejemplo

Aquí se creará un administrador de mapas de particiones junto con varias particiones, seguido de la inserción de datos en las particiones. Si resulta que ya dispone de la configuración de particiones con almacenes de datos en ellas, puede omitir los pasos siguientes y pasar a la sección siguiente.

1. Cree y ejecute la aplicación de ejemplo **Introducción a las herramientas de base de datos elástica**. Siga los pasos hasta el paso 7 de la sección [Descarga y ejecución de la aplicación de ejemplo](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools). Al final del paso 7, verá la siguiente línea de comandos:

	![símbolo del sistema][1]

2.  En la ventana de comandos, escriba "1" y pulse **Entrar**. De esta forma, se creará el administrador de mapas de particiones y se agregarán dos particiones al servidor. A continuación, escriba "3" y pulse **Entrar**; repita la acción cuatro veces. De esta forma, se insertan las filas de datos de ejemplo en sus particiones.
3.  El [Portal de Azure](https://portal.azure.com) debe mostrar tres nuevas bases de datos en el servidor v12:

	![Confirmación de Visual Studio][2]

	En este momento, se admiten las consultas entre bases de datos a través de las bibliotecas de cliente de base de datos elástica. Por ejemplo, use la opción 4 en la ventana de comandos. Los resultados de una consulta de varias particiones son siempre una **UNIÓN DE TODOS** los resultados de todas las particiones.

	En la siguiente sección, crearemos un extremo de la base de datos de ejemplo que admite las consultas más completas de los datos entre las particiones.

## Creación una base de datos de consulta elástica

1. Abra el [Portal de Azure](https://portal.azure.com) e inicie sesión.
2. Cree una nueva Base de datos SQL de Azure en el mismo servidor que la configuración de la partición. Utilice el nombre "ElasticDBQuery" para la base de datos.

	![Portal de Azure y nivel de precios][3]

	Nota: Puede usar una base de datos existente. Si puede hacerlo, no debe ser una de las particiones en las que desee ejecutar las consultas. Esta base de datos se usará para crear los objetos de metadatos para una consulta de base de datos elástica.


## Creación de objetos de base de datos

### Clave maestra y credenciales de ámbito de base de datos

Se usan para conectarse al administrador de mapas de particiones y particiones:

1. Abra SQL Server Management Studio o SQL Server Data Tools en Visual Studio.
2. Conéctese a la base de datos ElasticDBQuery y ejecute los siguientes comandos de T-SQL:

		CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

		CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
		WITH IDENTITY = '<username>',
		SECRET = '<password>';

	El nombre de usuario y la contraseña deben coincidir con la información de inicio de sesión del paso 6 de [Descarga y ejecución de la aplicación de ejemplo](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools) en [Introducción a herramientas de base de datos elástica](sql-database-elastic-scale-get-started.md).

### Orígenes de datos externos

Para crear un origen de datos externo, ejecute el siguiente comando en la base de datos ElasticDBQuery:

	CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
	  CREDENTIAL = ElasticDBQueryCred,
 	  SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" es el nombre del mapa de particiones, si ha creado el administrador de mapa de particiones y el mapa de particiones con el ejemplo de herramientas de base de datos elástica. Sin embargo, si usó la configuración personalizada para este ejemplo, debe ser el nombre de mapa de particiones que eligió en la aplicación.

### Tablas externas

Cree una tabla externa que coincida con la tabla Clientes en las particiones ejecutando el siguiente comando en la base de datos ElasticDBQuery:

	CREATE EXTERNAL TABLE [dbo].[Customers]
	( [CustomerId] [int] NOT NULL,
	  [Name] [nvarchar](256) NOT NULL,
	  [RegionId] [int] NOT NULL)
	WITH
	( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
	) ;

## Ejecución de la consulta de T-SQL de la base de datos elástica de ejemplo

Una vez que haya definido el origen de datos externo y las tablas externas, puede usar el T-SQL completo en las tablas externas.

Ejecute esta consulta en la base de datos ElasticDBQuery:

	select count(CustomerId) from [dbo].[Customers]

Observará que la consulta agrega resultados de todas las particiones y proporciona el siguiente resultado:

![Detalles de salida][4]

## Importación de los resultados de la consulta de base de datos elástica a Excel

 Puede importar los resultados de una consulta a un archivo Excel.

1. Inicie Excel 2013.
2. 	Diríjase a la barra de herramientas **Datos**.
3. 	Haga clic en **Desde otros orígenes** y luego en **Desde SQL Server**.

	![Importación de Excel desde otros orígenes][5]
4. 	En el **Asistente de conexión de datos**, escriba el nombre del servidor y las credenciales de inicio de sesión. A continuación, haga clic en **Siguiente**.
5. 	En el cuadro de diálogo **Seleccione la base de datos que contiene la información que desea**, seleccione la base de datos **ElasticDBQuery**.
6. 	Seleccione la tabla **Clientes** de la vista de lista y haga clic en **Siguiente**. Haga clic en **Finalizar**.
7. 	En el formulario **Importar datos**, en **Seleccione cómo desea ver estos datos en el libro**, seleccione **Tabla** y haga clic en **Aceptar**.

Todas las filas de la tabla **Clientes**, almacenadas en distintas particiones, completan la hoja de Excel.

## Pasos siguientes
Ahora puede usar las funciones de visualización de datos decisivas de Excel. Puede usar la cadena de conexión con el nombre de servidor, el nombre de base de datos y las credenciales para conectar su BI y las herramientas de integración de datos a la base de datos de consulta elástica. Asegúrese de que SQL Server se admite como origen de datos para la herramienta. Puede consultar la base de datos de consulta elástica y las tablas externas como cualquier otra base de datos SQL Server y las tablas de SQL Server que quiera conectar con la herramienta.

### Coste
No hay ningún cargo adicional por usar la característica de consulta de base de datos elástica.

Para obtener información sobre los precios, consulte [Detalles de precios de Base de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->

<!---HONumber=AcomDC_0128_2016-->