<properties
   pageTitle="Creación y configuración de una puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB) mediante el Administrador de recursos de Azure | Microsoft Azure"
   description="En esta página se proporcionan instrucciones para crear, configurar, iniciar y eliminar una puerta de enlace de aplicaciones de Azure con un equilibrador de carga interno (ILB) en el Administrador de recursos de Azure"
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


# Creación de una puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB) mediante el Administrador de recursos de Azure

> [AZURE.SELECTOR]
- [Azure classic steps](application-gateway-ilb.md)
- [Resource Manager PowerShell steps](application-gateway-ilb-arm.md)

Puerta de enlace de aplicaciones de Azure se puede configurar con una VIP conexión a Internet o con un punto de conexión interno no expuesto a Internet, también conocido como punto de conexión ILB (equilibrador de carga interno). La configuración de la puerta de enlace con un ILB es útil para aplicaciones de línea de negocio internas no expuestas a Internet. También es útil para los distintos servicios y niveles de una aplicación de niveles múltiples que se encuentran dentro de un límite de seguridad no expuesto a Internet, pero que aún así siguen necesitando distribución de carga round robin, permanencia de sesión o terminación SSL (Capa de sockets seguros).

Este artículo le guiará por los pasos necesarios para configurar una puerta de enlace de la aplicaciones con un ILB.

## Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección **Windows PowerShell** de la [página Descargas](https://azure.microsoft.com/downloads/).
2. Creará una red virtual y una subred para la puerta de enlace de aplicaciones. Asegúrese de que ninguna máquina virtual o implementación en la nube usan la subred. La puerta de enlace de aplicaciones debe encontrarse en una subred de una red virtual.
3. Los servidores que configurará para que usen la Puerta de enlace de aplicaciones deben existir, o bien sus puntos de conexión deben haberse creado en la red virtual o tener una dirección IP/VIP pública asignada.

## ¿Qué se necesita para crear una Puerta de enlace de aplicaciones?


- **Grupo de servidores back-end:** lista de direcciones IP de los servidores back-end. Las direcciones IP que se enumeran deben pertenecer a la subred de la red virtual o ser una IP/VIP pública.
- **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración en la que se incluye el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
- **Puerto front-end:** este puerto es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
- **Agente de escucha:** tiene un puerto front-end, un protocolo (Http o Https, que distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL).
- **Regla:** enlaza el agente de escucha y el grupo de servidores back-end y define a qué grupo de servidores back-end se dirigirá el tráfico cuando llega a un agente de escucha concreto. Actualmente, solo se admite la regla *básica*. La regla *básica* es la distribución de carga round robin.



## Creación de una nueva puerta de enlace de aplicaciones

La diferencia entre el uso del Portal de Azure clásico y el Administrador de recursos de Azure es el orden en que se crea la puerta de enlace de aplicaciones y los elementos que es necesario configurar. Con el Administrador de recursos, todos los elementos que componen una puerta de enlace de aplicaciones se configurarán individualmente y, luego, se unirán para crear el recurso de la Puerta de enlace de aplicaciones.


Estos son los pasos necesarios para crear una puerta de enlace de aplicaciones:

1. Creación de un grupo de recursos para el Administrador de recursos
2. Creación de una red virtual y una subred para la puerta de enlace de aplicaciones.
3. Creación de un objeto de configuración de la Puerta de enlace de aplicaciones
4. Creación de un recurso de puerta de enlace de aplicaciones


## Creación de un grupo de recursos para el Administrador de recursos

Asegúrese de cambiar el modo de PowerShell para que use los cmdlets del Administrador de recursos de Azure. Hay más información disponible en [Uso de Azure PowerShell con Administrador de recursos de Azure](powershell-azure-resource-manager.md).

### Paso 1

		PS C:\> Login-AzureRmAccount

### Paso 2

Compruebe las suscripciones para la cuenta.

		PS C:\> get-AzureRmSubscription

Se le solicitará que se autentique con sus credenciales.<BR>

### Paso 3

Elija qué suscripción de Azure va a utilizar.<BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Paso 4

Cree un grupo de recursos nuevo (omita este paso si usa uno existente).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones se van a usar en el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos denominado "appgw-rg" y la ubicación "West US".

## Creación de una red virtual y una subred para la puerta de enlace de aplicaciones.

En el ejemplo siguiente se muestra cómo crear una red virtual con el Administrador de recursos:

### Paso 1

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Se asigna el intervalo de direcciones 10.0.0.0/24 a la variable de subred que se va a usar para crear una red virtual.

### Paso 2

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Se crea una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-rg" para la región West US con el prefijo 10.0.0.0/16 y la subred 10.0.0.0/24.

### Paso 3

	$subnet=$vnet.subnets[0]

Se asigna el objeto de subred a la variable $subnet para los siguientes pasos.

## Creación de un objeto de configuración de la Puerta de enlace de aplicaciones

### Paso 1

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Se crea una configuración de la IP de la puerta de enlace de aplicaciones denominada "gatewayIP01". Cuando se inicia la Puerta de enlace de aplicaciones, elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia tomará una dirección IP.


### Paso 2

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.0.10,10.0.0.11,10.0.0.12

Se configura el grupo de direcciones IP de back-end denominado "pool01" con las direcciones IP "10.0.0.10, 10.0.0.11 y 10.0.0.12". Serán las direcciones IP que reciban el tráfico de red procedente del punto de conexión de la IP del front-end. Deberá reemplazar las direcciones IP anteriores para agregar sus propios extremos de direcciones IP de la aplicación.

### Paso 3

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Configura la opción de la puerta de enlace de aplicaciones "poolsetting01" para el tráfico de red con carga equilibrada del grupo de back-end.

### Paso 4

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Configura el puerto IP del front-end denominado "frontendport01" para el ILB.

### Paso 5

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Crea la configuración de la IP del front-end llamada "fipconfig01" y la asocia una dirección IP privada de la subred de la red virtual actual.

### Paso 6

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Crea el nombre de agente de escucha "listener01" y asocia el puerto front-end con la configuración de la IP del front-end.

### Paso 7

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Crea la regla de enrutamiento del equilibrador de carga denominada "rule01" que configura el comportamiento del equilibrador de carga.

### Paso 8

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Configura el tamaño de instancia de la puerta de enlace de aplicaciones.

>[AZURE.NOTE]  El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es Medium. Se puede elegir entre Standard\_Small, Standard\_Medium y Standard\_Large.

## Creación de una puerta de enlace de aplicaciones con New-AzureApplicationGateway

Crea una puerta de enlace de aplicaciones con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la puerta de enlace de aplicaciones se denomina "appgwtest".


	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Crea una puerta de enlace de aplicaciones con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la Puerta de enlace de aplicaciones se denomina "appgwtest".


## Eliminación de una puerta de enlace de aplicaciones

Para eliminar una puerta de enlace de aplicaciones, deberá hacer lo siguiente en orden:

1. Use el cmdlet **Stop-AzureRmApplicationGateway** para detener la puerta de enlace.
2. Use el cmdlet **Remove-AzureRmApplicationGateway** para quitar la puerta de enlace.
3. Compruebe que se ha quitado la puerta de enlace mediante el cmdlet **Get-AzureApplicationGateway**.


### Paso 1

Obtenga el objeto de puerta de enlace de aplicaciones y asócielo a una variable "$getgw".

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Paso 2

Use **Stop-AzureRmApplicationGateway** para detener la puerta de enlace de aplicaciones. Este ejemplo muestra el cmdlet **Stop-AzureRmApplicationGateway** en la primera línea, seguido de la salida.

	PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Cuando la puerta de enlace de aplicaciones esté en estado detenido, use el cmdlet **Remove-AzureRmApplicationGateway** para quitar el servicio.


	PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] Se puede usar el modificador **-force** para suprimir el mensaje de confirmación de eliminación.


Para comprobar que el servicio se ha quitado, se puede usar el cmdlet **Get-AzureRmApplicationGateway**. Este paso no es necesario.


	PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
	.....

## Pasos siguientes

Si desea configurar la descarga de SSL, consulte [Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el modelo de implementación clásica](application-gateway-ssl.md).

Si quiere configurar una puerta de enlace de aplicaciones para usarla con un ILB, consulte [Creación de una puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea obtener más información acerca de opciones de equilibrio de carga en general, vea:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0128_2016-->