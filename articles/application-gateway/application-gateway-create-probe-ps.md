<properties
   pageTitle="Creación de un sondeo personalizado para Puerta de enlace de aplicaciones mediante PowerShell en el Administrador de recursos | Microsoft Azure"
   description="Aprenda a crear un sondeo personalizado para Puerta de enlace de aplicaciones mediante PowerShell en el Administrador de recursos"
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# Creación de un sondeo personalizado para Puerta de enlace de aplicaciones de Azure mediante PowerShell en el Administrador de recursos de Azure

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]


[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](application-gateway-create-probe-classic-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


### Paso 1

Utilice Login-AzureRmAccount para autenticarse.

	Login-AzureRmAccount

### Paso 2

Compruebe las suscripciones para la cuenta.

		get-AzureRmSubscription

Se le pedirá que se autentique con sus credenciales.<BR>

### Paso 3

Elija qué suscripción de Azure va a utilizar.<BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Paso 4

Cree un grupo de recursos nuevo (omita este paso si usa uno existente).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usen el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos denominado "appgw-RG" y la ubicación "Oeste de EE. UU.".

## Creación de una red virtual y una subred para la puerta de enlace de aplicaciones

Con estos pasos, se crea una red virtual y una subred para la puerta de enlace de aplicaciones.

### Paso 1


Asigne el intervalo de direcciones 10.0.0.0/24 a la variable de subred que se usará para crear una red virtual.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### Paso 2

Cree una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-rg" para la región Oeste de EE. UU. con el prefijo 10.0.0.0/16 y la subred 10.0.0.0/24.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Paso 3

Asigne una variable de subred en los pasos siguientes para crear una puerta de enlace de aplicaciones.

	$subnet=$vnet.Subnets[0]

## Creación de una dirección IP pública para la configuración del front-end


Cree un recurso IP público "publicIP01" en el grupo de recursos "appgw-rg" para la región Oeste de EE. UU..

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Creación de un objeto de configuración de la puerta de enlace de aplicaciones con un sondeo personalizado

Debe configurar todos los elementos de configuración antes de crear la puerta de enlace de aplicaciones. En los pasos siguientes, se crean los elementos de configuración necesarios para un recurso de puerta de enlace de aplicaciones.


### Paso 1

Cree una configuración de IP de puerta de enlace de aplicaciones denominada "gatewayIP01". Cuando se inicia Puerta de enlace de aplicaciones, esta elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP del back-end. Tenga en cuenta que cada instancia tomará una dirección IP.

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Paso 2


Configure el grupo de direcciones IP del back-end denominado "pool01" con las direcciones IP "134.170.185.46,134.170.188.221,134.170.185.50". Serán las direcciones IP que reciban el tráfico de red procedente del punto de conexión de la IP del front-end. Deberá reemplazar las direcciones IP anteriores para agregar sus propios extremos de direcciones IP de la aplicación.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### Paso 3


El sondeo personalizado se configura en este paso.

Los parámetros utilizados son:

- **-Interval**: configura las comprobaciones de intervalo de sondeo en segundos.
- **-Timeout**: define el tiempo de espera de sondeo para una comprobación de respuesta HTTP.
- **-Hostname y -path**: dirección URL completa que Puerta de enlace de aplicaciones invoca para determinar el estado de la instancia. Por ejemplo, si tiene el sitio web http://contoso.com/, el sondeo personalizado se puede configurar para "http://contoso.com/path/custompath.htm" de forma que las comprobaciones del sondeo tengan una respuesta HTTP correcta.
- **-UnhealthyThreshold**: el número de respuestas HTTP con error que es necesario para marcar la instancia del back-end como *incorrecta*.

<BR>

	$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


### Paso 4

Configure la puerta de enlace de aplicaciones "poolsetting01" para el tráfico del grupo del back-end. Este paso también tiene una configuración de tiempo de espera para la respuesta del grupo del back-end a una solicitud de puerta de enlace de aplicaciones. Cuando una respuesta del back-end alcanza un límite de tiempo de espera, Puerta de enlace de aplicaciones cancela la solicitud. Esto es diferente al tiempo de espera de sondeo, que es solo para que la respuesta del back-end a las comprobaciones de sondeo.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80


### Paso 5

Configure el puerto IP del front-end denominado "frontendport01" para el punto de conexión de la IP pública.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Paso 6

Cree la configuración de direcciones IP del front-end denominada "fipconfig01" y asocie la dirección IP pública con dicha configuración.


	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### Paso 7

Cree el nombre del agente de escucha "listener01" y asocie el puerto del front-end con la configuración de direcciones IP del front-end.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Paso 8

Cree la regla de enrutamiento del equilibrador de carga denominada "rule01" que configura el comportamiento del equilibrador de carga.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Paso 9:

Configure el tamaño de la instancia de la puerta de enlace de aplicaciones.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]  El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es Medium. Se puede elegir entre Standard\_Small, Standard\_Medium y Standard\_Large.

## Creación de una puerta de enlace de aplicaciones mediante New-AzureRmApplicationGateway

Cree una puerta de enlace de aplicaciones con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la puerta de enlace de aplicaciones se denomina "appgwtest".

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## Agregar un sondeo a una puerta de enlace de aplicaciones existente

Debe seguir cuatro pasos para agregar un sondeo personalizado a una puerta de enlace de aplicaciones existente.

### Paso 1

Cargue el recurso de puerta de enlace de aplicaciones en una variable de PowerShell mediante **Get-AzureRmApplicationGateway**.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Paso 2

Agregue un sondeo a la configuración de puerta de enlace existente.

	$probe = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


En el ejemplo, el sondeo personalizado está configurado para comprobar la dirección URL contoso.com/path/custompath.htm cada 30 segundos. Se configura un umbral de tiempo de espera de 120 segundos con un número máximo de 8 solicitudes de sondeo con error.

### Paso 3

Agregue el sondeo a la configuración y al tiempo de espera del grupo de back-end mediante **-Set-AzureRmApplicationGatewayBackendHttpSettings**.


	 $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### Paso 4

Guarde la configuración en la puerta de enlace de aplicaciones mediante **Set-AzureRmApplicationGateway**.

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

## Eliminación de un sondeo de una puerta de enlace de aplicaciones existente

Para eliminar un sondeo personalizado de una puerta de enlace de aplicaciones existentes, lleve a cabo los siguientes pasos.

### Paso 1

Cargue el recurso de puerta de enlace de aplicaciones en una variable de PowerShell mediante **Get-AzureRmApplicationGateway**.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### Paso 2

Quite la configuración de sondeo de la puerta de enlace de aplicaciones mediante **Remove-AzureRmApplicationGatewayProbeConfig**.

	$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### Paso 3

Actualice la configuración del grupo de back-end para quitar la configuración de sondeo y tiempo de espera mediante **-Set-AzureRmApplicationGatewayBackendHttpSettings**.


	 $getgw=Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### Paso 4

Guarde la configuración en la puerta de enlace de aplicaciones mediante **Set-AzureRmApplicationGateway**.

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

<!---HONumber=AcomDC_0128_2016-->