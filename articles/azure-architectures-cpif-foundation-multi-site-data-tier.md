<properties 
   pageTitle="Nivel de datos de varios sitios (patrones de arquitectura de Azure)" 
   description="El patrón Nivel de datos de varios sitios es parte del área Fundación, que se describe ampliamente en el documento de arquitectura de CPIF." 
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

# Nivel de datos de varios sitios (patrones de arquitectura de Azure)

El [Marco de integración de plataformas en la nube](azure-architectures-cpif-overview.md) proporciona instrucciones de integración de carga de trabajo para incorporar aplicaciones en una solución en la nube de Microsoft.

CPIF describe cómo las organizaciones, los clientes y los socios deben diseñar e implementar cargas de trabajo para la nube utilizando la plataforma en la nube híbrida y las capacidades de administración de Azure, System Center y Windows Server.

El patrón **Nivel de datos de varios sitios** es parte del área **Fundación**, que se describe ampliamente en el documento de arquitectura de CPIF.

## Nivel de datos de varios sitios

El patrón de diseño Nivel de datos de varios sitios detalla las características y los servicios de Azure necesarios para prestar servicios de nivel de datos que pueden proporcionar un rendimiento predecible y alta disponibilidad a través de fronteras geográficas. Para los fines de este patrón de diseño, se define un nivel de datos como un nivel de servicio que proporciona servicios de plataforma de datos tradicionales de forma aislada o como parte de una aplicación de varios niveles. Dentro de este patrón, el equilibrio de carga del nivel de datos se proporciona tanto localmente dentro de la región como entre regiones.

Presentada con SQL Server 2012, Grupos de disponibilidad AlwaysOn es una característica de alta disponibilidad y recuperación ante desastres que es totalmente compatible con los servicios de infraestructura de Azure. En el artículo Grupos de disponibilidad AlwaysOn puede encontrar información detallada y el anuncio de compatibilidad oficial para Grupos de disponibilidad AlwaysOn en el servicio de infraestructura de Microsoft Azure.

Este documento proporciona información general de la arquitectura de un nivel de datos de varios sitios en Azure utilizando Grupos de disponibilidad AlwaysOn de SQL. Con un nodo secundario de solo lectura opcional en un centro de datos de Azure para funcionalidad adicional y recuperación ante desastres. El uso de AlwaysOn de SQL en Azure proporciona un nivel de datos de alta disponibilidad que puede ser consumido por los niveles de aplicación o web.

Aunque este documento se centra en patrones y prácticas de arquitectura, puede encontrar instrucciones de implementación completas en los tutoriales oficiales, que describen la configuración de Grupos de disponibilidad AlwaysOn en Azure y la configuración del agente de escucha de Grupos de disponibilidad AlwaysOn.

## Información general del patrón de arquitectura 

Este documento describe un patrón para proporcionar acceso a contenido de Microsoft SQL Server en varias zonas geográficas para los fines de disponibilidad y redundancia. A continuación se ilustran los servicios críticos sin prestar atención al nivel de aplicación o web que obtendrá acceso a los datos por sí mismo. El siguiente diagrama es una ilustración simple de los servicios relevantes y de cómo se utilizan como parte de este patrón.

Cada una de las áreas de servicio principales se describen con más detalle en el siguiente diagrama.
 
![Parte de etiquetas en hojas de recursos y grupos de recursos](./media/azure-architectures-cpif-foundation-multi-site-data-tier/overview.png)

##  Recursos adicionales
[Nivel de datos de carga equilibrada (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

## Otras referencias
[Arquitectura CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Nivel web de carga equilibrada global](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Redes híbridas](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Nivel de Búsqueda de Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

[Nivel de procesamiento por lotes](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

<!---HONumber=Oct15_HO4-->