## Creación de una aplicación de dispositivo simulado

En esta sección, creará una aplicación de consola de Java que simula un dispositivo que envía mensajes de dispositivo a nube a un Centro de IoT.

1. En la carpeta iot-java-get-started creada en la sección *Creación de una identidad de dispositivo*, cree un nuevo proyecto de Maven denominado **simulated-device** mediante el comando siguiente en la línea de comandos. Tenga en cuenta que es un comando único y largo.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, navegue a la nueva carpeta simulated-device.

3. Con un editor de texto, abra el archivo pom.xml en la carpeta simulated-device y agregue las siguientes dependencias al nodo **dependencies**. Esto le permite usar el paquete iothub-java-client en la aplicación para comunicarse con el Centro de IoT y serializar los objetos de Java a JSON:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.0</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Guarde y cierre el archivo pom.xml.

5. Con un editor de texto, abra el archivo simulated-device\src\main\java\com\mycompany\app\App.java.

6. Agregue las siguientes instrucciones **import** al archivo:

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.Random;
    import javax.naming.SizeLimitExceededException;
    import com.google.gson.Gson;
    ```

7. Agregue las siguientes variables de nivel de clase a la clase **App**, reemplazando **{youriothubname}** por el nombre del Centro de IoT y **{yourdeviceid}** y **{yourdevicekey}** por los valores del dispositivo generados en la sección *Creación de una identidad de dispositivo*:

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQPS;
    private static boolean stopThread = false;
    ```

    Esta aplicación de ejemplo usa la variable **protocol** cuando se crea una instancia de un objeto **DeviceClient**. Puede usar el protocolo HTTPS o AMQPS para comunicarse con el Centro de IoT.

8. Agregue la siguiente clase **TelemetryDataPoint** anidada dentro de la clase **App** para especificar los datos de telemetría que envía el dispositivo al Centro de IoT:

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. Agregue la siguiente clase **EventCallback** anidada dentro de la clase **App** para mostrar el estado de confirmación que devuelve el Centro de IoT cuando procesa un mensaje desde el dispositivo simulado. Este método también notifica el subproceso principal de la aplicación cuando se ha procesado el mensaje:

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Agregue la siguiente clase **MessageSender** anidada dentro de la clase **App**. El método **run** de esta clase genera datos de telemetría de ejemplo para enviar al Centro de IoT y espera una confirmación antes de enviar el mensaje siguiente:

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;

      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          DeviceClient client;
          client = new DeviceClient(connString, protocol);
          client.open();
        
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = "myFirstDevice";
            telemetryDataPoint.windSpeed = currentWindSpeed;
      
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println(msgStr);
        
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
          client.close();
        } catch (Exception e) {
          e.printStackTrace();
        }
      }
    }
    ```

    Este método envía un nuevo mensaje de dispositivo a nube un segundo después de que Centro de IoT reconozca el mensaje anterior. El mensaje contiene un objeto JSON serializado con el valor de deviceId y un número generado aleatoriamente para simular un sensor de velocidad del viento.

11. Reemplace el método **main** con el siguiente código que crea un subproceso para enviar mensajes de dispositivo a nube a su Centro de IoT:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
    
      MessageSender ms0 = new MessageSender();
      Thread t0 = new Thread(ms0);
      t0.start(); 
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      ms0.stopThread = true;
    }
    ```

12. Guarde y cierre el archivo App.java.

13. Para generar la aplicación **simulated-device** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta simulated-device:

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Control de errores transitorios][lnk-transient-faults].

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0204_2016-->