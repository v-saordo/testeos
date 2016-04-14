## Envío de mensajes interactivos desde dispositivo simulado

En esta sección, modificará la aplicación de dispositivo simulado que creó en el tutorial [Introducción al Centro de IoT] para enviar mensajes de dispositivo a nube interactivos al Centro de IoT.

1. En Visual Studio, en el proyecto **SimulatedDevice**, agregue el método siguiente a la clase **Program**.

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Thread.Sleep(10000);
      }
    }
    ```

    Este método es muy similar al método **SendDeviceToCloudMessagesAsync** del proyecto **SimulatedDevice**. Las únicas diferencias son que ahora establece la propiedad del sistema **MessageId** y una propiedad de usuario denominada **messageType**. El código asigna un identificador único global (guid) a la propiedad **MessageId**, que el Bus de servicio puede usar para desduplicar los mensajes que recibe. En el ejemplo se usa la propiedad **messageType** para distinguir los mensajes interactivos de los mensajes de punto de datos. La aplicación pasa esta información en las propiedades del mensaje, no en el cuerpo, para que el procesador de eventos no tenga que deserializar el mensaje para realizar su enrutamiento.

    > [AZURE.NOTE]Es importante crear el **MessageId** que se usa para desduplicar mensajes interactivos en el código de dispositivo, ya que las comunicaciones de red intermitentes (u otros errores) podrían provocar varias retransmisiones del mismo mensaje desde el dispositivo. También puede utilizar un identificador de mensaje semántico, como un hash de los campos de datos relevante del mensaje, en lugar de un guid.

2. Agregue el siguiente método en el método **Main** inmediatamente antes de la línea `Console.ReadLine()`:

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE]Para simplificar, en este tutorial no se implementa ninguna directiva de reintentos. En el código de producción, tendrá que implementar una directiva de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Control de errores transitorios].

<!-- Links -->
[Control de errores transitorios]: https://msdn.microsoft.com/es-ES/library/hh675232.aspx
[Introducción al Centro de IoT]: iot-hub-csharp-csharp-getstarted.md

<!---HONumber=AcomDC_0107_2016-->