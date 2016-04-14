<properties 
   pageTitle="Redes híbridas (patrones de arquitectura de Azure)" 
   description="El patrón Redes híbridas es parte del área Infraestructura, que se describe ampliamente en el documento de arquitectura de CPIF." 
   services="" 
   documentationCenter="" 
   authors="arynes" 
   manager="fredhar" 
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple" 
   ms.date="03/25/2015"
   ms.author="arynes"/>

# Redes híbridas (patrones de arquitectura de Azure)

El [Marco de integración de plataformas en la nube](azure-architectures-cpif-overview.md) proporciona instrucciones de integración de carga de trabajo para incorporar aplicaciones en una solución en la nube de Microsoft.

CPIF describe cómo las organizaciones, los clientes y los socios deben diseñar e implementar cargas de trabajo para la nube utilizando la plataforma en la nube híbrida y las capacidades de administración de Azure, System Center y Windows Server.

El patrón **Redes híbridas** es parte del área **Infraestructura**, que se describe ampliamente en el documento de arquitectura de CPIF.

##  Redes híbridas

El patrón de diseño Redes híbridas detalla las características y los servicios de Azure necesarios para prestar funcionalidad de red que puede proporcionar un rendimiento predecible y alta disponibilidad a través de fronteras geográficas. En el sitio de documentación de Microsoft Azure se proporciona una lista completa de regiones de Microsoft Azure y los servicios disponibles dentro de cada una de ellas. Este documento proporciona información general de las capacidades de red de Microsoft Azure para entornos híbridos. Redes virtuales de Microsoft Azure permite crear redes aisladas de forma lógica en Azure y conectarlas de forma segura al centro de datos local a través de Internet o mediante una conexión de red privada. Además, los equipos cliente individuales pueden conectarse a una red aislada de Azure mediante una conexión VPN IPsec.

El modelo de arquitectura Redes híbridas incluye las siguientes áreas de enfoque:

- Conexión de redes locales a Azure 
- Ampliación de redes virtuales de Azure entre regiones 
- Ampliación de redes virtuales de Azure entre suscripciones 
- Proporcionar a los desarrolladores acceso de red remoto 

## Información general del patrón de arquitectura 

El patrón de arquitectura Redes híbridas es complejo debido al posible número de escenarios que se pueden crear. Este modelo de arquitectura se centrará en los cuatro escenarios siguientes:

- Redes híbridas de sitio a sitio con enrutamiento de red virtual de saltos múltiples dentro de una sola suscripción y región 
- Redes híbridas de sitio a sitio con enrutamiento de red virtual de saltos múltiples entre suscripciones y regiones 
- Redes híbridas ExpressRoute usando conectividad MPLS 
- Redes híbridas ExpressRoute usando conectividad IXP 

##  Recursos adicionales
[Redes híbridas (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

## Otras referencias
[Arquitectura CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Nivel web de carga equilibrada global](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Nivel de datos de carga equilibrada](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Nivel de Búsqueda de Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[Nivel de procesamiento por lotes](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=Oct15_HO4-->