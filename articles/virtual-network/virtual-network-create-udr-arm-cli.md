<properties 
   pageTitle="Control del enrutamiento y uso de aplicaciones virtuales en el Administrador de recursos mediante la CLI de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo controlar el enrutamiento y uso de aplicaciones virtuales mediante la CLI de Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

#Creación de rutas definidas por el usuario (UDR) en la CLI de Azure

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puede [crear rutas definidas por el usuario en el modelo de implementación clásico](virtual-network-create-udr-classic-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

En los siguientes comandos de CLI de Azure de ejemplo se presupone que ya se ha creado un entorno simple según el escenario anterior. Si desea ejecutar los comandos tal y como aparecen en este documento, cree primero el entorno de prueba mediante la implementación de [esta plantilla](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), haga clic en **Implementar en Azure**, reemplace los valores de parámetro predeterminados si es necesario y siga las instrucciones del portal.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Creación de la ruta definida por el usuario para la subred front-end
Para crear la tabla de rutas y la ruta necesaria para la subred front-end según el escenario anterior, siga estos pasos.

3. Ejecute el comando **`azure network route-table create`** para crear una tabla de rutas para la subred front-end.

		azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest

	Salida:

		info:    Executing command network route-table create
		info:    Looking up route table "UDR-FrontEnd"
		info:    Creating route table "UDR-FrontEnd"
		info:    Looking up route table "UDR-FrontEnd"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/
		routeTables/UDR-FrontEnd
		data:    Name                            : UDR-FrontEnd
		data:    Type                            : Microsoft.Network/routeTables
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		info:    network route-table create command OK

	Parámetros:- **-g (o --resource-group)**. Nombre del grupo de recursos donde se creará el grupo de seguridad de red En este escenario, *TestRG*. - **-l (o --location)**. Región de Azure donde se creará la red virtual. En este escenario, *westus*. - **-n (o --name)**. Nombre del nuevo grupo de seguridad de red. En este escenario, *NSG-FrontEnd*.

4. Ejecute el comando **`azure network route-table route create`** para crear una ruta en la tabla de rutas creada anteriormente para enviar todo el tráfico destinado a la subred back-end (192.168.2.0/24) a la máquina virtual **FW1** (192.168.0.4).

		azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4

	Salida:

		info:    Executing command network route-table route create
		info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
		info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
		info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		routeTables/UDR-FrontEnd/routes/RouteToBackEnd
		data:    Name                            : RouteToBackEnd
		data:    Provisioning state              : Succeeded
		data:    Next hop type                   : VirtualAppliance
		data:    Next hop IP address             : 192.168.0.4
		data:    Address prefix                  : 192.168.2.0/24
		info:    network route-table route create command OK

	Parámetros: - **-r (o --route-table-name)**. Nombre de la tabla de rutas a la que se agregará la ruta. Para nuestro escenario, *UDR-FrontEnd*.- **- a (o --address-prefix)**. Prefijo de dirección de la subred a la que se destinan los paquetes. Para nuestro escenario, *192.168.2.0/24*. - **-t (o --next-hop-type)**. Tipo de objeto al que se enviará el tráfico. Los valores posibles son *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* o *None*. - **-p (o --next-hop-ip-address**). Dirección IP para el próximo salto. En este escenario, *192.168.0.4*.

5. Ejecute el comando **`azure network vnet subnet set`** para asociar la tabla de rutas creada anteriormente a la subred **FrontEnd**.

		azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd

	Salida:

		info:    Executing command network vnet subnet set
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up route table "UDR-FrontEnd"
		info:    Setting subnet "FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:    Network security group          : [object Object]
		data:    Route Table                     : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		routeTables/UDR-FrontEnd
		data:    IP configurations:
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
		igurations/ipconfig1
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
		igurations/ipconfig1
		data:    
		info:    network vnet subnet set command OK

	Parámetros: - **-t (o --vnet-name)**. Nombre de la red virtual donde se encuentra la subred. En este escenario, *TestVNet*.
 
## Creación de la ruta definida por el usuario para la subred back-end
Para crear la tabla de rutas y la ruta necesaria para la subred back-end según el escenario anterior, siga estos pasos.

1. Ejecute el comando **`azure network route-table create`** para crear una tabla de rutas para la subred back-end.

		azure network route-table create -g TestRG -n UDR-BackEnd -l westus

4. Ejecute el comando **`azure network route-table route create`** para crear una ruta en la tabla de rutas creada anteriormente para enviar todo el tráfico destinado a la subred front-end (192.168.1.0/24) a la VM **FW1** (192.168.0.4).

		azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4

5. Ejecute el comando **`azure network vnet subnet set`** para asociar la tabla de rutas creada anteriormente a la subred **BackEnd**.

		azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd

## Habilitación del reenvío IP en FW1
Para habilitar el reenvío IP en la NIC usada por **FW1**, siga estos pasos.

1. Ejecute el comando **`azure network nic show`** y observe el valor de **Habilitar reenvío IP**. Se debe establecer en *false*.

		azure network nic show -g TestRG -n NICFW1

	Salida:
		
		info:    Executing command network nic show
		info:    Looking up the network interface "NICFW1"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkInterfaces/NICFW1
		data:    Name                            : NICFW1
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    MAC address                     : 00-0D-3A-30-95-B3
		data:    Enable IP forwarding            : false
		data:    Tags                            : displayName=NetworkInterfaces - DMZ
		data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
		virtualMachines/FW1
		data:    IP configurations:
		data:      Name                          : ipconfig1
		data:      Provisioning state            : Succeeded
		data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		publicIPAddresses/PIPFW1
		data:      Private IP address            : 192.168.0.4
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/DMZ
		data:    
		info:    network nic show command OK

2. Ejecute el comando **`azure network nic set`** para habilitar el reenvío IP.

		azure network nic set -g TestRG -n NICFW1 -f true

	Salida:

		info:    Executing command network nic set
		info:    Looking up the network interface "NICFW1"
		info:    Updating network interface "NICFW1"
		info:    Looking up the network interface "NICFW1"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkInterfaces/NICFW1
		data:    Name                            : NICFW1
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    MAC address                     : 00-0D-3A-30-95-B3
		data:    Enable IP forwarding            : true
		data:    Tags                            : displayName=NetworkInterfaces - DMZ
		data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
		virtualMachines/FW1
		data:    IP configurations:
		data:      Name                          : ipconfig1
		data:      Provisioning state            : Succeeded
		data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		publicIPAddresses/PIPFW1
		data:      Private IP address            : 192.168.0.4
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/DMZ
		data:    
		info:    network nic set command OK

	Parámetros:

	- **-f (o --enable-ip-forwarding)**. *true* o *false*.

<!---HONumber=AcomDC_1217_2015-->