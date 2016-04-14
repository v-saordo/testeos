<properties
   pageTitle="Restricciones de posición de orquestación del clúster de Service Fabric | Microsoft Azure"
   description="Información general conceptual de las restricciones de posición en Service Fabric"
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
   ms.date="02/03/2016"
   ms.author="masnider"/>

# Información general de las restricciones de ubicación

Service Fabric de Azure permite a los desarrolladores restringir la colocación de réplicas de servicio en los nodos que cumplen ciertas condiciones. Estas condiciones se expresan a través de una expresión booleana que se evalúa con valores específicos de contexto de servicio adecuados.


## Capacidades
Al usar restricciones de posición, puede:

- Restringir tipos diferentes de servicios en distintos tipos de nodos mediante la definición de NodeProperties en los nodos.

- Enfoque las limitaciones de tal manera que se apliquen a las réplicas principal y secundaria de un servicio, o bien para que se apliquen solo a las réplicas principales.


## Conceptos clave
NodeProperty: una asignación definida por el sistema o el usuario desde una cadena a un valor, que puede variar en cada nodo, es decir, NodeName.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Para más información: [Escenarios de aplicación](service-fabric-application-scenarios.md).

<!---HONumber=AcomDC_0211_2016-->