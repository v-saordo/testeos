<properties
   pageTitle="Información general del enrutamiento de contenido basado en URL | Microsoft Azure"
   description="En esta página se proporciona información general sobre el enrutamiento de contenido basado en URL de la puerta de enlace de aplicaciones, la configuración de UrlPathMap y la regla PathBasedRouting."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma"/>

# Información general del enrutamiento basado en URL

El enrutamiento basado en URL le permite enrutar el tráfico a los grupos de servidores back-end en función de las direcciones URL de la solicitud. Por ejemplo, puede enrutar las solicitudes de diferentes tipos de contenido a diferentes grupos de servidores back-end. En el ejemplo siguiente, la puerta de enlace de aplicaciones atiende el tráfico de contoso.com desde tres grupos de servidores back-end: VideoServerPool, ImageServerPool y DefaultServerPool.

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

Las solicitudes de http://contoso.com/video* se enrutarán a VideoServerPool y las de http://contoso.com/images* a ImageServerPool. DefaultServerPool se selecciona si ninguno de los patrones de ruta de acceso coincide.

## Elemento de configuración UrlPathMap

El elemento UrlPathMap se utiliza para especificar patrones de ruta de acceso para las asignaciones de grupos de servidores back-end. Este es el fragmento de código del elemento urlPathMap del archivo de plantilla.

	"urlPathMaps": [
	{
    "name": "<urlPathMapName>",
    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName>"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpSettingsList/<settingsName>"
        },
        "pathRules": [
            {
                "paths": [
                    <pathPattern>
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendAddressPools/<poolName2>"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/backendHttpsettingsList/<settingsName2>"
                },

            },

        ],

    }
	}
	

>[AZURE.NOTE] PathPattern: la lista de patrones de ruta de acceso con los que se buscan coincidencias. Cada uno de ellos debe comenzar con / y el único lugar donde se permite un carácter * es al final, después de un carácter '/'. La cadena que se suministra al comprobador de rutas de acceso no incluye texto después del primer ? o #, y esos caracteres no se permiten aquí.

Para más información, puede desproteger una [plantilla de ARM mediante el enrutamiento basado en URL](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing).

## Regla de PathBasedRouting

RequestRoutingRule de tipo PathBasedRouting se usa para enlazar un agente de escucha a un elemento urlPathMap. Todas las solicitudes que se reciben para este agente de escucha se enrutan según la directiva especificada en urlPathMap. Fragmento de código de la regla PathBasedRouting:

	"requestRoutingRules": [
  	{

    "name": "<ruleName>",
    "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/requestRoutingRules/<ruleName>",
    "properties": {
        "ruleType": "PathBasedRouting",
        "httpListener": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/httpListeners/<listenerName>"
        },
        "urlPathMap": {
            "id": "/subscriptions/<subscriptionId>/../microsoft.network/applicationGateways/<gatewayName>/ urlPathMaps/<urlPathMapName>"
        },

    }
	
## Pasos siguientes 

Ahora que conoce el enrutamiento de contenido basado en URL, vaya a [Create an application gateway using URL based routing](application-gateway-create-url-route-arm-ps.md) (Creación de una puerta de enlace de aplicaciones mediante el enrutamiento basado en URL) para crear una puerta de enlace de aplicaciones con reglas de enrutamiento de direcciones URL.

<!---HONumber=AcomDC_0224_2016-->