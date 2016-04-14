<properties
   pageTitle="Comunicación remota de servicio en Service Fabric | Microsoft Azure"
   description="La comunicación remota de Service Fabric permite a los clientes y servicios comunicarse con los servicios mediante la llamada a procedimiento remoto."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/12/2015"
   ms.author="bharatn@microsoft.com"/>

# Comunicación remota de servicio con Reliable Services
Para los servicios que no están vinculados a una pila o un protocolo de comunicación concretos, como WebAPI, Windows Communication Foundation (WCF) u otros, el marco de trabajo ofrece un mecanismo de comunicación remota para configurar de manera rápida y sencilla una llamada a procedimiento remoto para servicios.

## Configurar la comunicación remota en un servicio
La configuración de la comunicación remota para un servicio se realiza en dos sencillos pasos:

1. Cree una interfaz para que la implemente su servicio. Esta interfaz define los métodos que estarán disponibles para la llamada a procedimiento remoto en su servicio. Los métodos deben ser métodos asincrónicos que devuelven tareas. La interfaz debe implementar `Microsoft.ServiceFabric.Services.Remoting.IService` para indicar que el servicio tiene una interfaz de comunicación remota.
2. Use `Microsoft.ServiceFabric.Services.Remoting.Runtime.ServiceRemotingListener` en su servicio. Se trata de una implementación de `ICommunicationListener` que ofrece capacidades de comunicación remota.

Por ejemplo, este servicio de Hello World expone un método único para obtener "Hello World" por la llamada a procedimiento remoto:

```csharp
public interface IHelloWorldStateful : IService
{
    Task<string> GetHelloWorld();
}

internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[] { new ServiceReplicaListener(parameters => new ServiceRemotingListener<HelloWorldStateful>(parameters, this)) };
    }

    public Task<string> GetHelloWorld()
    {
        return Task.FromResult("Hello World!");
    }
}

```
> [AZURE.NOTE]Los argumentos y los tipos de valor devueltos en la interfaz de servicio pueden ser cualquier tipo simple, complejo o personalizado, pero deben ser serializables por .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx).


## Llamar a métodos de servicio remoto
La llamada a métodos en un servicio con la pila de comunicación remota se realiza mediante un proxy local al servicio a través de la clase `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy`. El método `ServiceProxy` crea un proxy local con la misma interfaz que implementa el servicio. Con ese proxy, puede llamar simplemente a los métodos en la interfaz de forma remota.


```csharp

IHelloWorldStateful helloWorldClient = ServiceProxy.Create<IHelloWorldStateful>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.GetHelloWorld();

```

El marco de trabajo de comunicación remota propaga las excepciones generadas en el servicio al cliente. Por lo tanto, la lógica de control de excepciones en el cliente mediante `ServiceProxy` puede controlar excepciones directamente que el servicio genera.

## Pasos siguientes

* [Web API con OWIN en Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Comunicación WCF con Reliable Services](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0107_2016-->