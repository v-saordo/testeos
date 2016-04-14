<properties
   pageTitle="Creación, inicio o eliminación de una Puerta de enlace de aplicaciones con el Administrador de recursos de Azure | Microsoft Azure"
   description="Esta página proporciona instrucciones para crear, configurar, iniciar y eliminar una Puerta de enlace de aplicaciones de Azure mediante el Administrador de recursos de Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma"/>


# Creación, inicio o eliminación de una Puerta de enlace de aplicaciones con el Administrador de recursos de Azure

Puerta de enlace de aplicaciones de Azure es un equilibrador de carga de nivel 7. Proporciona conmutación por error, solicitudes HTTP de enrutamiento de rendimiento entre distintos servidores, independientemente de que se encuentren en la nube o en una implementación local. Puerta de enlace de aplicaciones tiene las siguientes características de entrega de aplicaciones: equilibrio de carga HTTP, afinidad de sesiones basada en cookies y descarga SSL (Capa de sockets seguros).


> [AZURE.SELECTOR]
- [Pasos de Azure Classic PowerShell](application-gateway-create-gateway.md)
- [PowerShell del Administrador de recursos de Azure](application-gateway-create-gateway-arm.md)
- [Plantilla del Administrador de recursos de Azure](application-gateway-create-gateway-arm-template.md)


<BR>


Este artículo le guiará por los pasos necesarios para crear, configurar, iniciar y eliminar una Puerta de enlace de aplicaciones.


>[AZURE.IMPORTANT] Antes de trabajar con recursos de Azure, es importante comprender que Azure tiene actualmente dos modelos de implementación: el Administrador de recursos y el clásico. Antes de trabajar con los recursos de Azure asegúrese de que conoce los [modelos de implementación y las herramientas](../azure-classic-rm.md). Puede ver la documentación de las distintas herramientas haciendo clic en las fichas en la parte superior de este artículo. Este documento describe la creación de una Puerta de enlace de aplicaciones con el Administrador de recursos de Azure Para utilizar la versión clásica, vaya a [Creación, inicio o eliminación de una puerta de enlace de aplicaciones](application-gateway-create-gateway.md).



## Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. La versión más reciente se puede descargar e instalar desde la sección **Windows PowerShell** de la [página Descargas](https://azure.microsoft.com/downloads/).
2. Creará una red virtual y subred para la Puerta de enlace de aplicaciones. Asegúrese de que ninguna máquina virtual o implementación en la nube usan la subred. La Puerta de enlace de aplicaciones debe encontrarse en una subred de red virtual.
3. Los servidores que configurará para que usen la Puerta de enlace de aplicaciones deben existir, o bien sus puntos de conexión deben haberse creado en la red virtual o tener una dirección IP/VIP pública asignada.

## ¿Qué se necesita para crear una Puerta de enlace de aplicaciones?


- **Grupo de servidores back-end:** la lista de direcciones IP de los servidores back-end. Las direcciones IP que se enumeran deben pertenecer a la subred de la red virtual o ser una IP/VIP pública.
- **Configuración de grupo de servidores back-end:** cada grupo tiene una configuración en la que se incluye el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
- **Puerto front-end:** este es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
- **Escucha:** el agente de escucha tiene un puerto front-end, un protocolo (Http o Https, que distinguen mayúsculas de minúsculas) y el nombre de certificado SSL (si se configura la descarga de SSL).
- **Regla**: enlaza el agente de escucha y el grupo de servidores back-end, y define a qué grupo de servidores back-end se debe redireccionar el tráfico llegue a un agente de escucha concreto. 



## Creación de una nueva puerta de enlace de aplicaciones

La diferencia entre el uso del Portal de Azure clásico y el Administrador de recursos de Azure es el orden en que se crea la puerta de enlace de aplicaciones y los elementos que es necesario configurar.

Con el Administrador de recursos, todos los elementos que componen una puerta de enlace de aplicaciones se configurarán individualmente y, luego, se unirán para crear el recurso de la Puerta de enlace de aplicaciones.


Estos son los pasos necesarios para crear una puerta de enlace de aplicaciones:

1. Cree un grupo de recursos para el Administrador de recursos.
2. Cree una red virtual, una subred y una IP pública para la puerta de enlace de aplicaciones.
3. Cree un objeto de configuración de la Puerta de enlace de aplicaciones.
4. Cree un recurso de Puerta de enlace de aplicaciones.


## Creación de un grupo de recursos para el Administrador de recursos

Asegúrese de que está usando la versión más reciente de Azure PowerShell. Para más información, consulte [Uso de Azure PowerShell con Administrador de recursos de Azure](../powershell-azure-resource-manager.md).

### Paso 1
Inicio de sesión en Login-AzureRmAccount de Azure

Se le solicitará que se autentique con sus credenciales.<BR>
### Paso 2
Compruebe las suscripciones para la cuenta.

		Get-AzureRmSubscription

### Paso 3
Elija qué suscripción de Azure va a utilizar.<BR>

		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Paso 4
Cree un grupo de recursos nuevo (omita este paso si usa uno existente).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usen el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos denominado "appgw-RG" y la ubicación "West US".

>[AZURE.NOTE] Si necesita configurar un sondeo personalizado para la puerta de enlace de aplicaciones, consulte [Creación de un sondeo personalizado para la Puerta de enlace de aplicaciones de Azure (clásica) mediante PowerShell](application-gateway-create-probe-ps.md). Para más información, consulte [Información general sobre la supervisión de estado de la puerta de enlace de aplicaciones](application-gateway-probe-overview.md).



## Creación de una red virtual y una subred para la puerta de enlace de aplicaciones.

En el ejemplo siguiente se muestra cómo crear una red virtual con el Administrador de recursos.

### Paso 1

Asigne el intervalo de direcciones 10.0.0.0/24 a la variable de subred que se utilizará para crear una red virtual.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Paso 2

Cree una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-rg" para la región Oeste de EE. UU. con el prefijo 10.0.0.0/16 y la subred 10.0.0.0/24.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Paso 3

Asigne una variable de subred en los pasos siguientes para crear una puerta de enlace de aplicaciones.

	$subnet=$vnet.Subnets[0]

## Creación de una dirección IP pública para la configuración del front-end

Cree un recurso IP público "publicIP01" en el grupo de recursos "appgw-rg" para la región West US.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Creación de un objeto de configuración de la Puerta de enlace de aplicaciones

Debe configurar todos los elementos de configuración antes de crear la Puerta de enlace de aplicaciones de la aplicación. En los pasos siguientes, se crean los elementos de configuración necesarios para un recurso de puerta de enlace de aplicaciones.

### Paso 1

Cree una configuración de IP de puerta de enlace de aplicaciones denominada "gatewayIP01". Cuando se inicia Puerta de enlace de aplicaciones, esta elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP del back-end. Tenga en cuenta que cada instancia tomará una dirección IP.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Paso 2

Configure el grupo de direcciones IP del back-end denominado "pool01" con las direcciones IP "134.170.185.46,134.170.188.221,134.170.185.50". Serán las direcciones IP que reciban el tráfico de red procedente del punto de conexión de la IP del front-end. Deberá reemplazar las direcciones IP anteriores para agregar sus propios extremos de direcciones IP de la aplicación.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50



### Paso 3

Configure la opción de la puerta de enlace de aplicaciones "poolsetting01" para el tráfico de red con carga equilibrada del grupo de back-end.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled


### Paso 4

Configure el puerto IP del front-end denominado "frontendport01" para el punto de conexión de la IP pública.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Paso 5

Cree la configuración de direcciones IP front-end denominada "fipconfig01" y asocie la dirección IP pública con dicha configuración.

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip


### Paso 6

Cree el nombre del agente de escucha "listener01" y asocie el puerto front-end con la configuración de direcciones IP del front-end.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Paso 7

Cree la regla de enrutamiento del equilibrador de carga denominada "rule01" que configura el comportamiento del equilibrador de carga.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Paso 8

Configure el tamaño de la instancia de la Puerta de enlace de aplicaciones.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]  El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es Medium. Se puede elegir entre Standard\_Small, Standard\_Medium y Standard\_Large.

## Creación de una puerta de enlace de aplicaciones mediante New-AzureRmApplicationGateway

Cree una puerta de enlace de aplicaciones con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la Puerta de enlace de aplicaciones se denomina "appgwtest".

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku


## Eliminación de una puerta de enlace de aplicaciones

Para eliminar una Puerta de enlace de aplicaciones, siga estos pasos:

1. Utilice el cmdlet **Stop-AzureRmApplicationGateway** para detener la puerta de enlace.
2. Utilice el cmdlet **Remove-AzureRmApplicationGateway** para quitar la puerta de enlace.
3. Para comprobar que se ha quitado la puerta de enlace, use el cmdlet **Get-AzureRmApplicationGateway**.

### Paso 1

Obtenga el objeto de puerta de enlace de aplicaciones y asócielo a una variable "$getgw".

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Paso 2

Utilice **Stop-AzureRmApplicationGateway** para detener la puerta de enlace de aplicaciones.

	Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


Cuando la puerta de enlace de aplicaciones se haya detenido, use el cmdlet **Remove-AzureRmApplicationGateway** para quitar el servicio.


	Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force



>[AZURE.NOTE] Se puede usar el modificador **-force** para suprimir el mensaje de confirmación


Para comprobar que el servicio se ha quitado, se puede usar el cmdlet **Get-AzureRmApplicationGateway**. Este paso no es necesario.


	Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


## Pasos siguientes

Si desea configurar la descarga de SSL, consulte [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el modelo de implementación clásica](application-gateway-ssl.md).

Si desea configurar una puerta de enlace de aplicaciones para usarla con un equilibrador de carga interno, consulte [Creación de una puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea obtener más información acerca de opciones de equilibrio de carga en general, vea:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0302_2016-->