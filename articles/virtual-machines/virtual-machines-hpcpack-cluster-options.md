<properties
 pageTitle="Opciones de clúster de HPC Pack en la nube | Microsoft Azure"
 description="Obtenga información sobre opciones con Microsoft HPC Pack para crear y administrar un clúster de informática de alto rendimiento (HPC) en la nube de Azure."
 services="virtual-machines,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="02/04/2016"
 ms.author="danlep"/>

# Opciones para crear y administrar un clúster de informática de alto rendimiento (HPC) en Azure con Microsoft HPC Pack

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Aproveche los servicios de infraestructura y proceso de Microsoft HPC Pack para crear y administrar un clúster de informática de alto rendimiento (HPC) basado en la nube. [HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) es la solución HPC gratuita de Microsoft que se basa en las tecnologías de Microsoft Azure y Windows Server y admite cargas de trabajo HPC tanto de Windows como de Linux. Un clúster de HPC Pack basado en la nube proporciona un administrador de clústeres o un proveedor de software independiente (ISV) a una plataforma flexible y escalable para ejecutar aplicaciones de proceso intensivo reduciendo al mismo tiempo la inversión en una infraestructura de clúster de proceso local.


## Ejecución de un clúster de HPC Pack en VM de Azure

### Plantillas de Azure

* (Marketplace) [HPC Pack cluster for Windows workloads (Clúster de HPC par cargas de trabajo de Windows)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (Marketplace) [HPC Pack cluster for Excel workloads (Clúster de HPC par cargas de trabajo de Excel)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (Marketplace) [HPC Pack cluster for Linux workloads (Clúster de HPC par cargas de trabajo de Linux)](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (Quickstart) [Create an HPC cluster (Creación de clúster de HPC)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/)

* (Quickstart) [Create an HPC cluster with Linux compute nodes (Creación de clúster de HPC con nodos de proceso de Linux)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/)

* (Quickstart) [Create an HPC cluster with custom compute node image (Creación de clúster de HPC con nodos de proceso personalizados)](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/)

### Imágenes de VM de Azure

* [HPC Pack en Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Nodo de proceso de HPC Pack en Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Nodo de proceso de HPC Pack con Excel en Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### Script de implementación de PowerShell

* [Creación de un clúster de HPC con el script de implementación de HPC Pack IaaS.](virtual-machines-hpcpack-cluster-powershell-script.md)

### Tutoriales

* [Tutorial: Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure](virtual-machines-linux-cluster-hpcpack.md)

* [Tutorial: Ejecute NAMD con Microsoft HPC Pack en los nodos de cálculo de Linux en Azure](virtual-machines-linux-cluster-hpcpack-namd.md)

* [Tutorial: Ejecución de OpenFOAM con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-cluster-hpcpack-openfoam.md)

* [Tutorial: Introducción a un clúster de HPC Pack en Azure para ejecutar cargas de trabajo de Excel y SOA](virtual-machines-excel-cluster-hpcpack.md)



### Implementación manual con el portal de Azure

* [Configuración del nodo principal de un clúster de HPC Pack en una VM de Azure](virtual-machines-hpcpack-cluster-headnode.md)

### Administración de clústeres

* [Administración de nodos de proceso en un clúster de HPC Pack en Azure](virtual-machines-hpcpack-cluster-node-manage.md)


* [Aumento y reducción de los recursos de proceso de Azure en un clúster de HPC Pack](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

* [Envío de trabajos a un clúster de HPC Pack en Azure](virtual-machines-hpcpack-cluster-submit-jobs.md)


## Adición de nodos de rol de trabajo a un clúster de HPC Pack


* [Irrumpir en instancias de trabajo de Azure con HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Tutorial: Configuración de un clúster híbrido con HPC Pack en Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Adición de nodos "ráfaga" de Azure a un nodo principal de HPC Pack en Azure](virtual-machines-hpcpack-cluster-node-burst.md)

* [Aumento y reducción de los recursos de proceso de Azure en un clúster de HPC Pack](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)

## Integración con Azure Batch 

* [Irrumpir en Lote de Azure con HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## Creación de clústeres RDMA para cargas de trabajo MPI

* [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](virtual-machines-windows-hpcpack-cluster-rdma.md)

* [Tutorial: Ejecución de OpenFOAM con Microsoft HPC Pack en un clúster de Linux RDMA en Azure](virtual-machines-linux-cluster-hpcpack-openfoam.md)

* [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](virtual-machines-linux-cluster-rdma.md)

<!---HONumber=AcomDC_0211_2016-->