## Creación de una aplicación de dispositivo simulado

En esta sección, creará una aplicación de consola de Node.js que simula un dispositivo que envía mensajes de dispositivo a nube a un Centro de IoT.

1. Cree una nueva carpeta vacía denominada **simulateddevice**. En la carpeta **simulateddevice**, cree un nuevo archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:

    ```
    npm init
    ```

2. En el símbolo del sistema en la carpeta **simulateddevice**, ejecute el siguiente comando para instalar el paquete **azure-iot-device-amqp**:

    ```
    npm install azure-iot-device-amqp --save
    ```

3. Con un editor de texto, cree un nuevo archivo **SimulatedDevice.js** en la carpeta **simulateddevice**.

4. Agregue las siguientes `require` instrucciones al principio del archivo **SimulatedDevice.js**:

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Agregue una variable **connectionString** y utilícela para crear un cliente de dispositivo. Reemplace **{youriothubname}** por el nombre del Centro de IoT y **{yourdeviceid}** y **{yourdevicekey}** por los valores del dispositivo generados en la sección *Creación de una identidad de dispositivo*:

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Agregue la siguiente función para mostrar la salida de la aplicación:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ': message sent');
      };
    }
    ```

7. Utilice la función **setInterval** para enviar un mensaje nuevo al Centro de IoT cada segundo:

    ```
    setInterval(function(){
      var windSpeed = 10 + (Math.random() * 4);
      var data = JSON.stringify({ deviceId: 'myFirstDevice', windSpeed: windSpeed });
      var message = new Message(data);
      console.log("Sending message: " + message.getData());
      client.sendEvent(message, printResultFor('send'));
    }, 1000);
    ```

8. Guarde y cierre el archivo **SimulatedDevice.js**.

> [AZURE.NOTE] Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Control de errores transitorios][lnk-transient-faults].

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/es-ES/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0128_2016-->