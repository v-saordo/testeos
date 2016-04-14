<properties 
	pageTitle="Entorno de prueba de nube híbrida | Microsoft Azure" 
	description="Aprenda a crear un entorno de nube híbrida para profesionales de TI o para pruebas de desarrollo, completo con una red local simplificada." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/28/2016" 
	ms.author="josephd"/>

# Configuración de un entorno de nube híbrida para pruebas

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.
 

En este tema se le guiará en el proceso de creación de un entorno de nube híbrida con Microsoft Azure para pruebas. Aquí está la configuración resultante.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_5.png)

Esto simula un entorno de producción real híbrido desde su ubicación en Internet. Consta de:

-  Una red local simplificada (la subred de red corporativa).
-  Una red virtual entre locales hospedada en Azure (TestVNET).
-  Una conexión VPN de sitio a sitio.
-  Un controlador de dominio secundario en la red virtual TestVNET.

Esta configuración proporciona una base y un punto de partida común desde el que puede:

-  Desarrollar y probar las aplicaciones en un entorno de nube híbrida.
-  Crear configuraciones de pruebas de los equipos, algunos en la subred de la red corporativa y otros dentro de la red virtual TestVNET, para cargas de trabajo de TI basadas en la nube híbrida.

Hay cinco fases principales para configurar este entorno de prueba de nube híbrida:

1.	Configuración de los equipos en la subred de la red corporativa.
2.	Configuración de RRAS1.
3.	Creación de la red virtual de Azure entre locales.
4.	Creación de la conexión VPN de sitio a sitio.
5.	Configuración de DC2. 

Si todavía no dispone de una suscripción a Azure, puede registrarse para obtener una evaluación gratuita en [Probar Azure](https://azure.microsoft.com/pricing/free-trial/). Si tiene una suscripción a MSDN, consulte [Beneficio de Azure para los suscriptores de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Las máquinas virtuales y las puertas de enlace de redes virtuales en Azure incurren en un coste económico constante cuando se están ejecutando. Este coste se factura en su suscripción de prueba gratuita, de MSDN o de pago. Para reducir el coste de la ejecución de este entorno de prueba cuando no lo esté usando, consulte [Minimización de los costes de este entorno](#costs) en este tema para obtener más información.

Esta configuración requiere una subred de prueba de hasta cuatro equipos conectados directamente a Internet mediante una dirección IP pública. Si no tiene estos recursos, también puede realizar la [Configuración de un entorno de nube híbrida simulado para pruebas](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md). El entorno de prueba de nube híbrida simulada solo requiere una suscripción de Azure.

## Fase 1: Configuración de los equipos en la subred Corpnet

Siga las instrucciones de la sección "Pasos para configurar la subred Corpnet" de la [Test Lab Guide: Configuración base para Windows Server 2012 R2](http://www.microsoft.com/download/details.aspx?id=39638) para configurar los equipos DC1, APP1 y CLIENT1 en una subred denominada Corpnet. **Esta subred debe estar aislada de la red de su organización, ya que se conectará directamente a Internet a través del equipo RRAS1.**

A continuación, inicie sesión en DC1 con las credenciales de CORP\\User1. Para configurar el dominio CORP para que los usuarios y equipos usen su controlador de dominio local para la autenticación, ejecute estos comandos desde un símbolo del sistema de Windows PowerShell con nivel de administrador.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet “Name "10.0.0.0/8" “Site "TestLab"
	New-ADReplicationSubnet “Name "192.168.0.0/16" “Site "TestVNET

Esta es su configuración actual.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_1.png)
 
## Fase 2: Configuración de RRAS1

RRAS1 proporciona enrutamiento del tráfico y servicios de dispositivos VPN entre los equipos de la subred de la red corporativa y la red virtual TestVNET. RRAS1 debe tener dos adaptadores de red instalados.

En primer lugar, instale el sistema operativo en RRAS1.

1.	Inicie la instalación de Windows Server 2012 R2.
2.	Siga las instrucciones para completar la instalación, especificando una contraseña segura para la cuenta del administrador local. Inicie sesión con la cuenta del administrador local.
3.	Conecte RRAS1 a una red que tenga acceso a Internet y ejecute Windows Update para instalar las actualizaciones más recientes de Windows Server 2012 R2.
4.	Conecte un adaptador de red a la subred de la red corporativa y el otro directamente a Internet. RRAS1 puede estar situado detrás de un firewall de Internet, pero no debe estar detrás de un traductor de direcciones de red (NAT).

A continuación, configure las propiedades TCP/IP de RRAS1. Necesitará una configuración de dirección IP pública, incluyendo una dirección, una máscara de subred (o longitud de prefijo), y la puerta de enlace predeterminada y los servidores DNS de su proveedor de servicios de Internet (ISP).

Utilice estos comandos en un símbolo del sistema de Windows PowerShell con nivel de administrador en RRAS1. Antes de ejecutar estos comandos, introduzca los valores de las variables y quite los caracteres < and >. Puede obtener los nombres actuales de los adaptadores de red desde la pantalla del comando **Get-NetAdapter**.

	$corpnetAdapterName="<Name of the adapter attached to the Corpnet subnet>"
	$internetAdapterName="<Name of the adapter attached to the Internet>"
	[Ipaddress]$publicIP="<Your public IP address>"
	$publicIPpreflength=<Prefix length of your public IP address>
	[IPAddress]$publicDG="<Your ISP default gateway>"
	[IPAddress]$publicDNS="<Your ISP DNS server(s)>"
	Rename-NetAdapter -Name $corpnetAdapterName -NewName Corpnet
	Rename-NetAdapter -Name $internetAdapterName -NewName Internet
	New-NetIPAddress -InterfaceAlias "Internet" -IPAddress $publicIP -PrefixLength $publicIPpreflength “DefaultGateway $publicDG
	Set-DnsClientServerAddress -InterfaceAlias Internet -ServerAddresses $publicDNS
	New-NetIPAddress -InterfaceAlias "Corpnet" -IPAddress 10.0.0.2 -AddressFamily IPv4 -PrefixLength 24
	Set-DnsClientServerAddress -InterfaceAlias "Corpnet" -ServerAddresses 10.0.0.1
	Set-DnsClient -InterfaceAlias "Corpnet" -ConnectionSpecificSuffix corp.contoso.com
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Input" -Protocol ICMPv4
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Output" -Protocol ICMPv4 -Direction Outbound
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_msclient
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_server
	ping dc1.corp.contoso.com

En el último comando, compruebe que hay cuatro respuestas desde la dirección IP 10.0.0.1.

Esta es su configuración actual.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_2.png)

## Fase 3: Creación de la red virtual de Azure entre locales

En primer lugar, inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/) con sus credenciales de suscripción de Azure y cree una red virtual denominada TestVNET.

1.	En la barra de tareas del Portal de administración de Azure, haga clic en **Nuevo > Servicios de red > Red virtual > Creación personalizada**.
2.	En la página Detalles de redes virtuales, escriba **TestVNET** en **Nombre**.
3.	En **Ubicación**, seleccione el centro de datos apropiado para su ubicación.
4.	Haga clic en la flecha Siguiente.
5.	En la página Servidores DNS y conectividad VPN, en **Servidores DNS**, escriba **DC1** en **Seleccione o especifique un nombre**, escriba **10.0.0.1** en **Dirección IP** y, a continuación, seleccione **Configurar una VPN de sitio a sitio**.
6.	En **Red local**, seleccione **Especifique una nueva red local**. 
7.	Haga clic en la flecha Siguiente.
8.	En la página Conectividad de sitio a sitio:
	- En **Nombre**, escriba **Corpnet**. 
	- En **Dirección IP del dispositivo VPN**, escriba la dirección IP pública asignada a la interfaz de Internet de RRAS1.
	- En **Espacio de direcciones**, en la columna **IP inicial**, escriba **10.0.0.0**. En **CIDR (recuento de direcciones)**, seleccione **/8** y haga clic en **Agregar espacio de direcciones**.
9.	Haga clic en la flecha Siguiente.
10.	En la página Espacios de direcciones de redes virtuales:
	- En **Espacio de direcciones**, en **IP inicial**, seleccione **192.168.0.0**.
	- En **Subredes**, haga clic en **Subnet-1** y reemplace el nombre por **TestSubnet**. 
	- En la columna **CIDR (recuento de direcciones)** de TestSubnet, haga clic en **/24 (256)**.
	- Haga clic en **Agregar subred de puerta de enlace**.
11.	Haga clic en el icono Completar. Espere hasta que se cree la red virtual antes de continuar.

A continuación, siga las instrucciones en [Cómo instalar y configurar Azure PowerShell](../install-configure-powershell.md) para instalar Azure PowerShell en el equipo local.

A continuación, cree un nuevo servicio en la nube para la red virtual TestVNET. Debe elegir un nombre único. Por ejemplo, podría asignarle el nombre TestVNET-*UniqueSequence*, en el que *UniqueSequence* es una abreviatura de su organización. Por ejemplo, si el nombre de su organización es Tailspin Toys, podría asignar el nombre del servicio de nube TestVNET-Tailspin.

Puede comprobar que el nombre sea único con el siguiente comando de Azure PowerShell en el equipo local.

	Test-AzureName -Service <Proposed cloud service name>

Si este comando devuelve "False", el nombre propuesto es único. Cree el servicio en la nube con este comando.

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

A continuación, cree una nueva cuenta de almacenamiento para la red virtual TestVNET. Debe elegir un nombre único. Se puede comprobar que el nombre de cuenta de almacenamiento sea único con este comando.

	Test-AzureName -Storage <Proposed storage account name>

Si este comando devuelve "False", el nombre propuesto es único. Cree y configure la cuenta de almacenamiento con estos comandos.

	New-AzureStorageAccount -StorageAccountName <Unique storage account name> -Location "<Same location name as your virtual network>"
	Set-AzureStorageAccount -StorageAccountName <Unique storage account name>

Esta es su configuración actual.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_3.png)

 
## Fase 4: Creación de la conexión VPN de sitio a sitio

En primer lugar, cree una puerta de enlace de red virtual.

1.	En el Portal de administración de Azure, en el equipo local, haga clic en **Redes** en el panel izquierdo y, a continuación, compruebe que la columna **Estado** de TestVNET está establecida en **Creado**.
2.	Haga clic en **TestVNET**. En la página Panel, verá un estado de **Puerta de enlace no creada**.
3.	En la barra de tareas, haga clic en **Crear puerta de enlace** y, a continuación, en **Enrutamiento dinámico**. Haga clic en **Sí** cuando se le solicite. Espere hasta que la puerta de enlace se haya completado y su estado cambie a **Conectando**. Esto puede tardar unos minutos.
4.	En la página Panel, tenga en cuenta la **Dirección IP de la puerta de enlace**. Se trata de la dirección IP pública de la puerta de enlace VPN de la red virtual TestVNET. Necesitará esta dirección IP para configurar RRAS1.
5.	En la barra de tareas, haga clic en **Administrar clave** y, a continuación, en el icono de copia al lado de la clave para copiarla al Portapapeles. Pegue esta clave en un documento y guárdelo. Necesitará este valor de clave para configurar RRAS1. 

A continuación, configure RRAS1 con el servicio de acceso remoto y enrutamiento para que actúe como el dispositivo VPN para la subred de la red corporativa. Inicie sesión en RRAS1 como administrador local y ejecute estos comandos en un símbolo del sistema de Windows PowerShell.

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

Después, configure RRAS1 para recibir la conexión VPN de sitio a sitio desde la puerta de enlace VPN de Azure. Reinicie RRAS1 e inicie sesión como administrador local y ejecute estos comandos en un símbolo del sistema de Windows PowerShell. Debe proporcionar la dirección IP de la puerta de enlace VPN de Azure y el valor de la clave.

	$PresharedKey="<Key value>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

A continuación, vaya al Portal de administración de Azure, en el equipo local, y espere hasta que se muestre un estado de la red virtual TestVNET de **Conectado**.

A continuación, configure RRAS1 para que admita tráfico traducido para ubicaciones de Internet. En RRAS1:

1.	En la pantalla de inicio, escriba **rras** y haga clic en **Enrutamiento y acceso remoto**. 
2.	En el árbol de consola, abra el nombre del servidor y haga clic en **IPv4**.
3.	Haga clic con el botón derecho en **General** y, a continuación, haga clic en **Nuevo protocolo de enrutamiento**.
4.	Haga clic en **NAT** y, a continuación, en **Aceptar**.
5.	En el árbol de consola, haga clic con el botón derecho en **NAT**, haga clic en **Nueva interfaz**, en **Corpnet** y, a continuación, en **Aceptar** dos veces.
6.	Haga clic con el botón derecho en **NAT**, haga clic en **Nueva interfaz**, haga clic en **Internet** y, a continuación, en **Aceptar**.
7.	En la pestaña **NAT**, haga clic en **Interfaz pública conectada a Internet**, seleccione **Habilitar NAT en esta interfaz** y, a continuación, haga clic en **Aceptar**.


Después, configure DC1, APP1 y CLIENT1 para utilizar RRAS1 como puerta de enlace predeterminada.
 
En DC1, ejecute estos comandos en un símbolo del sistema de Windows PowerShell con nivel de administrador.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2
	Set-DhcpServerv4OptionValue -Router 10.0.0.2

Si el nombre de la interfaz no es Ethernet, use el comando **Get-NetAdapter** para determinar el nombre de interfaz.

En APP1, ejecute este comando en un símbolo del sistema de Windows PowerShell con nivel de administrador.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2

En CLIENT1, ejecute este comando en un símbolo del sistema de Windows PowerShell con nivel de administrador.

	ipconfig /renew

Esta es su configuración actual.
 

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_4.png)


## Fase 5: Configuración de DC2

En primer lugar, cree una máquina virtual de Azure de DC2 con estos comandos en el símbolo del sistema de Azure PowerShell en el equipo local.

	$ServiceName="<Your cloud service name from Phase 3>"
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password 
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdminName -Password $LocalAdminPW	
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET


A continuación, inicie sesión en la nueva máquina virtual de DC2.

1.	En el panel izquierdo del Portal de administración de Azure, haga clic en **Máquinas virtuales** y, a continuación, haga clic en **En ejecución** en la columna **Estado** de DC2.
2.	En la barra de tareas, haga clic en **Conectar**. 
3.	Cuando se le pida que abra DC2.rdp, haga clic en **Abrir**.
4.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto, haga clic en **Conectar**.
5.	Cuando se le pidan las credenciales, utilice las siguientes:
	- Nombre: **DC2\**[Nombre de la cuenta del administrador local]
	- Contraseña: [Contraseña de la cuenta de administrador local]
6.	Cuando aparezca un cuadro de mensaje de conexión a Escritorio remoto referido a certificados, haga clic en **Sí**.

A continuación, configure una regla del Firewall de Windows para permitir el tráfico para probar la conectividad básica. Desde un símbolo del sistema de Windows PowerShell con nivel de administrador en DS2, ejecute:

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

El comando ping debería producir cuatro respuestas correctas desde la dirección IP 10.0.0.1. Esto es una prueba del tráfico a través de la conexión VPN de sitio a sitio.

A continuación, agregue un disco de datos adicional como un nuevo volumen con la letra de unidad F:.

1.	En el panel izquierdo del Administrador de servidores, haga clic en **Servicios de archivos y almacenamiento** y, a continuación, haga clic en **Discos**.
2.	En el panel de contenido, en el grupo **Discos**, haga clic en **disco 2** (con la **partición** establecida en **Desconocida**).
3.	Haga clic en **Tareas** y, a continuación, haga clic en **Nuevo volumen**.
4.	En la página Antes de empezar del Asistente para volumen nuevo, haga clic en **Siguiente**.
5.	En la página Selección del servidor y del disco, haga clic en **Disco 2** y, a continuación, haga clic en **Siguiente**. Cuando se le solicite, haga clic en **Aceptar**.
6.	En la página Especificación del tamaño de la página de volumen, haga clic en **Siguiente**.
7.	En la página Asignación a una letra de unidad o carpeta, haga clic en **Siguiente**.
8.	En la página Selección de la configuración del sistema de archivos, haga clic en **Siguiente**.
9.	En la página Confirmación de las selecciones, haga clic en **Crear**.
10.	Cuando haya terminado, haga clic en **Cerrar**.

A continuación, configure DC2 como un controlador de dominio de réplica para el dominio corp.contoso.com. Ejecute estos comandos desde el símbolo del sistema de Windows PowerShell en DC2.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Tenga en cuenta que se le pedirá que proporcione la contraseña de CORP\\User1 y una contraseña de Modo de restauración de servicios de directorio (DSRM), y que reinicie DC2.

Ahora que la red virtual TestVNET tiene su propio servidor DNS (DC2), debe configurar la red virtual de TestVNET para utilizar este servidor DNS.

1.	En el panel izquierdo del Portal de administración de Azure, haga clic en **Redes** y, a continuación, haga clic en **TestVNET**.
2.	Haga clic en **Configurar**.
3.	En **Servidores DNS**, quite la entrada **10.0.0.1**
4.	En **Servidores DNS**, agregue una entrada con **DC2** como el nombre y **192.168.0.4** como la dirección IP. 
5.	En la barra de comandos de la parte inferior, haga clic en **Guardar**.
6.	En el panel izquierdo del Portal de administración de Azure, haga clic en **Máquinas virtuales** y, a continuación, haga clic en la columna **Estado** junto a DC2.
7.	En la barra de comandos, haga clic en **Reiniciar**. Espere a que DC2 se reinicie.


Esta es su configuración actual.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_5.png)

 
Su entorno de nube híbrida ya está listo para las pruebas.

## Minimización del coste de este entorno

Para minimizar el coste de la ejecución de las máquinas virtuales en este entorno, realice las pruebas y las demostraciones necesarias tan pronto como sea posible y, a continuación, elimínelas o apague las máquinas virtuales cuando no las esté utilizando. Por ejemplo, podría utilizar la automatización de Azure y un Runbook para apagar automáticamente las máquinas virtuales en la red virtual Test\_VNET al final de cada día laborable. Para obtener información, consulte [Introducción a la automatización de Azure](../automation-create-runbook-from-samples.md).

La puerta de enlace VPN de Azure se implementa como un conjunto de dos máquinas virtuales de Azure que incurren en un coste económico continuo. Para obtener más información, consulte [Precios: red virtual](https://azure.microsoft.com/pricing/details/virtual-network/). Para minimizar el coste de la puerta de enlace VPN, cree el entorno de prueba y realice las pruebas y demostraciones necesarias tan pronto como sea posible, o elimine la puerta de enlace con estos pasos.

1.	En el Portal de administración de Azure en el equipo local, haga clic en **Redes** en el panel izquierdo, haga clic en **TestVNET** y, a continuación, en **Panel**.
2.	En la barra de tareas, haga clic en **Eliminar puerta de enlace**. Haga clic en **Sí** cuando se le solicite. Espere hasta que se elimine la puerta de enlace y su estado cambie a **No se ha creado la puerta de enlace**.

Si elimina la puerta de enlace y desea restaurar el entorno de prueba, primero debe crear una nueva puerta de enlace.

1.	En el Portal de administración de Azure en el equipo local, haga clic en **Redes** en el panel izquierdo y, a continuación, en **TestVNET**. En la página Panel, verá un estado de **No se ha creado la puerta de enlace**.
2.	En la barra de tareas, haga clic en **Crear puerta de enlace** y, a continuación, en **Enrutamiento dinámico**. Haga clic en **Sí** cuando se le solicite. Espere hasta que la puerta de enlace se haya completado y su estado cambie a **Conectando**. Esto puede tardar unos minutos.
3.	En la página Panel, tenga en cuenta la **Dirección IP de la puerta de enlace**. Se trata de la nueva dirección IP pública de la puerta de enlace VPN de la red virtual TestVNET. Necesitará esta dirección IP para volver a configurar RRAS1.
4.	En la barra de tareas, haga clic en **Administrar clave** y, a continuación, en el icono de copia al lado de la clave para copiarla al Portapapeles. Pegue el valor de esta clave en un documento y guárdelo. Necesitará este valor de clave para volver a configurar RRAS1. 

A continuación, inicie sesión en RRAS1 como administrador local y ejecute estos comandos en un símbolo del sistema de Windows PowerShell con nivel de administrador para volver a configurar RRAS1 con la nueva dirección IP pública y una clave previamente compartida.

	$PresharedKey="<Key value>"
	Set-VpnS2SInterface -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -SharedSecret $PresharedKey

A continuación, vaya al Portal de administración de Azure en el equipo local y espere hasta que se muestre un estado de la red virtual TestVNET de conectado.
 
## Pasos siguientes

- Configure una [granja de intranet de SharePoint](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), una [aplicación de LOB basada en web](virtual-networks-setup-lobapp-hybrid-cloud-testing.md) o un [servidor de sincronización de directorios (DirSync) de Office 365](virtual-networks-setup-dirsync-hybrid-cloud-testing.md) en este entorno.

<!---HONumber=AcomDC_0204_2016-->