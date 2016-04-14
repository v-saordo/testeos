<properties 
	pageTitle="Aprovisionamiento de aplicación web con Caché de Redis" 
	description="Use una plantilla de Administrador de recursos de Azure para implementar una aplicación web con Caché en Redis." 
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
	ms.date="12/16/2015" 
	ms.author="tomfitz"/>

# Creación de una aplicación web y Caché en Redis mediante una plantilla

En este tema, aprenderá a crear una plantilla de Administrador de recursos de Azure que implementa una aplicación web de Azure con Caché en Redis. Aprenderá a definir los recursos que se implementan y los parámetros que se especifican cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarla para satisfacer sus necesidades.

Para obtener más información sobre la creación de plantillas, consulte [Creación de plantillas de Administrador de recursos de Azure](../resource-group-authoring-templates.md).

Para ver la plantilla completa, consulte [Plantilla Aplicación web con Caché en Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json).

## Lo que implementará

En esta plantilla, implementará lo siguiente:

- Aplicación web de Azure
- Caché en Redis de Azure.

Para ejecutar automáticamente la implementación, haga clic en el botón siguiente:

[![Implementación en Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## Parámetros para especificar

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[AZURE.INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]



## Recursos para implementar

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### Caché en Redis

Crea Caché en Redis de Azure que se usa con la aplicación web. El nombre de la memoria caché se especifica en el parámetro **redisCacheName**.

La plantilla crea la memoria caché en la misma ubicación que la aplicación web, lo que se recomienda para un mejor rendimiento.

    {
      "apiVersion": "2014-04-01-preview",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('siteLocation')]",
      "properties": {
        "sku": {
          "name": "[parameters('redisCacheSKU')]",
          "family": "[parameters('redisCacheFamily')]",
          "capacity": "[parameters('redisCacheCapacity')]"
        },
        "redisVersion": "[parameters('redisCacheVersion')]",
        "enableNonSslPort": true
      }
    }

### Aplicación web

Crea la aplicación web con el nombre especificado en el parámetro **siteName**.

Observe que la aplicación web está configurada con las propiedades de configuración de la aplicación que permiten trabajar con Caché en Redis. Esta configuración de la aplicación se crea dinámicamente de acuerdo con los valores proporcionados durante la implementación.
        
    {
      "apiVersion": "2015-04-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[parameters('siteLocation')]",
      "dependsOn": [
          "[resourceId('Microsoft.Web/serverFarms', parameters('hostingPlanName'))]",
          "[resourceId('Microsoft.Cache/Redis', parameters('redisCacheName'))]"
      ],
      "properties": {
          "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
          {
              "apiVersion": "2015-06-01",
              "type": "config",
              "name": "web",
              "dependsOn": [
                  "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
              ],
              "properties": {
                  "appSettings": [
                      {
                          "name": "REDIS_HOST",
                          "value": "[concat(parameters('siteName'), '.redis.cache.windows.net:6379')]"
                      },
                      {
                          "name": "REDIS_KEY",
                          "value": "[listKeys(resourceId('Microsoft.Cache/Redis', parameters('redisCacheName')), '2014-04-01').primaryKey]"
                      }
                  ]
              }
          }
      ]
    }



## Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup

<!---HONumber=AcomDC_1217_2015-->