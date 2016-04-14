<properties
	pageTitle="Alta disponibilidad y recuperación ante desastres para SQL Server | Microsoft Azure"
	description="Un análisis de los diversos tipos de estrategias HADR de SQL Server en ejecución en máquinas virtuales de Azure."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="01/22/2016"
	ms.author="jroth" />

# Alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure

## Información general

Las máquinas virtuales de Microsoft Azure con SQL Server pueden ayudar a reducir el costo de una solución de base de datos de alta disponibilidad y recuperación ante desastres (HADR). La mayoría de las soluciones HADR de SQL Server son compatibles con las máquinas virtuales de Azure, bien como soluciones exclusivas de Azure o híbridas. En una solución exclusiva de Azure, todo el sistema HADR se ejecuta en Azure. En una configuración híbrida, una parte de la solución se ejecuta en Azure y la otra parte se ejecuta localmente en su organización. La flexibilidad del entorno Azure permite migrar total o parcialmente a Azure a fin de satisfacer los requisitos de presupuesto y HADR de sus sistemas de bases de datos de SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


## Descripción de la necesidad de una solución HADR

Depende de usted el garantizar que su sistema de base de datos cuenta con las capacidades HADR que exige el contrato de nivel de servicio (SLA). El hecho de que Azure proporcione mecanismos de alta disponibilidad, como la recuperación del servicio en los servicios en la nube y la detección de errores para las máquinas virtuales, no garantiza por sí solo que pueda cumplir con el SLA. Estos mecanismos protegen la alta disponibilidad de las máquinas virtuales, pero no la alta disponibilidad del SQL Server que se ejecutan en ellas. Es posible que la instancia de SQL Server no funcione a pesar de que la máquina virtual esté en línea y en buen estado. Además, incluso con los mecanismos de alta disponibilidad proporcionados por Azure, es posible que se produzcan tiempos de inactividad de las máquinas virtuales debidos a eventos como la recuperación errores de software o hardware o las actualizaciones del sistema operativo.

Además, el almacenamiento con redundancia geográfica (GRS) de Azure, que se implementa con una característica llamada replicación geográfica, podría no ser una solución para recuperación ante desastres adecuada para sus bases de datos. Dado que la replicación geográfica envía los datos de forma asincrónica, las actualizaciones recientes se pueden perder en caso de desastre. En la sección [La replicación geográfica no se admite para los archivos de datos y de registro en discos independientes](#geo-replication-support) se ofrece más información relativa a las limitaciones de la replicación geográfica.

## Arquitecturas de implementación HADR

Entre las tecnologías HADR de SQL Server compatibles con Azure se incluyen:

- [Grupos de disponibilidad AlwaysOn](https://technet.microsoft.com/library/hh510230.aspx)
- [Creación de reflejo de la base de datos](https://technet.microsoft.com/library/ms189852.aspx)
- [Trasvase de registros](https://technet.microsoft.com/library/ms187103.aspx)
- [Copia de seguridad y restauración con el servicio Almacenamiento de blobs de Azure](https://msdn.microsoft.com/library/jj919148.aspx)
- [Instancias de clúster de conmutación por error AlwaysOn](https://technet.microsoft.com/library/ms189134.aspx)

Es posible combinar las tecnologías para implementar una solución SQL Server que posea alta disponibilidad y, al mismo tiempo, capacidades de recuperación ante desastres. Según la tecnología que se use, una implementación híbrida puede requerir un túnel VPN con la red virtual de Azure. Las secciones siguientes muestran algunas de las arquitecturas de implementación de ejemplo.

## Exclusiva de Azure: soluciones de alta disponibilidad

Puede tener una solución de alta disponibilidad para sus bases de datos de SQL Server en Azure con grupos de disponibilidad AlwaysOn o creación de reflejo de la base de datos.

|Technology|Arquitecturas de ejemplo|
|---|---|
|**Grupos de disponibilidad AlwaysOn**|Todas las réplicas de disponibilidad que se ejecutan en máquinas virtuales de Azure para lograr alta disponibilidad en la misma región. Debe configurar una máquina virtual de controlador de dominio, porque los clústeres de conmutación por error de Windows Server (WSFC) requieren un dominio de Active Directory.<br/> ![Grupos de disponibilidad AlwaysOn](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_ha_always_on.gif)<br/>Para obtener más información, consulte [Configuración de los grupos de disponibilidad AlwaysOn en Azure (GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md).|
|**Creación de reflejo de la base de datos**|Los servidores principal, de reflejo y testigo se ejecutan todos en el mismo centro de datos de Azure para lograr una alta disponibilidad. Puede realizar la implementación con un controlador de dominio.<br/>![Creación de reflejo de la base de datos](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_ha_dbmirroring1.gif)<br/>También puede implementar la misma configuración de creación de reflejo de la base de datos sin un controlador de dominio con certificados de servidor.<br/>![Creación de reflejo de la base de datos](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_ha_dbmirroring2.gif)|
|**Instancias de clúster de conmutación por error AlwaysOn**|Las instancias de clúster de conmutación por error (FCI), que requieren almacenamiento compartido, se pueden crear de dos maneras distintas.<br/><br/>1. Una FCI en un WSFC de dos nodos que se ejecuta en máquinas virtuales de Azure con almacenamiento con el respaldo de una solución de clústeres de terceros. Si desea obtener un ejemplo específico con SIOS DataKeeper, consulte [High availability for a file share using WSFC and 3rd party software SIOS Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/) (Alta disponibilidad de un recurso compartido de archivos con WSFC y SIOS Datakeeper de software de terceros).<br/><br/>2. Una FCI en un WSFC de dos nodos que se ejecuta en máquinas virtuales de Azure con almacenamiento en bloque compartido de destino iSCSI remoto a través de ExpressRoute. Por ejemplo, NetApp Private Storage (NPS) expone un destino iSCSI a través de ExpressRoute con Equinix a las máquinas virtuales de Azure.<br/><br/>En el caso de las soluciones de replicación de datos y almacenamiento compartido de terceros, deberá ponerse en contacto con el proveedor en caso de tener problemas relacionados con el acceso a los datos en la conmutación por error.<br/><br/>Tenga en cuenta que todavía no se admite el uso de FCI basado en el [Almacenamiento de archivos de Azure](https://azure.microsoft.com/services/storage/files/), porque esta solución no utiliza Almacenamiento premium. Trabajamos para que pronto sea compatible.|

## Exclusiva de Azure: soluciones de recuperación ante desastres

Puede tener una solución de recuperación ante desastres para las bases de datos de SQL Server en Azure con grupos de disponibilidad AlwaysOn, reflejos de bases de datos o copias de seguridad y restauración con blobs de almacenamiento.

|Technology|Arquitecturas de ejemplo|
|---|---|
|**Grupos de disponibilidad AlwaysOn**|Réplicas de disponibilidad que se ejecutan en varios centros de datos en máquinas virtuales de Azure para la recuperación ante desastres. Esta solución entre regiones protege frente a interrupciones en todo el sitio. <br/> ![Grupos de disponibilidad AlwaysOn](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_dr_alwayson.png)<br/>Dentro de una región, todas las réplicas deben estar en el mismo servicio en la nube y la misma red virtual. Dado que cada región tendrá una red virtual distinta, estas soluciones precisan de conectividad entre estas redes. Para obtener más información, consulte [Configurar una VPN de sitio a sitio en el Portal de Azure clásico](../vpn-gateway/vpn-gateway-site-to-site-create.md).|
|**Creación de reflejo de la base de datos**|Los servidores principal y de reflejo se ejecutan en distintos centros de datos para la recuperación ante desastres. Debe realizar la implementación con certificados de servidor porque un dominio de Active Directory no puede abarcar varios centros de datos.<br/>![Creación de reflejo de la base de datos](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_dr_dbmirroring.gif)|
|**Copia de seguridad y restauración con el servicio Almacenamiento de blobs de Azure**|Las bases de datos de producción de las que se realizó una copia de seguridad directamente en el almacenamiento de blobs en otro centro de datos para la recuperación ante desastres.<br/>![Copia de seguridad y restauración](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/azure_only_dr_backup_restore.gif)<br/>Para obtener más información, consulte [Copias de seguridad y restauración para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-backup-and-restore.md).|

## TI híbrida: soluciones de recuperación ante desastres

Puede tener una solución de recuperación ante desastres para las bases de datos de SQL Server en un entorno de TI híbrida con grupos de disponibilidad AlwaysOn, creación de reflejo de la base de datos, trasvase de registros y copias de seguridad y restauración con el almacenamiento de blobs de Azure.

|Technology|Arquitecturas de ejemplo|
|---|---|
|**Grupos de disponibilidad AlwaysOn**|Algunas réplicas de disponibilidad se ejecutan en máquinas virtuales de Azure y réplicas se ejecutan localmente para la recuperación ante desastres a través de sitios. El sitio de producción puede ser local o encontrarse en un centro de datos de Azure.<br/>![Grupos de disponibilidad AlwaysOn](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_alwayson.gif)<br/>Dado que todas las réplicas de disponibilidad deben estar en el mismo clúster WSFC, este debe abarcar ambas redes (un clúster WSFC de varias subredes). Esta configuración requiere una conexión VPN entre Azure y la red local.<br/><br/>Para una recuperación ante desastres correcta de las bases de datos, también debe instalar un controlador de dominio de réplica en el sitio de recuperación ante desastres.<br/><br/>Es posible usar el Asistente para agregar una réplica en SSMS para agregar una réplica de Azure a un grupo de disponibilidad AlwaysOn existente. Para obtener más información, consulte Tutorial: Ampliación del grupo de disponibilidad AlwaysOn a Azure.|
|**Creación de reflejo de la base de datos**|Un asociado se ejecuta en una máquina virtual de Azure y otro localmente para la recuperación ante desastres a través de sitios con certificados de servidor. Los asociados no necesitan estar en el mismo dominio de Active Directory y no se requiere ninguna conexión VPN.<br/>![Creación de reflejo de la base de datos](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_dbmirroring.gif)<br/>Otro escenario de creación de reflejo de la base de datos implica a un asociado que se ejecuta en una máquina virtual de Azure y los demás que se ejecutan localmente en el mismo dominio de Active Directory para la recuperación ante desastres entre sitios. Se requiere una [conexión VPN entre la red virtual y la red local](../vpn-gateway/vpn-gateway-site-to-site-create.md).<br/><br/>Para que se produzca una recuperación ante desastres correcta de las bases de datos, también debe instalar un controlador de dominio de réplica en el sitio de recuperación ante desastres.|
|**Trasvase de registros**|Un servidor se ejecuta en una máquina virtual de Azure y otro localmente para la recuperación ante desastres a través de sitios. El trasvase de registros depende del uso compartido de archivos de Windows, de modo que se requiere una conexión VPN entre la red virtual de Azure y la red local.<br/>![Trasvase de registros](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_log_shipping.gif)<br/>Para que se produzca una recuperación ante desastres correcta de las bases de datos, también debe instalar un controlador de dominio de réplica en el sitio de recuperación ante desastres.|
|**Copia de seguridad y restauración con el servicio Almacenamiento de blobs de Azure**|Bases de datos de producción locales con copia de seguridad directa en el almacenamiento de blobs de Azure para la recuperación ante desastres.<br/>![Copia de seguridad y restauración](./media/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions/hybrid_dr_backup_restore.gif)<br/>Para obtener más información, consulte [Copias de seguridad y restauración para SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-backup-and-restore.md).|

## Consideraciones importantes para HADR de SQL Server en Azure

Las máquinas virtuales de Azure, el almacenamiento y la conexión de red tienen características operativas diferentes de las de una infraestructura TI local y no virtualizada. Para implementar correctamente una solución HADR de SQL Server en Azure es necesario conocer estas diferencias y diseñar la solución adaptada a ellas.

### Nodos de alta disponibilidad en un conjunto de disponibilidad

Los conjuntos de disponibilidad de Azure permiten colocar los nodos de alta disponibilidad en dominios de error y dominios de actualización independientes. Para que las máquinas virtuales de Azure se coloquen en el mismo conjunto de disponibilidad, debe implementarlas en el mismo servicio en la nube. Tenga en cuenta que solo las máquinas virtuales del mismo servicio en la nube puede participar en el mismo conjunto de disponibilidad. Para obtener más información, consulte [Administración de la disponibilidad de las máquinas virtuales](virtual-machines-manage-availability.md).

### Comportamiento de clúster WSFC en redes de Azure

El servicio DHCP no compatible con RFC en Azure puede hacer que la creación de determinadas configuraciones de clúster WSFC produzcan errores al asignarle una dirección IP duplicada al nombre de red del clúster como, por ejemplo, la misma dirección IP que uno de los nodos de clúster. Se trata de un problema al implementar grupos de disponibilidad AlwaysOn que depende de la característica WSFC.

Considere un escenario en el que se crea un clúster de dos nodos y se pone en conexión:

1. El clúster se conecta y NODE1 solicita una dirección IP asignada dinámicamente para el nombre de red en clúster.

1. El servicio DHCP no otorga ninguna dirección IP a excepción de la propia de NODE1, ya que el servicio DHCP reconoce que la solicitud procede del propio NODE1.

1. Windows detecta que se asigna una dirección duplicada tanto a NODE1 como al nombre de red en clúster y el grupo de clústeres predeterminado no puede poner en línea.

1. El grupo de clústeres predeterminado se pasa a NODE2, que trata la dirección IP de NODE1 como la dirección IP del clúster y pone en línea el grupo de clústeres predeterminado.

1. Cuando NODE2 intenta establecer conectividad con NODE1, los paquetes dirigidos a NODE1 nunca abandonan NODE2 porque resuelve la dirección IP de NODE1 en sí mismo. NODE2 no puede establecer conectividad con NODE1, pierde el cuórum y cierra el clúster.

1. Mientras tanto, NODE1 puede enviar paquetes a NODE2 pero NODE2 no puede responder. Node1 pierde el cuórum y cierra el clúster.

Puede evitar esta situación asignando una dirección IP estática no usada, por ejemplo una dirección IP de vínculo local como 169.254.1.1, al nombre de red del clúster a fin de poner dicho nombre de red en línea. Para simplificar este proceso, consulte [Configuración del clúster de conmutación por error de Windows en Azure para grupos de disponibilidad AlwaysOn](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Para obtener más información, consulte [Configuración de los grupos de disponibilidad AlwaysOn en Azure (GUI)](virtual-machines-sql-server-alwayson-availability-groups-gui.md).

### Compatibilidad del agente de escucha del grupo de disponibilidad

Los agentes de escucha del grupo de disponibilidad son compatibles con máquinas virtuales de Azure que ejecutan Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2. Esta compatibilidad es posible gracias al uso de puntos de conexión de carga equilibrada habilitados en las máquinas virtuales de Azure y que son nodos del grupo de disponibilidad. Debe seguir pasos de configuración especiales para que los agentes de escucha funcionen tanto con las aplicaciones de cliente que se ejecutan en Azure como con las que se ejecutan localmente.

Existen dos opciones principales para configurar el agente de escucha: externa (público) o interna. El agente de escucha externo (público) está asociado a una IP virtual pública (VIP) que es accesible a través de internet. Si usa un agente de escucha externo, debe habilitar Direct Server Return, lo que significa que debe conectarse al agente de escucha desde un equipo que no esté en el mismo servicio en la nube que los nodos de grupo de disponibilidad AlwaysOn. La otra opción, es un agente de escucha interno que utilice un Equilibrador de carga interno (ILB). Un agente de escucha interno sólo admite clientes dentro de la misma red virtual.

Si el grupo de disponibilidad abarca varias subredes de Azure (como por ejemplo, una implementación que comprenda varias regiones de Azure), la cadena de conexión de cliente debe incluir "**MultisubnetFailover = True**". Esto se traduce en intentos de conexión en paralelo a las réplicas de las diferentes subredes. Para obtener instrucciones sobre cómo configurar un agente de escucha, consulte

- [Configurar un agente de escucha con ILB para grupos de disponibilidad AlwaysOn en Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).
- [Configurar un agente de escucha externo para grupos de disponibilidad AlwaysOn en Azure](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md).

Puede seguir conectándose a cada réplica de disponibilidad por separado conectándose directamente a la instancia del servicio. Además, puesto que los grupos de disponibilidad AlwaysOn son compatibles con las versiones anteriores de los clientes de creación de reflejo de la base de datos, puede conectarse a las réplicas de disponibilidad como asociados de creación de reflejo de la base de datos siempre y cuando las réplicas estén configuradas de forma similar a la creación de reflejo de la base de datos:

- Una réplica principal y una réplica secundaria

- La réplica secundaria está configurada como no legible (la opción **Secundaria legible** está establecida en **No**)

A continuación se muestra una cadena de conexión de cliente de ejemplo correspondiente a esta configuración similar a la creación de reflejo de la base de datos que usa ADO.NET o SQL Server Native Client:

	Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Para obtener más información sobre la conectividad del cliente, consulte:

- [Usar palabras clave de cadena de conexión con SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
- [Conectar clientes a una sesión de creación de reflejo de la base de datos (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
- [Conexión con el agente de escucha del grupo de disponibilidad en TI híbrida ](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
- [Agentes de escucha del grupo de disponibilidad, conectividad de cliente y conmutación por error de una aplicación (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
- [Uso de cadenas de conexión de creación de reflejo de la base de datos con grupos de disponibilidad](https://technet.microsoft.com/library/hh213417.aspx)

### Latencia de red en TI híbrida

Es recomendable implementar la solución HADR partiendo de la suposición de que podría haber períodos de tiempo con una alta latencia de red entre la red local y Azure. Al implementar réplicas en Azure, debe usar la confirmación asincrónica en lugar de la confirmación sincrónica para el modo de sincronización. Al implementar servidores de creación de reflejo de la base de datos tanto en local como en Azure, use el modo de alto rendimiento en lugar del modo de alta seguridad.

### Compatibilidad de la replicación geográfica

La replicación geográfica en discos de Azure no admite que el archivo de datos y el archivo de registro de la misma base de datos se almacenen en discos independientes. La GRS replica los cambios en cada disco independiente y asincrónicamente. Este mecanismo garantiza el orden de escritura en un único disco en la copia con replicación geográfica pero no a través de las copias con replicación geográfica de varios discos. Si configura una base de datos para almacenar su archivo de datos y su archivo de registro en discos independientes, los discos recuperados después de un desastre pueden contener una copia más actualizada del archivo de datos que el archivo de registro, lo que interrumpe el registro de escritura previa en SQL Server y las propiedades ACID de las transacciones. Si no tiene la opción de deshabilitar la replicación geográfica en la cuenta de almacenamiento, debe conservar todos los archivos de datos y de registro de una base de datos dada en el mismo disco. Si debe usar más de un disco debido al tamaño de la base de datos, debe implementar una de las soluciones de recuperación de desastres enumeradas anteriormente para garantizar la redundancia de datos.

## Pasos siguientes

Si necesita crear una máquina virtual de Azure con SQL Server, consulte [Aprovisionamiento de una máquina virtual de SQL Server en Azure](virtual-machines-provision-sql-server.md).

Para obtener el mejor rendimiento de SQL Server en una máquina virtual de Azure, consulte la guía en [Procedimientos recomendados para SQL Server en Máquinas virtuales de Azure](virtual-machines-sql-server-performance-best-practices.md).

Para ver otros temas sobre la ejecución de SQL Server en máquinas virtuales de Azure, consulte [SQL Server en máquinas virtuales de Azure](virtual-machines-sql-server-infrastructure-services.md).

### Otros recursos:

- [Instalación de un nuevo bosque de Active Directory en Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
- [Crear el clúster WSFC para grupos de disponibilidad AlwaysOn en la VM de Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

<!---HONumber=AcomDC_0128_2016-->