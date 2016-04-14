<properties 
	pageTitle="Azure Site Recovery: Preguntas más frecuentes (P+F) | Microsoft Azure" 
	description="En este artículo se responde a las preguntas más frecuentes acerca de Azure Site Recovery."
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
	ms.date="02/14/2016"
	ms.author="raynew"/>


# Azure Site Recovery: preguntas más frecuentes (P+F)
## Información acerca de este artículo

En este artículo se incluyen las preguntas más frecuentes sobre Site Recovery. Si tiene alguna pregunta después de leer las preguntas más frecuentes, publíquela en el [Foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## Soporte técnico

###¿Qué hace Site Recovery?

Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres mediante la coordinación y la automatización de la replicación desde máquinas virtuales locales y servidores físicos a Azure o a un centro de datos secundario. [Más información](site-recovery-overview.md).


### ¿Qué se puede proteger con Site Recovery?

- **Máquinas virtuales de Hyper-V**: Site Recovery puede proteger cualquier carga de trabajo que se ejecute en una máquina virtual de Hyper-V. 
- **Servidores físicos**: Site Recovery puede proteger servidores físicos con Windows o Linux.
- **Máquinas virtuales de VMware**: Site Recovery puede proteger cualquier carga de trabajo que se ejecute en una máquina virtual de VMware.


###¿Qué máquinas virtuales de Hyper-V se pueden proteger?

Depende del escenario de implementación.

Compruebe los requisitos previos del servidor de host de Hyper-V en:

- [Replicación de máquinas virtuales de Hyper-V (sin VMM) a Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Replicación de máquinas virtuales de Hyper-V (con VMM) a Azure](site-recovery-vmm-to-azure.md#before-you-start)
- [Replicación de máquinas virtuales de Hyper-V a un centro de datos secundario](site-recovery-vmm-to-vmm.md#before-you-start)

Respecto a los sistemas operativos invitados:

- Si está replicando a un centro de datos secundario, consulte una lista de los sistemas operativos invitados compatibles para las máquinas virtuales que se ejecutan en servidores de host de Hyper-V en [Sistemas operativos invitados compatibles para máquinas virtuales de Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
- Si está replicando a Azure, Site Recovery es compatible con todos los sistemas operativos invitados [admitidos por Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

Site Recovery puede proteger cualquier carga de trabajo que se ejecute en una máquina virtual compatible.

### ¿Se pueden proteger las máquinas virtuales cuando se ejecuta Hyper-V en un sistema operativo cliente?

No, esto no se admite. Como solución alternativa deberá replicar el equipo como una máquina física [a Azure](site-recovery-vmware-to-azure-classic.md) o a un [centro de datos secundario](site-recovery-vmware-to-vmware.md).


### ¿Qué cargas de trabajo se pueden proteger con Site Recovery?

Puede usar Site Recovery para proteger la mayoría de las cargas de trabajo que se ejecutan en una máquina virtual o un servidor físico. Site Recovery puede ayudarle a implementar la recuperación ante desastres en función de las aplicaciones. Se integra con aplicaciones de Microsoft, incluido SharePoint, Exchange, Dynamics, SQL Server y Active Directory, y colabora estrechamente con los principales proveedores, como Oracle, SAP, IBM y Red Hat. Puede personalizar su solución de recuperación ante desastres para cada aplicación específica. [Obtenga más información](site-recovery-workload.md) acerca de la protección de la carga de trabajo.


### ¿Se necesita siempre un servidor de System Center VMM para proteger las máquinas virtuales de Hyper-V? 

No. Además de poder replicar máquinas virtuales de Hyper-V ubicadas a nubes de VMM, también puede replicar máquinas virtuales de Hyper-V a un entorno en el que no se haya implementado VMM. [Más información](site-recovery-hyper-v-site-to-azure.md). Tenga en cuenta que si está replicando a un centro de datos secundario, los servidores de host de Hyper-V deben administrarse en nubes de VMM.

### ¿Se puede implementar Site Recovery con VMM si solo se tiene un servidor de VMM? 

Sí. Puede replicar máquinas virtuales de Hyper-V en la nube en el servidor de VMM a Azure o puede replicar entre nubes de VMM en el mismo servidor. Tenga en cuenta que se recomienda que para la replicación de local a local se tenga un servidor de VMM tanto en el sitio principal como en el secundario. [Más información](site-recovery-single-vmm.md)

### ¿Qué servidores físicos se pueden proteger?

Puede proteger servidores físicos con Windows y Linux, en Azure o en un sitio secundario. [Conozca](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) los requisitos acerca del sistema operativo. Se aplican las mismas limitaciones si está replicando servidores físicos a Azure o a un sitio secundario.

Tenga en cuenta que los servidores físicos se ejecutarán como máquinas virtuales en Azure si se desactiva el servidor local. La conmutación por recuperación a un servidor físico local no está admitida actualmente. Solo puede conmutar por error a una máquina virtual que se ejecutan en VMware.

### ¿Qué máquinas virtuales de VMware se pueden proteger?

En este escenario necesitará un servidor VMware vCenter, un hipervisor vSphere y máquinas virtuales que ejecutan herramientas de VMware. [Conozca](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) los requisitos exactos. Se aplican las mismas limitaciones si está replicando servidores físicos a Azure o a un sitio secundario.

### ¿Hay algún requisito previo para replicar las máquinas virtuales a Azure?

Las máquinas virtuales que quiera replicar a Azure deben cumplir con los [Requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

### ¿Se pueden replicar máquinas virtuales de generación 2 de Hyper-V a Azure?

Sí. Site Recovery convierte de la generación 2 a la generación 1 durante una conmutación por error. En la conmutación por recuperación, la máquina se convierte de nuevo a la generación 2. [Más información](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Si se replica a Azure ¿cómo se paga por las máquinas virtuales de Azure? 

Durante una replicación normal, los datos se replican en el almacenamiento de Azure con redundancia geográfica y no es necesario pagar los cargos de máquina virtual de IaaS de Azure, lo cual proporciona una ventaja considerable. Cuando se realiza una conmutación por error a Azure, Site Recovery crea automáticamente las máquinas virtuales de IaaS de Azure, después de lo cual se le factura por los recursos de proceso que se consumen en Azure.

### ¿Es posible administrar la recuperación ante desastres para las sucursales con Site Recovery?

Sí. Si usa Site Recovery para coordinar la replicación y la conmutación por error en las sucursales, disfrutará de una coordinación unificada y una vista de todas las cargas de trabajo de las sucursales desde un mismo punto. Puede ejecutar con facilidad conmutaciones por error y administrar la recuperación ante desastres de todas las sucursales desde la sede central, sin necesidad de visitar estas sucursales.

### ¿Hay un SDK que se pueda usar para automatizar el flujo de trabajo de Site Recovery?

Sí. Puede automatizar los flujos de trabajo de Site Recovery mediante la API de Rest, PowerShell o el SDK de Azure. Conozca más información acerca de [la implementación de Azure Site Recovery con PowerShell y el Administrador de recursos de Azure](site-recovery-deploy-with-powershell-resource-manager.md).


## Seguridad y cumplimiento normativo

### ¿Se envían mis datos al servicio de Site Recovery?

No, Site Recovery no intercepta los datos de la aplicación ni tiene información sobre lo que se ejecuta en sus máquinas virtuales o servidores físicos.

Los datos de replicación se intercambian entre hosts de Hyper-V, hipervisores de VMware o servidores físicos en los centros de datos principal y secundario, o entre el centro de datos y el almacenamiento de Azure. Site Recovery no tiene capacidad para interceptar los datos. Únicamente se envían los metadatos necesarios para coordinar la replicación y la conmutación por error al servicio Site Recovery.

Site Recovery está certificado según la norma ISO 27001:2005 y está completando sus evaluaciones de HIPAA, DPA y FedRAMP JAB.

### Para garantizar el cumplimiento, incluso los metadatos de nuestros entornos locales deben permanecer dentro de la misma región geográfica. ¿Site Recovery puede ayudarnos?

Sí. Al crear un almacén de Site Recovery en una región, garantizamos que todos los metadatos que necesitamos para habilitar y coordinar la replicación y la conmutación por error permanezcan dentro del límite geográfico de esa región.

### ¿Site Recovery cifra la replicación?
Al replicar máquinas virtuales y servidores físicos entre sitios locales, se admite el cifrado en tránsito. Al replicar máquinas virtuales y servidores físicos entre sitios locales y Azure, se admite tanto el cifrado en tránsito como el cifrado en Azure.




## Replicación y conmutación por error

### Si se replica a Azure, ¿qué tipo de cuenta de almacenamiento se necesita?

Necesitará una cuenta de almacenamiento con [almacenamiento con redundancia geográfica estándar](../storage/storage-introduction.md#replication-for-durability-and-high-availability). El Almacenamiento premium no se admite actualmente.

### ¿Con qué frecuencia se pueden replicar los datos?
- **Hyper-V:** las máquinas virtuales de Hyper-V que se estén ejecutando en Windows Server 2012 R2 se pueden replicar cada 30 segundos, 5 minutos o 15 minutos. Si ha configurado la replicación de SAN, la replicación debe ser sincrónica.
- **VMware y servidores físicos:** en este caso no es relevante la frecuencia de replicación. La replicación será continua. 

### ¿Se puede ampliar la replicación desde el sitio de recuperación existente a otro tercer sitio?
No se admite la replicación extendida o encadenada. Envíe los comentarios que tenga acerca de esta característica al [foro de comentarios](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication/).


### ¿Se puede hacer una replicación sin conexión la primera vez que se replique en Azure? 

No es una opción admitida. Envíenos los comentarios que tenga acerca de esta característica al [foro de comentarios](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from/).


### ¿Se pueden excluir discos específicos de la replicación?

No es una opción admitida. Envíenos los comentarios que tenga acerca de esta característica al [foro de comentarios](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication/).

### ¿Se pueden replicar máquinas virtuales con discos dinámicos?

Los discos dinámicos se admiten al replicar máquinas virtuales de Hyper-V. Sin embargo, no se admiten al replicar máquinas virtuales de VMware o servidores físicos. Envíenos los comentarios que tenga acerca de esta característica al [foro de comentarios](https://feedback.azure.com/forums/256299-site-recovery/).

### Si se realiza una conmutación por error a Azure, ¿cómo se puede tener acceso a las máquinas virtuales de Azure tras este proceso? 

Es posible tener acceso a las máquinas virtuales de Azure a través de una conexión segura a Internet o a través de una VPN de sitio a sitio (o Azure ExpressRoute) si dispone de una. Las comunicaciones a través de una conexión VPN van a puertos internos de la red de Azure en la que se encuentra la máquina virtual. Las comunicaciones a través de Internet se asignan a los extremos públicos en el servicio en la nube de Azure para máquinas virtuales.

### Si se realiza una conmutación por error a Azure, ¿cómo se asegura Azure de que los datos resistan el proceso?

Azure está diseñado para la resistencia del servicio. Site Recovery ya se ha diseñado para la conmutación por error en un centro de datos de Azure secundario según el SLA de Azure por si surge la necesidad de hacerlo. Si esto ocurre, nos aseguraremos de que los metadatos y los almacenes permanecen en la misma región geográfica que eligió para su almacén.

### Si se replica entre dos centros de datos, ¿qué ocurre si el centro de datos principal experimenta una interrupción inesperada?

Puede desencadenar una conmutación por error no planeada desde el sitio secundario. Site Recovery no necesita conectividad desde el sitio principal para realizar la conmutación por error.


### ¿La conmutación por error es automática?

La conmutación por error no es automática. Puede iniciar las conmutaciones por error con solo un clic en el portal o bien puede utilizar los [cmdlets de Site Recovery PowerShell](https://msdn.microsoft.com/library/dn850420.aspx) para desencadenar una conmutación por error. La conmutación por recuperación también es una acción sencilla en el portal de Site Recovery. Para automatizar estos procesos, puede utilizar Orchestrator u Operations Manager locales para detectar un error de la máquina virtual y desencadenar luego la conmutación por error mediante el SDK.

### Si se replican máquinas virtuales de Hyper-V, ¿puedo limitar el ancho de banda asignado para el tráfico de replicación de Hyper-V?
- Si va a replicar entre dos sitios locales de máquinas virtuales de Hyper-V, puede usar QoS de Windows. Este es un script de ejemplo: 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- Si está replicando máquinas virtuales de Hyper-V a Azure, puede configurar el límite mediante el siguiente cmdlet de PowerShell de ejemplo:

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)



## Implementación del proveedor de servicios

### ¿Site Recovery funciona para los modelos de infraestructura compartida o dedicada?
Sí, Site Recovery admite ambos modelos de infraestructura, dedicados y compartidos.

### ¿La identidad de mi inquilino compartido se comparte con el servicio Site Recovery?
No. En realidad, los inquilinos no tienen que acceder al portal de Site Recovery. El administrador del proveedor de servicios es el único que realiza acciones en el portal.


### ¿Los datos de la aplicación de mis inquilinos llegarán a Azure?
Cuando se replica entre sitios que pertenecen al proveedor de servicios, los datos de la aplicación nunca llegan a Azure. Los datos se cifran en tránsito y se replican directamente entre los sitios del proveedor de servicios.

Si está replicando a Azure, los datos de la aplicación se envían al almacenamiento de Azure, pero no al servicio Site Recovery. Los datos se cifran en tránsito y permanecen cifrados en Azure.

### ¿Recibirán mis inquilinos una factura por los servicios de Azure?

No. La relación de facturación de Azure se entabla directamente con el proveedor de servicios. Los proveedores de servicios son responsables de generar facturas específicas para sus inquilinos.

### Si se está replicando a Azure, ¿es necesario ejecutar máquinas virtuales en Azure en todo momento?

No, los datos se replican a una cuenta de almacenamiento de Azure con redundancia geográfica en su suscripción. Al realizar una conmutación por error de prueba (obtención de detalles de recuperación ante desastres) o una conmutación por error real, Site Recovery crea automáticamente las máquinas virtuales en su suscripción.

### ¿Se puede garantizar el aislamiento a nivel de inquilino al replicar a Azure?

Sí.

### ¿Qué plataformas se admiten actualmente?

Se admite Azure Pack, el sistema de plataforma en la nube e implementaciones basadas en System Center (2012 y superiores). Obtenga más información acerca de la integración de Azure Pack y Site Recovery en [Configurar la protección para máquinas virtuales](https://technet.microsoft.com/library/dn850370.aspx).

### ¿Se admite un paquete Azure Pack sencillo e implementaciones de servidor VMM individuales?

Sí, es posible replicar máquinas virtuales de Hyper-V y Azure, o entre sitios del proveedor de servicios. Tenga en cuenta que si se replica entre sitios del proveedor de servicios, la integración de runbooks de Azure no estará disponible.


## Pasos siguientes

- Lea la [Información general sobre Site Recovery](site-recovery-overview.md)
- Obtenga información acerca de la [Arquitectura de Site Recovery](site-recovery-components.md)  

<!---HONumber=AcomDC_0309_2016-->