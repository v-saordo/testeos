<properties 
	pageTitle="Aprovisionamiento de Caché en Redis" 
	description="Use una plantilla del Administrador de recursos de Azure para implementar Caché en Redis de Azure." 
	services="app-service" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="web" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/16/2015" 
	ms.author="tomfitz"/>

# Creación de una Caché en Redis mediante una plantilla

En este tema, aprenderá a crear una plantilla de Administrador de recursos de Azure que implementa Caché en Redis de Azure. La memoria caché se puede usar con una cuenta de almacenamiento existente para mantener los datos de diagnóstico. Aprenderá a definir los recursos que se implementan y los parámetros que se especifican cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarla para satisfacer sus necesidades.

Actualmente, se comparten los ajustes de configuración de diagnóstico para todas las cachés de la misma región para una suscripción. Actualizar una caché en la región afectará a todas las demás cachés de la región.

Para obtener más información sobre la creación de plantillas, consulte [Creación de plantillas de Administrador de recursos de Azure](../resource-group-authoring-templates.md).

Para ver la plantilla completa, consulte [Plantilla Caché en Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

>[AZURE.NOTE]Las plantillas ARM para el nuevo [nivel Premium](cache-premium-tier-intro.md) están disponibles.
>
>-    [Crear una caché en Redis Premium con agrupación en clústeres](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
>-    [Crear una caché en Redis Premium con persistencia de datos](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
>-    [Crear una caché en Redis Premium con una red virtual y agrupación en clústeres opcional](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
>
>Para buscar las últimas plantillas, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/) y busque `Redis Cache`.

## Lo que implementará

En esta plantilla, implementará una caché en Redis de Azure que utiliza una cuenta de almacenamiento de datos de diagnóstico.

Para ejecutar automáticamente la implementación, haga clic en el botón siguiente:

[![Implementación en Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## Parámetros

Con el Administrador de recursos de Azure, se definen los parámetros de los valores que desea especificar al implementar la plantilla. La plantilla incluye una sección denominada Parámetros que contiene todos los valores de los parámetros. Debe definir un parámetro para los valores que variarán según el proyecto que vaya a implementar o según el entorno en el que vaya a realizar la implementación. No defina parámetros para valores que vayan a permanecer igual. Cada valor de parámetro se usa en la plantilla para definir los recursos que se implementan.

Vamos a describir cada parámetro de la plantilla.

[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### redisCacheLocation

La ubicación de Caché en Redis. Para un mejor rendimiento, utilice la misma ubicación que la aplicación que se usará con la memoria caché.

    "redisCacheLocation": {
      "type": "string"
    }

### diagnosticsStorageAccountName

Nombre de la cuenta de almacenamiento existente que desea usar para el diagnóstico.

    "diagnosticsStorageAccountName": {
      "type": "string"
    }

### enableNonSslPort

Valor booleano que indica si se debe permitir el acceso a través de puertos no SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### diagnosticsStatus

Un valor que indica si están activados los diagnósticos. Utilice ON (activados) u OFF (desactivados).

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }
    
## Recursos para implementar

### Caché en Redis

Crea Caché en Redis de Azure.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "redisVersion": "[parameters('redisCacheVersion')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
        "resources": [
          {
            "apiVersion": "2014-04-01",
            "type": "diagnosticSettings",
            "name": "service", 
            "location": "[parameters('redisCacheLocation')]",
            "dependsOn": [
              "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
            ],
            "properties": {
              "status": "[parameters('diagnosticsStatus')]",
              "storageAccountName": "[parameters('diagnosticsStorageAccountName')]",
              "retention": "30"
            }
          }
        ]
    }

## Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup

<!---HONumber=AcomDC_1217_2015-->