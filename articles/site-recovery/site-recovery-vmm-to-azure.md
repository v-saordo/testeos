<properties
	pageTitle="Replicación de máquinas virtuales de Hyper-V de nubes de VMM en Azure | Microsoft Azure"
	description="En este artículo se describe cómo replicar en Azure máquinas virtuales de Hyper-V de hosts de Hyper-V ubicados en nubes VMM de System Center."
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
	ms.topic="hero-article"
	ms.date="02/16/2016"
	ms.author="raynew"/>

#  Replicación de máquinas virtuales de Hyper-V situadas en nubes de VMM en Azure

El servicio Azure Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. Para obtener una introducción rápida, lea [¿Qué es Azure Site Recovery?](site-recovery-overview.md).

## Información general

En este artículo se describe cómo implementar Site Recovery para replicar en Azure máquinas virtuales de Hyper-V de servidores host de Hyper-V que se encuentran en nubes privadas de VMM.

El artículo incluye los requisitos previos para el escenario y muestra cómo configurar un almacén de Site Recovery, instalar el proveedor de Azure Site Recovery en el servidor VMM de origen, registrar el servidor en el almacén, agregar una cuenta de almacenamiento de Azure, instalar el Agente de Servicios de recuperación de Azure en servidores host de Hyper-V y configurar la protección de las nubes VMM que se aplicará a todas las máquinas virtuales protegidas para luego habilitar la protección en esas máquinas virtuales. Se termina comprobando la conmutación por error para asegurarse de que todo funciona según lo esperado.

Publique cualquier comentario o pregunta en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Arquitectura

![Arquitectura](./media/site-recovery-vmm-to-azure/topology.png)

- El proveedor de Azure Site Recovery se instala en el servidor VMM durante la implementación de Site Recovery y el servidor VMM se registra en el almacén de Site Recovery. El proveedor se comunica con Site Recovery para administrar la orquestación de la replicación.
- El Agente de Servicios de recuperación de Azure se instala en servidores host de Hyper-V durante la implementación de Site Recovery. Se encarga de administrar la replicación de datos en Almacenamiento de Azure.


## Requisitos previos de Azure

Esto es lo que necesita en Azure:

**Requisito previo** | **Detalles**
--- | ---
**Cuenta de Azure**| Necesitará una cuenta de [Microsoft Azure](https://azure.microsoft.com/). Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) sobre los precios de Site Recovery. 
**Almacenamiento de Azure** | Necesitará una cuenta de almacenamiento de Azure para almacenar los datos replicados. Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se ponen en marcha cuando se produce la conmutación por error. <br/><br/>Necesita una [cuenta de almacenamiento con redundancia geográfica de tipo estándar](../storage/storage-redundancy.md#geo-redundant-storage). La cuenta debe encontrarse en la misma región que el servicio Site Recovery y debe estar asociada a la misma suscripción. Tenga en cuenta que la replicación en cuentas de almacenamiento premium no se admite actualmente y no se debe utilizar.<br/><br/>[Más información sobre](../storage/storage-introduction.md) Almacenamiento de Azure.
**Red de Azure** | Necesitará una red virtual de Azure a la que se conectarán las máquinas virtuales de Azure cuando se produzca la conmutación por error. La red virtual de Azure debe estar en la misma región que el almacén de Site Recovery. 

## Requisitos previos locales

Esto es lo que necesita en el entorno local.

**Requisito previo** | **Detalles**
--- | ---
**VMM** | Necesitará al menos un servidor VMM implementado como un servidor físico o virtual independiente o como un clúster virtual. <br/><br/>El servidor VMM debe estar ejecutando System Center 2012 R2 con las últimas actualizaciones acumulativas.<br/><br/>Necesitará al menos una nube configurada en el servidor VMM.<br/><br/>La nube de origen que desea proteger debe contener uno o varios grupos de hosts de VMM.<br/><br/>Puede encontrar más información sobre la configuración de nubes VMM en [Walkthrough: Creating private clouds with System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) (Tutorial: Creación de nubes privadas con VMM de System Center 2012 SP1) en el blog de Keith Mayer.
**Hyper-V** | Necesitará uno o varios servidores host de Hyper-V o clústeres en la nube VMM. El servidor host debe tener una o varias máquinas virtuales. <br/><br/>El servidor Hyper-V se debe estar ejecutando en Windows Server 2012 R2 como mínimo con el rol de Hyper-V y tener instaladas las actualizaciones más recientes.<br/><br/>Todo servidor Hyper-V que contenga máquinas virtuales que desee proteger debe estar ubicado en una nube VMM.<br/><br/>Si está ejecutando Hyper-V en un clúster, tenga en cuenta que ese agente de clúster no se crea automáticamente si tiene un clúster basado en una dirección IP estática. Tendrá que configurar manualmente el agente de clúster. [Puede encontrar más información](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) en la entrada de blog de Aidan Finn.
**Máquinas protegidas** | Las máquinas virtuales que desee proteger deben cumplir los [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).


## Requisitos previos de asignación de redes
Al proteger las máquinas virtuales en los mapas de asignación de redes de Azure entre redes de VM en el servidor de VMM de origen y las redes de Azure de destino para habilitar lo siguiente:

- Todas las máquinas con conmutación por error en la misma red pueden conectarse entre sí, independientemente del plan de recuperación en el que se encuentran.
- Si se configura una puerta de enlace de red en la red Azure de destino, las máquinas virtuales se pueden conectar a otras máquinas virtuales locales.
- Si no configura la asignación de redes, solo las máquinas virtuales con conmutación por error en el mismo plan de recuperación podrán conectarse entre sí después de la conmutación por error en Azure.

Si desea implementar la asignación de redes, necesitará lo siguiente:

- Las máquinas virtuales que desea proteger en el servidor VMM de origen deben estar conectadas a una red de máquina virtual. Esa red debe estar vinculada a una red lógica asociada con la nube.
- Una red de Azure a la que pueden conectarse máquinas virtuales replicadas después de la conmutación por error. Seleccionará esta red en el momento de la conmutación por error. La red debe estar en la misma región que su suscripción de Azure Site Recovery.

Para prepararse para la asignación de red, siga estos pasos:

1. [Más información](site-recovery-network-mapping.md) sobre los requisitos de asignación de red.
2. Prepare las redes de máquinas virtuales en VMM:

	- [Información general de la configuración de redes lógicas en VMM](https://technet.microsoft.com/library/jj721568.aspx).
	- [Configure redes de máquinas virtuales](https://technet.microsoft.com/library/jj721575.aspx).


## Paso 1: Creación de un almacén de recuperación del sitio

1. Inicie sesión en el [Portal de administración](https://portal.azure.com) desde el servidor VMM que desee registrar.
2. Haga clic en **Servicios de datos** > **Servicios de recuperación** y **Almacén de Site Recovery**.
3. Haga clic en **Crear nuevo** > **Creación rápida**.
4. En **Nombre**, escriba un nombre descriptivo para identificar el almacén.
5. En **Región**, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Haga clic en **Crear almacén**.

	![Almacén nuevo](./media/site-recovery-vmm-to-azure/create-vault.png)

Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como **Activo** en la página principal de Servicios de recuperación.

## Paso 2: Generación de una clave de registro de almacén

Generación de una clave de registro en el almacén. Después de descargar el proveedor de Azure Site Recovery y de instalarlo en el servidor VMM, usará esta clave para registrar el servidor VMM en el almacén.

1. En la página **Servicios de recuperación**, haga clic en el almacén para abrir la página Inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

	![Icono de inicio rápido](./media/site-recovery-vmm-to-azure/qs-icon.png)

2. En la lista desplegable, seleccione **Entre un sitio de VMM local y Microsoft Azure**.
3. En **Preparar servidores VMM**, haga clic en **Generar archivo de clave de registro**. El archivo de clave se genera automáticamente y es válido durante 5 días después de su generación. Si no tiene acceso al Portal de Azure desde el servidor VMM, tendrá que copiar este archivo en el servidor.

	![Clave de registro](./media/site-recovery-vmm-to-azure/register-key.png)

## Paso 3: Instalación del proveedor de Azure Site Recovery

1. En **Inicio rápido** > **Preparar servidores VMM**, haga clic en **Descargar el proveedor de Microsoft Azure Site Recovery para instalarlo en servidores VMM** para obtener la versión más reciente del archivo de instalación del proveedor.
2. Ejecute este archivo en el servidor VMM de origen. Si VMM está implementado en un clúster y va a instalar el proveedor por primera vez, instálelo en un nodo activo y finalice la instalación para registrar el servidor VMM en el almacén. A continuación, instale el proveedor en los demás nodos. Tenga en cuenta que si está actualizando el proveedor, tendrá que actualizarlo en todos los nodos, ya que deben ejecutar la misma versión del proveedor.
3. El instalador realiza una comprobación de los requisitos previos y solicita permiso para detener el servicio VMM y así comenzar la instalación del proveedor. El servicio VMM se reiniciará automáticamente cuando finalice la instalación. Si va a instalarlo en un clúster VMM, se le pedirá que detenga el rol de clúster.

	![Requisitos previos](./media/site-recovery-vmm-to-azure/prereq.png)

4. En **Microsoft Update** puede optar por recibir actualizaciones. Con esta configuración habilitada, las actualizaciones del proveedor se instalarán según su directiva de Microsoft Update.

	![Microsoft Updates](./media/site-recovery-vmm-to-azure/updates.png)


5.  La ubicación de instalación del proveedor está establecida en **<SystemDrive>\\Program Files\\Microsoft System Center 2012 R2\\Virtual Machine Manager\\bin**. Haga clic en **Instalar**.

	![InstallLocation](./media/site-recovery-vmm-to-azure/install-location.png)

6. Una vez instalado el proveedor, haga clic en **Registrar** para registrar el servidor en el almacén.

	![InstallComplete](./media/site-recovery-vmm-to-azure/install-complete.png)

7. En **Conexión a Internet**, especifique cómo se conecta a Internet el proveedor que se ejecuta en el servidor VMM. Seleccione **Utilizar la configuración proxy del sistema predeterminado** para usar la configuración predeterminada de conexión a Internet establecida en el servidor.

	![Configuración de Internet](./media/site-recovery-vmm-to-azure/proxy.png)

	- Si desea utilizar un proxy personalizado, debe configurarlo antes de instalar el proveedor. Al configurar las opciones del proxy personalizado, se ejecuta una prueba para comprobar la conexión del proxy.
	- Si utiliza a un proxy personalizado o el proxy predeterminado requiere autenticación, tendrá que especificar los detalles del proxy, incluida la dirección y el puerto del proxy.
	- Las siguientes direcciones URL deben ser accesibles desde el servidor VMM y los hosts de Hyper-v
		- **.hypervrecoverymanager.windowsazure.com
- **.accesscontrol.windows.net
- **.backup.windowsazure.com
- **.blob.core.windows.net
- **.store.core.windows.net
- Permita las direcciones IP que se describen en [Intervalos de direcciones IP de los centros de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) y el protocolo HTTPS (443). Deberá también incluir en una lista blanca los intervalos de direcciones IP de la región de Azure que va a usar y los del oeste de EE. UU.

	- Si utiliza un proxy personalizado, se creará una cuenta de ejecución de VMM (DRAProxyAccount) mediante el uso automático de las credenciales de proxy especificadas. Configure el servidor proxy para que esta cuenta pueda autenticarse correctamente. La configuración de la cuenta de ejecución de VMM puede modificarse en la consola VMM. Para ello, abra el área de trabajo Configuración, expanda Seguridad, haga clic en Cuentas de ejecución y, a continuación, modifique la contraseña de DRAProxyAccount. Deberá reiniciar el servicio VMM para que esta configuración surta efecto.

8. En **Clave de registro**, seleccione lo que ha descargado de Azure Site Recovery y copiado en el servidor VMM.
9. En **Nombre del almacén**, compruebe el nombre del almacén en el que se registrará el servidor. 

	![Registro de servidor](./media/site-recovery-vmm-to-azure/credentials.png)

10. Puede especificar una ubicación para guardar el certificado SSL que se genera automáticamente para el cifrado de datos. Este certificado se usa si habilita el cifrado de datos para una nube VMM durante la implementación de Site Recovery. Mantenga el certificado en un lugar seguro. Cuando ejecute una conmutación por error en Azure, la seleccionará para descifrar los datos cifrados.

	![Registro de servidor](./media/site-recovery-vmm-to-azure/encryption.png)

11. En **Nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén. En una configuración de clúster, especifique el nombre del rol de clúster VMM.

12. En la sincronización de **Metadatos de la nube inicial** seleccione si desea sincronizar los metadatos de todas las nubes en el servidor VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM.

	![Registro de servidor](./media/site-recovery-vmm-to-azure/friendly.png)

13. Haga clic en **Next** para finalizar el proceso. Después del registro, la Recuperación del sitio de Azure recupera los metadatos del servidor VMM. El servidor se muestra en la pestaña **Servidores VMM** de la página **Servidores** del almacén.

### Instalación de la línea de comandos

El proveedor de Azure Site Recovery también puede instalarse mediante la siguiente línea de comandos. Este método se puede usar para instalar el proveedor en Server Core para Windows Server 2012 R2.

1. Descargue el archivo de instalación del proveedor y la clave de registro en una carpeta. Por ejemplo, C:\\ASR.
2. Detenga el servicio System Center Virtual Machine Manager.
3. Desde un símbolo del sistema con privilegios elevados, extraiga al instalador del proveedor con estos comandos:

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Instale al proveedor de la siguiente manera:

		C:\ASR> setupdr.exe /i

5. Registre el proveedor de la siguiente manera:

    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Los parámetros son los siguientes:

 - **/Credentials**: parámetro obligatorio que especifica la ubicación donde se encuentra el archivo de clave de registro.  
 - **/FriendlyName**: parámetro obligatorio para el nombre del servidor host Hyper-V que aparece en el portal de Azure Site Recovery.
 - **/EncryptionEnabled**: parámetro opcional para especificar si desea cifrar las máquinas virtuales en Azure (en cifrado de reposo). El nombre de archivo debe tener una extensión **.pfx**.
 - **/proxyAddress**: parámetro opcional que especifica la dirección del servidor proxy.
 - **/proxyport**: parámetro opcional que especifica el puerto del servidor proxy.
 - **/proxyusername**: parámetro opcional que especifica el nombre de usuario del proxy.
 - **/proxypassword**: parámetro opcional que especifica la contraseña del proxy.  


## Paso 4: Creación de una cuenta de almacenamiento de Azure

1. Si no dispone de una cuenta de almacenamiento de Azure, haga clic en **Agregar cuenta de almacenamiento de Azure** para crear una.
2. Cree una cuenta con la replicación geográfica habilitada. Además, debe estar en la misma región que el servicio Azure Site Recovery y estar asociada a la misma suscripción.

	![Cuenta de almacenamiento](./media/site-recovery-vmm-to-azure/storage.png)

## Paso 5: Instalación del agente de los Servicios de recuperación de Azure

Instale el Agente de Servicios de recuperación de Azure en cada servidor host de Hyper-V situado en la nube VMM.

1. Haga clic en **Inicio rápido** > **Descargar el Agente de servicios de Azure Site Recovery e instalarlo en los hosts** para obtener la última versión del archivo de instalación del agente.

	![Install Recovery Services Agent](./media/site-recovery-vmm-to-azure/install-agent.png)

2. Ejecute el archivo de instalación en cada servidor host de Hyper-V.
3. En la página **Comprobación de requisitos previos**, haga clic en **Siguiente**. Todos los requisitos previos que falten se instalarán automáticamente.

	![Prerequisites Recovery Services Agent](./media/site-recovery-vmm-to-azure/agent-prereqs.png)

4. En la página **Configuración de la instalación**, especifique dónde desea instalar el agente y seleccione la ubicación de la caché en la que se instalarán los metadatos de copia de seguridad. Luego haga clic en **Instalar**.
5. Una vez finalizada instalación, haga clic en **Cerrar** para completar el asistente.
	
	![Registro del agente MARS](./media/site-recovery-vmm-to-azure/agent-register.png)

### Instalación de la línea de comandos 

También puede instalar el Agente de Servicios de recuperación de Microsoft Azure desde la línea de comandos mediante el comando siguiente:

    marsagentinstaller.exe /q /nu
	
## Paso 6: Configuración de la protección de la nube

Una vez registrado el servidor de VMM, puede configurar los valores de protección de la nube. La opción **Sincronizar datos de nube con el almacén** se habilita cuando se instala el proveedor, de modo que todas las nubes del servidor VMM aparecerán en la pestaña <b>Elementos protegidos</b> del almacén.

![Nube publicada](./media/site-recovery-vmm-to-azure/clouds-list.png)

1. En la página Inicio rápido, haga clic en **Configurar protección para nubes de VMM**.
2. En la pestaña **Elementos protegidos**, haga clic en la nube que desea configurar y vaya a la pestaña **Configuración**.
3. En **Destino**, seleccione **Azure**.
4. En **Cuenta de almacenamiento**, seleccione la cuenta de almacenamiento de Azure que desea usar para la replicación.
5. Establezca **Cifrar datos almacenados** en **Desactivado**. Este valor especifica que los datos de deben cifrar replicados entre el sitio local y Azure.
6. En **Copiar frecuencia**, deje la configuración predeterminada. Este valor especifica la frecuencia con que se deben sincronizar los datos entre las ubicaciones de origen y de destino.
7. En **Retener puntos de recuperación para**, deje la configuración predeterminada. Con un valor predeterminado de cero, el punto de recuperación más reciente para una máquina virtual es el único que se almacena en un servidor host de réplica.
8. En **Frecuencia de las instantáneas coherentes con la aplicación**, deje la configuración predeterminada. Este valor especifica la frecuencia de creación de instantáneas. Las instantáneas utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea. Si establece un valor, asegúrese de que sea inferior al número de puntos de recuperación adicionales que configure.
9. En **Hora de inicio de la replicación**, especifique cuándo debe comenzar la replicación de los datos en Azure. Se usará la zona horaria del servidor host de Hyper-V. Se recomienda que programe la replicación inicial durante las horas de menos actividad.

	![Cloud replication settings](./media/site-recovery-vmm-to-azure/cloud-settings.png)

Tras guardar la configuración, se creará un trabajo que se podrá supervisar en la pestaña **Trabajos**. Todos los servidores host de Hyper-V de la nube de origen VMM se configurarán para la replicación.

Tras guardar, puede modificar la configuración de la nube en la pestaña **Configurar**. Para modificar la ubicación de destino o la cuenta de almacenamiento de destino, deberá eliminar la configuración de la nube y luego volver a configurar la nube. Tenga en cuenta que si cambia la cuenta de almacenamiento, el cambio solo se aplicará a las máquinas virtuales que tengan la protección habilitada después de que la cuenta de almacenamiento se ha modificado. Las máquinas virtuales existentes no se migrarán a la nueva cuenta de almacenamiento.

## Paso 7: Configuración de la asignación de red
Antes de comenzar la asignación de red, compruebe que las máquinas virtuales en el servidor de VMM de origen están conectadas a una red de VM. Además, cree una o varias redes virtuales de Azure. Tenga en cuenta que pueden asignarse varias redes de VM a una sola red de Azure.

1. En la página de inicio rápido, haga clic en **Asignar redes**.
2. En la pestaña **Redes**, en **Ubicación de origen**, seleccione el servidor VMM de origen. En **Ubicación de destino**, seleccione Azure.
3. En **Red de origen** se muestra una lista de las redes de máquinas virtuales asociadas al servidor de VMM. En **Red de destino** se muestran las redes de Azure asociadas a la suscripción.
4. Seleccione la red de máquina virtual de origen y haga clic en **Asignar**.
5. En la página **Seleccione una red de destino**, seleccione la red de Azure de destino que desea usar.
6. Haga clic en la marca de verificación para completar el proceso de asignación.

	![Cloud replication settings](./media/site-recovery-vmm-to-azure/map-networks.png)

Después de guardar la configuración, se inicia un trabajo para realizar un seguimiento del progreso de la asignación, que puede supervisarse en la pestaña Trabajos. Todas las máquinas virtuales de réplica existentes que correspondan a la red de VM de origen se conectarán a las redes de Azure de destino. Las nuevas máquinas virtuales que se conecten a la red de VM de origen se conectarán a la red de Azure asignada después de la replicación. Si modifica una asignación existente a una nueva red, las máquinas virtuales de réplica se conectarán con la nueva configuración.

Tenga en cuenta que si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.

## Paso 8: Habilitación de la protección para las máquinas virtuales

Una vez que los servidores, las nubes y las redes se configuran correctamente, puede habilitar la protección para las máquinas virtuales en la nube. Tenga en cuenta lo siguiente:

- Las máquinas virtuales deben cumplir los [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).
- Para habilitar la protección del sistema operativo y el disco del sistema operativo, deben establecerse las propiedades de la máquina virtual. Al crear una máquina virtual en VMM con una plantilla de máquina virtual puede establecer la propiedad. También puede establecer estas propiedades para máquinas virtuales existentes en las pestañas **General** y **Configuración del hardware** de las propiedades de la máquina virtual. Si no ve estas propiedades en VMM, podrá configurarlas en el portal de Azure Site Recovery.

	![Create virtual machine](./media/site-recovery-vmm-to-azure/enable-new.png)

	![Modify virtual machine properties](./media/site-recovery-vmm-to-azure/enable-existing.png)


1. Para habilitar la protección, en la pestaña **Máquinas virtuales** de la nube en la que se encuentra la máquina virtual, haga clic en **Habilitar protección** > **Agregar máquinas virtuales**.
2. En la lista de máquinas virtuales de la nube, seleccione la que desea proteger.

	![Enable virtual machine protection](./media/site-recovery-vmm-to-azure/select-vm.png)

	Siga el progreso de la acción **Habilitar protección** en la pestaña **Trabajos**, incluida la replicación inicial. La máquina virtual estará preparada para la conmutación por error después de que finalice el trabajo **Finalizar protección**. Después de habilitar la protección y replicar las máquinas virtuales, podrá verlas en Azure.


	![Virtual machine protection job](./media/site-recovery-vmm-to-azure/vm-jobs.png)

3. Compruebe las propiedades de la máquina virtual y modifíquelas según sea necesario.

	![Verify virtual machines](./media/site-recovery-vmm-to-azure/vm-properties.png)

4. En la pestaña **Configurar** de las propiedades de la máquina virtual, se pueden modificar las siguientes propiedades de red.



- **Número de adaptadores de red de la máquina virtual de destino**: el número de adaptadores de red viene determinado por el tamaño que especifique para la máquina virtual de destino. Compruebe las [especificaciones de tamaño de la máquina virtual](../virtual-machines/virtual-machines-size-specs.md#size-tables) para saber el número de adaptadores admitidos. Al modificar el tamaño de una máquina virtual y guardar la configuración, el número del adaptador de red cambiará la próxima vez que abra la página **Configurar**. El número de adaptadores de red de las máquinas virtuales de destino es el número mínimo de adaptadores de red en la máquina virtual de origen y el número máximo de adaptadores de red compatible con el tamaño de la máquina virtual elegida, de la forma siguiente:

	- Si el número de adaptadores de red en el equipo de origen es menor o igual al número de adaptadores permitido para el tamaño de la máquina de destino, el destino tendrá el mismo número de adaptadores que el origen.
	- Si el número de adaptadores para la máquina virtual de origen supera el número permitido para el tamaño de destino, entonces se utilizará el tamaño máximo de destino.
	- Por ejemplo, si una máquina de origen tiene dos adaptadores de red y el tamaño de la máquina de destino admite cuatro, el equipo de destino tendrá dos adaptadores. Si el equipo de origen tiene dos adaptadores pero el tamaño de destino compatible solo admite uno, el equipo de destino tendrá solo un adaptador. 	

- **Red de la máquina virtual de destino**: la red a la que se conecta la máquina virtual viene determinada por la asignación de red de la red de la máquina virtual de origen. Si la máquina virtual de origen tiene más de un adaptador de red y las redes de origen están asignadas a distintas redes en el destino, tendrá que elegir entre una de las redes de destino.
- **Subred de cada adaptador de red**: para cada adaptador de red puede seleccionar la subred a la que se conectará la máquina virtual con conmutación por error.
- **IP de destino**: si el adaptador de red de la máquina virtual de origen está configurado para usar una dirección IP estática, puede proporcionar la dirección IP para la máquina virtual de destino. Utilice esta característica para conservar la dirección IP de una máquina virtual de origen después de una conmutación por error. Si no se proporciona ninguna dirección IP, se proporciona al adaptador de red cualquier dirección IP disponible en el momento de la conmutación por error. Si se especifica la dirección IP de destino, pero ya está en uso por otra máquina virtual que se ejecuta en Azure, la conmutación por error dará error.  

	![Modificación de las propiedades de red](./media/site-recovery-vmm-to-azure/multi-nic.png)

>[AZURE.NOTE] No se admiten máquinas virtuales de Linux con una dirección IP estática.

## Prueba de la implementación

Para probar la implementación puede realizar una prueba de conmutación por error para una máquina virtual individual, o crear un plan de recuperación que incluya numerosas máquinas virtuales y realizar una conmutación por error de prueba para el plan.

La conmutación por error de prueba simula su mecanismo de conmutación por error y recuperación en una red aislada. Observe lo siguiente:

- Si después de la conmutación por error desea conectarse a la máquina virtual de Azure mediante Escritorio remoto, habilite Conexión a Escritorio remoto en la máquina virtual antes de ejecutar la prueba.
- Después de la conmutación por error, usará una dirección IP pública para conectarse a la máquina virtual de Azure mediante Escritorio remoto. Si desea realizar esto, asegúrese de no tener ninguna directiva de dominio que impida que se conecte a una máquina virtual mediante una dirección pública.

### Creación de un plan de recuperación

1. En la pestaña **Planes de recuperación**, agregue un plan nuevo. Especifique un nombre, **VMM** en **Tipo de origen** y el servidor VMM de origen en **Origen**. El destino será Azure.

	![Creación de un plan de recuperación](./media/site-recovery-vmm-to-azure/recovery-plan1.png)

2. En la página **Seleccionar máquinas virtuales**, seleccione las máquinas virtuales que se agregarán al plan de recuperación. Estas máquinas virtuales se agregan al grupo predeterminado del plan de recuperación: grupo 1. Se ha probado un máximo de 100 máquinas virtuales en un solo plan de recuperación.

	- Si desea comprobar las propiedades de la máquina virtual antes de agregarlas al plan, haga clic en la máquina virtual en la página de propiedades de la nube en la que se encuentra. También puede configurar las propiedades de la máquina virtual en la consola VMM.
	- Todas las máquinas virtuales que se muestran se han habilitado para la protección. La lista incluye las máquinas virtuales que se han habilitado para protección y cuya replicación inicial se ha completado, así como las que están habilitadas para protección y cuya replicación inicial está pendiente. Solo las máquinas virtuales con la replicación inicial completada pueden conmutar por error como parte de un plan de recuperación. 


	![Creación de un plan de recuperación](./media/site-recovery-vmm-to-azure/select-rp.png)

Una vez creado un plan de recuperación, aparecerá en la pestaña **Planes de recuperación**. También puede agregar [runbooks de automatización de Azure](site-recovery-runbook-automation.md) al plan de recuperación para automatizar las acciones durante la conmutación por error.

### Ejecución de una conmutación por error de prueba

Hay dos maneras de ejecutar una prueba de conmutación por error en Azure.

- **Probar la conmutación por error sin una red de Azure**: este tipo de conmutación por error de prueba comprueba que la máquina virtual aparece correctamente en Azure. La máquina virtual no estará conectada a ninguna red de Azure después de la conmutación por error.
- **Probar la conmutación por error con una red de Azure**: este tipo de conmutación por error comprueba que todo el entorno de replicación se incluye como se esperaba y que las máquinas virtuales con conmutación por error se conectarán a la red Azure de destino especificada. En el caso del control de subredes, para probar la conmutación por error de la subred se averiguará la máquina virtual de prueba de acuerdo con la subred de la máquina virtual de réplica. Esto es diferente a la replicación normal cuando la subred de una máquina virtual de réplica se basa en la subred de la máquina virtual de origen.

Si desea ejecutar una conmutación por error de prueba para una máquina virtual habilitada para protección en Azure sin especificar una red de Azure de destino, no es necesario preparar nada. Para ejecutar una conmutación por error de prueba con una red de Azure de destino, es necesario crear una nueva red de Azure que esté aislada de su red de Azure de producción (el comportamiento predeterminado cuando se crea una nueva red de Azure). Veamos cómo [ejecutar una prueba de conmutación por error](site-recovery-failover.md#run-a-test-failover) para obtener más detalles.


También deberá configurar la infraestructura de la máquina virtual replicada para que funcione según lo previsto. Por ejemplo, una máquina virtual con controlador de dominio y DNS se pueden replicar en Azure con Azure Site Recovery y se puede crear en la red de prueba mediante pruebas de conmutación por error. Consulte la sección [Consideraciones sobre la conmutación por error de prueba para Active Directory](site-recovery-active-directory.md#considerations-for-test-failover) para más información.

Para ejecutar un conmutación por error de prueba, realice lo siguiente:

1. En la pestaña **Planes de recuperación**, seleccione el plan y haga clic en **Conmutación por error de prueba**.
2. En la página **Confirmar conmutación por error de prueba**, seleccione **Ninguna** o una red de Azure concreta. Tenga en cuenta que si selecciona Ninguna, la conmutación por error de prueba comprueba que la máquina virtual se ha replicado correctamente en Azure, pero no comprueba la configuración de red de replicación.

	![Sin red](./media/site-recovery-vmm-to-azure/test-no-network.png)

3. Si el cifrado de datos para la nube está habilitado, en **Clave de cifrado**, seleccione el certificado que se emitió durante la instalación del proveedor en el servidor VMM, cuando activó la opción para habilitar el cifrado de datos para una nube.
4. En la pestaña **Trabajos** puede seguir el progreso de la conmutación por error. También debe poder ver la réplica de prueba de la máquina virtual en el Portal de Azure. Si está configurando para acceder a máquinas virtuales desde la red local puede iniciar una conexión de Escritorio remoto a la máquina virtual.
5. Cuando la conmutación por error alcance la fase **Completar pruebas**, haga clic en **Completar prueba** para terminar la conmutación por error de prueba. Puede profundizar hasta la pestaña **Trabajo** para realizar un seguimiento de la conmutación por error del progreso y el estado, y llevar a cabo las acciones necesarias.
6. Después de la conmutación por error, podrá ver la réplica de prueba de la máquina virtual en el Portal de Azure. Si está configurando para acceder a máquinas virtuales desde la red local puede iniciar una conexión de Escritorio remoto a la máquina virtual. Haga lo siguiente:

    1. Compruebe que las máquinas virtuales se inician correctamente.
    2. Si después de la conmutación por error desea conectarse a la máquina virtual de Azure mediante Escritorio remoto, habilite Conexión a Escritorio remoto en la máquina virtual antes de ejecutar la prueba. También deberá agregar un punto de conexión RDP a la máquina virtual. Puede aprovechar un [Runbooks de automatización de Azure](site-recovery-runbook-automation.md) para hacerlo.
    3. Después de conmutación por error, si usa una dirección IP pública para conectarse a la máquina virtual en Azure mediante Escritorio remoto, asegúrese de no tener directivas de dominio que le impidan conectarse a una máquina virtual con una dirección pública.

7.  Cuando se complete la prueba, haga lo siguiente:
	- Haga clic en **La conmutación por error de prueba se ha completado**. Limpie el entorno de prueba para apagar y eliminar automáticamente las máquinas virtuales de prueba.
	- Haga clic en **Notas** para registrar y guardar las observaciones asociadas a la conmutación por error de prueba.

## Pasos siguientes

Más información sobre la [configuración de los planes de recuperación](site-recovery-create-recovery-plans.md) y la [conmutación por error](site-recovery-failover.md).

<!---HONumber=AcomDC_0218_2016-->