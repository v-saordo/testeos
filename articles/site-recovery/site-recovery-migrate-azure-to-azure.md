<properties
	pageTitle="Migrar máquinas virtuales de IaaS de Azure de una región de Azure a otra con Site Recovery | Microsoft Azure"
	description="Use Azure Site Recovery para migrar máquinas virtuales de IaaS de Azure de una región de Azure a otra."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="raynew"/>

#  Migrar máquinas virtuales de IaaS de Azure entre regiones de Azure con Site Recovery


## Información general

Azure Site Recovery contribuye a su estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la organización de la replicación, la conmutación por error y la recuperación de máquinas virtuales en varios escenarios de implementación. Para obtener una lista completa de los escenarios de implementación, consulte [Información general sobre Azure Site Recovery](site-recovery-overview.md)

En este artículo se describe cómo usar Site Recovery para migrar máquinas virtuales de IaaS de Azure de una región de Azure a otra. Los artículos usan la mayoría de los pasos descritos en [Configuración de la protección entre las máquinas virtuales de VMware locales o servidores físicos y Azure](site-recovery-vmware-to-azure-classic-legacy.md). Le sugerimos que lea ese artículo para obtener instrucciones detalladas acerca de cada paso de la implementación.

## Primeros pasos

Esto es lo necesita antes de empezar:

- **Servidor de configuración**: máquina virtual de Azure que actúa como servidor de configuración. El servidor de configuración coordina la comunicación entre equipos locales y servidores de Azure.
- **Servidor de destino principal**: máquina virtual de Azure que actúa como servidor de destino principal. Este servidor recibe y conserva los datos replicados de equipos protegidos.
- **Servidor de procesos**: máquina virtual que ejecuta Windows Server 2012 R2. Las máquinas virtuales protegidas envían datos de replicación a este servidor.
- **Máquinas virtuales IaaS**: las máquinas virtuales que desea migrar.

- Obtenga más información acerca de estos componentes en [¿Qué es necesario?](site-recovery-vmware-to-azure-classic-legacy.md#what-do-i-need)
- También debe leer las instrucciones de [planificación de capacidad](site-recovery-vmware-to-azure-classic-legacy.md#capacity-planning) y asegurarse de cumplir todos los [requisitos previos de implementación](site-recovery-vmware-to-azure-classic-legacy.md#before-you-start) establecidos antes de empezar.

## Pasos de implementación

1. [Creación de un almacén](site-recovery-vmware-to-azure-classic-legacy.md#step-1-create-a-vault)
2. [Implementación de un servidor de configuración](site-recovery-vmware-to-azure-classic-legacy.md#step-2-deploy-a-configuration-server) como una máquina virtual de Azure.
3. [Implementación del servidor de destino principal](site-recovery-vmware-to-azure-classic-legacy.md#step-2-deploy-a-configuration-server) como una máquina virtual de Azure.
4. [Implementación de un servidor de procesos](site-recovery-vmware-to-azure.md-classic-legacy#step-4-deploy-the-on-premises-process-server). Observe lo siguiente:

	- Debe implementar el servidor de procesos en la misma red o subred virtual que las máquinas virtuales de IaaS que desea migrar. ![Máquinas virtuales de IaaS](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure1.png)

	- Una vez que haya implementado el servidor de procesos, compruebe que se puede comunicar con las máquinas virtuales que va a migrar.
	- Cada máquina virtual que desea proteger necesita tener instalado el servicio de Movilidad. Este servicio envía datos al servidor de procesos. El servicio de Movilidad puede instalarse manualmente o insertarse e instalarse automáticamente mediante el servidor de procesos cuando está habilitada la protección para la máquina virtual. Las reglas de firewall de máquinas virtuales de IaaS que desea migrar deben configurarse para permitir la instalación de inserción de este servicio. 

	- Una vez implementado y registrado el servidor de procesos con el servidor de configuración en el almacén de Site Recovery, deberá aparecer en la pestaña **Servidores de configuración** de la consola de Site Recovery. Tenga en cuenta que esto puede tardar hasta 15 minutos.
	
		![servidor de proceso](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure2.png)

5. [Instale las actualizaciones más recientes](site-recovery-vmware-to-azure-classic-legacy.md#step-5-install-latest-updates). Asegúrese de que los servidores de todos los componentes que haya instalado estén actualizados.
6. [Cree un grupo de protección](site-recovery-vmware-to-azure-classic-legacy.md#step-7-create-a-protection-group). Para poder empezar a proteger las máquinas virtuales migradas mediante Site Recovery, es necesario configurar un grupo de protección. Especifique la configuración de replicación de un grupo y se podrá aplicar a todas las máquinas que agregue a ese grupo. 
7. [Configure máquinas virtuales](site-recovery-vmware-to-azure-classic-legacy.md#step-8-set-up-machines-you-want-to-protect). Necesitará obtener el servicio de Movilidad instalado en cada máquina virtual (de forma automática o manual).
8. [Paso 8: Habilitación de la protección para las máquinas virtuales](site-recovery-vmware-to-azure-classic-legacy.md#step-9-enable-protection). Habilite la protección para máquinas virtuales agregándolas a un grupo de protección. Observe lo siguiente:

	- Puede detectar las máquinas virtuales de IaaS que desea migrar a Azure usando la dirección IP privada de las máquinas virtuales. Busque esta dirección en el panel de la máquina virtual de Azure.
	-  En la pestaña del grupo de protección que ha creado, haga clic en Agregar máquinas > Máquinas físicas. ![Detección de EC2](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure3.png)
	- Especifique la dirección IP privada de la máquina virtual.
		- ![Detección de EC2](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure4.png)
	- Se habilitará la protección y la replicación inicial se ejecutará según la configuración de la replicación inicial del grupo de protección.
9. [Paso 9: Ejecución de una conmutación por error no planeada](site-recovery-failover.md#run-an-unplanned-failover). Una vez completada la replicación inicial puede ejecutar una conmutación por error no planeada desde una región de Azure a otra. Si lo desea, puede crear un plan de recuperación y una conmutación por error no planeada para migrar varias máquinas virtuales entre las regiones. [Obtenga más información](site-recovery-create-recovery-plans.md) sobre los planes de recuperación.
		
## Pasos siguientes

Publique comentarios o preguntas en el [foro de Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

<!---HONumber=AcomDC_0121_2016-->