<properties
   pageTitle="Disponibilidad de los servicios de Service Fabric | Microsoft Azure"
   description="Describe la detección de errores, la conmutación por error y la recuperación para los servicios"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="aprameyr"/>

# Disponibilidad de los servicios de Service Fabric
Los servicios de Service Fabric de Azure pueden ser con o sin estado. En este artículo se ofrece una visión general de cómo Service Fabric mantiene la disponibilidad de un servicio en caso de errores.

## Disponibilidad de los servicios sin estado de Service Fabric
Un servicio sin estado es un servicio de aplicación que no tiene ningún [estado persistente local](service-fabric-concepts-state.md).

La creación de un servicio sin estado requiere la definición de un recuento de instancias, que es el número de instancias del servicio sin estado que se debe estar ejecutando en el clúster. Este es el número de copias de la lógica de aplicación de la que se crearán instancias en el clúster. El aumento del número de instancias es la manera recomendada de escalar verticalmente un servicio sin estado.

Cuando se detecta un error en cualquier instancia de un servicio sin estado, se crea una nueva instancia en algún otro nodo elegible del clúster.

## Disponibilidad de los servicios con estado de Service Fabric
Un servicio con estado tiene algún estado asociado a él. En Service Fabric, un servicio con estado se modela como un conjunto de réplicas. Cada réplica es una instancia del código del servicio que tiene una copia del estado. Las operaciones de lectura y escritura se realizan en una réplica (denominada la principal). Los cambios al estado debido a las operaciones de escritura se *replican* en varias otras réplicas (denominadas secundarias activas). La combinación de las réplicas principal y secundaria activa es el conjunto de réplicas del servicio.

Solo puede haber una réplica principal que atienda solicitudes de lectura y escritura, pero puede haber varias réplicas secundarias activas. El número de réplicas secundarias activas es configurable y un mayor número de réplicas para tolerar un mayor número de errores de hardware y software simultáneos.

En el caso de un error (cuando la réplica principal deja de funcionar), Service Fabric convierte a una de las réplicas secundarias activas en la nueva réplica principal. Esta réplica secundaria activa ya tiene la versión actualizada del estado (a través de la *replicación*) y puede continuar procesando más operaciones de lectura y escritura.

Este concepto de una réplica como secundaria activa o principal se conoce como el rol de réplica.

### Roles de réplica
El rol de una réplica se usa para administrar el ciclo de vida del estado que se está administrando por esa réplica. Una réplica cuyo rol sea solicitudes de lectura de servicios principales. También ofrece solicitudes de escritura al actualizar su estado y replicar los cambios en las secundarias activas en su conjunto de réplicas. El rol de una secundaria activa es recibir cambios de estado que la réplica principal ha replicado y de actualizar su vista del estado.

>[AZURE.NOTE] Como los modelos de programación de nivel superior como el [marco de Reliable Actors](service-fabric-reliable-actors-introduction.md) abstraen del concepto de rol de réplica del desarrollador.

## Pasos siguientes

Para información sobre los conceptos de Service Fabric, vea lo siguiente:

- [Escalabilidad de servicios de Service Fabric](service-fabric-concepts-scalability.md)

- [Creación de particiones de los servicios de Service Fabric](service-fabric-concepts-partitioning.md)

- [Definición y administración del estado](service-fabric-concepts-state.md)

<!---HONumber=AcomDC_0128_2016-->