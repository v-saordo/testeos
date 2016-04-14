## Carga de un archivo desde un dispositivo simulado

En esta sección, modificará la aplicación de dispositivo simulado que creó en [Envío de mensajes de nube a dispositivo con el Centro de IoT] para recibir mensajes de nube a dispositivo del Centro de IoT.

1. En Visual Studio, haga clic con el botón derecho en el proyecto **SimulatedDevice** y, luego, haga clic en **Administrar paquetes de NuGet...**. 

    De este modo aparece la ventana Administrar paquetes de NuGet.

2. Busque `WindowsAzure.Storage`, haga clic en **Instalar** y acepte las condiciones de uso.

    De esta forma, se descarga, instala y agrega una referencia al [SDK de Almacenamiento de Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage/).

3. En el archivo **Program.cs**, agregue las siguientes instrucciones al principio del archivo:

        using System.IO;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. En la clase **Program**, cambie el método **ReceiveC2dAsync** método de la siguiente manera:
         
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                if (receivedMessage.Properties.ContainsKey("command") && receivedMessage.Properties["command"] == "FileUpload")
                {
                    UploadFileToBlobAsync(receivedMessage);
                    continue;
                }

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
            }
        }

    Esto hace que el método **ReceiveC2dAsync** distinga los mensajes con la propiedad `command` establecida en `FileUpload`, que administrará el método **UploadFileToBlobAsync**.

    Agregue el método siguiente para controlar los comandos de carga de archivos.
   
        private static async Task UploadFileToBlobAsync(Message fileUploadCommand)
        {
            var fileUri = fileUploadCommand.Properties["fileUri"];
            var blob = new CloudBlockBlob(new Uri(fileUri));

            byte[] data = new byte[10 * 1024 * 1024];
            Random rng = new Random();
            rng.NextBytes(data);

            MemoryStream msWrite = new MemoryStream(data);
            msWrite.Position = 0;
            using (msWrite)
            {
                await blob.UploadFromStreamAsync(msWrite);
            }
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Uploaded file to: {0}", fileUri);
            Console.ResetColor();

            await deviceClient.CompleteAsync(fileUploadCommand);
        }

    Este método usa el SDK de Almacenamiento de Azure para cargar un blob de 10 megabytes generado de forma aleatoria en el URI especificado. Consulte [Almacenamiento de Azure - Uso de blobs] para obtener más información sobre cómo cargar blobs.

> [AZURE.NOTE]Observe que esta implementación del dispositivo simulado solo completa el mensaje de nube a dispositivo una vez cargado el blob. Este enfoque simplifica el procesamiento de los archivos cargados en el back-end porque la confirmación de entrega representa la disponibilidad del archivo cargado para su procesamiento. Pero, como se explica en la [Guía para desarrolladores del Centro de IoT][IoT Hub Developer Guide - C2D], un mensaje que no se completa antes del *tiempo de espera de visibilidad* (normalmente 1 minuto) se vuelve a colocar en la cola del dispositivo y el método **ReceiveAsync()** lo volverá a recibir. En escenarios donde la carga de archivos puede tardar más tiempo, tal vez sea preferible que el dispositivo simulado mantenga un almacén permanente de los trabajos de carga actuales. Esto permite al dispositivo simulado completar el mensaje de nube a dispositivo antes de que finalice la carga de archivos y luego enviar un mensaje de dispositivo a nube notificando la finalización al back-end.

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Almacenamiento de Azure - Uso de blobs]: https://azure.microsoft.com/es-ES/documentation/articles/storage-dotnet-how-to-use-blobs/#upload-a-blob-into-a-container

<!-- Images -->

<!---HONumber=Nov15_HO3-->