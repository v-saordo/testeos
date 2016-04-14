<properties
 pageTitle="Acerca de las instancias A8, A9, A10 y A11 | Microsoft Azure"
 description="Obtenga información general y consideraciones sobre el uso de las instancias de proceso intensivo A8, A9, A10 y A11 de Azure."
 services="virtual-machines, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="infrastructure-services"
 ms.date="01/26/2016"
 ms.author="danlep"/>

# Sobre las instancias de proceso intensivo A8, A9, A10 y A11

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


En este artículo se proporciona información general y consideraciones sobre el uso de las instancias de Azure A8, A9, A10 y A11, también conocidas como instancias de *proceso intensivo*. Entre las características clave de estas instancias se incluyen:

* **Hardware de alto rendimiento**: el hardware de centro de datos de Azure que ejecuta estas instancias está diseñado y optimizado para el proceso intensivo y para aplicaciones de red intensiva, incluidas las aplicaciones de clúster de proceso de alto rendimiento (HCP), el modelado y las simulaciones.

* **Conexión de red RDMA para aplicaciones MPI**: puede configurar las instancias A8 y A9 para que se comuniquen con otras instancias A8 y A9 a través de una red de baja latencia y alto rendimiento en Azure basadas en tecnología de acceso a memoria directa remota (RDMA). Esta característica puede mejorar el rendimiento de determinadas aplicaciones de la interfaz de transferencia de mensajes (MPI) de Linux y Windows.

* **Compatibilidad con clústeres de HPC de Windows y Linux**: implemente la administración del clúster y el software de programación de trabajos en las instancias A8, A9, A10 y A11 en Azure para crear un clúster HPC independiente o para agregar más capacidad a un clúster local.

>[AZURE.NOTE]Las instancias A10 y A11 tienen las mismas optimizaciones y especificaciones de rendimiento que las instancias A8 y A9. Sin embargo, no incluyen el acceso a la red RDMA en Azure. Están diseñadas para aplicaciones HPC que no requieren comunicación constante y de baja latencia entre nodos, también conocidas como aplicaciones paramétricas o embarazosamente paralelas.


## Especificaciones

### CPU y memoria

Las instancias de proceso intensivo Azure A8, A9, A10 y A11 cuentan con CPU de alta velocidad y varios núcleos y grandes cantidades de memoria, como se muestra en la tabla siguiente.

Tamaño | CPU | Memoria
------------- | ----------- | ----------------
A8 y A10 | Intel Xeon E5-2670<br/>8 núcleos a 2,6 GHz | DDR3-1600 MHz<br/>56 GB
A9 y A11 | Intel Xeon E5-2670<br/>16 núcleos a 2,6 GHz | DDR3-1600 MHz<br/>112 GB


>[AZURE.NOTE]En el sitio web Intel.com hay detalles adicionales de procesador, incluidas las extensiones del conjunto de instrucciones compatibles. Para obtener información sobre las capacidades de almacenamiento de máquina virtual y los detalles del disco, consulte [Tamaños de máquinas virtuales](virtual-machines-size-specs.md).

### Adaptadores de red

Las instancias A8 y A9 tienen dos adaptadores de red que se conectan a las siguientes redes back-end de Azure.


Red | Descripción
-------- | -----------
Ethernet de 10 Gbps | Se conecta a los servicios de Azure (por ejemplo, Almacenamiento de Azure y Red virtual de Azure) y a Internet.
Back-end de 32 Gbps, compatible con RDMA | Permite una baja latencia y comunicación de aplicaciones de alto rendimiento entre las instancias de un servicio en la nube único o un conjunto de disponibilidad. Reservado solo para el tráfico MPI.


>[AZURE.IMPORTANT]Vea [Acceso a la red RDMA](#access-the-rdma-network) en este artículo para conocer requisitos adicionales para que las aplicaciones de la MPI de Linux y Windows accedan a la red RDMA.

Las instancias A10 y A11 tienen un único adaptador de red Ethernet de 10 Gbps que se conecta a servicios de Azure e Internet.

## Consideraciones de la implementación

* **Cuenta de Azure**: si desea implementar más de un pequeño número de instancias de proceso intensivo, considere la posibilidad de usar una suscripción de pago por uso u otras opciones de compra. También puede utilizar su suscripción a MSDN. Consulte [Ventajas de Azure para suscriptores de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Si usa una [evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/), solo puede usar un número limitado de núcleos de proceso de Azure.

* **Cuota de núcleos** : es posible que necesite aumentar la cuota de núcleos de su suscripción de Azure desde el valor predeterminado de 20 núcleos por suscripción (si se usa el modelo de implementación clásica) o 20 núcleos por región (si utiliza el modelo de implementación del Administrador de recursos de Azure). Para solicitar un aumento de cuota, abra una incidencia de soporte técnico gratuita, tal como se muestra en [Descripción de los límites y aumentos de Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/).

    >[AZURE.NOTE]Las cuotas de Azure son límites de crédito, no garantías de capacidad. Solamente se le cobrarán los núcleos que use.

* **Red virtual**: no se necesita una [red virtual](https://azure.microsoft.com/documentation/services/virtual-network/) de Azure para usar instancias de proceso intensivo. Sin embargo, puede que necesite al menos una red virtual Azure basada en la nube para muchos escenarios IaaS o una conexión de sitio a sitio si necesita tener acceso a recursos locales como un servidor de licencias de aplicaciones. Necesitará crear una nueva red virtual para implementar las instancias. No se admite la adición de una máquina virtual A8, A9, A10 o A11 a una red virtual en un grupo de afinidad.

* **Servicio o conjunto de disponibilidad en la nube** : para conectarse a través de la red RDMA, las instancias A8 y A9 deben implementarse en el mismo servicio en la nube (si utiliza el modelo de implementación clásica) o en el mismo conjunto de disponibilidad (si utiliza el modelo de implementación del Administrador de recursos de Azure).

## Consideraciones sobre el uso de HPC Pack

### Consideraciones sobre el HPC Pack y Linux

[HPC Pack](https://technet.microsoft.com/library/jj899572.aspx) es la solución gratuita de administración de trabajos y clústeres HPC de Microsoft para Windows. Las últimas versiones de HPC Pack 2012 R2 admiten varias distribuciones de Linux en nodos de ejecución implementados en máquinas virtuales de Azure, administradas por un nodo principal de Windows Server. Para obtener más información, consulte [Introducción a los nodos de proceso de Linux en un clúster de HPC Pack en Azure](virtual-machines-linux-cluster-hpcpack.md).

### Consideraciones sobre HPC Pack y Linux

HPC Pack no es necesario para usar las instancias A8, A9, A10 y A11 con Windows Server, pero es una herramienta recomendada para crear clústeres basados en Windows HPC Server en Azure. En el caso de las instancias de A8 y A9, HPC Pack es la forma más eficaz de ejecutar aplicaciones de MPI basadas en Windows que tienen acceso a la red RDMA en Azure. HPC Pack incluye un entorno de tiempo de ejecución para la implementación de Microsoft de la interfaz de transferencia de mensajes (MS-MPI) para Windows.

Para obtener más información y listas de comprobación para usar instancias de proceso intensivo con HPC Pack en Windows Server, consulte [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](virtual-machines-windows-hpcpack-cluster-rdma.md).

## Acceso a la red RDMA

### Acceso desde máquinas virtuales Linux A8 y A9

En un único servicio en la nube o en un conjunto de disponibilidad, las instancias A8 y A9 pueden acceder a la red RDMA en Azure para ejecutar aplicaciones de la MPI de Linux. En este momento, Azure Linux RDMA solo se admite con [Intel MPI Library 5](https://software.intel.com/es-ES/intel-mpi-library/).

>[AZURE.NOTE] Actualmente los controladores Azure Linux RDMA no están disponibles para instalarse a través de extensiones de controladores. Solamente están disponibles mediante la imagen SLES 12 habilitada para RDMA desde Azure Marketplace.

En la tabla siguiente se resumen los requisitos previos para las aplicaciones de la MPI de Linux para acceder a la red RDMA en clústeres de nodos de ejecución (IaaS). Consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](virtual-machines-linux-cluster-rdma.md) para obtener opciones de implementación y pasos de configuración.

Requisito previo | Máquinas virtuales (IaaS)
------------ | -------------
Sistema operativos | Imagen de SLES 12 HPC de Azure Marketplace
MPI | Intel MPI Library 5

### Acceso desde instancias Windows A8 y A9

En un único servicio en la nube o conjunto de disponibilidad, las instancias A8 y A9 pueden tener acceso a la red RDMA de Azure para ejecutar aplicaciones de la MPI de Windows que usan la interfaz Microsoft Network Direct para comunicarse entre instancias.

Consulte la siguiente tabla para obtener información acerca de los requisitos previos para que las aplicaciones de MPI tengan acceso a la red RDMA en las implementaciones de máquina virtual (IaaS) y de servicio en la nube (PaaS) de las instancias A8 o A9. Para escenarios de implementación típicos, consulte [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](virtual-machines-windows-hpcpack-cluster-rdma.md).


Requisito previo | Máquinas virtuales (IaaS) | Servicios en la nube (PaaS)
---------- | ------------ | -------------
Sistema operativos | Windows Server 2012 R2 o Windows Server 2012 | Familia de SO invitado Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2.
MPI | MS-MPI 2012 R2 o versiones posteriores, ya sean independientes o instaladas a través de HPC Pack 2012 R2 o versiones posteriores<br/><br/>Intel MPI Library 5 | MS-MPI 2012 R2 o versiones posteriores, instaladas a través de HPC Pack 2012 R2 o versiones posteriores<br/><br/>Intel MPI Library 5


>[AZURE.NOTE]Para escenarios de IaaS, es necesario agregar la extensión HpcVmDrivers a las máquinas virtuales para instalar los controladores de dispositivo de red de Windows necesarios para la conectividad RDMA. Según su método de implementación, la extensión HpcVmDrivers podría agregarse automáticamente a una máquina virtual de tamaño A8 o A9 o es posible que tenga que agregarla manualmente. Para agregar la extensión, consulte [Administración de extensiones de máquina virtual](virtual-machines-extensions-install.md).


## Aspectos adicionales que debe conocer

* **Precios**: los tamaños de máquina virtual A8-A11 solo están disponibles en el plan de tarifas estándar.

* **Cambio de tamaño**: no puede cambiar el tamaño de una instancia con un tamaño distinto de A8-A11 a alguno de los tamaños de instancias de proceso intensivo (A8-11) ni tampoco puede cambiar el tamaño de una instancia de proceso intensivo a un tamaño no de proceso intensivo. Esto se debe al hardware especializado y a las optimizaciones de rendimiento que son específicos para las instancias de proceso intensivo.

* **Espacio de direcciones de la red RDMA**: la red RDMA en Azure reserva el espacio de direcciones 172.16.0.0/12. Si planea ejecutar aplicaciones MPI en instancias A8 y A9 en una red virtual de Azure, asegúrese de que el espacio de direcciones de la red virtual no se superpone a la red RDMA.

## Pasos siguientes

* Para obtener más información sobre la disponibilidad y los precios de las instancias A8, A9, A10 y A11, consulte [Precios de máquinas virtuales](https://azure.microsoft.com/pricing/details/virtual-machines/) y [Precios de servicios en la nube](https://azure.microsoft.com/pricing/details/cloud-services/).
* Para implementar y configurar un clúster basado en Linux con instancias A8 y A9 para tener acceso a la red RDMA de Azure, consulte [Configuración de un clúster de Linux RDMA para ejecutar aplicaciones MPI](virtual-machines-linux-cluster-rdma.md).
* Para empezar a implementar y usar instancias A8 y A9 con HPC Pack en Windows, consulte [Configuración de un clúster de Windows RDMA con HPC Pack para ejecutar aplicaciones MPI](virtual-machines-windows-hpcpack-cluster-rdma.md).

<!---HONumber=AcomDC_0204_2016-->