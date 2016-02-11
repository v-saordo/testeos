<properties 
	pageTitle="Implementación de una aplicación de API con una puerta de enlace existente" 
	description="Use una plantilla del Administrador de recursos de Azure para implementar una aplicación de API que usa una puerta de enlace y un plan del Servicio de aplicaciones existentes." 
	services="app-service" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tomfitz"/>

# Aprovisionamiento de una aplicación de API con una puerta de enlace existente

En este tema, aprenderá a crear una plantilla del Administrador de recursos de Azure que implementa una aplicación de API de Azure y una puerta de enlace existente. Aprenderá a definir los recursos que se implementan y los parámetros que se especifican cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarla para satisfacer sus necesidades.

Para obtener más información sobre la creación de plantillas, consulte [Creación de plantillas de Administrador de recursos de Azure](../resource-group-authoring-templates.md).

Para obtener más información acerca de la implementación de aplicaciones, consulte [Implementación de una aplicación compleja de forma predecible en Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).

Para la plantilla completa, consulte [Aplicación de API con una plantilla de puerta de enlace existente](https://github.com/Azure/azure-quickstart-templates/blob/master/201-api-app-gateway-existing/azuredeploy.json).

## Lo que implementará

En esta plantilla, implementará una aplicación de API que se asocia a un plan de hospedaje de Servicio de aplicaciones y a una puerta de enlace existentes.

Para ejecutar automáticamente la implementación, haga clic en el botón siguiente:

[![Implementación en Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-app-gateway-existing%2Fazuredeploy.json)

## Parámetros

[AZURE.INCLUDE [app-service-api-deploy-parameters](../../includes/app-service-api-deploy-parameters.md)]

### hostingPlanId

El identificador del plan de hospedaje de Servicio de aplicaciones existente.

    "hostingPlanId": {
      "type": "string"
    }

### hostingPlanSettings

Configuración del plan de hospedaje existente.

    "hostingPlanSettings": {
      "type": "Object",
      "defaultValue": {
        "hostingEnvironment": ""
      }
    }

## Variables

Esta plantilla define una variable que se usa para implementar los recursos.

    "variables": {
      "packageId": "Microsoft.ApiApp"
    }
    
El valor se usa a continuación como **variables('packageId')**. Contiene el identificador del paquete NuGet para las aplicaciones de API.

## Recursos para implementar

### Aplicación web para hospedar una aplicación de API

Crea una aplicación web que hospeda la aplicación de API.

Observe que **kind** se establece en **apiApp**, lo que notifica al Portal de Azure que esta aplicación web hospeda una aplicación de API. El portal ocultará la aplicación web en la hoja Aplicaciones web del explorador. La aplicación incluye una extensión para instalar el paquete de la aplicación de API vacío predeterminado. Se establece un vínculo entre la aplicación de API y la aplicación web de hospedaje. La sección de configuración de la aplicación incluye los valores necesarios para hospedar la aplicación de API. La propiedad **serverFarmId** se define en el valor que proporcionó en el parámetro **hostingPlanId**.

    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2015-04-01",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "kind": "apiApp",
      "resources": [
        {
          "type": "siteextensions",
          "apiVersion": "2015-02-01",
          "name": "[variables('packageId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "type": "WebRoot",
            "feed_url": "http://apiapps-preview.nuget.org/api/v2/",
            "version": "0.9.4"
          }
        },
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/apiApp",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
          }
        }
      ],
      "properties": {
        "name": "[parameters('apiAppName')]",
        "gatewaySiteName": "[parameters('gatewayName')]",
        "serverFarmId": "[parameters('hostingPlanId')]",
        "hostingEnvironment": "[parameters('hostingPlanSettings').hostingEnvironment]",
        "siteConfig": {
          "appSettings": [
            {
              "name": "EMA_MicroserviceId",
              "value": "[parameters('apiAppName')]"
            },
            {
              "name": "EMA_Secret",
              "value": "[parameters('apiAppSecret')]"
            },
            {
              "name": "EMA_RuntimeUrl",
              "value": "[concat('https://', parameters('gatewayName'), '.azurewebsites.net')]"
            },
            {
              "name": "WEBSITE_START_SCM_ON_SITE_CREATION",
              "value": "1"
            }
          ]
        }
      }
    }

### Aplicación de API

Cree la aplicación de API.

Observe que los nombres de la aplicación web de hospedaje y la puerta de enlace se definen como propiedades en la aplicación de API.

    {
      "type": "Microsoft.AppService/apiapps",
      "apiVersion": "2015-03-01-preview",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/apiAppSite",
          "dependsOn": [
            "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          }
        }
      ],
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites/siteextensions', parameters('apiAppName'), variables('packageId'))]"
      ],
      "properties": {
        "package": {
          "id": "[variables('packageId')]"
        },
        "host": {
          "resourceName": "[parameters('apiAppName')]"
        },
        "gateway": {
          "resourceName": "[parameters('gatewayName')]"
        }
      }
    }


## Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-api-app-gateway-existing/azuredeploy.json

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-api-app-gateway-existing/azuredeploy.json


 

<!---HONumber=AcomDC_0114_2016-->