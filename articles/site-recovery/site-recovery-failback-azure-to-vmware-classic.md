<properties 
   pageTitle="Conmutación por recuperación de máquinas virtuales de VMware y servidores físicos en el sitio local | Microsoft Azure"
   description="Aprenda a conmutar por recuperación al sitio local después de conmutar por error las máquinas virtuales de VMware y los servidores físicos a Azure." 
   services="site-recovery" 
   documentationCenter="" 
   authors="rayne-wiselman" 
   manager="jwhit" 
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required" 
   ms.date="01/11/2015"
   ms.author="raynew"/>

# Conmutación por recuperación de máquinas virtuales de VMware y servidores físicos al sitio local

> [AZURE.SELECTOR]
- [Enhanced](site-recovery-failback-azure-to-vmware-classic.md)
- [Legacy](site-recovery-failback-azure-to-vmware-classic-legacy.md)


En este artículo se describe cómo conmutar por recuperación máquinas virtuales de Azure al sitio local. Siga las instrucciones que se describen en este artículo cuando esté listo para conmutar por recuperación máquinas virtuales de VMware o servidores físicos de Windows o Linux después de que han conmutado por error desde el sitio local a Azure mediante este [tutorial](site-recovery-vmware-to-azure-classic.md).



## Información general

Este diagrama muestra la arquitectura de conmutación por recuperación en este escenario.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Así es cómo funciona la conmutación por recuperación:

- Después de conmutar por error a Azure, conmuta por recuperación a su sitio local en unas cuantas fases:
	- **Fase 1**: protege de nuevo las máquinas virtuales de Azure de modo que comienzan a replicarse otra vez en las máquinas virtuales de VMware que se ejecutan en el sitio local. Al habilitar la reprotección, la máquina virtual se mueve a un grupo de protección de conmutación por recuperación que se crea automáticamente cuando crea al principio el grupo de protección de conmutación por error. Si agregó el grupo de protección de conmutación por error a un plan de recuperación, el grupo de protección de conmutación por recuperación también se agregó automáticamente al plan. Durante la reprotección, especifica cómo planear la conmutación por recuperación.
	- **Fase 2**: después de que las máquinas virtuales de Azure se están replicando en el sitio local, ejecuta una conmutación por error para conmutar por recuperación desde Azure.
	- **Fase 3**: después de que los datos han conmutado por recuperación, vuelve a proteger las máquinas virtuales locales a las que conmutó por recuperación, así que comienzan a replicarse en Azure.

> [AZURE.VIDEO enhanced-vmware-to-azure-failback]


### Conmutación por recuperación a la ubicación original o alternativa

Si ha conmutado por error una máquina virtual de VMware, puede conmutar por recuperación a la misma máquina virtual de origen si aún existe en el entorno local. En este escenario solo se conmutarán por recuperación los cambios diferenciales. Observe lo siguiente:

- Si ha conmutado por error servidores físicos, la conmutación por recuperación siempre es a una nueva máquina virtual de VMware.
- Si conmuta por recuperación a la máquina virtual original, se necesita lo siguiente:
	- Si la máquina virtual está administrada por un servidor vCenter, el host ESX del destino principal debe tener acceso al almacén de datos de máquinas virtuales.
	- Si la máquina virtual está en un host ESX, pero no está administrada por vCenter, el disco duro de la máquina virtual debe estar en un almacén de datos accesible para el host de MT.
	- Si la máquina virtual está en un host ESX y no usa vCenter, debe completar la detección del host ESX de MT antes de volver a protegerla. Lo mismo se aplica si también conmuta por recuperación a servidores físicos.
	- Otra opción (si existe la máquina virtual local) es eliminarla antes de realizar una conmutación por recuperación. En este caso, la conmutación por recuperación crea una nueva máquina virtual en el mismo host que el host ESX de destino maestro.
	
- Cuando se conmuta por recuperación a una ubicación alternativa, los datos se recuperan en el mismo almacén de datos y el mismo host ESX que los usados por el servidor de destino maestro local.


## Requisitos previos

- Necesitará un entorno de VMware para conmutar por recuperación máquinas virtuales de VMware y servidores físicos. No se admite la conmutación por recuperación a un servidor físico.
- Para realizar la conmutación por recuperación , debe haber creado una red de Azure cuando configuró inicialmente la protección. La conmutación por recuperación necesita una conexión VPN o ExpressRoute desde la red de Azure, en la que se encuentran las máquinas virtuales de Azure, hasta el sitio local.
- Si las máquinas virtuales que desea conmutar por recuperación las administra un servidor vCenter, deberá asegurarse de tener los permisos necesarios para la detección de máquinas virtuales en servidores vCenter. [Más información](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
- Si existen instantáneas en una máquina virtual, la reprotección dará error. Puede eliminar las instantáneas o los discos. 
- Antes de conmutar por recuperación, deberá crear una serie de componentes:
	- **Crear un servidor de procesos de Azure**. Es una máquina virtual de Azure que necesitará crear y mantener en ejecución durante la conmutación por recuperación. Puede eliminar la máquina una vez completada la operación.
	- **Crear un servidor de destino maestro**: el servidor de destino maestro envía y recibe datos de conmutación por recuperación. El servidor de administración que creó de forma local tiene instalado de forma predeterminada un servidor de destino maestro. Sin embargo, según el volumen de tráfico conmutado por recuperación, puede que deba crear un servidor de destino maestro distinto para esta operación.
	- Si desea crear un servidor de destino maestro adicional que se ejecute en Linux, debe configurar la máquina virtual de Linux antes de instalar al servidor de destino maestro, como se describe a continuación.

## Configuración del servidor de procesos en Azure

Para que las máquinas virtuales de Azure puedan devolver los datos a un servidor de destino maestro local, es preciso instalar un servidor de procesos en Azure.

1.  En el portal de Site Recovery > **Servidores de configuración**, seleccione esta opción para agregar un nuevo servidor de procesos.

	![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)

2.  Especifique un nombre de servidor de procesos y escriba un nombre y la contraseña que se utilizará para conectarse a la máquina virtual de Azure como administrador. En **Servidor de configuración**, seleccione el servidor de administración local y especifique la red de Azure en la que se debe implementar el servidor de procesos. Debe ser la red en la que se encuentran las máquinas virtuales de Azure. Especifique una dirección IP única de la subred seleccionada y comience la implementación.

	![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

	Se desencadenará un trabajo para implementar el servidor de procesos.

	![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

	Después de que el servidor de procesos se implemente en Azure, podrá iniciar sesión en él con las credenciales que especificó. La primera vez que inicia sesión, se ejecutará el cuadro de diálogo del servidor de procesos. Escriba la dirección IP del servidor de administración local y su frase de contraseña. Deje la configuración predeterminada del puerto 443. También puede dejar el puerto predeterminado 9443 para la replicación de datos, a no ser que modificara en concreto este valor al configurar el servidor de administración local.

	>[AZURE.NOTE] El servidor no será visible en las **propiedades de la máquina virtual**. Solo aparecerá en la pestaña **Servidores** del servidor de administración en el que se ha registrado. Puede tardar entre 10 y 15 minutos en aparecer el servidor de procesos.


## Configuración del servidor de destino maestro local

El servidor de destino maestro recibe los datos de conmutación por recuperación. Un servidor de destino maestro se instala automáticamente en el servidor de administración local, pero si va a conmutar por recuperación muchos datos puede que tenga que configurar otro adicional. Para ello, realice lo siguiente:

>[AZURE.NOTE] Si desea instalar un servidor de destino maestro en Linux, siga las instrucciones del procedimiento siguiente.

1. Si va a instalar al servidor de destino maestro en Windows, abra la página de inicio rápido de la máquina virtual en la que va a instalarlo y descargue el archivo de instalación para el Asistente de configuración unificada de Azure Site Recovery.
2. Ejecute el programa de instalación y, en **Antes de comenzar**, seleccione **Agregar servidores de procesos adicionales para el escalado horizontal de la implementación**.
3. Complete el asistente tal y como hiciera cuando [configuró el servidor de administración](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). En la página **Detalles del servidor de configuración**, especifique la dirección IP de este servidor de destino maestro y una frase de contraseña para tener acceso a la máquina virtual.

### Configuración de una máquina virtual de Linux como servidor de destino maestro
Para configurar el servidor de administración que ejecuta el servidor de destino maestro como una máquina virtual de Linux, será necesario que instale el sistema operativo mínimo CentOS 6.6, que recupere los id. SCSI de cada disco duro SCSI, que instale algunos paquetes adicionales y que aplique algunos cambios personalizados.

#### Instalación de CentOS 6.6

1.	Instale el sistema operativo mínimo CentOS 6.6 en la máquina virtual del servidor de administración. Mantenga la imagen ISO en una unidad de DVD y arranque el sistema. Omita la comprobación de medios, seleccione inglés de Estados Unidos como el idioma, seleccione **Basic Storage Devices** (Dispositivos de almacenamiento básico), compruebe que el disco duro no tenga ningún dato importante y haga clic en **Yes** (sí) para descartar los datos. Escriba el nombre de host del servidor de administración y seleccione el adaptador de red del servidor. En el cuadro de diálogo **Editing System** (Sistema de edición), seleccione **Connect automatically ** (Conectar automáticamente) y agregue una configuración de dirección IP estática, red y DNS. Especifique una zona horaria y una contraseña raíz para acceder al servidor de administración. 
2.	Cuando se le pregunte por el tipo de instalación que desea, seleccione **Create Custom Layout** (Crear diseño personalizado) como partición. Tras hacer clic en **Next** (Siguiente), seleccione **Free** (Gratis) y haga clic en Create (Crear). Cree **/**, **/var/crash** y **/home partitions** con **FS Type:** (Tipo FS) **ext4**. Cree la partición de intercambio como **FS Type: swap** (Tipo FS: intercambio).
3.	Si se encuentran dispositivos ya existentes, aparecerá un mensaje de advertencia. Haga clic en **Format** (Formato) para formatear la unidad con la configuración de partición. Haga clic en **Write change to disk** (Escribir cambios en el disco) para aplicar los cambios de partición.
4.	Seleccione **Install boot loader** (Instalar cargador de arranque) > **Next** (Siguiente) para instalar el cargador de arranque en la partición raíz.
5.	Cuando la instalación se haya completado, haga clic en **Reboot** (Reiniciar).


#### Recuperación de los id. SCSI

1. Después de la instalación, recupere los id. SCSI de cada disco duro SCSI de la máquina virtual. Para ello, cierre la máquina virtual del servidor de administración y, en las propiedades de la máquina virtual en VMware, haga clic con el botón derecho en la entrada de la máquina virtual > **Edit Settings** (Editar configuración) > **Options** (Opciones).
2. Seleccione **Advanced** (Avanzadas) > **General item** (Elemento general) y haga clic en **Configuration Parameters** (Parámetros de configuración). Esta opción estará desactivada cuando se ejecuta la máquina. Para que se active, la máquina debe estar apagada.
3. Si existe la fila **disk.EnableUUID**, asegúrese de que el valor esté establecido en **True** (Verdadero) (distingue mayúsculas de minúsculas). En este caso, puede cancelar y probar el comando SCSI en un sistema operativo invitado después de arrancar. 
4.	Si la fila no existe, haga clic en **Add Row** (Agregar fila) y agréguela con el valor **True** (Verdadero). No utilice comillas dobles.

#### Instalación de paquetes adicionales

Debe descargar e instalar algunos paquetes adicionales.

1.	Asegúrese de que el servidor de destino maestro esté conectado a Internet.
2.	Ejecute este comando para descargar e instalar 15 paquetes desde el repositorio de CentOS: **# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools**.
3.	Si las máquinas de origen que va a proteger ejecutan Linux con el sistema de archivos Reiser o XFS en el dispositivo raíz o de arranque, debe descargar e instalar paquetes adicionales de la manera siguiente:

	- # # cd /usr/local
	- # wget [http://elrepo.org/linux/elrepo/el6/x86\_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
	- # wget [http://elrepo.org/linux/elrepo/el6/x86\_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
	- # rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86\_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86\_64.rpm
	- # wget [http://mirror.centos.org/centos/6.6/os/x86\_64/Packages/xfsprogs-3.1.1-16.el6.x86\_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
	- # rpm –ivh xfsprogs-3.1.1-16.el6.x86\_64.rpm

#### Aplicación de cambios personalizados

Lleve a cabo los siguientes pasos para aplicar cambios personalizados después de haber completado los pasos posteriores a la instalación y haber instalado los paquetes:

1.	Copie al binario de RHEL 6-64 Unified Agent en la máquina virtual. Ejecute este comando para descomprimir el binario: **tar –zxvf <file name>**
2.	Ejecute este comando para conceder permisos: **# chmod 755 ./ApplyCustomChanges.sh**
3.	Ejecute el script: **# ./ApplyCustomChanges.sh**. Solo debe ejecutar el script una vez. Después de que el script se haya ejecutado correctamente, reinicie el servidor.


## Ejecución de la conmutación por recuperación

### Reprotección de las máquinas virtuales de Azure

1.	En el portal de Site Recovery > pestaña **Máquinas**, seleccione la máquina virtual que se ha conmutado por error y haga clic en **Reproteger**.
2.	En **Servidor de destino maestro** y **Servidor de procesos**, seleccione el servidor de destino maestro local y el servidor de procesos de la máquina virtual de Azure.
3.	Seleccione la cuenta configurada para conectarse a la máquina virtual.
4.	Seleccione la versión de la conmutación por recuperación del grupo de protección. Por ejemplo, si la máquina virtual está protegida en PG1, deberá seleccionar PG1\_Failback.
5.	Si desea recuperar en una ubicación alternativa, seleccione la unidad de retención y el almacén de datos configurado para el servidor de destino maestro. Cuando se conmuta por recuperación al sitio local, las máquinas virtuales de VMware del plan de protección de conmutación por recuperación utilizarán el mismo almacén de datos que el servidor de destino maestro. Si desea recuperar la réplica de la máquina virtual de Azure en la misma máquina virtual local, la máquina virtual local ya debe estar en el mismo almacén de datos que el servidor de destino maestro. Si no hay ninguna máquina virtual local, se creará una nueva durante la reprotección.

	![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)

6.	Tras hacer clic en **Aceptar** para comenzar la reprotección, comienza un trabajo para replicar la máquina virtual de Azure en el sitio local. Puede realizar el seguimiento del progreso en la pestaña **Trabajos**.

	![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### Ejecución de una conmutación por error en el sitio local

Tras la reprotección, la máquina virtual se mueve a la versión de conmutación por recuperación de su grupo de protección y se agrega automáticamente al plan de recuperación usado para la conmutación por error a Azure, si es que existe.

1.	En la página **Planes de recuperación**, seleccione el plan de recuperación que contiene el grupo de conmutación por recuperación y haga clic en **Conmutación por error** > **Conmutación por error no planeada**.
2.	En **Confirmar conmutación por error**, compruebe la dirección de conmutación por error (a Azure) y seleccione el punto de recuperación que desea usar para esta (el último). Si habilitó **Varias máquinas virtuales** al configurar las propiedades de replicación, puede realizar la recuperación al último punto de recuperación coherente con el bloqueo o la aplicación. Haga clic en la marca de verificación para iniciar la conmutación por error.
3.	Durante la conmutación por error, se cerrarán las máquinas virtuales de Azure en Site Recovery. Después de comprobar que la conmutación por recuperación se ha completado como se esperaba, puede comprobar que las máquinas virtuales de Azure se han cerrado según lo previsto.

### Reprotección del sitio local

Después de completarse la conmutación por recuperación, los datos vuelven al sitio local, pero no están protegidos. Para volver a iniciar la replicación en Azure, vuelva a hacer lo siguiente:

1.	En el portal de Site Recovery > pestaña **Máquinas**, seleccione las máquinas virtuales que se ha conmutado por recuperación y haga clic en **Reproteger**. 
2.	Después de comprobar que la replicación en Azure funciona como se esperaba, puede eliminar en Azure las máquinas virtuales de Azure (que no se ejecutan actualmente) que se conmutaron por recuperación.


## Conmutación por recuperación con ExpressRoute

Puede conmutar por recuperación a través de una conexión VPN o de Azure ExpressRoute. Si desea usar ExpressRoute, tenga en cuenta lo siguiente:

- ExpressRoute se debe configurar en la red virtual de Azure a la que conmutarán por error las máquinas de origen, y en la que se encuentran las máquinas virtuales de Azure después de que tiene lugar este proceso.
- Los datos se replican en una cuenta de almacenamiento de Azure en un punto de conexión público. Para usar ExpressRoute, debe realizar la configuración entre pares públicos en ExpressRoute con el centro de datos de destino para la replicación de Site Recovery.

<!---HONumber=AcomDC_0224_2016-->