## Creación de una identidad de dispositivo

En esta sección, creará una aplicación de consola de Java que crea una nueva identidad de dispositivo en el registro de identidades de su Centro de IoT. No se puede conectar un dispositivo al Centro de IoT a menos que tenga una entrada en el registro de identidades de dispositivo. Consulte la sección **Registro de identidad de dispositivos** de la [Guía para desarrolladores del Centro de IoT][lnk-devguide-identity] para obtener más información. Al ejecutar esta aplicación de consola, genera y una clave y un identificador de dispositivo únicos con el que el dispositivo puede identificarse cuando envía al Centro de IoT mensajes de dispositivo a la nube.

1. Cree una nueva carpeta vacía denominada iot-java-get-started. En la carpeta iot-java-get-started, cree un nuevo proyecto Maven denominado **create-device-identity** mediante el comando siguiente en la línea de comandos. Tenga en cuenta que es un comando único y largo.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, navegue a la nueva carpeta create-device-identity.

3. Con un editor de texto, abra el archivo pom.xml en la carpeta create-device-identity y agregue la siguiente dependencia al nodo **dependencies**. Permite que se use el paquete iothub-service-sdk en su aplicación:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.0</version>
    </dependency>
    ```
    
4. Guarde y cierre el archivo pom.xml.

5. Mediante un editor de texto, abra el archivo create-device-identity\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Agregue las siguientes instrucciones **import** al archivo:

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Agregue las siguientes variables de nivel de clase a la clase **App**, reemplazando **{yourhubname}** y **{yourhubkey}** por los valores indicados anteriormente:

    ```
    private static final String connectionString = "HostName={yourhubname}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={yourhubkey}";
    private static final String deviceId = "javadevice";
    
    ```
    
8. Modifique la firma del método **main** para incluir las excepciones que se muestran a continuación:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Agregue el siguiente código como cuerpo del método **main**. Este código crea un dispositivo denominado *javadevice* en el registro de identidades de Centro de IoT si no existe. Después muestra el identificador de dispositivo y la clave que necesitará más adelante:

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Guarde y cierre el archivo App.java.

11. Para generar la aplicación **create-device-identity** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta create-device-identity:

    ```
    mvn clean package -DskipTests
    ```

12. Para ejecutar la aplicación **create-device-identity** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta create-device-identity:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Tome nota del **identificador de dispositivo** y de la **clave de dispositivo**. Los necesitará más adelante cuando cree una aplicación que se conecta a Centro de IoT como un dispositivo.

> [AZURE.NOTE] El registro de identidades del Centro de IoT solo almacena las identidades de dispositivo para permitir el acceso seguro al centro. Almacena las claves y los identificadores de dispositivo para usarlos como credenciales de seguridad, y un indicador de habilitado o deshabilitado que permite deshabilitar el acceso a un dispositivo individual. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Consulte [Guía para desarrolladores del Centro de IoT][lnk-devguide-identity] para más información.

## Recepción de mensajes de dispositivo a nube

En esta sección, creará una aplicación de consola de Java que lee los mensajes de dispositivo a nube desde el Centro de IoT. El Centro de IoT expone un punto de conexión compatible con [Centros de eventos ][lnk-event-hubs-overview] para poder leer los mensajes de dispositivo a nube. Para simplificar las cosas, este tutorial crea un lector básico que no es apto para una implementación de alta capacidad de procesamiento. El [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-processd2c-tutorial] muestra cómo procesar mensajes de dispositivo a la nube a escala. El tutorial [Introducción a los Centros de eventos][lnk-eventhubs-tutorial] proporciona información adicional acerca de cómo procesar los mensajes desde los Centros de eventos y se puede aplicar a los puntos de conexión compatibles con Centro de eventos de Centro de IoT.

1. En la carpeta iot-java-get-started creada en la sección *Creación de una identidad de dispositivo*, cree un nuevo proyecto de Maven denominado **read-d2c-messages** mediante el comando siguiente en la línea de comandos. Tenga en cuenta que es un comando único y largo.

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. En el símbolo del sistema, navegue a la nueva carpeta read-d2c-messages.

3. Con un editor de texto, abra el archivo pom.xml en la carpeta read-d2c-messages y agregue la siguiente dependencia al nodo **dependencies**. Esto permite usar el paquete eventhubs-client en la aplicación para leer desde el punto de conexión compatible con Centros de eventos:

    ```
    <dependency>
      <groupId>com.microsoft.eventhubs.client</groupId>
      <artifactId>eventhubs-client</artifactId>
      <version>1.0</version>
    </dependency>
    ```

4. Guarde y cierre el archivo pom.xml.

5. Con un editor de texto, abra el archivo read-d2c-messages\\src\\main\\java\\com\\mycompany\\app\\App.java.

6. Agregue las siguientes instrucciones **import** al archivo:

    ```
    import java.io.IOException;
    import com.microsoft.eventhubs.client.Constants;
    import com.microsoft.eventhubs.client.EventHubClient;
    import com.microsoft.eventhubs.client.EventHubEnqueueTimeFilter;
    import com.microsoft.eventhubs.client.EventHubException;
    import com.microsoft.eventhubs.client.EventHubMessage;
    import com.microsoft.eventhubs.client.EventHubReceiver;
    import com.microsoft.eventhubs.client.ConnectionStringBuilder;
    ```

7. Agregue las siguientes variables de nivel de clase a la clase **App**.

    ```
    private static EventHubClient client;
    private static long now = System.currentTimeMillis();
    ```

8. Agregue la siguiente clase anidada dentro de la clase **App**. La aplicación crea dos subprocesos para ejecutar la clase **MessageReceiver** para leer mensajes de las dos particiones del Centro de eventos:

    ```
    private static class MessageReceiver implements Runnable
    {
        public volatile boolean stopThread = false;
        private String partitionId;
    }
    ```

9. Agregue el siguiente constructor a la clase **MessageReceiver**:

    ```
    public MessageReceiver(String partitionId) {
        this.partitionId = partitionId;
    }
    ```

10. Agregue el siguiente método **run** a la clase **MessageReceiver**. Este método crea una instancia de **EventHubReceiver** para leer desde una partición de Centro de eventos. Reproduce en bucle continuo e imprime los detalles del mensaje en la consola de imprime hasta que la clase **stopThread** sea true.

    ```
    public void run() {
      try {
        EventHubReceiver receiver = client.getConsumerGroup(null).createReceiver(partitionId, new EventHubEnqueueTimeFilter(now), Constants.DefaultAmqpCredits);
        System.out.println("** Created receiver on partition " + partitionId);
        while (!stopThread) {
          EventHubMessage message = EventHubMessage.parseAmqpMessage(receiver.receive(5000));
          if(message != null) {
            System.out.println("Received: (" + message.getOffset() + " | "
                + message.getSequence() + " | " + message.getEnqueuedTimestamp()
                + ") => " + message.getDataAsString());
          }
        }
        receiver.close();
      }
      catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
      }
    }
    ```

    > [AZURE.NOTE] Este método utiliza un filtro cuando crea el receptor para que este solo lea los mensajes enviados al Centro de IoT después de que el receptor comience a ejecutarse. Esto es útil en un entorno de prueba, por que puede ver el conjunto actual de mensajes, pero en un entorno de producción el código debe asegurarse de que se procesan todos los mensajes. Consulte el [Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-processd2c-tutorial] para obtener más información.

11. Modifique la firma del método **main** para incluir las excepciones que se muestran a continuación:

    ```
    public static void main( String[] args ) throws IOException
    ```

12. Agregue el siguiente código al método **main** en la clase **App**. Este código crea una instancia de **EventHubClient** para conectarse al punto de conexión compatible del Centro eventos en Centro de IoT. Después, crea dos subprocesos para leer de las dos particiones. Reemplace **{youriothubkey}**, **{youreventhubcompatiblenamespace}** y **{youreventhubcompatiblename}** por los valores indicados anteriormente. El valor del marcador de posición **{youreventhubcompatiblenamespace}** proviene del **punto de conexión compatible con el Centro de eventos** y adopta la forma siguiente: **xxxxnamespace** (dicho de otro modo, quite el prefijo ****sb://** y el sufijo **.servicebus.windows.net** del valor de punto de conexión compatible con el Centro de eventos del portal).

    ```
    String policyName = "iothubowner";
    String policyKey = "{youriothubkey}";
    String namespace = "{youreventhubcompatiblenamespace}";
    String name = "{youreventhubcompatiblename}";
    try {
      ConnectionStringBuilder csb = new ConnectionStringBuilder(policyName, policyKey, namespace);
      client = EventHubClient.create(csb.getConnectionString(), name);
    }
    catch(EventHubException e) {
        System.out.println("Exception: " + e.getMessage());
    }
    
    MessageReceiver mr0 = new MessageReceiver("0");
    MessageReceiver mr1 = new MessageReceiver("1");
    Thread t0 = new Thread(mr0);
    Thread t1 = new Thread(mr1);
    t0.start(); t1.start();

    System.out.println("Press ENTER to exit.");
    System.in.read();
    mr0.stopThread = true;
    mr1.stopThread = true;
    client.close();
    ```

    > [AZURE.NOTE] Este código supone que usted creó el Centro de IoT en el nivel de F1 (gratis). Un Centro de IoT gratis tiene dos particiones denominadas "0" y "1". Si creó el Centro de IoT con otro plan de tarifa, debe ajustar el código para crear una clase **MessageReceiver** para cada partición.

13. Guarde y cierre el archivo App.java.

14. Para generar la aplicación **read-d2c-messages** con Maven, ejecute el siguiente comando en el símbolo del sistema en la carpeta read-d2c-messages:

    ```
    mvn clean package -DskipTests
    ```



<!-- Links -->

[lnk-eventhubs-tutorial]: event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: event-hubs-overview.md
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0224_2016-->