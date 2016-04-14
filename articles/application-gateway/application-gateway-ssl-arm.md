<properties
   pageTitle="Configuración de una puerta de enlace de aplicaciones para la descarga SSL con el Administrador de recursos de Azure | Microsoft Azure"
   description="En esta página se ofrecen instrucciones para crear una puerta de enlace de aplicaciones con descarga SSL mediante el Administrador de recursos de Azure"
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
   ms.date="03/03/2016"
   ms.author="joaoma"/>

# Configuración de una puerta de enlace de aplicaciones para la descarga SSL mediante el Administrador de recursos de Azure

> [AZURE.SELECTOR]
-[Azure Classic PowerShell](application-gateway-ssl.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)

 Puerta de enlace de aplicaciones de Azure puede configurarse para terminar la sesión SSL (Capa de sockets seguros) en la puerta de enlace para evitar las costosas tareas de descifrado de SSL que tienen lugar en la granja de servidores web. La descarga SSL también simplifica la configuración del servidor front-end y la administración de la aplicación web.


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

**Notas de configuración adicionales**

Para la configuración de certificados SSL, el protocolo de **HttpListener** debería cambiar a *Https* (con distinción entre mayúsculas y minúsculas). El elemento **SslCertificate** debe agregarse a **HttpListener** con el valor de la variable configurado para el certificado SSL. El puerto front-end debe actualizarse al 443.

**Para habilitar la afinidad basada en cookies**: se puede configurar una puerta de enlace de aplicaciones para asegurarse de que las solicitudes de una sesión de cliente siempre se dirigen a la misma máquina virtual de la granja de servidores web. Para ello, es preciso inyectar una cookie de sesión que permita a la puerta de enlace dirigir el tráfico de forma adecuada. Para habilitar la afinidad basada en cookies, establezca **CookieBasedAffinity** en *Habilitado* en el elemento **BackendHttpSettings**.


## Creación de una nueva puerta de enlace de aplicaciones

La diferencia entre el uso del modelo de implementación clásico de Azure y el Administrador de recursos de Azure es el orden en que creará una puerta de enlace de aplicaciones y los elementos que es necesario configurar.

Con el Administrador de recursos, todos los elementos que componen un puerta de enlace de aplicaciones se configurarán individualmente y, luego, se unirán para crear un recurso de puerta de enlace de aplicaciones.


Estos son los pasos necesarios para crear una puerta de enlace de aplicaciones:

1. Creación de un grupo de recursos para el Administrador de recursos
2. Creación de una red virtual, una subred y una IP pública para la puerta de enlace de aplicaciones
3. Creación de un objeto de configuración de la Puerta de enlace de aplicaciones
4. Creación de un recurso de puerta de enlace de aplicaciones


## Creación de un grupo de recursos para el Administrador de recursos

Asegúrese de cambiar el modo de PowerShell para que use los cmdlets del Administrador de recursos de Azure. Hay más información disponible en [Uso de Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md).

### Paso 1

		PS C:\> Login-AzureRmAccount



### Paso 2

Compruebe las suscripciones para la cuenta.

		PS C:\> get-AzureRmSubscription

Se le solicitará que se autentique con sus credenciales.<BR>

### Paso 3

Elija qué suscripción de Azure va a utilizar.<BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Paso 4

Cree un grupo de recursos nuevo (omita este paso si usa uno existente).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usen el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos denominado "appgw-RG" y la ubicación "Oeste de EE. UU.".

## Creación de una red virtual y una subred para la puerta de enlace de aplicaciones.

En el ejemplo siguiente se muestra cómo crear una red virtual con el Administrador de recursos:

### Paso 1

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Se asigna el intervalo de direcciones 10.0.0.0/24 a la variable de subred que se va a usar para crear una red virtual.

### Paso 2
	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Se crea una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-rg" para la región West US con el prefijo 10.0.0.0/16 y la subred 10.0.0.0/24.

### Paso 3

	$subnet=$vnet.Subnets[0]

Se asigna el objeto de subred a la variable $subnet para los siguientes pasos.

## Creación de una dirección IP pública para la configuración del front-end

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Se crea un recurso de IP pública "publicIP01" en el grupo de recursos "appgw-rg" para la región West US.


## Creación de un objeto de configuración de la Puerta de enlace de aplicaciones

### Paso 1

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Se crea una configuración de la IP de la puerta de enlace de aplicaciones denominada "gatewayIP01". Cuando se inicia la Puerta de enlace de aplicaciones, elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP de back-end. Tenga en cuenta que cada instancia tomará una dirección IP.

### Paso 2

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Se configura el grupo de direcciones IP de back-end denominado "pool01" con las direcciones IP "134.170.185.46, 134.170.188.221 y 134.170.185.50". Serán las direcciones IP que reciban el tráfico de red procedente del punto de conexión de la IP del front-end. Reemplace las direcciones IP del ejemplo anterior por las direcciones IP de los puntos de conexión de la aplicación web.

### Paso 3

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Configura la opción de puerta de enlace de aplicaciones "poolsetting01" para el tráfico de red con carga equilibrada del grupo de back-end.

### Paso 4

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Configura el puerto IP del front-end denominado "frontendport01" para el punto de conexión de la IP pública.

### Paso 5

	$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Configura el certificado que se usa para la conexión SSL. Es preciso que el certificado tenga el formato .pfx y que la contraseña tenga entre 4 y 12 caracteres.

### Paso 6

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Crea la configuración de la IP de front-end denominada "fipconfig01" y asocia la dirección IP pública con dicha configuración.

### Paso 7

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Crea el nombre de agente de escucha "listener01" y asocia el puerto front-end con la configuración de la IP del front-end y el certificado.

### Paso 8

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Crea la regla de enrutamiento del equilibrador de carga denominada "rule01" que configura el comportamiento del equilibrador de carga.

### Paso 9:

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Configura el tamaño de instancia de la puerta de enlace de aplicaciones.

>[AZURE.NOTE]  El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es Medium. Se puede elegir entre Standard\_Small, Standard\_Medium y Standard\_Large.

## Creación de una puerta de enlace de aplicaciones con New-AzureApplicationGateway

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Crea una puerta de enlace de aplicaciones con todos los elementos de configuración de los pasos anteriores. En el ejemplo, la Puerta de enlace de aplicaciones se denomina "appgwtest".

## Pasos siguientes

Si desea configurar una puerta de enlace de aplicaciones para usarla con el equilibrador de carga interno (ILB), consulte [Creación de una puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea obtener más información acerca de opciones de equilibrio de carga en general, vea:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0309_2016-->