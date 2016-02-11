<properties 
	pageTitle="Ejecución del script de U-SQL en Análisis de Azure Data Lake desde Factoría de datos de Azure" 
	description="Aprenda a procesar datos ejecutando scripts U-SQL en el servicio de proceso de Análisis de Azure Data Lake." 
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
	ms.date="01/19/2016" 
	ms.author="spelluru"/>

# Ejecución del script de U-SQL en Análisis de Azure Data Lake desde Factoría de datos de Azure 
Una canalización en una factoría de datos de Azure procesa los datos de los servicios de almacenamiento vinculados mediante el uso de servicios de proceso vinculados. Contiene una secuencia de actividades donde cada actividad realiza una operación de procesamiento específica. En este artículo se describe la **actividad U-SQL de Análisis de Data Lake** que ejecuta un script de **U-SQL** en un servicio vinculado de proceso de **Análisis de Azure Data Lake**.

> [AZURE.NOTE] 
Debe crear una cuenta de Análisis de Azure Data Lake antes de crear una canalización con una actividad U-SQL de Análisis de Data Lake. Para obtener más información sobre Análisis de Azure Data Lake, consulte [Introducción al Análisis de Azure Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
>  
> Revise el [tutorial Compilación de la primera canalización ](data-factory-build-your-first-pipeline.md) para ver los pasos detallados para crear una factoría de datos, servicios vinculados, conjuntos de datos y una canalización. Use los fragmentos de código JSON con el Editor de Factoría de datos, Visual Studio o Azure PowerShell para crear las entidades de Factoría de datos.

## Servicio vinculado con Análisis de Azure Data Lake
Cree un servicio vinculado de **Análisis de Azure Data Lake** para vincular un servicio de proceso de Análisis de Azure Data Lake a una factoría de datos de Azure antes de usar la actividad U-SQL de Análisis de Data Lake en una canalización.

En el ejemplo siguiente se proporciona la definición de JSON para un servicio vinculado de Análisis de Azure Data Lake.

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>", 
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


En la siguiente tabla se ofrecen descripciones de las propiedades que se usan en la definición de JSON.

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
Tipo | La propiedad type se debe establecer en: **AzureDataLakeAnalytics**. | Sí
accountName | Nombre de la cuenta de Análisis de Azure Data Lake | Sí
dataLakeAnalyticsUri | Identificador URI de Análisis de Azure Data Lake. | No 
authorization | El código de autorización se recupera automáticamente después de hacer clic en el botón **Autorizar** situado en el Editor de Factoría de datos y de completar el inicio de sesión de OAuth. | Sí 
subscriptionId | Identificador de suscripción de Azure. | No (si no se especifica, se usa la suscripción de la factoría de datos). 
resourceGroupName | Nombre del grupo de recursos de Azure. | No (si no se especifica, se usa el grupo de recursos de la factoría de datos).
sessionId | Identificador de sesión de la sesión de autorización de OAuth. Cada identificador de sesión es único y solo puede usarse una vez. Esto se genera automáticamente en el Editor de Factoría de datos. | Sí

El código de autorización que se generó al hacer clic en el botón **Autorizar** expira poco tiempo después. Consulte la tabla siguiente para conocer el momento en que expiran los distintos tipos de cuentas de usuario. Puede ver el siguiente mensaje de error cuando el **token de autenticación expira**: Error de operación de credencial: invalid\_grant - AADSTS70002: error al validar las credenciales. AADSTS70008: la concesión de acceso proporcionada expiró o se revocó. Id. de seguimiento: d18629e8-af88-43c5-88e3-d8419eb1fca1 Id. de correlación: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Marca de tiempo: 2015-12-15 21:09:31Z

 
| Tipo de usuario | Expira después de |
| :-------- | :----------- | 
| No es usuario de AAD (@hotmail.com, @live.com, etc.) | 12 horas |
| El usuario de AAD y el origen basado en OAuth están en un [inquilino](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) que no es el de la Factoría de datos del usuario. | 12 horas |
| El usuario de AAD y el origen basado en OAuth se encuentran en el mismo inquilino que la Factoría de datos. | 14 días |

Para evitar o resolver este error, tendrá que volver a dar la autorización con el botón **Autorizar** cuando el **token expire** y volver a implementar el servicio vinculado. También puede generar valores para las propiedades **sessionId** y **authorization** mediante programación, para lo que usará el código de la sección siguiente.

  
### Para generar los valores de sessionId y authorization mediante programación 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Para más información sobre las clases de Factoría de datos que se usan en el código, vea los temas [Clase AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Clase AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) y [Clase AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Es preciso que agregue una referencia a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para la clase WindowsFormsWebAuthenticationDialog.
 
 
## Actividad U-SQL de Análisis de Data Lake 

El siguiente fragmento JSON define una canalización con una actividad U-SQL de Análisis de Data Lake. La definición de actividad tiene una referencia al servicio vinculado de Análisis de Azure Data Lake que creó anteriormente.
  

	{
    	"name": "ComputeEventsByRegionPipeline",
    	"properties": {
        	"description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        	"activities": 
			[
            	{
            	    "type": "DataLakeAnalyticsU-SQL",
                	"typeProperties": {
                    	"scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
	                    "scriptLinkedService": "StorageLinkedService",
    	                "degreeOfParallelism": 3,
    	                "priority": 100,
    	                "parameters": {
    	                    "in": "/datalake/input/SearchLog.tsv",
    	                    "out": "/datalake/output/Result.tsv"
    	                }
    	            },
    	            "inputs": [
	    				{
	                        "name": "DataLakeTable"
	                    }
	                ],
	                "outputs": 
					[
	                    {
                    	    "name": "EventsByRegionTable"
                    	}
                	],
                	"policy": {
                    	"timeout": "06:00:00",
	                    "concurrency": 1,
    	                "executionPriorityOrder": "NewestFirst",
    	                "retry": 1
    	            },
    	            "scheduler": {
    	                "frequency": "Day",
    	                "interval": 1
    	            },
    	            "name": "EventsByRegion",
    	            "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
    	        }
    	    ],
    	    "start": "2015-08-08T00:00:00Z",
    	    "end": "2015-08-08T01:00:00Z",
    	    "isPaused": false
    	}
	}


En la tabla siguiente se describen los nombres y descripciones de las propiedades que son específicas de esta actividad.

Propiedad | Descripción | Obligatorio
:-------- | :----------- | :--------
type | La propiedad type debe establecerse en **DataLakeAnalyticsU-SQL**. | Sí
scriptPath | Ruta de acceso a la carpeta que contiene el script U-SQL. | No (si se utiliza el script)
scriptLinkedService | Servicio vinculado que se vincula al almacenamiento que contiene el script para la factoría de datos | No (si se utiliza el script)
script | Especifique el script en línea en lugar de especificar scriptPath y scriptLinkedService. Por ejemplo: "script": "Prueba CREAR BASE DE DATOS". | No (si usa scriptPath y scriptLinkedService)
degreeOfParallelism | Número máximo de nodos que se usará de forma simultánea para ejecutar el trabajo. | No
prioridad | Determina qué trabajos de todos los están en cola deben seleccionarse para ejecutarse primero. Cuanto menor sea el número, mayor será la prioridad. | No 
parameters | Parámetros del script SQL U | No 

Para ver la definición del script, vea [Definición del script SearchLogProcessing.txt](#script-definition).

### Conjuntos de datos de entrada y salida de ejemplo

#### Conjunto de datos de entrada
En este ejemplo, los datos de entrada residen en Almacén de Azure Data Lake (archivo SearchLog.tsv en la carpeta de datalake/input).

	{
    	"name": "DataLakeTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
    	    "linkedServiceName": "AzureDataLakeStoreLinkedService",
    	    "typeProperties": {
    	        "folderPath": "datalake/input/",
    	        "fileName": "SearchLog.tsv",
    	        "format": {
    	            "type": "TextFormat",
    	            "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
    	        }
    	    },
    	    "availability": {
    	        "frequency": "Day",
    	        "interval": 1
    	    }
    	}
	}	

#### Conjunto de datos de salida
En este ejemplo, los datos de salida generados por el script U-SQL se almacenan en Almacén de Azure Data Lake (carpeta datalake/output).

	{
	    "name": "EventsByRegionTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "linkedServiceName": "AzureDataLakeStoreLinkedService",
	        "typeProperties": {
	            "folderPath": "datalake/output/"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

#### Servicio vinculado de Almacén de Azure Data Lake de ejemplo
Aquí está la definición del servicio vinculado de Almacén de Azure Data Lake usado que usan los conjuntos de datos de entrada y salida anteriores.

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

Vea [Movimiento de datos a y desde el Almacén de Azure Data Lake](data-factory-azure-datalake-connector.md) para obtener descripciones de las propiedades JSON del servicio vinculado de Almacén de Azure Data Lake y de los fragmentos de código JSON del conjunto de datos.

### Definición del script

	@searchlog =
	    EXTRACT UserId          int,
	            Start           DateTime,
	            Region          string,
	            Query           string,
	            Duration        int?,
	            Urls            string,
	            ClickedUrls     string
	    FROM @in
	    USING Extractors.Tsv(nullEscape:"#NULL#");
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @searchlog
	WHERE Region == "en-gb";
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @rs1
	    WHERE Start <= DateTime.Parse("2012/02/19");
	
	OUTPUT @rs1   
	    TO @out
	      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

ADF pasa dinámicamente los valores de los parámetros **@in** y **@out** en el script de U-SQL anterior, para lo que usa la sección 'parameters'. Vea la sección 'parameters' anterior en la definición de la canalización.

Puede especificar otro degreeOfParallelism de viz. de propiedades, prioridad, etc., también en su definición de la canalización para los trabajos que se ejecutan en el servicio Análisis de Azure Data Lake.

<!---HONumber=AcomDC_0128_2016-->