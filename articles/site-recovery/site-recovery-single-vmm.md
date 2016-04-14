
<properties
	pageTitle="Azure Site Recovery: replique máquinas virtuales de Hyper-V (servidor VMM único)"
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales ubicadas en nubes de VMM en Azure o en una nube de VMM secundaria."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="12/01/2015"
	ms.author="raynew"/>

#  Azure Site Recovery: replique máquinas virtuales de Hyper-V (servidor VMM único)

El servicio Azure Site Recovery contribuye a una solución sólida de continuidad empresarial y recuperación ante desastres (BCDR) mediante la orquestación y replicación automática de servidores físicos locales y máquinas virtuales en Azure o en un centro de datos local secundario. En este artículo se describe cómo implementar Site Recovery para proteger las máquinas virtuales de Hyper-V que se encuentran en una nube VMM si solo tiene un único servidor VMM en su implementación. Si tiene alguna pregunta después de leer el artículo, publíquela en el [Foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Información general

Puede replicar las máquinas virtuales de Hyper-V de un par de maneras:

- Replicar las máquinas virtuales de Hyper-V que se encuentran en los hosts de Hyper-V que no se encuentran en una nube VMM en Azure
- Replicar las máquinas virtuales de Hyper-V que se encuentran en los hosts de Hyper-V en una nube VMM en Azure
- Replicar las máquinas virtuales de Hyper-V que se encuentran en los hosts de Hyper-V en una nube VMM en Azure

¿Pero qué sucede si desea usar VMM pero solo tiene un único servidor VMM en su infraestructura?

En este caso tiene dos opciones:

- Replicar las máquinas virtuales de Hyper-V de las nubes VMM en Azure. [Obtenga más información](site-recovery-vmm-to-azure.md) acerca de este escenario.
- Replicar entre nubes en el único servidor VMM

Se recomienda que la primera opción, ya que la conmutación por error y la recuperación no es perfecta en la segunda opción y se necesita llevar a cabo un número determinado de pasos manuales.


### Replicar en los sitios con un servidor VMM único e independiente

En este escenario implementará el servidor VMM independiente como una máquina virtual en un sitio principal y replicar esta máquina virtual en un sitio secundario con réplica de Hyper-V y Site Recovery. Para ello, siga estos pasos:

1. Configurar VMM en una máquina virtual de Hyper-V. Tenga en cuenta la colocación de la instancia de SQL Server usada por VMM en la misma máquina virtual. Esto permite ahorrar tiempo, ya que solo se tienen que crear instancias de una máquina virtual. Si desea usar una instancia remota y se produce una interrupción, necesitará recuperar esa instancia para poder recuperar VMM.
2. Asegúrese de que el servidor VMM tenga al menos dos nubes configuradas. Una nube contendrá las máquinas virtuales que desee replicar y la otra nube actuará como la ubicación secundaria. La nube contiene las máquinas virtuales que desea proteger. Debe disponer de uno o más grupos de host VMM con uno o más servidores host de Hyper-V en cada grupo de hosts y por lo menos una máquina virtual Hyper-V en cada servidor host.
2. Cree un almacén de recuperación del sitio, genere y descargue una clave de registro de almacén y registre el servidor VMM en el almacén.
2. Configure una o más nubes en la máquina virtual de VMM y agregue los hosts de Hyper-V que contienen máquinas virtuales que desea proteger en estas nubes.
3. En Azure Site Recovery configurar opciones de protección para las nubes. En Ubicación de origen y de destino, especificará el nombre del servidor VMM único. Si configura la asignación de red, se asignará la red de la máquina virtual de la nube que contiene máquinas virtuales que desea proteger a la red de máquina virtual de la nube en la que desea efectuar la replicación.
4. Habilite la replicación de máquinas virtuales que desee proteger mediante **A través de la red** como método de replicación, ya que tanto ambas nubes se encuentran en el mismo servidor.
4. En la consola de administrador de Hyper-V, habilite la réplica de Hyper-V en el host de Hyper-V que contiene la máquina virtual de VMM y habilite la replicación en la máquina virtual. Asegúrese de no agregar la máquina virtual VMM a las nubes que están protegidas por Site Recovery para garantizar que la configuración de la réplica de Hyper-V no la invalide Site Recovery.
5. Si desea crear planes de recuperación, especifique el mismo servidor VMM de origen y de destino. 

Cuando se producen interrupciones, las cargas de trabajo de máquinas virtuales de Hyper-V se recuperan del modo siguiente:

1. Efectuando la conmutación por error manualmente de la réplica de la máquina virtual de VMM en el sitio secundario con el administrador de Hyper-V mediante una conmutación por error planeada.
2. Una vez recuperada la máquina virtual de VMM, puede iniciar sesión en Administrador de recuperación de Hyper-V desde el sitio secundario y realizar una conmutación por error no planeada de las máquinas virtuales desde el sitio primario al sitio secundario.
3. Una vez completada la conmutación por error no planeada, los usuarios pueden acceder a todos los recursos en el sitio principal.

Tenga en cuenta que la máquina virtual de VMM debe conmutar por error manualmente en el sitio secundario antes de que las cargas de trabajo pueden conmutar por error.

![Servidor VMM virtual independiente](./media/site-recovery-single-vmm/single-vmm-standalone.png)

### Replicar en los sitios con un servidor VMM único en un clúster extendido

En lugar de implementar un servidor VMM independiente como una máquina virtual que se replica en un sitio secundario, puede hacer que VMM esté altamente disponible mediante su implementación como una máquina virtual en un clúster de conmutación por error de Windows, proporcionando protección frente a errores de hardware y resistencia de las cargas de trabajo. Para efectuar una implementación con Site Recovery, VMM debe implementarse en un clúster extendido en sitios separados geográficamente. Para ello, siga estos pasos:

1. Instale VMM en una máquina virtual en un clúster de conmutación por error de Windows y seleccione la opción de ejecución del servidor de alta disponibilidad durante la instalación.
2. La instancia de SQL Server usada por VMM se debe replicar con grupos de disponibilidad de SQL Server AlwaysOn para que haya una réplica de la base de datos en el sitio secundario. 

Si se producen interrupciones, el servidor VMM y su Base de datos de SQL Server correspondiente se conmutan por error y se tiene acceso a ellos desde el sitio secundario.

![Servidor VMM virtual en clúster](./media/site-recovery-single-vmm/single-vmm-cluster.png)




 

<!---HONumber=AcomDC_1203_2015-->