<properties
	pageTitle="Replicación de máquinas virtuales de Hyper-V (en una nube VMM) en un sitio secundario con Azure Site Recovery mediante SAN | Microsoft Azure"
	description="En este artículo se describe cómo replicar máquinas virtuales de Hyper-V entre dos sitios con Azure Site Recovery mediante la replicación de SAN."
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
	ms.date="02/16/2016"
	ms.author="raynew"/>

# Replicación de máquinas virtuales de Hyper-V (en una nube de VMM) en un sitio secundario con Azure Site Recovery mediante SAN

El servicio Azure Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. Para obtener una introducción rápida, lea [¿Qué es Azure Site Recovery?](site-recovery-overview.md).

## Información general

En este artículo se describe cómo implementar Site Recovery para orquestar y automatizar la protección de las máquinas virtuales de Hyper-V que se encuentran en las nubes privadas de System Center Virtual Machine Manager (VMM). En este escenario, las máquinas virtuales se replican de un sitio VMM principal a un sitio VMM secundario mediante Site Recovery y replicación de SAN.

La guía incluye información general y requisitos previos de la implementación. Le guía a través de configuración y habilitación de la replicación en VMM y en el almacén de Site Recovery. También podrá descubrir y clasificar el almacenamiento SAN en VMM, así como aprovisionar LUN y asignar el almacenamiento a los clústeres de Hyper-V. Finaliza comprobando la conmutación por error para asegurarse de que todo funciona según lo esperado.

Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## ¿Por qué replicar con SAN?

Este escenario ofrece las siguientes ventajas:

- Proporciona una solución de replicación escalable de nivel empresarial automatizada por Site Recovery.
- Aprovecha las capacidades de replicación de SAN proporcionadas por asociados de almacenamiento de información empresarial en el almacenamiento de canal de fibra o iSCSI. Consulte nuestros [asociados de almacenamiento de SAN](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).
- Aprovecha su infraestructura SAN existente para proteger aplicaciones críticas implementadas en clústeres de Hyper-V.
- Proporciona compatibilidad con clústeres invitados.
- Garantiza la coherencia de la replicación entre distintos niveles de una aplicación mediante la replicación sincronizada para un bajo RTO y RPO y la replicación no sincronizada para una alta flexibilidad, según las capacidades de matriz de almacenamiento.  
- La integración con VMM proporciona la administración de SAN en la consola VMM y SMI-S en VMM detecta el almacenamiento existente.  

## Arquitectura

Este escenario protege las cargas de trabajo al hacer una copia de seguridad de las máquinas virtuales de Hyper-V desde un sitio de VMM local a otro mediante la replicación de SAN.

![Arquitectura SAN](./media/site-recovery-vmm-san/architecture.png)

Los componentes de este escenario son:

- **Máquinas virtuales locales**: los servidores de Hyper-V locales que se administran en nubes privadas de VMM contienen máquinas virtuales que desea proteger.
- **Servidores VMM locales**: puede ejecutar uno o más servidores VMM en el sitio principal que desea proteger y en el sitio secundario.
- **Almacenamiento SAN**: una matriz de SAN en el sitio principal y una en el sitio secundario
-  **Almacén de Azure Site Recovery**: el almacén coordina y organiza la réplica de datos, la conmutación por error y la recuperación entre sitios locales.
- **Proveedor de Azure Site Recovery**: el proveedor está instalado en cada servidor VMM.

## Antes de comenzar

Asegúrese de que tiene preparados estos requisitos previos:

**Requisitos previos** | **Detalles** 
--- | ---
**Las tablas de Azure**| Necesitará una cuenta de [Microsoft Azure](https://azure.microsoft.com/). Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) sobre los precios de Site Recovery. 
**VMM** | Necesitará al menos un servidor VMM implementado como un servidor físico o virtual independiente o como un clúster virtual. <br/><br/>El servidor VMM debe ejecutar System Center 2012 R2 con las últimas actualizaciones acumulativas.<br/><br/>Necesitará al menos una nube configurada en el servidor VMM principal que quiere proteger, y una nube configurada en el servidor VMM secundario que quiere usar para la protección y la recuperación.<br/><br/>La nube de origen que quiere proteger debe contener uno o más grupos de host VMM.<br/><br/>Todas las nubes de VMM deben tener establecido el perfil de capacidad de Hyper-V.<br/><br/>Obtener más información sobre cómo configurar las nubes de VMM en [Configuración del tejido de nube de VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) y [Walkthrough: Creating private clouds with System Center 2012 SP1 VMM (Tutorial: creación de nubes privadas con System Center 2012 SP1 VMM)](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
**Hyper-V** | Necesitará uno o más clústeres de Hyper-V en los sitios principales y secundarios, así como una o más VM en el clúster de Hyper-V de origen. Los grupos host de VMM en ubicaciones principales y secundarias deberían tener uno o más clústeres de Hyper-V en cada grupo.<br/><br/>Los servidores de Hyper-V host y de destino deben ejecutar al menos Windows Server 2012 con el rol de Hyper-V, además de tener instaladas las actualizaciones más recientes.<br/><br/>Cualquier servidor de Hyper-V que contenga VM que quiera proteger debe estar ubicado en una nube de VMM.<br/><br/>Si está ejecutando Hyper-V en un clúster, tenga en cuenta que ese agente de clúster no se crea automáticamente si tiene un clúster basado en una dirección IP estática. Tendrá que configurar manualmente el agente de clúster. [Puede encontrar más información](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) en la entrada de blog de Aidan Finn.
**Almacenamiento SAN** | Mediante la replicación de SAN puede replicar máquinas virtuales con clúster de invitado con iSCSI o almacenamiento de canal de fibra o mediante discos duros virtuales compartidos (vhdx).<br/><br/>Deberá configurar dos matrices de SAN, una en el sitio principal y otra en el secundario.<br/><br/>La infraestructura de red debe configurarse entre las matrices. Es necesario configurar la replicación y el emparejamiento. Las licencias de replicación deben configurarse de acuerdo con los requisitos de la matriz de almacenamiento.<br/><br/>Las funciones de red deben configurarse entre los servidores host de Hyper-V y la matriz de almacenamiento para que los hosts puedan comunicarse con los LUN de almacenamiento mediante ISCSI o Canal de fibra.<br/><br/> Échele un vistazo a la lista de [matrices de almacenamiento admitidas](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).<br/><br/>Es necesario instalar los proveedores de SMI-S proporcionados por los fabricantes de matrices de almacenamiento; asimismo, el proveedor deberá administrar las matrices de SAN. Configure el proveedor de acuerdo con su documentación.<br/><br/>Asegúrese de que el proveedor de SMI-S para la matriz esté en un servidor al que el servidor VMM pueda tener acceso mediante la red por dirección IP o FQDN.<br/><br/>Cada matriz de SAN debe tener uno o más grupos de almacenamiento disponibles para su uso en esta implementación. El servidor VMM del sitio principal necesitará administrar la matriz principal y el servidor VMM secundario administrará la matriz secundaria.<br/><br/>El servidor VMM del sitio principal debe administrar la matriz principal y el servidor VMM secundario debe administrar la matriz secundaria.
**Asignación de red** | Puede configurar la asignación de red para asegurarse de que las máquinas virtuales replicadas se colocan de manera óptima en los servidores host de Hyper-V secundarios tras la conmutación por error y que se pueden conectar a las redes de VM adecuadas. Si no configura la asignación de red, las VM de réplica no se conectarán a ninguna red después de la conmutación por error.<br/><br/>Para configurar la asignación de red durante la implementación, asegúrese de que las máquinas virtuales del servidor host de Hyper-V de origen estén conectadas a una red de VM de VMM. Dicha red debería estar vinculada a una red lógica que esté asociada a la nube.<br/<br/>La nube de destino en el servidor VMM secundario que se utiliza para la recuperación debe tener configurada una red de VM correspondiente y, a su vez, debe estar vinculada a una red lógica correspondiente que esté asociada a la nube de destino.<br/><br/>[Más información](site-recovery-network-mapping.md) sobre la asignación de red.


## Paso 1: Preparación de la infraestructura de VMM

Para preparar la infraestructura de VMM debe:

1. Asegurarse de que las nubes de VMM están configuradas
2. Integrar y clasificar el almacenamiento SAN en VMM
3. Crear LUN y asignar almacenamiento
4. Crear grupos de replicación
5. Configurar redes de máquinas virtuales

### Asegurarse de que las nubes de VMM están configuradas

Site Recovery organiza la protección para máquinas virtuales ubicadas en servidores host de Hyper-V en nubes de VMM. Deberá asegurarse de que esas nubes están configuradas correctamente antes de empezar la implementación de Site Recovery. Más información en [Creating private clouds](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) (Creación de nubes privadas) en el blog de Keith Mayer.

### Integrar y clasificar el almacenamiento SAN en VMM

Agregue y clasifique redes SAN en la consola VMM:

1. En el área de trabajo **Tejido**, haga clic en **Almacenamiento**. Haga clic en **Inicio** > **Agregar recursos** > **Dispositivos de almacenamiento** para iniciar el Asistente para agregar dispositivos de almacenamiento.
2. En **Seleccionar un tipo de proveedor de almacenamiento**, seleccione **Dispositivos NAS y SAN detectados y administrados con un proveedor de SMI-S**.

	![Tipo de proveedor](./media/site-recovery-vmm-san/provider-type.png)

3. En la página **Especificar el protocolo y la dirección del proveedor de SMI-S de almacenamiento**, seleccione **SMI-S CIMXMLS** y especifique la configuración para conectar con el proveedor.
4. En **Dirección IP o FQDN del proveedor** y **Puerto TCP/IP**, especifique la configuración para conectar con el proveedor. Solo puede utilizar una conexión SSL para SMI-S CIMXML.

	![Conexión de proveedor](./media/site-recovery-vmm-san/connect-settings.png)

5. En **Cuenta de ejecución**, especifique una cuenta de ejecución de VMM que pueda tener acceso al proveedor, o bien cree una cuenta nueva.
6. En la página **Recopilar información**, VMM intenta detectar e importar la información del dispositivo de almacenamiento de forma automática. Para volver a intentar la detección, haga clic en **Proveedor de examen**. Si el proceso de detección se realiza correctamente, se mostrarán en la página las matrices de almacenamiento detectadas, los grupos de almacenamiento, el fabricante, el modelo y la capacidad.

	![Detección de almacenamiento](./media/site-recovery-vmm-san/discover.png)

7. En **Seleccionar grupos de almacenamiento para administrar y asignar una clasificación**, seleccione los grupos de almacenamiento que VMM va a administrar y asigne una clasificación. La información de LUN se importará desde los grupos de almacenamiento. Cree LUN en función de las aplicaciones que necesita proteger, de sus requisitos de capacidad y de las necesidades de replicación.

	![Clasificación de almacenamiento](./media/site-recovery-vmm-san/classify.png)

### Crear LUN y asignar almacenamiento

1. Una vez integrado el almacenamiento SAN en el VMM, deberá crear (aprovisionar) las unidades lógicas (LUN).

	- [Selección de un método para crear unidades lógicas en VMM](https://technet.microsoft.com/library/gg610624.aspx)
	- [Aprovisionamiento de unidades lógicas de almacenamiento en VMM](https://technet.microsoft.com/library/gg696973.aspx)

2. A continuación, asigne la capacidad de almacenamiento al clúster del host de Hyper-V para que VMM pueda implementar datos de las máquinas virtuales en el almacenamiento aprovisionado:

	- Antes de asignar el almacenamiento al clúster, deberá asignarlo al grupo de host de VMM en el que reside el clúster. Consulte [Asignación de unidades lógicas de almacenamiento a un grupo de host](https://technet.microsoft.com/library/gg610686.aspx) y [Asignación de grupos de almacenamiento a un grupo de host](https://technet.microsoft.com/library/gg610635.aspx) </a>.
	- A continuación, asigne capacidad de almacenamiento al clúster, tal como se describe en [Configuración del almacenamiento en un clúster de host de Hyper-V en VMM](https://technet.microsoft.com/library/gg610692.aspx) </a>.

### Crear grupos de replicación

Cree un grupo de replicación que incluya todas las LUN necesarias para la replicación conjunta.

1. En la consola VMM, abra la pestaña **Grupos de replicación** de las propiedades de la matriz de almacenamiento y haga clic en **Nuevo**.
2. A continuación, cree el grupo de replicación.

	![Grupo de replicación de SAN](./media/site-recovery-vmm-san/rep-group.png)

### Configuración de redes

Si desea configurar la asignación de redes, haga lo siguiente:

1. [Más información sobre](site-recovery-network-mapping.md) la asignación de red.
2. Prepare las redes de máquinas virtuales en VMM:

	- [Configure redes lógicas](https://technet.microsoft.com/library/jj721568.aspx).
	- [Configure redes de máquinas virtuales](https://technet.microsoft.com/library/jj721575.aspx).

## Paso 2: Creación de un almacén


1. Inicie sesión en el [Portal de administración](https://portal.azure.com) desde el servidor VMM que desee registrar.

2. Expanda **Servicios de datos** > **Servicios de recuperación** y haga clic en **Almacén de Site Recovery**.

3. Haga clic en **Crear nuevo** > **Creación rápida**.

4. En **Nombre**, escriba un nombre descriptivo para identificar el almacén.

5. En **Región**, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

6. Haga clic en **Crear almacén**.

	![Almacén nuevo](./media/site-recovery-vmm-san/create-vault.png)

Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como **Activo** en la página principal de **Servicios de recuperación**.


### Registro de los servidores VMM

1. Abra la página Inicio rápido desde la página **Servicios de recuperación**. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

	![Icono de inicio rápido](./media/site-recovery-vmm-san/quick-start-icon.png)

2. En la lista desplegable, seleccione **Entre sitios locales de Hyper-V mediante la replicación de la matriz**.

	![Clave de registro](./media/site-recovery-vmm-san/select-san.png)


3. En **Preparar servidores VMM**, descargue la versión más reciente del archivo de instalación del proveedor de Azure Site Recovery.
4. Ejecute este archivo en el servidor VMM de origen. Si VMM está implementado en un clúster y va a instalar el proveedor por primera vez, instálelo en un nodo activo y finalice la instalación para registrar el servidor VMM en el almacén. A continuación, instale el proveedor en los demás nodos. Tenga en cuenta que si está actualizando el proveedor, tendrá que actualizarlo en todos los nodos, ya que deben ejecutar la misma versión del proveedor.
5. El instalador realiza algunas operaciones de **Comprobación de requisitos previos** y solicita permiso para detener el inicio de la instalación del proveedor por parte del servicio VMM. El servicio VMM se reiniciará automáticamente cuando finalice la instalación. Si va a instalarlo en un clúster VMM, se le pedirá que detenga el rol de clúster.
6. En **Microsoft Update** puede optar por recibir actualizaciones. Con esta configuración habilitada, las actualizaciones del proveedor se instalarán según su directiva de Microsoft Update.

	![Microsoft Updates](./media/site-recovery-vmm-san/ms-update.png)

7. La ubicación de instalación está establecida en **<SystemDrive>\\Program Files\\Microsoft System Center 2012 R2\\Virtual Machine Manager\\bin**. Haga clic en el botón Instalar para iniciar la instalación del proveedor.

	![InstallLocation](./media/site-recovery-vmm-san/install-location.png)

8. Una vez instalado el proveedor, haga clic en el botón "Registrar" para registrar el servidor en el almacén.

	![InstallComplete](./media/site-recovery-vmm-san/install-complete.png)

9. En **Conexión a Internet**, especifique cómo se conecta a Internet el proveedor que se ejecuta en el servidor VMM. Seleccione **Utilizar la configuración proxy del sistema predeterminado** para usar la configuración predeterminada de conexión a Internet establecida en el servidor.

	![Configuración de Internet](./media/site-recovery-vmm-san/proxy-details.png)

	- Si desea utilizar un proxy personalizado, debe configurarlo antes de instalar el proveedor. Al configurar las opciones del proxy personalizado, se ejecuta una prueba para comprobar la conexión del proxy.
	- Si utiliza a un proxy personalizado o el proxy predeterminado requiere autenticación, tendrá que especificar los detalles del proxy, incluida la dirección y el puerto del proxy.
	- Las siguientes direcciones URL deben ser accesibles desde el servidor VMM y los hosts de Hyper-v
		- *.hypervrecoverymanager.windowsazure.com
		- *.accesscontrol.windows.net
		- *.backup.windowsazure.com
		- *.blob.core.windows.net
		- *.store.core.windows.net
	- Permita las direcciones IP que se describen en [Intervalos de direcciones IP de los centros de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el protocolo HTTPS (443). Tendrá que incluir en una lista blanca los intervalos de direcciones IP de la región de Azure que va a usar y los del Oeste de EE. UU.
	- Si utiliza un proxy personalizado, se creará una cuenta de ejecución de VMM (DRAProxyAccount) mediante el uso automático de las credenciales de proxy especificadas. Configure el servidor proxy para que esta cuenta pueda autenticarse correctamente. La configuración de la cuenta de ejecución de VMM puede modificarse en la consola VMM. Para ello, abra el área de trabajo Configuración, expanda Seguridad, haga clic en Cuentas de ejecución y, a continuación, modifique la contraseña de DRAProxyAccount. Deberá reiniciar el servicio VMM para que esta configuración surta efecto.

10. En **Clave de registro**, seleccione lo que ha descargado de Azure Site Recovery y copiado en el servidor VMM.
11. En **Nombre del almacén**, compruebe el nombre del almacén en el que se registrará el servidor. 

	![Registro de servidor](./media/site-recovery-vmm-san/vault-creds.png)

12. La configuración solo se usa para el escenario de VMM a Azure. Si usa únicamente la opción de VMM a VMM, puede omitir esta pantalla.

	![Registro de servidor](./media/site-recovery-vmm-san/encrypt.png)

13. En **Nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén. En una configuración de clúster, especifique el nombre del rol de clúster VMM.
14. En **Sincronización inicial de metadatos de nube**, especifique un nombre descriptivo para el servidor que aparecerá en el almacén y seleccione si quiere sincronizar los metadatos de todas las nubes del servidor VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM.

	![Registro de servidor](./media/site-recovery-vmm-san/friendly-name.png)

15. Haga clic en **Next** para finalizar el proceso. Después del registro, la Recuperación del sitio de Azure recupera los metadatos del servidor VMM. El servidor se muestra en la pestaña *Servidores VMM* de la página **Servidores** del almacén.

### Instalación de la línea de comandos

El proveedor de Azure Site Recovery también puede instalarse mediante la siguiente línea de comandos. Este método se puede usar para instalar el proveedor en un Server CORE para Windows Server 2012 R2.

1. Descargue el archivo de instalación del proveedor y la clave de registro en una carpeta, por ejemplo, C:\\ASR.
2. Detenga el servicio System Center Virtual Machine Manager.
3. Ejecute los siguientes comandos con privilegios de **Administrador** desde el símbolo del sistema para extraer el instalador del proveedor:

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Instale el proveedor ejecutando lo siguiente:

		C:\ASR> setupdr.exe /i

5. Registre el proveedor ejecutando lo siguiente:

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


## Paso 3: Asignación de grupos y matrices de almacenamiento

Asigne las matrices para especificar qué grupo de almacenamiento secundario recibe datos de replicación desde el grupo principal. Debe asignar el almacenamiento antes de configurar la protección de la nube, ya que la información de asignación se utiliza cuando se habilita la protección de los grupos de replicación.

Antes de iniciar el proceso, asegúrese de que las nubes aparecen en el almacén. Las nubes se detectan mediante la selección de la sincronización de todas las nubes durante la instalación del proveedor, o mediante la sincronización de una nube específica en la pestaña **General** de las propiedades de la nube en la consola VMM. A continuación, asigne las matrices de almacenamiento de la forma siguiente:

1. Haga clic en **Recursos** > **Almacenamiento del servidor** > **Asignar matrices de origen y destino**. ![Registro de servidor](./media/site-recovery-vmm-san/storage-map.png)
2. Seleccione las matrices de almacenamiento en el sitio principal y asígnelas a las matrices de almacenamiento en el sitio secundario.
3.  Asigne los grupos de almacenamiento de origen y de destino en las matrices. Para ello, en **Grupos de almacenamiento**, seleccione un grupo de almacenamiento de origen y de destino para asignar.

	![Registro de servidor](./media/site-recovery-vmm-san/storage-map-pool.png)

## Paso 4: Configuración de la protección de la nube

Una vez que los servidores VMM están registrados, puede configurar la protección de la nube. La opción **Sincronizar datos de nube con el almacén** se habilita cuando se instala el proveedor, de modo que todas las nubes del servidor VMM aparecerán en la pestaña <b>Elementos protegidos</b> del almacén.

![Nube publicada](./media/site-recovery-vmm-san/clouds-list.png)

1. En la página Inicio rápido, haga clic en **Configurar protección para nubes de VMM**.
2. En la pestaña **Elementos protegidos**, seleccione la nube que desea configurar y vaya a la pestaña **Configuración**. Observe lo siguiente:
3. En <b>Destino</b>, seleccione <b>VMM</b>.
4. En <b>Ubicación de destino</b>, seleccione el servidor VMM local que administra la nube que desea utilizar para recuperación.
5. En <b>Nube de destino</b>, seleccione la nube de destino que desea utilizar para la conmutación por error de máquinas virtuales en la nube de origen. Observe lo siguiente:
	- Recomendamos seleccionar una nube de destino que cumpla con los requisitos de recuperación para las máquinas virtuales que protegerá.
	- Una nube solo puede pertenecer a un único par de nubes, ya sea como nube principal o de destino.
6. Azure Site Recovery comprueba que las nubes tienen acceso al almacenamiento compatible con la replicación de SAN y que las matrices de almacenamiento están emparejadas. Se mostrarán las parejas de matrices participantes.
7. Si la comprobación se realiza correctamente, en **Tipo de replicación,** seleccione **SAN**.

<p>Tras guardar la configuración, se creará un trabajo que se podrá supervisar en la pestaña <b>Trabajos</b>. Puede modificar la configuración de la nube en la pestaña <b>Configurar</b>. Si desea modificar la ubicación de destino o la nube de destino, deberá eliminar la configuración de la nube y después volver a configurarla.</p>

## Paso 5: habilitación de la asignación de redes

1. En la página de inicio rápido, haga clic en **Asignar redes**.
2. Seleccione el servidor VMM de origen desde el que desea asignar las redes y, a continuación, el servidor VMM de destino al que se asignarán las redes. Se muestra la lista de redes de origen y sus redes de destino asociadas. En el caso de las redes no asignadas, aparece un valor en blanco. Haga clic en el icono de información junto a los nombres de las redes de origen y de destino para ver las subredes para cada de red.
3. Seleccione una red en **Red en origen** > **Asignar**. El servicio detecta las redes de VM en el servidor de destino y las muestra.

	![Arquitectura SAN](./media/site-recovery-vmm-san/network-map1.png)

4. En el cuadro de diálogo que se muestra, seleccione una de las redes de máquina virtual desde el servidor VMM de destino.

	![Arquitectura SAN](./media/site-recovery-vmm-san/network-map2.png)

5. Al seleccionar una red de destino, se muestran las nubes protegidas que utilizan la red de origen. También se muestran las redes de destino disponibles asociadas a las nubes que se utilizan para protección. Se recomienda que seleccione una red de destino que esté disponible para todas las nubes que esté utilizando para protección.
6.  Haga clic en la marca de verificación para completar el proceso de asignación. Un trabajo comienza a realizar un seguimiento del progreso de la asignación. Puede consultarlo en la pestaña **Trabajos**.


## Paso 6: Habilitación de la replicación para los grupos de replicación</h3>

: para poder habilitar la protección de máquinas virtuales deberá habilitar la replicación para grupos de almacenamiento de replicación.

1. En el portal de Azure Site Recovery, en la página de propiedades de la nube principal, abra la pestaña **Máquinas virtuales**. Haga clic en **Agregar grupo de replicación**.
2. Seleccione uno o más grupos de replicación de VMM asociados con la nube, compruebe las matrices de origen y de destino y especifique la frecuencia de replicación.

Una vez completada esta operación, Azure Site Recovery junto con VMM y los proveedores de SMI-S aprovisionarán las LUN de almacenamiento del sitio de destino y se habilitará la replicación de almacenamiento. Si el grupo de replicación ya está replicado, Azure Site Recovery reutilizará la relación de replicación existente y actualizará la información de Azure Site Recovery.

## Paso 7: Habilitación de la protección para las máquinas virtuales

Cuando la replicación del grupo de almacenamiento esté en curso, habilite la protección para máquinas virtuales en la consola VMM mediante cualquiera de los métodos siguientes:

- **Nueva máquina virtual**: en la consola VMM, cuando se crea una nueva máquina virtual se habilita la protección de Azure Site Recovery y se asocia la máquina virtual al grupo de replicación. Con esta opción, VMM usa la ubicación inteligente para colocar de forma óptima el almacenamiento de la máquina virtual en las LUN del grupo de replicación. Azure Site Recovery organiza la creación de una máquina virtual de instantáneas en el sitio secundario y asigna capacidad para que las máquinas virtuales de réplica se puedan iniciar tras la conmutación por error.
- **Máquina virtual existente**: en caso de que la máquina virtual ya esté implementada en VMM, puede habilitar la protección de Azure Site Recovery y realizar una migración del almacenamiento a un grupo de replicación. Tras finalizar este proceso, VMM y Azure Site Recovery detectarán la nueva máquina virtual y comenzarán a administrarla en Azure Site Recovery para ofrecer protección. En el sitio secundario se creará una máquina virtual de instantáneas y se asignará capacidad suficiente para que se pueda iniciar la máquina virtual de réplica tras la conmutación por error.

	![Habilitar protección](./media/site-recovery-vmm-san/enable-protect.png)

Tras habilitar las máquinas virtuales para su protección, estas aparecerán en la consola de Azure Site Recovery. Puede ver las propiedades de la máquina virtual, realizar un seguimiento de estado y conmutar por error los grupos de replicación que contienen varias máquinas virtuales. Tenga en cuenta que en la replicación de SAN, todas las máquinas virtuales asociadas a un grupo de replicación deberán realizar la conmutación por error de manera conjunta. Esto es porque la conmutación por error se realiza en la capa de almacenamiento en primer lugar. Es importante agrupar correctamente los grupos de replicación y colocar solo las máquinas virtuales asociadas juntas.

Puede seguir el progreso de la acción de habilitación de la protección en la pestaña **Trabajos**, incluida la replicación inicial. La máquina virtual estará preparada para la conmutación por error después de que finalice el trabajo de Finalizar protección.

![Virtual machine protection job](./media/site-recovery-vmm-san/job-props.png)

## Paso 8: Prueba de la implementación

Pruebe la implementación para asegurarse de que la conmutación por error de las máquinas virtuales y los datos se realiza de la manera esperada. Para ello, creará un plan de recuperación seleccionando los grupos de replicación. A continuación, ejecute una conmutación por error de prueba en el plan.

1. En la pestaña **Planes de recuperación**, haga clic en **Crear plan de recuperación**.
2. Especifique un nombre para el plan de recuperación, y los servidores VMM de origen y destino. El servidor de origen debe tener máquinas virtuales habilitadas para conmutación por error y recuperación. Seleccione **SAN** para ver solo las nubes configuradas para la replicación de SAN.
3.
	![Creación de un plan de recuperación](./media/site-recovery-vmm-san/r-plan.png)

4. En **Seleccionar máquina virtual**, seleccione grupos de replicación. Se seleccionarán todas las máquinas virtuales asociadas al grupo de replicación y se agregarán al plan de recuperación. Estas máquinas virtuales se agregan al grupo predeterminado del plan de recuperación: grupo 1. Puede agregar más grupos si es necesario. Tenga en cuenta que tras la replicación las máquinas virtuales se iniciarán según el orden de los grupos del plan de recuperación.

	![Agregar máquinas virtuales](./media/site-recovery-vmm-san/r-plan-vm.png)
5. Cuando se haya creado un plan de recuperación, aparecerá en la lista de la pestaña **Planes de recuperación**.
6. En la pestaña **Planes de recuperación**, seleccione el plan y haga clic en **Conmutación por error de prueba**.
7. En la página **Confirmar conmutación por error de prueba** seleccione **Ninguno**. Tenga en cuenta que con esta opción habilitada las máquinas virtuales de réplica de conmutación por error no se conectarán a ninguna red. Esto probará que la máquina virtual realiza un conmutación por error de la manera esperada pero no prueba su entorno de red de replicación. Veamos cómo [ejecutar una conmutación por error de prueba](site-recovery-failover.md#run-a-test-failover) para obtener más detalles sobre cómo usar las diferentes opciones de red.


	![Selección de la red de prueba](./media/site-recovery-vmm-san/test-fail1.png)


8. La máquina virtual de prueba se creará en el mismo host en el que existe la máquina virtual de réplica. No se agregará a la nube en la que se encuentra la máquina virtual de réplica.
9. Después de la replicación, la máquina virtual de réplica tendrá una dirección IP que no es la misma que la dirección IP de la máquina virtual principal. Si está emitiendo direcciones de DHCP, a continuación, se actualizará automáticamente. Si no utiliza DHCP y desea asegurarse de que las direcciones son las mismas, deberá ejecutar un par de scripts.
10. Ejecute este script de ejemplo para recuperar la dirección IP.

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

11. Ejecute este script de ejemplo para actualizar DNS, especificando la dirección IP que ha recuperado con el script de ejemplo anterior.

		[string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## Pasos siguientes

Cuando haya ejecutado una conmutación por error de prueba para comprobar que su entorno funciona según lo esperado, [conozca](site-recovery-failover.md) cuáles son los diferentes tipos de conmutaciones por error.

<!---HONumber=AcomDC_0218_2016-->