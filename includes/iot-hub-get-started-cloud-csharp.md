## Creación de una identidad de dispositivo

En esta sección, creará una aplicación de consola de Windows que crea una nueva identidad de dispositivo en el registro de identidades de su Centro de IoT. No se puede conectar un dispositivo al Centro de IoT a menos que tenga una entrada en el registro de identidades de dispositivo. Consulte la sección **Registro de identidad de dispositivos** de la [Guía para desarrolladores del Centro de IoT][lnk-devguide-identity] para obtener más información. Al ejecutar esta aplicación de consola, genera y una clave y un identificador de dispositivo únicos con el que el dispositivo puede identificarse cuando envía al Centro de IoT mensajes de dispositivo a la nube.

1. En Visual Studio, agregue un nuevo proyecto de aplicación de escritorio clásico de Windows de Visual C# con la plantilla de proyecto **Aplicación de consola**. Denomine el proyecto **CreateDeviceIdentity**.

	![][10]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **CreateDeviceIdentity** y, a continuación, seleccione **Administrar paquetes de NuGet**.

3. En la ventana **Administrador de paquetes NuGet**, busque **Microsoft Azure Devices**, haga clic en **Instalar** para instalar el paquete de **Microsoft.Azure.Devices** y acepte los términos de uso.

	![][11]

4. De esta forma, se descarga, instala y agrega una referencia al paquete NuGet [del SDK de Servicio IoT de Microsoft Azure][lnk-nuget-service-sdk].

4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs**:

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Agregue los siguientes campos a la clase **Program**; para ello, sustituya el valor de marcador de posición por la cadena de conexión del Centro de IoT que creó en la sección anterior:

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. Agregue el método siguiente a la clase **Program**:

		private async static Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

	Este método crea una nueva identidad de dispositivo con el id. **myFirstDevice** (si ese id. de dispositivo ya existe en el registro, el código simplemente recupera la información del dispositivo existente). A continuación, la aplicación muestra la clave principal de esa identidad. Esta clave se usará en el dispositivo simulado para conectarse al Centro de IoT.

7. Por último, agregue las líneas siguientes al método **Main**:

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Ejecute la aplicación y anote la clave del dispositivo.

    ![][12]

> [AZURE.NOTE] El registro de identidades del Centro de IoT solo almacena las identidades de dispositivo para permitir el acceso seguro al centro. Almacena las claves y los identificadores de dispositivo para usarlos como credenciales de seguridad, y un indicador de habilitado o deshabilitado que permite deshabilitar el acceso a un dispositivo individual. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Consulte [Guía para desarrolladores del Centro de IoT][lnk-devguide-identity] para más información.

## Recepción de mensajes de dispositivo a nube

En esta sección, creará una aplicación de consola de Windows que lee los mensajes de dispositivo a nube del Centro de IoT. El Centro de IoT expone un punto de conexión compatible con [Centros de eventos ][lnk-event-hubs-overview] para poder leer los mensajes de dispositivo a nube. Para simplificar las cosas, este tutorial crea un lector básico que no es apto para una implementación de alta capacidad de procesamiento. El [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-processd2c-tutorial] muestra cómo procesar mensajes de dispositivo a la nube a escala. El tutorial [Introducción a los Centros de eventos][lnk-eventhubs-tutorial] proporciona información adicional acerca de cómo procesar los mensajes desde los Centros de eventos y se puede aplicar a los puntos de conexión compatibles con Centro de eventos de Centro de IoT.

1. En Visual Studio, agregue un nuevo proyecto de aplicación de escritorio clásico de Windows de Visual C# con la plantilla de proyecto **Aplicación de consola**. Asigne al proyecto el nombre **ReadDeviceToCloudMessages**.

    ![][10]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **ReadDeviceToCloudMessages** y luego haga clic en **Administrar paquetes de NuGet**.

3. En la ventana **Administrador de paquetes NuGet**, busque **WindowsAzure.ServiceBus**, haga clic en **Instalar** y acepte los términos de uso.

    De esta forma, se descarga, se instala y se agrega una referencia al [Bus de servicio de Azure][lnk-servicebus-nuget].

4. Agregue la siguiente instrucción `using` en la parte superior del archivo **Program.cs**:

        using Microsoft.ServiceBus.Messaging;

5. Agregue los siguientes campos a la clase **Program**; para ello, sustituya los valores de marcador de posición por la cadena de conexión del Centro de IoT que creó en la sección *Creación de un Centro de IoT*:

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Agregue el método siguiente a la clase **Program**:

        private async static Task ReceiveMessagesFromDeviceAsync(string partition)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine(string.Format("Message received. Partition: {0} Data: '{1}'", partition, data));
            }
        }

    Este método usa una instancia de **EventHubReceiver** para recibir mensajes de todas las particiones de recepción de dispositivo a nube del Centro de IoT. Observe cómo pasar un parámetro `DateTime.Now` al crear el objeto **EventHubReceiver** para que solo reciba los mensajes enviados después de iniciarse. Esto es útil en un entorno de prueba, por que puede ver el conjunto actual de mensajes, pero en un entorno de producción el código debe asegurarse de que se procesan todos los mensajes. Consulte el [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-processd2c-tutorial] para obtener más información.

7. Por último, agregue las líneas siguientes al método **Main**:

        Console.WriteLine("Receive messages\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        foreach (string partition in d2cPartitions)
        {
           ReceiveMessagesFromDeviceAsync(partition);
        }  
        Console.ReadLine();


<!-- Links -->

[lnk-eventhubs-tutorial]: event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!---HONumber=AcomDC_0218_2016-->