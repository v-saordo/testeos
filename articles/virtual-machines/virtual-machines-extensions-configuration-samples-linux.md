<properties
   pageTitle="Ejemplo de configuración para las extensiones de la máquina virtual de Linux | Microsoft Azure"
   description="Configuración de ejemplo para crear plantillas con extensiones para máquinas virtuales de Linux"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Ejemplos de configuración de la extensión de máquina virtual Linux

> [AZURE.SELECTOR]
- [PowerShell - Template](virtual-machines-extensions-configuration-samples-windows.md)
- [CLI - Template](virtual-machines-extensions-configuration-samples-linux.md)

<br>


Este artículo proporciona un ejemplo de configuración para configurar las extensiones de máquina virtual de Azure para máquinas virtuales Linux.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.


Para obtener más información sobre estas extensiones, haga clic aquí: [Información general de las extensiones de máquinas virtuales de Azure.](virtual-machines-extensions-features.md)

Para obtener más información sobre la creación de plantillas de extensión, haga clic aquí: [Creación de plantillas de extensión.](virtual-machines-extensions-authoring-templates.md)

En este artículo se indican los valores de configuración esperados para algunas de las extensiones de Linux.

## Fragmento de plantilla de ejemplo para extensiones de VM.
El fragmento de plantilla para extensiones de implementación tiene el aspecto siguiente:

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

Antes de implementar la extensión, compruebe la versión más reciente de la extensión y reemplace la "typeHandlerVersion" por la versión más reciente actual.

El resto del artículo proporciona ejemplos de configuraciones para las extensiones de máquina virtual de Linux.

### Agente de SecureVM de CloudLink
          {
            "publisher": "CloudLinkEMC.SecureVM",
            "type": "CloudLinkSecureVMLinuxAgent",
            "typeHandlerVersion": "4.0",
            "settings": {
              "CloudLinkCenter" : "specify valid IP/FQDN to CloudLinkCenter"
            }
          }

### Extensión de CustomScript para Linux.
    {
        "publisher": " Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.3",
        "settings": {
            "fileUris": [
                "http: //Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
            ],
            "commandToExecute": "powershell.exe-ExecutionPolicyUnrestricted-Filestart.ps1"
        }
    }


### Agente de Datadog
        {
          "publisher": "Datadog.Agent",
          "type": "DatadogLinuxAgent",
          "typeHandlerVersion": "0.4",
          "settings": {
            "api_key" : "API Key from https://app.datadoghq.com/account/settings#api"
          }
        }

### Agente de Chef
        {
          "publisher": "Chef.Bootstrap.WindowsAzure",
          "type": "CentosChefClient|LinuxChefClient",
          "typeHandlerVersion": "1210.12",
          "settings": {
            "validation_key" : " Validation key",
            "client_rb" : "client_rb file",
            "runlist" : "Optional runlist"
          }
        }

### Extensión de acceso máquina virtual (restablecimiento de contraseña)
Para el esquema actualizado, consulte la [Documentación de VMAccessForLinux](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

        {
          "publisher": "Microsoft.OSTCExtensions",
          "type": "VMAccessForLinux",
          "typeHandlerVersion": "1.2",
          "protectedSettings": {
            "username": "(required, string) the name of the user",
            "password": "(optional, string) the password of the user",
            "reset_ssh": "(optional, boolean) whether or not reset the ssh",
            "ssh_key": "(optional, string) the public key of the user, base64 encoded pem",
            "remove_user": "(optional, string) the user name to remove"
          }
        }

### Revisión de SO
Para el esquema actualizado, consulte la [Documentación de OSPatching](https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching).

        {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "OSPatchingForLinux",
        "typeHandlerVersion": "2.9",
        "Settings": {
          "disabled": false,
          "stop": false,
          "rebootAfterPatch": "RebootIfNeed|Required|NotRequired|Auto",
          "category": "Important|ImportantAndRecommended",
          "installDuration": "<hr:min>",
          "oneoff": false,
          "intervalOfWeeks": "<number>",
          "dayOfWeek": "Sunday|Monday|Tuesday|Wednesday|Thursday|Friday|Saturday|Everyday",
          "startTime": "<hr:min>",
          "vmStatusTest": {
              "local": false,
              "idleTestScript": "<path_to_idletestscript>",
              "healthyTestScript": "<path_to_healthytestscript>"
          }
        }
        }

### Extensión de Docker
Para el esquema actualizado, consulte la [Documentación la extensión de Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md#1-configuration-schema).

        {
          "publisher": "Microsoft.Azure.Extensions ",
          "type": "DockerExtension ",
          "typeHandlerVersion": "1.0",
          "Settings": {
            "docker":{
                "port": "2376",
                "options": ["-D", "--dns=8.8.8.8"]
            },
            "compose": {
                "cache" : {
                    "image" : "memcached",
                    "ports" : ["11211:11211"]
                },
                "blog": {
                    "image": "ghost",
                    "ports": ["80:2368"]
                }
            }
            }
        }

        ### Linux Diagnostics Extension
        {
        "storageAccountName": "storage account to receive data",
        "storageAccountKey": "key of the account",
        "perfCfg": [
        {
            "query": "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
            "table": "LinuxMemory"
        }
        ],
        "fileCfg": [
        {
            "file": "/var/log/mysql.err",
            "table": "mysqlerr"
        }
        ]
        }

En los ejemplos anteriores, reemplace el número de versión por el número de versión más reciente.

Esta es una plantilla de máquina virtual completa para la creación de una VM de Linux con una extensión:

[Extensión del script personalizado en una máquina virtual de Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

<!---HONumber=AcomDC_0114_2016-->