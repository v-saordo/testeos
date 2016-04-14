<properties
   pageTitle="Uso del secreto del Almacén de claves con la plantilla del Administrador de recursos | Microsoft Azure"
   description="Muestra cómo pasar un secreto de un almacén de claves como un parámetro durante la implementación."
   services="azure-resource-manager,key-vault"
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
   ms.date="02/09/2016"
   ms.author="tomfitz"/>

# Paso de valores seguros durante la implementación

Si necesita pasar un valor seguro (como una contraseña) como un parámetro durante la implementación, puede almacenar ese valor como un secreto en un [Almacén de claves de Azure](./key-vault/key-vault-whatis.md) y hacer referencia al valor en otras plantillas del Administrador de recursos. Incluya solo una referencia al secreto en la plantilla para que el secreto nunca se exponga. No es necesario especificar manualmente el valor para el secreto cada vez que implemente los recursos. Especifique qué usuarios o entidades de servicio pueden tener acceso al secreto.

## Implementación de un almacén de claves y un secreto

Para crear un almacén de claves al que se pueda hacer referencia desde otras plantillas del Administrador de recursos, debe establecer la propiedad **enabledForTemplateDeployment** en **true**, y debe conceder acceso al usuario o entidad de servicio que ejecutará la implementación que hace referencia al secreto.

Para obtener información acerca de cómo implementar un almacén de claves y un secreto, consulte [Esquema del almacén de claves](resource-manager-template-keyvault.md) y [Esquema del secreto del almacén de claves](resource-manager-template-keyvault-secret.md).

## Referencia a un secreto

Se hace referencia al secreto desde dentro de un archivo de parámetros que pasa valores a la plantilla. Se hace referencia al secreto pasando el identificador de recurso de almacén de claves y el nombre del secreto.

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

Un archivo de parámetros completo podría tener el siguiente aspecto:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

El parámetro que acepta el secreto debe ser **securestring**. En el ejemplo siguiente se muestran las secciones pertinentes de una plantilla que implementa un SQL Server que requiere una contraseña de administrador.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }




## Pasos siguientes

- Para obtener información general sobre almacenes de claves, consulte [Introducción al Almacén de claves de Azure](./key-vault/key-vault-get-started.md).
- Para obtener información sobre el uso de un almacén de claves con una máquina virtual, consulte [Consideraciones de seguridad para Azure Resource Manager](best-practices-resource-manager-security.md).
- Para obtener ejemplos completos de secretos de clave de referencia, consulte [Ejemplos del Almacén de claves](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

<!---HONumber=AcomDC_0224_2016-->