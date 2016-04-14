<properties 
   pageTitle="Establecimiento de una IP privada estática en el modo ARM mediante la CLI | Microsoft Azure"
   description="Descripción de las IP estáticas (DIP) y su administración en el modo ARM mediante la CLI"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
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

# Configuración de una dirección IP privada estática en la CLI de Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puedes [Administrar la dirección IP privada estática en el modelo de implementación clásica](virtual-networks-static-private-ip-classic-cli.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

En los siguientes comandos de ejemplo de la CLI de Azure se presupone que ya se creó un entorno simple. Si desea ejecutar los comandos que aparecen en este documento, cree primero el entorno de prueba descrito en [creación de una red virtual](virtual-networks-create-vnet-arm-cli.md).

## Especificación de una dirección IP privada estática al crear una VM
Para crear una VM denominada *DNS01* en la subred de *FrontEnd* de una Red virtual denominada *TestVNet* con una IP privada estática de *192.168.1.101*, sigue estos pasos:

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](xplat-cli-install.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.

2. Ejecuta el comando **azure config mode** para cambiar al modo de Administrador de recursos, como se muestra a continuación.

		azure config mode arm

	Resultado esperado:

		info:    New mode is arm

3. Ejecuta **azure network public-ip create** para crear una dirección IP pública para la VM. En la lista que se muestra en la salida se explican los parámetros utilizados.

		azure network public-ip create -g TestRG -n TestPIP -l centralus
	
	Resultado esperado:

		info:    Executing command network public-ip create
		+ Looking up the public ip "TestPIP"
		+ Creating public ip address "TestPIP"
		+ Looking up the public ip "TestPIP"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
		data:    Name                            : TestPIP
		data:    Type                            : Microsoft.Network/publicIPAddresses
		data:    Location                        : centralus
		data:    Provisioning state              : Succeeded
		data:    Allocation method               : Dynamic
		data:    Idle timeout                    : 4
		info:    network public-ip create command OK

	- **-g (or --resource-group)**. Nombre del grupo de recursos en el que se creará la dirección IP pública.
	- **-n (or --name)**. Nombre de la dirección IP pública.
	- **-l (or --location)**. Región de Azure donde se creará la dirección IP pública. En este escenario, *centralus*.

3. Ejecuta el comando **azure network nic create** para crear una NIC con una dirección IP privada estática. En la lista que se muestra en la salida se explican los parámetros utilizados.

		azure network nic create -g TestRG -n TestNIC -l centralus -a 192.168.1.101 -m TestVNet -k FrontEnd

	Resultado esperado:

		+ Looking up the network interface "TestNIC"
		+ Looking up the subnet "FrontEnd"
		+ Creating network interface "TestNIC"
		+ Looking up the network interface "TestNIC"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
		data:    Name                            : TestNIC
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : centralus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.1.101
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:
		info:    network nic create command OK

	- **-a (or --private-ip-address)**. Dirección IP privada estática para la NIC.
	- **-m (or --subnet-vnet-name)**. Nombre de la red virtual donde se creará la NIC.
	- **-k (or --subnet-name)**. Nombre de la subred donde se creará la NIC.

4. Ejecuta el comando **azure vm create** para crear la máquina virtual mediante la dirección IP pública y la NIC creadas anteriormente. En la lista que se muestra en la salida se explican los parámetros utilizados.

		azure vm create -g TestRG -n DNS01 -l centralus -y Windows -f TestNIC -i TestPIP -F TestVNet -j FrontEnd -o vnetstorage -q bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 -u adminuser -p AdminP@ssw0rd

	Resultado esperado:

		info:    Executing command vm create
		+ Looking up the VM "DNS01"
		info:    Using the VM Size "Standard_A1"
		warn:    The image "bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2" will be used for VM
		info:    The [OS, Data] Disk or image configuration requires storage account
		+ Looking up the storage account vnetstorage
		+ Looking up the NIC "TestNIC"
		info:    Found an existing NIC "TestNIC"
		info:    Found an IP configuration with virtual network subnet id "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "TestNIC"
		info:    Found public ip parameters, trying to setup PublicIP profile
		+ Looking up the public ip "TestPIP"
		info:    Found an existing PublicIP "TestPIP"
		info:    Configuring identified NIC IP configuration with PublicIP "TestPIP"
		+ Updating NIC "TestNIC"
		+ Looking up the NIC "TestNIC"
		+ Creating VM "DNS01"
		info:    vm create command OK

	- **-y (or --os-type)**. Tipo de sistema de operativo para la máquina virtual, ya sea *Windows* o *Linux*.
	- **-f (or --nic-name)**. Nombre de la NIC que usará la máquina virtual.
	- **-i (or --public-ip-name)**. Nombre de la dirección IP pública que usará la máquina virtual.
	- **-F (or --vnet-name)**. Nombre de la red virtual donde se creará la VM.
	- **-j (or --vnet-subnet-name)**. Nombre de la subred donde se creará la VM.

## Recuperación de la información de la dirección IP privada estática para una VM

Para ver la información de la dirección IP privada estática para la VM que se creó con el script anterior, ejecuta el siguiente comando de la CLI de Azure y observa los valores para *Método de asignación de dirección IP privada* y *Dirección IP privada*:

	azure vm show -g TestRG -n DNS01

Resultado esperado:

	info:    Executing command vm show
	+ Looking up the VM "DNS01"
	+ Looking up the NIC "TestNIC"
	+ Looking up the public ip "TestPIP
	data:    Id                              :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
	data:    ProvisioningState               :Succeeded
	data:    Name                            :DNS01
	data:    Location                        :centralus
	data:    Type                            :Microsoft.Compute/virtualMachines
	data:
	data:    Hardware Profile:
	data:      Size                          :Standard_A1
	data:
	data:    Storage Profile:
	data:      Source image:
	data:        Id                          :/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/services/images/bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
	data:
	data:      OS Disk:
	data:        OSType                      :Windows
	data:        Name                        :cli08d7bd987a0112a8-os-1441774961355
	data:        Caching                     :ReadWrite
	data:        CreateOption                :FromImage
	data:        Vhd:
	data:          Uri                       :https://vnetstorage2.blob.core.windows.net/vhds/cli08d7bd987a0112a8-os-1441774961355vhd
	data:
	data:    OS Profile:
	data:      Computer Name                 :DNS01
	data:      User Name                     :adminuser
	data:      Windows Configuration:
	data:        Provision VM Agent          :true
	data:        Enable automatic updates    :true
	data:
	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-90-1A-A8
	data:          Provisioning State        :Succeeded
	data:          Name                      :TestNIC
	data:          Location                  :centralus
	data:            Private IP alloc-method :Static
	data:            Private IP address      :192.168.1.101
	data:            Public IP address       :40.122.213.159
	info:    vm show command OK

## Eliminación de una dirección IP privada estática de una VM
No se puede eliminar una dirección IP privada estática de una NIC de la CLI de Azure para el Administrador de recursos. Tienes que crear una nueva NIC que use una dirección IP dinámica, eliminar la NIC anterior de la VM y después agregar la nueva a la VM. Para cambiar la NIC para la VM usada en los comandos anteriores, sigue estos pasos.
	
1. Ejecuta el comando**azure network nic create** para crear una nueva NIC con la asignación de IP dinámica. Observa cómo no tienes que especificar la dirección IP en este momento.

		azure network nic create -g TestRG -n TestNIC2 -l centralus -m TestVNet -k FrontEnd

	Resultado esperado:

		info:    Executing command network nic create
		+ Looking up the network interface "TestNIC2"
		+ Looking up the subnet "FrontEnd"
		+ Creating network interface "TestNIC2"
		+ Looking up the network interface "TestNIC2"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
		data:    Name                            : TestNIC2
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : centralus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.1.6
		data:      Private IP Allocation Method  : Dynamic
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:
		info:    network nic create command OK

2. Ejecuta el comando **azure vm set** para cambiar la NIC que usó la VM.

		azure vm set -g TestRG -n DNS01 -N TestNIC2

	Resultado esperado:

		info:    Executing command vm set
		+ Looking up the VM "DNS01"
		+ Looking up the NIC "TestNIC2"
		+ Updating VM "DNS01"
		info:    vm set command OK

3. Si quieres, puedes ejecutar el comando **azure network nic delete** para eliminar la NIC antigua.

		azure network nic delete -g TestRG -n TestNIC --quiet

	Resultado esperado:

		info:    Executing command network nic delete
		+ Looking up the network interface "TestNIC"
		+ Deleting network interface "TestNIC"
		info:    network nic delete command OK

## Adición de una dirección IP privada estática a una VM existente
Para agregar una dirección IP privada estática a la NIC que usó la VM y creada con el script anterior, ejecuta el siguiente comando:

	azure network nic set -g TestRG -n TestNIC2 -a 192.168.1.101

Resultado esperado:

	info:    Executing command network nic set
	+ Looking up the network interface "TestNIC2"
	+ Updating network interface "TestNIC2"
	+ Looking up the network interface "TestNIC2"
	data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
	data:    Name                            : TestNIC2
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : centralus
	data:    Provisioning state              : Succeeded
	data:    MAC address                     : 00-0D-3A-90-29-25
	data:    Enable IP forwarding            : false
	data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
	data:    IP configurations:
	data:      Name                          : NIC-config
	data:      Provisioning state            : Succeeded
	data:      Private IP address            : 192.168.1.101
	data:      Private IP Allocation Method  : Static
	data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:
	info:    network nic set command OK

## Pasos siguientes

- Obtenga más información acerca de las [direcciones IP públicas reservadas](../virtual-networks-reserved-public-ip).
- Obtenga información sobre las [direcciones IP públicas a nivel de instancia (ILPIP)](../virtual-networks-instance-level-public-ip).
- Consulta las [API de REST de IP reservadas](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=AcomDC_0128_2016-->