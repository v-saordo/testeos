<properties 
   pageTitle="Configuración de una IP privada estática en modo clásico con la CLI| Microsoft Azure"
   description="Descripción de las IP privadas estáticas (DIP) y su administración en el modo clásico con la CLI"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
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

# Configuración de una dirección IP privada estática (clásica) en la CLI de Azure

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación clásico. También puede [administrar la dirección IP privada estática en el modelo de implementación del Administrador de recursos](virtual-networks-static-private-ip-arm-cli.md).

En los siguientes comandos de la CLI de Azure de ejemplo se presupone que ya se ha creado un entorno simple. Si desea ejecutar los comandos que aparecen en este documento, cree primero el entorno de prueba descrito en [creación de una red virtual](virtual-networks-create-vnet-classic-cli.md).

## Especificación de una dirección IP privada estática al crear una VM
Para crear una nueva VM denominada *DNS01* en un nuevo servicio en la nube denominado *TestService* según el escenario anterior, siga estos pasos:

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](xplat-cli-install.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.
1. Ejecute el comando **azure service create** para crear el servicio en la nube.

		azure service create TestService --location uscentral

	Resultado esperado:

		info:    Executing command service create
		info:    Creating cloud service
		data:    Cloud service name TestService
		info:    service create command OK
	
2. Ejecute el comando **azure crear vm** para crear la VM. Tenga en cuenta el valor de una dirección IP privada estática. En la lista que se muestra en la salida se explican los parámetros utilizados.

		azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd

	Resultado esperado:

		info:    Executing command vm create
		warn:    --vm-size has not been specified. Defaulting to "Small".
		info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
		info:    Looking up virtual network
		info:    Looking up cloud service
		warn:    --location option will be ignored
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Retrieving storage accounts
		info:    Creating VM
		info:    OK
		info:    vm create command OK

	- **-l (o --location)**. La región de Azure donde se creará la VM. En este escenario, *centralus*.
	- **-n (o --vm-name)**. Nombre de la VM que se va a crear.
	- **-w (o --virtual-network-name)**. Nombre de la red virtual donde se creará la VM. 
	- **-S (o --static-ip)**. Dirección IP privada estática para la VM.
	- **TestService**. Nombre del servicio en la nube en el que se creó la VM.
	- **bd507d3a70934695bc2128e3e5a255ba\_\_RightImage-Windows-2012R2-x64-v14.2**. Imagen utilizada para crear la VM.
	- **adminuser**. Administrador local para la VM de Windows.
	- ****AdminP@ssw0rd**. Contraseña del administrador local para la VM de Windows.

## Recuperación de la información de la dirección IP privada estática para una VM
Para ver la información de la dirección IP privada estática para la VM creada con el script anterior, ejecute el comando de la CLI de Azure y observe el valor para *Network StaticIP*:

	azure vm static-ip show DNS01

Resultado esperado:

	info:    Executing command vm static-ip show
	info:    Getting virtual machines
	data:    Network StaticIP "192.168.1.101"
	info:    vm static-ip show command OK

## Eliminación de una dirección IP privada estática de una VM
Para quitar la dirección IP privada estática agregada a la VM en el script anterior, ejecute los siguientes comandos de la CLI de Azure.
	
	azure vm static-ip remove DNS01

Resultado esperado:

	info:    Executing command vm static-ip remove
	info:    Getting virtual machines
	info:    Reading network configuration
	info:    Updating network configuration
	info:    vm static-ip remove command OK

## Adición de una IP privada estática a una VM existente
Para agregar una dirección IP privada estática a la VM creada con el script anterior, ejecute el siguiente comando:

	azure vm static-ip set DNS01 192.168.1.101

Resultado esperado:

	info:    Executing command vm static-ip set
	info:    Getting virtual machines
	info:    Looking up virtual network
	info:    Reading network configuration
	info:    Updating network configuration
	info:    vm static-ip set command OK

## Pasos siguientes

- Obtenga más información acerca de las [direcciones IP públicas reservadas](../virtual-networks-reserved-public-ip).
- Obtenga más información acerca de las [direcciones IP públicas a nivel de instancia (ILPIP)](../virtual-networks-instance-level-public-ip).
- Consulte las [API de REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=AcomDC_1217_2015-->