<properties 
   pageTitle="Nivel web de carga equilibrada global (patrones de arquitectura de Azure)" 
   description="El patrón Nivel web de carga equilibrada global es parte del área Fundación, que se describe ampliamente en el documento de arquitectura de CPIF." 
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

# Nivel web de carga equilibrada global (patrones de arquitectura de Azure)

El [Marco de integración de plataformas en la nube](azure-architectures-cpif-overview.md) proporciona instrucciones de integración de carga de trabajo para incorporar aplicaciones en una solución en la nube de Microsoft.

CPIF describe cómo las organizaciones, los clientes y los socios deben diseñar e implementar cargas de trabajo para la nube utilizando la plataforma en la nube híbrida y las capacidades de administración de Azure, System Center y Windows Server.

El patrón **Nivel web de carga equilibrada global** es parte del área **Fundación**, que se describe ampliamente en el documento de arquitectura de CPIF.

##  Nivel web de carga equilibrada global

El patrón de diseño Nivel web de carga equilibrada global detalla las características y los servicios de Azure necesarios para prestar servicios de nivel web que pueden proporcionar un rendimiento predecible y alta disponibilidad a través de fronteras geográficas.

Para los fines de este patrón de diseño, se define un nivel web como un nivel de servicio que proporciona contenido HTTP/HTTPS tradicional o servicios de aplicación de forma aislada o como parte de una aplicación web de varios niveles. Dentro de este patrón, el equilibrio de carga del nivel web se proporciona tanto localmente dentro de la región como entre regiones. Desde una perspectiva de proceso, estos servicios se pueden proporcionar a través de máquinas virtuales de Microsoft Azure, sitios web o una combinación de ambos. Las máquinas virtuales que proporcionan servicios web pueden hospedar contenido mediante cualquier sistema operativo de invitado de distribución de Microsoft Windows o Linux en Microsoft Azure.


## Información general del patrón de arquitectura 

Este documento describe un patrón para proporcionar acceso a servicios web o al contenido del servidor web en varias zonas geográficas para los fines de disponibilidad y redundancia. Los servicios críticos se ilustran a continuación sin prestar atención a las restricciones de plataforma web o la metodología de desarrollo dentro del propio servicio web. Hay dos variaciones a este patrón: una que hospeda el contenido web o los servicios en máquinas virtuales (usando sistemas operativos y familias admitidas por Azure) y otra que usa Sitios Web Azure. El siguiente diagrama es una ilustración simple de los servicios relevantes y de cómo se utilizan como parte de este patrón usando el ejemplo de máquinas virtuales.

##  Recursos adicionales
[Nivel web de carga equilibrada global (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

## Otras referencias
[Arquitectura CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Nivel de datos de carga equilibrada](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Redes híbridas](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Nivel de Búsqueda de Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[Nivel de procesamiento por lotes](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=Oct15_HO4-->
