<properties
	pageTitle="Las aplicaciones de API y CLI de Azure | Microsoft Azure"
	description="Cómo usar la interfaz de la línea de comandos (CLI) de Microsoft Azure para Mac, Linux y Windows con las aplicaciones de API de Azure."
	editor="jimbe"
	manager="wpickett"
	documentationCenter=""
	authors="tdykstra"
	services="app-service\api"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Interfaz de la línea de comandos de Azure (CLI) y aplicaciones de API

En este artículo se muestra cómo crear, administrar y eliminar aplicaciones de API en el servicio de aplicaciones de Azure, mediante la interfaz de línea de comandos de Azure (CLI) para Mac, Linux y Windows.

## Requisitos previos

En este artículo se supone que ha instalado la CLI de Azure y que sabe cómo realizar tareas básicas. Para ver una introducción a la API, consulte [Instalación y configuración de la interfaz de la CLI de Azure](../xplat-cli-install.md).

> [AZURE.NOTE]Las instrucciones para [conectarse a una suscripción de Azure](../xplat-cli-connect.md)ofrecen dos alternativas: iniciar sesión con una cuenta de trabajo o educativas y descargar un archivo *.publishsettings*. Para aplicaciones de API, no funcionará el método de autenticación de archivos *.publishsettings*. Esto se debe a que tiene que usar el modo de Administración de recursos (presentado en la siguiente sección) para trabajar con aplicaciones de API y el método de autenticación de archivos *.publishsettings* no funciona con el Administrador de recursos.

## Habilitar el modo de Administración de recursos

La CLI se puede usar en el modo [Administración de servicios (asm)](../virtual-machines/virtual-machines-command-line-tools.md) o el modo o[Administración de recursos (arm)](../xplat-cli-azure-resource-manager.md). Para las aplicaciones de API debe usar el modo de Administración de recursos. Dado que el modo `arm` no está habilitado de forma predeterminada, use el comando `config mode arm` para habilitarlo.

	azure config mode arm

## Enumerar los comandos disponibles para trabajar con las aplicaciones de API

Para ver todos los comandos disponibles actualmente para trabajar con las aplicaciones de API, ejecute el comando `apiapp`.

	azure apiapp

## Enumerar todas las aplicaciones de API en una suscripción o grupo de recursos

Para enumerar todas las aplicaciones de API que tiene en su suscripción, ejecute el comando `apiapp list` sin parámetros.

	azure apiapp list

La lista muestra el grupo de recursos, el nombre de aplicación de API, el id. de paquete y la URL de cada aplicación de API.

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id        Url                                                                       
	data:    --------------  -------------  ----------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  Microsoft.ApiApp  https://microsoft-apiappf1bbba377c6d4aa1f03146cadd6.azurewebsites.net
	info:    apiapp list command OK

Para limitar la lista a un grupo de recursos especificado, agregue el parámetro (`-g`) de grupo.

	azure apiapp list -g <resource group name>

Por ejemplo:

	azure apiapp list -g mygroup

Para agregar información de nivel de acceso y versión de aplicación de API a la lista, agregue el parámetro (`-d`) de detalles.

	azure apiapp list -d

La salida `list` con los campos adicionales tiene un aspecto similar al de este ejemplo:

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id     Version  Auth                 Url                                                                       
	data:    --------------  -------------  -------------  -------  -------------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  SimpleDropbox  1.0.0    PublicAuthenticated  https://microsoft-apiappf1bbba377cbd2a3aa1f03146c6.azurewebsites.net
	info:    apiapp list command OK

### Listar detalles sobre una aplicación de API

Para mostrar los detalles para una aplicación de API, use el comando `apiapp show` con los parámetros (`-g`) de grupo y (`-n`) de nombre de aplicación de API.

	azure apiapp show -g <resource group name> -n <API app name>

Por ejemplo:

	azure apiapp show -g mygroup -n SimpleDropbox

El resultado tendrá un aspecto similar al siguiente:

	info:    Executing command apiapp show
	info:    Getting ApiApp
	data:    Name:              SimpleDropbox
	data:    Location:          West US
	data:    Resource Group:    mygroup
	data:    Package Id:        SimpleDropbox
	data:    Package Version:   1.0.0
	data:    Update Policy:     Disabled
	data:    Access Level:      PublicAuthenticated
	data:    Hosting site name: microsoft-apiappf1bbba377c6d4bd2a36cadd6
	data:    Gateway name:      SD1aeb4ae60b7cb4f3d966dfa43b6607f30
	info:    apiapp show command OK

## Creación de una clave de API

Hay dos maneras de crear una aplicación de API: Puede usar los comandos de CLI imperativos para crear recursos de Azure individualmente o puede usar la sintaxis declarativa en una plantilla para definir todos los recursos necesarios juntos e implementar esa plantilla con un comando de CLI. Para el enfoque declarativo, consulte [Pasos siguientes](#next-steps).

Para crear una aplicación de API con el enfoque imperativo, lleve a cabo los siguientes pasos:

1. Elegir una ubicación válida
1. Crear o encontrar un grupo de recursos para usar
2. Crear o encontrar un plan de Servicio de aplicaciones para usar
4. Creación de la aplicación de API

### Elegir una ubicación válida

Cuando se crea un grupo de recursos, necesita especificar una ubicación. Estas son algunas de las ubicaciones que son válidas para las aplicaciones de API.

* Este de EE. UU.
* Oeste de EE. UU.
* Centro-Sur de EE. UU
* Europa del Norte
* Asia oriental
* Este de Japón
* Europa occidental
* Sudeste asiático
* Oeste de Japón
* Centro-Norte de EE. UU
* Central EE. UU.:
* Sur de Brasil
* Este de EE. UU. 2

Para obtener una lista completa y actualizada de ubicaciones, use el comando `location list` y vea la línea del proveedor de recursos `Microsoft.AppService/apiapps`.

	azure location list

Este es una salida de ejemplo desde el comando `location list`.

	info:    Executing command location list
	info:    Getting Resource Providers
	data:    Name                                                                Location                                                                                                                                                   
	data:    ------------------------------------------------------------------  -----------------------------------------------------------------------------------------------------------------------------------------------------------
	data:    Microsoft.ApiManagement/service                                     East US,North Central US,South Central US,West US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West,Brazil South                     
	data:    Microsoft.AppService/apiapps                                        East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	data:    Microsoft.AppService/appIdentities                                  East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	info:    location list command OK

### Crear o encontrar un grupo de recursos para usar

Para crear un grupo de recursos use el comando `group create` con los parámetros (`n`) de nombre y (`l`) de ubicación.

	azure group create -n <name> -l <location>

Por ejemplo:

	azure group create -n "mygroup" -l "West US"

Para encontrar un grupo de recursos existente, use el comando `group list` y elija un grupo de recursos en una ubicación válida para las aplicaciones de API.

	azure group list

### Crear o encontrar un plan de Servicio de aplicaciones para usar

Para crear un plan de Servicio de aplicaciones, use el comando `resource create` y el parámetro de tipo de recurso (`-r`) para especificar que el tipo de recurso que desea crear es un plan de Servicio de aplicaciones.

	azure resource create -g <resource group> -n <app service plan name> -r "Microsoft.Web/serverfarms" -l <location> -o <api version> -p "{"sku": {"tier": "<pricing tier>"}, "numberOfWorkers" : <number of workers>, "workerSize": "<worker size>"}"
	
Por ejemplo:

	azure resource create -g mygroup -n myplan -r "Microsoft.Web/serverfarms" -l "West US" -o "2015-06-01" -p "{"sku": {"tier": "Standard"}, "numberOfWorkers" : 1, "workerSize": "Small"}"

Se requiere la cadena JSON para el parámetro `properties` (`-p`) debido a algunos cambios recientes en la API de REST; en el futuro, el parámetro `-p` será opcional.

El comando de ejemplo especifica la versión de la API más reciente a partir de la fecha en que se escribió este artículo. Para comprobar si hay disponible una versión posterior, use el comando `provider show` para ver la matriz `apiVersions` para el objeto `sites` de la matriz `resourceTypes`.

	azure provider show -n Microsoft.Web --json
   
Este es un ejemplo del objeto `sites` en la salida del comando.

	{
	  "resourceTypes": [
	    {
	      "apiVersions": [
	        "2015-06-01",
	        "2015-05-01",
	        "2015-04-01",
	        "2014-04-01"
	      ],
	      "locations": [
	        "East Asia",
	        "East US",
	        "Japan East",
	        "Japan West",
	        "North Europe",
	        "West Europe",
	        "West US",
	        "Southeast Asia",
	        "Central US",
	        "East US 2"
	      ],
	      "properties": {},
	      "name": "sites"
	    }

Para enumerar los planes del Servicio de aplicaciones existentes, use el comando `resource list` y especifique el plan de Servicio de aplicaciones como el tipo de recurso mediante el parámetro `-r`.

	azure resource list -r Microsoft.Web/serverfarms

El resultado tendrá un aspecto similar al siguiente:

	info:    Executing command resource list
	info:    Listing resources
	data:    Id                                                                                                                                           Name             Resource Group          Type                       Parent  Location  Tags
	data:    -------------------------------------------------------------------------------------------------------------------------------------------  ---------------  ----------------------  -------------------------  ------  --------  ----
	data:    /subscriptions/aeb4ae60-b7cb-4f3d-966d-fa43b6607f30/resourceGroups/ContosoAdsGroup/providers/Microsoft.Web/serverFarms/ContosoAdsPlan        ContosoAdsPlan   ContosoAdsGroup         Microsoft.Web/serverFarms          westus    null
	info:    resource list command OK

### Crear una aplicación de API vacía (personalizada)

Para crear una aplicación de API vacía (una en la que escribirá código para usted mismo), use el comando`apiapp create` y especifique el paquete Nuget `Microsoft.ApiApp` (parámetro `-u`).

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u Microsoft.ApiApp

Por ejemplo:

	azure apiapp create -g mygroup -n newapiapp -p myplan -u Microsoft.ApiApp

La salida informa del progreso conforme se crea la aplicación de API.

	info:    Executing command apiapp create
	info:    Checking resource group and app service plan
	info:    Getting package metadata
	info:    Creating deployment
	info:    Waiting for deployment completion
	info:    Deployment started:
	data:    Subscription Id: aeb4ae60-b7cb-4f3d-966d-fa43b6607f30
	data:    Resource Group:  mygroup
	data:    Deployment Name: AppServiceDeployment_f67fa710-d565-43cf-8c9c-
	                          d515dd2eb903
	data:    Correlation Id:  a7894287-c585-46d5-96a4-feac4b2f48c6
	data:    Timestamp:       2015-07-21T23:20:12.8858274Z
	data:    
	data:    Operation           State         Status        Resource                                          
	data:    ------------------- ------------- ------------- --------------------------------------------------
	data:    E8D88DA720375394    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a
	data:    FC31834D2E73D10E    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/providers/Microsoft.Resources/links/apiApp
	data:    C5B48DAF91306BB4    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/siteextensions/Microsoft.ApiApp
	data:    F4B943428026A1B2    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp
	data:    4B3A935892415369    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp/providers/Microsoft.Resources/links/apiAppSite
	info:    Deployment complete with status: Succeeded
	info:    apiapp create command OK

#### Creación de una aplicación de API desde Marketplace

Para obtener una lista de paquetes de aplicaciones de API disponibles en Marketplace, use el comando `group template list` y especifique las aplicaciones de API mediante el parámetro (`-c`) de categoría.

	azure group template list -c apiapps

El resultado tendrá un aspecto similar al de este ejemplo:

	info:    Executing command group template list
	info:    Listing gallery resource group templates
	data:    Publisher      Name                                                  
	data:    -------------  ------------------------------------------------------
	data:    Microsoft      Microsoft.Template.1.0.1                              
	data:    microsoft_com  microsoft_com.MicrosoftSharePointConnector.0.2.2      
	data:    microsoft_com  microsoft_com.FTPConnector.0.2.2                      
	info:    group template list command OK

Para crear una aplicación de API desde el Marketplace use el comando `apiapap create` y especifique el nombre de la aplicación de API que desea crear mediante el parámetro (`-u`) del paquete NuGet.

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u <marketplace name>

El valor que se usa para el parámetro `-u` es la sección central del nombre del marketplace. Por ejemplo, para microsoft\_com.MicrosoftSqlConnector.0.2.2, el aspecto tiene el siguiente aspecto:

	azure apiapp create -g mygroup -n mysqlconnector -p myplan -u MicrosoftSqlConnector
	
Si hay parámetros de plantilla de aplicación de API necesarios, como nombres de servidor y cadenas de conexión para un conector de SQL, se le solicitarán los datos requeridos. Tiene la opción de usar `--parameters` o `--parameters-file` para pasar los valores de parámetros. Para obtener más información sobre los archivos de parámetros, vea [Aprovisionar una aplicación de API con una nueva puerta de enlace](app-service-api-arm-new-gateway-provision.md).

## Pasos siguientes

Este artículo ha mostrado cómo usar comandos de Azure CLI individuales para trabajar con aplicaciones de API personalizadas o aplicaciones de API creadas a partir de plantillas de Marketplace. Para obtener información sobre cómo usar plantillas personalizadas para automatizar la creación de aplicaciones de API, vea estos recursos:

* [Aprovisionamiento de una aplicación de API con una puerta de enlace existente](app-service-api-arm-existing-gateway-provision.md)
* [Aprovisionamiento de una aplicación de API con una nueva puerta de enlace](app-service-api-arm-new-gateway-provision.md)

Para obtener más información sobre cómo usar las utilidades de la línea de comandos de Azure con el Administrador de recursos de Azure, vea estos recursos:
 
* [Uso de la interfaz de la línea de comandos entre plataformas de Azure con el Administrador de recursos de Azure](../xplat-cli-azure-resource-manager.md)
* [Uso de Azure PowerShell con el Administrador de recursos de Azure](../powershell-azure-resource-manager.md)
 

<!---HONumber=AcomDC_0114_2016-->