<properties 
	pageTitle="Tutorial: Crear una canalización con la actividad de copia con Azure PowerShell" 
	description="En este tutorial, creará una canalización de la factoría de datos de Azure con una actividad de copia mediante Azure PowerShell." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2015" 
	ms.author="spelluru"/>

# Tutorial: Crear y supervisar una factoría de datos mediante PowerShell de Azure
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)


El tutorial [Introducción a la Factoría de datos de Azure][adf-get-started] le muestra cómo crear y supervisar una factoría de datos de Azure con el [Portal de Azure][azure-portal]. En este tutorial, creará y supervisará una factoría de datos de Azure con cmdlets de PowerShell de Azure. La canalización en la factoría de datos que cree en este tutorial copia los datos del blob de Azure a una base de datos SQL de Azure.

> [AZURE.IMPORTANT]Revise el artículo [Información general del tutorial](data-factory-get-started.md) y realice los pasos de requisitos previos antes de completar este tutorial.
>   
> Este artículo no abarca todos los cmdlets de Factoría de datos. Vea [Referencia de cmdlets de factoría de datos](https://msdn.microsoft.com/library/dn820234.aspx) para obtener la documentación completa sobre los cmdlets de la factoría de datos.
  

##Requisitos previos
Además de los requisitos previos que se enumeran en el tema de información general del tutorial, debe tener instalado lo siguiente:

- **Azure PowerShell**. Siga las instrucciones del artículo [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para instalar Azure PowerShell en su equipo.

Si usa Azure PowerShell de una **versión inferior a 1.0**, deberá usar los cmdlets que se documentan [aquí][old-cmdlet-reference]. También debe ejecutar los comandos siguientes antes de usar los cmdlets de Factoría de datos:

1. Inicie Azure PowerShell y ejecute los comandos siguientes. Mantenga Azure PowerShell abierto hasta el final de este tutorial. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.
	1. Ejecute **Add-AzureAccount** y escriba el mismo nombre de usuario y contraseña que utilizó para iniciar sesión en el Portal de Azure.
	2. Ejecute **Get-AzureSubscription** para ver todas las suscripciones para esta cuenta.
	3. Ejecute **Select-AzureSubscription** para seleccionar la suscripción con la que quiere trabajar. Esta suscripción debe ser la misma que la usada en el Portal de Azure.
4. Cambie al modo AzureResourceManager a medida que los cmdlets de Factoría de datos de Azure están disponibles en este modo: **Switch-AzureMode AzureResourceManager**.
  

##Apartados de este tutorial
En la tabla siguiente se enumera los pasos que se llevarán a cabo como parte de este tutorial y sus descripciones.

Paso | Descripción
-----| -----------
[Paso 1: Crear una factoría de datos de Azure](#CreateDataFactory) | En este paso creará una factoría de datos de Azure denominada **ADFTutorialDataFactoryPSH**. 
[Paso 2: Crear servicios vinculados](#CreateLinkedServices) | En este paso, creará dos servicios vinculados: **StorageLinkedService** y **AzureSqlLinkedService**. StorageLinkedService vincula el Almacenamiento de Azure y AzureSqlLinkedService vincula la Base de datos SQL de Azure a ADFTutorialDataFactoryPSH.
[Paso 3: Crear conjuntos de datos de entrada y salida](#CreateInputAndOutputDataSets) | En este paso, definirá dos conjuntos de datos (**EmpTableFromBlob** y **EmpSQLTable**) que se usan como tablas de entrada y salida de la **actividad de copia** en la ADFTutorialPipeline que creará en el siguiente paso.
[Paso 4: Crear y ejecutar una canalización](#CreateAndRunAPipeline) | En este paso, creará una canalización denominada **ADFTutorialPipeline** en Factoría de datos: **ADFTutorialDataFactoryPSH**. La canalización dispondrá de una **actividad de copia** que copia datos de un blob de Azure en una tabla de base de datos de Azure de salida.
[Paso 5: Supervisar conjuntos de datos y canalización](#MonitorDataSetsAndPipeline) | En este paso, supervisará los conjuntos de datos y la canalización con PowerShell de Azure.

## <a name="CreateDataFactory"></a>Paso 1: Crear una factoría de datos de Azure
En este paso, use PowerShell de Azure para crear una factoría de datos de Azure llamada **ADFTutorialDataFactoryPSH**.

1. Inicie Azure PowerShell y ejecute el comando siguiente. Mantenga Azure PowerShell abierto hasta el final de este tutorial. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.
	- Ejecute **Login-AzureRmAccount** y escriba el mismo nombre de usuario y contraseña que utilizó para iniciar sesión en el Portal de Azure.  
	- Ejecute **Get-AzureSubscription** para ver todas las suscripciones para esta cuenta.
	- Ejecute **Select-AzureSubscription<Name of the subscription>** para seleccionar la suscripción con la que quiere trabajar. Esta suscripción debe ser la misma que la usada en el Portal de Azure.
3. Cree un grupo de recursos de Azure con el nombre: **ADFTutorialResourceGroup** ejecutando el siguiente comando.
   
		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	En algunos de los pasos de este tutorial se supone que se usa el grupo de recursos denominado **ADFTutorialResourceGroup**. Si usa un grupo de recursos diferentes, deberá usarlo en lugar de ADFTutorialResourceGroup en este tutorial. 
4. Ejecute el cmdlet **New-AzureRmDataFactory** para crear una factoría de datos con el nombre: **ADFTutorialDataFactoryPSH**.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

	El nombre del generador de datos de Azure debe ser único global. Si recibe el error: **El nombre de la factoría de datos "ADFTutorialDataFactoryPSH" no está disponible**, cambie el nombre (por ejemplo, yournameADFTutorialDataFactoryPSH). Use este nombre en lugar de ADFTutorialFactoryPSH mientras lleva a cabo los pasos de este tutorial. Consulte el tema [Factoría de datos: reglas de nomenclatura](data-factory-naming-rules.md) para las reglas de nomenclatura para los artefactos de Factoría de datos.

	> [AZURE.NOTE]El nombre de la factoría de datos se puede registrar como un nombre DNS en el futuro y, por lo tanto, hacerse públicamente visible.

## <a name="CreateLinkedServices"></a>Paso 2: Crear servicios vinculados
Los servicios vinculados vinculan almacenes de datos o servicios de proceso con una factoría de datos de Azure. Un almacén de datos puede ser Almacenamiento de Azure, Base de datos SQL de Azure o Base de datos SQL Server local que contenga los datos de entrada o almacene los datos de salida de una canalización de Factoría de datos. Un servicio de proceso es el servicio que procesa datos de entrada y genera datos de salida.

En este paso, creará dos servicios vinculados: **StorageLinkedService** y **AzureSqlLinkedService**. El servicio vinculado StorageLinkedService vincula una cuenta de almacenamiento de Azure y AzureSqlLinkedService vincula una base de datos SQL de Azure a la factoría de datos: **ADFTutorialDataFactoryPSH**. Más adelante en este tutorial creará una canalización que copia datos de un contenedor de blobs de StorageLinkedService en una tabla SQL de AzureSqlLinkedService.

### Creación de un servicio vinculado para una cuenta de almacenamiento de Azure
1.	Cree un archivo JSON con el nombre **StorageLinkedService.json** en **C:\\ADFGetStartedPSH** con el siguiente contenido. Si todavía no existe, cree la carpeta ADFGetStartedPSH.

			{
		  		"name": "StorageLinkedService",
		  		"properties": {
	    			"type": "AzureStorage",
		    		"typeProperties": {
		      			"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    		}
		  		}
			}

	Reemplace **accountname** por el nombre de la cuenta de almacenamiento y **accountkey** por la clave de la cuenta de almacenamiento de Azure.
2.	En **PowerShell de Azure**, cambie a la carpeta **ADFGetStartedPSH**. 
3.	Use el cmdlet **New-AzureRmDataFactoryLinkedService** para crear un servicio vinculado. Este cmdlet y otros cmdlets de Factoría de datos que usa en este tutorial requieren que pase los valores para los parámetros **ResourceGroupName** y **DataFactoryName**. Como alternativa, puede usar **Get-AzureRmDataFactory** para obtener un objeto DataFactory y pasarlo sin necesidad de escribir ResourceGroupName y DataFactoryName cada vez que ejecuta un cmdlet. Ejecute el comando siguiente para asignar el resultado del cmdlet **Get-AzureRmDataFactory** a una variable: **$df**. 

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH

4.	Ahora, ejecute el cmdlet **New-AzureRmDataFactoryLinkedService** para crear el servicio vinculado: **StorageLinkedService**.

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Si no hubiera ejecutado el cmdlet **Get-AzureRmDataFactory** y asignado a la salida a la variable **$df**, tendría que especificar valores para los parámetros ResourceGroupName y DataFactoryName de la siguiente forma.
		
		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Si cierra Azure PowerShell en el centro del tutorial, tendrá que ejecutar el cmdlet Get-AzureRmDataFactory la próxima vez que ejecute Azure PowerShell para completar el tutorial.

### Creación de un servicio vinculado para una base de datos SQL de Azure
1.	Cree un archivo JSON con el nombre AzureSqlLinkedService.json con el siguiente contenido.

			{
				"name": "AzureSqlLinkedService",
				"properties": {
					"type": "AzureSqlDatabase",
					"typeProperties": {
				      	"connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
					}
		  		}
			}

	Reemplace **servername**, **databasename**, ****username@servername** y **password** por los nombres de servidor, base de datos, cuenta de usuario y contraseña de Azure SQL.

2.	Ejecute el siguiente comando para crear un servicio vinculado.
	
		New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	Confirme que la configuración **Permitir el acceso a los servicios de Azure** esté activada para el servidor SQL de Azure. Para comprobarla y activarla, realice la siguiente tarea:

	1. Haga clic en el concentrador **EXAMINAR** a la izquierda y haga clic en **Servidores SQL**.
	2. Seleccione un servidor y haga clic en **CONFIGURACIÓN** en la hoja SQL SERVER.
	3. En la hoja **CONFIGURACIÓN**, haga clic en **Firewall**.
	4. En la hoja **Configuración de firewall**, haga clic en **ACTIVADA** para **Permitir el acceso a los servicios de Azure**.
	5. Haga clic en el concentrador **ACTIVO** a la izquierda para cambiar a la hoja **Factoría de datos** en la que está.
	

## <a name="CreateInputAndOutputDataSets"></a>Paso 3: Crear tablas de entrada y salida

En el paso anterior, creó los servicios vinculados **StorageLinkedService** y **AzureSqlLinkedService** para vincular una cuenta de almacenamiento de Azure y una base de datos SQL de Azure con la factoría de datos: **ADFTutorialDataFactoryPSH**. En este paso, creará conjuntos de datos que representan los datos de entrada y salida de la actividad de copia en la canalización que va a crear en el paso siguiente.

Una tabla es un conjunto de datos rectangular y es el único tipo de conjunto de datos que se admite en este momento. La tabla de entrada de este tutorial hace referencia a un contenedor de blobs en el Almacenamiento de Azure al que apunta StorageLinkedService y la tabla de salida hace referencia a una tabla SQL en la Base de datos de SQL Azure a la que apunta AzureSqlLinkedService.

### Preparación del almacenamiento de blobs de Azure y la base de datos SQL de Azure para el tutorial
Omita este paso si ha pasado por el tutorial desde el artículo [Introducción a la Factoría de datos de Azure][adf-get-started].

Tiene que realizar los siguientes pasos para preparar el Almacenamiento de blobs de Azure y Base de datos SQL de Azure para este tutorial.
 
* Cree un contenedor de blobs con el nombre **adftutorial** en el almacenamiento de blobs de Azure al que señala **StorageLinkedService**. 
* Cree y cargue un archivo de texto, **emp.txt**, como un blob para el contenedor **adftutorial**. 
* Cree una tabla llamada **emp** en la Base de datos SQL de Azure a la que apunta **AzureSqlLinkedService**.


1. Inicie el Bloc de notas, pegue el texto siguiente y guárdelo como **emp.txt** en la carpeta **C:\\ADFGetStartedPSH** de su disco duro. 

        John, Doe
		Jane, Doe
				
2. Use herramientas como el [Explorador de almacenamiento de Azure](https://azurestorageexplorer.codeplex.com/) para crear el contenedor **adftutorial** y cargar el archivo **emp.txt** en el contenedor.

    ![Explorador de almacenamiento de Azure][image-data-factory-get-started-storage-explorer]
3. Use el siguiente script de SQL para crear la tabla **emp** en su Base de datos SQL de Azure.  


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	Si tiene SQL Server 2014 instalado en el equipo: siga las instrucciones del artículo [Paso 2: Conexión con la base de datos SQL de la base de datos de administración de SQL de Azure con SQL Server Management Studio][sql-management-studio] para conectarse al servidor SQL de Azure y ejecutar el script de SQL.

	Si tiene instalado Visual Studio 2013 en el equipo: en el Portal de Azure, ([http://portal.azure.com](http://portal.sazure.com)), haga clic en el concentrador **EXAMINAR** a la izquierda, en **Servidores SQL**, seleccione su base de datos y haga clic en el botón **Abrir en Visual Studio** de la barra de herramientas para conectarse a su servidor SQL de Azure y ejecutar el script. Si el cliente no tiene permiso para acceder al servidor SQL de Azure, tendrá que configurar el firewall de su servidor SQL de Azure para permitir el acceso desde su equipo (dirección IP). Consulte el artículo anterior para conocer los pasos para configurar el firewall para el servidor SQL de Azure.
		
### Creación de la tabla de entrada 
Una tabla es un conjunto de datos rectangular y tiene un esquema. En este paso, creará una tabla denominada **EmpBlobTable** que apunta a un contenedor de blobs en el almacenamiento de Azure representado por el servicio vinculado **StorageLinkedService**. Este contenedor de blobs (**adftutorial**) contiene los datos de entrada en el archivo: **emp.txt**.

1.	Cree un archivo JSON llamado **EmpBlobTable.json** en la carpeta **C:\\ADFGetStartedPSH** con el siguiente contenido:

			{
			  "name": "EmpTableFromBlob",
			  "properties": {
			    "structure": [
			      {
			        "name": "FirstName",
			        "type": "String"
			      },
			      {
			        "name": "LastName",
			        "type": "String"
			      }
			    ],
			    "type": "AzureBlob",
			    "linkedServiceName": "StorageLinkedService",
			    "typeProperties": {
				  "fileName": "emp.txt",
			      "folderPath": "adftutorial/",
			      "format": {
			        "type": "TextFormat",
			        "columnDelimiter": ","
			      }
			    },
			    "external": true,
			    "availability": {
			      "frequency": "Hour",
			      "interval": 1
			    }
			  }
			}
	
	Tenga en cuenta lo siguiente:
	
	- **type** de conjunto de datos está establecido en **AzureBlob**.
	- **linkedServiceName** se establece en **StorageLinkedService**. 
	- **folderPath** se establece en el contenedor **adftutorial**. 
	- **fileName** se establece en **emp.txt**. Si no especifica el nombre del blob, los datos de todos los blobs del contenedor se consideran datos de entrada.  
	- el **tipo** de formato se establece en **TextFormat**
	- Hay dos campos en el archivo de texto: **FirstName** y **LastName** separados por un carácter de coma (**columnDelimiter**)	
	- La **disponibilidad** se establece en **cada hora** (**frecuencia** se establece en **hora** e **intervalo** se establece en **1**), por lo que el servicio de la factoría de datos buscará los datos de entrada cada hora en la carpeta raíz del contenedor de blob (**adftutorial**) especificado.

	Si no especifica **fileName** para una **tabla** de **entrada**, todos los archivos o blobs de la carpeta de entrada (**folderPath**) se consideran entradas. Si especifica un nombre de archivo en JSON, solo el archivo o blob especificado se consideran una entrada. Consulte los archivos de muestra del [tutorial][adf-tutorial] para ver algunos ejemplos.
 
	Si no especifica un valor **fileName** para una **tabla de salida**, los archivos generados en la **ruta de la carpeta** se denominan con el siguiente formato: Data.<Guid>.txt (ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Para establecer **folderPath** y **fileName** de forma dinámica según la hora de **SliceStart**, use la propiedad **partitionedBy**. En el ejemplo siguiente, folderPath usa Year, Month y Day de SliceStart (hora de inicio del segmento que se va a procesar) y fileName usa Hour de SliceStart. Por ejemplo, si se está produciendo una división de 2014-10-20T08:00:00, el nombre de carpeta se establece en wikidatagateway/wikisampledataout/2014/10/20 y el nombre de archivo se establece en 08.csv.

			"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	        "fileName": "{Hour}.csv",
	        "partitionedBy": 
	        [
	        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	        ],

	Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

2.	Ejecute el comando siguiente para crear el conjunto de datos de Factoría de datos.

		New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json

### Creación de la tabla de salida
En esta parte del paso, creará una tabla de salida denominada **EmpSQLTable** que apunta a una tabla SQL (**emp**) de la base de datos SQL de Azure que está representada por el servicio vinculado **AzureSqlLinkedService**. La canalización copia los datos del blob de entrada a la tabla **emp**.

1.	Cree un archivo JSON llamado**EmpSQLTable.json** en la carpeta **C:\\ADFGetStartedPSH** con el siguiente contenido:
		
			{
			  "name": "EmpSQLTable",
			  "properties": {
			    "structure": [
			      {
			        "name": "FirstName",
			        "type": "String"
			      },
			      {
			        "name": "LastName",
			        "type": "String"
			      }
			    ],
			    "type": "AzureSqlTable",
			    "linkedServiceName": "AzureSqlLinkedService",
			    "typeProperties": {
			      "tableName": "emp"
			    },
			    "availability": {
			      "frequency": "Hour",
			      "interval": 1
			    }
			  }
			}

     Tenga en cuenta lo siguiente:
	
	* **type** de conjunto de datos está establecido en **AzureSqlTable**.
	* **linkedServiceName** se establece en **AzureSqlLinkedService**.
	* **tablename** está establecido en **emp**.
	* Hay tres columnas: **ID**, **FirstName** y **LastName**: en la tabla emp de la base de datos, pero el id. es una columna de identidad, por lo que deberá especificar solo **FirstName** y **LastName** aquí.
	* El elemento **availability** está establecido en **hourly** (**frequency** está establecido en **hour** e **interval**, en **1**). El servicio Factoría de datos generará un segmento de datos de salida cada hora en la tabla **emp** de la base de datos SQL de Azure.

2.	Ejecute el comando siguiente para crear el conjunto de datos de Factoría de datos.
	
		New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>Paso 4: Crear y ejecutar una canalización
En este paso, creará una canalización con una **actividad de copia** que usa **EmpTableFromBlob** como entrada y **EmpSQLTable** como salida.

1.	Cree un archivo JSON llamado **ADFTutorialPipeline.json** en la carpeta **C:\\ADFGetStartedPSH** con el siguiente contenido: 
	
			 {
			  "name": "ADFTutorialPipeline",
			  "properties": {
			    "description": "Copy data from a blob to Azure SQL table",
			    "activities": [
			      {
			        "name": "CopyFromBlobToSQL",
			        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
			        "type": "Copy",
			        "inputs": [
			          {
			            "name": "EmpTableFromBlob"
			          }
			        ],
			        "outputs": [
			          {
			            "name": "EmpSQLTable"
			          }
			        ],
			        "typeProperties": {
			          "source": {
			            "type": "BlobSource"
			          },
			          "sink": {
			            "type": "SqlSink"
			          }
			        },
			        "Policy": {
			          "concurrency": 1,
			          "executionPriorityOrder": "NewestFirst",
			          "style": "StartOfInterval",
			          "retry": 0,
			          "timeout": "01:00:00"
			        }
			      }
			    ],
			    "start": "2015-12-09T00:00:00Z",
			    "end": "2015-12-10T00:00:00Z",
			    "isPaused": false
			  }
			}

	Tenga en cuenta lo siguiente:

	- En la sección de actividades, solo hay una actividad con **type** establecido en **Copy**.
	- La entrada de la actividad está establecida en **EmpTableFromBlob** y la salida en **EmpSQLTable**.
	- En la sección **transformación**, **BlobSource** se especifica como el tipo de origen y **SqlSink** como el tipo de receptor.

	Reemplace el valor de la propiedad **start** por el día actual y el valor **end** por el próximo día. Las fechas y horas de inicio y de finalización deben estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-10-14T16:32:41Z. La hora de **end** es opcional, pero se utilizará en este tutorial.
	
	Si no especifica ningún valor para la propiedad **end**, se calcula como "**start + 48 horas**". Para ejecutar la canalización indefinidamente, especifique **9/9/9999** como valor de la propiedad **end**.
	
	En el ejemplo anterior, habrá 24 segmentos de datos, porque cada segmento de datos se produce cada hora.
	
	Para obtener más información sobre las propiedades JSON, vea [Referencia de scripting JSON](http://go.microsoft.com/fwlink/?LinkId=516971).
2.	Ejecute el comando siguiente para crear la tabla Factoría de datos. 
		
		New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**¡Enhorabuena!** Ha creado correctamente una factoría de datos de Azure, servicios vinculados, tablas y una canalización, y ha programado la canalización.

## <a name="MonitorDataSetsAndPipeline"></a>Paso 5: Supervisar la canalización y los conjuntos de datos
En este paso, usará PowerShell de Azure para supervisar lo que está ocurriendo en una factoría de datos de Azure.

1.	Ejecute **Get-AzureRmDataFactory** y asigne el resultado a la variable $df.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	Ejecute **Get-AzureRmDataFactorySlice** para obtener la información sobre todos los segmentos de **EmpSQLTable**, que es la tabla de salida de la canalización.

		Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	Reemplace la parte del año, mes y fecha del parámetro **StartDateTime** por el año, mes y fecha actuales. Debe coincidir con el valor de **inicio** en JSON de canalización.

	Debe ver 24 segmentos, uno para cada hora de 12 A.M. del día actual hasta las 12 A.M. del día siguiente.
	
	**Primer segmento:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     :
		LongRetryCount    : 0

	**Último segmento:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     : 
		LongRetryCount    : 0

3.	Ejecute **Get-AzureRmDataFactoryRun** para obtener la información de la actividad que se ejecuta para un segmento **específico**. Cambie el valor del parámetro **StartDateTime** para que coincida con la hora de **inicio** del segmento desde la salida anterior. El valor de **StartDateTime** debe estar en [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por ejemplo: 2014-03-03T22:00:00Z.

		Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

	Debería ver una salida similar a la siguiente:

		Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : ADFTutorialDataFactoryPSH
		TableName           : EmpSQLTable
		ProcessingStartTime : 3/3/2015 11:03:28 PM
		ProcessingEndTime   : 3/3/2015 11:04:36 PM
		PercentComplete     : 100
		DataSliceStart      : 3/8/2015 10:00:00 PM
		DataSliceEnd        : 3/8/2015 11:00:00 PM
		Status              : Succeeded
		Timestamp           : 3/8/2015 11:03:28 PM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : CopyFromBlobToSQL
		PipelineName        : ADFTutorialPipeline
		Type                : Copy

Vea [Referencia de cmdlets de factoría de datos][cmdlet-reference] para obtener la documentación completa sobre los cmdlets de la factoría de datos.



[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[adf-get-started]: data-factory-get-started.md
[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md
 

<!---HONumber=AcomDC_0107_2016-->