<properties 
	pageTitle="Cálculo de las opciones de hospedaje proporcionadas por Azure" 
	description="Obtenga información sobre la forma en que Azure hospeda las opciones y cómo funcionan: Máquinas virtuales, Sitios web y Servicios en la nube, entre otros" 
	headerExpose="" 
	footerExpose="" 
	services="cloud-services,virtual-machines"
	authors="Thraka" 
	documentationCenter=""
	manager="timlt"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2015" 
	ms.author="adegeo;cephalin;kathydav"/>




# Cálculo de las opciones de hospedaje proporcionadas por Azure

Azure proporciona distintos modelos de hospedaje para ejecutar aplicaciones. Cada uno de ellos proporciona un conjunto diferente de servicios; por tanto, el que elija dependerá exactamente de lo que intente hacer. En este artículo se analizan las distintas opciones, se describe cada una de las tecnologías y se dan ejemplos de cuándo se podrían utilizar.

| Opciones de proceso | Público |
| ------------------ | --------   |
| [Servicio de aplicaciones] | Aplicaciones web, aplicaciones móviles, aplicaciones de API y aplicaciones lógicas escalables para cualquier dispositivo |
| [Servicios en la nube] | Aplicaciones en la nube de n niveles escalables y altamente disponibles con mayor control del sistema operativo |
| [Máquinas virtuales] | Máquinas virtuales Linux y Windows personalizadas con un control completo del sistema operativo |

[AZURE.INCLUDE [contenido](../../includes/app-service-choose-me-content.md)]

[AZURE.INCLUDE [contenido](../../includes/cloud-services-choose-me-content.md)]

[AZURE.INCLUDE [contenido](../../includes/virtual-machines-choose-me-content.md)]

## Otras opciones

Azure también ofrece otros modelos de hospedaje de proceso con fines más especializadas, como los siguientes:

* [Servicios móviles](/services/mobile-services/) Optimizado para proporcionar un back-end de nube para aplicaciones que se ejecutan en dispositivos móviles.
* [Lote](/services/batch/) Optimizado para el procesamiento de grandes volúmenes de tareas similares, idealmente cargas de trabajo que se prestan a ejecutarse como tareas paralelas en varios equipos.
* [HDInsight (Hadoop)](/services/hdinsight/) Optimizado para ejecutar trabajos de [MapReduce](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options/#hadoop) en clústeres de Hadoop. 

## ¿Cuál debo utilizar? Toma de decisión

Los tres modelos de hospedaje de proceso de Azure de propósito general le permiten crear aplicaciones escalables y confiables en la nube. Dada esta similitud esencial, ¿cuál de estos modelos debería utilizar?

El Servicio de aplicaciones es la opción más adecuada para la mayoría de las aplicaciones web. La implementación y la administración están integradas en la plataforma, los sitios pueden escalarse rápidamente para asumir altas cargas de tráfico y el equilibrio de carga y el administrador de tráfico incluidos ofrecen una gran disponibilidad. Los sitios actuales se pueden mover fácilmente al Servicio de aplicaciones con una [herramienta de migración en línea](https://www.migratetoazure.net/) y también se puede utilizar una aplicación de código abierto de la galería de aplicaciones web o crear un sitio nuevo con el marco y las herramientas preferidos. La característica [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) facilita la incorporación del procesamiento de trabajos en segundo plano a la aplicación o incluso la ejecución de una carga de trabajo de proceso que no sea una aplicación web.

Si necesita más control sobre el entorno del servidor web, como la posibilidad de tener acceso remoto al servidor o configurar las tareas de inicio del servidor, Servicios en la nube de Azure es por lo general la mejor opción.

Si tiene actualmente una aplicación que requeriría cambios sustanciales para ejecutarse en Sitios web Azure o Servicios en la nube de Azure, podría elegir Máquinas virtuales de Azure a fin de simplificar la migración a la nube. Sin embargo, se requiere más tiempo para configurar, proteger y mantener adecuadamente las Máquinas virtuales, además de mayores conocimientos informáticos, en comparación con Sitios web Azure y Servicios en la nube. Si está considerando la opción de Máquinas virtuales Azure, tenga en cuenta el esfuerzo constante de mantenimiento requerido para aplicar revisiones al entorno de Máquina virtual, así como para actualizarlo y administrarlo.

A veces, ninguna opción individualmente es correcta. En situaciones así, es perfectamente posible combinar opciones. Por ejemplo, imagine que va a compilar una aplicación en la que desea contar con los beneficios de administración de los roles web de Servicios en la nube, pero también necesita utilizar un SQL Server estándar hospedado en una máquina virtual por motivos de compatibilidad o rendimiento.

<!-- In this case, the best option is to combine compute hosting options, as the figure below shows.--

<a name="fig4"></a>
![07_CombineTechnologies][07_CombineTechnologies] 
 
**Figure: A single application can use multiple hosting options.**

As the figure illustrates, the Cloud Services VMs run in a separate cloud service from the Virtual Machines VMs. Still, the two can communicate quite efficiently, so building an app this way is sometimes the best choice.
[07_CombineTechnologies]: ./media/fundamentals-application-models/ExecModels_07_CombineTechnologies.png
!-->

[Servicio de aplicaciones]: #tellmeas
[Máquinas virtuales]: #tellmevm
[Servicios en la nube]: #tellmecs

## Pasos siguientes

* [Comparación de](../choose-web-site-cloud-service-vm/) Servicio de aplicaciones, Servicios en la nube y Máquinas virtuales
* Más información sobre [Servicio de aplicaciones](../app-service-web-overview.md)
* Más información sobre [Servicio en la nube](services/cloud-services/)
* Más información sobre [Máquinas virtuales](https://msdn.microsoft.com/library/azure/jj156143.aspx) 

<!---HONumber=Oct15_HO3-->