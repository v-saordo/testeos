## Procesamiento de mensajes de dispositivo a nube

En esta sección, creará una aplicación de consola de Windows que procesa mensajes de dispositivo a nube desde el Centro de IoT. El Centro de IoT expone un punto de conexión compatible con [Centros de eventos ] que habilita a una aplicación para poder leer los mensajes de dispositivo a nube. En este tutorial se utiliza la clase [EventProcessorHost] para procesar estos mensajes en una aplicación de consola. Para obtener más información sobre cómo procesar los mensajes de los Centros de eventos, puede consultar el tutorial [Introducción a los Centros de eventos].

La principal dificultad a la que se enfrenta a la hora de implementar un almacenamiento confiable de mensajes de puntos de datos o de reenviar los mensajes interactivos, es que el procesamiento de eventos de los Centros de eventos depende del consumidor de mensajes para ejecutar puntos de control de su progreso. Además, para lograr un alto rendimiento, al leer desde Centros de eventos debería ejecutar puntos de control en lotes grandes. Esto crea la posibilidad de realizar un procesamiento duplicado para un gran número de mensajes si se produce un error y tiene que volver al punto de control anterior. En este tutorial puede ver cómo sincronizar escrituras de Almacenamiento de Azure y ventanas de desduplicación de Bus de servicio con puntos de control de la clase **EventProcessorHost**.

Para escribir mensajes de manera confiable en el Almacenamiento de Azure, en el ejemplo se utiliza la característica de confirmación de bloques individuales de [blobs en bloques][Azure Block Blobs]. El procesador de eventos acumula los mensajes en memoria hasta que llega la hora de ejecutar un punto de control (por ejemplo, cuando el búfer acumulado de mensajes es mayor que el tamaño máximo de bloque de 4 MB, o bien una vez que transcurre la ventana de tiempo de desduplicación del Bus de servicio). A continuación, antes de ejecutar los puntos de control, el código confirma un nuevo bloque en el blob.

El procesador de eventos usa desplazamientos de mensajes de Centros de eventos como identificadores de bloque. Esto le permite realizar una comprobación de desduplicación antes de confirmar el nuevo bloque en el almacenamiento, vigilando la posibilidad de un bloqueo entre la confirmación de un bloque y el punto de control.

> [AZURE.NOTE] En este tutorial se usa una sola cuenta de almacenamiento para escribir todos los mensajes que se recuperan del Centro de IoT. Consulte [Objetivos de escalabilidad y rendimiento del Almacenamiento de Azure] para decidir si necesita utilizar varias cuentas de Almacenamiento de Azure en su solución.

La aplicación utiliza la característica de desduplicación del Bus de servicio para evitar duplicados cuando procesa mensajes interactivos. El dispositivo simulado marca cada mensaje interactivo con un valor de **MessageId** exclusivo que permite al Bus de servicio asegurarse de que, en la ventana de tiempo especificada de la desduplicación, no se entregan dos mensajes con el mismo valor de **MessageId** a los receptores. Esta desduplicación, junto con la semántica de finalización de cada mensaje proporcionada por las colas del Bus de servicio, facilita la implementación de un procesamiento confiable de los mensajes interactivos.

Para tener la seguridad de que no se reenvía ningún mensaje fuera de la ventana de desduplicación, el código sincroniza el mecanismo de ejecución de puntos de control de la clase **EventProcessorHost** con la ventana de desduplicación de cola del Bus de servicio. Para ello, se fuerza un punto de control al menos una vez cada vez que transcurre una ventana de tiempo de desduplicación (en este tutorial, una hora).

> [AZURE.NOTE] En este tutorial se usa una cola del Bus de servicio para procesar todos los mensajes interactivos recibidos del Centro de IoT. Consulte la [documentación del Bus de servicio] para más información sobre cómo utilizar las colas del Bus de servicio para reunir los requisitos de escalabilidad de su solución.

### Aprovisionamiento de una cuenta de almacenamiento de Azure y una cola del Bus de servicio
Para poder utilizar la clase [EventProcessorHost], debe tener una cuenta de Almacenamiento de Azure que habilite la clase **EventProcessorHost** para que registre la información del punto de control. Puede utilizar una cuenta de almacenamiento ya existente o seguir las instrucciones que se indican en [Acerca de las cuentas de Almacenamiento de Azure] para crear una nueva. Tome nota de la cadena de conexión de la cuenta de almacenamiento.

También necesitará una cola del Bus de servicio para habilitar el procesamiento confiable de los mensajes interactivos. Puede crear una cola mediante programación con una ventana de desduplicación de 1 hora, como se explica en [Utilización de las colas del Bus de servicio][Service Bus Queue], o bien utilizar el [Portal de Azure], siguiendo estos pasos:

1. Haga clic en **NUEVO** en la esquina inferior izquierda, a continuación, en **Servicios de aplicaciones**, a continuación, en **Bus de servicio**, a continuación, en **Cola** y, a continuación, en **Creación personalizada**, escriba el nombre **d2ctutorial**, seleccione una región, utilice un espacio de nombres existente o cree uno nuevo y, a continuación, en la siguiente página, seleccione **Habilitar detección de duplicados** y establezca la **ventana de tiempo del historial de detección de duplicados** en una hora. A continuación, haga clic en la marca de verificación para guardar la configuración de la cola.

    ![][30]

2. En la lista de colas del Bus de servicio, haga clic en **d2ctutorial** y, a continuación, haga clic en **Configurar**. Cree dos directivas de acceso compartido, una llamada **enviar** con permisos de **envío** y otra llamada **escuchar** con permisos de **escucha**. Haga clic en **Guardar** en la parte inferior de la página.

    ![][31]

3. Haga clic en **Panel** en la parte superior, a continuación, en **Información de conexión** en la parte inferior y tome nota de las dos cadenas de conexión.

    ![][32]

### Creación del procesador de eventos

1. En la solución actual de Visual Studio, haga clic en **Archivo**, a continuación, en **Agregar** y, finalmente, en **Nuevo proyecto** para crear un nuevo proyecto de Visual C# para Windows con la plantilla de proyecto **Aplicación de consola**. Denomine el proyecto **ProcessDeviceToCloudMessages**.

    ![][10]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **ProcessDeviceToCloudMessages** y, a continuación, haga clic en **Administrar paquetes de NuGet**. Aparecerá el cuadro de diálogo **Administrador de paquetes NuGet**.

3. Busque **WindowsAzure.ServiceBus**, haga clic en **Instalar** y acepte los términos de uso. De esta forma, se descarga, instala y agrega una referencia al [paquete de NuGet del Bus de servicio de Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus), con todas sus dependencias.

4. Busque **Centro de eventos del Bus de servicio de Microsoft Azure - EventProcessorHost**, haga clic en **Instalar** y acepte los términos de uso. Esto descarga, instala y agrega una referencia al [Centro de eventos del Bus de servicio de Azure - Paquete de NuGet de EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), con todas sus dependencias.

5. Haga clic con el botón derecho en el proyecto **ProcessDeviceToCloudMessages**, haga clic en **Agregar** y, luego, en **Clase**. Asigne a la nueva clase el nombre **StoreEventProcessor** y, luego, haga clic en **Aceptar** para crear la clase.

6. Agregue las siguientes instrucciones en la parte superior del archivo StoreEventProcessor.cs:

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. Sustituya el código siguiente por el cuerpo de la clase:

    ```
    class StoreEventProcessor : IEventProcessor
    {
      private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
      public static string StorageConnectionString;
      public static string ServiceBusConnectionString;

      private CloudBlobClient blobClient;
      private CloudBlobContainer blobContainer;
      private QueueClient queueClient;

      private long currentBlockInitOffset;
      private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

      private Stopwatch stopwatch;
      private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

      public StoreEventProcessor()
      {
        var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
        blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("d2ctutorial");
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString, "d2ctutorial");
      }

      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }

      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

        if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
        {
          currentBlockInitOffset = 0;
        }
        stopwatch = new Stopwatch();
        stopwatch.Start();

        return Task.FromResult<object>(null);
      }

      async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
      {
        foreach (EventData eventData in messages)
        {
          byte[] data = eventData.GetBytes();

          if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
          {
            var messageId = (string) eventData.SystemProperties["message-id"];

            var queueMessage = new BrokeredMessage(new MemoryStream(data));
            queueMessage.MessageId = messageId;
            queueMessage.Properties["messageType"] = "interactive";
            await queueClient.SendAsync(queueMessage);

            WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
            continue;
          }

          if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
          {
            await AppendAndCheckpoint(context);
          }
          await toAppend.WriteAsync(data, 0, data.Length);

          Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
            context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
        }
      }

      private async Task AppendAndCheckpoint(PartitionContext context)
      {
        var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
        var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
        toAppend.Seek(0, SeekOrigin.Begin);
        byte[] md5 = MD5.Create().ComputeHash(toAppend);
        toAppend.Seek(0, SeekOrigin.Begin);

        var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
        var currentBlob = blobContainer.GetBlockBlobReference(blobName);

        if (await currentBlob.ExistsAsync())
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var blockList = await currentBlob.DownloadBlockListAsync();
          var newBlockList = new List<string>(blockList.Select(b => b.Name));

          if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
          {
            newBlockList.Add(blockId);
            WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
          }
          else
          {
            WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
          }
          await currentBlob.PutBlockListAsync(newBlockList);
        }
        else
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var newBlockList = new List<string>();
          newBlockList.Add(blockId);
          await currentBlob.PutBlockListAsync(newBlockList);

          WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
        }

        toAppend.Dispose();
        toAppend = new MemoryStream(MAX_BLOCK_SIZE);

        // checkpoint.
        await context.CheckpointAsync();
        WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

        currentBlockInitOffset = long.Parse(context.Lease.Offset);
        stopwatch.Restart();
      }

      private void WriteHighlightedMessage(string message)
      {
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine(message);
        Console.ResetColor();
      }
    }
    ```

    La clase **EventProcessorHost** llama a esta clase para procesar los mensajes de dispositivo a nube recibidos del Centro de IoT. El código de esta clase implementa la lógica para almacenar los mensajes de manera confiable en un contenedor de blobs y reenviar los mensajes interactivos a la cola del Bus de servicio. El método **OpenAsync**, inicializa la variable **currentBlockInitOffset** que realiza un seguimiento del desplazamiento actual del primer mensaje leído por este procesador de eventos. Recuerde que cada procesador es responsable de una sola partición.
    
    El método **ProcessEventsAsync** recibe un lote de mensajes desde el Centro de IoT y los procesa como sigue: envía mensajes interactivos a la cola del Bus de servicio y anexa mensajes de punto de datos en el búfer de memoria denominado **toAppend**. Si el búfer de memoria alcanza el límite de bloques de 4 MB o si las ventanas de tiempo de desduplicación del Bus de servicio transcurrieron desde el último punto de control (en este tutorial, una hora), se desencadenará un punto de control.

    El método **AppendAndCheckpoint** genera primero un valor de blockId para el bloque que se va a anexar. El Almacenamiento de Azure requiere que todos los identificadores de bloque tengan la misma longitud, por lo que el método rellena el desplazamiento con ceros iniciales (`currentBlockInitOffset.ToString("0000000000000000000000000")`). Luego, si ya existe un bloque con este identificador en el blob, el método lo sobrescribe con el contenido actual del búfer.

    > [AZURE.NOTE] Para simplificar el código, en este tutorial se usa un solo archivo de blob por partición para almacenar los mensajes. En una solución real se implementan los archivos gradualmente, de modo que se crean archivos adicionales cuando alcanzan un determinado tamaño (tenga en cuenta que el blob en bloques de Azure puede tener como máximo 195 GB), o después de un determinado período de tiempo.

8. En la clase **Program**, agregue las instrucciones **using** siguientes en la parte superior:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Modifique el método **Main** para la clase **Program** como se muestra a continuación y sustituya la cadena de conexión **iothubowner** del Centro de IoT (del tutorial [Introducción al Centro de IoT]), la cadena de conexión de almacenamiento y la cadena de conexión del Bus de servicio por los permisos de **envío** para la cola llamada **d2ctutorial**:

    ```
    static void Main(string[] args)
    {
      string iotHubConnectionString = "{iot hub connection string}";
      string iotHubD2cEndpoint = "messages/events";
      StoreEventProcessor.StorageConnectionString = "{storage connection string}";
      StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```
    
    > [AZURE.NOTE] Para hacerlo más sencillo, en este tutorial se usa una sola instancia de [EventProcessorHost]. Consulte [Guía de programación de Centros de eventos] para más información.

## Recepción de mensajes interactivos
En esta sección, escribirá una aplicación de consola de Windows que recibe los mensajes interactivos de la cola del Bus de servicio. Para obtener más información sobre cómo diseñar una solución con el Bus de servicio, consulte [Compilación de aplicaciones de varios con el Bus de servicio][].

1. En la solución actual de Visual Studio, cree un nuevo proyecto de aplicación de Visual C# para Windows con la plantilla de proyecto **Aplicación de consola**. Denomine el proyecto como **ProcessD2CInteractiveMessages**.

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **ProcessD2CInteractiveMessages** y, a continuación, haga clic en **Administrar paquetes de NuGet**. Con esto, aparecerá la ventana **Administrador de paquetes NuGet**.

3. Busque **WindowsAzure.Service Bus**, haga clic en **Instalar** y acepte los términos de uso. De esta forma, se descarga, se instala y se agrega una referencia al [Bus de servicio de Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

4. Agregue la siguiente instrucción **using** en la parte superior del archivo **Program.cs**:

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Por último, agregue las siguientes líneas al método **Main** y sustituya la cadena de conexión por los permisos de **escucha** para la cola llamada **d2ctutorial**:

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString, "d2ctutorial");

    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    Client.OnMessage((message) =>
    {
      try
      {
        var bodyStream = message.GetBody<Stream>();
        bodyStream.Position = 0;
        var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

        Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

        message.Complete();
      }
      catch (Exception)
      {
        message.Abandon();
      }
    }, options);

    Console.WriteLine("Receiving interactive messages from SB queue...");
    Console.WriteLine("Press any key to exit.");
    Console.ReadLine();
    ```

<!-- Links -->
[Acerca de las cuentas de Almacenamiento de Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Introducción a los Centros de eventos]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry
[Objetivos de escalabilidad y rendimiento del Almacenamiento de Azure]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Centros de eventos ]: ../event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Guía de programación de Centros de eventos]: ../event-hubs/event-hubs-programming-guide.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portal de Azure]: https://manage.windowsazure.com/
[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues.md
[Compilación de aplicaciones de varios con el Bus de servicio]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[Introducción al Centro de IoT]: iot-hub-csharp-csharp-getstarted.md
[documentación del Bus de servicio]: https://azure.microsoft.com/documentation/services/service-bus/

<!-- Images -->
[10]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

<!---HONumber=AcomDC_0204_2016-->