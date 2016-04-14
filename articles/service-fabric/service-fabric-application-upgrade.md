<properties
   pageTitle="Tutorial de actualización de aplicación de Service Fabric | Microsoft Azure"
   description="Este artículo proporciona una introducción a la actualización de una aplicación de Service Fabric, incluida la elección de los modos de actualización y las comprobaciones de estado."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/04/2016"
   ms.author="subramar"/>


# Actualización de la aplicación de Service Fabric

Una aplicación de Azure Service Fabric es una colección de servicios. Durante una actualización, Service Fabric compara el nuevo [manifiesto de aplicación](service-fabric-application-model.md#describe-an-application) con la versión anterior y determina qué servicios de la aplicación requieren actualizaciones. Para ello, Service Fabric compara los números de versión en los manifiestos de servicio con los números de versión en la versión anterior. Si un servicio no ha cambiado, no se actualiza.

## Información general de las actualizaciones graduales

En una actualización de la aplicación gradual, la actualización se realiza por etapas. En cada etapa, la actualización se aplica a un subconjunto de nodos del clúster, llamado dominio de actualización. Como resultado, la aplicación permanece disponible durante la actualización. Durante la actualización, el clúster puede contener una mezcla de las versiones anteriores y nuevas.

Por esa razón, las dos versiones deben ser compatibles con las versiones anteriores y nuevas. Si no son compatibles, el administrador de la aplicación es responsable del ensayo de una actualización multifase para mantener la disponibilidad. El administrador hace esto antes de actualizar la versión final, realizando una actualización con una versión intermedia de la aplicación que es compatible con la versión anterior.

Los dominios de actualización se especifican en el manifiesto de clúster al configurar el clúster. No debe asumir que los dominios de actualización recibirán actualizaciones en un orden concreto. Un dominio de actualización es una unidad lógica de implementación para una aplicación. Los dominios de actualización permiten que la disponibilidad de los servicios se mantenga alta durante una actualización.

Las actualizaciones no graduales son posibles si la actualización se aplica a todos los nodos del clúster, que es el caso cuando la aplicación tiene un solo dominio de actualización. Esto no se recomienda, ya que el servicio estará inactivo y no disponible en el momento de la actualización. Además, Azure no ofrece ninguna garantía cuando se configura un clúster con un solo dominio de actualización.

## Comprobaciones de estado durante las actualizaciones

Para realizar una actualización, las directivas de mantenimiento deben establecerse (o se pueden usar valores predeterminados). Una actualización se califica como correcta cuando se actualizan todos los dominios de actualización dentro de los tiempos de espera especificados, y dichos dominios se consideran correctos. Un dominio de actualización correcto significa que el dominio de actualización pasa todas las comprobaciones de estado especificadas en la directiva de mantenimiento. Por ejemplo, una directiva de mantenimiento puede exigir que todos los servicios dentro de una instancia de la aplicación tienen que ser *correctos*, tal como Service Fabric define el estado correcto.

Las directivas de mantenimiento y comprobaciones de estado durante la actualización de Service Fabric son independientes de los servicios y aplicaciones. Es decir, no se realiza ninguna prueba específica del servicio. Por ejemplo, su servicio puede tener un requisito de rendimiento mínimo. Sin embargo, Service Fabric no tiene la información para comprobarlo y, por tanto, no comprobará el rendimiento como se define para su aplicación. Consulte el artículo [Introducción a la supervisión del estado de Service Fabric](service-fabric-health-introduction.md) para encontrar información sobre las comprobaciones que se llevan a cabo. Las comprobaciones que se producen durante una actualización incluyen pruebas para saber si el paquete de aplicación se copió correctamente, si se inició la instancia y así sucesivamente.

El estado de la aplicación es una agregación de las entidades secundarias de la aplicación. En resumen, Service Fabric evalúa el estado de la aplicación a través del estado notificado en la aplicación. También evalúa el estado de todos los servicios de la aplicación de este modo. Service Fabric evalúa aún más el estado de los servicios de aplicación al agregar el estado de sus elementos secundarios, como la réplica de servicio. Cuando se cumple la directiva de mantenimiento de la aplicación, se puede continuar con la actualización. Si se infringe la directiva de mantenimiento, se produce un error en la actualización de la aplicación.

## Modos de actualización

El modo en que se recomienda para las actualizaciones es el modo supervisado, que es el más utilizado. El modo supervisado realiza la actualización en un dominio de actualización y, si se superan todas las comprobaciones de estado (según la directiva especificada), pasa al siguiente dominio de actualización automáticamente. Si se produce un error en las comprobaciones de estado o los tiempos de espera llegan a su fin, la actualización se revierte para el dominio de actualización o el modo cambia a manual sin supervisión, si esa es la opción seleccionada en el momento de la actualización.

El modo manual sin supervisión requiere intervención manual tras cada actualización en un dominio de actualización para iniciar la actualización en el siguiente dominio de actualización. No se realiza ninguna comprobación de mantenimiento de Service Fabric. El administrador realiza las comprobaciones de estado antes de iniciar la actualización en el siguiente dominio de actualización.

## Diagrama de flujo de actualización de la aplicación

El siguiente diagrama de flujo ayuda a entender el proceso de actualización de una aplicación de Service Fabric. En concreto, el flujo describe cómo los tiempos de espera, incluidos *HealthCheckStableDuration*, *HealthCheckRetryTimeout* y *UpgradeHealthCheckInterval* ayudan a controlar cuándo se considera un éxito o un fracaso la actualización en un dominio de actualización.

![El proceso de actualización de una aplicación de Service Fabric][image]


## Pasos siguientes

El procedimiento de [actualización de aplicaciones usando Visual Studio](service-fabric-application-upgrade-tutorial.md) ofrece información para actualizar una aplicación mediante Visual Studio.

El procedimiento de [actualización de aplicaciones usando Powershell](service-fabric-application-upgrade-tutorial-powershell.md) ofrece información para actualizar una aplicación mediante PowerShell.

Puede controlar cómo se actualiza una aplicación usando [parámetros de actualización](service-fabric-application-upgrade-parameters.md).

Consiga que sus actualizaciones de aplicaciones sean compatibles aprendiendo a usar la [serialización de datos](service-fabric-application-upgrade-data-serialization.md).

Aprenda a usar funcionalidades avanzadas para actualizar una aplicación. Para ello, consulte los [temas avanzados](service-fabric-application-upgrade-advanced.md).

Solucione problemas habituales en las actualizaciones de aplicaciones consultando los pasos que figuran en [Solución de problemas de las actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md).
 


[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png

<!---HONumber=AcomDC_0211_2016-->