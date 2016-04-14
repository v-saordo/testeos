## Envío de un mensaje de nube a dispositivo desde el back-end de la aplicación

En esta sección, escribirá una aplicación de consola de Windows que envía mensajes de nube a dispositivo a la aplicación del dispositivo simulado.

1. En la solución actual de Visual Studio, cree un proyecto de aplicación de escritorio de Visual C# con la plantilla de proyecto **Aplicación de consola**. Denomine el proyecto **SendCloudToDevice**.

   	![][20]

2. En el Explorador de soluciones, haga clic con el botón derecho en la solución y luego haga clic en **Administrar paquetes de NuGet para la solución...**.

	Se muestra la ventana Administrar paquetes de NuGet.

3. Busque `Microsoft Azure Devices`, haga clic en **Instalar** y acepte las condiciones de uso.

	De esta forma, se descarga, instala y agrega una referencia al [paquete NuGet del SDK de Servicios IoT de Azure].

4. Agregue la siguiente instrucción `using` en la parte superior del archivo **Program.cs**:

		using Microsoft.Azure.Devices;

5. Agregue los siguientes campos a la clase **Program**; para ello, sustituya el valor de marcador de posición por la cadena de conexión del Centro de IoT empleado en la [Introducción al Centro de IoT]\:

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Agregue el método siguiente a la clase **Program**:

		private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

	Este método enviará un nuevo mensaje de nube a dispositivo al dispositivo con el identificador `myFirstDevice`. Si modificó el parámetro usado en [Introducción al Centro de IoT], cambie este en consecuencia.

7. Por último, agregue las líneas siguientes al método **Main**:

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. Desde Visual Studio, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio...** Seleccione **Proyectos de inicio múltiples** y elija la acción **Iniciar** para las aplicaciones **ProcessDeviceToCloudMessages**, **SimulatedDevice** y **SendCloudToDevice**.

9.  Presione **F5** y verá cómo se inician las tres aplicaciones. Seleccione las ventanas de **SendCloudToDevice** y presione **ENTRAR**: debería ver el mensaje que recibe la aplicación simulada.

    ![][21]

## Recibir comentarios de entrega
Es posible solicitar confirmaciones de entrega (o expiración) del Centro de IoT para cada mensaje de nube a dispositivo. Esto permite que el back-end de nube notifique fácilmente la lógica de reintento o compensación. Consulte la [Guía para desarrolladores del Centro de IoT][IoT Hub Developer Guide - C2D] para obtener más información sobre los comentarios de nube a dispositivo.

En esta sección, modificará la aplicación **SendCloudToDevice** para solicitar comentarios y recibirlos desde el Centro de IoT.

1. En Visual Studio, en el proyecto **SendCloudToDevice**, agregue el método siguiente a la clase **Program**.
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    Tenga en cuenta que el patrón de recepción es el mismo que se usa para recibir mensajes de nube a dispositivo desde la aplicación de dispositivo.

2. Agregue el siguiente método en el método **Main** inmediatamente después de la línea `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`:

        ReceiveFeedbackAsync();

3. Con el fin de solicitar comentarios sobre la entrega del mensaje de nube a dispositivo, debe especificar una propiedad en el método **SendCloudToDeviceMessageAsync**. Agregue la línea siguiente, inmediatamente después de la línea `var commandMessage = new Message(...);`:

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Ejecute las aplicaciones presionando **F5** y verá cómo se inician las tres aplicaciones. Seleccione las ventanas de **SendCloudToDevice** y presione **ENTRAR**: debería ver el mensaje que recibe la aplicación simulada y, después de unos segundos, el mensaje de comentarios que recibe la aplicación **SendCloudToDevice**.

    ![][22]

> [AZURE.NOTE]Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, se recomienda implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal como se sugiere en el artículo de MSDN [Control de errores transitorios].

<!-- Links -->

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[paquete NuGet del SDK de Servicios IoT de Azure]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Control de errores transitorios]: https://msdn.microsoft.com/es-ES/library/hh680901(v=pandp.50).aspx
[Introducción al Centro de IoT]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[20]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png
[21]: ./media/iot-hub-c2d-cloud-csharp/sendc2d1.png
[22]: ./media/iot-hub-c2d-cloud-csharp/sendc2d2.png

<!---HONumber=AcomDC_1203_2015-->