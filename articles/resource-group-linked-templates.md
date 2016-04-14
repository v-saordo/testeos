<properties
   pageTitle="Uso de plantillas vinculadas con el Administrador de recursos de Azure"
   description="Describe cómo usar plantillas vinculadas en una plantilla del Administrador de recursos de Azure para crear una solución de plantilla modular. Muestra cómo pasar valores de parámetros y especificar un archivo de parámetros y las direcciones URL creadas dinámicamente."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/07/2015"
   ms.author="tomfitz"/>

# Uso de plantillas vinculadas con el Administrador de recursos de Azure

Desde dentro de una plantilla del Administrador de recursos de Azure, puede realizar la vinculación con otra plantilla que le permita descomponer la implementación en un conjunto de plantillas con fines específicos y dirigidas. Como ocurre con la descomposición de una aplicación en un número de clases de código, la descomposición proporciona ventajas en cuanto a las pruebas, la reutilización y la legibilidad.

Puede pasar parámetros de una plantilla principal a una plantilla vinculada de nuevo, y esos parámetros pueden asignarse directamente a parámetros o variables expuestas por la plantilla de llamada. La plantilla vinculada también puede pasar una variable de salida de nuevo a la plantilla de origen, lo que permite un intercambio de datos bidireccional entre las plantillas.

## Vinculación con una plantilla

Cree un vínculo entre dos plantillas mediante la adición de un recurso de implementación dentro de la plantilla principal que apunta a la plantilla vinculada. Para ello, debe establecer la propiedad **templateLink** en el URI de la plantilla vinculada. Puede proporcionar valores de parámetro para la plantilla vinculada especificando los valores directamente en la plantilla o mediante la vinculación a un archivo de parámetros. El siguiente ejemplo usa la propiedad **parameters** para especificar un valor de parámetro directamente.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

El Administrador de recursos debe poder tener acceso a la plantilla vinculada, lo que significa que no se puede especificar un archivo local o un archivo que solo esté disponible en la red local para la plantilla vinculada. Solo se puede proporcionar un valor de URI que incluya **http** o **https**. Una opción es colocar la plantilla vinculada en una cuenta de almacenamiento y usar el URI para dicho elemento, como se muestra a continuación.

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }


## Vinculación con un archivo de parámetros

El siguiente ejemplo utiliza la propiedad **parametersLink** para vincular a un archivo de parámetros.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

El valor del URI para el archivo del parámetro vinculado no puede ser un archivo local y debe incluir **http** o **https**.

## Uso de variables para vincular plantillas

Los ejemplos anteriores mostraron valores de dirección URL codificadas de forma rígida para los vínculos de la plantilla. Este enfoque puede funcionar en una plantilla sencilla pero no funciona bien cuando se trabaja con un gran conjunto de plantillas modulares. En su lugar, puede crear una variable estática que almacene una dirección URL base para la plantilla principal y, luego, crear dinámicamente direcciones URL para las plantillas vinculadas desde esa dirección URL base. La ventaja de este enfoque es que puede mover fácilmente o bifurcar la plantilla porque solo tendrá que cambiar la variable estática en la plantilla principal. La plantilla principal pasa los URI correctos por toda la plantilla descompuesta.

En el ejemplo siguiente se muestra cómo usar una dirección URL base para crear dos direcciones URL para las plantillas vinculadas (**sharedTemplateUrl** y **vmTemplate**).

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

También puede usar la función [deployment()](../resource-group-template-functions/#deployment) para obtener la dirección URL base de la plantilla actual y usar esta información para obtener la dirección URL de otras plantillas en la misma ubicación. Esto resulta útil si cambia la ubicación de la plantilla (probablemente debido al control de versiones) o desea evitar la codificación de forma rígida de las direcciones URL en el archivo de plantilla.

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## Paso de valores de nuevo desde una plantilla vinculada

Si necesita pasar un valor de una plantilla vinculada a la plantilla principal, puede crear un valor en la sección de **resultados** de la plantilla vinculada. Para obtener un ejemplo, consulte [Uso compartido del estado en las plantillas del Administrador de recursos de Azure](best-practices-resource-manager-state.md).

## Pasos siguientes
- [Creación de plantillas](./resource-group-authoring-templates.md)
- [Implementación de plantillas](resource-group-template-deploy.md)

<!---HONumber=AcomDC_1210_2015-->