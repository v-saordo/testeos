## Creación de una aplicación de dispositivo simulado

En esta sección, creará una aplicación de consola de Windows que simula un dispositivo que envía mensajes de dispositivo a nube a un Centro de IoT.

1. En Visual Studio, agregue un nuevo proyecto de aplicación de escritorio clásico de Windows de Visual C# con la plantilla de proyecto **Aplicación de consola**. Asigne al proyecto el nombre **SimulatedDevice**.

   	![][30]

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **SimulatedDevice** y, a continuación, seleccione **Administrar paquetes de NuGet**.

3. En la ventana **Administrador de paquetes NuGet**, busque **Microsoft Azure Devices Client**, haga clic en **Instalar** y acepte los términos de uso.

	De esta forma, se descarga, instala y agrega una referencia al [paquete de NuGet del SDK de dispositivo de IoT de Azure][lnk-device-nuget].

4. Agregue la siguiente instrucción `using` en la parte superior del archivo **Program.cs**:

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. Agregue los siguientes campos a la clase **Program**; para ello, sustituya los valores de marcador de posición por el nombre de host del Centro de IoT que recuperó en la sección *Creación de un Centro de IoT* y la clave de dispositivo que recuperó en la sección *Creación de una identidad de dispositivo*, respectivamente:

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Agregue el método siguiente a la clase **Program**:

		private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Thread.Sleep(1000);
            }
        }

	Este método envía un nuevo mensaje de dispositivo a nube cada segundo. El mensaje contiene un objeto JSON serializado con el valor de deviceId y un número generado aleatoriamente para simular un sensor de velocidad del viento.

7. Por último, agregue las líneas siguientes al método **Main**:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  De forma predeterminada, el método **Create** crea un **DeviceClient** que usa el protocolo AMQP para comunicarse con el Centro de IoT. Para usar el protocolo HTTPS, use la invalidación del método **Create** que permite especificar el protocolo. Si decide usar el protocolo HTTPS, debe agregar también el paquete de NuGet **Microsoft.AspNet.WebApi.Client** al proyecto para incluir el espacio de nombres **System.Net.Http.Formatting**.


> [AZURE.NOTE] Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Control de errores transitorios][lnk-transient-faults].

<!-- Links -->

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_0204_2016-->