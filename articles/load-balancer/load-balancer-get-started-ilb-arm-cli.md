<properties 
   pageTitle="Creación de un equilibrador de carga interno mediante la CLI de Azure en el Administrador de recursos | Microsoft Azure"
   description="Obtenga información sobre cómo crear un equilibrador de carga interno mediante la CLI de Azure en el Administrador de recursos"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="joaoma" />

# Primeros pasos en la creación de un equilibrador de carga interno mediante la CLI de Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [modelo de implementación clásica](load-balancer-get-started-ilb-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## ¿Qué es necesario para crear un equilibrador de carga interno?

Para implementar un equilibrador de carga, debe crear y configurar los objetos siguientes:

- Configuración de direcciones IP front-end: configura la dirección IP privada para el tráfico de red entrante. 

- Grupo de direcciones de back-end: contiene interfaces de red (NIC) para recibir tráfico proveniente del equilibrador de carga.

- Reglas de equilibrio de carga: contiene reglas de asignación de un puerto de tráfico de red entrante a un puerto de recepción de tráfico de red en el grupo de back-end.

- Reglas NAT de entrada: contiene reglas que asignan un puerto público en el equilibrador de carga a un puerto para una máquina virtual específica en el grupo de direcciones de back-end.

- Sondeos: contiene los sondeos de estado que se usan para comprobar la disponibilidad de las máquinas virtuales con el grupo de direcciones de back-end.

Puede obtener más información acerca de los componentes del equilibrador de carga con el Administrador de recursos de Azure en [Compatibilidad del Administrador de recursos de Azure con el Equilibrador de carga](load-balancer-arm.md).

## Configurar la CLI para utilizar el Administrador de recursos

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](xplat-cli.md) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.

2. Ejecuta el comando **azure config mode** para cambiar al modo de Administrador de recursos, como se muestra a continuación.

		azure config mode arm

	Resultado esperado:

		info:    New mode is arm

## Creación paso a paso de un equilibrador de carga interno 

Los pasos siguientes crearán un equilibrador de carga interno basado en el escenario anterior:

### Paso 1 

Si no lo ha hecho todavía, descargue la versión más reciente de la [interfaz de línea de comandos de Azure](https://azure.microsoft.com/downloads/).

### Paso 2 

Tras su instalación, autentique la cuenta.

	azure login

El proceso de autenticación solicitará el nombre de usuario y la contraseña de su suscripción de Azure.

### Paso 3

Cambiar las herramientas de comandos al modo de Administrador de recursos de Azure.

	azure config mode arm

## Crear un grupo de recursos

Todos los recursos del Administrador de recursos de Azure están asociados a un grupo de recursos. Si todavía no lo ha hecho, cree un grupo de recursos.

	azure group create <resource group name> <location>


## Crear un conjunto de equilibrador de carga interno 


### Paso 1 

Cree un equilibrador de carga interno mediante el comando `azure network lb create`. En el siguiente escenario, se crea un grupo de recursos denominado nrprg en la región Este de EE. UU.
 	
	azure network lb create -n nrprg -l westus

>[AZURE.NOTE] Todos los recursos de un equilibrador de carga interno, como una red virtual y una subred de red virtual, deben estar en el mismo grupo de recursos y en la misma región.


### Paso 2 

Crear una dirección IP de front-end para el equilibrador de carga interno. La dirección IP usada debe estar dentro del intervalo de subred de la red virtual.

	
	azure network lb frontend-ip create -g nrprg -l ilbset -n feilb -a 10.0.0.7 -e nrpvnetsubnet -m nrpvnet

Parámetros usados:

**-g**: grupo de recursos 
**-l**: nombre del conjunto de equilibrador de carga interno 
**-n**: nombre de la dirección IP de front-end 
**-a**: dirección IP privada dentro del intervalo de subred. 
**-e**: nombre de la subred 
**-m**: nombre de red virtual

### Paso 3 

Crear el grupo de direcciones de back-end.

	azure network lb address-pool create -g nrprg -l ilbset -n beilb

Parámetros usados:

**-g**: grupo de recursos 
**-l**: nombre del conjunto del equilibrador de carga interno 
**-n**: nombre del grupo de direcciones de back-end

Después de definir una dirección IP de front-end y un grupo de direcciones de back-end, puede crear reglas de equilibradores de carga, reglas NAT de entrada y personalizar sondeos de estado.

### Paso 4


Crear una regla de equilibrador de carga para el equilibrador de carga interno. Siguiendo el escenario anterior, el comando crea una regla de equilibrador de carga que escucha al puerto 1433 en el grupo de servidores front-end y envía tráfico de red con equilibrio de carga al grupo de direcciones de back-end usando también el puerto 1433.

	azure network lb rule create -g nrprg -l ilbset -n ilbrule -p tcp -f 1433 -b 1433 -t feilb -o beilb

Parámetros usados:

**-g**: grupo de recursos 
**-l**: nombre del conjunto del equilibrador de carga interno 
**-n**: nombre de la regla del equilibrador de carga 
**-p**: protocolo usado para la regla 
**-f**: puerto que escucha el tráfico de red entrante en el front-end del equilibrador de carga 
**-b**: puerto que recibe el tráfico de red en el grupo de direcciones de back-end

### Paso 5

Crear reglas NAT de entrada. Las reglas NAT de entrada se usan para crear puntos de conexión en un equilibrador de carga que se dirijan a una instancia de máquina virtual específica. Siguiendo el ejemplo anterior, se crearon 2 reglas NAT para el acceso de escritorio remoto.

	azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule1 -p TCP -f 5432 -b 3389
	
	azure network lb inbound-nat-rule create -g nrprg -l ilbset -n NATrule2 -p TCP -f 5433 -b 3389

Parámetros usados:

**-g**: grupo de recursos 
**-l**: nombre del conjunto del equilibrador de carga interno 
**-n**: nombre de la regla NAT entrante 
**-p**: protocolo usado para la regla 
**-f**: puerto que escucha el tráfico de red entrante en el front-end del equilibrador de carga 
**-b**: puerto que recibe el tráfico de red en el grupo de direcciones de back-end

### Paso 5 

Crear sondeos de estado del equilibrador de carga. Los sondeos de estado comprueban todas las instancias de máquina virtual para asegurarse de que puede enviar tráfico de red. La instancia de máquina virtual con comprobaciones de sondeo incorrectas se elimina del equilibrador de carga hasta que vuelve a estar en línea y el sondeo resulta correcto.

	azure network lb probe create -g nrprg -l ilbset -n ilbprobe -p tcp -i 300 -c 4

**-g** -grupo de recursos 
**-l**: nombre del conjunto de equilibrador de carga interno 
**-n**: nombre del sondeo de estado 
**-p** -protocolo usado por sondeo de estado 
**i-**: intervalo de sondeo en segundos 
**-c**: número de comprobaciones

>[AZURE.NOTE] La Plataforma Microsoft Azure utiliza una dirección IPv4 estática enrutable públicamente para una variedad de escenarios de administración. La dirección IP es 168.63.129.16. Ningún firewall debe bloquear esta dirección IP, ya que puede causar un comportamiento inesperado. Con respecto al Equilibrio de carga interno de Azure, esta dirección IP la usan las sondas de supervisión del equilibrador de carga para determinar el estado de mantenimiento de las máquinas virtuales en un conjunto con equilibrio de carga. Si se usa un grupo de seguridad de red para restringir el tráfico a máquinas virtuales de Azure en un conjunto de carga equilibrada internamente o se aplica a una subred de Red virtual, asegúrate de agregar una regla de seguridad de red para permitir el tráfico desde 168.63.129.16.

## Cree tarjetas NIC

Debe crear tarjetas NIC (o modificar las existentes) y asociarlas a las reglas NAT, las reglas de equilibrador de carga y los sondeos.

### Paso 1 

Cree una NIC llamada *lb-nic1-be* y asóciela con la regla NAT *rdp1* y el grupo de direcciones de back-end *beilb*.
	
	azure network nic create -g nrprg -n lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

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

Cree una NIC llamada *lb-nic2-be* y asóciela con la regla NAT *rdp2* y el grupo de direcciones de back-end *beilb*.

 	azure network nic create -g nrprg -n lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet -d "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" -e "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

### Paso 3 

Cree una máquina virtual llamada *DB1* y asóciela con la NIC llamada *lb-nic1-be*. Se creó una cuenta de almacenamiento llamada *web1nrp* antes de ejecutar el comando que aparece a continuación.

	azure vm create --resource-group nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

>[AZURE.IMPORTANT] Las máquinas virtuales en un equilibrador de carga deben estar en el mismo conjunto de disponibilidad. Use `azure availset create` para crear un conjunto de disponibilidad.

### Paso 4

Cree una máquina virtual llamada *DB2* y asóciela con la NIC llamada *lb-nic2-be*. Se creó una cuenta de almacenamiento llamada *web1nrp* antes de ejecutar el comando que aparece a continuación.

	azure vm create --resource-group nrprg --name DB2 --location eastus --vnet-	name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## Eliminar un equilibrador de carga 


Para quitar un equilibrador de carga, use el siguiente comando

	azure network lb delete -g nrprg -n ilbset 

Donde **nrprg** es el grupo de recursos y **ilbset** el nombre del equilibrador de carga interno.


## Pasos siguientes

[Configurar un modo de distribución del equilibrador de carga mediante la afinidad IP de origen](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0218_2016-->
