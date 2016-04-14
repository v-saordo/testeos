<properties
 pageTitle="Recursos de proceso de escalado automático en el clúster de HPC | Microsoft Azure"
 description="Obtener información sobre maneras de aumentar y reducir automáticamente los recursos de proceso en un clúster de HPC Pack en Azure"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="01/07/2016"
 ms.author="danlep"/>

# Escalar automáticamente los recursos de proceso de Azure hacia arriba y abajo en un clúster de HPC Pack según la carga de trabajo del clúster

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


Si implementa nodos de "ráfaga" de Azure en su clúster de HPC Pack, o crea un clúster de HPC Pack en máquinas virtuales de Azure, puede que quiera una manera de aumentar o reducir automáticamente los recursos informáticos de Azure según la carga de trabajo actual de los trabajos y las tareas en el clúster. Esto le permite usar su recursos de Azure de forma más eficiente y controlar sus costos. Para ello, use el script de PowerShell de HPC **AzureAutoGrowShrink.ps1** que se instala con HPC Pack.

>[AZURE.TIP]A partir de HPC Pack 2012 R2 Update 2, HPC Pack incluye un servicio integrado para aumentar y reducir los nodos de ráfaga de Azure automáticamente o los nodos de ejecución de la máquina virtual de Azure. Configure el servicio con una configuración en el [script de implementación de IaaS de HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md) o establezca manualmente la propiedad **AutoGrowShrink** del clúster. Consulte [Novedades en Microsoft HPC Pack 2012 R2 Update 2](https://technet.microsoft.com/library/mt269417.aspx).

## Requisitos previos

* **Clúster de HPC Pack 2012 R2 Update 1 o versiones posteriores**: el script **AzureAutoGrowShrink.ps1** se instala en la carpeta %CCP\_HOME%bin. El nodo principal del clúster se puede implementar de forma local o en una máquina virtual de Azure. Vea [configurar un clúster híbrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) para comenzar con un nodo principal local y nodos de "ráfaga" de Azure. Consulte el [script de implementación de HPC Pack IaaS](virtual-machines-hpcpack-cluster-powershell-script.md) para implementar rápidamente un clúster de HPC Pack en máquinas virtuales de Azure o use una [plantilla de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).

* **Azure PowerShell 0.8.12**: el script depende en esta versión específica de Azure PowerShell. Si ejecuta una versión posterior en el nodo principal, es posible que tenga que cambiar Azure PowerShell a la [versión 0.8.12](http://az412849.vo.msecnd.net/downloads03/azure-powershell.0.8.12.msi) para ejecutar el script.

* **Para un clúster con nodos de ráfaga de Azure**: ejecute el script en un equipo cliente donde esté instalado HPC Pack o en el nodo principal. Si se ejecuta en un equipo cliente, asegúrese de establecer la variable $env:CCP\_SCHEDULER correctamente para que apunte al nodo principal. Los nodos de "ráfaga" de Azure ya deben haberse agregado al clúster, pero es posible que se encuentren en el estado Not-Deployed.


* **Para un clúster implementado en máquinas virtuales de Azure**: ejecute el script en la máquina virtual del nodo principal, porque depende de los scripts **Start-HpcIaaSNode.ps1** y **Stop-HpcIaaSNode.ps1** que están instalados allí. Esas scripts requieren además un certificado de administración de Azure o un archivo de configuración de publicación (consulte [Administrar nodos de ejecución en un clúster de HPC Pack en Azure](virtual-machines-hpcpack-cluster-node-manage.md)). Asegúrese de que todas las máquinas virtuales de nodo de ejecución que necesita ya se agregaron al clúster. Pueden tener el estado Detenido.

## Sintaxis

```
AzureAutoGrowShrink.ps1
[[-NodeTemplates] <String[]>] [[-JobTemplates] <String[]>] [[-NodeType] <String>]
[[-NumOfQueuedJobsPerNodeToGrow] <Int32>]
[[-NumOfQueuedJobsToGrowThreshold] <Int32>]
[[-NumOfActiveQueuedTasksPerNodeToGrow] <Int32>]
[[-NumOfActiveQueuedTasksToGrowThreshold] <Int32>]
[[-NumOfInitialNodesToGrow] <Int32>] [[-GrowCheckIntervalMins] <Int32>]
[[-ShrinkCheckIntervalMins] <Int32>] [[-ShrinkCheckIdleTimes] <Int32>]
[-UseLastConfigurations] [[-ArgFile] <String>] [[-LogFilePrefix] <String>]
[<CommonParameters>]

```
## Parámetros

 * **NodeTemplates**: nombres de las plantillas de nodos para definir el ámbito de crecimiento y reducción de los nodos aumente. Si no se especifican (el valor predeterminado es @()), todos los nodos del grupo de nodos **AzureNodes** se encuentran dentro del ámbito cuando **NodeType** tiene un valor de AzureNodes y todos los nodos del grupo de nodos **ComputeNodes** se encuentran dentro del ámbito cuando **NodeType** tiene un valor de ComputeNodes.

 * **JobTemplates**: nombres de las plantillas de trabajos para definir el ámbito de crecimiento de los nodos.

 * **NodeType**: el tipo que se va a aumentar y a reducir. Los valores admitidos son:

     * **AzureNodes**: para los nodos (ráfaga) de Azure PaaS en un clúster de Azure IaaS o local.

     * **ComputeNodes**: para máquinas virtuales de nodos de ejecución solo en un clúster de Azure IaaS.

* **NumOfQueuedJobsPerNodeToGrow**: número de trabajos en cola que se requieren para aumentar un nodo.

* **NumOfQueuedJobsToGrowThreshold**: el número máximo de trabajos en cola para iniciar el proceso de crecimiento.

* **NumOfActiveQueuedTasksPerNodeToGrow** : el número de tareas en cola activas necesarias para aumentar un nodo. Si se especifica **NumOfQueuedJobsPerNodeToGrow** con un valor superior a 0, se omite este parámetro.

* **NumOfActiveQueuedTasksToGrowThreshold**: el número máximo de tareas en cola activas para iniciar el proceso de crecimiento.

* **NumOfInitialNodesToGrow**: el número mínimo inicial de nodos que se van a aumentar si todos los nodos del ámbito son **Not-Deployed** o **Stopped (Deallocated)**.

* **GrowCheckIntervalMins**: el intervalo en minutos entre comprobaciones que se van a aumentar.

* **ShrinkCheckIntervalMins**: el intervalo en minutos entre comprobaciones que se van a reducir.

* **ShrinkCheckIdleTimes**: el número de comprobaciones de reducción continua (separadas por **ShrinkCheckIntervalMins**) para indicar que los nodos están inactivos.

* **UseLastConfigurations*** Las configuraciones anteriores guardadas en el archivo de argumento.

* **ArgFile**: el nombre del archivo de argumento usado para guardar y actualizar las configuraciones para ejecutar el script.

* **LogFilePrefix**: el nombre del prefijo del archivo de registro. Puede especificar una ruta de acceso. De forma predeterminada, el registro escrito en el directorio de trabajo actual.

### Ejemplo 1

En el ejemplo siguiente se configuran los nodos de ráfaga de Azure implementados con la plantilla AzureNode predeterminada para aumentar y reducir automáticamente. Si todos los nodos se encuentran inicialmente en el nodo **Not-Deployed**, se inician al menos 3 nodos. Si el número de trabajos en cola es superior a 8, el script inicia nodos hasta que su número supera la proporción de trabajos en cola en **NumOfQueuedJobsPerNodeToGrow**. Si se descubre que un nodo está inactivo en 3 tiempos de inactividad consecutivos, se detiene.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### Ejemplo 2

En el ejemplo siguiente se configuran las máquinas virtuales del nodo de ejecución de Azure con la plantilla de ComputeNode predeterminada para aumentar y disminuir automáticamente. Los trabajos configurados por la plantilla de trabajo predeterminada definen el ámbito de la carga de trabajo en el clúster. Si todos los nodos se detienen inicialmente, se inician al menos 5 nodos. Si el número de tareas en cola activas es superior a 15, el script inicia nodos hasta que su número supera la relación de tareas en cola activas en **NumOfActiveQueuedTasksPerNodeToGrow**. Si se descubre que un nodo está inactivo en 10 tiempos de inactividad consecutivos, se detiene.

```
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```

<!---HONumber=AcomDC_0114_2016-->