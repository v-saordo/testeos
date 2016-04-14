## Creación de una identidad de dispositivo

En esta sección, creará una aplicación de consola de Node.js que crea una nueva identidad de dispositivo en el registro de identidades de su Centro de IoT. No se puede conectar un dispositivo al Centro de IoT a menos que tenga una entrada en el registro de identidades de dispositivo. Consulte la sección **Registro de identidad de dispositivos** de la [Guía para desarrolladores del Centro de IoT][lnk-devguide-identity] para obtener más información. Al ejecutar esta aplicación de consola, genera y una clave y un identificador de dispositivo únicos con el que el dispositivo puede identificarse cuando envía al Centro de IoT mensajes de dispositivo a la nube.

1. Cree una nueva carpeta vacía denominada **createdeviceidentity**. En la carpeta **createdeviceidentity**, cree un nuevo archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:

    ```
    npm init
    ```

2. En el símbolo del sistema en la carpeta **createdeviceidentity**, ejecute el siguiente comando para instalar el paquete **azure-iothub**:

    ```
    npm install azure-iothub --save
    ```

3. Con un editor de texto, cree un nuevo archivo **CreateDeviceIdentity.js** en la carpeta **createdeviceidentity**.

4. Agregue la siguiente `require` instrucción al principio del archivo **CreateDeviceIdentity.js**:

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Agregue el siguiente código al archivo **CreateDeviceIdentity.js**; para ello, sustituya el valor de marcador de posición por la cadena de conexión del Centro de IoT que creó en la sección anterior:

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Agregue el siguiente código para crear una nueva definición de dispositivo en el registro de identidades de dispositivo en el Centro de IoT. Este código crea un nuevo dispositivo si el identificador de dispositivo no existe en el registro o, de lo contrario, devuelve la clave del dispositivo existente:

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. Guarde y cierre el archivo **CreateDeviceIdentity.js**.

8. Para ejecutar la aplicación **createdeviceidentity**, ejecute el siguiente comando en el símbolo del sistema en la carpeta createdeviceidentity:

    ```
    node CreateDeviceIdentity.js 
    ```

9. Tome nota del **identificador de dispositivo** y de la **clave de dispositivo**. Los necesitará más adelante cuando cree una aplicación que se conecta a Centro de IoT como un dispositivo.

> [AZURE.NOTE] El registro de identidades del Centro de IoT solo almacena las identidades de dispositivo para permitir el acceso seguro al centro. Almacena las claves y los identificadores de dispositivo para usarlos como credenciales de seguridad, y un indicador de habilitado o deshabilitado que permite deshabilitar el acceso a un dispositivo individual. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Consulte [Guía para desarrolladores del Centro de IoT][lnk-devguide-identity] para más información.

## Recepción de mensajes de dispositivo a nube

En esta sección, creará una aplicación de consola de Node.js que lee los mensajes de dispositivo a nube del Centro de IoT. El Centro de IoT expone un punto de conexión compatible con [Centros de eventos ][lnk-event-hubs-overview] para poder leer los mensajes de dispositivo a nube. Para simplificar las cosas, este tutorial crea un lector básico que no es apto para una implementación de alta capacidad de procesamiento. El tutorial [Procesamiento de mensajes de dispositivo a la nube][lnk-processd2c-tutorial] muestra cómo procesar mensajes de dispositivo a la nube a escala. El tutorial [Introducción a los Centros de eventos][lnk-eventhubs-tutorial] proporciona información adicional acerca de cómo procesar los mensajes desde los Centros de eventos y se puede aplicar a los puntos de conexión compatibles con Centro de eventos de Centro de IoT.

1. Cree una nueva carpeta vacía llamada **readdevicetocloudmessages**. En la carpeta **readdevicetocloudmessages**, cree un nuevo archivo package.json con el siguiente comando en el símbolo del sistema. Acepte todos los valores predeterminados:

    ```
    npm init
    ```

2. En el símbolo del sistema, en la carpeta **readdevicetocloudmessages**, ejecute el siguiente comando para instalar los paquetes **amqp10** y **bluebird**:

    ```
    npm install amqp10 bluebird --save
    ```

3. Con un editor de texto, cree un nuevo archivo **ReadDeviceToCloudMessages.js** en la carpeta **readdevicetocloudmessages**.

4. Agregue las siguientes `require` instrucciones al principio del archivo **ReadDeviceToCloudMessages.js**:

    ```
    'use strict';

    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```

5. Agregue las siguientes declaraciones de variables, reemplazando los marcadores de posición por los valores que anotó anteriormente. El valor del marcador de posición **{your event hub-compatible namespace}** proviene del **punto de conexión compatible con Centro de eventos** y adopta la forma siguiente: **xxxxnamespace.servicebus.windows.net**.

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] Este código supone que usted creó el Centro de IoT en el nivel de F1 (gratis). Un Centro de IoT gratis tiene dos particiones denominadas "0" y "1". Si creó el Centro de IoT con uno de los demás planes de tarifa, debe ajustar el código para crear una clase **MessageReceiver** para cada partición.

6. Agregue la siguiente definición de filtro. Esta aplicación utiliza un filtro cuando crea el receptor para que este solo lea los mensajes enviados al Centro de IoT después de que el receptor comience a ejecutarse. Esto es útil en un entorno de prueba, porque puede ver el conjunto actual de mensajes, pero en un entorno de producción el código debe asegurarse de que se procesan todos los mensajes. Consulte el tutorial [Procesamiento de mensajes de dispositivo a la nube del Centro de IoT][lnk-processd2c-tutorial] para más información.

    ```
    var filterOffset = new Date().getTime();
    var filterOption;
    if (filterOffset) {
      filterOption = {
      attach: { source: { filter: {
      'apache.org:selector-filter:string': translator(
        ['described', ['symbol', 'apache.org:selector-filter:string'], ['string', "amqp.annotation.x-opt-enqueuedtimeutc > " + filterOffset + ""]])
        } } }
      };
    }
    ```

7. Agregue el código siguiente para crear la dirección de recepción y un cliente AMQP:

    ```
    var uri = protocol + '://' + encodeURIComponent(sasName) + ':' + encodeURIComponent(sasKey) + '@' + eventHubHost;
    var recvAddr = eventHubName + '/ConsumerGroups/$default/Partitions/';
    
    var client = new AMQPClient(Policy.EventHub);
    ```

8. Agregue las siguientes dos funciones que imprimen la salida en la consola:

    ```
    var messageHandler = function (partitionId, message) {
      console.log('Received(' + partitionId + '): ', message.body);
    };
    
    var errorHandler = function(partitionId, err) {
      console.warn('** Receive error: ', err);
    };
    ```

9. Agregue la siguiente función que actúa como un receptor de una partición determinada mediante el filtro:

    ```
    var createPartitionReceiver = function(partitionId, receiveAddress, filterOption) {
      return client.createReceiver(receiveAddress, filterOption)
        .then(function (receiver) {
          console.log('Listening on partition: ' + partitionId);
          receiver.on('message', messageHandler.bind(null, partitionId));
          receiver.on('errorReceived', errorHandler.bind(null, partitionId));
        });
    };
    ```

10. Agregue el código siguiente para conectarse al punto de conexión compatible con el Centro de eventos e inicie los receptores:

    ```
    client.connect(uri)
      .then(function () {
        var partitions = [];
        for (var i = 0; i < numPartitions; ++i) {
          partitions.push(createPartitionReceiver(i, recvAddr + i, filterOption));
        }
        return Promise.all(partitions);
    })
    .error(function (e) {
        console.warn('Connection error: ', e);
    });
    ```

11. Guarde y cierre el archivo **ReadDeviceToCloudMessages.js**.

<!-- Links -->

[lnk-eventhubs-tutorial]: event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: event-hubs-overview.md
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0128_2016-->