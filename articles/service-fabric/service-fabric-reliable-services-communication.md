<properties
   pageTitle="Información general sobre la comunicación de Reliable Services | Microsoft Azure"
   description="Información general sobre el modelo de comunicación de Reliable Services, incluidos los agentes de escucha de apertura en los servicios, la resolución de puntos de conexión y la comunicación entre servicios."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/17/2015"
   ms.author="vturecek@microsoft.com"/>

# El modelo de comunicación de Reliable Services

Azure Service Fabric como una plataforma es completamente independiente de la comunicación entre los servicios. Todos los protocolos y las pilas son aceptables, desde UDP hasta HTTP. El desarrollador del servicio es quien debe elegir cómo deberían comunicarse los servicios. El marco de trabajo de la aplicación de Reliable Services proporciona algunas pilas de comunicación creadas previamente y herramientas que puede utilizar para implementar la pila de comunicación personalizada. Se incluyen abstracciones que los clientes pueden utilizar para detectar puntos de conexión de servicio y comunicarse con ellos.

## Configuración de la comunicación del servicio

La API de Reliable Services usa una interfaz simple para la comunicación del servicio. Para abrir un punto de conexión para el servicio, solo tiene que implementar esta interfaz:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

Luego puede agregar su implementación del agente de escucha de comunicación devolviéndolo en una invalidación del método de clase basado en el servicio.

Para servicios sin estado:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
```

Para servicios con estado:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
```

En ambos casos, devuelve una colección de agentes de escucha. Esto permite que el servicio pueda utilizar con facilidad varios agentes de escucha. Por ejemplo, puede tener un agente de escucha HTTP y un agente de escucha de WebSocket independiente. Cada agente de escucha recibe un nombre y la colección resultante del *nombre: los pares de direcciones* se representan como un objeto JSON cuando un cliente solicita las direcciones de escucha para una partición o instancia de servicio.

En un servicio sin estado, la invalidación devuelve una colección de ServiceInstanceListeners. Un ServiceInstanceListener contiene una función para crear un ICommunicationListener y le da un nombre. Para servicios con estado, la invalidación devuelve una colección de ServiceReplicaListeners. Difiere ligeramente de su homólogo sin estado, porque ServiceReplicaListener tiene una opción de abrir un ICommunicationListener en las réplicas secundarias. No solo puede usar varios agentes de escucha de comunicación en un servicio, sino también especificar cuáles aceptan solicitudes en réplicas secundarias y cuáles escuchan solo en las réplicas principales.

Por ejemplo, puede hacer que un ServiceRemotingListener realice llamadas RPC solo en las principales réplicas, y un segundo agente de escucha personalizado que lea solicitudes en réplicas secundarias:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(initParams =>
            new MyCustomListener(initParams),
            "customReadonlyEndpoint",
            true),

        new ServiceReplicaListener(initParams =>
            new ServiceRemotingListener<IMyStatefulInterface2>(initParams, this),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

Por último, puede describir los puntos de conexión que son necesarios para el servicio en el [manifiesto de servicio](service-fabric-application-model.md) en la sección que trata sobre los puntos de conexión.

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

El agente de escucha de comunicación puede tener acceso a los recursos de puntos de conexión asignados a él desde `CodePackageActivationContext` en `ServiceInitializationParameters`. El agente de escucha luego puede empezar a escuchar las solicitudes cuando se abre.

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]Los recursos de los puntos de conexión son comunes para el paquete de servicio completo y los asigna Service Fabric cuando se activa el paquete de servicio. Todas las réplicas hospedadas en el mismo ServiceHost comparten el mismo puerto. Esto significa que el agente de escucha de comunicación debe admitir el uso compartido de puertos. La manera recomendada de hacerlo es que el agente de escucha de comunicación utilice el identificador de partición y el identificador de instancia o de réplica cuando genera la dirección de escucha.

```csharp

var replicaOrInstanceId = 0;
var parameters = this.serviceInitializationParameters as StatelessServiceInitializationParameters;
if (parameters != null)
{
   replicaOrInstanceId = parameters.InstanceId;
}
else
{
   replicaOrInstanceId = ((StatefulServiceInitializationParameters) this.serviceInitializationParameters).ReplicaId;
}

var nodeContext = FabricRuntime.GetNodeContext();

var listenAddress = new Uri(
    string.Format(
        CultureInfo.InvariantCulture,
        "{0}://{1}:{2}/{5}/{3}-{4}",
        scheme,
        nodeContext.IPAddressOrFQDN,
        port,
        this.serviceInitializationParameters.PartitionId,
        replicaOrInstanceId,
        Guid.NewGuid().ToString()));

```

Para obtener un tutorial completo de cómo escribir un `ICommunicationListener`, vea [Introducción a los servicios de la API web de Microsoft Azure Service Fabric con autohospedaje OWIN](service-fabric-reliable-services-communication-webapi.md).

### Comunicación entre cliente y servicio
La API de Reliable Services proporciona las abstracciones siguientes que facilitan la escritura de clientes que se comunican con los servicios.

#### ServicePartitionResolver
Esta clase de utilidad ayuda a los clientes a determinar el punto de conexión de un Service Fabric en tiempo de ejecución. En la terminología de Service Fabric, el proceso de determinar el punto de conexión de un servicio se denomina *resolución de puntos de conexión de servicio*.

```csharp

public delegate FabricClient CreateFabricClientDelegate();

// ServicePartitionResolver methods

public ServicePartitionResolver(CreateFabricClientDelegate createFabricClient);

Task<ResolvedServicePartition> ResolveAsync(Uri serviceName,
   long partitionKey,
   CancellationToken cancellationToken);

Task<ResolvedServicePartition> ResolveAsync(ResolvedServicePartition previousRsp,
   CancellationToken cancellationToken);


```
> [AZURE.NOTE]FabricClient es el objeto que se utiliza para comunicarse con el clúster de Service Fabric para realizar diversas operaciones de administración en el clúster.

Normalmente, el código de cliente no necesita trabajar con `ServicePartitionResolver` directamente. Se crea y se pasa a otras clases auxiliares en la API de Reliable Services. Estas clases usan el solucionador internamente y ayudan al cliente a comunicarse con el servicio.

#### CommunicationClientFactory
`ICommunicationClientFactory` define la interfaz base que implementa un generador de cliente de comunicación que genera clientes que pueden comunicarse con un servicio de Service Fabric. La implementación de CommunicationClientFactory depende de la pila de comunicación que el servicio de Service Fabric utiliza donde el cliente desea comunicarse. La API de Reliable Services ofrece un `CommunicationClientFactoryBase<TCommunicationClient>`. Esto proporciona una implementación base de la interfaz `ICommunicationClientFactory` y realiza tareas comunes para todas las pilas de comunicación. (Estas tareas incluyen el uso de `ServicePartitionResolver` para determinar el punto de conexión de servicio). Normalmente, los clientes implementan la clase CommunicationClientFactoryBase abstracta para controlar la lógica específica de la pila de comunicación.

```csharp

protected CommunicationClientFactoryBase(
   ServicePartitionResolver servicePartitionResolver = null,
   IEnumerable<IExceptionHandler> exceptionHandlers = null,
   IEnumerable<Type> doNotRetryExceptionTypes = null);


public class MyCommunicationClient : ICommunicationClient
{
   public MyCommunicationClient(MyCommunicationChannel communicationChannel)
   {
      this.CommunicationChannel = communicationChannel;
   }
   public MyCommunicationChannel CommunicationChannel { get; private set; }
   public ResolvedServicePartition ResolvedServicePartition;
}

public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

#### ServicePartitionClient
`ServicePartitionClient` utiliza CommunicationClientFactory (y ServicePartitionResolver internamente). También ayuda en la comunicación con el servicio mediante el control de reintentos de comunicación común y excepciones transitorias de Service Fabric.

```csharp

public ServicePartitionClient(
    ICommunicationClientFactory<TCommunicationClient> factory,
    Uri serviceName,
    long partitionKey);

public async Task<TResult> InvokeWithRetryAsync<TResult>(
    Func<TCommunicationClient, Task<TResult>> func,
    CancellationToken cancellationToken,
    params Type[] doNotRetryExceptionTypes)

```

Un patrón de uso típico tiene este aspecto:

```csharp

public MyCommunicationClientFactory myCommunicationClientFactory;
public Uri myServiceUri;

... other client code ..

// Call the service corresponding to the partitionKey myKey.

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(
   client =>
   {
      // Communicate with the service using the client.
      throw new NotImplementedException();
   },
   CancellationToken.None);


... other client code ...

```

## Pasos siguientes
* [Llamadas a procedimiento remoto con la comunicación remota de Reliable Services](service-fabric-reliable-services-communication-remoting.md)

* [API web que usa OWIN en Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Comunicación WCF con la utilización de Reliable Services](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0121_2016-->