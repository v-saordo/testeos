<properties
	pageTitle="Preparación de la implementación de Azure Site Recovery | Microsoft Azure"
	description="En este artículo se describe cómo prepararse para la implementación de la replicación con Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="02/15/2016"
	ms.author="raynew"/>

# Preparación de la implementación de Azure Site Recovery

El servicio Azure Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. Para obtener una introducción rápida, lea [¿Qué es Azure Site Recovery?](site-recovery-overview.md).

## Información general

Azure Site Recovery admite la replicación de máquinas virtuales de VMware e Hyper-V y de servidores físicos en Azure o en un centro de datos secundario. En este artículo se describe cómo preparar la implementación de Azure Site Recovery para cada uno de estos escenarios de replicación.

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Requisitos de implementación para la replicación de Hyper-V

En la tabla se resumen los requisitos de implementación general para la replicación de Hyper-V en Azure (con y sin VMM) y en un sitio secundario. La tabla le ayuda a entender y comparar los requisitos generales de cada escenario de replicación. También se proporciona un vínculo a los requisitos previos de implementación detallados.

**Replicación en Azure (con VMM)** | **Replicación en Azure (sin VMM)** | **Replicación en un sitio secundario (con VMM)**
---|---|---
**VMM**: al menos un servidor VMM que se ejecute en System Center 2012 R2. El servidor VMM debe tener al menos una nube que contenga uno o más grupos de hosts VMM.<br/><br/> **Hyper-V**: uno o varios servidores host de Hyper-V en el centro de datos local que ejecuten como mínimo Windows Server 2012 R2. El servidor de Hyper-V debe estar ubicado en un grupo de hosts de una nube de máquina virtual.<br/><br/> **Máquinas virtuales**: necesitará al menos una máquina virtual en el servidor de Hyper-V de origen. Las máquinas virtuales que se replican en Azure deben cumplir los [requisitos previos para las máquinas virtuales de Azure](#azure-virtual-machine-requirements).<br/><br/> **Cuenta de Azure**: necesitará una cuenta y una suscripción de [Azure](https://azure.microsoft.com/).<br/><br/> **Almacenamiento de Azure**: necesitará una [cuenta de almacenamiento de Azure](../storage/storage-redundancy.md#geo-redundant-storage) para almacenar los datos replicados. Los datos replicados se almacenan en Almacenamiento de Azure y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error.<br/><br/> **Asignación de red**: configure una asignación de red para que todas las máquinas que conmuten por error en la misma red Azure se puedan conectar entre sí, con independencia del plan de recuperación al que pertenezcan. Si hay una puerta de enlace de red en la red Azure de destino, las máquinas virtuales también se pueden conectar a las máquinas virtuales locales. Si no configura la asignación de red, solo se pueden conectar las máquinas que conmutan por error en el mismo plan de recuperación.<br/><br/> **Proveedores y agentes**: durante la implementación, instalará el proveedor de Azure Site Recovery en servidores VMM y el Agente de Servicios de recuperación de Azure en los servidores host de Hyper-V. El proveedor se comunica con Azure Site Recovery. El agente administra la replicación de datos entre los servidores de Hyper-V de origen y destino. No se instala nada en las máquinas virtuales.<br/><br/> **Conectividad a Internet**: desde el servidor VMM y los hosts de Hyper-V.<br/><br/> **Conectividad del proveedor**: si el proveedor se conecta a Site Recovery mediante un proxy, deberá asegurarse de que el proxy pueda tener acceso a las direcciones URL de Site Recovery.<br/><br/> [Requisitos previos de implementación detallados](site-recovery-vmm-to-azure.md#before-you-start) | **Hyper-V**: al menos un servidor de Hyper-V en los sitios de origen y destino que ejecute como mínimo Windows Server 2012 R2.<br/><br/> **Máquinas virtuales**: al menos una máquina virtual en el servidor de Hyper-V de origen. La replicación de máquinas virtuales en Azure debe ajustarse a los [requisitos previos de máquinas virtuales de Azure](#azure-virtual-machine-requirements)<br/><br/> **Cuenta de Azure**: necesitará una cuenta y una suscripción de [Azure](https://azure.microsoft.com/).<br/><br/> **Almacenamiento de Azure**: necesitará una [ cuenta de Almacenamiento de Azure](../storage/storage-redundancy.md#geo-redundant-storage) para almacenar los datos replicados.<br/><br/> **Proveedores y agentes**: durante la implementación, instalará el proveedor de Azure Site Recovery y el Agente de Servicios de recuperación de Azure en servidores host o clústeres de Hyper-V. No se instala nada en las máquinas virtuales.<br/><br/> **Conectividad a Internet**: desde los hosts de Hyper-V.<br/><br/> **Conectividad del proveedor**: si el proveedor se conecta mediante un proxy, deberá asegurarse de que el proxy pueda tener acceso a las direcciones URL de Site Recovery.<br/><br/> [Requisitos previos de implementación detallados](site-recovery-hyper-v-site-to-azure.md#before-you-start#before-you-start) | **VMM**: el servidor VMM debe tener al menos una nube que contenga uno o más grupos de hosts VMM. Las nubes deben tener establecido el perfil de capacidad de Hyper-V. <br/><br/>**Hyper-V**: uno o varios servidores de Hyper-V en los sitios de origen y destino que ejecuten al menos Windows Server 2012 con las actualizaciones más recientes. El servidor de Hyper-V debe encontrarse en un grupo de hosts de una nube VMM.<br/><br/> **Máquinas virtuales**: al menos una máquina virtual en la nube VMM de origen. <br/><br/> **Asignación de red**: configure la asignación de red de modo que las máquinas virtuales estén conectadas a las redes adecuadas tras la conmutación por error y las máquinas virtuales de réplica estén colocadas óptimamente en los servidores host de Hyper-V de destino. Si no configura la asignación de red, las máquinas replicadas no se conectarán a ninguna red de máquinas virtuales tras la conmutación por error.<br/><br/> **Asignación de almacenamiento**: opcionalmente, puede configurar la asignación de almacenamiento para asegurarse de que las máquinas virtuales estén conectadas óptimamente al almacenamiento tras la conmutación por error (de forma predeterminada, la máquina virtual de réplica se almacenará en la ubicación indicada en el servidor de Hyper-V de destino).<br/><br/> **Replicación de SAN**: si desea replicar entre dos sitios VMM locales con la replicación de SAN, puede usar su entorno de SAN existente. Consulte las [matrices de SAN admitidas](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).<br/><br/> **Proveedores y agentes**: durante la implementación, instalará el proveedor de Azure Site Recovery en servidores VMM para comunicarse con Azure Site Recovery. La replicación se produce entre los servidores de origen y destino de Hyper-V mediante la LAN o la red privada virtual (VPN).<br/><br/> **Conectividad a Internet**: solo en el servidor VMM.<br/><br/> **Conectividad del proveedor**: si el proveedor se conecta mediante un proxy, deberá asegurarse de que sea posible tener acceso a las direcciones URL de Site Recovery.<br/><br/> [Requisitos previos de implementación detallados](site-recovery-vmm-to-vmm.md#before-you-start)


## Requisitos de implementación para la replicación de servidores físicos y máquinas virtuales de VMware

En la tabla se resumen los requisitos para la replicación de máquinas virtuales de VMware y servidores físicos de Windows o Linux en Azure y en un sitio secundario.

>[AZURE.NOTE] Puede replicar máquinas virtuales de VMware y servidores físicos a Azure con un modelo de implementación [mejorado](site-recovery-vmware-to-azure-classic.md) o con un modelo [heredado](site-recovery-vmware-to-azure-classic-legacy.md) que usara en implementaciones anteriores. En la tabla siguiente se incluyen los requisitos de implementación de cada modelo.

**Replicación a Azure (mejorado)** | **Replicación a Azure (heredado)** | **Replicar al sitio secundario**
---|---|---
**Servidor de administración local**: en el sitio local necesitará un servidor dedicado que actúe como servidor de administración. Todos los componentes de Site Recovery se instalan en este servidor.<br/><br/> **Servidores de procesos adicionales**: un servidor de procesos se instala de forma predeterminada en el servidor de administración, pero puede instalar opcionalmente servidores de procesos locales adicionales para escalar la implementación.<br/><br/> **VMware vCenter/ESXi**: si va a replicar máquinas virtuales de VMware (o desea conmutar por recuperación servidores físicos atrás), necesitará un hipervisor vSphere ESX/ESXi en el que se encuentran las máquinas virtuales. Se recomienda un servidor vCenter para administrar los hosts ESXi.</br><br/> **Conmutación por recuperación**: necesitará un entorno de VMware para conmutar por recuperación desde Azure, incluso si replica servidores físicos. Además, deberá configurar un servidor de procesos como una máquina virtual de Azure y, si va a conmutar por recuperación grandes volúmenes de tráfico, puede que quiera configurar un servidor de destino maestro local adicional. [Más información](site-recovery-failback-azure-to-vmware-classic.md)<br/><br/> **Cuenta de Azure**: necesitará una cuenta y una suscripción de [Azure](https://azure.microsoft.com/).<br/><br/> **Almacenamiento de Azure**: necesitará una [cuenta de Almacenamiento de Azure](../storage/storage-redundancy.md#geo-redundant-storage) para almacenar los datos replicados. Los datos replicados se almacenan en Almacenamiento de Azure y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error.<br/><br/> **Red virtual de Azure**: necesitará una red virtual de Azure a la que se conectarán las máquinas virtuales de Azure cuando se produzca la conmutación por error. Para realizar la conmutación por recuperación después de la conmutación por error, necesitará una conexión VPN (o Azure ExpressRoute) configurada entre la red de Azure y el sitio local.<br/><br/> **Máquinas protegidas**: al menos una máquina virtual de VMware o un servidor físico de Windows o Linux. Durante la implementación, el servicio de movilidad se instala en cada máquina que quiera replicar.<br/><br/> **Conectividad**: si el servidor de administración se conecta a Site Recovery mediante un proxy, deberá asegurarse de que el servidor proxy pueda conectarse a las direcciones URL específicas.<br/><br/> [Requisitos previos de implementación detallados](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | **Sitio primario**: deberá configurar un servidor de procesos.<br/><br/> **Conmutación por recuperación**: necesita un entorno de VMware para conmutar por recuperación desde Azure, incluso si replica servidores físicos. En el sitio local deberá configurar un servidor de vContinuum y un servidor de destino maestro. En Azure, deberá configurar un servidor de procesos. [Más información](site-recovery-failback-azure-to-vmware-classic-legacy.md)<br/><br/> **Cuenta de Azure**: necesitará una cuenta y una suscripción de [Azure](https://azure.microsoft.com/).<br/><br/> **Almacenamiento de Azure**: necesitará una [cuenta de Almacenamiento de Azure](../storage/storage-redundancy.md#geo-redundant-storage) para almacenar los datos replicados. Los datos replicados se almacenan en Almacenamiento de Azure y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error.<br/><br/> **Máquinas virtuales de infraestructura de Azure**: deberá configurar un servidor de configuración y un servidor de destino maestro como máquinas virtuales de Azure.<br/><br/> **Red virtual de Azure**: necesitará una red virtual de Azure en la que se implementarán el servidor de configuración y el servidor de destino maestro. Las máquinas virtuales de Azure se conectarán a esta red después de la conmutación por error.<br/><br/> **Máquinas protegidas**: al menos una máquina virtual de VMware o un servidor físico de Windows o Linux. Durante la implementación, el servicio de movilidad se instala en cada máquina que quiere replicar.<br/><br/> **Conectividad**: si el servidor de administración se conecta a Site Recovery mediante un proxy, deberá asegurarse de que el servidor proxy pueda conectarse a las direcciones URL específicas.<br/><br/> [Requisitos previos de implementación detallados](site-recovery-vmware-to-azure-classic-legacy.md#before-you-start) | **Sitio primario**: un servidor de Windows dedicado (físico o una máquina virtual de VMware).<br/><br/> **Sitio secundario**: servidores dedicados de configuración y de destino maestro.<br/><br/> **Máquinas protegidas**: al menos una máquina virtual de VMware o un servidor físico de Windows o Linux. Durante la implementación, se instala el agente unificado en cada equipo.





## Requisitos para las máquinas virtuales de Azure

Puede implementar Site Recovery para replicar máquinas virtuales y servidores físicos que ejecuten cualquier sistema operativo compatible con Azure. Incluye la mayoría de las versiones de Windows y Linux. Debe asegurarse de que las máquinas virtuales locales que desea proteger cumplan los requisitos de Azure.


**Característica** | **Soporte técnico** | **Detalles**
---|---|---
Sistema operativo host de Hyper-V | Windows Server 2012 R2 | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Sistema operativo de hipervisor de VMware | Con un sistema operativo compatible | [Detalles](site-recovery-vmware-to-azure.md#before-you-start)
Sistema operativo invitado | Para la replicación de Hyper-V en Azure, Site Recovery es compatible con todos los sistemas operativos [admitidos por Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para la replicación de servidores físicos y VMware, consulte los [requisitos previos](site-recovery-vmware-to-azure.md#before-you-start) para Windows y Linux. | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Arquitectura del sistema operativo invitado | 64 bits | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Tamaño del disco del sistema operativo | Hasta 1.023 GB | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Número de discos del sistema operativo | 1 | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Número de discos de datos | 16 o menos (el valor máximo es una función del tamaño de la máquina virtual que se está creando. 16 = XL) | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Tamaño de VHD del disco de datos | Hasta 1023 GB | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Adaptadores de red | Se admiten varios adaptadores |
Dirección IP estática | Compatible | Si la máquina virtual principal usa una dirección IP estática, puede especificar la dirección IP estática para la máquina virtual que se creará en Azure.
Disco iSCSI | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
VHD compartido | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Disco FC | No compatible | Se producirá un error en la comprobación de los requisitos previos si no es compatible.
Formato de disco duro| VHD <br/><br/> VHDX | Aunque VHDX no se admite en Azure en este momento, Site Recovery convierte automáticamente VHDX en VHD cuando se conmuta por error en Azure. Cuando se realiza la conmutación por recuperación en local, las máquinas virtuales siguen usando el formato VHDX.
Nombre de la máquina virtual| Entre 1 y 63 caracteres. Restringido a letras, números y guiones. El nombre debe empezar y terminar por una letra o un número. | Actualizar el valor de las propiedades de la máquina virtual en Site Recovery
Tipo de máquina virtual | <p>Generación 1</p> <p>Generación 2 - Windows</p> | Se admiten las máquinas virtuales de generación 2 con el tipo de disco de SO de disco básico que incluye uno o dos volúmenes de datos con un formato de disco como VHDX, inferior a 300 GB. No se admiten máquinas virtuales Linux de generación 2. [Más información](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## Optimización de la implementación

Aplique las siguientes sugerencias como ayuda para optimizar y escalar la implementación.

- **Tamaño del volumen del sistema operativo**: cuando se replica una máquina virtual en Azure, el volumen del sistema operativo tiene que ser inferior a 1 TB. Si tiene más volúmenes, puede moverlos manualmente a otro disco antes de comenzar la implementación.
- **Tamaño de disco de datos**: si se está replicando en Azure, podrá tener hasta 32 discos de datos en una máquina virtual, cada una con un máximo de 1 TB. Puede replicar y conmutar por error de manera eficaz una máquina virtual de ~32 TB.
- **Límites del plan de recuperación**: Site Recovery puede escalar a miles de máquinas virtuales. Los planes de recuperación están diseñados como un modelo para las aplicaciones que deben conmutar por error entre sí por lo que se limita el número de máquinas en un plan de recuperación a 50.
- **Límites del servicio Azure**: cada suscripción de Azure incluye un conjunto de límites predeterminados sobre núcleos, servicios en la nube, etc. Le recomendamos que ejecute una conmutación por error de prueba para validar la disponibilidad de los recursos de la suscripción. Puede modificar estos límites a través de soporte técnico de Azure.
- **Planemiento de capacidad**: obtenga información sobre el [planeamiento de la capacidad](site-recovery-capacity-planner.md) de Site Recovery.
- **Ancho de banda de replicación**: si tiene poco ancho de banda de replicación, tenga en cuenta lo siguiente:
	- **ExpressRoute**: Site Recovery funciona con los optimizadores de ExpressRoute de Azure y WAN, como Riverbed. [Obtenga más información](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre ExpressRoute.
	- **Tráfico de replicación**: Site Recovery realiza una replicación inicial inteligente usando solo bloques de datos y no todo el VHD. Solo se replican los cambios durante la replicación continua.
	- **Tráfico de red**: puede controlar el tráfico de red que se utiliza para la replicación mediante la configuración de [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) con una directiva basada en la dirección IP de destino y el puerto. Además, si se está replicando en Azure Site Recovery mediante el agente Copia de seguridad de Azure. Puede configurar la limitación para ese agente. [Más información](https://support.microsoft.com/kb/3056159).
- **RTO**: si desea medir el objetivo de tiempo de recuperación (RTO) que se puede esperar con Site Recovery, le sugerimos que ejecute una conmutación por error de prueba y vea los trabajos de Site Recovery para analizar la cantidad de tiempo que se tarda en completarse las operaciones. Si está conmutando por error en Azure, para obtener un mejor RTO se recomienda que automatice todas las acciones manuales mediante la integración con Automatización de Azure y los planes de recuperación.
- **RPO**: Site Recovery admite un objetivo de punto de recuperación casi sincrónico (RPO) al replicar a Azure. Esto supone suficiente ancho de banda entre el centro de datos y Azure.





## Pasos siguientes

Una vez que conoce y puede comparar los requisitos de implementación generales, puede leer los requisitos previos detallados y comenzar a implementar cada escenario.

- [Replicación de máquinas virtuales de VMware en Azure](site-recovery-vmware-to-azure-classic.md)
- [Replicación de servidores físicos a Azure](site-recovery-vmware-to-azure-classic.md)
- [Replicación de servidores de Hyper-V de nubes VMM a Azure](site-recovery-vmm-to-azure.md)
- [Replicación de máquinas virtuales de Hyper-V (sin VMM) a Azure](site-recovery-hyper-v-site-to-azure.md)
- [Replicación de máquinas virtuales de Hyper-V a un sitio secundario](site-recovery-vmm-to-vmm.md)
- [Replicación de máquinas virtuales de Hyper-V a un sitio secundario con SAN](site-recovery-vmm-san.md)
- [Replicación de máquinas virtuales de Hyper-V con un solo servidor VMM](site-recovery-single-vmm.md)

<!---HONumber=AcomDC_0218_2016-->