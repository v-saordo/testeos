<properties
	pageTitle="Replicación de máquinas virtuales de Hyper-V (en nubes de VMM) en un sitio de VMM secundario | Microsoft Azure"
	description="En este artículo se describe cómo replicar máquinas virtuales de Hyper-V de nubes VMM en un sitio VMM secundario con Azure Site Recovery."
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
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="raynew"/>

# Replicación de máquinas virtuales de Hyper-V (en nubes VMM) en un sitio de VMM secundario

El servicio Azure Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. Para obtener una introducción rápida, lea [¿Qué es Azure Site Recovery?](site-recovery-overview.md).

## Información general

En este artículo se describe cómo replicar máquinas virtuales de Hyper-V de servidores host de Hyper-V que se administran en nubes VMM en un sitio VMM secundario mediante Azure Site Recovery.

Esta artículo incluye los requisitos previos, muestra cómo configurar un almacén de recuperación del sitio, instalar el proveedor de Azure Site Recovery en los servidores VMM de origen y destino, registrar los servidores en el almacén, configurar los valores de la protección para las nubes VMM y, luego, habilitar la protección de VM Hyper-V. Se termina comprobando la conmutación por error para asegurarse de que todo funciona según lo esperado.

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Arquitectura

La siguiente imagen muestra los distintos canales y puertos de comunicación utilizados por Azure Site Recovery para la orquestación y la replicación

![Topología E2E](./media/site-recovery-vmm-to-vmm/e2e-topology.png)

## Antes de comenzar

Asegúrese de que tiene preparados estos requisitos previos:

**Requisitos previos** | **Detalles** 
--- | ---
**Las tablas de Azure**| Necesitará una cuenta de [Microsoft Azure](https://azure.microsoft.com/). Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). [Obtenga más información](https://azure.microsoft.com/pricing/details/site-recovery/) sobre los precios de Site Recovery. 
**VMM** | Necesitará al menos un servidor VMM.<br/><br/>El servidor VMM debe ejecutar al menos System Center 2012 SP1 con las últimas actualizaciones acumulativas.<br/><br/>Si quiere configurar la protección con un único servidor VMM, necesitará al menos dos nubes configuradas en el servidor.<br/><br/>Si quiere implementar la protección con dos servidores VMM, cada servidor debe tener al menos una nube configurada en el servidor VMM principal que quiere proteger, y una nube configurada en el servidor VMM secundario que quiera usar para la protección y la recuperación<br/><br/>Todas las nubes de VMM deben tener establecido el perfil de capacidad de Hyper-V.<br/><br/>La nube de origen que quiere proteger debe contener uno o más grupos de host VMM.<br/><br/>Puede obtener más información sobre cómo configurar nubes de VMM en [Walkthrough: Creating private clouds with System Center 2012 SP1 VMM (Tutorial: Creación de nubes privadas) con System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) en el blog de Keith Mayer.
**Hyper-V** | Necesitará uno o más servidores host de Hyper-V en los grupos host de VMM principales y secundarios, así como una o varias máquinas virtuales en cada servidor host de Hyper-V.<br/><br/>Los servidores de Hyper-V host y de destino deben ejecutar al menos Windows Server 2012 con el rol de Hyper-V, además de tener instaladas las actualizaciones más recientes.<br/><br/>Cualquier servidor de Hyper-V que contenga máquinas virtuales que quiera proteger debe estar ubicado en una nube de VMM.<br/><br/>Si está ejecutando Hyper-V en un clúster, tenga en cuenta que ese agente de clúster no se crea automáticamente si tiene un clúster basado en una dirección IP estática. Tendrá que configurar manualmente el agente de clúster. [Puede encontrar más información](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) en la entrada de blog de Aidan Finn.
**Asignación de red** | Puede configurar la asignación de red para asegurarse de que las máquinas virtuales replicadas se colocan de manera óptima en los servidores host de Hyper-V secundarios tras la conmutación por error y que se pueden conectar a las redes de VM adecuadas. Si no configura la asignación de red, las VM de réplica no se conectarán a ninguna red después de la conmutación por error.<br/><br/>Para configurar la asignación de red durante la implementación, asegúrese de que las máquinas virtuales del servidor host de Hyper-V de origen estén conectadas a una red de VM de VMM. Dicha red debería estar vinculada a una red lógica que esté asociada a la nube.<br/<br/>La nube de destino en el servidor VMM secundario que se utiliza para la recuperación debe tener configurada una red de VM correspondiente y, a su vez, debe estar vinculada a una red lógica correspondiente que esté asociada a la nube de destino.<br/><br/>[Más información](site-recovery-network-mapping.md) sobre la asignación de red.
**Asignación de almacenamiento** | De forma predeterminada cuando se replica una máquina virtual en un servidor host de Hyper-V de origen a un servidor host de Hyper-V de destino, los datos replicados se almacenan en la ubicación predeterminada indicada para el host de Hyper-V de destino en el Administrador de Hyper-V. Para obtener más control sobre dónde se almacenan los datos replicados, puede configurar la asignación de almacenamiento<br/><br/> Para configurar la asignación de almacenamiento, deberá configurar las clasificaciones de almacenamiento en los servidores VMM de origen y de destino antes de comenzar la implementación. [Más información](site-recovery-storage-mapping.md).


## Paso 1: Creación de un almacén de recuperación del sitio

1. Inicie sesión en el [Portal de administración](https://portal.azure.com) desde el servidor VMM que desee registrar.

2. Expanda **Servicios de datos** > **Servicios de recuperación** y haga clic en **Almacén de Site Recovery**.

3. Haga clic en **Crear nuevo** > **Creación rápida**.

4. En **Nombre**, escriba un nombre descriptivo para identificar el almacén.

5. En **Región**, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](http://go.microsoft.com/fwlink/?LinkId=389880).

6. Haga clic en **Crear almacén**.

	![Crear almacén](./media/site-recovery-vmm-to-vmm/create-vault.png)

En la barra de estado, compruebe que se ha creado el almacén. El almacén aparecerá como **Activo** en la página principal de Servicios de recuperación.

## Paso 2: Generación de una clave de registro de almacén

Generación de una clave de registro en el almacén. Después de descargar el proveedor de Azure Site Recovery y de instalarlo en el servidor VMM, usará esta clave para registrar el servidor VMM en el almacén.

1. En la página **Servicios de recuperación**, haga clic en el almacén para abrir la página Inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

	![Icono de inicio rápido](./media/site-recovery-vmm-to-vmm/quick-start-icon.png)

2. En la lista desplegable seleccione **Entre dos sitios Hyper-V locales**.
3. En **Preparar servidores VMM**, haga clic en** Generar archivo de clave de registro**. El archivo de clave se genera automáticamente y es válido durante 5 días después de su generación. Si no tiene acceso al Portal de Azure desde el servidor VMM, tendrá que copiar este archivo en el servidor.

	![Clave de registro](./media/site-recovery-vmm-to-vmm/register-key.png)

## Paso 3: Instalación del proveedor de Azure Site Recovery

4. En la página de **Inicio rápido**, en **Preparar servidores VMM**, haga clic en **Descargar el proveedor de Microsoft Azure Site Recovery para la instalación en servidores VMM** a fin de obtener la versión más reciente del archivo de instalación del proveedor.

2. Ejecute este archivo en el servidor VMM de origen. Si VMM está implementado en un clúster y va a instalar el proveedor por primera vez, instálelo en un nodo activo y finalice la instalación para registrar el servidor VMM en el almacén. A continuación, instale el proveedor en los demás nodos. Tenga en cuenta que si está actualizando el proveedor, tendrá que actualizarlo en todos los nodos, ya que deben ejecutar la misma versión del proveedor.


3. El instalador realiza algunas operaciones de **Comprobación de requisitos previos** y solicita permiso para detener el inicio de la instalación del proveedor por parte del servicio VMM. El servicio VMM se reiniciará automáticamente cuando finalice la instalación. Si va a instalarlo en un clúster VMM, se le pedirá que detenga el rol de clúster.

4. En **Microsoft Update** puede optar por recibir actualizaciones. Con esta configuración habilitada, las actualizaciones del proveedor se instalarán según su directiva de Microsoft Update.

	![Microsoft Updates](./media/site-recovery-vmm-to-vmm/ms-update.png)

5. La ubicación de instalación está establecida en **<SystemDrive>\\Program Files\\Microsoft System Center 2012 R2\\Virtual Machine Manager\\bin**. Haga clic en el botón Instalar para iniciar la instalación del proveedor.

	![InstallLocation](./media/site-recovery-vmm-to-vmm/install-location.png)

6. Una vez instalado el proveedor, haga clic en **Registrar** para registrar el servidor en el almacén.

	![InstallComplete](./media/site-recovery-vmm-to-vmm/install-complete.png)

7. En **Conexión a Internet**, especifique cómo se conecta a Internet el proveedor que se ejecuta en el servidor VMM. Seleccione **Conectarse con la configuración de proxy existente** para usar la configuración predeterminada de conexión a Internet establecida en el servidor.

	![Configuración de Internet](./media/site-recovery-vmm-to-vmm/proxy-details.png)

	- Si desea utilizar un proxy personalizado, debe configurarlo antes de instalar el proveedor. Al configurar las opciones del proxy personalizado, se ejecuta una prueba para comprobar la conexión del proxy.
	- Si utiliza a un proxy personalizado o el proxy predeterminado requiere autenticación, tendrá que especificar los detalles del proxy, incluida la dirección y el puerto del proxy.
	- Las siguientes direcciones URL deben ser accesibles desde el servidor VMM y los hosts de Hyper-v
		- *.hypervrecoverymanager.windowsazure.com
		- *.accesscontrol.windows.net
		- *.backup.windowsazure.com
		- *.blob.core.windows.net
		- *.store.core.windows.net
	- Permita las direcciones IP que se describen en [Intervalos de direcciones IP de los centros de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el protocolo HTTPS (443). Tendrá que incluir en una lista blanca los intervalos de direcciones IP de la región de Azure que va a usar y los del Oeste de EE. UU.
	- Si utiliza un proxy personalizado, se creará una cuenta de ejecución de VMM (DRAProxyAccount) mediante el uso automático de las credenciales de proxy especificadas. Configure el servidor proxy para que esta cuenta pueda autenticarse correctamente. La configuración de la cuenta de ejecución de VMM puede modificarse en la consola VMM. Para ello, abra el área de trabajo **Configuración**, expanda **Seguridad**, haga clic en **Cuentas de ejecución** y luego modifique la contraseña de DRAProxyAccount. Deberá reiniciar el servicio VMM para que esta configuración surta efecto.

8. En **Clave de registro**, seleccione lo que ha descargado de Azure Site Recovery y copiado en el servidor VMM.
9. En **Nombre del almacén**, compruebe el nombre del almacén en el que se registrará el servidor. Haga clic en *Siguiente*.

	![Registro de servidor](./media/site-recovery-vmm-to-vmm/vault-creds.png)

10.  La configuración de cifrado solo se usa cuando se está replicando VM de Hyper-V en nubes de VMM en Azure. Si se está replicando en un sitio secundario, no se usa.

	![Registro de servidor](./media/site-recovery-vmm-to-vmm/encrypt.png)

11.  En **Nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén. En una configuración de clúster, especifique el nombre del rol de clúster VMM.
12.  En **Sincronizar metadatos en la nube** seleccione si quiere sincronizar los metadatos de todas las nubes del servidor VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM.
 
	![Registro de servidor](./media/site-recovery-vmm-to-vmm/friendly-name.png)

13.  Haga clic en **Next** para finalizar el proceso. Después del registro, la Recuperación del sitio de Azure recupera los metadatos del servidor VMM. El servidor se muestra en la pestaña **Servidores VMM** de la página **Servidores** del almacén.


### Instalación de la línea de comandos

El proveedor de Azure Site Recovery también puede instalarse desde la línea de comandos. Este método se puede usar para instalar el proveedor en un Server CORE para Windows Server 2012 R2.

1. Descargue el archivo de instalación del proveedor y la clave de registro en una carpeta. Por ejemplo, C:\\ASR.
2. Detenga el servicio System Center Virtual Machine Manager.
3. Ejecute los siguientes comandos con privilegios de **Administrador** desde el símbolo del sistema para extraer el instalador del proveedor:

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Ejecute lo siguiente para instalar el proveedor:

    	C:\ASR> setupdr.exe /i

5. Registre el proveedor ejecutando:

    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Los parámetros son los siguientes:

 - **/Credentials**: parámetro obligatorio que especifica la ubicación donde se encuentra el archivo de clave de registro.  
 - **/FriendlyName**: parámetro obligatorio para el nombre del servidor host Hyper-V que aparece en el portal de Azure Site Recovery.
 - **/EncryptionEnabled**: parámetro opcional que solo es necesario usar en el escenario de VMM a Azure si se requiere el cifrado de las máquinas virtuales en reposo en Azure. Asegúrese de que el nombre del archivo que proporciona tiene la extensión **.pfx**.
 - **/proxyAddress**: parámetro opcional que especifica la dirección del servidor proxy.
 - **/proxyport**: parámetro opcional que especifica el puerto del servidor proxy.
 - **/proxyUsername**: parámetro opcional que especifica el nombre de usuario de proxy (si el proxy requiere autenticación).
 - **/proxyPassword**: parámetro opcional que especifica la contraseña para autenticarse con el servidor proxy (si el proxy requiere autenticación).  

## Paso 4: Configuración de la protección de la nube

Una vez que los servidores VMM están registrados, puede configurar la protección de la nube. Si habilitó la opción **Sincronizar datos de nube con el almacén** al instalar el proveedor, todas las nubes del servidor VMM aparecerán en la pestaña **Elementos protegidos** del almacén. Si no lo hizo, puede sincronizar una nube específica con Azure Site Recovery en la pestaña **General** de la página de propiedades de nube en la consola VMM.

![Nube publicada](./media/site-recovery-vmm-to-vmm/clouds-list.png)

1. En la página Inicio rápido, haga clic en **Configurar protección para nubes de VMM**.
2. En la pestaña **Nubes de VMM**, seleccione la nube que desea configurar y vaya a la pestaña **Configuración**.
3. En **Target**, seleccione **VMM**.
4. En **Target location**, seleccione el servidor VMM que administra la nube que desea utilizar para recuperación.
4. En **Target cloud**, seleccione la nube de destino que desea utilizar para la conmutación por error de máquinas virtuales en la nube de origen. Observe lo siguiente:

	- Recomendamos seleccionar una nube de destino que cumpla con los requisitos de recuperación para las máquinas virtuales que protegerá.
	- Una nube solo puede pertenecer a un único par de nubes, ya sea como nube principal o de destino.

5. En **Copiar frecuencia**, especifique con qué frecuencia se deben sincronizar los datos entre las ubicaciones de origen y de destino. Observe que esta configuración solo es pertinente cuando el host de Hyper-V ejecuta Windows Server 2012 R2. En el caso de otros servidores, se utiliza una configuración predeterminada de cinco minutos.
6. En **Puntos de recuperación adicionales**, especifique si desea crear más puntos de recuperación. El valor predeterminado de cero indica que el punto de recuperación más reciente de una máquina virtual principal es el único que se almacena en un servidor host de réplica. Tenga en cuenta que al habilitar varios puntos de recuperación necesita almacenamiento adicional para las instantáneas almacenadas en cada punto de recuperación. De forma predeterminada, los puntos de recuperación se crean cada hora, por lo que cada punto de recuperación contiene datos equivalentes a una hora. El valor del punto de recuperación que asigne a la máquina virtual en la consola VMM no debe ser menor que el valor asignado en la consola de Azure Site Recovery.
7. En **Frecuencia de las instantáneas coherentes con la aplicación**, especifique la frecuencia de creación de estas instantáneas. Hyper-V usa dos tipos de instantáneas, una instantánea estándar que proporciona una instantánea incremental de toda la máquina virtual y una instantánea coherente con la aplicación que toma una instantánea en un momento concreto de los datos de la aplicación dentro de la máquina virtual. Las instantáneas coherentes con la aplicación utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea. Tenga en cuenta que si habilita las instantáneas coherentes con la aplicación, se verá afectado el rendimiento de aplicaciones que se ejecutan en las máquinas virtuales de origen. Asegúrese de que el valor establecido es menor que el número de puntos de recuperación adicionales configurados.

	![Configuración de los valores de protección](./media/site-recovery-vmm-to-vmm/cloud-settings.png)

8. En **Compresión de transferencia de datos**, especifique si se deben comprimir los datos replicados que se transfieren.
9. En **Authentication**, especifique cómo se autentica el tráfico entre el servidor host de Hyper-V principal y el servidor host de Hyper-V de recuperación. Seleccione HTTPS a menos que tenga un entorno de Kerberos configurado en funcionamiento. Azure Site Recovery configurará automáticamente los certificados para la autenticación de HTTPS. No se requiere configuración manual. Si selecciona Kerberos, se usará un vale de Kerberos para la autenticación mutua de los servidores host. De forma predeterminada, se abrirán los puertos 8083 y 8084 (para los certificados) en el Firewall de Windows en los servidores hosts de Hyper-V. Observe que esta configuración solo es pertinente para servidores host de Hyper-V que se ejecutan en Windows Server 2012 R2.
10. En **Puerto**, modifique el número de puerto en el que los equipos host de origen y de destino escuchan el tráfico de replicación. Por ejemplo, puede modificar la configuración si desea aplicar el límite de ancho de banda de red de Calidad de servicio (QoS) para el tráfico de replicación. Compruebe que el puerto no lo está usando ninguna otra aplicación y que está abierto en la configuración del firewall.
11. En **Método de replicación**, especifique cómo se administrará la replicación inicial de los datos desde la ubicación de origen a la ubicación de destino, antes de que comience la replicación normal:

	- **Over the network**: Copiar los datos sobre la red puede ser un proceso lento y que consume muchos recursos. Le recomendamos utilizar esta opción si la nube contiene máquinas virtuales con discos duros virtuales relativamente pequeños y si el sitio principal está conectado al sitio secundario a través de una conexión con un amplio ancho de banda. Puede especificar que la copia se inicie de inmediato, o bien, seleccionar una hora. Si utiliza la replicación de red, le recomendamos programarla evitando las horas de mayor consumo.
	- **Sin conexión**: este método especifica que la replicación inicial se realizará mediante el uso de medios externos. Esta opción resulta útil si desea evitar la degradación del rendimiento de la red o en el caso de ubicaciones geográficamente remotas. Para utilizar este método, especifique la ubicación de exportación en la nube de origen y la ubicación de importación en la nube de destino. Cuando habilita la protección de una máquina virtual, el disco duro virtual se copia en la ubicación de exportación especificada. Usted lo envía al sitio de destino y lo copia en la ubicación de importación. El sistema copia la información importada en las máquinas virtuales de réplica. 

12. Seleccione **Delete Replica Virtual Machine** para especificar la máquina virtual de réplica que se debe eliminar si deja de proteger la máquina virtual al seleccionar la opción **Delete protection for the virtual machine** en la pestaña de máquinas virtuales de las propiedades de la nube. Con esta configuración deshabilitada, cuando deshabilite la protección, la máquina virtual se elimina de Azure Site Recovery, la configuración de Site Recovery para la máquina virtual se elimina de la consola VMM y la réplica se elimina.

	![Configuración de los valores de protección](./media/site-recovery-vmm-to-vmm/cloud-settings-replica.png)

Tras guardar la configuración, se creará un trabajo que se podrá supervisar en la pestaña **Trabajos**. Todos los servidores host de Hyper-V de la nube de origen VMM se configurarán para la replicación. Puede modificar la configuración de la nube en la pestaña **Configurar**. Si desea modificar la ubicación de destino o la nube de destino, deberá eliminar la configuración de la nube y después volver a configurarla.

### Preparación para la replicación inicial sin conexión

Deberá realizar las siguientes acciones para preparar la replicación inicial sin conexión:

- En el servidor de origen, especifique una ubicación de ruta de acceso desde la que se llevará a cabo la exportación de datos. Asigne Control total a NTFS y Compartir permisos con el servicio VMM en la ruta de acceso de exportación. En el servidor de destino, especifique una ubicación de ruta de acceso desde la cual tendrá lugar la importación de datos. Asigne los mismos permisos en esta ruta de acceso de importación.
- Si se comparte la ruta de acceso de importación o exportación, asigne la pertenencia de grupo de administrador, usuario avanzado, operador de impresión o de operador de servidor a la cuenta de servicio de VMM en el equipo remoto en el que se encuentra el recurso compartido.
- Si utiliza cualquier cuenta de ejecución para agregar hosts, en las rutas de acceso de importación y exportación, asigne permisos de lectura y escritura a las cuentas de ejecución de VMM.
- Los recursos compartidos de importación y exportación no deben ubicarse en ningún equipo utilizado como servidor host de Hyper-V, porque la configuración de bucle invertido no es compatible con Hyper-V.
- En Active Directory en cada servidor host de Hyper-V que contiene máquinas virtuales que desea proteger, habilite y configure la delegación restringida para confiar en los equipos remotos en los que se ubican las rutas de acceso de importación y exportación, de la forma siguiente:
	1. En el controlador de dominio, abra **Usuarios y equipos de Active Directory**.
	2. En el árbol de consola, haga clic en **Nombre de dominio** > **Equipos**.
	3. Haga clic con el botón derecho en el nombre del servidor host de Hyper-V > **Propiedades**.
	4. En la pestaña **Delegación**, haga clic en **Confiar en este equipo para la delegación solo a los servicios especificados**.
	5. Haga clic en **Usar cualquier protocolo de autenticación**.
	6. Haga clic en **Agregar** > **Usuarios y equipos**.
	7. Escriba el nombre del equipo que hospeda la ruta de acceso de exportación > **Aceptar**. En la lista de servicios disponibles, mantenga presionada la tecla CTRL y haga clic en **cifs** > **Aceptar**. Repita el proceso para el nombre del equipo que hospeda la ruta de acceso de importación. Repita según sea necesario para servidores host de Hyper-V adicionales.

## Paso 5: Configuración de la asignación de red
1. En la página de inicio rápido, haga clic en **Asignar redes**.
2. Seleccione el servidor VMM de origen desde el que desea asignar las redes y, a continuación, el servidor VMM de destino al que se asignarán las redes. Se muestra la lista de redes de origen y sus redes de destino asociadas. En el caso de las redes no asignadas, aparece un valor en blanco.
3. Seleccione una red en **Red en origen** > **Asignar**. El servicio detecta las redes de VM en el servidor de destino y las muestra. Haga clic en el icono de información junto a los nombres de las redes de origen y de destino para ver las subredes para cada de red.

	![Configurar asignación de red](./media/site-recovery-vmm-to-vmm/network-mapping1.png)

4. En el cuadro de diálogo, seleccione una de las redes de máquina virtual desde el servidor VMM de destino.

	![Selección de una red de destino](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

5. Al seleccionar una red de destino, se muestran las nubes protegidas que utilizan la red de origen. También se muestran las redes de destino disponibles asociadas a las nubes que se utilizan para protección. Se recomienda que seleccione una red de destino que esté disponible para todas las nubes que esté utilizando para protección. También puede ir al servidor VMM y modificar las propiedades de la nube para agregar la red lógica que corresponde a la red de máquina virtual que desea elegir.
6. Haga clic en la marca de verificación para completar el proceso de asignación. Un trabajo comienza a realizar un seguimiento del progreso de la asignación. Puede consultarlo en la pestaña **Trabajos**.


## Paso 6: Configuración de la asignación de almacenamiento
De forma predeterminada cuando se replica una máquina virtual en un servidor host de Hyper-V de origen a un servidor host de Hyper-V de destino, los datos replicados se almacenan en la ubicación predeterminada indicada para el host de Hyper-V de destino en el Administrador de Hyper-V. Para obtener más control sobre dónde se almacenan los datos replicados, puede configurar la asignación de almacenamiento de la manera siguiente:



1. Defina las clasificaciones de almacenamiento en los servidores VMM de origen y destino. [Más información](https://technet.microsoft.com/library/gg610685.aspx). Las clasificaciones deben estar disponibles para los servidores host de Hyper-V en las nubes de origen y destino. Las clasificaciones no necesitan tener el mismo tipo de almacenamiento. Por ejemplo puede asignar una clasificación de origen que contenga los recursos compartidos de SMB a una clasificación de destino que contenga CSV.
2. Cuando estén definidas las clasificaciones, podrá crear las asignaciones. Para ello, en la página **Inicio rápido** > **Asignar almacenamiento**.
3. Haga clic en la pestaña **Almacenamiento** > **Asignar clasificaciones de almacenamiento**.
4. En la pestaña **Asignar clasificaciones de almacenamiento**, seleccione las clasificaciones en los servidores VMM de origen y de destino. Guarde la configuración.

	![Selección de una red de destino](./media/site-recovery-vmm-to-vmm/storage-mapping.png)


## Paso 7: Habilitación de la protección de máquinas virtuales
Una vez que los servidores, las nubes y las redes se configuran correctamente, puede habilitar la protección para las máquinas virtuales en la nube.

1. En la pestaña **Máquinas virtuales**, en la nube en la que se encuentra la máquina virtual, haga clic en **Habilitar la protección** > **Agregar máquinas virtuales**.
2. En la lista de máquinas virtuales de la nube, seleccione la que desea proteger.

	![Enable virtual machine protection](./media/site-recovery-vmm-to-vmm/enable-protection.png)

3. Siga el progreso de la acción de habilitación de la protección en la pestaña **Trabajos**, incluida la replicación inicial. La máquina virtual estará preparada para la conmutación por error después de que finalice el trabajo de Finalizar protección. Después de habilitar la protección y replicar las máquinas virtuales, podrá verlas en Azure.

	![Virtual machine protection job](./media/site-recovery-vmm-to-vmm/vm-jobs.png)
	
>[AZURE.NOTE] También puede habilitar la protección de las máquinas virtuales en la consola VMM. Haga clic en **Habilitar protección** en la barra de herramientas de la pestaña **Azure Site Recovery** en las propiedades de la máquina virtual.

### Incorporación de máquinas virtuales existentes

Si tiene máquinas virtuales en VMM que se replican mediante Réplica de Hyper-V, necesitará incorporarlas para la protección de Azure Site Recovery de la forma siguiente:

1. Compruebe que dispone de nubes principales y secundarias. Asegúrese de que el servidor de Hyper-V que hospeda la máquina virtual existente se encuentra en la nube principal y que el servidor de Hyper-V que hospeda la máquina virtual de réplica se encuentra en la nube secundaria. Asegúrese de que ha configurado las opciones de protección para las nubes. La configuración debe coincidir con la configurada para Réplica de Hyper-V. En caso contrario, es posible que la replicación de las máquinas virtuales no funcione según lo esperado.
2. Después habilite la protección de la máquina virtual principal. Azure Site Recovery y VMM se asegurarán de que se detecta el mismo host de réplica y la máquina virtual, y Azure Site Recovery reutilizará y restablecerá la replicación mediante los valores configurados durante la configuración de la nube.


## Prueba de la implementación

Para probar la implementación puede realizar una prueba de conmutación por error para una máquina virtual individual o crear un plan de recuperación que incluya numerosas máquinas virtuales y realizar una conmutación por error de prueba para el plan. La conmutación por error de prueba simula su mecanismo de conmutación por error y recuperación en una red aislada.

### Creación de un plan de recuperación

1. En la pestaña **Planes de recuperación**, haga clic en **Crear plan de recuperación**.
2. Especifique un nombre para el plan de recuperación, y los servidores VMM de origen y destino. El servidor de origen debe tener máquinas virtuales habilitadas para conmutación por error y recuperación. Seleccione **Hyper-V** para ver solo las nubes que están configuradas para la replicación de Hyper-V.

	![Creación de un plan de recuperación](./media/site-recovery-vmm-to-vmm/recovery-plan1.png)

3. En **Seleccionar máquina virtual**, seleccione grupos de replicación. Se seleccionarán todas las máquinas virtuales asociadas al grupo de replicación y se agregarán al plan de recuperación. Estas máquinas virtuales se agregan al grupo predeterminado del plan de recuperación: grupo 1. Puede agregar más grupos si es necesario. Tenga en cuenta que tras la replicación las máquinas virtuales se iniciarán según el orden de los grupos del plan de recuperación.

	![Agregar máquinas virtuales](./media/site-recovery-vmm-to-vmm/recovery-plan2.png)

Cuando se haya creado un plan de recuperación, aparecerá en la lista de la pestaña **Planes de recuperación**.

###Ejecución de una conmutación por error de prueba

1. En la pestaña **Planes de recuperación**, seleccione el plan y haga clic en **Conmutación por error de prueba**.
2. En la página **Confirmar conmutación por error de prueba** seleccione **Ninguno**. Tenga en cuenta que con esta opción habilitada las máquinas virtuales de réplica de conmutación por error no se conectarán a ninguna red. Esto probará que la máquina virtual realiza un conmutación por error de la manera esperada pero no prueba su entorno de red de replicación. Veamos cómo [ejecutar una conmutación por error de prueba](site-recovery-failover.md#run-a-test-failover) para obtener más detalles sobre cómo usar las diferentes opciones de red.
3. La máquina virtual de prueba se creará en el mismo host en el que existe la máquina virtual de réplica. Se ha agregado a la misma nube en la que se encuentra la máquina virtual de réplica.

### Ejecución de un plan de recuperación
Después de la replicación, es posible que la máquina virtual de réplica tenga la misma dirección IP que la máquina virtual principal. Las máquinas virtuales actualizarán el servidor DNS que están utilizando después de iniciarse. También puede agregar un script para actualizar el servidor DNS para garantizar una actualización puntual.

#### Script para recuperar la dirección IP
Ejecute este script de ejemplo para recuperar la dirección IP.

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

#### Script para actualizar DNS
Ejecute este script de ejemplo para actualizar DNS, especificando la dirección IP que ha recuperado con el script de ejemplo anterior.

		string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## Pasos siguientes

Cuando haya ejecutado una conmutación por error de prueba para comprobar que su entorno funciona según lo esperado, [conozca](site-recovery-failover.md) cuáles son los diferentes tipos de conmutaciones por error.


## Información de privacidad para Site Recovery

En esta sección se proporciona información adicional de privacidad del servicio Microsoft Azure Site Recovery ("servicio"). Para ver la declaración de privacidad de los servicios de Microsoft Azure, consulte [Declaración de privacidad de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899)

**Característica: Registro**

- **Qué hace**: registra el servidor en el servicio para que las máquinas virtuales se puedan proteger.
- **Información recopilada**: después de registrar el servicio, recopila, procesa y transmite la información del certificado de administración desde el servidor VMM designado para proporcionar recuperación ante desastres con el nombre de servicio del servidor VMM y el nombre de las nubes de máquinas virtuales en el servidor VMM.
- **Uso de la información**:
	- Certificado de administración: se utiliza para ayudar a identificar y autenticar el servidor VMM registrado para el acceso al Servicio. El servicio utiliza la parte de clave pública del certificado para proteger un token al que solo puede obtener acceso el servidor VMM registrado. El servidor debe utilizar este token para obtener acceso a las características del Servicio.
	- Nombre del servidor VMM: el nombre del servidor VMM es necesario para identificar y comunicarse con el servidor VMM adecuado en donde se ubican las nubes.
	- Nombres de nubes del servidor VMM: el nombre de nube es obligatorio cuando se utiliza la característica de emparejar o desemparejar las nubes del servicio descrita a continuación. Si decide emparejar la nube de un centro de datos principal con otra nube en el centro de datos de recuperación, se muestran los nombres de todas las nubes del centro de datos de recuperación.

- **Opción**: esta información es una parte esencial del proceso de registro del servicio, ya que ayuda al usuario y al servicio a identificar el servidor VMM para el que desea proporcionar protección de Azure Site Recovery, así como para identificar el servidor VMM registrado correcto. Si no desea enviar esta información al servicio, no lo utilice. Si registra el servidor y más tarde desea anular su registro, puede hacerlo eliminando la información del servidor VMM en el portal del Servicio (que es el Portal de Azure).

**Característica: Habilitación de la protección de Azure Site Recovery**

- **Qué hace**: el proveedor de Azure Site Recovery instalado en el servidor VMM es el conducto para comunicarse con el servicio. El proveedor es una biblioteca de vínculos dinámicos (DLL) que se hospeda en el proceso VMM. Después de instalar el proveedor, se habilita la característica "Datacenter Recovery" en la consola de administrador de VMM. Las máquinas virtuales nuevas o existentes en una nube pueden habilitar una propiedad denominada "Datacenter Recovery" para ayudar a proteger la máquina virtual. Una vez que se establece esta propiedad, el proveedor envía el nombre y el identificador de la máquina virtual al Servicio. La protección virtual está habilitada mediante tecnología de replicación de Windows Server 2012 o de Windows Server 2012 R2 Hyper-V. Los datos de la máquina virtual se replican de un host de Hyper-V a otro (normalmente se encuentran en un centro de datos de "recuperación" diferente).

- **Información recopilada**: el servicio recopila, procesa y transmite los metadatos de la máquina virtual, que incluyen el nombre, el identificador, la red virtual y el nombre de la nube a la que pertenece.

- **Uso de la información**: el servicio usa la información anterior para rellenar la información de la máquina virtual en el portal del servicio.

- **Opción**: es una parte esencial del servicio y no se puede desactivar. Si no desea que esta información se envíe al Servicio, no habilite la protección de Azure Site Recovery para ninguna máquina virtual. Tenga en cuenta que todos los datos enviados por el proveedor al Servicio se envían a través de HTTPS.

**Característica: Plan de recuperación**

- **Qué hace**: esta característica le ayuda a crear un plan de coordinación para el centro de datos de "recuperación". Puede definir el orden por el que se deben iniciar las máquinas virtuales o un grupo de máquinas virtuales en el sitio de recuperación. También puede especificar los scripts automatizados que se pueden ejecutar, o cualquier acción manual que se debe emprender, en el momento de la recuperación para cada máquina virtual. Normalmente se activa la conmutación por error (que se describe en la sección siguiente) en el nivel del plan de recuperación para la recuperación coordinada.

- **Información recopilada**: el servicio recopila, procesa y transmite los metadatos para el plan de recuperación, incluidos los metadatos de la máquina virtual y los metadatos de todos los scripts de automatización y las notas de acción manual.

- **Uso de la información**: los metadatos descritos anteriormente se usan para crear el plan de recuperación en el portal del servicio.

- **Opción**: es una parte esencial del servicio y no se puede desactivar. Si no desea que esta información se envíe al Servicio, no cree planes de recuperación en este Servicio.

**Característica: Asignación de red**

- **Qué hace**: esta característica permite asignar la información de red del centro de datos principal al centro de datos de recuperación. Cuando se recuperan las máquinas virtuales en el sitio de recuperación, esta asignación permite establecer la conectividad de red para ellas.

- **Información recopilada**: como parte de la característica de asignación de red, el servicio recopila, procesa y transmite los metadatos de las redes lógicas para cada sitio (principal y centro de datos).

- **Uso de la información**: el servicio usa los metadatos para rellenar el portal del servicio donde puede asignar la información de red.

- **Opción**: es una parte esencial del Servicio y no se puede desactivar. Si no desea que esta información se envíe al Servicio, no utilice la característica de asignación de red.

**Característica :Conmutación por error - Planeada, no planeada, prueba**

- **Qué hace**: esta característica contribuye a la conmutación por error de una máquina virtual de un centro de datos administrados de VMM a otro centro de datos administrados de VMM. La acción de conmutación por error la desencadena el usuario en el portal del Servicio. Entre las posibles razones para una conmutación por error se incluyen un evento no planeado (por ejemplo, en el caso de un desastre natural; un evento planeado (por ejemplo, equilibrio de carga de centro de datos); una conmutación por error de prueba (por ejemplo, un ensayo del plan de recuperación).

El proveedor en el servidor VMM recibe notificación del evento desde el Servicio y ejecuta una acción de conmutación por error en el host de Hyper-V a través de las interfaces de VMM. La conmutación por error real de la máquina virtual desde un host de Hyper-V a otro (normalmente se ejecuta en un centro de datos de "recuperación" diferente) se controla mediante la tecnología de replicación de Windows Server 2012 o de Windows Server 2012 R2 Hyper-V. Una vez finalizada la conmutación por error, el proveedor instalado en el servidor VMM del centro de datos de "recuperación" envía la información de que está todo correcto al Servicio.

- **Información recopilada**: el servicio usa la información anterior para rellenar el estado de la información de la acción de conmutación por error en el portal del servicio.

- **Uso de la información**: el servicio usa la información anterior del modo siguiente:

	- Certificado de administración: se utiliza para ayudar a identificar y autenticar el servidor VMM registrado para el acceso al Servicio. El servicio utiliza la parte de clave pública del certificado para proteger un token al que solo puede obtener acceso el servidor VMM registrado. El servidor debe utilizar este token para obtener acceso a las características del Servicio.
	- Nombre del servidor VMM: el nombre del servidor VMM es necesario para identificar y comunicarse con el servidor VMM adecuado en donde se ubican las nubes.
	- Nombres de nubes del servidor VMM: el nombre de nube es obligatorio cuando se utiliza la característica de emparejar o desemparejar las nubes del servicio descrita a continuación. Si decide emparejar la nube de un centro de datos principal con otra nube en el centro de datos de recuperación, se muestran los nombres de todas las nubes del centro de datos de recuperación.

- **Opción**: es una parte esencial del servicio y no se puede desactivar. Si no desea que esta información se envíe al Servicio, no utilice este Servicio.

<!---HONumber=AcomDC_0218_2016-->