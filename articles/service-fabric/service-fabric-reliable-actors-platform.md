<properties
   pageTitle="Reliable Actors en Service Fabric | Microsoft Azure"
   description="Describe cómo Reliable Actors usa las características de la plataforma Service Fabric y se abarcan conceptos desde el punto de vista de los desarrolladores de actores."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="abhisram"/>

# Uso de la plataforma Service Fabric por parte de actores confiables

Los actores usan el modelo de aplicación de Azure Service Fabric para administrar el ciclo de vida de la aplicación. Cada tipo de actor se asigna a un [tipo de servicio](service-fabric-application-model.md#describe-a-service) de Service Fabric. El código de actor se [empaqueta](service-fabric-application-model.md#package-an-application) como una aplicación de Service Fabric y se [implementa](service-fabric-deploy-remove-applications.md#deploy-an-application) en el clúster.

## Concepto del modelo de aplicación de ejemplo para actores

Veamos el ejemplo de un proyecto de actor [creado con Visual Studio](service-fabric-reliable-actors-get-started.md) para ilustrar algunos de los conceptos anteriores.

El manifiesto de aplicación, el manifiesto de servicio y el archivo de configuración Settings.xml se incluyen en el proyecto del servicio de actor cuando la solución se crea en Visual Studio. Esto se muestra en la captura de pantalla siguiente.

![Proyecto creado mediante Visual Studio][1]

Para encontrar el tipo de la aplicación y la versión de la misma en la que se empaqueta el actor, consulte el manifiesto de aplicación incluido en el proyecto para el servicio de actor. El siguiente fragmento de un manifiesto de aplicación es un ejemplo de esto.

~~~
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                     ApplicationTypeName="VoiceMailBoxApplication"
                     ApplicationTypeVersion="1.0.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric">
~~~

Para encontrar el tipo de servicio al que se asigna el tipo de actor, consulte el manifiesto de servicio incluido en el proyecto para el servicio de actor. El siguiente fragmento de un manifiesto de servicio es un ejemplo de esto.

~~~
<StatefulServiceType ServiceTypeName="VoiceMailBoxActorServiceType" HasPersistedState="true">
~~~

Cuando el paquete de aplicación se crea mediante Visual Studio, los registros de la ventana de resultados de la compilación indican la ubicación de dicho paquete. Por ejemplo:

    -------- Package started: Project: VoiceMailBoxApplication, Configuration: Debug x64 ------
    VoiceMailBoxApplication -> C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug

A continuación, se muestra una lista parcial de la ubicación anterior (se omite el listado completo para mayor brevedad).

    C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug>tree /f
    Folder PATH listing
    Volume serial number is 303F-6F91
    C:.
    │   ApplicationManifest.xml
    │
    ├───VoiceMailBoxActorServicePkg
    │   │   ServiceManifest.xml
    │   │
    │   ├───Code
    │   │   │   Microsoft.ServiceFabric.Actors.dll
    │   │   │       :
    │   │   │   Microsoft.ServiceFabric.Services.dll
    │   │   │   ServiceFabricServiceModel.dll
    │   │   │   System.Fabric.Common.Internal.dll
    │   │   │   System.Fabric.Common.Internal.Strings.dll
    │   │   │   VoiceMailBox.exe
    │   │   │   VoiceMailBox.exe.config
    │   │   │   VoiceMailBox.Interfaces.dll
    │   │   │
    │   │   └───es-ES
    │   │           System.Fabric.Common.Internal.Strings.resources.dll
    │   │
    │   └───Config
    │           Settings.xml
    │
    └───VoicemailBoxWebServicePkg
        │   ServiceManifest.xml
        │
        └───Code
            │   Microsoft.Owin.dll
            │       :
            │   Microsoft.ServiceFabric.Services.dll
            │       :
            │   System.Fabric.Common.Internal.dll
            │   System.Fabric.Common.Internal.Strings.dll
            │       :
            │   VoiceMailBox.Interfaces.dll
            │   VoicemailBoxWebService.exe
            │   VoicemailBoxWebService.exe.config
            │
            └───es-ES
                    System.Fabric.Common.Internal.Strings.resources.dll

En el listado anterior se muestran los ensamblados que implementan el actor VoicemailBox incluido en el paquete de código dentro del paquete de servicio que, a su vez, se incluye en el paquete de aplicación.

La administración posterior (es decir, las actualizaciones y la eliminación final) de la aplicación también se realiza mediante los mecanismos de administración de aplicaciones de Service Fabric. Para obtener más información, consulte los temas sobre el [modelado de la aplicación](service-fabric-application-model.md), la [implementación y eliminación de la aplicación](service-fabric-deploy-remove-applications.md) y la [actualización de la aplicación](service-fabric-application-upgrade.md).

## Escalabilidad de los servicios de actor
Los administradores de clústeres pueden crear uno o varios servicios de actor de cada tipo de servicio en el clúster. Cada uno de esos servicios de actor puede tener una o más particiones (de igual forma que cualquier otro servicio de Service Fabric). La capacidad de crear varios servicios de un tipo de servicio (que se asigna a un tipo de actor) y la capacidad de crear varias particiones para un servicio permiten que la aplicación de actor se escale. Consulte el artículo sobre [escalabilidad](service-fabric-concepts-scalability.md) para obtener más información.

> [AZURE.NOTE]Los servicios de actores sin estado necesitan tener un recuento de [instancias](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) de 1. No se admite tener más de una instancia de un servicio de actores sin estado en una partición. Por lo tanto, los servicios de actores sin estado no tienen la posibilidad de aumentar el recuento de instancias para lograr escalabilidad. Deben usar las opciones de escalabilidad que se describen en el [artículo sobre escalabilidad](service-fabric-concepts-scalability.md).

## Conceptos de partición de Service Fabric para los actores
El identificador de un actor se asigna a una partición de un servicio de actor. El actor se crea dentro de la partición a la que se asigna su identificador de actor. Cuando se crea un actor, el tiempo de ejecución de los actores escribe un [evento EventSource](service-fabric-reliable-actors-diagnostics.md#eventsource-events) que indica la partición en que se creó. A continuación, se muestra un ejemplo de este evento que indica que un actor con el identificador `-5349766044453424161` se creó en la partición `b6afef61-be9a-4492-8358-8f473e5d2487` del servicio `fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService`, aplicación `fabric:/VoicemailBoxAdvancedApplication`.

    {
      "Timestamp": "2015-04-26T10:12:20.2485941-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -5349766044453424161, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: 0583c745-1bed-43b2-9545-29d7e3448156.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-5349766044453424161",
        "isStateful": "True",
        "replicaOrInstanceId": "130906628008120392",
        "partitionId": "b6afef61-be9a-4492-8358-8f473e5d2487",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

Otro actor con el identificador `-4952641569324299627` se creó en una partición distinta (`5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`) del mismo servicio, como indica el evento siguiente.

    {
      "Timestamp": "2015-04-26T15:06:56.93882-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -4952641569324299627, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: c146fe53-16d7-4d96-bac6-ef54613808ff.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-4952641569324299627",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "5405d449-2da6-4d9a-ad75-0ec7d65d1a2a",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

> [AZURE.NOTE]Algunos campos de los eventos anteriores se omiten para mayor brevedad.

El identificador de partición puede usarse para obtener otra información sobre la partición. Por ejemplo, la herramienta [Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md) se puede usar para ver información sobre la partición, y sobre el servicio y la aplicación a los que pertenece. En la captura de pantalla siguiente se muestra información sobre la partición `5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`, que contiene el actor con el identificador `-4952641569324299627` en el ejemplo anterior.

![Información sobre una partición en el Explorador de Service Fabric][3]

Los actores pueden obtener mediante programación el identificador de partición, el nombre del servicio, el nombre de la aplicación y otra información específica de la plataforma Service Fabric a través de `Host.ActivationContext` y los miembros de la clase base `Host.StatelessServiceInitialization` o `Host.StatefulServiceInitializationParameters` de los que se deriva el tipo de actor. El fragmento de código siguiente muestra un ejemplo.

```csharp
public void ActorMessage(StatefulActorBase actor, string message, params object[] args)
{
    if (this.IsEnabled())
    {
        string finalMessage = string.Format(message, args);
        ActorMessage(
            actor.GetType().ToString(),
            actor.Id.ToString(),
            actor.ActorService.ServiceInitializationParameters.CodePackageActivationContext.ApplicationTypeName,
            actor.ActorService.ServiceInitializationParameters.CodePackageActivationContext.ApplicationName,
            actor.ActorService.ServiceInitializationParameters.ServiceTypeName,
            actor.ActorService.ServiceInitializationParameters.ServiceName.ToString(),
            actor.ActorService.ServiceInitializationParameters.PartitionId,
            actor.ActorService.ServiceInitializationParameters.ReplicaId,
            FabricRuntime.GetNodeContext().NodeName,
            finalMessage);
    }
}
```

### Conceptos de particiones de Service Fabric para actores sin estado
Los actores sin estado se crean dentro de una partición de un servicio sin estado de Service Fabric. El identificador de actor determina la partición en la que se crea el actor. El recuento de [instancias](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services) de un servicio de actores sin estado debe ser 1. No es posible cambiar el recuento de instancias por cualquier otro valor. Por lo tanto, el actor se crea dentro de la instancia de servicio único dentro de la partición.

> [AZURE.TIP]El tiempo de ejecución de los actores de Fabric emite algunos [eventos relacionados con instancias de actores sin estado](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateless-actor-instances). Son útiles para la supervisión del rendimiento y los diagnósticos.

Cuando se crea un actor sin estado, el tiempo de ejecución de los actores escribe un [evento EventSource](service-fabric-reliable-actors-diagnostics.md#eventsource-events) que indica la partición y la instancia en que se creó. A continuación, se muestra un ejemplo de este evento. Indica que un actor con el identificador `abc` se creó dentro de la instancia `130745709600495974` de la partición `8c828833-ccf1-4e21-b99d-03b14d4face3`, del servicio `fabric:/HelloWorldApplication/HelloWorldActorService` y de la aplicación `fabric:/HelloWorldApplication`.

    {
      "Timestamp": "2015-04-26T18:17:46.1453113-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: HelloWorld.HelloWorld, actor ID: abc, stateful: False, replica/instance ID: 130,745,709,600,495,974, partition ID: 8c828833-ccf1-4e21-b99d-03b14d4face3.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "HelloWorld.HelloWorld",
        "actorId": "abc",
        "isStateful": "False",
        "replicaOrInstanceId": "130745709600495974",
        "partitionId": "8c828833-ccf1-4e21-b99d-03b14d4face3",
        "serviceName": "fabric:/HelloWorldApplication/HelloWorldActorService",
        "applicationName": "fabric:/HelloWorldApplication",
      }
    }

> [AZURE.NOTE]Algunos campos del evento anterior se omiten para mayor brevedad.

### Conceptos de particiones de Service Fabric para actores con estado
Los actores con estado se crean dentro de una partición del servicio con estado de Service Fabric. El identificador de actor determina la partición en la que se crea el actor. Cada partición del servicio puede tener una o más [réplicas](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services) que se colocan en nodos distintos del clúster. Al disponer de varias réplicas, se ofrece confiabilidad para el estado del actor. El Administrador de recursos de Azure optimiza la selección de ubicación según los dominios de error y actualización disponibles en el clúster. Dos réplicas de la misma partición nunca se colocan en el mismo nodo. Los actores siempre se crean en la réplica principal de la partición a la que se asigna su identificador de actor.

> [AZURE.TIP]El tiempo de ejecución de actores de Fabric emite algunos [eventos relacionados con las réplicas de actores con estado](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateful-actor-replicas). Son útiles para la supervisión del rendimiento y los diagnósticos.

Recuerde que en el [ejemplo de VoiceMailBoxActor que se explicó anteriormente](#service-fabric-partition-concepts-for-actors), el actor con el identificador `-4952641569324299627` se creó en la partición `5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`. El evento EventSource de ese ejemplo también indicaba que el actor se creó en la réplica `130745418574851853` de dicha partición. Esta era la réplica principal de esa partición en el momento en que se creó el actor. La captura de pantalla siguiente del Explorador de Service Fabric lo confirma.

![Una réplica principal en el Explorador de Service Fabric][4]

## Opciones de proveedores de estado de actor
En el runtime de Actors se incluyen algunos proveedores de estado de actor predeterminados. Para elegir un proveedor de estado adecuado para un servicio de actor, es necesario comprender la forma en que los proveedores de estado usan las características subyacentes de la plataforma de Service Fabric para que el estado de actor esté altamente disponible.

De manera predeterminada, un actor con estado usa el proveedor de estado de actor del almacén de clave-valor. Este proveedor de estado se integra en el almacén de pares clave-valor distribuido que ofrece la plataforma Service Fabric. El estado se guarda de forma duradera en el disco local del nodo que hospeda la [réplica](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services) principal. Además, también se replica y se guarda de forma duradera en los discos locales de los nodos que hospedan las réplicas secundarias. La operación para guardar el estado se completa solo cuando un cuórum de réplicas confirma el estado en sus discos locales. El almacén de clave-valor cuenta con funciones avanzadas para detectar incoherencias, como un progreso falso, y las corrige automáticamente.

El runtime de Actors también incluye `VolatileActorStateProvider`. Este proveedor de estado replica el estado en las réplicas (copias del estado), pero el estado permanece en memoria en cada réplica. Si una réplica deja de funcionar y vuelve a activarse, se vuelve a generar su estado a partir de la otra réplica. Sin embargo, si todas las réplicas dejan de funcionar simultáneamente, los datos de estado se perderán. Por lo tanto, este proveedor de estado es adecuado para las aplicaciones donde los datos pueden sobrevivir a los errores de algunas réplicas y a las conmutaciones por error planeadas, como las actualizaciones. Si se pierden todas las réplicas, los datos deben volver a crearse mediante mecanismos externos a Service Fabric. El actor con estado se puede configurar para que use el proveedor de estado de actor volátil si se agrega el atributo `VolatileActorStateProvider` a la clase de actor o un atributo de nivel de ensamblado.

En el fragmento de código siguiente se muestra cómo cambiar todos los actores en el ensamblado que no tiene un atributo de proveedor de estado explícito para usar `VolatileActorStateProvider`.

```csharp
[assembly:Microsoft.ServiceFabric.Actors.VolatileActorStateProvider]
```

En el fragmento de código siguiente se muestra cómo cambiar el proveedor de estado de un tipo de actor determinado, `VoicemailBox` en este caso, para que sea `VolatileActorStateProvider`.

```csharp
[VolatileActorStateProvider]
public class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

Tenga en cuenta que, al cambiar el proveedor de estado, debe volver a crearse el servicio de actor. Los proveedores de estado no se pueden cambiar como parte de la actualización de la aplicación.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/manifests-in-vs-solution.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png

<!---HONumber=AcomDC_1217_2015-->