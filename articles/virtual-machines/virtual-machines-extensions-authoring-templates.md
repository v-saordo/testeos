<properties
   pageTitle="Creación de plantillas con extensiones de VM de Azure | Microsoft Azure"
   description="Más información sobre la creación de plantillas con extensiones"
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
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Cree plantillas del Administrador de recursos de Azure con extensiones de VM.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.
 

## Información general de las plantillas del Administrador de recursos de Azure

Las plantillas del Administrador de recursos de Azure le permiten especificar mediante declaración la infraestructura IaaS de Azure en el lenguaje Json definiendo las dependencias entre recursos. Para obtener información más detallada de las plantillas del Administrador de recursos de Azure, consulte los siguientes artículos:

[Información general del grupo de recursos](../resource-group-overview.md)

## Fragmento de plantilla de ejemplo para extensiones de VM.
La implementación de extensión de máquinas virtuales como parte de la plantilla de Administrador de recursos de Azure requiere que especifique de forma declarativa la configuración de la extensión en la plantilla. Este es el formato para especificar la configuración de la extensión.

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

Como se puede ver en lo anterior, la plantilla de extensión contiene dos partes principales:

1. Nombre de extensión, publicador y versión.
2. Configuración de la extensión.

## Identificación de publisher, type y typeHandlerVersion para cualquier extensión.

Tanto Microsoft como publicadores de terceros de confianza publican las extensiones de VM de Azure y cada extensión se identifica de forma exclusiva por publisher, type y typeHandlerVersion. Pueden determinarse de la manera siguiente:

En Azure PowerShell, ejecute el siguiente cmdlet de Azure PowerShell:

      Get-AzureVMAvailableExtension

En la CLI de Azure, ejecute el siguiente cmdlet de Azure PowerShell:

      Azure VM Extension list

Este cmdlet devuelve el nombre de publicador, el nombre de extensión y la versión de la manera siguiente:

       Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Estas tres propiedades se asignan a "publisher", "type" y "typeHandlerVersion", respectivamente, en el fragmento de plantilla anterior. Nota: siempre se recomienda utilizar la versión más reciente de extensión para obtener la funcionalidad más actualizada.

## Identificación del esquema para los parámetros de configuración de extensión

El paso siguiente en la creación de plantillas de extensión es identificar el formato para proporcionar parámetros de configuración. Cada extensión es compatible con su propio conjunto de parámetros.

Para ver una configuración de ejemplo de las extensiones de Windows, haga clic en la documentación [Ejemplos de extensiones de Windows](virtual-machines-extensions-configuration-samples-windows.md).

Para consultar una configuración de ejemplo de las extensiones de Linux, haga clic en la documentación [Ejemplos de extensiones de Linux](virtual-machines-extensions-configuration-samples-linux.md).

Consulte lo siguiente en las plantillas de VM para obtener una plantilla totalmente completada con extensiones de VM.

[Extensión de script personalizada en una máquina virtual de Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

[Extensión del script personalizado en una máquina virtual de Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Después de crear la plantilla, puede implementarla con la CLI de Azure o Azure PowerShell.

<!---HONumber=Oct15_HO3-->