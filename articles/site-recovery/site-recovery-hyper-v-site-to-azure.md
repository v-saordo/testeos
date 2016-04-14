<properties
	pageTitle="Replicación ente máquinas virtuales de Hyper-V local y Azure (sin VMM) con Site Recovery | Microsoft Azure"
	description="En este artículo se describe cómo replicar máquinas virtuales de Hyper-V en Azure con Azure Site Recovery cuando las máquinas no se administran en nubes de VMM."
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
	ms.workload="storage-backup-recovery"
	ms.date="02/16/2016"
	ms.author="raynew"/>


# Replicación entre máquinas virtuales de Hyper-V local y Azure (sin VMM) con Azure Site Recovery

El servicio Azure Site Recovery contribuye a su estrategia de continuidad empresarial y recuperación ante desastres (BCDR) mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales y servidores físicos. Las máquinas se pueden replicar a Azure o a un centro de datos secundario local. Para obtener una introducción rápida, lea [¿Qué es Azure Site Recovery?](site-recovery-overview.md).

## Información general

En este artículo se describe cómo implementar Site Recovery para replicar máquinas virtuales de Hyper-V cuando los hosts de Hyper-V no se administran en nubes de System Center Virtual Machine Manager (VMM).

El artículo resume los requisitos previos de implementación, ayuda a configurar la replicación y habilita la protección para máquinas virtuales. Finaliza comprobando la conmutación por error para asegurarse de que todo funciona según lo esperado.


Publique cualquier comentario o pregunta que tenga en la parte inferior de este artículo, o bien en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Antes de comenzar

Asegúrese de que tiene todo lo que necesita antes de comenzar.

### Requisitos previos de Azure

- Necesitará una cuenta de [Microsoft Azure](https://azure.microsoft.com/). Puede comenzar con una [evaluación gratuita](pricing/free-trial/).
- Necesitará una cuenta de almacenamiento de Azure para almacenar los datos replicados. La cuenta debe tener habilitada la replicación geográfica. Además, debe estar en la misma región que el almacén de Azure Site Recovery y estar asociada a la misma suscripción. [Más información sobre Almacenamiento de Azure](../storage/storage-introduction.md).
- Necesitará una red virtual de Azure para que las máquinas virtuales de Azure se conecten a una red al conmutar por error desde el sitio principal.

### Requisitos previos de Hyper-V

- En el sitio local de origen necesitará al menos un servidor que ejecute Windows Server 2012 R2 con el rol de Hyper-V instalado. Este servidor debe:
- Contener una o más máquinas virtuales.
- Estar conectado a Internet, directamente o a través de un proxy.
- Ejecutar las revisiones descritas en el artículo de KB [2961977](https://support.microsoft.com/es-ES/kb/2961977 "KB2961977").

### Requisitos previos de las máquinas virtuales

Las máquinas virtuales que quiera proteger deben cumplir los [requisitos previos de máquinas virtuales](site-recovery-best-practices.md#virtual-machines).

### Requisitos previos del proveedor y del agente

Como parte de la implementación de Azure Site Recovery, instalará el proveedor de Azure Site Recovery y el Agente de Servicios de recuperación de Azure en cada servidor de Hyper-V. Observe lo siguiente:

- Se recomienda que siempre ejecute las versiones más recientes del proveedor y del agente. Puede encontrarlas en el portal de Site Recovery.
- Todos los servidores de Hyper-V de un almacén deben tener las mismas versiones del proveedor y del agente.
- El proveedor que se ejecuta en el servidor conecta con Site Recovery a través de Internet. Dicha conexión se puede realizar sin un proxy, con la configuración de proxy actualmente definida en el servidor de Hyper-V o con la configuración de proxy personalizada que defina durante la instalación del proveedor. Debe asegurarse de que el servidor proxy que desea utilizar pueda tener acceso a estas direcciones URL para conectarse a Azure:
	- *.hypervrecoverymanager.windowsazure.com
	- *.accesscontrol.windows.net
	- *.backup.windowsazure.com
	- *.blob.core.windows.net
	- *.store.core.windows.net
	
- Además, permita las direcciones IP que se describen en [Intervalos de direcciones IP de los centros de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) y el protocolo HTTPS (443). Tendrá que incluir en una lista blanca los intervalos de direcciones IP de la región de Azure que va a usar y los del Oeste de EE. UU.


En el gráfico siguiente se muestran canales y puertos de comunicación diferentes que usa Site Recovery para la orquestación y la replicación.

![Topología B2A](./media/site-recovery-hyper-v-site-to-azure/B2ATopology.png)


## Paso 1: Creación de un almacén

1. Inicie sesión en el [Portal de administración](https://portal.azure.com).


2. Expanda **Servicios de datos** > **Servicios de recuperación** y haga clic en **Almacén de Site Recovery**.


3. Haga clic en **Crear nuevo** > **Creación rápida**.

4. En **Nombre**, escriba un nombre descriptivo para identificar el almacén.

5. En **Región**, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](pricing/details/site-recovery/).

6. Haga clic en **Crear almacén**.

	![Almacén nuevo](./media/site-recovery-hyper-v-site-to-azure/SR_HvVault.png)

Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como **Activo** en la página principal de Servicios de recuperación.


## Paso 2: Creación de un sitio de Hyper-V

1. En la página Servicios de recuperación, haga clic en el almacén para abrir la página de inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

	![Inicio rápido](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_QuickStartIcon.png)

2. En la lista desplegable, seleccione **Entre un sitio de Hyper-V local y Azure**.

	![Escenario de sitio de Hyper-V](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_SelectScenario.png)

3. En **Crear un sitio de Hyper-V** haga clic en **Crear sitio de Hyper-V**. Especifique un nombre de sitio y guarde.

	![Sitio de Hyper-V](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_CreateSite2.png)


## Paso 3: Instalación del proveedor y del agente
Instale el proveedor y el agente en cada servidor de Hyper-V que tenga máquinas virtuales que desee proteger.

Si va a instalar en un clúster de Hyper-V, realice los pasos 5-11 en cada nodo del clúster de conmutación por error. Una vez registrados todos los nodos y habilitada la protección, las máquinas virtuales estarán protegidas incluso si se migran a través de los nodos del clúster.

1. En **Preparar servidores Hyper-V**, haga clic en **Descargar una clave de registro**.
2. En la página **Descargar clave de registro**, haga clic en **Descargar junto al sitio**. Descargue la clave en una ubicación segura a la que Hyper-V pueda acceder con facilidad. La clave será válida para 5 días a partir del momento en que se genera.

	![Clave de registro](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_DownloadKey2.png)

4. Haga clic en **Descargar proveedor** para obtener la versión más reciente.
5. Ejecute el archivo en cada servidor de Hyper-V que desee registrar en el almacén. El archivo instala dos componentes:
	- **Proveedor de Azure Site Recovery**: controla la comunicación y la coordinación entre el servidor de Hyper-V y el portal de Azure Site Recovery.
	- **Agente de los Servicios de recuperación de Azure**: controla el transporte de datos entre las máquinas virtuales que se ejecutan en el servidor de Hyper-V de origen y el almacenamiento de Azure.
6. En **Microsoft Update** puede optar por recibir actualizaciones. Con esta opción habilitada, las actualizaciones del proveedor y del agente se instalarán según la directiva de Microsoft Update.

	![Microsoft Updates](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider1.png)

7. En **Instalación** especifique dónde desea instalar el proveedor y el agente en el servidor de Hyper-V.

	![Ubicación de instalación](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider2.png)

8. Una vez se complete la instalación, continúe con la instalación para registrar el servidor en el almacén.

	![Instalación completada](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider3.png)


9. En la página **Conexión a Internet**, especifique cómo se conecta el proveedor a Azure Site Recovery. Seleccione **Utilizar la configuración proxy del sistema predeterminado** para usar la configuración predeterminada de conexión a Internet establecida en el servidor. Si no especifica un valor, se utilizará la configuración predeterminada.

	![Configuración de Internet](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider4.png)

9. En la página **Configuración de almacén**, haga clic en **Examinar** para seleccionar el archivo de clave. Especifique la suscripción de Azure Site Recovery, el nombre del almacén y el sitio de Hyper-V al que pertenece el servidor Hyper-V.

	![Registro de servidor](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_SelectKey.png)


11. El registro empieza a registrar el servidor en el almacén.

	![Registro de servidor](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider6.png)

11. Después de que finalice el registro, los metadatos de Hyper-V Server se recuperan mediante Azure Site Recovery y el servidor se muestra en la pestaña **Sitios de Hyper-V** de la página **Servidores** del almacén.

	![Registro de servidor](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_Provider7.png)


### Instalación del proveedor desde la línea de comandos

Como alternativa, puede instalar al proveedor de Azure Site Recovery desde la línea de comandos. Utilice este método si desea instalar al proveedor en un equipo que ejecuta Windows Server Core 2012 R2. Para ejecutar desde la línea de comandos:

1. Descargue el archivo de instalación del proveedor y la clave de registro en una carpeta. Por ejemplo, C:\\ASR.
2. Ejecute un símbolo del sistema como administrador y escriba:

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

3. A continuación, instale el proveedor mediante la ejecución del comando:

		C:\ASR> setupdr.exe /i

4. Ejecute el siguiente comando para completar el registro:

    	CD C:\Program Files\Microsoft Azure Site Recovery Provider
    	C:\Program Files\Microsoft Azure Site Recovery Provider> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>         

Los parámetros son:

- **/Credentials**: especifique la ubicación de la clave de registro que se ha descargado.
- **/FriendlyName**: especifique un nombre para identificar el servidor host de Hyper-V. Este nombre aparecerá en el portal.
- **/EncryptionEnabled**: opcional. Especifique si desea cifrar máquinas virtuales de réplica en Azure (en cifrado en reposo).
- **/proxyAddress**; **/proxyport**; **/proxyUsername**; **/proxyPassword**: opcional. Especifique los parámetros de proxy si desea usar un proxy personalizado o el proxy existente requiere autenticación.


## Paso 4: Creación de una cuenta de almacenamiento de Azure 

1. En **Preparar recursos**, seleccione **Crear cuenta de almacenamiento** para crear una cuenta de almacenamiento de Azure si no tiene una. La cuenta debe tener habilitada la replicación geográfica. Además, debe estar en la misma región que el almacén de Azure Site Recovery y estar asociada a la misma suscripción.

	![Crear una cuenta de almacenamiento](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_CreateResources1.png)

## Paso 5: Creación y configuración de grupos de protección

Los grupos de protección son agrupaciones lógicas de máquinas virtuales que desea proteger con la misma configuración de protección. Aplique la configuración de protección a un grupo de protección y esa configuración se aplicará a todas las máquinas virtuales que agregue al grupo.

1. En **Crear y configurar grupos de protección**, haga clic en **Crear un grupo de protección**. Si no están establecidos los requisitos previos, se emitirá un mensaje y podrá hacer clic en **Ver detalles** para obtener más información.

2. En la pestaña **Grupos de protección**, agregue un grupo de protección. Especifique un nombre, el sitio de Hyper-V de origen, el **Azure** de destino, el nombre de la suscripción de Azure Site Recovery y la cuenta de almacenamiento de Azure.

	![Grupo de protección](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroupCreate3.png)


2. En **Configuración de la replicación**, establezca **Copiar frecuencia** para que especifique la frecuencia a la que se deben sincronizar los datos delta entre el origen y el destino. Puede establecerlo en 30 segundos, 5 minutos o 15 minutos.
3. En **Conservar puntos de recuperación**, especifique el número de horas del historial de recuperación que se deben almacenar.
4. En **Frecuencia de las instantáneas coherentes con la aplicación**, puede especificar si desea tomar instantáneas que utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente al capturar la instantánea. De forma predeterminada, estas no se capturan. Asegúrese de que el valor establecido es menor que el número de puntos de recuperación adicionales configurados. Esto solo se admite si la máquina virtual está ejecutándose en un sistema operativo Windows.
5. En **Hora de inicio de la replicación inicial**, especifique cuándo se debe enviar la replicación inicial de máquinas virtuales del grupo de protección a Azure.

	![Grupo de protección](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_ProtectionGroup4.png)


## Paso 6: Habilitación de la protección de máquinas virtuales


Agregue máquinas virtuales a grupos de protección para habilitar su protección.

>[AZURE.NOTE] No se admite la protección de máquinas virtuales que ejecutan Linux con una dirección IP estática.

1. En la pestaña **Máquinas** del grupo de protección, haga clic en **Agregar máquinas virtuales a grupos de protección para habilitar protección**.
2. En la página **Habilitar protección de máquina virtual**, seleccione las máquinas virtuales que desea proteger.

	![Enable virtual machine protection](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_AddVM3.png)

	Comienzan los trabajos de Habilitar protección. Puede realizar un seguimiento del progreso en la pestaña **Trabajos**. La máquina virtual estará preparada para la conmutación por error después de que finalice el trabajo de Finalizar protección.
3. Una vez configurada la protección, puede:

	- Ver máquinas virtuales en **Elementos protegidos** > **Grupos de protección** > *nombre\_grupo\_protección* > **Máquinas virtuales**. Puede desplazarse hasta los detalles de la máquina en la pestaña **Propiedades**.
	- Configurar las propiedades de conmutación por error de una máquina virtual en **Elementos protegidos** > **Grupos de protección** > *nombre\_grupo\_protección* > **Máquinas virtuales** *nombre\_máquina\_virtual* > **Configurar**. Puede configurar:
		- **Nombre**: el nombre de la máquina virtual en Azure.
		- **Tamaño**: el tamaño de destino de la máquina virtual que conmuta por error.

		![Configuración de propiedades de la máquina virtual](./media/site-recovery-hyper-v-site-to-azure/VMProperties.png)
	- Establecer la configuración de una máquina virtual adicional en *Elementos protegidos* > **Grupos de protección** > *nombre\_grupo\_protección* > **Máquinas virtuales** *nombre\_máquina\_virtual* > **Configurar**, incluidos:

		- **Adaptadores de red**: el número de adaptadores de red viene determinado por el tamaño que especifique para la máquina virtual de destino. Compruebe las [especificaciones de tamaño de la máquina virtual](../virtual-machines/virtual-machines-size-specs.md#size-tables) para conocer el número de NIC que se admiten.


			Al modificar el tamaño de una máquina virtual y guardar la configuración, el número del adaptador de red cambiará la próxima vez que abra la página **Configurar**. El número de adaptadores de red de máquinas virtuales de destino es el mínimo número de adaptadores de red en la máquina virtual de origen y el número máximo de adaptadores de red compatible con el tamaño de la máquina virtual elegida. Se explica a continuación:


			- Si el número de adaptadores de red en el equipo de origen es menor o igual al número de adaptadores permitido para el tamaño de la máquina de destino, el destino tendrá el mismo número de adaptadores que el origen.
			- Si el número de adaptadores para la máquina virtual de origen supera el número permitido para el tamaño de destino, entonces se utilizará el tamaño máximo de destino.
			- Por ejemplo, si una máquina de origen tiene dos adaptadores de red y el tamaño de la máquina de destino es compatible con cuatro, el equipo de destino tendrá dos adaptadores. Si el equipo de origen tiene dos adaptadores pero el tamaño de destino compatible solo admite uno, el equipo de destino tendrá solo un adaptador. 	
		- **Red de Azure**: especifique la red a la que la máquina virtual debe conmutar por error. Si la máquina virtual tiene varios adaptadores de red, todos ellos deben conectarse a la misma red de Azure.
		- **Subred**: para cada adaptador de red de la máquina virtual, seleccione la subred en la red de Azure a la que debe conectarse el equipo después de una conmutación por error.
		- **Dirección IP de destino**: si el adaptador de red de la máquina virtual de origen está configurado para usar una dirección IP estática, puede especificar la dirección IP de la máquina virtual de destino para asegurarse de que el equipo tiene la misma dirección IP después de la conmutación por error. Si no especifica una dirección IP, se asignará cualquier dirección disponible en el momento de la conmutación por error. Si especifica una dirección que está en uso, se producirá un error en la conmutación por error.

		![Configuración de propiedades de la máquina virtual](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_VMMultipleNic.png)




## Paso 7: Creación de un plan de recuperación

Para probar la implementación, puede ejecutar una conmutación por error de prueba para una sola máquina virtual o un plan de recuperación que contenga una o varias máquinas virtuales. [Más](site-recovery-create-recovery-plans.md) acerca de la creación de un plan de recuperación.

## Paso 8: Prueba de la implementación

Hay dos maneras de ejecutar una prueba de conmutación por error en Azure.

- **Probar la conmutación por error sin una red de Azure**: este tipo de conmutación por error de prueba comprueba que la máquina virtual aparece correctamente en Azure. La máquina virtual no estará conectada a ninguna red de Azure después de la conmutación por error.
- **Probar la conmutación por error con una red de Azure**: este tipo de conmutación por error comprueba que todo el entorno de replicación aparece como se esperaba y que las máquinas virtuales conmutadas se conectan a la red de Azure de destino especificada. Tenga en cuenta que la conmutación por error de prueba de la máquina virtual de prueba se calculará en función de la subred de la máquina virtual de réplica. Esto es diferente a la replicación normal cuando la subred de una máquina virtual de réplica se basa en la subred de la máquina virtual de origen.

Si desea ejecutar una conmutación por error de prueba sin especificar una red de Azure, no es necesario preparar nada.

Para ejecutar una conmutación por error de prueba con una red de Azure de destino, es necesario crear una nueva red de Azure que esté aislada de su red de Azure de producción (el comportamiento predeterminado cuando se crea una nueva red de Azure). Para más detalles, lea [Ejecución de una conmutación por error de prueba](site-recovery-failover.md#run-a-test-failover).


Para probar completamente la implementación de la replicación y de la red, deberá configurar la infraestructura para que la máquina virtual replicada funcione como está previsto. Una manera de hacerlo es configurar una máquina virtual como controlador de dominio con DNS y replicarla en Azure mediante Site Recovery con el fin de crearla en la red de prueba mediante la ejecución de una conmutación por error de prueba. [Más información sobre](site-recovery-active-directory.md#considerations-for-test-failover) consideraciones de la conmutación por error de prueba para Active Directory.

Ejecute la conmutación por error de prueba de la manera siguiente:

1. En la pestaña **Planes de recuperación**, seleccione el plan y haga clic en **Conmutación por error de prueba**.
2. En la página **Confirmar conmutación por error de prueba**, seleccione **Ninguna** o una red de Azure concreta. Tenga en cuenta que si selecciona **Ninguna**, la conmutación por error de prueba comprueba que la máquina virtual se ha replicado correctamente en Azure, pero no comprueba la configuración de red de replicación.

	![Conmutación por error de prueba](./media/site-recovery-hyper-v-site-to-azure/SRHVSite_TestFailoverNoNetwork.png)

3. En la pestaña **Trabajos** puede seguir el progreso de la conmutación por error. También debe poder ver la réplica de prueba de la máquina virtual en el Portal de Azure. Si está configurando para acceder a máquinas virtuales desde la red local puede iniciar una conexión de Escritorio remoto a la máquina virtual.
4. Cuando la conmutación por error alcance la fase **Completar pruebas**, haga clic en **Completar prueba** para terminar la conmutación por error de prueba. Puede profundizar hasta la pestaña **Trabajo** para realizar un seguimiento de la conmutación por error del progreso y el estado, y llevar a cabo las acciones necesarias.
5. Después de la conmutación por error, podrá ver la réplica de prueba de la máquina virtual en el Portal de Azure. Si está configurando para acceder a máquinas virtuales desde la red local puede iniciar una conexión de Escritorio remoto a la máquina virtual.

	1. Compruebe que las máquinas virtuales se inician correctamente.
    2. Si después de la conmutación por error desea conectarse a la máquina virtual de Azure mediante Escritorio remoto, habilite Conexión a Escritorio remoto en la máquina virtual antes de ejecutar la prueba. También necesitará agregar un extremo RDP a la máquina virtual. Puede aprovechar un [runbook de automatización de Azure](site-recovery-runbook-automation.md) para hacerlo.
    3. Después de conmutación por error, si usa una dirección IP pública para conectarse a la máquina virtual en Azure mediante Escritorio remoto, asegúrese de no tener directivas de dominio que le impidan conectarse a una máquina virtual con una dirección pública.

6. Cuando se complete la prueba, haga lo siguiente:

	- Haga clic en **La conmutación por error de prueba se ha completado**. Limpie el entorno de prueba para apagar y eliminar automáticamente las máquinas virtuales de prueba.
	- Haga clic en **Notas** para registrar y guardar las observaciones asociadas a la conmutación por error de prueba.
7. Cuando la conmutación por error alcance la fase **Completar pruebas**, complete la comprobación de la siguiente manera:
	1. Vea la máquina virtual de réplica en el portal de Azure. Compruebe que la máquina virtual se inicia correctamente.
	2. Si está configurando para acceder a máquinas virtuales desde la red local puede iniciar una conexión de Escritorio remoto a la máquina virtual.
	3. Haga clic en **Completar la prueba** para terminarla.
	4. Haga clic en **Notas** para registrar y guardar las observaciones asociadas a la conmutación por error de prueba.
	5.  Haga clic en **La conmutación por error de prueba se ha completado**. Limpie el entorno de prueba para apagar y eliminar automáticamente la máquina virtual de prueba.

## Pasos siguientes

Después de que la implementación esté configurada y en ejecución, [obtenga más información](site-recovery-failover.md) acerca de la conmutación por error.

<!---HONumber=AcomDC_0218_2016-->