<properties
 pageTitle="Compatibilidad de MQTT con el Centro de IoT | Microsoft Azure"
 description="Descripción de la compatibilidad de MQTT a nivel de Centro de IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# Compatibilidad con MQTT del Centro de IoT

El Centro de IoT permite a los dispositivos comunicarse con los puntos de conexión de dispositivos del Centro de IoT utilizando el protocolo [MQTT v3.1.1][lnk-mqtt-org] en el puerto 8883. El Centro de IoT requiere que toda la comunicación de los dispositivos se proteja mediante TLS/SSL.

## Conexión al Centro de IoT

Un dispositivo puede conectarse a un Centro de IoT mediante el protocolo MQTT, ya sea mediante las bibliotecas en los [SDK de IoT de Microsoft Azure][lnk-device-sdks] o mediante el protocolo MQTT directamente.

## Uso de los SDK de cliente de dispositivo

Los [SDK de cliente de dispositivo][lnk-device-sdks] que admiten el protocolo MQTT están disponibles para Java, Node.js, C y C#. El SDK de cliente de dispositivo utiliza la cadena de conexión de Centro de IoT estándar para establecer una conexión a un Centro de IoT. Para utilizar el protocolo MQTT, el parámetro de protocolo de cliente debe establecerse en **MQTT**. De forma predeterminada, los SDK de cliente de dispositivo se conectan a un Centro de IoT con la marca **CleanSession** establecida en **0** y usan **QoS 1** para el intercambio de mensajes con el Centro de IoT.

Cuando un dispositivo se conecta a un Centro de IoT, los SDK de cliente de dispositivo proporcionan métodos que permiten al dispositivo enviar y recibir mensajes desde un Centro de IoT.

La tabla siguiente contiene vínculos a ejemplos de código para cada idioma admitido y especifica el parámetro que se debe utilizar para establecer una conexión con el Centro de IoT mediante el protocolo MQTT.

| Idioma | Parámetro de protocolo |
| -------------------------- | ------------------------- |
| [Node.js][lnk-sample-node] | azure-iot-device-mqtt |
| [Java][lnk-sample-java] | IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] | MQTT\_Protocol |
| [C#][lnk-sample-csharp] | TransportType.Mqtt |

## Uso del protocolo MQTT directamente

Si un dispositivo no puede usar los SDK de cliente de dispositivo, tendrá la posibilidad de conectarse a los puntos de conexión públicos del dispositivo mediante el protocolo MQTT. En el paquete **CONNECT** el dispositivo debe usar los siguientes valores:

- El valor **deviceId** como **ClientId**
- `{iothubhostname}/{device_id}` en el campo **nombre de usuario**, donde {iothubhostname} es el CName completo del Centro de IoT (por ejemplo, contoso.azure-devices.net).
- Un token SAS en el campo **Contraseña**. El [formato del token SAS][lnk-iothub-security] es el mismo que se describe para los protocolos HTTP y AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`.

Para que MQTT conecte y desconecte paquetes, el Centro de IoT emite un evento en el canal **Supervisión de operaciones**.

### Envío de mensajes al Centro de IoT

Una vez conectado correctamente, un dispositivo puede enviar mensajes al Centro de IoT mediante `devices/{did}/messages/events/` o `devices/{did}/messages/events/{property_bag}` como un **Nombre de tema**. El elemento `{property_bag}` permite al dispositivo enviar mensajes con propiedades adicionales en un formato con codificación url. Por ejemplo:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```
 
> [AZURE.NOTE] Se trata de la misma codificación que se utiliza para las cadenas de consulta en el protocolo HTTP.

La aplicación de cliente de dispositivo también puede utilizar `devices/{did}/messages/events/{property_bag}` como el **nombre del tema Will** para definir los *Mensajes Will* que se reenviarán como un mensaje de telemetría.

### Recepción de mensajes

Para recibir mensajes de un Centro de IoT, un dispositivo debe suscribirse mediante `devices/{did}/messages/devicebound/#”` como un **Filtro de tema**. El Centro de IoT entrega los mensajes con el **Nombre de tema** `devices/{did}/messages/devicebound/`, o `devices/{did}/messages/devicebound/{property_bag}` si hay propiedades de mensaje. `{property_bag}` contiene pares clave-valor con codificación URL de propiedades del mensaje. Las propiedades de la aplicación y del sistema configuradas por el usuario (como **messageId** o **correlationId**) son las únicas que se incluyen en el paquete de propiedades. Los nombres de propiedad del sistema tienen el prefijo **$**, las propiedades de la aplicación utilizan el nombre de propiedad original sin prefijo.

## Pasos siguientes

Para obtener más información acerca del uso de los SDK de cliente de dispositivo para comunicarse con el Centro de IoT, consulte [Introducción al Centro de IoT de Azure][lnk-iot-get-stated].

Para obtener más información sobre el protocolo MQTT, consulte la [documentación de MQTT][lnk-mqtt-docs].

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-iot-get-stated]: iot-hub-csharp-csharp-getstarted.md
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-iothub-security]: iot-hub-devguide.md#security
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples

<!---HONumber=AcomDC_0218_2016-->