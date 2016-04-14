<properties
   pageTitle="Creación de una puerta de enlace de aplicaciones mediante reglas de enrutamiento de direcciones URL | Microsoft Azure"
   description="En esta página se proporcionan instrucciones para crear y configurar una puerta de enlace de aplicaciones mediante reglas de enrutamiento de direcciones URL"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/10/2016"
   ms.author="joaoma"/>


# Creación de una puerta de enlace de aplicaciones mediante enrutamiento basado en URL 

El enrutamiento basado en URL permite asociar rutas en función de la dirección URL de la solicitud HTTP. Comprueba si hay una ruta a un grupo de back-end configurado para las listas de direcciones URL en la puerta de enlace de aplicaciones y envía el tráfico de red al grupo de back-end definido. Un uso común del enrutamiento basado en URL es el equilibrio de carga de las solicitudes de diferentes tipos de contenido entre diferentes grupos de servidores de back-end.

El enrutamiento basado en URL introduce un nuevo tipo de regla en la puerta de enlace de aplicaciones. La puerta de enlace de aplicaciones tiene dos tipos de reglas: básicas y PathBasedRouting. El tipo de regla básica proporciona un servicio round robin mientras que el tipo PathBasedRouting, además de la distribución round robin, también tiene en cuenta el patrón de ruta de acceso de la URL de la solicitud durante la elección del grupo de back-end.

>[AZURE.IMPORTANT] PathPattern: la lista de patrones de ruta de acceso con los que se buscan coincidencias. Cada uno de ellos debe comenzar con / y el único lugar donde se permite un carácter * es al final, después de un carácter '/'. La cadena que se suministra al comprobador de rutas de acceso no incluye texto después del primer ? o #, y esos caracteres no se permiten.

## Escenario
En el ejemplo siguiente, la puerta de enlace de aplicaciones atiende el tráfico de contoso.com con dos grupos de servidores de back-end: el grupo de servidores de vídeo y el grupo de servidores de imagen.

Las solicitudes de http://contoso.com/image* se enrutarán al grupo de servidores de imagen (pool1) y las de http://contoso.com/video* se enrutarán al grupo de servidores de vídeo (pool2). Si ninguno de los patrones de ruta de acceso coincide, se selecciona un grupo de servidores predeterminado (pool1).

![ruta de dirección URL](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente en la sección **Windows PowerShell** de la página [Descargas](https://azure.microsoft.com/downloads/).
2. Creará una red virtual y subred para la Puerta de enlace de aplicaciones. Asegúrese de que ninguna máquina virtual o implementación en la nube usan la subred. La Puerta de enlace de aplicaciones debe encontrarse en una subred de red virtual.
3. Los servidores agregados al grupo de back-end para usar la puerta de enlace de aplicaciones deben existir, o bien sus puntos de conexión deben haberse creado en la red virtual o tener una dirección IP/VIP pública asignada.



## ¿Qué se necesita para crear una Puerta de enlace de aplicaciones?


- **Grupo de servidores back-end**: lista de direcciones IP de los servidores back-end. Las direcciones IP que se enumeran deben pertenecer a la subred de la red virtual o ser una IP/VIP pública.
- **Configuración del grupo de servidores back-end**: cada grupo tiene una configuración como el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
- **Puerto front-end**: este puerto es el puerto público que se abre en la puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
- **Agente de escucha**: el agente de escucha tiene un puerto front-end, un protocolo (Http o Https, que distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL).
- **Regla**: enlaza el agente de escucha y el grupo de servidores back-end y define a qué grupo de servidores back-end se redirigirá el tráfico cuando se seleccione un agente de escucha concreto.

## Creación de una nueva puerta de enlace de aplicaciones

La diferencia entre el uso del Portal de Azure clásico y el Administrador de recursos de Azure es el orden en que se crea la puerta de enlace de aplicaciones y los elementos que es necesario configurar.

Con el Administrador de recursos, todos los elementos que componen una puerta de enlace de aplicaciones se configurarán individualmente y, luego, se unirán para crear el recurso de la Puerta de enlace de aplicaciones.


Estos son los pasos necesarios para crear una puerta de enlace de aplicaciones:

1. Cree un grupo de recursos para el Administrador de recursos.
2. Cree una red virtual, una subred y una IP pública para la puerta de enlace de aplicaciones.
3. Cree un objeto de configuración de la Puerta de enlace de aplicaciones.
4. Cree un recurso de Puerta de enlace de aplicaciones.

## Creación de un grupo de recursos para el Administrador de recursos

Asegúrese de que está usando la versión más reciente de Azure PowerShell. Hay más información disponible en [Uso de Windows PowerShell con Resource Manager](../powershell-azure-resource-manager.md).

### Paso 1
Inicio de sesión en Login-AzureRmAccount de Azure

Se le pedirá que se autentique con sus credenciales.<BR>

### Paso 2
Compruebe las suscripciones para la cuenta.

		Get-AzureRmSubscription

### Paso 3
Elija qué suscripción de Azure va a utilizar.<BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Paso 4
Cree un grupo de recursos nuevo (omita este paso si usa uno existente).

    New-AzureRmResourceGroup -Name appgw-RG -location "West US"
También puede crear etiquetas para un grupo de recursos de la puerta de enlace de aplicaciones:
	
	$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 

El Administrador de recursos de Azure requiere que todos los grupos de recursos especifiquen una ubicación. Esta se utiliza como ubicación predeterminada para los recursos de ese grupo de recursos. Asegúrese de que todos los comandos para crear una puerta de enlace de aplicaciones usen el mismo grupo de recursos.

En el ejemplo anterior, creamos un grupo de recursos denominado "appgw-RG" y la ubicación "West US".

>[AZURE.NOTE] Si necesita configurar un sondeo personalizado para la puerta de enlace de aplicaciones, consulte el artículo [Create an application gateway with custom probes by using PowerShell](application-gateway-create-probe-ps.md) (Creación de una puerta de enlace de aplicaciones con sondeos personalizados mediante PowerShell). Consulte también [Información general sobre la supervisión de estado de la puerta de enlace de aplicaciones](application-gateway-probe-overview.md) para más información.

## Creación de una red virtual y una subred para la puerta de enlace de aplicaciones.

En el ejemplo siguiente se muestra cómo crear una red virtual con el Administrador de recursos.

### Paso 1
Asigne el intervalo de direcciones 10.0.0.0/24 a la variable de subred que se utilizará para crear una red virtual.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Paso 2
Cree una red virtual denominada "appgwvnet" en el grupo de recursos "appgw-rg" para la región Oeste de EE. UU. con el prefijo 10.0.0.0/16 y la subred 10.0.0.0/24.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Paso 3
Asignación de una variable de subred en los pasos siguientes para crear una puerta de enlace de aplicaciones

	$subnet=$vnet.Subnets[0]

## Creación de una dirección IP pública para la configuración del front-end
Cree un recurso IP público "publicIP01" en el grupo de recursos "appgw-rg" para la región Oeste de EE. UU..

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

Se asignará una dirección IP a la puerta de enlace de aplicaciones cuando se inicia el servicio.

## Creación de una configuración de puerta de enlace de aplicaciones

Debe configurar todos los elementos de configuración antes de crear la puerta de enlace de aplicaciones. En los pasos siguientes, se crean los elementos de configuración necesarios para un recurso de puerta de enlace de aplicaciones.

### Paso 1
Cree una configuración de IP de puerta de enlace de aplicaciones denominada "gatewayIP01". Cuando se inicia Puerta de enlace de aplicaciones, esta elige una dirección IP de la subred configurada y redirige el tráfico de red a las direcciones IP en el grupo IP del back-end. Tenga en cuenta que cada instancia tomará una dirección IP.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Paso 2
Configuración del grupo de direcciones IP de back-end llamado "pool01"y "pool2" con las direcciones IP "134.170.185.46, 134.170.188.221,134.170.185.50" para "pool1" y "134.170.186.46, 134.170.189.221,134.170.186.50" para "pool2".


	$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

	$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.46, 134.170.189.221,134.170.186.50

En este ejemplo habrá dos grupos de back-end para enrutar el tráfico de red según la dirección URL. Un grupo recibe el tráfico de la dirección URL "/video" y otro lo recibe de la dirección "/image". Tiene que reemplazar las direcciones IP anteriores para agregar sus propios puntos de conexión de dirección IP de aplicación.

### Paso 3
Configuración de la opción de la puerta de enlace de aplicaciones "poolsetting01" y "poolsetting02" para el tráfico de red con carga equilibrada en el grupo de back-end En este ejemplo se configura otro grupo de back-end para los grupos de back-end. Cada grupo de back-end puede tener su propia configuración de grupo de back-end.

	$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

	$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### Paso 4
Configuración de la dirección IP de front-end con el punto de conexión de IP pública

	$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### Paso 5 
Configuración del puerto front-end de una puerta de enlace de aplicaciones

	$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
### Paso 6
Configuración del agente de escucha En este paso se configura el agente de escucha para la dirección IP pública y el puerto que se utiliza para recibir el tráfico de red entrante.
 
	$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

### Paso 7 
Configuración de rutas de acceso de reglas de URL para los grupos de back-end En este paso se configura la ruta de acceso relativa que utiliza la puerta de enlace de aplicaciones para definir la asignación entre la dirección URL y el grupo de back-end al que se asignará para administrar el tráfico entrante.

En el ejemplo siguiente se crean dos reglas: una para la ruta de acceso "/image/" que enruta el tráfico al grupo "pool1" de back-end y otra para la ruta de acceso "/video/" que enruta el tráfico al grupo "pool2" de back-end.
    
	$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

	$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting01

La configuración de asignación de ruta de acceso de regla también configura un grupo de direcciones de back-end predeterminado si la ruta de acceso no coincide con ninguna de las reglas de ruta de acceso predefinidas.

	$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02

### Paso 8 
Creación de una configuración de regla En este paso se configura la puerta de enlace de aplicaciones para usar el enrutamiento basado en URL.

	$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
### Paso 9: 
Configuración del número de instancias y el tamaño de la puerta de enlace de aplicaciones

	$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2

## Creación de la puerta de enlace de aplicaciones
Cree una puerta de enlace de aplicaciones con todos los objetos de configuración de los pasos anteriores.

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku

## Obtención de la puerta de enlace de aplicaciones
	$getgw =  Get-AzureRmApplicationGateway -Name $appgwName -ResourceGroupName $rgname

<!---HONumber=AcomDC_0224_2016-->