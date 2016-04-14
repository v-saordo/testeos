<properties 
   pageTitle="Información general de las API de los Centros de eventos de Azure | Microsoft Azure"
   description="Resumen de algunas de las API de cliente de .NET de los centros de eventos clave."
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="sethm" />

# Introducción a la API de Centros de eventos

Este artículo resume algunas de las API de cliente de .NET de los centros de eventos. Existen dos categorías: API de administración y de tiempo de ejecución. Las API de tiempo de ejecución están compuestas por todas las operaciones necesarias para enviar y recibir un mensaje. Las operaciones de administración le permiten administrar un estado de entidad de centros de eventos mediante la creación, actualización y eliminación de entidades.

Los escenarios de supervisión abarcan la administración y el tiempo de ejecución. Para obtener documentación de referencia detallada sobre las API de .NET, vea las referencias [.NET del Bus de servicio](https://msdn.microsoft.com/library/azure/mt419900.aspx) y [API de EventProcessorHost](https://msdn.microsoft.com/library/azure/mt445521.aspx).

## API de administración

Para realizar las siguientes operaciones de administración debe tener permisos de **administración** en el espacio de nombres del Bus de servicio:

### Crear

```
// Create the Event Hub
EventHubDescription ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
namespaceManager.CreateEventHubAsync(ehd).Wait();
```

### Actualizar

```
EventHubDescription ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
string ruleName = "myeventhubmanagerule";
string ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
namespaceManager.UpdateEventHubAsync(ehd).Wait();
```

### Eliminar

```
namespaceManager.DeleteEventHubAsync("Event Hub name").Wait();
```

## API de tiempo de ejecución

### Crear publicador

```
// EventHubClient model (uses implicit factory instance, so all links on same connection)
EventHubClient eventHubClient = EventHubClient.Create("Event Hub name");
```

### Publicar mensaje

```
// Create the device/temperature metric
MetricEvent info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
EventData data = new EventData(new byte[10]); // Byte array
EventData data = new EventData(Stream); // Stream 
EventData data = new EventData(info, serializer) //Object and serializer 
    {
       PartitionKey = info.DeviceId.ToString()
    };

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### Crear consumidor

```
// Create the Event Hubs client
EventHubClient eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
EventHubConsumerGroup defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index);

// From one day ago
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));
                        
// From specific offset, -1 means oldest
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index,startingOffset:-1); 
```

### Consumir mensaje

```
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)
                                    
// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## API de host del procesador de eventos

Estas API ofrecen resistencia a los procesos de trabajo que pueden dejar de estar disponibles, distribuyendo particiones entre trabajos disponibles.

```
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

string eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
string blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

EventHubDescription eventHubDescription = new EventHubDescription(EventHubName);
EventProcessorHost host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
            host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
host.UnregisterEventProcessorAsync().Wait();   
```

La interfaz [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx) se define como sigue:

```
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            Process messages here
        }
        
        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }


    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## Pasos siguientes

Para obtener más información acerca de los escenarios de los centros de eventos, visite estos vínculos:

- [¿Qué es Centros de eventos de Azure?](event-hubs-what-is-event-hubs.md)
- [Información general de los Centros de eventos](event-hubs-overview.md)
- [Guía de programación de Centros de eventos](event-hubs-programming-guide.md)
- [Ejemplos de código de los centros de eventos](http://code.msdn.microsoft.com/site/search?query=event hub&f[0].Value=event hubs&f[0].Type=SearchText&ac=5)

A continuación se incluyen referencias de API de .NET:

- [Referencias de la API de .NET de Centros de eventos y de Bus de servicio](https://msdn.microsoft.com/library/azure/mt419900.aspx)
- [Referencia de la API de host del procesador de eventos](https://msdn.microsoft.com/library/azure/mt445521.aspx)

<!---HONumber=AcomDC_0128_2016-->