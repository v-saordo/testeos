<properties 
   pageTitle="Nivel de procesamiento por lotes fuera del sitio (patrones de arquitectura de Azure)" 
   description="El patrón Nivel de procesamiento por lotes fuera del sitio es parte del área Infraestructura, que se describe ampliamente en el documento de arquitectura de CPIF." 
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

# Nivel de procesamiento por lotes fuera del sitio (patrones de arquitectura de Azure)

El [Marco de integración de plataformas en la nube](azure-architectures-cpif-overview.md) proporciona instrucciones de integración de carga de trabajo para incorporar aplicaciones en una solución en la nube de Microsoft.

CPIF describe cómo las organizaciones, los clientes y los socios deben diseñar e implementar cargas de trabajo para la nube utilizando la plataforma en la nube híbrida y las capacidades de administración de Azure, System Center y Windows Server.

El patrón **Nivel de procesamiento por lotes fuera del sitio** es parte del área **Infraestructura**, que se describe ampliamente en el documento de arquitectura de CPIF.

##  Nivel de procesamiento por lotes fuera del sitio

El patrón de diseño Nivel de procesamiento por lotes fuera del sitio detalla las características y servicios de Azure necesarios para ofrecer procesamiento de datos de back-end que es tanto tolerante a errores como escalable. Estos servicios se realizan como roles de trabajo en servicios en la nube en Azure, que actualmente se pueden implementar en cualquier centro de datos de Azure.

Las cargas de trabajo de procesamiento por lotes son únicas, ya que normalmente proporcionan poca o ninguna interfaz de usuario. Un ejemplo de este tipo de carga de trabajo local sería un servicio de Windows que se ejecuta en Windows Server. Al considerar este tipo de carga de trabajo en un entorno en la nube, sería una pérdida de tiempo implementar un servidor completo para ejecutar una carga de trabajo, cuando lo que realmente se necesita es cálculo, almacenamiento y conectividad de red. El rol de trabajo es la implementación de todo esto en Azure.

Por definición, un trabajo de procesamiento por lotes que se ejecuta en Azure es una carga de trabajo que se conecta a un recurso, proporciona alguna lógica de negocios (cálculo) y ofrece alguna salida. Los recursos de entrada y salida son definidos por el usuario y pueden abarcar desde archivos planos, blobs en almacenamiento de blobs de Azure, una base de datos NoSQL o bases de datos relacionales.

La lógica de negocios se implementa en un rol de trabajo de Azure, normalmente mediante la definición de la lógica de negocios necesaria en una biblioteca .NET. Aunque la implementación de un rol de trabajo en Azure es una operación sencilla, la implementación de un rol de trabajo que sea tolerante a errores y escalable requiere un diseño que tiene en cuenta cómo se ejecuta el servicio y se mantiene dentro de Azure. Este patrón detallará el diseño, que considera estos requisitos y describe cómo se pueden implementar.

## Información general del patrón de arquitectura 

Este documento describe un patrón de procesamiento por lotes fuera del sitio mediante instancias de rol de trabajo contenidas en un servicio en la nube en Azure. A continuación se muestran los componentes críticos para este diseño. Este diagrama ilustra las instancias mínimas necesarias para conseguir tolerancia a errores. Para aumentar el rendimiento del servicio se pueden implementar instancias adicionales. Además, el escalado automático puede habilitarse para ayudar a escalar las instancias por tiempo o métricas de servidor adicionales.

##  Recursos adicionales
[Nivel de procesamiento por lotes (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

## Otras referencias
[Arquitectura CPIF](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

[Nivel web de carga equilibrada global](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Nivel de datos de carga equilibrada](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Redes híbridas](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Nivel de Búsqueda de Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

<!---HONumber=Oct15_HO4-->