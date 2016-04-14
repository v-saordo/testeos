<properties 
   pageTitle="Creación de un equilibrador de carga orientado a Internet en el Administrador de recursos con la CLI de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo crear un equilibrador de carga orientado a Internet en el Administrador de recursos con la CLI de Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/24/2016"
   ms.author="joaoma" />

# Introducción a la creación de un equilibrador de carga orientado a Internet con la CLI de Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puede [obtener información sobre cómo crear un equilibrador de carga orientado a Internet mediante la implementación clásica](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Aquí se tratará la secuencia de tareas individuales que debe realizarse para crear un equilibrador de carga y se explicará con detalle qué se hace para lograr el objetivo.


## ¿Qué se necesita para crear un equilibrador de carga orientado a Internet?

Para implementar un equilibrador de carga, debe crear y configurar los objetos siguientes.

- Configuración de direcciones IP front-end: contiene direcciones IP públicas para el tráfico de red entrante. 

- Grupo de direcciones de back-end: contiene interfaces de red (NIC) para que las máquinas virtuales reciban tráfico de red del equilibrador de carga.

- Reglas de equilibrio de carga: contiene reglas que asignan un puerto público en el equilibrador de carga al del grupo de direcciones de back-end.

- Reglas NAT de entrada: contiene reglas que asignan un puerto público en el equilibrador de carga a un puerto para una máquina virtual específica en el grupo de direcciones de back-end.

- Sondeos: contiene los sondeos de estado que se usan para comprobar la disponibilidad de las instancias de las máquinas virtuales del grupo de direcciones de back-end.

Para más información sobre los componentes del equilibrador de carga con el Administrador de recursos de Azure en [Compatibilidad del Administrador de recursos de Azure con el Equilibrador de carga](load-balancer-arm.md).

## Configurar la CLI para utilizar el Administrador de recursos

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](../../articles/xplat-cli-install.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.

2. Ejecuta el comando **azure config mode** para cambiar al modo de Administrador de recursos, como se muestra a continuación.

		azure config mode arm

	Resultado esperado:

		info:    New mode is arm

## Crear una red virtual y una dirección IP pública para el grupo de direcciones IP front-end

### Paso 1

Cree una red virtual llamada *NRPVnet* en la ubicación Este de EE. UU. mediante el uso de un grupo de recursos llamado *NRPRG*.

	azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

Cree una subred llamada *NRPVnetSubnet* con un bloque CIDR de 10.0.0.0/24 en *NRPVnet*.

	azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

### Paso 2

Cree una dirección IP pública llamada *NRPPublicIP* para que la use un grupo de direcciones IP front-end con nombre de DNS *loadbalancernrp.eastus.cloudapp.azure.com*. El comando siguiente usa el tipo de asignación estática y el tiempo de espera de inactividad de 4 minutos.

	azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4


>[AZURE.IMPORTANT] El equilibrador de carga usará la etiqueta de dominio de la dirección IP pública como su FQDN. Esto representa un cambio en la implementación clásica, la que usa el servicio en la nube como el FQDN del equilibrador de carga. En este ejemplo, el FQDN será *loadbalancernrp.eastus.cloudapp.azure.com*.

## Crear un equilibrador de carga

En el ejemplo siguiente, el comando que aparece a continuación crea un equilibrador de carga llamado *NRPlb* en el grupo de recursos *NRPRG* de la ubicación *Este de EE. UU.* de Azure.

	azure network lb create NRPRG NRPlb eastus

## Crear un grupo de direcciones IP front-end y un grupo de direcciones de back-end

En el ejemplo siguiente, se crea el grupo de direcciones IP front-end que recibirá el tráfico de red que entra al equilibrador de carga y el grupo de direcciones IP de back-end donde el grupo front-end enviará el tráfico de red con equilibrio de carga.

### Paso 1 

Cree un grupo de direcciones IP front-end mediante la asociación de la dirección IP pública creada en el paso anterior y el equilibrador de carga.

	azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

### Paso 2 

Configure un grupo de direcciones de back-end usado para recibir el tráfico entrante proveniente del grupo de direcciones IP front-end.

	azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## Crear reglas de equilibrador de carga, reglas NAT y sondeo

En el ejemplo que aparece a continuación, se crean los elementos siguientes.

- Una regla NAT para trasladar todo el tráfico entrante del puerto 21 al puerto 22<sup>1</sup>
- Una regla NAT para trasladar todo el tráfico entrante del puerto 23 al puerto 22
- Una regla de equilibrador de carga para equilibrar todo el tráfico entrante del puerto 80 al puerto 80 en las direcciones del grupo de back-end.
- Una regla de sondeo que comprobará el estado de mantenimiento en una página llamada *HealthProbe.aspx*.

<sup>1</sup> Las reglas NAT están asociadas a una instancia de máquina virtual específica detrás del equilibrador de carga. En el ejemplo siguiente, el tráfico de red entrante al puerto 21 se enviará a una máquina virtual específica en el puerto 22 asociada a una regla NAT. Debe elegir un protocolo para la regla NAT, UDP o TCP. Los dos protocolos no se pueden asignar al mismo puerto.

### Paso 1

Cree las reglas NAT.

	azure network lb inbound-nat-rule create -g nrprg -l nrplb -n ssh1 -p tcp -f 21 -b 22
	azure network lb inbound-nat-rule create -g nrprg -l nrplb -n ssh2 -p tcp -f 23 -b 22

Parámetros:

- **-g**: nombre del grupo de recursos
- **-l**: nombre del equilibrador de carga 
- **-n**: nombre del recurso, ya sea una regla NAT, una regla de equilibrador de carga o un sondeo
- **-p**: protocolo (puede ser TCP o UDP)  
- **-f**: el puerto front-end que se usará (el comando probe usa -f para definir la ruta de acceso de sondeo)
- **-b**: el puerto de back-end que se usará

### Paso 2

Cree una regla de equilibrador de carga.

	azure network lb rule create nrprg nrplb lbrule -p tcp -f 80 -b 80 -t NRPfrontendpool -o NRPbackendpool
### Paso 3

Cree un sondeo de estado.

	azure network lb probe create -g nrprg -l nrplb -n healthprobe -p "http" -o 80 -f healthprobe.aspx -i 15 -c 4

	
	

**-g** -grupo de recursos **-l**: nombre del conjunto de equilibrador de carga **- n**: nombre del sondeo de estado **-p** -protocolo usado por sondeo de estado **i -**: intervalo de sondeo en segundos **- c**: número de comprobaciones

### Paso 4

Compruebe la configuración.

	azure network lb show nrprg nrplb

Resultado esperado:

	info:    Executing command network lb show
	+ Looking up the load balancer "nrplb"
	+ Looking up the public ip "NRPPublicIP"	
	data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
	data:    Name                            : nrplb
	data:    Type                            : Microsoft.Network/loadBalancers
	data:    Location                        : eastus
	data:    Provisioning State              : Succeeded
	data:    Frontend IP configurations:
	data:      Name                          : NRPfrontendpool
	data:      Provisioning state            : Succeeded
	data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
	data:      Public IP allocation method   : Static
	data:      Public IP address             : 40.114.13.145
	data:
	data:    Backend address pools:
	data:      Name                          : NRPbackendpool
	data:      Provisioning state            : Succeeded
	data:
	data:    Load balancing rules:
	data:      Name                          : HTTP
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Tcp
	data:      Frontend port                 : 80
	data:      Backend port                  : 80
	data:      Enable floating IP            : false
	data:      Idle timeout in minutes       : 4
	data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
	data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
	data:
	data:    Inbound NAT rules:
	data:      Name                          : ssh1
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Tcp
	data:      Frontend port                 : 21
	data:      Backend port                  : 22
	data:      Enable floating IP            : false
	data:      Idle timeout in minutes       : 4
	data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
	data:
	data:      Name                          : ssh2
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Tcp
	data:      Frontend port                 : 23
	data:      Backend port                  : 22
	data:      Enable floating IP            : false
	data:      Idle timeout in minutes       : 4
	data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
	data:
	data:    Probes:
	data:      Name                          : healthprobe
	data:      Provisioning state            : Succeeded
	data:      Protocol                      : Http
	data:      Port                          : 80
	data:      Interval in seconds           : 15
	data:      Number of probes              : 4
	data:
	info:    network lb show command OK

## Cree tarjetas NIC

Debe crear tarjetas NIC (o modificar las existentes) y asociarlas a las reglas NAT, las reglas de equilibrador de carga y los sondeos.

### Paso 1 

Cree una NIC llamada *lb-nic1-be* y asóciela con la regla NAT *rdp1* y el grupo de direcciones de back-end *NRPbackendpool*.
	
	azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

Parámetros:

- **-g**: nombre del grupo de recursos
- **-n**: nombre del recurso de NIC
- **--subnet-name**: nombre de la subred 
- **--subnet-vnet-name**: nombre de la red virtual
- **-d**: el Id. del recurso del grupo de back-end. Comienza con /subscription/{subscriptionID/resourcegroups/<resourcegroup-name>/providers/Microsoft.Network/loadbalancers/<load-balancer-name>/backendaddresspools/<name-of-the-backend-pool> 
- **-e**: el Id. de la regla NAT que se asociará con el recurso de NIC. Comienza con /subscriptions/####################################/resourceGroups/<resourcegroup-name>/providers/Microsoft.Network/loadBalancers/<load-balancer-name>/inboundNatRules/<nat-rule-name>


Resultado esperado:

	info:    Executing command network nic create
	+ Looking up the network interface "lb-nic1-be"
	+ Looking up the subnet "nrpvnetsubnet"
	+ Creating network interface "lb-nic1-be"
	+ Looking up the network interface "lb-nic1-be"
	data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	data:    Name                            : lb-nic1-be
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : eastus
	data:    Provisioning state              : Succeeded
	data:    Enable IP forwarding            : false
	data:    IP configurations:
	data:      Name                          : NIC-config
	data:      Provisioning state            : Succeeded
	data:      Private IP address            : 10.0.0.4
	data:      Private IP Allocation Method  : Dynamic
	data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
	data:      Load balancer backend address pools
	data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
	data:      Load balancer inbound NAT rules:
	data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
	data:
	info:    network nic create command OK

### Paso 2

Cree una NIC llamada *lb-nic2-be* y asóciela con la regla NAT *rdp2* y el grupo de direcciones de back-end *NRPbackendpool*.

 	azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

### Paso 3 

Cree una máquina virtual llamada *web1* y asóciela con la NIC llamada *lb-nic1-be*. Se creó una cuenta de almacenamiento llamada *web1nrp* antes de ejecutar el comando que aparece a continuación.

	azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

>[AZURE.IMPORTANT] Las máquinas virtuales en un equilibrador de carga deben estar en el mismo conjunto de disponibilidad. Use `azure availset create` para crear un conjunto de disponibilidad.

El resultado será el siguiente:

	info:    Executing command vm create
	+ Looking up the VM "web1"
	Enter username: azureuser
	Enter password for azureuser: *********
	Confirm password: *********
	info:    Using the VM Size "Standard_A1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Looking up the storage account web1nrp
	+ Looking up the availability set "nrp-avset"
	info:    Found an Availability set "nrp-avset"
	+ Looking up the NIC "lb-nic1-be"
	info:    Found an existing NIC "lb-nic1-be"
	info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
	info:    This is a NIC without publicIP configured
	+ Creating VM "web1"
	info:    vm create command OK

>[AZURE.NOTE] El mensaje informativo **Esta es una NIC sin dirección IP pública configurada** es un comportamiento esperado, debido a que la NIC que se creó para el equilibrador de carga se conecta a Internet con la dirección IP pública del equilibrador de carga.

Como la NIC *lb-nic1-be* está asociada con la regla NAT *rdp1*, es posible conectarse a *web1* con RDP a través del puerto 3441 en el equilibrador de carga.

### Paso 4

Cree una máquina virtual llamada *web2* y asóciela con la NIC llamada *lb-nic2-be*. Se creó una cuenta de almacenamiento llamada *web1nrp* antes de ejecutar el comando que aparece a continuación.

	azure vm create --resource-group nrprg --name web2 --location eastus --vnet-	name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## Actualización de un equilibrador de carga existente

Puede agregar reglas que hacen referencia a un equilibrador de carga existente. En el ejemplo siguiente, se agrega una nueva regla de equilibrador de carga a un equilibrador de carga existente **NRPlb**.

	azure network lb rule create -g nrprg -l nrplb -n lbrule2 -p tcp -f 8080 -b 8051 -t frontendnrppool -o NRPbackendpool

Parámetros:

**-g**: nombre del grupo de recursos<br> **-l**: nombre del equilibrador de carga<BR> **- n**: nombre de la regla del equilibrador de carga<BR> **-p**: protocolo<BR> **-f**: puerto de front-end<BR> **-b**: puerto de back-end<BR> **-t**: nombre de grupo de front-end<BR> **-b**: nombre de grupo de back-end<BR>

## Eliminar un equilibrador de carga 


Para quitar un equilibrador de carga, use el siguiente comando

	azure network lb delete -g nrprg -n nrplb 

donde **nrprg** es el grupo de recursos y **nrplb** el nombre del equilibrador de carga.

## Pasos siguientes

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-get-started-ilb-arm-cli.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0302_2016-->