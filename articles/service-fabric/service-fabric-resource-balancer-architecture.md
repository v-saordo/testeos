<properties
   pageTitle="Arquitectura del equilibrador de recursos | Microsoft Azure"
   description="Información general de la arquitectura del equilibrador de recursos de Service Fabric"
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

# Información general de arquitectura de equilibrador de recursos

![Arquitectura del equilibrador de recursos][Image1]

El Equilibrador de recursos de Service Fabric de Azure consta de un solo servicio de equilibrio de recursos centralizado y de un componente que existe en todos los nodos. Se combinan conceptualmente con el Administrador de conmutación por error de Service Fabric y con el nodo de Service Fabric local, respectivamente.

El agente local es responsable de recopilar y almacenar en búfer los informes de carga de los servicios que se ejecutan en el nodo local, para enviarlos al servicio del equilibrador de recursos y también para los informes de errores y otros eventos al Administrador de conmutación por error y al equilibrador de recursos (1 y 2 anteriores). El Equilibrador de recursos y el Administrador de conmutación por error colaboran para responder a eventos de carga y otros eventos en el sistema. Estos eventos pueden incluir errores de réplica o nodo, cargar informes, y servicios y aplicaciones que se crean y eliminan.

Por ejemplo, cuando se produce un error en una réplica, el Administrador de conmutación por error solicita que el equilibrador de recursos sugiera una ubicación para el reemplazo, que se basa en los datos de carga de los distintos nodos. De forma similar, cuando se recibe una solicitud de servicio nuevo, el Administrador de conmutación por error solicita recomendaciones desde el equilibrador de recursos sobre dónde colocar ese servicio. En todos estos casos, el equilibrador de recursos responde con cambios a varias configuraciones de servicio (3). Estos cambios se aprueban a continuación mediante el Administrador de conmutación por error. En el ejemplo anterior, el Administrador de conmutación por error crea una nueva réplica en el nodo, lo que produce el mejor equilibrio general (4).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Características de equilibrio de recursos:

- [Describir el clúster](service-fabric-resource-balancer-cluster-description.md)
- [Describir servicios](service-fabric-resource-balancer-service-description.md)
- [Informe de carga dinámica](service-fabric-resource-balancer-dynamic-load-reporting.md)
- [Empaquetado de métrica proactivo](service-fabric-resource-balancer-proactive-metric-packing.md)
- [Porcentaje de búfer de nodo](service-fabric-resource-balancer-node-buffer-percentage.md)

[Image1]: media/service-fabric-resource-balancer-architecture/Service-Fabric-Resource-Balancer-Architecture.png

<!---HONumber=AcomDC_1223_2015-->