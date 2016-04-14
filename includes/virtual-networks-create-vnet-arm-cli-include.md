## Cómo crear una red virtual mediante la CLI de Azure

Puede utilizar la CLI de Azure para administrar los recursos de Azure desde el símbolo del sistema de cualquier equipo que ejecute Windows, Linux o bien OSX. Para crear una red virtual mediante la CLI de Azure, siga estos pasos.

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](xplat-cli-install.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.
2. Ejecute el comando **azure config mode** para cambiar al modo de Administrador de recursos, como se muestra a continuación.

		azure config mode arm

	Este es el resultado esperado del comando anterior:

		info:    New mode is arm

3. Si es necesario, ejecute **azure group create** para crear un nuevo grupo de recursos, tal como se muestra a continuación. Observe la salida del comando. En la lista que se muestra en la salida se explican los parámetros utilizados. Para obtener más información sobre los grupos de recursos, visite [Información general del Administrador de recursos de Azure](resource-group-overview.md/#resource-groups).

		azure group create -n TestRG -l centralus

	Este es el resultado esperado del comando anterior:

		info:    Executing command group create
		+ Getting resource group TestRG
		+ Creating resource group TestRG
		info:    Created resource group TestRG
		data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            centralus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

	- **-n (o --name)**. Nombre del nuevo grupo de recursos. En este escenario, *TestRG*.
	- **-l (o --location)**. Región de Azure donde se creará el nuevo grupo de recursos. En este escenario, *centralus*.

4. Ejecute el comando **azure network vnet create** para crear una red virtual y una subred, como se muestra a continuación.

		azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

	Este es el resultado esperado del comando anterior:

		info:    Executing command network vnet create
		+ Looking up virtual network "TestVNet"
		+ Creating virtual network "TestVNet"
		+ Loading virtual network state
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : centralus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		info:    network vnet create command OK

	- **-g (o --resource-group)**. Nombre del grupo de recursos donde se creará la red virtual. En este escenario, *TestRG*.
	- **-n (o --name)**. Nombre de la red virtual que se va a crear. En este escenario, *TestVNet*.
	- **-a (o --address-prefixes)**. Lista de bloques CIDR utilizados para el espacio de direcciones de la red virtual. En este escenario, *192.168.0.0/16*.
	- **-l (o --location)**. Región de Azure donde se creará la red virtual. En este escenario, *centralus*.

5. Ejecute el comando **azure network vnet subnet create** para crear una subred, como se muestra a continuación. Observe la salida del comando. En la lista que se muestra en la salida se explican los parámetros utilizados.

		azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

	Este es el resultado esperado del comando anterior:

		info:    Executing command network vnet subnet create
		+ Looking up the subnet "FrontEnd"
		+ Creating subnet "FrontEnd"
		+ Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:
		info:    network vnet subnet create command OK

	- **-e (o --vnet-name**. Nombre de la red virtual donde se creará la subred. En este escenario, *TestVNet*.
	- **-n (o --name)**. Nombre de la nueva subred. En este escenario, *FrontEnd*.
	- **-a (o --address-prefix)**. Bloque CIDR de subred. En este escenario, *192.168.1.0/24*.

6. Repita el paso 5 anterior para crear otras subredes, si es necesario. En este escenario, ejecute el siguiente comando para crear la subred *BackEnd*.

		azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. Ejecute el comando **azure network vnet show** para ver las propiedades de la nueva red virtual, tal como se muestra a continuación.

		azure network vnet show -g TestRG -n TestVNet

	Este es el resultado esperado del comando anterior:

		info:    Executing command network vnet show
		+ Looking up virtual network "TestVNet"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : centralus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		data:    Subnets:
		data:      Name                          : FrontEnd
		data:      Address prefix                : 192.168.1.0/24
		data:
		data:      Name                          : BackEnd
		data:      Address prefix                : 192.168.2.0/24
		data:
		info:    network vnet show command OK

<!---HONumber=Oct15_HO3-->