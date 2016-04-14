## Cómo crear una red virtual clásica mediante la CLI de Azure

Puede utilizar la CLI de Azure para administrar los recursos de Azure desde el símbolo del sistema de cualquier equipo que ejecute Windows, Linux o bien OSX. Para crear una red virtual mediante la CLI de Azure, siga estos pasos.

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](xplat-cli-install.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.
2. Ejecute el comando **azure network vnet create** para crear una red virtual y una subred, como se muestra a continuación. En la lista que se muestra en la salida se explican los parámetros utilizados.

			azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
	
	Resultado esperado:

			info:    Executing command network vnet create
			+ Looking up network configuration
			+ Looking up locations
			+ Setting network configuration
			info:    network vnet create command OK

	- **--vnet**. Nombre de la red virtual que se va a crear. En este escenario, *TestVNet*.
	- **-e (o --address-space)**. Espacio de direcciones de red virtual. En este escenario, *192.168.0.0*.
	- **-i (o -cidr)**. Máscara de red en formato CIDR. En este escenario, *16*.
	- **-n (o --subnet-name**). Nombre de la primera subred. En este escenario, *FrontEnd*.
	- **-p (o --subnet-start-ip)**. Dirección IP inicial de la subred o el espacio de direcciones de la subred. En este escenario, *192.168.1.0*.
	- **-r (o --subnet-cidr)**. Máscara de red en formato CIDR para subred. En este escenario, *24*.
	- **-l (o --location)**. Región de Azure donde se creará la red virtual. En este escenario, *Central US*.

3. Ejecute el comando **azure network vnet subnet create** para crear una subred, como se muestra a continuación. En la lista que se muestra en la salida se explican los parámetros utilizados.

			azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
	
	Este es el resultado esperado del comando anterior:

			info:    Executing command network vnet subnet create
			+ Looking up network configuration
			+ Creating subnet "BackEnd"
			+ Setting network configuration
			+ Looking up the subnet "BackEnd"
			+ Looking up network configuration
			data:    Name                            : BackEnd
			data:    Address prefix                  : 192.168.2.0/24
			info:    network vnet subnet create command OK

	- **-t (o --vnet-name**. Nombre de la red virtual donde se creará la subred. En este escenario, *TestVNet*.
	- **-n (o --name)**. Nombre de la nueva subred. En este escenario, *BackEnd*.
	- **-a (o --address-prefix)**. Bloque CIDR de subred. En este escenario, *192.168.2.0/24*.

4. Ejecute el comando **azure network vnet show** para ver las propiedades de la nueva red virtual, tal como se muestra a continuación.

			azure network vnet show

	Este es el resultado esperado del comando anterior:

			info:    Executing command network vnet show
			Virtual network name: TestVNet
			+ Looking up the virtual network sites
			data:    Name                            : TestVNet
			data:    Location                        : Central US
			data:    State                           : Created
			data:    Address space                   : 192.168.0.0/16
			data:    Subnets:
			data:      Name                          : FrontEnd
			data:      Address prefix                : 192.168.1.0/24
			data:
			data:      Name                          : BackEnd
			data:      Address prefix                : 192.168.2.0/24
			data:
			info:    network vnet show command OK

<!---HONumber=Oct15_HO3-->