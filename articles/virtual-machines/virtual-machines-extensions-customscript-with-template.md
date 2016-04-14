<properties
   pageTitle="Scripts personalizados en máquinas virtuales mediante el uso de plantillas | Microsoft Azure"
   description="Automatizar tareas de configuración de máquinas virtuales Windows y Linux Azure mediante la extensión de script personalizado con plantillas de administrador de recursos"
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
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="11/01/2015"
   ms.author="kundanap"/>

# Uso de la extensión de scripts personalizados con plantillas del Administrador de recursos de Azure

Este artículo proporciona información general sobre la escritura de plantillas del Administrador de recursos de Azure con la extensión de scripts personalizados para cargas de trabajo de arranque en una máquina virtual de Linux o Windows.

Para obtener información general sobre la extensión de scripts personalizados, vea el artículo [aquí](virtual-machines-extensions-customscript.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-extensions-customscript.md).

Desde su lanzamiento, la extensión de scripts personalizados se ha usado ampliamente para configurar cargas de trabajo en máquinas virtuales de Windows y Linux. Con la introducción de las plantillas del Administrador de recursos de Azure, los usuarios ahora pueden crear una única plantilla que no solo aprovisiona la máquina virtual sino que también configura sus cargas de trabajo.

## Acerca de las plantillas del Administrador de recursos de Azure

Las plantillas del Administrador de recursos de Azure le permiten especificar mediante declaración la infraestructura IaaS de Azure en el lenguaje Json definiendo las dependencias entre recursos. Para obtener información más detallada de las plantillas del Administrador de recursos de Azure, consulte los siguientes artículos:

- [Información general del grupo de recursos](../resource-group-overview)
- [Implementación de plantillas con la CLI de Azure](virtual-machines-deploy-rmtemplates-azure-cli)
- [Implementación de plantillas con Azure PowerShell](virtual-machines-deploy-rmtemplates-powershell)

### Requisitos previos

1. Instale los últimos cmdlets de Azure PowerShell o la CLI de Azure [aquí](https://azure.microsoft.com/downloads/).
2. Si las secuencias de comandos se van a ejecutar en una máquina virtual existente, asegúrese de que el agente de máquina virtual está habilitado en la máquina virtual; si no lo está, siga las indicaciones de este [artículo](virtual-machines-extensions-install) para instalar uno.
3. Cargue las secuencias de comandos que desea ejecutar en la máquina virtual para el almacenamiento de Azure. Las secuencias de comandos pueden proceder de un único contenedor de almacenamiento o de varios.
4. También pueden cargarse los scripts en una cuenta de Github.
5. La secuencia de comandos debe crearse de forma tal que la secuencia de comandos de entrada que inicia la extensión inicie, a su vez, otras secuencias de comandos.

## Uso de la extensión de script personalizada

Para realizar la implementación con plantillas, usamos la misma versión de la extensión de scripts personalizados que está disponible para las API de administración de servicios de Azure. La extensión es compatible con los mismos parámetros y escenarios que la carga de archivos en la cuenta de Almacenamiento de Azure o la ubicación de Github. La diferencia clave cuando usa plantillas es que debe especificarse la versión exacta de la extensión, al contrario de la especificación de la versión en el formato majorversion.*.

 ## Ejemplo de plantilla para una máquina virtual de Linux

Definición del siguiente recurso de extensión en la sección Recurso de la plantilla

      {
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "MyCustomScriptExtension",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
    "properties":
    {
      "publisher": "Microsoft.OSTCExtensions",
      "type": "CustomScriptForLinux",
      "typeHandlerVersion": "1.2",
      "settings": {
      "fileUris": [ "https: //raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh                        ],
      "commandToExecute": "shmongo-install-ubuntu.sh"
      }
    }
    }

## Ejemplo de plantilla para una máquina virtual de Windows

Definición del siguiente recurso en la sección Recurso de la plantilla

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.4",
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

En los ejemplos anteriores, reemplace la dirección URL de archivo y el nombre de archivo con su propia configuración.

Después de crear la plantilla, puede implementarla con la CLI de Azure o Azure PowerShell.

Consulte los ejemplos siguientes para obtener ejemplos completos de configuración de aplicaciones en una máquina virtual con una extensión de script personalizado.

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Extensión del script personalizado en una máquina virtual de Linux</a>. </br> <a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">Extensión de script personalizada en una máquina virtual de Windows</a>.

<!---HONumber=AcomDC_0128_2016-->