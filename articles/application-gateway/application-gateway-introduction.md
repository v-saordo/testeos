<properties
   pageTitle="Introducción a Puerta de enlace de aplicaciones | Microsoft Azure"
   description="Esta página proporciona información general sobre el servicio Puerta de enlace de aplicaciones para equilibrio de carga de nivel 7, incluidos los tamaños de puerta de enlace, el equilibrio de carga HTTP, la afinidad de sesión basada en cookies y la descarga SSL."
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
   ms.date="02/18/2016"
   ms.author="joaoma"/>

# Introducción a Puerta de enlace de aplicaciones

La Puerta de enlace de aplicaciones de Microsoft Azure ofrece una solución de equilibrio de carga HTTP administrada de Azure basada en el equilibrio de carga de nivel 7.

El equilibrio de carga de aplicaciones permite a los administradores de TI y desarrolladores crear reglas de enrutamiento para el tráfico de red basadas en HTTP. El servicio Puerta de enlace de aplicaciones tiene alta disponibilidad y está muy limitado. Para información sobre el contrato de nivel de servicio y los precios, consulte las páginas [Contratos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/) y [Precios de Puerta de enlace de aplicaciones](https://azure.microsoft.com/pricing/details/application-gateway/).

Puerta de enlace de aplicaciones actualmente admite la entrega de aplicación del nivel 7 para lo siguiente:

- Equilibrio de carga HTTP
- Afinidad de sesión basada en cookies
- [Descarga de Capa de sockets seguros (SSL)](application-gateway-ssl-arm.md)
- [Enrutamiento de contenido basado en direcciones URL](application-gateway-url-route-overview.md) 

## Equilibrio de carga de nivel 7 HTTP

Azure ofrece equilibrio de carga de nivel 4 a través del equilibrador de carga de Azure operativo en el nivel de transporte (TCP/UDP), y la carga de todo el tráfico de red entrante se equilibra en el servicio Puerta de enlace de aplicaciones. La Puerta de enlace de aplicaciones aplicará las reglas de enrutamiento al tráfico HTTP, ofreciendo un equilibrio de carga de nivel 7 (HTTP). Cuando se crea una puerta de enlace de aplicaciones, un punto de conexión (VIP) se asocia y usa como dirección IP pública para el tráfico de red de entrada.

La Puerta de enlace de aplicaciones enrutará el tráfico HTTP en función de su configuración, independientemente de que se trate de una máquina virtual, un servicio en la nube, una aplicación web o una dirección IP externa.

El equilibrio de carga de nivel 7 HTTP es útil para:

- Aplicaciones que requieren solicitudes de la misma sesión de usuario o cliente para llegar a la misma máquina virtual de back-end. Ejemplos de esto serían las aplicaciones de carro de la compra y los servidores de correo web.
- Aplicaciones que desean liberar a las granjas de servidores web de la sobrecarga de terminación SSL.
- Aplicaciones, como la red de entrega de contenido, que requieren que varias solicitudes HTTP en la misma conexión TCP de ejecución prolongada se enruten a servidores backend diferentes o su carga se equilibre entre estos.

 
## Tamaños e instancias de puerta de enlace

Puerta de enlace de aplicaciones actualmente se ofrece en tres tamaños: pequeño, mediano y grande. Tamaños pequeños de instancia están pensados para escenarios de desarrollo y pruebas.

Puede crear hasta 50 puertas de enlace de aplicaciones por suscripción y cada una de esas puertas de enlace de aplicaciones puede tener un máximo de 10 instancias. El equilibrio de carga de Puerta de enlace de aplicaciones como servicio administrado de Azure permite el aprovisionamiento de un equilibrador de carga de nivel 7 detrás del equilibrador de carga de software de Azure.

En la tabla siguiente se muestra un promedio de rendimiento para cada instancia de puerta de enlace de aplicaciones:


| Respuesta de la página de back-end | Pequeña | Mediano | Grande|
|---|---|---|---|
| 6K | 7,5 Mbps | 13 Mbps | 50 Mbps |
|100 k | 35 Mbps | 100 Mbps| 200 Mbps |


>[AZURE.NOTE] Se trata de una guía aproximada para un rendimiento de puerta de enlace de aplicaciones. El rendimiento real depende de varios detalles del entorno, como el tamaño medio de página, la ubicación de las instancias de back-end y el tiempo de procesamiento para proporcionar una página.


## Supervisión del estado

La puerta de enlace de aplicaciones de Azure supervisa el estado de las instancias de back-end automáticamente. Para obtener más información, consulte [Información general sobre la supervisión de estado de la puerta de enlace de aplicaciones](application-gateway-probe-overview.md).

## Configuración y administración

Puede crear y administrar una puerta de enlace de aplicaciones mediante las API de REST y mediante el uso de cmdlets de PowerShell.


## Pasos siguientes

Ahora que ya conoce cómo funciona Puerta de enlace de aplicaciones, puede [crear una puerta de enlace de aplicaciones](application-gateway-create-gateway.md) o bien puede [crear una descarga de SSL de Puerta de enlace de aplicaciones](application-gateway-ssl.md) para equilibrar la carga de las conexiones HTTPS.

Para aprender a crear una puerta de enlace de aplicaciones mediante el enrutamiento de contenido basado en direcciones URL, vaya a [Creación de una puerta de enlace de aplicaciones mediante enrutamiento basado en direcciones URL](application-gateway-create-url-route-arm-ps.md) para obtener más información.

<!---HONumber=AcomDC_0224_2016-->