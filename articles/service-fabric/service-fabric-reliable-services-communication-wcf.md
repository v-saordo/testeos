<properties
   pageTitle="Pila comunicación de WCF de Reliable Services | Microsoft Azure"
   description="La pila de comunicación de WCF integrada en Service Fabric ofrece comunicación de WCF del servicio de cliente para Reliable Services."
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
   ms.date="11/17/2015"
   ms.author="bharatn@microsoft.com"/>

# Pila de comunicación basada en WCF de Reliable Services
El marco de Reliable Services permite a los autores de servicio elegir la pila de comunicación que desean usar para su servicio. Pueden conectar la pila de comunicaciones que deseen mediante la clase **ICommunicationListener** devuelta desde los métodos [CreateServiceReplicaListeners o CreateServiceInstanceListeners](service-fabric-reliable-service-communication.md). El marco proporciona una implementación de la pila de comunicación basada en Windows Communication Foundation (WCF) para los autores de servicio que desean usar la comunicación basada en WCF.

## Agente de escucha de comunicación WCF
La implementación específica de WCF de **ICommunicationListener** la proporciona la clase **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener**.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    // TODO: If your service needs to handle user requests, return a list of ServiceReplicaListeners here.
    return new[] { new ServiceReplicaListener(parameters =>
        new WcfCommunicationListener(ServiceInitializationParameters,typeof(ICalculator), this)
        {
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            EndpointResourceName = "ServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            Binding = this.CreateListenBinding()
        }
    )};
}

```

## Escritura de clientes para la pila de comunicación de WCF
Para que los clientes de escritura se comuniquen con los servicios mediante WCF, el marco proporciona la clase **WcfClientCommunicationFactory**, que es la implementación específica de WCF de la clase [ClientCommunicationFactoryBase](service-fabric-reliable-service-communication.md).

```csharp

public WcfCommunicationClientFactory(
    ServicePartitionResolver servicePartitionResolver = null,
    Binding binding = null,
    object callback = null,
    IList<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null)

```

Se puede acceder al canal de comunicación de WCF desde la clase **WcfCommunicationClient** cread por la clase **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient<TChannel> : ICommunicationClient where TChannel : class
{
    public TChannel Channel { get; }
    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}

```

El código de cliente puede usar la clase **WcfCommunicationClientFactory** junto con la clase **ServicePartitionClient** para determinar el punto de conexión de servicio y comunicarse con el servicio.

```csharp

//
// Create a service resolver for resolving the endpoints of the calculator service.
//
ServicePartitionResolver serviceResolver = new ServicePartitionResolver(() => new FabricClient());

//
// Create the binding.
//
NetTcpBinding binding = CreateClientConnectionBinding();

var clientFactory = new WcfCommunicationClientFactory<ICalculator>(
    serviceResolver,// ServicePartitionResolver
    binding,        // Client binding
    null,           // Callback object
    null);          // do not retry Exception types


//
// Create a client for communicating with the calc service that has been created with the
// Singleton partition scheme.
//
var calculatorServicePartitionClient = new ServicePartitionClient<WcfCommunicationClient<ICalculator>>(
    clientFactory,
    ServiceName);

//
// Call the service to perform the operation.
//
var result = calculatorServicePartitionClient.InvokeWithRetryAsync(
    client => client.Channel.AddAsync(2, 3)).Result;

```

## Pasos siguientes
* [Llamada a procedimiento remoto con la comunicación remota de Reliable Services](service-fabric-reliable-services-communication-remoting.md)

* [Web API con OWIN en Reliable Services](service-fabric-reliable-services-communication-webapi.md)

<!---HONumber=AcomDC_0128_2016-->