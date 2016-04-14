## Recepción de mensajes en el dispositivo simulado

En esta sección, modificará la aplicación de dispositivo simulado que creó en [Introducción al Centro de IoT] para recibir mensajes de nube a dispositivo del Centro de IoT.

1. En Visual Studio, en el proyecto **SimulatedDevice**, agregue el método siguiente a la clase **Program**.

        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    El método `ReceiveAsync` devuelve de forma asincrónica el mensaje recibido en el momento en que lo recibe el dispositivo. Devuelve *null* tras un período de tiempo de espera que se puede especificar (en este caso se usa el valor predeterminado, 1 minuto). Cuando esto sucede, queremos que el código siga esperando nuevos mensajes. Este es el motivo para la línea `if (receivedMessage == null) continue`.

    La llamada a `CompleteAsync()` notifica al Centro de IoT que el mensaje se procesó correctamente y que puede quitarse de forma segura de la cola del dispositivo. Si sucedió algo que impidió que la aplicación de dispositivo completase el procesamiento del mensaje, el Centro de IoT volverá a entregarlo; es importante que la lógica de procesamiento de mensajes de la aplicación de dispositivo sea *idempotente*, es decir, si recibe varias veces el mismo mensaje debe producir el mismo resultado. Una aplicación también puede `Abandon` un mensaje temporalmente, lo que hará que el Centro de IoT lo retenga en la cola para su uso en el futuro; o `Reject` un mensaje, lo que lo quitará definitivamente de la cola. Consulte la [Guía para desarrolladores del Centro de IoT][IoT Hub Developer Guide - C2D] para obtener más información sobre el ciclo de vida de los mensajes de nube a dispositivo.

> [AZURE.NOTE]Cuando se usa HTTP/1 en lugar de AMQP como transporte, el `ReceiveAsync` se devolverá inmediatamente. El patrón admitido para los mensajes de nube a dispositivo con HTTP/1 es dispositivos conectados de forma intermitente que buscan mensajes con poca frecuencia (es decir, menos de 25 minutos). Emitir más recepciones HTTP/1 tendrá como resultado la limitación de solicitudes del Centro de IoT. Consulte la [Guía para desarrolladores del Centro de IoT][IoT Hub Developer Guide - C2D] para obtener más información sobre las diferencias entre la compatibilidad con AMQP y HTTP/1, y la limitación del Centro de IoT.

2. Agregue el siguiente método en el método **Main** inmediatamente antes de la línea `Console.ReadLine()`:

        ReceiveC2dAsync();

> [AZURE.NOTE]Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, se recomienda implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal como se sugiere en el artículo MSDN [control de errores transitorios].

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->

<!---HONumber=Nov15_HO2-->