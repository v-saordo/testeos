<properties 
   pageTitle="Creación de un equilibrador de carga orientado a Internet en el Administrador de recursos con PowerShell | Microsoft Azure"
   description="Obtenga información sobre cómo crear un equilibrador de carga orientado a Internet en el Administrador de recursos con PowerShell"
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
   ms.date="01/21/2016"
   ms.author="joaoma" />

# Introducción a la creación de un equilibrador de carga orientado a Internet en el Administrador de recursos con PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artículo trata sobre el modelo de implementación del Administrador de recursos. También puede [obtener información sobre cómo crear un equilibrador de carga orientado a Internet mediante el modelo de implementación clásica](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

Los pasos siguientes muestran cómo crear un equilibrador de carga orientado a Internet con el Administrador de recursos de Azure con PowerShell. Con el Administrador de recursos de Azure, los elementos para crear un equilibrador de carga orientado a Internet se configuran individualmente y después se colocan juntos para crear un recurso.

Aquí se tratará la secuencia de tareas individuales que debe realizarse para crear un equilibrador de carga y se explicará con detalle qué se hace para lograr el objetivo.

## ¿Qué se necesita para crear un equilibrador de carga orientado a Internet?

Para implementar un equilibrador de carga, debe crear y configurar los objetos siguientes:

- Configuración de direcciones IP front-end: contiene direcciones IP públicas para el tráfico de red entrante. 

- Grupo de direcciones de back-end: contiene interfaces de red (NIC) para que las máquinas virtuales reciban tráfico de red del equilibrador de carga.

- Reglas de equilibrio de carga: contiene reglas que asignan un puerto público en el equilibrador de carga al del grupo de direcciones de back-end.

- Reglas NAT de entrada: contiene reglas que asignan un puerto público en el equilibrador de carga a un puerto para una máquina virtual específica en el grupo de direcciones de back-end.

- Sondeos: contiene los sondeos de estado que se usan para comprobar la disponibilidad de las instancias de las máquinas virtuales del grupo de direcciones de back-end.

Para más información sobre los componentes del equilibrador de carga con el Administrador de recursos de Azure en [Compatibilidad del Administrador de recursos de Azure con el Equilibrador de carga](load-balancer-arm.md).


## Configurar PowerShell para que use el Administrador de recursos

Asegúrese de que tiene la última versión de producción del módulo Administrador de recursos de Azure de PowerShell.

### Paso 1

		PS C:\> Login-AzureRmAccount

Se le pedirá que se autentique con sus credenciales.<BR>

### Paso 2

Compruebe las suscripciones para la cuenta.

		PS C:\> Get-AzureRmSubscription 

### Paso 3 

Elija qué suscripción de Azure va a utilizar.<BR>

		PS C:\> Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

### Paso 4

Creación de un grupo de recursos (omitir este paso si se usa un grupo de recursos existente)


    	PS C:\> New-AzureRmResourceGroup -Name NRP-RG -location "West US"


## Creación de una red virtual y una dirección IP pública para el grupo de direcciones IP front-end

### Paso 1

Cree una subred y una red virtual.

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
    New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### Paso 2

Cree un recurso de dirección IP pública (PIP) de Azure, llamada *PublicIP* para que la use un grupo de direcciones IP front-end con el nombre DNS *loadbalancernrp.westus.cloudapp.azure.com*. El comando siguiente usa el tipo de asignación estática.

	$publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT] El equilibrador de carga usará la etiqueta de dominio de la dirección IP pública como prefijo para su FQDN. Esto representa un cambio en el modelo de implementación clásica, que usa el servicio en la nube como el FQDN del equilibrador de carga. En este ejemplo, el FQDN será *loadbalancernrp.westus.cloudapp.azure.com*.

## Creación de un grupo de direcciones IP front-end y un grupo de direcciones de back-end

### Paso 1 

Cree un grupo de direcciones IP front-end llamado *LB-Frontend* que use la dirección IP pública *PublicIp*.

	$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### Paso 2 

Cree un grupo de direcciones de back-end llamado *LB-backend*.

	$beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## Crear reglas de equilibrador de carga, reglas NAT, un sondeo y un equilibrador de carga

En el ejemplo que aparece a continuación, se crean los elementos siguientes:

- Una regla NAT para trasladar todo el tráfico entrante del puerto 3441 al puerto 3389.
- Una regla NAT para trasladar todo el tráfico entrante del puerto 3442 al puerto 3389.
- Una regla de equilibrador de carga para equilibrar todo el tráfico entrante del puerto 80 al puerto 80 en las direcciones del grupo de back-end.
- Una regla de sondeo que comprobará el estado de mantenimiento en una página llamada *HealthProbe.aspx*.
- Un equilibrador de carga que usa todos los objetos anteriores.


### Paso 1

Cree las reglas NAT.

	$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### Paso 2

Cree una regla de equilibrador de carga.

	$lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### Paso 3

Cree un sondeo de estado. Hay dos formas de configurar un sondeo:
 
Sondeo HTTP
	
	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
o

Sondeo TCP
	
	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### Paso 4

Cree el equilibrador de carga mediante los objetos creados anteriormente.

	$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## Cree tarjetas NIC

Debe crear interfaces de red (o modificar las existentes) y asociarlas a las reglas NAT, las reglas del equilibrador de carga y los sondeos.

### Paso 1 

Obtenga la red virtual y la subred de red virtual, donde deben crearse las NIC.

	$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### Paso 2

Cree una NIC llamada *lb-nic1-be* y asóciela con la primera regla NAT y el primer (y único) grupo de direcciones de back-end.
	
	$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### Paso 3

Cree una NIC llamada *lb-nic2-be* y asóciela con la segunda regla NAT y el primer (y único) grupo de direcciones de back-end.

	$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### Paso 4

Compruebe las tarjetas NIC.


	PS C:\> $backendnic1

Resultado esperado:

	Name                 : lb-nic1-be
	ResourceGroupName    : NRP-RG
	Location             : westus
	Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
	ProvisioningState    : Succeeded
	Tags                 :
	VirtualMachine       : null
	IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/"d448256a-e1df-413a-9103-a137e07276d1"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
	DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
	AppliedDnsSettings   :
	NetworkSecurityGroup : null
	Primary              : False



### Paso 5

Use el cmdlet `Add-AzureRmVMNetworkInterface` para asignar las tarjetas NIC a distintas máquinas virtuales.

Puede encontrar instrucciones sobre cómo crear una máquina virtual y asignar una NIC en [Creación y preconfiguración de una máquina virtual de Windows con el Administrador de recursos y Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example) con la opción 5 del ejemplo.


O bien, si ya tiene una máquina virtual creada, puede agregar la interfaz de red con los pasos siguientes:

#### Paso 1 

Cargue el recurso de equilibrador de carga en una variable (si no lo ha hecho todavía). La variable que se utiliza se denomina $lb y utiliza los mismos nombres del recurso de equilibrador de carga creado anteriormente.

	$lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

#### Paso 2 

Cargue la configuración de back-end a una variable.

	PS C:\> $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### Paso 3 

Cargue la interfaz de red ya creada en una variable. El nombre de variable que se utiliza es $nic. El nombre de la interfaz de red utilizado es el mismo que en el ejemplo anterior.

	$nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

#### Paso 4

Cambie la configuración de back-end en la interfaz de red.

	PS C:\> $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### Paso 5 

Guarde el objeto de interfaz de red.

	PS C:\> Set-AzureRmNetworkInterface -NetworkInterface $nic

Después de agregar una interfaz de red para el grupo de back-end del equilibrador de carga, este inicia la recepción del tráfico de red según la reglas de equilibrio de carga para ese recurso de equilibrador de carga.

## Actualización de un equilibrador de carga existente


### Paso 1

Con el equilibrador de carga del ejemplo anterior, asigne el objeto del equilibrador de carga a la variable $slb mediante Get-AzureLoadBalancer

	$slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### Paso 2

En el ejemplo siguiente, agregará una nueva regla de NAT de entrada mediante el puerto 81 en el front-end y el puerto 8181 para el grupo de back-end a un equilibrador de carga existente

	$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP


### Paso 3

Guarde la nueva configuración mediante Set-AzureLoadBalancer

	$slb | Set-AzureRmLoadBalancer

## Eliminación de un equilibrador de carga

Use el comando `Remove-AzureLoadBalancer` para eliminar un equilibrador de carga creado previamente denominado "NRP-LB" en un grupo de recursos llamado "NRP-RG".

	Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Puede usar el conmutador opcional -Force para evitar la solicitud de eliminación.

## Pasos siguientes

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0302_2016-->