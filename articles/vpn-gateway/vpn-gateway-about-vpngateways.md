<properties 
   pageTitle="Acerca de las puertas de enlace de VPN para la conectividad entre entornos locales en redes virtuales | Microsoft Azure"
   description="Obtenga información sobre las puertas de enlace de VPN, que se pueden utilizar para conexiones entre entornos locales para configuraciones híbridas. Este artículo trata sobre las SKU de puerta de enlace (básica, estándar y de alto rendimiento), las configuraciones de la puerta de enlace de VPN y de ExpressRoute que coexisten, los tipos de enrutamiento de puerta de enlace (estática, dinámica, basada en directivas y basada en enrutamiento) y los requisitos de puerta de enlace para la conectividad de redes virtuales, tanto para los modelos de implementación del Administrador de recursos como clásico."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/19/2016"
   ms.author="cherylmc" />

# Información acerca de las puertas de enlace de VPN

Las puertas de enlace de VPN se utilizan para enviar tráfico de red entre redes virtuales y ubicaciones locales. También se usan para enviar el tráfico entre varias redes virtuales dentro de Azure (red virtual a red virtual). Al crear una puerta de enlace, es necesario tener en cuenta algunos factores.

- La SKU de puerta de enlace que desea utilizar
- El tipo VPN para la conexión
- El dispositivo VPN, si es necesario para la conexión

**Información sobre los modelos de implementación**

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]
 

## SKU de puerta de enlace

[AZURE.INCLUDE [vpn-gateway-table-sku](../../includes/vpn-gateway-table-sku-include.md)]

## Tipos de puerta de enlace de VPN

Hay dos tipos de VPN:

- **Basada en directivas:** las puertas de enlace basadas en directivas se denominan *puertas de enlace estáticas* en el modelo de implementación clásico. La funcionalidad de una puerta de enlace estática no ha cambiado, aun cuando haya cambiado el nombre. Este tipo de puerta de enlace admite las VPN basadas en directivas. Las VPN basadas en directivas dirigen los paquetes a través de túneles de IPsec con selectores de tráfico basados en las combinaciones de prefijos de dirección entre su red local y la red virtual de Azure. Normalmente, los selectores de tráfico o las directivas se definen como una lista de acceso en la configuración de la VPN.
 
- **Basada en enrutamientos:** las puertas de enlace basadas en enrutamientos se denominan *puertas de enlace dinámicas* en el modelo de implementación clásico. La funcionalidad de una puerta de enlace dinámica no ha cambiado, aun cuando haya cambiado el nombre. Las puertas de enlace basadas en enrutamiento implementan VPN basadas en enrutamiento. Las VPN basadas en enrutamiento utilizan "rutas" en la tabla de enrutamiento o reenvío de IP para dirigir los paquetes a sus correspondientes interfaces de túnel VPN. A continuación, las interfaces de túnel cifran o descifran los paquetes dentro y fuera de los túneles. La directiva o el selector de tráfico para las VPN basadas en enrutamiento se configura como conectividad de tipo cualquiera a cualquier (o caracteres comodín).

Algunas conexiones, como las de sitio a sitio y las de red virtual a red virtual, solo funcionarán con un tipo de VPN específico. Verá los requisitos de puerta de enlace enumerados en el artículo que se corresponde con el escenario de conexión que desea crear.

Los dispositivos VPN también tienen limitaciones de configuración. Cuando se crea una puerta de enlace de VPN, seleccionará el tipo de VPN que se requiere para la conexión, asegurándose de que el dispositivo VPN que prevé usar también admite dicho tipo de enrutamiento. Consulte [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md) para obtener más información.

Por ejemplo, si tiene previsto utilizar una conexión de sitio a sitio simultáneamente con una conexión de punto a sitio, necesitará configurar una puerta de enlace de VPN basada en enrutamiento. Si bien es cierto que las conexiones de sitio a sitio funcionarán con puertas de enlace basadas en directivas, las conexiones de punto a sitio requieren un tipo de puerta de enlace basada en enrutamiento. Dado que ambas conexiones utilizarán la misma puerta de enlace, tendrá que seleccionar el tipo de puerta de enlace que admite ambas conexiones. Además, el dispositivo VPN que utiliza también debe admitir las configuraciones basadas en enrutamiento.


## Requisitos de las puertas de enlace


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## Pasos siguientes

Seleccione el dispositivo VPN para su configuración. Consulte [Acerca de los dispositivos VPN](vpn-gateway-about-vpn-devices.md).





 

<!---HONumber=AcomDC_0302_2016-->