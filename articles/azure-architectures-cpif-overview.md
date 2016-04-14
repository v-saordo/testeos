<properties 
   pageTitle="Marco de integración de plataformas en la nube | Microsoft Azure" 
   description="El Marco de integración de plataformas en la nube proporciona instrucciones de integración de carga de trabajo para incorporar aplicaciones en una solución en la nube de Microsoft que consta de patrones de arquitectura para Microsoft Azure" 
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


# Marco de integración de plataformas en la nube (patrones de arquitectura de Azure)

El Marco de integración de plataformas en la nube proporciona instrucciones de integración de carga de trabajo para incorporar aplicaciones en una solución en la nube de Microsoft.

CPIF describe cómo las organizaciones, los clientes y los socios deben diseñar e implementar cargas de trabajo para la nube utilizando la plataforma en la nube híbrida y las capacidades de administración de Azure, System Center y Windows Server. Los dominios CPIF se han descompuesto en las siguientes funciones:

![Parte de etiquetas en hojas de recursos y grupos de recursos](./media/azure-architecture-cpif-overview/overview.png)

##  Arquitectura CPIF

Tanto los entornos en la nube pública como privada proporcionan elementos comunes para admitir la ejecución de cargas de trabajo complejas. Aunque estas arquitecturas se entienden relativamente bien en entornos físicos y virtualizados locales tradicionales, las construcciones que se encuentran dentro de Microsoft Azure necesitan una planeación adicional para racionalizar las capacidades de plataforma e infraestructura que se encuentran dentro de entornos en la nube pública. Para admitir el desarrollo de una aplicación o servicio hospedado en Azure, se requiere una serie de patrones que describen los distintos componentes necesarios para crear una solución de carga de trabajo determinada.

Estos patrones de arquitectura se dividen en las siguientes categorías:

- **Infraestructura** – Microsoft Azure es una solución de infraestructura y plataforma como servicio que se compone de varios servicios y capacidades subyacentes. Estos servicios se pueden descomponer en gran medida en servicios de cálculo, almacenamiento y red. Sn embargo, hay varias funcionalidades que pueden estar fuera de estas definiciones. Los patrones de infraestructura detallan una determinada área funcional de Microsoft Azure que es necesaria para proporcionar un servicio dado a una o varias soluciones hospedadas dentro de una determinada suscripción de Azure. 
- **Fundación** – Al crear una aplicación de varios niveles o un servicio dentro de Azure, se deben usar varios componentes conjuntamente para proporcionar un entorno de hospedaje adecuado. Los patrones de fundación se componen de uno o más servicios de Microsoft Azure para admitir un determinado nivel de funcionalidad dentro de una aplicación. Esto puede requerir el uso de uno o varios componentes descritos en los patrones de infraestructura descritos anteriormente. Por ejemplo, el nivel de presentación de una aplicación de varios niveles requiere capacidades de cálculo, red y almacenamiento dentro de Azure para ser funcional. Los patrones de fundación están pensados para estar compuestos con otros patrones como parte de una solución dada.
- **Solución** – Los patrones de solución se componen de patrones de infraestructura y/o fundación para representar una aplicación o servicio que se está desarrollando. Está previsto que las soluciones complejas no se desarrollen independientemente de otros patrones. En su lugar, deben utilizar los componentes y las interfaces definidos en cada una de las categorías de patrón descritas anteriormente.    

## Conceptos del patrón de arquitectura de Azure

Para admitir el desarrollo de arquitecturas de soluciones dentro de Azure, se proporcionan una serie de instrucciones de patrones para escenarios comunes. Aunque estas instrucciones de escenario se construirán con el tiempo, los patrones previstos incluyen:

- Nivel web de carga equilibrada global 
- Nivel de datos de carga equilibrada
- Nivel de procesamiento por lotes
- Redes híbridas
- Búsqueda de Azure 

##  Recursos adicionales
[Arquitectura de CPIF (pdf)](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-bd1e434a)

## Otras referencias
[Nivel web de carga equilibrada global](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-2c3c663a)

[Nivel de datos de carga equilibrada](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-dfb09e41)

[Nivel de procesamiento por lotes](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-0bc3f8b1)

[Redes de Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-5e401f38)

[Búsqueda de Azure](https://gallery.technet.microsoft.com/Cloud-Platform-Integration-e581d65d)

<!---HONumber=Oct15_HO4-->