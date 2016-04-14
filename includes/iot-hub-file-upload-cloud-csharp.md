## Aprovisionamiento de una cuenta de Almacenamiento de Azure
Puesto que el dispositivo simulado cargará un archivo en un blob de almacenamiento de Azure, debe tener una cuenta de Almacenamiento de Azure. Puede usar una existente o seguir las instrucciones de [Acerca del Almacenamiento de Azure] para crear una. Tome nota de la cadena de conexión de la cuenta de almacenamiento.

## Envío de un URI de blob de Azure al dispositivo simulado

En esta sección, modificará la aplicación de consola **SendCloudtoDevice** que creó en [Envío de mensajes de nube a dispositivo con el Centro de IoT] para incluir un URI del blob de Azure con una firma de acceso compartido. Esto permite que el back-end de nube solo conceda acceso de escritura en el blob al destinatario del mensaje de nube a dispositivo.

1. En Visual Studio, haga clic con el botón derecho el proyecto **SendCloudtoDevice** y luego en **Administrar paquetes de NuGet...**. 

    De este modo aparece la ventana Administrar paquetes de NuGet.

2. Busque `WindowsAzure.Storage`, haga clic en **Instalar** y acepte las condiciones de uso.

    De esta forma, se descarga, instala y agrega una referencia al [SDK de Almacenamiento de Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage/).

3. En el archivo **Program.cs**, agregue las siguientes instrucciones al principio del archivo:

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. En la clase **Program**, agregue los siguientes campos de clase, sustituyendo la cadena de conexión de la cuenta de almacenamiento.

        static string storageConnectionString = "{storage connection string}";

    Luego agregue el siguiente método (puede sustituir cualquier nombre de contenedor de blobs, en este tutorial se usa **iothubfileuploadtutorial**):
   
        private static async Task<string> GenerateBlobUriAsync()
        {
            var storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("iothubfileuploadtutorial");
            await blobContainer.CreateIfNotExistsAsync();

            var blobName = String.Format("deviceUpload_{0}", Guid.NewGuid().ToString());
            CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);

            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
            sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
            sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
            sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            return blob.Uri + sasBlobToken;
        }

    Este método crea una nueva referencia de blob y genera un URI de firma de acceso compartido, tal como se describe en [Creación y uso de una firma de acceso compartido con el servicio BLOB](https://azure.microsoft.com/es-ES/documentation/articles/storage-dotnet-shared-access-signature-part-2/). Tenga en cuenta que el método anterior genera un URI de firma que es válido durante 24 horas. Si el dispositivo de destino necesita más tiempo para cargar el archivo (por ejemplo, se conecta con poca frecuencia, tiene una conectividad poco confiable para cargar un archivo grande), podría plantearse prolongar los tiempos de expiración de las firmas.

5. Modifique el método **SendCloudToDeviceMessageAsync** de la siguiente manera:

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message();
            commandMessage.Properties["command"] = "FileUpload";
            commandMessage.Properties["fileUri"] = await GenerateBlobUriAsync();
            commandMessage.Ack = DeliveryAcknowledgement.Full;

            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    Este método envía un mensaje de nube a dispositivo que contiene dos propiedades de aplicación: una que identifica el mensaje como un comando para cargar un archivo y otra que contiene el URI del blob. También solicita confirmaciones de entrega completa. Tenga en cuenta que la información de las dos propiedades de aplicación se puede serializar en el cuerpo de un mensaje, pero esto conllevaría un procesamiento adicional para serializar y deserializar la información.

<!-- Links -->

[Acerca del Almacenamiento de Azure]: https://azure.microsoft.com/es-ES/documentation/articles/storage-create-storage-account/#create-a-storage-account

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/es-ES/library/hh680901(v=pandp.50).aspx
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->

<!---HONumber=AcomDC_0114_2016-->