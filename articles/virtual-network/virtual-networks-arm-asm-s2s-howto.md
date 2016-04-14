<properties 
   pageTitle="Cómo conectar redes virtuales clásicas a redes virtuales ARM en Azure"
   description="Obtenga información acerca de cómo crear una conexión VPN entre redes virtuales clásicas y nuevas"
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

En tales situaciones, deseará asegurarse de que la nueva infraestructura sea capaz de comunicarse con sus recursos clásicos. Puede hacerlo mediante la creación de una conexión VPN entre las dos redes virtuales.

En este artículo, aprenderá a crear una conexión VPN de sitio a sitio (S2S) entre una red virtual clásica y una red virtual de ARM.

>[AZURE.NOTE]En este artículo se supone que ya tiene redes virtuales clásicas y redes virtuales ARM y que está familiarizado con la configuración de una conexión VPN de S2S para redes virtuales clásicas. Para una solución integral detallada en la conectividad de VPN S2S entre redes virtuales clásicas y ARM, visite [Guía de soluciones: conexión de una red virtual clásica y una red virtual ARM mediante una VPN S2S](../virtual-networks-arm-asm-s2s.md).

Puede ver un resumen de las tareas necesarias para crear una conexión VPN S2S entre una red virtual clásica y una red virtual ARM mediante las puertas de enlace Azure facilitadas a continuación.

1 - [Crear una puerta de enlace de VPN para la red virtual clásica](#Step-1:-Create-a-VPN-gateway-for-the-classic-VNet)

2 - [Crear una puerta de enlace de VPN para la red virtual ARM](#Step-2:-Create-a-VPN-gateway-for-the-ARM-VNet)

3 - [Crear una conexión entre las puertas de enlace](#Step-3:-Create-a-connection-between-the-gateways)

## Paso 1: Crear una puerta de enlace de VPN para la red virtual clásica

Para crear la puerta de enlace de VPN para la red virtual clásica, siga las instrucciones siguientes.

1. Abra el portal clásico desde https://manage.windowsazure.com y escriba sus credenciales, si es necesario.
2. En la esquina inferior izquierda de la pantalla, haga clic en el botón **NUEVO**, haga clic en **SERVICIOS DE RED** en**REDES VIRTUALES** y, a continuación, en**AGREGAR RED LOCAL**.
3. En la ventana **Especificar los detalles de la red local**, escriba un nombre para la red virtual ARM a la que desea conectarse y, a continuación, en la esquina inferior derecha de la ventana, haga clic en el botón de flecha.
3. En el cuadro de texto **IP DE INICIO** del espacio de direcciones, escriba el prefijo de red de la red virtual ARM a la que desea conectarse. 
4. En el menú desplegable **CIDR (RECUENTO DE DIRECCIONES)**, seleccione el número de bits usados para la parte de la red del bloque CIDR usado por la red virtual ARM a la que desea conectarse.
5. En **DIRECCIÓN IP DEL DISPOSITIVO VPN (OPCIONAL)**, escriba cualquier dirección IP pública válida. Cambiaremos esta dirección IP más adelante. A continuación, haga clic en el botón de marca de verificación de la parte inferior derecha de la pantalla. En la siguiente ilustración se muestra la configuración de ejemplo de esta página.

	![Configuración de la red local](..\virtual-network\media\virtual-networks-arm-asm-s2s-howto\figurex1.png)

5. En la página **Redes**, haga clic en **REDES VIRTUALES**, en la red virtual clásica y, a continuación, en **CONFIGURAR**.
6. En **conectividad de sitio a sitio**, marque la casilla de verificación **conectarse a la red local**.
7. Seleccione la red local que creó en el paso 4 de la lista de redes disponibles desde el menú desplegable **RED LOCAL** y, a continuación, haga clic en **GUARDAR**.
8. Una vez guardada la configuración, haga clic en **PANEL** y, a continuación, en la parte inferior de la página, haga clic en **CREAR PUERTA DE ENLACE**, en **ENRUTAMIENTO DINÁMICO** y, por último, en **SÍ**.
9. Espere a que se cree la puerta de enlace y copie su dirección IP pública. La necesitará para configurar la puerta de enlace en la red virtual ARM.

## Paso 2: Crear una puerta de enlace VPN para la red virtual ARM

Para crear una puerta de enlace de VPN para la red virtual ARM, siga las instrucciones siguientes.

1. Desde una consola de PowerShell, ejecute el comando siguiente para cambiar al modo ARM.

		Switch-AzureMode AzureResourceManager

2. Ejecute el comando siguiente para crear una red local. La red local debe usar el bloque CIDR de la red virtual clásica a la que desea conectarse y la dirección IP pública de la puerta de enlace creada en el paso 1 anterior.

		New-AzureLocalNetworkGateway -Name VNetClassicNetwork `
			-Location "East US" -AddressPrefix "10.0.0.0/20" `
			-GatewayIpAddress "168.62.190.190" -ResourceGroupName RG1

3. Ejecute el comando siguiente para crear una dirección IP pública para la puerta de enlace.

		$ipaddress = New-AzurePublicIpAddress -Name gatewaypubIP`
			-ResourceGroupName RG1 -Location "East US" `
			-AllocationMethod Dynamic

4. Ejecute el comando siguiente para recuperar la subred usada para la puerta de enlace.

		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name GatewaySubnet `
			-VirtualNetwork (Get-AzureVirtualNetwork -Name VNetARM -ResourceGroupName RG1) 

	>[AZURE.IMPORTANT]La subred de la puerta de enlace ya debe existir previamente y debe tener el nombre GatewaySubnet.

5. Ejecute el comando siguiente para crear un objeto de configuración de IP para la puerta de enlace. Observe el identificador de una subred de puerta de enlace. Esa subred debe existir en la red virtual.

		$ipconfig = New-AzureVirtualNetworkGatewayIpConfig `
			-Name ipconfig -PrivateIpAddress 10.1.2.4 `
			-SubnetId $subnet.id -PublicIpAddressId $ipaddress.id

	>[AZURE.IMPORTANT]Debe pasarse la propiedad de Id de la subred y los objetos de la dirección IP respectivamente a los parámetros *SubnetId* y *PublicIpAddressId*. No puede usar una cadena sencilla.
	
5. Ejecute el comando siguiente para crear la puerta de enlace de la red virtual ARM.

		New-AzureVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1 `
			-Location "East US" -GatewayType Vpn -IpConfigurations $ipconfig `
			-EnableBgp $false -VpnType RouteBased

6. Una vez creada la puerta de enlace VPN, ejecute el comando siguiente para recuperar su dirección IP pública. Copie la dirección IP, la necesitará para configurar la red local para la red virtual clásica.

		Get-AzurePublicIpAddress -Name gatewaypubIP -ResourceGroupName RG1

## Paso 3: Crear una conexión entre las puertas de enlace

1. Abra el portal clásico desde https://manage.windowsazure.com y escriba sus credenciales, si es necesario.
2. En el portal clásico, desplácese hacia abajo, haga clic en **REDES**, en **REDES LOCALES**, en la red virtual ARM a la que desee conectarse y, por último, en el botón **EDITAR**.
3. En **DIRECCIÓN IP DE DISPOSITIVO VPN (OPCIONAL)**, escriba la dirección IP para la recuperación de la puerta de enlace de red virtual ARM en el paso 2 anterior, haga clic en la flecha derecha situada en la esquina inferior derecha y, por último, en el botón de la marca de verificación.
4. Desde una consola de PowerShell, cambie al modo de Administrador de servicios de Azure ejecutando el siguiente comando.

		Switch-AzureMode AzureServiceManager

5. Ejecute el comando siguiente para configurar una clave compartida. Asegúrese de cambiar los nombres de las redes virtuales por los nombres de su propia red virtual.

		Set-AzureVNetGatewayKey -VNetName VNetClassic `
			-LocalNetworkSiteName VNetARM -SharedKey abc123

6. Desde una consola de PowerShell, ejecute el comando siguiente para cambiar al modo de Administrador de recursos de Azure ejecutando el siguiente comando.

		Switch-AzureMode AzureResourceManager

7. Ejecute los comandos siguientes para crear la conexión VPN.

		$vnet01gateway = Get-AzureLocalNetworkGateway -Name VNetClassic -ResourceGroupName RG1
		$vnet02gateway = Get-AzureVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1
		
		New-AzureVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "East US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

## Pasos siguientes

- Obtener más información sobre [el proveedor de recursos de red (NRP) para ARM](../resource-groups-networking.md).
- Cree una [solución integral mediante la conexión de una red virtual clásica a una ARM mediante una VPN S2S](../virtual-networks-arm-asm-s2s.md).

<!---HONumber=AcomDC_1217_2015-->