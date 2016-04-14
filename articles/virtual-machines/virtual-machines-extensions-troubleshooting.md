<properties
   pageTitle="Solución de problemas de los errores de las extensiones de máquina virtual de Azure | Microsoft Azure"
   description="Obtenga información sobre la solución de problemas de los errores de la extensión de máquina virtual de Azure"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Solución de problemas de los errores de la extensión de máquina virtual de Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.


## Información general de las plantillas del Administrador de recursos de Azure

Las plantillas del Administrador de recursos de Azure le permiten especificar mediante declaración la infraestructura IaaS de Azure en el lenguaje Json definiendo las dependencias entre recursos.


Haga clic en el artículo [Creación de plantillas de extensión](virtual-machines-extensions-authoring-templates.md) para obtener más información sobre la creación de plantillas para el uso de extensiones.

En este artículo, obtendremos información sobre cómo solucionar algunos de los errores comunes de la extensión de máquina virtual.

## Ver el estado de la extensión:
Las plantillas del Administrador de recursos de Azure se pueden ejecutar desde Azure Powershell o CLI de Azure. Cuando se ejecute la plantilla, el estado de extensión se puede ver desde el Explorador de recursos de Azure o las herramientas de la línea de comandos. Estos son algunos ejemplos:

CLI de Azure:

      azure vm get-instance-view

Azure PowerShell:

      Get-AzureVM -ResourceGroupName $RGName -Name $vmName -Status

Este es la salida de ejemplo:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## Solucionar problemas de errores de extensión:

### Volver a ejecutar la extensión en la máquina virtual:

Si está ejecutando los scripts en la máquina virtual mediante la extensión de script personalizada, a veces podría recibir un error en el que la máquina virtual se creó correctamente pero en el que el script ha generado un error. En estas condiciones, la manera que se recomienda para recuperarse de este error es quitar la extensión y volver a ejecutar la plantilla. Nota: en el futuro, esta funcionalidad se mejoraría para eliminar la necesidad de desinstalar la extensión.

#### Quitar la extensión de CLI de Azure:

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

Donde "publisher-name" se corresponde con el tipo de extensión de la salida de "azure vm get-instance-view" y el nombre es el nombre del recurso de extensión de la plantilla.

#### Quitar la extensión de Azure Powershell:

    Remove-AzureVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Cuando se ha quitado la extensión, la plantilla puede volver a ejecutarse para ejecutar los scripts en la máquina virtual.

<!---HONumber=Oct15_HO3-->