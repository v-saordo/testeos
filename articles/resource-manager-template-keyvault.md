<properties
   pageTitle="Plantilla del Administrador de recursos para almacén de claves | Microsoft Azure"
   description="Muestra el esquema del Administrador de recursos para implementar los almacenes de claves mediante una plantilla."
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
   ms.date="02/23/2016"
   ms.author="tomfitz"/>

# Esquema de la plantilla del Almacén de claves

Crea un almacén de claves.

## Formato de esquema

Para crear un almacén de claves, agregue el siguiente esquema a la sección de recursos de la plantilla.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## Valores

Las tablas siguientes describen los valores que debe establecer en el esquema.

| Nombre | Tipo | Obligatorio | Valores permitidos | Descripción |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Sí | **Microsoft.KeyVault/vaults** | El tipo de recurso que se creará. |
| apiVersion | enum | Sí | **2015-06-01** <br /> **2014-12-19-preview** | La versión de la API que se usará para crear el recurso. | 
| name | cadena | Sí | | El nombre del almacén de claves que crear. El nombre debe ser único en todo Azure. Considere usar la función [uniqueString](resource-group-template-functions.md#uniquestring) junto con su convención de nomenclatura, tal como se indica en el ejemplo que aparece a continuación. |
| location | cadena | Sí | Para determinar las regiones válidas, consulte [Regiones admitidas](resource-manager-supported-services.md#supported-regions). | La región donde se hospedará el almacén de claves. |
| propiedades | objeto | Sí | ([se muestra a continuación](#properties)) | Un objeto que especifica el tipo de almacén de claves que se creará. |
| resources | array | No | [Secretos del Almacén de claves](resource-manager-template-keyvault-secret.md) | Recursos secundarios para el Almacén de claves. |

<a id="properties" />
### properties object

| Nombre | Tipo | Obligatorio | Valores permitidos | Descripción |
| ---- | ---- | -------- | ---------------- | ----------- |
| enabledForDeployment | boolean | No | **true** o **false** | Especifica si el almacén está habilitado para la implementación de Service Fabric o Máquina virtual. |
| enabledForTemplateDeployment | boolean | No | **true** o **false** | Especifica si el almacén está habilitado para su uso en implementaciones de plantilla del Administrador de recursos. Para obtener más información, consulte [Paso de valores seguros durante la implementación](resource-manager-keyvault-parameter.md). |
| enabledForVolumeEncryption | boolean | No | **true** o **false** | Especifica si el almacén está habilitado para el cifrado de volumen. |
| tenantId | cadena | Sí | Identificador único global | Identificador del inquilino para la suscripción. Puede recuperarlo con el cmdlet de PowerShell **AzureRMSubscription Get**. |
| accessPolicies | array | Sí | ([se muestra a continuación](#accesspolicies)) | Una matriz de hasta 16 objetos que especifican los permisos para el usuario o la entidad de servicio. |
| sku | objeto | Sí | ([se muestra a continuación](#sku)) | La SKU para el almacén de claves. |

<a id="accesspolicies" />
### properties.accessPolicies object

| Nombre | Tipo | Obligatorio | Valores permitidos | Descripción |
| ---- | ---- | -------- | ---------------- | ----------- |
| tenantId | cadena | Sí | Identificador único global | El identificador del inquilino de Azure Active Directory que contiene el **objectId** en esta directiva de acceso. |
| objectId | cadena | Sí | Identificador único global | El identificador de objeto del usuario AAD o entidad de servicio que tendrá acceso al almacén. Puede recuperar el valor desde los cmdlets **AzureRMADUser Get** o **AzureRMADServicePrincipal Get**. |
| permisos | objeto | Sí | ([se muestra a continuación](#permissions)) | Los permisos concedidos en este almacén al objeto de Active Directory. |

<a id="permissions" />
### properties.accessPolicies.permissions object

| Nombre | Tipo | Obligatorio | Valores permitidos | Descripción |
| ---- | ---- | -------- | ---------------- | ----------- |
| claves | array | Sí | Una lista separada por comas de los siguientes valores:<br />**all**<br />**backup**<br />**create**<br />**decrypt**<br />**delete**<br />**encrypt**<br />**get**<br />**import**<br />**list**<br />**restore**<br />**sign**<br />**unwrapkey**<br/>**update**<br />**verify**<br />**wrapkey** | Los permisos concedidos para las claves de este almacén a este objeto de Active Directory. Este valor debe especificarse como una matriz de valores permitidos. |
| secrets | array | Sí | Una lista separada por comas de los valores siguientes:<br />**all**<br />**delete**<br />**get**<br />**list**<br />**set** | Los permisos concedidos para los secretos de este almacén a este objeto de Active Directory. Este valor debe especificarse como una matriz de valores permitidos. |

<a id="sku" />
### properties.sku object

| Nombre | Tipo | Obligatorio | Valores permitidos | Descripción |
| ---- | ---- | -------- | ---------------- | ----------- |
| name | enum | Sí | **standard**<br />**premium** | El nivel de servicio de KeyVault que usar. Standard admite secretos y claves protegidas mediante software. Premium incorpora compatibilidad para claves protegidas con HSM. |
| family | enum | Sí | **ENCONTRARÁ** | La familia de SKU que usar. 
 
	
## Ejemplos

En el ejemplo siguiente se implementa un almacén de claves y un secreto.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant Id for the subscription and use assigned access to the vault. Available from the Get-AzureRMSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRMADUser or the Get-AzureRMADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

## Plantillas de inicio rápido

La siguiente plantilla de inicio rápido implementa un almacén de claves.

- [Create Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)


## Pasos siguientes

- Para obtener información general sobre almacenes de claves, consulte [Introducción al Almacén de claves de Azure](./key-vault/key-vault-get-started.md).
- Para obtener un ejemplo de referencia de un secreto de almacén de claves al implementar plantillas, consulte [Paso de valores seguros durante la implementación](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0224_2016-->