<properties 
   pageTitle="Creación de grupos de seguridad de red en el modo clásico mediante la CLI de Azure | Microsoft Azure"
   description="Aprenda a crear e implementar grupos de seguridad de red en modo clásico mediante la CLI de Azure"
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
   ms.date="02/02/2016"
   ms.author="telmos" />

# Creación de grupos de seguridad de red (modo clásico) en la CLI de Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación clásico. También puede [crear grupos de seguridad de red con el modelo de implementación del Administrador de recursos](virtual-networks-create-nsg-arm-cli.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

En los siguientes comandos de CLI de Azure de ejemplo se presupone que ya se ha creado un entorno simple según el escenario anterior. Si desea ejecutar los comandos tal y como aparecen en este documento, compile primero el entorno de prueba mediante la [creación de una red virtual](virtual-networks-create-vnet-classic-cli.md).

## Creación del grupo de seguridad de red para la subred front-end
Para crear un grupo de seguridad de red denominado **NSG-FrontEnd** según el escenario anterior, siga estos pasos.

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](xplat-cli-install.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.

2. Ejecute el comando **`azure config mode`** para cambiar al modo clásico, como se muestra a continuación.

		azure config mode asm

	Resultado esperado:

		info:    New mode is asm

3. Ejecute el comando **`azure network nsg create`** para crear un grupo de seguridad de red.

		azure network nsg create -l uswest -n NSG-FrontEnd

	Resultado esperado:

		info:    Executing command network nsg create
		info:    Creating a network security group "NSG-FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : NSG-FrontEnd
		data:    Location                        : West US
		data:    Security group rules:
		data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
		ity  Default
		data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
		---  -------
		data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
		     true   
		data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
		     true   
		data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
		     true   
		data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
		     true   
		data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
		     true   
		data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
		     true   
		info:    network nsg create command OK

	Parámetros:

	- **-l (o --location)**. Región de Azure donde se creará la red virtual. En este escenario, *TestRG*.
	- **-n (o --name)**. Nombre del nuevo grupo de seguridad de red. En este escenario, *NSG-FrontEnd*.

4. Ejecute el comando **`azure network nsg rule create`** para crear una regla que permita el acceso al puerto 3389 (RDP) desde Internet.

		azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

	Resultado esperado:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security rule "rdp-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : rdp-rule
		data:    Source address prefix           : INTERNET
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 3389
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

	Parámetros:

	- **- a (o --nsg-name)**. Nombre del grupo de seguridad de red en el que se creará la regla. En este escenario, *NSG-FrontEnd*.
	- **-n (o --name)**. Nombre de la nueva regla. En este escenario, *rdp-rule*.
	- **-c (o --action)**. Nivel de acceso de la regla (Denegar o Permitir).
	- **-p (o --protocol)**. Protocolo (Tcp, Udp o *) para la regla.
	- **-r (o --type)**. Dirección de conexión (Entrante o Saliente).
	- **-y (o --priority)**. Prioridad de la regla.
	- **-f (o --source-address-prefix)**. Prefijo de dirección de origen en CIDR o con las etiquetas predeterminadas.
	- **-o (o --source-port-range)**. Puerto de origen, o intervalo de puertos.
	- **-e (o --destination-address-prefix)**. Prefijo de dirección de destino en CIDR o con las etiquetas predeterminadas.
	- **-u (o --destination-port-range)**. Puerto de destino, o intervalo de puertos.	

5. Ejecute el comando **`azure network nsg rule create`** para crear una regla que permita el acceso al puerto 80 (HTTP) desde Internet.

		azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

	Resultado esperado:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : web-rule
		data:    Source address prefix           : INTERNET
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 80
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Ejecute el comando **`azure network nsg subnet add`** para vincular el grupo de seguridad de red a la subred front-end.

		azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd 

	Resultado esperado:

		info:    Executing command network nsg subnet add
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up network configuration
		info:    Creating a network security group "NSG-FrontEnd"
		info:    network nsg subnet add command OK

## Creación del grupo de seguridad de red para la subred back-end
Para crear un grupo de seguridad de red denominado *NSG-BackEnd* según el escenario anterior, siga estos pasos.

3. Ejecute el comando **`azure network nsg create`** para crear un grupo de seguridad de red.

		azure network nsg create -l uswest -n NSG-BackEnd

	Resultado esperado:

		info:    Executing command network nsg create
		info:    Creating a network security group "NSG-BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : NSG-BackEnd
		data:    Location                        : West US
		data:    Security group rules:
		data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
		ity  Default
		data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
		---  -------
		data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
		     true   
		data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
		     true   
		data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
		     true   
		data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
		     true   
		data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
		     true   
		data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
		     true   
		info:    network nsg create command OK

	Parámetros:

	- **-l (o --location)**. Región de Azure donde se creará la red virtual. En este escenario, *TestRG*.
	- **-n (o --name)**. Nombre del nuevo grupo de seguridad de red. En este escenario, *NSG-FrontEnd*.

4. Ejecute el comando **`azure network nsg rule create`** para crear una regla que permita el acceso al puerto 1433 (SQL) desde la subred front-end.

		azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

	Resultado esperado:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security rule "sql-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : sql-rule
		data:    Source address prefix           : 192.168.1.0/24
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 1433
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK


5. Ejecute el comando **`azure network nsg rule create`** para crear una regla que deniegue el acceso a Internet.

		azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80

	Resultado esperado:

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : web-rule
		data:    Source address prefix           : *
		data:    Source Port                     : *
		data:    Destination address prefix      : INTERNET
		data:    Destination Port                : 80
		data:    Protocol                        : TCP
		data:    Type                            : Outbound
		data:    Action                          : Deny
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Ejecute el comando **`azure network nsg subnet add`** para vincular el grupo de seguridad de red a la subred de back-end.

		azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd 

	Resultado esperado:

		info:    Executing command network nsg subnet add
		info:    Looking up the network security group "NSG-BackEndX"
		info:    Looking up the subnet "BackEnd"
		info:    Looking up network configuration
		info:    Creating a network security group "NSG-BackEndX"
		info:    network nsg subnet add command OK

<!----HONumber=AcomDC_0211_2016-->