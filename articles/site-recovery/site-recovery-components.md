<properties
	pageTitle="¿Cómo funciona Azure Site Recovery? | Microsoft Azure"
	description="Este artículo proporciona información general sobre la arquitectura de Site Recovery"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/16/2016"
	ms.author="raynew"/>

# ¿Cómo funciona Azure Site Recovery?

Este artículo describe la arquitectura subyacente de Site Recovery y los componentes que hacen que funcione. Después de leer este artículo, puede publicar sus preguntas en el [Foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Información general

Las organizaciones necesitan una estrategia de recuperación ante desastres y continuidad del negocio (BCDR) que determine cómo seguirán en funcionamiento y disponibles las aplicaciones, las cargas de trabajo y los datos durante los tiempos de inactividad planeados y no planeados, y cómo recuperar las condiciones de funcionamiento normales lo antes posible. Su estrategia de BCDR se centra en soluciones que mantengan los datos empresariales seguros y recuperables, y las cargas de trabajo disponibles continuamente en caso de desastre.

Site Recovery es un servicio de Azure que contribuye a su estrategia de BCDR mediante la coordinación de la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error al sitio secundario para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal.

Site Recovery se puede usar en diversos escenarios y puede proteger varias cargas de trabajo.

- **Protección de máquinas virtuales de VMware**: para proteger máquinas virtuales de VMware locales, replíquelas en [Azure](site-recovery-vmware-to-azure-classic.md) o un [centro de datos secundario](site-recovery-vmware-to-vmware.md).
- **Protección de máquinas virtuales de Hyper-V**: para proteger máquinas de virtuales de Hyper-V locales en nubes VMM, puede replicarlas en [Azure](site-recovery-vmm-to-azure.md) o un [centro de datos secundario](site-recovery-vmm-to-vmm.md). Puede replicar las máquinas virtuales de Hyper-V que no estén administradas por VMM en [Azure](site-recovery-hyper-v-site-to-azure.md).
- **Protección de servidores físicos en Azure**: para proteger máquinas físicas que se ejecutan en Windows o Linux, puede replicarlas en [Azure](site-recovery-vmware-to-azure-classic.md) o en un [centro de datos secundario](site-recovery-vmware-to-vmware.md).
- **Migración de máquinas virtuales**: puede usar Site Recovery para [migrar máquinas virtuales IaaS de Azure](site-recovery-migrate-azure-to-azure.md) de una región a otra o para [migrar instancias de Windows en AWS](site-recovery-migrate-aws-to-azure.md) a máquinas virtuales IaaS de Azure.

Site Recovery puede replicar la mayoría de las aplicaciones que se ejecutan en estas máquinas virtuales y estos servidores físicos. Puede obtener un resumen completo de las aplicaciones admitidas en [¿Qué cargas de trabajo se pueden proteger con Azure Site Recovery?](site-recovery-workload.md)


## Replicación de máquinas virtuales de VMware locales o servidores físicos en Azure

Si desea proteger máquinas virtuales de VMware o máquinas físicas de Windows/Linux replicándolas a Azure, esto es lo que necesitará.

Actualmente hay dos arquitecturas diferentes disponibles para este escenario de replicación:

- **Arquitectura heredada**: esta arquitectura no se debe usar para nuevas implementaciones. 
- **Arquitectura mejorada**: es la solución más reciente y se debe usar para todas las implementaciones nuevas. También puede [migrar su arquitectura heredada](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) a esta nueva solución.

Esta es la arquitectura para la implementación mejorada:

![Mejorada](./media/site-recovery-components/arch-enhanced.png)

- **Local**: cuando se implementa la arquitectura mejorada, no es necesario implementar máquinas virtuales de infraestructura en Azure. Además, se cifra todo el tráfico y las comunicaciones de administración de replicación se envían a través de HTTPS 443. Esto es lo que necesita en su infraestructura local:

	- **Servidor de administración**: un único servidor de administración donde se ejecutan todos los componentes de Site Recovery, entre los que se incluyen:

		- **Servidor de configuración**: para coordinar la comunicación entre el entorno local y Azure, además de administrar los procesos de replicación y recuperación de datos.
		- **Servidor de procesos**: actúa como puerta de enlace de replicación. Recibe datos provenientes de máquinas de origen, los optimiza con almacenamiento en caché, compresión y cifrado y envía los datos de replicación al almacenamiento de Azure. También controla la instalación de inserción del servicio de Movilidad en máquinas protegidas y realiza al detección automática de máquinas virtuales de VMware. A medida que crezca la implementación, puede agregar más servidores dedicados que se ejecuten como servidores de procesos solo para administrar mayores volúmenes de tráfico de replicación.
		- **Servidor de destino maestro**: controla los datos de replicación durante la conmutación por recuperación desde Azure. 

	- **Host VMware ESX/ESXi y servidor vCenter**: uno o más servidores host ESX/ESXi donde se encuentran las máquinas virtuales de VMware. Se recomienda tener un servidor vCenter para administrar esos hosts. Tenga en cuenta que, incluso si protege servidores físicos, necesitará un entorno de VMware para realizar la conmutación por recuperación de Azure a su sitio local.
	
	- **Máquinas protegidas**: cada máquina que desee replicar en Azure deberá tener instalado el componente Mobility Service. Captura las escrituras de datos en la máquina y los reenvía al servidor de procesos. Este componente puede instalarse manualmente o insertarse e instalarse de forma automática mediante el servidor de procesos cuando se habilita la protección para una máquina.

- **Azure**: esto es lo que necesita en su infraestructura de Azure:
	- **Cuenta de Azure**: necesitará una cuenta de Microsoft Azure.
	- **Almacenamiento de Azure**: necesitará una cuenta de Almacenamiento de Azure para almacenar los datos replicados. Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error. 
	- **Red de Azure**: necesitará una red virtual de Azure a la que se conectarán las máquinas virtuales de Azure cuando se produzca la conmutación por error. También necesitará configurar una conexión VPN (o Azure ExpressRoute) entre la red de Azure y el sitio local.

	![Mejorada](./media/site-recovery-components/arch-enhanced2.png)

Más información sobre los [requisitos de implementación](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) exactos.

### Arquitectura de conmutación por recuperación

- La conmutación por recuperación desde Azure debe hacerse en máquinas virtuales de VMware. No se puede conmutar por recuperación a un servidor físico.
- Para realizar la conmutación por recuperación, necesitará una conexión VPN (o Azure ExpressRoute) entre la red de Azure y la red local.
- Necesitará un servidor de procesos de Azure para la conmutación por recuperación. Puede eliminarlo una vez finalizada la conmutación por recuperación.
- Necesitará un servidor de destino maestro local. El servidor de administración instala uno de forma predeterminada cuando lo instala localmente. Sin embargo, para grandes volúmenes de tráfico, se recomienda configurar un servidor de destino maestro local independiente para la conmutación por recuperación.

![Conmutación tras recuperación mejorada](./media/site-recovery-components/enhanced-failback.png)

Más información acerca de la [conmutación por recuperación](site-recovery-failback-azure-to-vmware-classic.md).

### Arquitectura heredada

En la arquitectura heredada, es necesario que se instalen en las máquinas que se vayan a proteger un servidor de configuración local y un servidor de procesos, hosts VMware ESX/ESXi y un servidor vCenter, así como el componente Mobility Service. En Azure, configure las máquinas virtuales de Azure para el servidor de configuración y el servidor de destino maestro. También necesitará una suscripción de Azure, una cuenta de almacenamiento y una red virtual.



## Replicación de máquinas virtuales de Hyper-V de nubes VMM en Azure

Si estas máquinas virtuales se encuentran en un host Hyper-V que se administra en una nube de System Center VMM, necesitará lo siguiente para poder replicarlas en Azure.

- Local: 
	- **Servidor VMM**: como mínimo, un servidor VMM configurado con al menos una nube privada VMM. El servidor se debe estar ejecutando en System Center 2012 R2. El servidor VMM debe tener conectividad a Internet. Si desea asegurarse de que las máquinas virtuales de Azure estén conectadas a una red después de la conmutación por error, debe configurar la asignación de red. Para hacerlo, necesita conectar las máquinas virtuales de origen a una red de máquinas virtuales VMM. Esa red debe estar vinculada a una red lógica asociada con la nube.
	- **Servidor Hyper-V**: al menos un servidor host de Hyper-V ubicado en la nube VMM. Los hosts de Hyper-V se deben ejecutar en Windows Server 2012 R2.
	- **Máquinas protegidas**: el servidor host de Hyper-V de origen debe tener como mínimo una máquina virtual que desee proteger.
	
- Azure:
	- **Cuenta de Azure**: necesitará una cuenta de Microsoft Azure.
	- **Almacenamiento de Azure**: necesitará una cuenta de Almacenamiento de Azure para almacenar los datos replicados. Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error.
	- **Red de Azure**: si desea asegurarse de que las máquinas virtuales de Azure estén conectadas a redes después de la conmutación por error, debe configurar la asignación de red. Para hacerlo, necesitará que haya una red de Azure configurada.

	![VMM a Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

En este escenario, el proveedor de Azure Site Recovery se instala en el servidor VMM durante la implementación de Site Recovery. Coordina y organiza la replicación con el servicio Site Recovery a través de Internet. El agente de Servicios de recuperación de Azure se instala durante la implementación de Site Recovery en el servidor host de Hyper-V y los datos se replican entre él y el Almacenamiento de Azure a través de HTTPS 443. Las comunicaciones del proveedor y el agente son seguras y cifradas. También se cifran los datos replicados en el almacenamiento de Azure.

Más información sobre los [requisitos de implementación](site-recovery-vmm-to-azure.md#before-you-start) exactos.

## Replicación de máquinas virtuales de Hyper-V en Azure (sin VMM)

Si las máquinas virtuales no están administradas por un servidor de System Center VMM, esto es lo que debe hacer para replicarlas en Azure

- Local: 
	- **Servidor Hyper-V**: al menos un servidor host Hyper-V. Los hosts de Hyper-V se deben ejecutar en Windows Server 2012 R2.
	- **Máquinas protegidas**: el servidor host de Hyper-V de origen debe tener como mínimo una máquina virtual que desee proteger.
	
- Azure:
	- **Cuenta de Azure**: necesitará una cuenta de Microsoft Azure.
	- **Almacenamiento de Azure**: necesitará una cuenta de Almacenamiento de Azure para almacenar los datos replicados. Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error.

	![Sitio de Hyper-V a Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

En este escenario, el proveedor de Azure Site Recovery y el agente de Servicios de recuperación de Azure se instalan en el servidor VMM durante la implementación de Site Recovery. El proveedor coordina y organiza la replicación con el servicio Site Recovery a través de Internet. El agente se encarga de la replicación de datos a través de HTTPS 443. Las comunicaciones del proveedor y el agente son seguras y cifradas. También se cifran los datos replicados en el almacenamiento de Azure.

Más información sobre los [requisitos de implementación](site-recovery-hyper-v-site-to-azure.md#before-you-start) exactos.

## Replicación de máquinas virtuales de Hyper-V en un centro de datos secundario

Si desea replicar las máquinas virtuales de Hyper-V en un centro de datos secundario para protegerlas, necesitará lo siguiente. Tenga en cuenta que solo puede hacerlo si el servidor host Hyper-V se administra en una nube de System Center VMM.

- **Local**: 
	- **Servidor VMM**: se recomienda un servidor VMM en el sitio principal y uno en el sitio secundario; cada uno debe contener al menos una nube privada VMM. El servidor se debe ejecutar como mínimo en System Center 2012 SP1 con las últimas actualizaciones y estar conectado a Internet. Las nubes deben tener establecido el perfil de funcionalidad de Hyper-V.
	- **Servidor de Hyper-V**: servidores host de Hyper-V ubicados en las nubes VMM principales y secundarias. Los servidores host se deben ejecutar como mínimo en Windows Server 2012 con las últimas actualizaciones instaladas y estar conectados a Internet.
	- **Máquinas protegidas**: el servidor host de Hyper-V de origen debe tener como mínimo una máquina virtual que desee proteger.
	
- **Azure**: necesitará una suscripción de Azure.

	![De local a local](./media/site-recovery-components/arch-onprem-onprem.png)

En este escenario, el proveedor de Azure Site Recovery se instala durante la implementación de Site Recovery en el servidor VMM. Coordina y organiza la replicación con el servicio Site Recovery a través de Internet. Los datos se replican entre los servidores host de Hyper-V principales y secundarios a través de la LAN o VPN mediante autenticación Kerberos o de certificado. Las comunicaciones tanto del proveedor como entre los servidores host Hyper-V son seguras y cifradas.

Más información sobre los [requisitos de implementación](site-recovery-vmm-to-vmm.md#before-you-start) exactos.



## Replicación de máquinas virtuales de Hyper-V en un centro de datos secundario con replicación SAN

Si las máquinas virtuales se encuentran en un host Hyper-V que se administra en una nube de System Center VMM y usa almacenamiento SAN, necesitará lo siguiente para replicar entre dos centros de datos.

- **Local**: 
	- **Matriz SAN**: una [matriz SAN admitida](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) administrada por el servidor VMM principal. La SAN comparte infraestructura de red con otra matriz de SAN en el sitio secundario.
	- **Servidor VMM**: se recomienda un servidor VMM en el sitio principal y uno en el sitio secundario; cada uno debe contener al menos una nube privada VMM. El servidor se debe ejecutar como mínimo en System Center 2012 SP1 con las últimas actualizaciones y estar conectado a Internet. Las nubes deben tener establecido el perfil de funcionalidad de Hyper-V.
	- **Servidor de Hyper-V**: servidores host de Hyper-V ubicados en las nubes VMM principales y secundarias. Los servidores host se deben ejecutar como mínimo en Windows Server 2012 con las últimas actualizaciones instaladas y estar conectados a Internet.
	- **Máquinas protegidas**: el servidor host de Hyper-V de origen debe tener como mínimo una máquina virtual que desee proteger.
	
- **Azure**: necesitará una suscripción de Azure.

	![Replicación de SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

En este escenario, el proveedor de Azure Site Recovery se instala durante la implementación de Site Recovery en el servidor VMM. Coordina y organiza la replicación con el servicio Site Recovery a través de Internet. Los datos se replican entre las matrices de almacenamiento principal y secundaria mediante replicación SAN sincrónica.

Más información sobre los [requisitos de implementación](site-recovery-vmm-san.md#before-you-start) exactos.


## Replicación de máquinas virtuales de VMware o servidores físicos en un sitio secundario

Si para proteger máquinas virtuales de VMware o máquinas físicas de Windows/Linux desea replicarlas entre dos centros de datos locales, esto es lo que necesita.

- **Centro de datos principal local**: 
	- **Servidor de proceso**: configure el componente de servidor de proceso en el sitio principal para administrar el almacenamiento en caché, la compresión y la optimización de los datos. También controla la instalación por inserción del agente unificada en las máquinas que desea proteger.
	- **VMware ESX/ESXi y servidor vCenter**: si va a proteger máquinas virtuales de VMware, necesitará un hipervisor VMware EXS/ESXi o un servidor VMware vCenter que administre varios hipervisores.
	- Máquinas protegidas: las máquinas virtuales de VMware o los servidores físicos Windows o Linux que desee proteger tendrán instalado el agente unificado. También se instala el agente unificado en las máquinas que actúan de servidor de destino maestro. Actúa como un proveedor de comunicación entre los componentes de InMage.
	
- **Centro de datos secundario local**:
	- **Servidor de configuración**: el servidor de configuración es el primer componente que se instala, y se instala en el sitio secundario para administrar, configurar y supervisar la implementación, ya sea mediante el sitio web de administración o la consola de vContinuum. El servidor de configuración también incluye el mecanismo de inserción para una implementación remota de Unified Agent. Solo hay un servidor de configuración en una implementación y debe instalarse en una máquina que se ejecute en Windows Server 2012 R2.
	- **Servidor vContinuum**: se instala en la misma ubicación (sitio secundario) que el servidor de configuración. Proporciona una consola para administrar y supervisar su entorno protegido. En una instalación predeterminada, el servidor vContinuum es el primer servidor de destino maestro y tiene instalado Unified Agent.
	- **Servidor de destino maestro**: el servidor de destino maestro contiene los datos replicados. Recibe los datos del servidor de proceso, crea una máquina de réplica en el sitio secundario y contiene los puntos de retención de datos. El número de servidores de destino maestros que necesita depende del número de equipos que va a proteger. Si desea realizar una conmutación por recuperación al sitio principal, necesitará un servidor de destino maestro allí también. 

- **Azure**: necesitará una suscripción de Azure. Descargue InMage Scout para configurar la implementación después de crear un almacén de Site Recovery. Instale también la actualización más reciente para todos los servidores del componente InMage.


	![VMware a VMware](./media/site-recovery-components/vmware-to-vmware.png)

En este escenario, los cambios diferenciales de la replicación se envían desde Unified Agent que se ejecuta en el equipo protegido al servidor de proceso. El servidor de proceso optimiza estos datos y loso transfiere al servidor de destino maestro en el sitio secundario. El servidor de configuración administra el proceso de replicación.


## Ciclo de vida de protección de Hyper-V

Este flujo de trabajo muestra el proceso de protección, replicación y conmutación por error de máquinas virtuales Hyper-V.

1. **Habilitar la protección**: se configura el almacén de Site Recovery, se configuran las opciones de replicación para una nube de VMM o un sitio de Hyper-V y se habilita la protección de las máquinas virtuales. Se inicia un trabajo llamado **Habilitar la protección** y se puede supervisar en la pestaña **Trabajos**. El trabajo comprueba que el equipo cumple los requisitos previos y después se invoca el método [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) donde se configura la replicación en Azure con la configuración establecida. El trabajo **Habilitar la protección** también invoca el método [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) para inicializar una replicación completa de la máquina virtual.
2. **Replicación inicial**: se toma una instantánea de la máquina virtual y se replican los discos duros virtuales uno por uno hasta que todos se copian en Azure o en el centro de datos secundario. El tiempo para completar esta tarea depende del ancho de banda de red, del tamaño y del método de replicación inicial elegido. Si se producen cambios en el disco mientras la replicación inicial está en curso, el seguimiento de replicaciones de Réplica de Hyper-V realiza un seguimiento de esos cambios en registros de replicación de Hyper-V (.hrl) que se encuentran en la misma carpeta que los discos. Cada disco tiene un archivo .hrl asociado que se enviará al almacenamiento secundario. Tenga en cuenta que los archivos de instantáneas y de registro consumen recursos de disco mientras la replicación inicial está en curso. Cuando la replicación inicial finaliza, se elimina la instantánea de la máquina virtual y se sincronizan y se combinan los cambios diferenciales del disco en el registro.
3. **Finalizar la protección**: una vez finalizada la replicación inicial, el trabajo **Finalizar la protección** configura la red y otras opciones posteriores a la replicación, y la máquina virtual se protege. Si va a replicar en Azure, debe ajustar la configuración de la máquina virtual para que esté preparada para conmutación por error. En este momento puede ejecutar una conmutación por error de prueba para comprobar que todo funciona según lo esperado.
4. **Replicación**: después de la replicación inicial, se produce una sincronización diferencial con el método y la configuración de replicación. 
	- **Error de replicación**: si se produce un error en la replicación diferencial y una réplica completa sería costosa en términos de ancho de banda o tiempo, se produce una resincronización. Por ejemplo, si los archivos .hrl alcanzan el 50% del tamaño del disco, la máquina virtual se marcará para resincronización. La resincronización minimiza la cantidad de datos que se envían; para ello, calcula las sumas de comprobación de las máquinas virtuales de origen y destino y envía solo los datos diferenciales. Una vez finalizada la resincronización, se debe reanudar la replicación diferencial. De forma predeterminada, la resincronización está programada para ejecutarse automáticamente fuera del horario de oficina, pero puede resincronizar una máquina virtual manualmente.
	- **Error de replicación**: si se produce un error de replicación, se realiza un reintento de forma predefinida. Si el error es irrecuperable, como un error de autenticación o de autorización, o una máquina de réplica tiene un estado no válido, no se realizará ningún reintento. Si se produce un error irrecuperable, como un error de red o espacio en disco o memoria insuficiente, se producirán reintentos con un intervalo cada vez mayor entre cada reintento (1, 2, 4, 8, 10 y después cada 30 minutos).
4. **Conmutaciones por error planeadas y no planeadas**: las conmutaciones por error planeadas o no planeadas se ejecutan cuando es necesario. Si ejecuta una conmutación por error planeada, asegúrese de que las máquinas virtuales de origen están apagadas para que no haya pérdida de datos. Una vez creadas las máquinas virtuales de réplica, están en estado pendiente de confirmación. Deberá confirmarlas para completar la conmutación por error a menos que vaya a replicar con SAN, en cuyo caso la confirmación es automática. Una vez configurado y en marcha el sitio primario, puede producirse la conmutación por recuperación. Si replicó en Azure, la replicación inversa es automática. De lo contrario, inicie una replicación inversa.
 

![flujo de trabajo](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## Pasos siguientes

[Prepárese para la implementación](site-recovery-best-practices.md).

<!---HONumber=AcomDC_0218_2016-->