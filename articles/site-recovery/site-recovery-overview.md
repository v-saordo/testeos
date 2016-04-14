<properties
	pageTitle="¿Qué es Site Recovery? | Microsoft Azure" 
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales ubicadas localmente en Azure o en un sitio local secundario." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="02/22/2016" 
	ms.author="raynew"/>

#  ¿Qué es Site Recovery?

El servicio Azure Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. Lea algunas preguntas comunes en nuestras [Preguntas más frecuentes](site-recovery-faq.md).

## ¿Por qué usar Site Recovery? 

- **Historia de BCDR más sencilla**: Site Recovery facilita el control de la replicación, la conmutación por error y la recuperación para sus aplicaciones y cargas de trabajo locales.
- **Replicación flexible**: puede replicar servidores locales, máquinas virtuales de Hyper-V y máquinas virtuales de VMware. Site Recovery realiza la replicación inteligente, en la que se replican solo los bloques de datos, no todo el VHD para la replicación inicial. Para la replicación continua solo se replican los cambios diferenciales. Site Recovery admite la transferencia de datos sin conexión y funciona con los optimizadores de WAN. 
- **Eliminación de la necesidad de un centro de datos secundario**: Site Recovery puede automatizar la replicación entre los centros de datos, pero también proporciona la oportunidad de renunciar a una ubicación local secundaria mediante la replicación en Azure. Los datos replicados se almacenan en Almacenamiento de Azure con toda la resistencia que proporciona.


## Escenarios de implementación

Esta tabla resume los escenarios de replicación admitidos por Site Recovery.

**REPLICATE** | **REPLICACIÓN DESDE** | **REPLICACIÓN EN** | **ARTÍCULO**
---|---|---|---
Máquinas virtuales de VMware | Servidor VMware local | Almacenamiento de Azure | [Implementación](site-recovery-vmware-to-azure-classic.md)
Servidor físico de Windows/Linux | Servidor físico local | Almacenamiento de Azure | [Implementación](site-recovery-vmware-to-azure-classic.md)
Máquinas virtuales de Hyper-V | Servidor de host de Hyper-V local en la nube VMM | Almacenamiento de Azure | [Implementación](site-recovery-vmm-to-azure.md)
Máquinas virtuales de Hyper-V | Sitio de Hyper-V local (uno o más servidores de host de Hyper-V) | Almacenamiento de Azure | [Implementación](site-recovery-hyper-v-site-to-azure.md)
Máquinas virtuales de Hyper-V locales| Servidor de host de Hyper-V local en la nube VMM | Servidor de host de Hyper-V local en la nube VMM en el centro de datos secundario | [Implementación](site-recovery-vmm-to-vmm.md)
Máquinas virtuales de Hyper-V | Servidor de host de Hyper-V local en la nube VMM con almacenamiento SAN| Servidor de host de Hyper-V local en la nube VMM en el almacenamiento SAN en el centro de datos secundario | [Implementación](site-recovery-vmm-san.md)
Máquinas virtuales de VMware | Servidor VMware local | Centro de datos secundario que ejecuta VMware | [Implementación](site-recovery-vmware-to-vmware.md) 
Servidor físico de Windows/Linux | Servidor físico local | Centro de datos secundario | [Implementación](site-recovery-vmware-to-vmware.md) 

Se resumen en los diagramas siguientes.

![De local a local](./media/site-recovery-overview/asr-overview-graphic.png)

## ¿Qué cargas de trabajo puedo proteger?

Site Recovery ayuda a la continuidad empresarial con reconocimiento del dispositivo. Puede usar Site Recovery para coordinar la recuperación ante desastres para  
Windows y aplicaciones de terceros. Esta protección compatible con reconocimiento de las aplicaciones:


- La replicación casi sincrónica con RPO de tan solo 30 segundos para Hyper-V y la replicación continua para VMware, para cumplir con las necesidades de las aplicaciones más críticas.
- Instantáneas coherentes con la aplicación para aplicaciones simples o de N niveles
- Realice la integración con SQL Server AlwaysOn asociada con otras tecnologías de replicación de nivel de aplicación, incluida la replicación de Active Directory, DAG de Exchange y Oracle Data Guard.
- Los planes de recuperación flexibles permiten la recuperación de toda la pila de aplicaciones con un solo clic e incluyen los scripts externos o las acciones manuales. 
- La administración de red avanzada en Site Recovery y Azure simplifican los requisitos de red para una aplicación, incluida la reserva de direcciones IP, la configuración de equilibradores de carga o la integración del Administrador de tráfico de Azure para conmutaciones de red de RTO.
- Una biblioteca de automatización enriquecida que proporciona scripts específicos de la aplicación y preparados para la producción que pueden descargarse e integrarse con Site Recovery.  


Obtenga más información en [¿Qué cargas de trabajo se pueden proteger con Azure Site Recovery?](site-recovery-workload.md)


## Pasos siguientes

Una vez finalizada la esta descripción general, obtenga [más información](site-recovery-components.md) acerca de la arquitectura de Site Recovery.
 

<!---HONumber=AcomDC_0224_2016-->