<properties 
	pageTitle="Implementación de una aplicación web vinculada a un repositorio de GitHub" 
	description="Use una plantilla de Administrador de recursos de Azure para implementar una aplicación web que contenga un proyecto de un repositorio de GitHub." 
	services="app-service" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/16/2015" 
	ms.author="tomfitz"/>

# Implementación de una aplicación web vinculada a un repositorio de GitHub

En este tema, aprenderá a crear una plantilla de Administrador de recursos de Azure que implementa una aplicación web que está vinculada a un proyecto en un repositorio de GitHub. Aprenderá a definir los recursos que se implementan y los parámetros que se especifican cuando se ejecuta la implementación. Puede usar esta plantilla para sus propias implementaciones o personalizarla para satisfacer sus necesidades.

Para obtener más información sobre la creación de plantillas, consulte [Creación de plantillas de Administrador de recursos de Azure](../resource-group-authoring-templates.md).

Para la plantilla completa, consulte [Aplicación web vinculada a la plantilla de GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Lo que implementará

Con esta plantilla, implementará una aplicación web que contiene el código de un proyecto en GitHub.

Para ejecutar automáticamente la implementación, haga clic en el botón siguiente:

[![Implementación en Azure](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## Parámetros

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### repoURL

La dirección URL del repositorio GitHub que contiene el proyecto que se va a implementar. Este parámetro contiene un valor predeterminado, pero la finalidad de este valor es únicamente mostrarle cómo proporcionar la dirección URL del repositorio. Puede usar este valor cuando pruebe la plantilla, pero lo más seguro es que quiera proporcionar la dirección URL de su propio repositorio cuando trabaje con la plantilla.

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### branch

La rama del repositorio que se va a usar al implementar la aplicación. El valor predeterminado es maestro, pero puede proporcionar el nombre de cualquier rama del repositorio que desee implementar.

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }
    
## Recursos para implementar

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### Aplicación web

Crea la aplicación web que está vinculada al proyecto en GitHub.

Especifique el nombre de la aplicación web a través del parámetro **siteName** y la ubicación de la aplicación web a través del parámetro **siteLocation**. En el elemento **dependsOn**, la plantilla define la aplicación web como dependiente del plan de hospedaje de servicio. Puesto que depende del plan de hospedaje, la aplicación web no se crea hasta que ha terminado de crearse el plan de hospedaje. El elemento **dependsOn** solo se usa para especificar el orden de implementación. Si no marca la aplicación web como dependientes en el plan de hospedaje, el Administrador de recursos de Azure intentará crear ambos recursos al mismo tiempo y puede recibir un error si la aplicación web se crea antes que el plan de hospedaje.

La aplicación web también tiene un recurso secundario que se define en la sección **Recursos** más adelante. Este recurso secundario define el control de código fuente para el proyecto implementado con la aplicación web. En esta plantilla, el control de código fuente está vinculado a un repositorio de GitHub determinado. El repositorio de GitHub se define con el código **"RepoUrl": "https://github.com/davidebbo-test/Mvc52Application.git"**. Podría codificar la dirección URL del repositorio cuando desee crear una plantilla que implemente de forma repetida un único proyecto y se requiera el número mínimo de parámetros. En lugar de codificar la dirección URL del repositorio, puede agregar un parámetro para ella y usar ese valor para la propiedad **RepoUrl**. Puede ver un ejemplo de parámetro de dirección URL de repositorio en la [Aplicación web con la plantilla de trabajos web](../app-service-web-deploy-web-app-with-webjobs.md).

    {
      "apiVersion":"2015-04-01",
      "name":"[parameters('siteName')]",
      "type":"Microsoft.Web/sites",
      "location":"[parameters('siteLocation')]",
      "dependsOn":[
         "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties":{
        "serverFarmId":"[parameters('hostingPlanName')]"
      },
       "resources":[
         {
           "apiVersion":"2015-04-01",
           "name":"web",
           "type":"sourcecontrols",
           "dependsOn":[
             "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
           ],
           "properties":{
             "RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git",
             "branch":"master"
           }
         }
       ]
     }

## Comandos para ejecutar la implementación

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json


 

<!---HONumber=AcomDC_1217_2015-->