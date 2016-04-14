## Recepción de mensajes con EventProcessorHost

[EventProcessorHost][] es una clase de .NET que simplifica la recepción de eventos desde los Centros de eventos mediante la administración de puntos de control persistentes y recepciones paralelas desde tales Centros de eventos. Con [EventProcessorHost][], puede dividir eventos entre varios destinatarios, incluso cuando están hospedados en distintos nodos. Este ejemplo muestra cómo usar [EventProcessorHost][] para un solo destinatario. El ejemplo de [procesamiento de eventos escalados horizontalmente][] muestra cómo usar [EventProcessorHost][] con varios destinatarios.

Para poder usar [EventProcessorHost][], debe tener una [cuenta de almacenamiento de Azure][]\:

1. Inicie sesión en el [Portal de Azure clásico][] y haga clic en **NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de datos**, **Almacenamiento** y **Creación rápida** y, a continuación, escriba un nombre para la cuenta de almacenamiento. Seleccione la región deseada y, a continuación, haga clic en **Crear cuenta de almacenamiento**.

    ![][11]

3. Haga clic en la cuenta de almacenamiento recién creada y, a continuación, en **Administrar claves de acceso**:

    ![][12]

    Copie la clave de acceso para utilizarla más adelante en este tutorial.

4. En Visual Studio, cree un nuevo proyecto de aplicación de escritorio de Visual C# con la plantilla de proyecto **Aplicación de consola**. Asigne al proyecto el nombre **Receptor**.

    ![][14]

5. En el Explorador de soluciones, haga clic con el botón derecho en la solución y, a continuación, haga clic en **Administrar paquetes de NuGet**.

	Aparecerá el cuadro de diálogo **Administrar paquetes de NuGet**.

6. Busque `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, haga clic en **Instalar** y acepte las condiciones de uso.

    ![][13]

	Esto descarga, instala y agrega una referencia al [Centro de eventos del Bus de servicio de Azure - Paquete de NuGet de EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), con todas sus dependencias.

7. Haga clic con el botón derecho en el proyecto **Receptor**, haga clic en **Agregar** y, a continuación, haga clic en **Clase**. Asigne a la nueva clase el nombre **SimpleEventProcessor** y después haga clic en **Aceptar** para crear la clase.

8. Agregue las siguientes instrucciones en la parte superior del archivo SimpleEventProcessor.cs:

	```
	using Microsoft.ServiceBus.Messaging;
	using System.Diagnostics;
	using System.Threading.Tasks;
	```

	A continuación, sustituya el código siguiente por el cuerpo de la clase:

	```
    class SimpleEventProcessor : IEventProcessor
	{
	    Stopwatch checkpointStopWatch;

	    async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	    {
	        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
	        if (reason == CloseReason.Shutdown)
	        {
	            await context.CheckpointAsync();
	        }
	    }

	    Task IEventProcessor.OpenAsync(PartitionContext context)
	    {
	        Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
	        this.checkpointStopWatch = new Stopwatch();
	        this.checkpointStopWatch.Start();
	        return Task.FromResult<object>(null);
	    }

	    async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	    {
	        foreach (EventData eventData in messages)
	        {
	            string data = Encoding.UTF8.GetString(eventData.GetBytes());

	            Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
	                context.Lease.PartitionId, data));
	        }

	        //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
	        if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
	    }
	}
    ````

	**EventProcessorHost** llamará a esta clase para procesar los eventos recibidos del Centro de eventos. Tenga en cuenta que la clase `SimpleEventProcessor` usa un cronómetro para llamar periódicamente al método de punto de control en el contexto **EventProcessorHost**. Esto garantiza que, si se reinicia el destinatario, no perderá más de cinco minutos de trabajo de procesamiento.

9. En la clase **Program.cs**, agregue las siguientes instrucciones `using` al principio del archivo:

	```
	using Microsoft.ServiceBus.Messaging;
	using Microsoft.Threading;
	using System.Threading.Tasks;
	```

	Después, modifique el método `Main` de la clase `Program` de la siguiente manera: sustituyendo el nombre del Centro de eventos y la cadena de conexión, y la cuenta de almacenamiento y la clave que ha copiado en las secciones anteriores:

    ```
	static void Main(string[] args)
    {
      string eventHubConnectionString = "{event hub connection string}";
      string eventHubName = "{event hub name}";
      string storageAccountName = "{storage account name}";
      string storageAccountKey = "{storage account key}";
      string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
	````
> [AZURE.NOTE]Este tutorial usa una sola instancia de [EventProcessorHost][]. Para aumentar el rendimiento, se recomienda ejecutar varias instancias de [EventProcessorHost][], como se muestra en el ejemplo de [procesamiento de eventos escalados horizontalmente][]. En esos casos, las diferentes instancias se coordinan automáticamente entre sí con el fin de equilibrar la carga de los eventos recibidos. Si desea que varios destinatarios procesen *todos* los eventos, debe usar el concepto **ConsumerGroup**. Cuando se reciben eventos de distintos equipos, puede ser útil especificar nombres para las instancias de [EventProcessorHost][] según los equipos (o roles) en que se implementan. Para obtener más información acerca de estos temas, consulte [Información general de los Centros de eventos][] y [Guía de programación de Centros de eventos][].

<!-- Links -->
[Información general de los Centros de eventos]: event-hubs-overview.md
[Guía de programación de Centros de eventos]: event-hubs-programming-guide.md
[procesamiento de eventos escalados horizontalmente]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[cuenta de almacenamiento de Azure]: ../storage/storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Portal de Azure clásico]: http://manage.windowsazure.com

<!-- Images -->

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png

<!---HONumber=AcomDC_1217_2015-->
