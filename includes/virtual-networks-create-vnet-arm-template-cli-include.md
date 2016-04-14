## Implementar la plantilla ARM mediante la CLI de Azure

Para implementar la plantilla ARM mediante la CLI de Azure, siga estos pasos.

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](xplat-cli-install.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.
2. Ejecute el comando **`azure config mode`** para cambiar al modo de Administrador de recursos, como se muestra a continuación.

		azure config mode arm

	Este es el resultado esperado del comando anterior:

		info:    New mode is arm

3. Si es necesario, ejecute **`azure group create`** para crear un nuevo grupo de recursos, como se muestra a continuación. Observe la salida del comando. En la lista que se muestra en la salida se explican los parámetros utilizados. Para obtener más información sobre los grupos de recursos, visite [Información general del Administrador de recursos de Azure](resource-group-overview.md).

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

4. Ejecute el cmdlet **`azure group deployment create`** para implementar la nueva red virtual mediante la plantilla y los archivos de parámetros que descargó y modificó anteriormente. En la lista que se muestra en la salida se explican los parámetros utilizados.

		azure group deployment create -g TestRG -n TestVNetDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

	Este es el resultado esperado del comando anterior:

		info:    Executing command group deployment create
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "TestVNetDeployment"
		+ Registering providers
		info:    Registering provider microsoft.network
		+ Waiting for deployment to complete
		data:    DeploymentName     : TestVNetDeployment
		data:    ResourceGroupName  : TestRG
		data:    ProvisioningState  : Succeeded
		data:    Timestamp          : 2015-08-14T21:56:11.152759Z
		data:    Mode               : Incremental
		data:    Name           Type    Value
		data:    -------------  ------  --------------
		data:    location       String  Central US
		data:    vnetName       String  TestVNet
		data:    addressPrefix  String  192.168.0.0/16
		data:    subnet1Prefix  String  192.168.1.0/24
		data:    subnet1Name    String  FrontEnd
		data:    subnet2Prefix  String  192.168.2.0/24
		data:    subnet2Name    String  BackEnd
		info:    group deployment create command OK

	- **-g (o --resource-group)**. Nombre del grupo de recursos en el que se creará la red virtual.
	- **-f (o --template-file)**. Ruta de acceso al archivo de plantilla ARM.
	- **-e (o --parameters-file)**. Ruta de acceso al archivo de parámetros ARM.

5. Ejecute el comando **`azure network vnet show`** para ver las propiedades de la nueva red virtual, como se muestra a continuación.

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

<!---HONumber=AcomDC_0211_2016-->