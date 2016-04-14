<properties
   pageTitle="Porcentaje de búfer de nodo | Microsoft Azure"
   description="Información general del rol del porcentaje de búfer de nodo en el equilibrador de recursos"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Información general sobre el porcentaje de búfer de nodo

Actualmente, los clientes pueden especificar límite de capacidad de nodo como una restricción que respeta el equilibrador de recursos según la prioridad de la restricción. Si la prioridad de la restricción de capacidad es alta (no se puede infringir la capacidad de los nodos), la conmutación por error podría no ser inmediata o que infringiera la capacidad de los nodos.

Pueden producirse problemas si los nodos con réplicas secundarias están cerca de la capacidad de nodo cuando un nodo con la réplica principal deja de funcionar. En ese caso, si la carga principal es mayor que la carga secundaria, la réplica secundaria no se puede promocionar inmediatamente sin que el nodo se sobrecargue o se copie la réplica.

Cuando se ejecute la lógica de empaquetado proactiva, un mayor número de los nodos del clúster estará cerca del límite de capacidad del nodo. Porcentaje de búfer de nodo es una característica que impide un aumento del tiempo de conmutación por error o una sobrecarga del nodo durante la conmutación por error, al permitir a los clientes especificar el porcentaje del nodo que se debe mantener libre. No se deben agregar réplicas de los nuevos servicios al espacio de búfer de nodo, pero el equilibrador de recursos debe ser capaz de usar la capacidad total del nodo (espacio de búfer de cuenta) para las conmutaciones por error y agregar las réplicas que falten.

Esta característica se puede usar incluso cuando la caractérista de empaquetado de métrica proactiva esté desactivada.

El ejemplo de código siguiente muestra que se configuran porcentajes de búfer de nodo de las métricas por cada métrica a través del elemento FabricSettings situado dentro del manifiesto de clúster.

``` xml
<FabricSettings>
  <Section Name=" NodeBufferPercentage">
    <Parameter Name="MetricName" Value="0.1"/>
  </Section>
</FabricSettings>

```

El valor 0,1 de la métrica con el nombre "MetricName" significa que el 10 por ciento de la capacidad de cada nodo de la métrica "MetricName" debe mantenerse libre.

Si no se especifica el valor en esta sección, se usará 0 como valor predeterminado.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Para más información: [Arquitectura de equilibrador de recursos](service-fabric-resource-balancer-architecture.md)

<!---HONumber=AcomDC_1223_2015-->