<properties 
   pageTitle="Administración de Análisis de Azure Data Lake mediante Azure SDK para Node.js | Azure" 
   description="Aprenda a administrar cuentas de Análisis con Data Lake, orígenes de datos, usuarios y trabajos mediante Azure SDK para Node.js" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/11/2015"
   ms.author="jgao"/>

# Administración de Análisis de Azure Data Lake mediante Azure SDK para Node.js


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure ADK para Node.js se puede usar para administrar cuentas de Análisis con Azure Data Lake, trabajos y catálogos. Para ver los temas de administración con otras herramientas, haga clic en el selector de pestañas de arriba.

**Requisitos previos**

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Una cuenta de Análisis de Azure Data Lake**. Vea [Introducción a Análisis de Azure Data Lake mediante el portal de Azure](data-lake-analytics-get-started-portal.md) para crear una cuenta.
- **Una entidad de servicio con permisos de acceso a la cuenta de Análisis con Data Lake**. Vea [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](resource-group-authenticate-service-principal.md).

## Instalación del SDK

Lleve a cabo los pasos siguientes para instalar el SDK:

1. Instale [Node.js](https://nodejs.org/).
2. Ejecute los comandos siguientes en el símbolo del sistema, ventana de Terminal o Bash:

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-analytics
	
## Ejemplo de Node.js

En el ejemplo siguiente se obtiene la lista de trabajos de una cuenta de Análisis con Azure Data Lake dada.

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeAnalytics = require('azure-arm-datalake-analytics');
	
	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'
	
	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';
	
	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adla_resourcegroup_name';
	
	var accountName = 'adla_account_name';
	
	var context = new adalNode.AuthenticationContext(loginUri+tenantId);
	
	var client;
	var response;
	
	async.series([
		function (next) {
			context.acquireTokenWithClientCredentials(resourceUri, clientId, clientSecret, function(err, result){
				if (err) throw err;
				response = result;
				next();
			});
		},
		function (next) {
			var credentials = new azureCommon.TokenCloudCredentials({
				subscriptionId : subscriptionId,
				authorizationScheme : response.tokenType,
				token : response.accessToken
			});
		
			client = azureDataLakeAnalytics.createDataLakeAnalyticsJobManagementClient(credentials, 'azuredatalakeanalytics.net');
	
			next();
		},
		function (next) {
			client.jobs.list(resourceGroup, accountName, function(err, result){
				if (err) throw err;
				console.log(result);
			});
		}
	]);


##Consulte también 

- [SDK de Azure para Node.js](http://azure.github.io/azure-sdk-for-node/)
- [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Introducción a Análisis de Data Lake mediante el Portal de Azure](data-lake-analytics-get-started-portal.md)
- [Administración de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-use-portal.md)
- [Supervisión y solución de problemas de trabajos de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0114_2016-->