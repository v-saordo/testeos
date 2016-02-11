<properties 
   pageTitle="Administración del Almacén de Azure Data Lake mediante Azure SDK para Node.js | Azure" 
   description="Obtenga información sobre cómo administrar cuentas del Almacén de Data Lake y el sistema de archivos." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/11/2015"
   ms.author="jgao"/>

# Administración del Almacén de Azure Data Lake mediante Azure SDK para Node.js

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)
- [Using Node.js](data-lake-store-manage-use-nodejs.md)


Azure ADK for Node.js se puede usar para administrar cuentas de almacenamiento de Azure Data Lake y el sistema de archivos:

- Administración de cuentas: crear, obtener, enumerar, actualizar y eliminar.
- Administración del sistema de archivos: crear, obtener, cargar, anexar, descargar, leer, eliminar y enumerar.

**Requisitos previos**

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Una cuenta de Almacén de Azure Data Lake**. Vea [Introducción al Almacén de Azure Data Lake mediante el Portal de Azure](data-lake-store-get-started-portal.md) para crear una cuenta.
- **Una entidad de servicio con permisos de acceso a la cuenta de Análisis con Data Lake**. Vea [Autenticación de una entidad de servicio con el Administrador de recursos de Azure](resource-group-authenticate-service-principal.md).

## Instalación del SDK

Lleve a cabo los pasos siguientes para instalar el SDK:

1. Instale [Node.js](https://nodejs.org/).
2. Ejecute los comandos siguientes en el símbolo del sistema, ventana de Terminal o Bash:

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-store
	
## Ejemplo de Node.js

En el ejemplo siguiente se crea un archivo en una cuenta del Almacén Data Lake y anexa datos a ella.

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeStore = require('azure-arm-datalake-store');
	
	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'
	
	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';
	
	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adls_resourcegroup_name';
	
	var accountName = 'adls_account_name';
	
	var context = new adalNode.AuthenticationContext(loginUri+tenantId);
	
	var client;
	var response;
	
	var destinationFilePath = '/newFileName.txt';
	var content = 'desired file contents';
	
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
		
			client = azureDataLakeStore.createDataLakeStoreFileSystemManagementClient(credentials, 'azuredatalakestore.net');
	
			next();
		},
		function (next) {
			client.fileSystem.directCreate(destinationFilePath, accountName, content, function(err, result){
				if (err) throw err;
			});
		}
	]);


##Consulte también 

- [SDK de Azure para Node.js](http://azure.github.io/azure-sdk-for-node/)
- [Administración de Análisis de Azure Data Lake mediante Node.js](data-lake-analytics-use-nodejs.md)

<!---HONumber=AcomDC_1217_2015-->