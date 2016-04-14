<properties 
   pageTitle="Creación de una máquina virtual con varias NIC"
   description="Aprenda a crear y configurar máquinas virtuales con varias tarjetas NIC"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" 
   tags="azure-service-management,azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

# Creación de una máquina virtual con varias NIC

Puede crear máquinas virtuales (VM) en Azure y asociar varias interfaces de red (NIC) a cada una de las máquinas virtuales. Tener varias NIC es un requisito para muchos dispositivos virtuales de red, por ejemplo, en el caso de la entrega de aplicaciones y las soluciones de optimización de la WAN. Tener varias NIC también le proporciona más funcionalidad a la hora de administrar el tráfico de red, incluyendo el aislamiento del tráfico entre una NIC de front-end y varias NIC de back-end o la separación entre el tráfico del plano de datos y el tráfico del plano de administración.

![Varias NIC para máquina virtual](./media/virtual-networks-multiple-nics/IC757773.png)

La figura anterior muestra una máquina virtual con tres NIC, cada una de ellas conectada a una subred diferente.

## Límites y restricciones

Actualmente, tener varias NIC tiene los siguientes límites y restricciones:

- Las máquinas virtuales con varias NIC deben crearse en redes virtuales de Azure. Las máquinas virtuales que no sean de redes virtuales no están admitidas. 
- Solo se permiten los siguientes valores en un servicio en la nube único (implementaciones clásicas) o en un grupo de recursos (Administrador de recursos de implementación): 
	- Todas las máquinas virtuales de ese servicio en la nube deben tener varias NIC habilitadas o 
	- Todas las máquinas virtuales de ese servicio en la nube deben tener cada una sola NIC 

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.
 
- La VIP accesible desde Internet (implementaciones clásicas) solo se admite en la NIC marcada como "predeterminada". Solo hay una VIP a la IP de la NIC predeterminada. 
- En estos momentos, no se admiten direcciones IP públicas (implementaciones clásicas) de nivel de instancia (LPIP) para máquinas virtuales con varias NIC. 
- El orden de las NIC desde la máquina virtual será aleatorio y también podría cambiar en todas las actualizaciones de infraestructura de Azure. Sin embargo, las direcciones IP y las direcciones MAC Ethernet correspondientes seguirán siendo las mismas. Por ejemplo, suponga que **Eth1** tiene la dirección IP 10.1.0.100 y la dirección MAC 00-0D-3A-B0-39-0D; después de una actualización de la infraestructura de Azure y de reiniciar el equipo, se podría cambiar a **Eth2**, pero el emparejamiento de la IP y la MAC seguirá siendo el mismo. Cuando es un cliente quien ejecuta un reinicio, el orden de NIC seguirá siendo el mismo. 
- La dirección de cada NIC en cada máquina virtual debe estar ubicada en una subred; las direcciones que se encuentran en la misma subred se pueden asignar a cada una de las varias NIC de una sola máquina virtual. 
- El tamaño de la máquina virtual determina el número de las NIC que se puede crear para una máquina virtual. La tabla siguiente enumera los números de NIC correspondientes al tamaño de las máquinas virtuales: 

|Tamaño de memoria virtual (SKU estándar)|NIC (número máximo permitido por máquina virtual)|
|---|---|
|Todos los tamaños básicos|1|
|A0\\extra pequeño|1|
|A1\\pequeño|1|
|A2\\mediano|1|
|A3\\grande|2|
|A4\\extra grande|4|
|A5|1|
|A6|2|
|A7|4|
|A8|2|
|A9|4|
|A10|2|
|A11|4|
|D1|1|
|D2|2|
|D3|4|
|D4|8|
|D11|2|
|D12|4|
|D13|8|
|D14|8|
|DS1|1|
|DS2|2|
|DS3|4|
|DS4|8|
|DS11|2|
|DS12|4|
|DS13|8|
|DS14|8|
|D1\_v2|1|
|D2\_v2|2|
|D3\_v2|4|
|D4\_v2|8|
|D5\_v2|8|
|D11\_v2|2|
|D12\_v2|4|
|D13\_v2|8|
|D14\_v2|8|
|G1|1|
|G2|2|
|G3|4|
|G4|8|
|G5|8|
|Todos los tamaños|1|

## Grupos de seguridad de red (NSG)
En una implementación del Administrador de recursos, cualquier NIC de una máquina virtual puede asociarse a un grupo de seguridad de red (NSG), incluyendo cualquier NIC de una máquina virtual que tenga varias NIC habilitadas. Si a una NIC se le asigna una dirección dentro de una subred que está asociada a un NSG, las reglas de NSG de la subred también se aplican a esa NIC. Además de asociar subredes a NSG, también puede asociar una NIC a un NSG.

Si una subred está asociada a un NSG y una NIC dentro de esa subred está asociada individualmente a un NSG, las reglas asociadas de NSG se aplican en **orden de flujo** según la dirección del tráfico que se pasa dentro o fuera de la NIC:

- **El **tráfico entrante** cuyo destino es la NIC en cuestión fluye primero a través de la subred, desencadenando reglas de NSG de la subred, antes de pasar a la NIC y desencadenar reglas de NSG de la NIC.
- El **tráfico de salida** cuyo origen es la NIC en cuestión fluye primero fuera de la NIC, desencadenando reglas de NSG de la NIC, antes de pasar a la subred y desencadenar reglas de NSG de la subred. 

Obtener más información sobre los [Grupos de seguridad de red](virtual-networks-nsg.md) y cómo se aplican en función de las asociaciones de subredes, las máquinas virtuales y las NIC.

## Cómo configurar una máquina virtual con varias NIC en una implementación clásica

Las instrucciones siguientes le ayudarán a crear una máquina virtual de varias NIC con 3 NIC: una NIC predeterminada y dos NIC adicionales. Los pasos de configuración crearán una máquina virtual que se configurará según el fragmento de archivo de configuración de servicio siguiente:

	<VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
	<AddressSpace>
	  <AddressPrefix>10.1.0.0/16</AddressPrefix>
	    </AddressSpace>
	    <Subnets>
	      <Subnet name="Frontend">
	        <AddressPrefix>10.1.0.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Midtier">
	        <AddressPrefix>10.1.1.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Backend">
	        <AddressPrefix>10.1.2.0/23</AddressPrefix>
	      </Subnet>
	      <Subnet name="GatewaySubnet">
	        <AddressPrefix>10.1.200.0/28</AddressPrefix>
	      </Subnet>
	    </Subnets>
	… Skip over the remainder section …
	</VirtualNetworkSite>


Necesitará cumplir los siguientes requisitos previos antes de poder ejecutar los comandos de PowerShell del ejemplo.

- Una suscripción de Azure.
- Una red virtual configurada. Para obtener más información sobre redes virtuales, consulte [Información general sobre redes virtuales](virtual-networks-overview.md).
- La versión más reciente de Azure PowerShell descargada e instalada. Consulte [Instalación y configuración de Azure PowerShell](../install-configure-powershell).

Para crear una máquina virtual con varias tarjetas NIC, siga estos pasos:

1. Seleccione una imagen de máquina virutal en la galería de imágenes de máquinas virtuales de Azure. Tenga en cuenta que las imágenes cambian con frecuencia y están disponibles por región. La imagen especificada en el ejemplo siguiente puede cambiar o puede no estar presente en su región, así que asegúrese de especificar la imagen que necesita. 
	    
		$image = Get-AzureVMImage `
	    	-ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. Creación de una configuración de máquina virtual

		$vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
			-Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. Cree el inicio de sesión de administrador predeterminado.

		Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
			-Password "<YourAdminPassword>"

1. Agregue NIC adicionales a la configuración de la máquina virtual.

		Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
			-SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm 
		Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
			-SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. Especifique la subred y la dirección IP de la NIC predeterminada.

		Set-AzureSubnet -SubnetNames "Frontend" -VM $vm 
		Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm

1. Cree la máquina virtual en la red virtual.

		New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE] La red virtual que especifique aquí debe existir previamente (tal como se indicó en los requisitos previos). En el ejemplo siguiente se especifica una red virtual denominada **MultiNIC VNet**.

## Acceso de la NIC secundaria a otras subredes

El modelo actual de Azure se basa en que todas las NIC en una máquina virtual están configuradas con una puerta de enlace predeterminada. Esto permite a las NIC comunicarse con las direcciones IP fuera de su subred. En sistemas operativos que usan un modelo de enrutamiento de host no seguro, como Linux, se interrumpirá la conectividad a Internet si el tráfico de entrada y salida utiliza NIC diferentes.

Para solucionar este problema, Azure publicará una actualización en las primeras semanas de julio de 2015 en la plataforma, y que permitirá quitar la puerta de enlace predeterminada de la NIC secundaria. Esto no afectará a las máquinas virtuales existentes hasta que las reinicie. Una vez reiniciadas, se aplicará la nueva configuración y se limitará el flujo de tráfico en las NIC secundarias para que pueda permanecer dentro de la misma subred. Si los usuarios desean habilitar las NIC secundarias para hablar fuera de su propia subred, tendrán que agregar una entrada en la tabla de enrutamiento para configurar la puerta de enlace, tal y como se describe a continuación.

### Configurar las máquinas virtuales de Windows

Suponga que tiene una máquina virtual de Windows con dos NIC, tal y como se muestra a continuación:

- Dirección IP de la NIC principal: 192.168.1.4
- Dirección IP de la NIC secundaria: 192.168.2.5

La tabla de enrutamiento de IPv4 para esta máquina virtual tendría este aspecto:

	IPv4 Route Table
	===========================================================================
	Active Routes:
	Network Destination        Netmask          Gateway       Interface  Metric
	          0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
	        127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
	        127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
	  127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
	    168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
	      192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
	      192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
	    192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
	      192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
	      192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
	    192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
	        224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
	        224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
	        224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
	  255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
	  255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
	  255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
	===========================================================================

Tenga en cuenta que la ruta predeterminada (0.0.0.0) sólo está disponible para la NIC principal. No podrá obtener acceso a recursos externos a la subred de la NIC secundaria, tal y como se muestra a continuación:

	C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
	 
	Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.

Para agregar una ruta predeterminada en la NIC secundaria, siga estos pasos:

1. Desde un símbolo del sistema, ejecute el comando siguiente para identificar el número de índice de la NIC secundaria:

		C:\Users\Administrator>route print
		===========================================================================
		Interface List
		 29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
		 27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
		  1...........................Software Loopback Interface 1
		 14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
		 20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
		===========================================================================

2. Busque la segunda entrada en la tabla con un índice de 27 (en este ejemplo).
3. Desde el símbolo del sistema, ejecute el comando **route add** tal y como se muestra a continuación. En este ejemplo, está especificando 192.168.2.1 como puerta de enlace predeterminada para la NIC secundaria:

		route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27

4. Para probar la conectividad, vuelva al símbolo del sistema e intente hacer ping en una subred distinta de la NIC secundaria, tal y como se muestra en el ejemplo siguiente:

		C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
		 
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
		Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128

5. Asimismo, puede consultar la tabla de rutas para comprobar la ruta recién agregada, tal y como se muestra a continuación:

		C:\Users\Administrator>route print

		...

		IPv4 Route Table
		===========================================================================
		Active Routes:
		Network Destination        Netmask          Gateway       Interface  Metric
		          0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
		          0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
		        127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### Configurar máquinas virtuales de Linux

En cuanto a las máquinas virtuales de Linux, puesto que el comportamiento predeterminado está usando el enrutamiento del host no seguro, le recomendamos restrinja el flujo de tráfico de las NIC secundarias para que permanezca dentro de la misma subred. Sin embargo, si ciertos escenarios exigen que tenga conectividad fuera de la subred, los usuarios deben habilitar el enrutamiento basado en las directivas para asegurarse de que el tráfico de entrada y salida utiliza la misma NIC.

## Pasos siguientes

- Implemente [máquinas virtuales MultiNIC en un escenario de aplicación de 2 niveles en una implementación del Administrador de recursos](virtual-network-deploy-multinic-arm-template.md).
- Implemente [máquinas virtuales MultiNIC en un escenario de aplicación de 2 niveles en una implementación clásica](virtual-network-deploy-multinic-classic-ps.md).

<!---HONumber=AcomDC_0211_2016-->