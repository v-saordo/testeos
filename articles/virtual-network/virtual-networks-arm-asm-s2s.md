<properties 
   pageTitle="Cómo conectar redes virtuales clásicas a redes virtuales de ARM en Azure - Guía de la solución"
   description="Obtenga información sobre cómo crear una conexión VPN entre redes virtuales clásica y nuevas"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Conexión de redes virtuales clásicas a redes virtuales nuevas

Actualmente, Azure tiene dos modos de administración: Administrador de servicios de Azure (conocido como clásico) y Administrador de recursos de Azure (ARM). Si ha estado usando Azure durante algún tiempo, es probable que tenga máquinas virtuales de Azure y roles de instancia ejecutándose en una red virtual clásica. Y es posible que sus máquinas virtuales e instancias de roles más recientes se estén ejecutando en una red virtual creada en ARM.

En tales situaciones, deseará asegurarse de que la nueva infraestructura sea capaz de comunicarse con sus recursos clásicos. Puede hacerlo mediante la creación de una conexión VPN entre las dos redes virtuales. La ilustración siguiente muestra un ejemplo de entorno con dos redes virtuales (clásica y ARM), junto con una conectividad de túnel seguro entre las redes virtuales.

![](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure01.png)

>[AZURE.NOTE]Este documento le guía a través de una solución integral con fines de prueba. Si ya tiene sus redes virtuales configuradas y está familiarizado con las puertas de enlace de VPN y las conexiones de sitio a sitio en Azure, visite [Configuración de una VPN S2S entre una red virtual de ARM y una red virtual clásica](../virtual-networks-arm-asm-s2s-howto.md).

Para probar este escenario, deberá:

1. [Crear un entorno de red virtual clásico](#Create-a-classic-VNet-environment).
2. [Crear un nuevo entorno de red virtual](#Create-a-new-VNet-environment).
3. [Conectar las dos redes virtuales](#Connect-the-two-VNets).

Deberá ejecutar los pasos anteriores usando en primer lugar las herramientas de administración de Azure clásicas, como el portal clásico, archivos de configuración de red y cmdlets de PowerShell de Administrador de servicios de Azure; posteriormente, deberá pasar a las nuevas herramientas de administración, incluido el nuevo portal de Azure, plantillas ARM y cmdlets de PowerShell de ARM.

>[AZURE.IMPORTANT]Para que las redes virtuales se conecten entre sí, no pueden tener un bloque CIDR en conflicto. Asegúrese de que cada red virtual tenga un bloque CIDR único.

## Crear un entorno de red virtual clásico

Puede usar una red virtual clásica existente para conectarse a una nueva red virtual de ARM. En este ejemplo, verá cómo crear una nueva red virtual clásica, con dos subredes, una puerta de enlace y una máquina virtual con fines de prueba.

### Paso 1: Crear una máquina virtual clásica

Para crear una máquina virtual nueva que se asigne a la figura 1 anterior, siga las instrucciones siguientes.

1. Desde una consola de PowerShell, ejecute el comando siguiente para agregar su cuenta de Azure.

		Add-AzureAccount

2. Siga la instrucciones del cuadro de diálogo de inicio de sesión para iniciar sesión en su cuenta de Azure.

3. Ejecute el comando siguiente para asegurarse de que está usando los cmdlets de PowerShell de Administración del servicio de Azure.

		Switch-AzureMode AzureServiceManagement

4. Ejecute el comando siguiente para descargar el archivo de configuración de red de Azure.

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

		XMLConfiguration                                OperationDescription                            OperationId                                     OperationStatus                                
		----------------                                --------------------                            -----------                                     ---------------                                
		<?xml version="1.0" encoding="utf-8"?>...       Get-AzureVNetConfig                             04ab3224-7e1c-cc1a-8b75-96c6c300ddb8            Succeeded       

5. Abra el archivo que acaba de descargar y agregue un sitio de red local denominado *vnet02* que use *10.2.0.0/16* como un prefijo de dirección y cualquier dirección IP pública válida como dirección de puerta de enlace de VPN. Puede hacerlo agregando un elemento **LocalNetworkSite** al elemento**LocalNetworkSites** en su archivo de configuración. El siguiente fragmento de archivo muestra un ejemplo de elemento **LocalNetworksSites**.

	    <LocalNetworkSites>
	      <LocalNetworkSite name="vnet02">
	        <AddressSpace>
	          <AddressPrefix>10.2.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>2.0.0.2</VPNGatewayAddress>
	      </LocalNetworkSite>
	    </LocalNetworkSites>		

6. En el elemento **VirtualNetworkSites**, agregue una nueva red virtual con un prefijo de dirección de *10.1.0.0/16* y dos subredes de acuerdo con la ilustración del escenario anterior. El siguiente fragmento de archivo muestra un ejemplo de elemento **VirtualNetworkSites**.
	
	    <VirtualNetworkSites>
	      <VirtualNetworkSite name="vnet01" Location="East US">
	        <AddressSpace>
	          <AddressPrefix>10.1.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <Subnets>
	          <Subnet name="Subnet1">
	            <AddressPrefix>10.1.0.0/24</AddressPrefix>
	          </Subnet>
	          <Subnet name="GatewaySubnet">
	            <AddressPrefix>10.1.200.0/29</AddressPrefix>
	          </Subnet>
	        </Subnets>
	      </VirtualNetworkSite>
	    </VirtualNetworkSites>

7. Ejecute el comando siguiente para guardar el archivo y cárguelo en Azure. Asegúrese de cambiar la ruta de acceso según sea necesario para su entorno.

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

		OperationDescription                                            OperationId                                                     OperationStatus                                                
		--------------------                                            -----------                                                     ---------------                                                
		Set-AzureVNetConfig                                             e0ee6e66-9167-cfa7-a746-7cab93c22013                            Succeeded 

### Paso 2: Crear una máquina virtual en la red virtual clásica

Para crear una máquina virtual en la red virtual clásica mediante el uso de cmdlets de PowerShell de Administrador de servicios de Azure, siga las instrucciones siguientes.

1. Recuperar una imagen de máquina virtual de Azure. El siguiente comando de PowerShell recupera la imagen de Windows Server 2012 R2 más reciente disponible.

		$WinImage = (Get-AzureVMImage `
		    | ?{$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"} `
		    | sort PublishedDate -Descending)[0]		

2. Ejecute el comando siguiente para crear una cuenta de almacenamiento para almacenar el disco duro virtual (VHD) para la máquina virtual.

		$storage1 = New-AzureStorageAccount `
			-StorageAccountName v1v2teststorage1 `
		    -Location "East US"	

3.  Ejecute los comandos siguientes para crear la máquina virtual. Asegúrese de reemplazar los valores de nombre y contraseña de usuario.

		$vm1 = New-AzureVMConfig -Name "VM01" -InstanceSize "ExtraLarge" `
		    -Image $WinImage.ImageName –AvailabilitySetName "MyAVSet1" `
		    -MediaLocation "https://v1v2teststorage1.blob.core.windows.net/vhd/vm01.vhd"
		Add-AzureProvisioningConfig –VM $vm1 -Windows `
		    -AdminUserName "user" -Password "P@ssw0rd" 

4.  Ejecute los comandos siguientes para conectar la máquina virtual a *Subnet1*.

		Set-AzureSubnet -SubnetNames "Subnet1" -VM $vm1
		Set-AzureStaticVNetIP  -IPAddress "10.1.0.101" -VM $vm1

5. Ejecute el comando siguiente para crear un nuevo servicio en la nube para hospedar la máquina virtual.

		New-AzureService -ServiceName "v1v2svc01" -Location "East US"
 		New-AzureVM -ServiceName "v1v2svc01" –VNetName "vnet01" –VMs $vm1

### Paso 3: Crear una puerta de enlace de VPN para la red virtual clásica 

Para crear la puerta de enlace VPN vnet01 mediante el portal de Azure clásico, siga las instrucciones siguientes.

1. Abra el Portal de Azure clásico en https://manage.windowsazure.com. Si es necesario, especifique las credenciales.
2. Desplácese hacia abajo en la lista de **TODOS LOS ELEMENTOS** y haga clic en **REDES**.
3. En la lista de redes virtuales, haga clic en **vnet01** y, a continuación, haga clic en **CONFIGURAR**.
4. En **conectividad de sitio a sitio**, marque la casilla de verificación **Conectar a la red local**.
5. En la lista **RED LOCAL**, seleccione **vnet02** y, a continuación, haga clic en **GUARDAR**y en **SÍ**.
6. Haga clic en **PANEL** y observe el mensaje que indica que todavía no se ha creado una puerta de enlace, tal y como se muestra en la figura 2.

	![Panel de red virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure02.png)

7. Haga clic en **CREAR PUERTA DE ENLACE**tal y como se muestra en la figura 3 siguiente para crear una puerta de enlace VPN para vnet01.

	![Panel de red virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure03.png)

8. En la lista de tipos de puerta de enlace, haga clic en **DINÁMICA** y, a continuación, haga clic en **SÍ**. Observe que la imagen del panel cambia, ya que muestra la puerta de enlace en amarillo mientras se está creando.

	![Panel de red virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure04.png)

	>[AZURE.NOTE]Es posible que esta operación tarde varios minutos.

9. Anote la dirección IP pública para la puerta de enlace, tal como se muestra a continuación, una vez creada. Necesitará esta dirección para crear una red local para la red virtual de ARM posteriormente.

	![Panel de red virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure05.png)

## Crear un nuevo entorno de red virtual

Ahora que la red virtual clásica está funcionando con una máquina virtual y una puerta de enlace, es hora de crear la red virtual de ARM.

### Paso 1: Crear una red virtual nueva en ARM

Para crear la red virtual de ARM, con dos subredes y una red local para la red virtual clásica mediante una plantilla ARM, siga las instrucciones siguientes.

1. Descargue los archivos azuredeploy.json y azuredeploy-parameters.json de [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/arm-asm-s2s).
2. Abra el archivo azuredeploy.json en Visual Studio y observe que la plantilla crea cuatro recursos: 

	- **Puerta de enlace local**: este recurso representa la puerta de enlace creada para la red virtual a la que desea conectarse. En este escenario, la puerta de enlace para vnet01.
	- **Red virtual**: este recurso representa una red virtual de ARM que se desea crear. En este escenario, vnet02.
	- **Dirección IP pública de puerta de enlace**: este recurso representa la dirección IP pública de la puerta de enlace que se desea crear para la red virtual de ARM. 
	- **Puerta de enlace**: este recurso representa la puerta de enlace que se va a crear para la red virtual de ARM (vnet02).

3. Observe los parámetros utilizados en este archivo. La mayoría de ellos tienen un valor predeterminado. Puede cambiar estos valores según sus necesidades.

4. Abra el archivo azuredeploy-parameters.json en Visual Studio. Este archivo contiene valores que se usarán para los parámetros del archivo de plantilla. Edite los valores siguientes, si es necesario.

	- **subscriptionId**: edite y pegue el identificador de suscripción. Si no conoce el identificador de suscripción, ejecute el cmdlet de PowerShell **Get-AzureSubscription** para recuperar el id.
	- **ubicación**: especificar la ubicación de Azure en la que se creará la red virtual. En este escenario, será **Centro de EE. UU.**.
	- **virtualNetworkName**: este es el nombre de la red virtual de ARM que se va a crear. En este escenario, **vnet02**.
	- **localGatewayName**: esta es la red local a la que desea conectarse desde la nueva red virtual de ARM. En este escenario, **vnet01**.
	- **localGatewayIpAddress**: se trata de la dirección IP pública para la puerta de enlace creada para la red a la que desea conectarse. En este escenario, se trata de la dirección IP que anotó en el paso 9 anterior, al crear la puerta de enlace de VPN para **vnet01**.
	- **localGatewayAddressPrefix**: este es el bloque CIDR para la red local a la que se conectará la red virtual. En este escenario, la red virtual a la que se va a conectar es **vnet01**, y su bloque CIDR es **10.1.0.0/16**.
	- **gatewayPublicIPName**: este es el nombre del objeto IP que se va a crear para la dirección IP pública de la puerta de enlace que se va a crear para la red virtual de ARM.
	- **gatewayName**: este es el nombre del objeto de puerta de enlace que se va a crear para la red virtual de ARM.
	- **addressPrefix**: este es el bloque CIDR para la red virtual de ARM. En este escenario, **10.2.0.0/16**.
	- **subnet1Prefix**: este es el bloque CIDR para una subred de la red virtual de ARM. En este escenario, **10.2.0.0/24**.
	- **gatewaySubnetPrefix**: este es el bloque CIDR para la subred de puerta de enlace de la red virtual de ARM. En este escenario, **10.2.200.0/29**.
	- **connectionName**: este es el nombre del objeto de conexión que desea crear.
	- **sharedKey**: se trata de la clave compartida de IPSec de la conexión. En este escenario, **abc123**.

5. Para crear la red virtual de ARM y sus objetos relacionados, en un nuevo grupo de recursos denominado **RG1**, ejecute el siguiente comando de PowerShell. Asegúrese de cambiar la ruta de acceso para el archivo de plantilla y el archivo de parámetros.

		Switch-AzureMode AzureResourceManager
		New-AzureResourceGroup -Name RG1 -Location "Central US" `
		    -TemplateFile C:\Azure\azuredeploy.json `
		    -TemplateParameterFile C:\Azure\azuredeploy-parameters.json		

	>[AZURE.NOTE]Es posible que esta operación tarde varios minutos.

7. Desde el explorador, vaya a https://portal.azure.com/ y escriba sus credenciales, si es necesario.
8. Haga clic en el icono del grupo de recursos **RG1** en el portal de Azure, como se muestra a continuación.

	![Panel de red virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure06.png)

9. Tenga en cuenta los recursos agregados al grupo mediante la plantilla ARM.

### Paso 2: Crear una nueva máquina virtual en ARM

Para crear una máquina virtual en la red virtual nueva desde el portal de Azure, siga las instrucciones siguientes.

1. En el portal, haga clic en el botón **NUEVO**, en **Proceso** y, a continuación, en** Centro de datos Windows Server 2012 R2**.
2. En la parte inferior del panel derecho, en **Seleccionar una pila de proceso**, seleccione **Usar la pila del Administrador de recursos** para crear la máquina virtual en ARM, tal como se muestra a continuación y haga clic en **Crear**.

	![Panel de red virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure07.png)

3. En la hoja **Fundamentos**, escriba el nombre de la máquina virtual, el nombre de usuario, la contraseña, la suscripción, el grupo de recursos y la ubicación de la máquina virtual y, a continuación, haga clic en **Aceptar**. En la siguiente ilustración se muestra la configuración para este escenario.

	![Panel de red virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure08.png)

4. En la hoja **Elegir un tamaño**, seleccione un tamaño y, a continuación, haga clic en **Seleccionar**. En este escenario, seleccione **D2**.
5. En la hoja **Configuración**, haga clic en **Red Virtual** y, a continuación, haga clic en **vnet02**.
6. Haga clic en **Subred**, en **Subnet1** y, a continuación, haga clic en **Aceptar**. La hoja **Resumen** debería ser similar a la siguiente ilustración.

	![Panel de red virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure09.png)

7. Haga clic en **Aceptar** y, a continuación, en **Crear** para crear la máquina virtual. Verá un nuevo icono en el portal, que mostrará la máquina virtual que se está aprovisionando, tal y como se muestra a continuación.

	![Panel de red virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure10.png)

	>[AZURE.NOTE]Es posible que esta operación tarde varios minutos. Puede pasar a la siguiente parte de este documento.

## Conectar las dos redes virtuales

Ahora que tiene dos redes virtuales con máquinas virtuales conectadas a ellas, es necesario conectar las redes virtuales a través de las puertas de enlace establecidas previamente y probar la conexión.

### Paso 1: Configurar la puerta de enlace para la red virtual clásica

Debe configurar la red virtual clásica para usar la dirección IP de la puerta de enlace creada para la red virtual de ARM (vnet02) y, a continuación, establecer una conexión desde cada red virtual. Para ello, siga las instrucciones que se describen a continuación.

1. Para recuperar la dirección IP usada para la puerta de enlace en la red virtual de ARM, ejecute el siguiente comando y observe el resultado. Anote la dirección, la necesitará para modificar la configuración de red local para la red virtual clásica más adelante.

		Get-AzurePublicIpAddress | ?{$_.Name -eq "ArmAsmS2sGatewayIp"}

		Name                     : ArmAsmS2sGatewayIp
		ResourceGroupName        : RG1
		Location                 : centralus
		Id                       : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/publicIPAddresses/ArmAsmS2sGatewayIp
		Etag                     : W/"1ee6c1bd-8be1-488e-a571-77b05b49e33a"
		ProvisioningState        : Succeeded
		Tags                     : 
		PublicIpAllocationMethod : Dynamic
		IpAddress                : 23.99.213.28
		IdleTimeoutInMinutes     : 4
		IpConfiguration          : {
		                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworkGateways/ArmAsmGateway/ipConfigurations/vn
		                           etGatewayConfig"
		                           }
		DnsSettings              : null

2. Ejecute el comando siguiente para asegurarse de que está usando la API de Administración de servicios de Azure para los comandos de PowerShell.

		Switch-AzureMode AzureServiceManagement

3. Ejecute el comando siguiente para descargar el archivo de configuración de red de Azure.

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

4. Abra el archivo recién descargado y edite el elemento **LocalNetworkSite** para **vnet02** para agregar la dirección IP de la puerta de enlace para la red virtual nueva obtenida en el paso 1 anterior. El elemento debe tener un aspecto similar al del ejemplo siguiente.

	      <LocalNetworkSite name="vnet02">
	        <AddressSpace>
	          <AddressPrefix>10.2.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>23.99.213.28</VPNGatewayAddress>
	      </LocalNetworkSite>

5. Guarde el archivo y luego ejecute el siguiente comando para configurar la red virtual clásica. Asegúrese de cambiar la ruta de acceso para que señale al archivo guardado en el paso 4 anterior.

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

6. Ejecute el siguiente comando para establecer la clave compartida de IPSec para la puerta de enlace de red virtual clásica. Debería verse un resultado similar al que se expone a continuación. Asegúrese de cambiar los nombres de las redes virtuales si está usando sus propias redes virtuales ya existentes.

		Set-AzureVNetGatewayKey -VNetName vnet01 -LocalNetworkSiteName vnet02 -SharedKey abc123 

		Error          : 
		HttpStatusCode : OK
		Id             : b2154475-bf00-480e-ad1e-aed893014979
		Status         : Successful
		RequestId      : 08257a09d723cb8982c47b85edb0e08a
		StatusCode     : OK

### Paso 2: Configurar la puerta de enlace para la red virtual de ARM

Ahora que tiene la puerta de enlace de red virtual clásica configurada, es momento de establecer la conexión. Para ello, siga las instrucciones que se describen a continuación.

1. Desde una consola de PowerShell, ejecute el siguiente comando para cambiar al modo ARM. 

		Switch-AzureMode AzureResourceManager

2. Ejecute los siguientes comandos para crear la conexión entre las puertas de enlace.

		$vnet01gateway = Get-AzureLocalNetworkGateway -Name vnet01 -ResourceGroupName RG1
		$vnet02gateway = Get-AzureVirtualNetworkGateway -Name ArmAsmGateway -ResourceGroupName RG1
		
		New-AzureVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "Central US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

3. Abra el Portal de Azure en https://manage.windowsazure.com y, si es necesario, escriba sus credenciales.
4. En **TODOS LOS ELEMENTOS**, desplácese hacia abajo, haga clic en **REDES**, en **vnet01**y, a continuación, haga clic en**PANEL**. Observe que la conexión entre **vnet01** y **vnet02** se acaba de establecer, tal y como se muestra a continuación.

	![Panel de red virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure11.png)

5. Aunque puede administrar la red virtual clásica y su conexión desde el portal clásico, se recomienda usar el nuevo portal de Azure. Para abrir el nuevo portal, vaya a https://portal.azure.com.
6. En el nuevo portal, haga clic en **EXAMINAR TODO** y, a continuación, haga clic en **Redes virtuales (clásico)** y en **vnet01**. Observe el panel **Conexiones VPN** que se muestra a continuación.

	![Panel de red virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure12.png)

### Paso 3: Probar la conectividad

Ahora que dispone de las dos redes de virtuales conectadas, es momento de probar la conectividad haciendo ping en una máquina virtual desde la otra. Necesitará cambiar la configuración del firewall de una de las máquinas virtuales para habilitar ICMP y, a continuación, haga ping en esa máquina virtual desde la otra máquina virtual. Para ello, siga las instrucciones que se describen a continuación.

1. En el portal de Azure, haga clic en **EXAMINAR TODO** y, a continuación, haga clic en **Máquinas virtuales** y en **VM02**.
2. Desde la hoja **VM02**, haga clic en **Conectar**. Si es necesario, haga clic en **Abrir** en la pancarta de seguridad del explorador para abrir el archivo RDP.
3. Seleccione el cuadro de diálogo **Conexión de escritorio remoto** y haga clic en **Conectar**.
4. En el cuadro de diálogo **Seguridad de Windows**, escriba el nombre de usuario de la máquina virtual y la contraseña. 
5. En el cuadro de diálogo **Conexión a Escritorio remoto**, haga clic en **Sí**.
6. Una vez se conecte a la máquina virtual, desde **Administrador del servidor**, haga clic en **Herramientas** y, a continuación, haga clic en **Firewall de Windows con seguridad avanzada**.
7. En la ventana **Firewall de Windows con seguridad avanzada**, haga clic en **Reglas de entrada**.
8. En la lista **Reglas de entrada**, haga clic con el botón derecho en **Compartir archivos e impresoras (solicitud de eco: ICMPv4 de entrada)** y, a continuación, haga clic en **Habilitar regla**.
9. En la barra de tareas, haga clic en el botón **Windows PowerShell** y, a continuación, ejecute el siguiente comando.

		ipconfig

		Connection-specific DNS Suffix  . : 4oxp4ce0c5rulb1iey4cdqhasg.gx.internal.cloudapp.net
		Link-local IPv6 Address . . . . . : fe80::8cea:a98a:5c48:4c58%12
		IPv4 Address. . . . . . . . . . . : 10.2.0.101
		Subnet Mask . . . . . . . . . . . : 255.255.255.0
		Default Gateway . . . . . . . . . : 10.2.0.101

10. Anote la dirección IP de la máquina virtual. En este escenario, **10.2.0.101**. Hará ping en esa dirección desde la otra máquina virtual para probar la conectividad entre las redes virtuales.
11. En el portal de Azure, en el panel izquierdo, haga clic en **Máquinas virtuales (clásicas)**, en **VM01** y, a continuación, en **Conectar**. Si es necesario, haga clic en **Abrir** en la pancarta de seguridad del explorador para abrir el archivo RDP.
12. Seleccione el cuadro de diálogo **Conexión de escritorio remoto** y haga clic en **Conectar**.
13. En el cuadro de diálogo **Seguridad de Windows**, escriba el nombre de usuario de la máquina virtual y la contraseña. 
14. En el cuadro de diálogo **Conexión a Escritorio remoto**, haga clic en **Sí**.
15. En la barra de tareas de la máquina virtual remota, haga clic en el botón **Windows PowerShell** y, a continuación, ejecute el siguiente comando.

		ping 10.2.0.101

		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126

## Pasos siguientes

- Obtenga más información sobre [el proveedor de recursos de red (NRP) para ARM](../resource-groups-networking.md).
- Consulte las instrucciones generales sobre cómo [crear una conexión VPN de S2S entre una red virtual clásica y una red virtual de ARM](../virtual-networks-arm-asm-s2s-howto.md).

<!---HONumber=AcomDC_1217_2015-->