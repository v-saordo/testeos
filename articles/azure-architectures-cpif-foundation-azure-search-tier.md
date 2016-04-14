<properties 
   pageTitle="Nivel de Búsqueda de Azure (patrones de arquitectura de Azure)" 
   description="El patrón Nivel de Búsqueda de Azure es parte del área Fundación, que se describe ampliamente en el documento de arquitectura de CPIF" 
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

# Nivel de Búsqueda de Azure (patrones de arquitectura de Azure)

El [Marco de integración de plataformas en la nube](azure-architectures-cpif-overview.md) proporciona instrucciones de integración de carga de trabajo para incorporar aplicaciones en una solución en la nube de Microsoft.

CPIF describe cómo las organizaciones, los clientes y los socios deben diseñar e implementar cargas de trabajo para la nube utilizando la plataforma en la nube híbrida y las capacidades de administración de Azure, System Center y Windows Server.

El patrón **Nivel de Búsqueda de Azure** es parte del área **Fundación**, que se describe ampliamente en el documento de arquitectura de CPIF.

##  Nivel de Búsqueda de Azure

El patrón de diseño Nivel de Búsqueda de Azure detalla las características y los servicios de Azure necesarios para ofrecer servicios de búsqueda que pueden proporcionar un rendimiento predecible y alta disponibilidad a través de fronteras geográficas y proporciona un patrón de arquitectura para usar Búsqueda de Azure en la entrega de una solución de búsqueda. Búsqueda de Azure es un servicio "buscar como servicio" integrado en Microsoft Azure que permite a los desarrolladores incorporar capacidades de búsqueda en aplicaciones sin tener que implementar, administrar ni mantener servicios de infraestructura para proporcionar esta capacidad a las aplicaciones. La finalidad de este patrón es proporcionar una solución repetible pensada para usarse en distintas situaciones y diseños.

## Información general del patrón de arquitectura 

Este documento describe un patrón de núcleo para usar Búsqueda de Azure con dos variaciones del núcleo para mostrar el ámbito de la arquitectura del servicio. El patrón del núcleo consta de Búsqueda de Azure y servicios de Azure circundantes y está pensado para proporcionar instrucciones para crear diseños de un extremo a otro. Las variaciones del patrón, específicamente el servicio compartido y los patrones de simultaneidad, también se incluyen en esta sección para proporcionar instrucciones basadas en distintos requisitos, acuerdos de nivel de servicio (SLA) y otras condiciones específicas.

##  Recursos adicionales
[Nivel de Búsqueda de Azure (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

## Otras referencias
[Arquitectura CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Nivel web de carga equilibrada global](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Nivel de datos de carga equilibrada](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Redes híbridas](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Nivel de procesamiento por lotes](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=Oct15_HO4-->