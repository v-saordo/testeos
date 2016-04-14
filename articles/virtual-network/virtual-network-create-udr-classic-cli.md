<properties 
   pageTitle="Control del enrutamiento y uso de aplicaciones virtuales mediante la CLI de Azure en el modelo de implementación clásico | Microsoft Azure"
   description="Aprenda cómo controlar el enrutamiento en redes virtuales mediante la CLI de Azure en el modelo de implementación clásico"
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
   ms.date="12/11/2015"
   ms.author="telmos" />

#Control del enrutamiento y uso de aplicaciones virtuales (clásico) mediante la CLI de Azure

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación clásico. También puede [controlar el enrutamiento y el uso de aplicaciones virtuales en el modelo de implementación del Administrador de recursos](virtual-network-create-udr-arm-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

En los siguientes comandos de CLI de Azure de ejemplo se presupone que ya se ha creado un entorno simple según el escenario anterior. Si desea ejecutar los comandos según aparecen en este documento, cree primero el entorno mostrado en la [creación de una red virtual (clásico) mediante la CLI de Azure](virtual-networks-create-vnet-classic-cli.md).

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Creación de la ruta definida por el usuario para la subred front-end
Para crear la tabla de rutas y la ruta necesaria para la subred front-end según el escenario anterior, siga estos pasos.

1. Ejecute **`azure config mode`** para cambiar al modo clásico.

		azure config mode asm

	Salida:

		info:    New mode is asm

3. Ejecute el comando **`azure network route-table create`** para crear una tabla de rutas para la subred front-end.

		azure network route-table create -n UDR-FrontEnd -l uswest

	Salida:

		info:    Executing command network route-table create
		info:    Creating route table "UDR-FrontEnd"
		info:    Getting route table "UDR-FrontEnd"
		data:    Name                            : UDR-FrontEnd
		data:    Location                        : West US
		info:    network route-table create command OK

	Parámetros: - **-l (o --location)**. Región de Azure donde se creará la red virtual. En este escenario, *westus*. - **-n (o --name)**. Nombre del nuevo grupo de seguridad de red. En este escenario, *NSG-FrontEnd*.

4. Ejecute el comando **`azure network route-table route set`** para crear una ruta en la tabla de rutas creada anteriormente para enviar todo el tráfico destinado a la subred back-end (192.168.2.0/24) a la VM **FW1** (192.168.0.4).

		azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4

	Salida:

		info:    Executing command network route-table route set
		info:    Getting route table "UDR-FrontEnd"
		info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
		info:    network route-table route set command OK

	Parámetros: - **-r (o --route-table-name)**. Nombre de la tabla de rutas a la que se agregará la ruta. Para nuestro escenario, *UDR-FrontEnd*.- **- a (o --address-prefix)**. Prefijo de dirección de la subred a la que se destinan los paquetes. Para nuestro escenario, *192.168.2.0/24*. - **-t (o --next-hop-type)**. Tipo de objeto al que se enviará el tráfico. Los valores posibles son *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* o *None*. - **-p (o --next-hop-ip-address**). Dirección IP para el próximo salto. En este escenario, *192.168.0.4*.

5. Ejecute el comando **`azure network vnet subnet route-table add`** para asociar la tabla de rutas creada anteriormente a la subred **FrontEnd**.

		azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd

	Salida:

		info:    Executing command network vnet subnet route-table add
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up network configuration
		info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
		info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
		info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
		data:    Route table name                : UDR-FrontEnd
		data:      Location                      : West US
		data:      Routes:
		info:    network vnet subnet route-table add command OK	

	Parámetros: - **-t (o --vnet-name)**. Nombre de la red virtual donde se encuentra la subred. Para nuestro escenario, *TestVNet*.- **-n (o --subnet-name**. Nombre de la subred a la que se agregará la tabla de rutas. En este escenario, *FrontEnd*.
 
## Creación de la ruta definida por el usuario para la subred back-end
Para crear la tabla de rutas y la ruta necesaria para la subred back-end según el escenario anterior, siga estos pasos.

3. Ejecute el comando **`azure network route-table create`** para crear una tabla de rutas para la subred back-end.

		azure network route-table create -n UDR-BackEnd -l uswest

4. Ejecute el comando **`azure network route-table route set`** para crear una ruta en la tabla de rutas creada anteriormente para enviar todo el tráfico destinado a la subred front-end (192.168.1.0/24) a la VM **FW1** (192.168.0.4).

		azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4

5. Ejecute el comando **`azure network vnet subnet route-table add`** para asociar la tabla de rutas creada anteriormente a la subred **BackEnd**.

		azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd

<!---HONumber=AcomDC_1217_2015-->