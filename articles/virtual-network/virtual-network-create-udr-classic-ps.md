<properties 
   pageTitle="Control del enrutamiento y uso de aplicaciones virtuales mediante PowerShell en el modelo de implementación clásico | Microsoft Azure"
   description="Obtenga información sobre cómo controlar el enrutamiento en redes virtuales mediante PowerShell en el modelo de implementación clásico"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

#Control del enrutamiento y uso de aplicaciones virtuales (clásico) mediante PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación clásico.

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

En los siguientes comandos de Azure PowerShell de ejemplo se presupone que ya se ha creado un entorno simple según el escenario anterior. Si desea ejecutar los comandos según aparecen en este documento, cree primero el entorno mostrado en la [creación de una red virtual (clásico) mediante PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Creación de la ruta definida por el usuario para la subred front-end
Para crear la tabla de rutas y la ruta necesaria para la subred front-end según el escenario anterior, siga estos pasos.

3. Ejecute el cmdlet **`New-AzureRouteTable`** para crear una tabla de rutas para la subred front-end.

		New-AzureRouteTable -Name UDR-FrontEnd `
			-Location uswest `
			-Label "Route table for front end subnet"

	Salida:

		Name         Location   Label                          
		----         --------   -----                          
		UDR-FrontEnd West US    Route table for front end subnet

4. Ejecute el cmdlet **`Set-AzureRoute`** para crear una ruta en la tabla de rutas creada anteriormente para enviar todo el tráfico destinado a la subred back-end (192.168.2.0/24) a la VM **FW1** (192.168.0.4).
	
		Get-AzureRouteTable UDR-FrontEnd `
			|Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4

	Salida:

		Name     : UDR-FrontEnd
		Location : West US
		Label    : Route table for frontend subnet
		Routes   : 
		           Name                 Address Prefix    Next hop type        Next hop IP address
		           ----                 --------------    -------------        -------------------
		           RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. Ejecute el cmdlet **`Set-AzureSubnetRouteTable`** para asociar la tabla de rutas creada anteriormente a la subred **FrontEnd**.

		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName FrontEnd `
			-RouteTableName UDR-FrontEnd
 
## Creación de la ruta definida por el usuario para la subred back-end
Para crear la tabla de rutas y la ruta necesaria para la subred back-end según el escenario anterior, siga estos pasos.

3. Ejecute el cmdlet **`New-AzureRouteTable`** para crear una tabla de rutas para la subred back-end.

		New-AzureRouteTable -Name UDR-BackEnd `
			-Location uswest `
			-Label "Route table for back end subnet"

4. Ejecute el cmdlet **`Set-AzureRoute`** para crear una ruta en la tabla de rutas creada anteriormente para enviar todo el tráfico destinado a la subred front-end (192.168.1.0/24) a la VM **FW1** (192.168.0.4).

		Get-AzureRouteTable UDR-BackEnd `
			|Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4

5. Ejecute el cmdlet **`Set-AzureSubnetRouteTable`** para asociar la tabla de rutas creada anteriormente a la subred **BackEnd**.

		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName FrontEnd `
			-RouteTableName UDR-FrontEnd

## Habilitar el reenvío IP en la VM FW1
Para habilitar el reenvío IP en la VM FW1, siga estos pasos.

1. Ejecute el cmdlet **`Get-AzureIPForwarding`** para comprobar el estado del reenvío IP.

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Get-AzureIPForwarding

	Salida:

		Disabled

2. Ejecute el comando **`Set-AzureIPForwarding`** para habilitar el reenvío IP para la VM *FW1*.

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Set-AzureIPForwarding -Enable

<!---HONumber=AcomDC_0211_2016-->