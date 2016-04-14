<properties
 pageTitle="Información general del Centro de IoT de Azure | Microsoft Azure"
 description="Información general del servicio Centro de IoT de Azure que incluye la conectividad de dispositivos, los patrones de comunicación y el patrón de comunicación asistida por servicios"
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# ¿Qué es el centro de IoT de Azure?

Bienvenido al Centro de IoT de Azure. En este artículo se ofrece información general sobre el Centro de IoT de Azure y se describe por qué debería usar este servicio cuando se implementa una solución de Internet de las cosas (IoT).

El Centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y un back-end de soluciones. Centro de IoT de Azure:

- Ofrece una mensajería confiable de dispositivo a nube y de nube a dispositivo a escala.
- Habilita las comunicaciones seguras con las credenciales de seguridad de cada dispositivo y el control de acceso.
- Proporciona una supervisión exhaustiva para la conectividad de dispositivos y los eventos de administración de identidad de dispositivos.
- Incluye bibliotecas de dispositivos para las plataformas y los lenguajes más populares.

![El centro de IoT como puerta de enlace de nube][img-architecture]

## Problemas de conectividad de dispositivos IoT

El Centro de IoT y las bibliotecas de dispositivo le ayudan a superar los desafíos derivados de la conexión confiable y segura de dispositivos al back-end de soluciones. Dispositivos IoT:

- A menudo son sistemas insertados sin operador humano.
- Pueden encontrarse en ubicaciones remotas, donde el acceso físico resulta muy costoso.
- Es posible que solo sean accesibles a través del back-end de soluciones.
- Es posible que tengan limitaciones de recursos de procesamiento y alimentación.
- Es posible que tengan conectividad de red intermitente, lenta o costosa.
- Es posible que necesiten usar protocolos de aplicación propios, personalizados o específicos de determinados sectores.
- Pueden crearse mediante un amplio conjunto de plataformas populares de hardware y software.

Además de los requisitos anteriores, toda solución de IoT debe ser capaz de ofrecer escalabilidad, seguridad y fiabilidad. Esto da lugar a un conjunto de requisitos de conectividad cuya implementación resulta compleja y lenta si se realiza con tecnologías tradicionales como, por ejemplo, los contenedores web y los agentes de mensajería.

## ¿Por qué usar el centro de IoT de Azure?

El Centro de IoT de Azure aborda las dificultades de conectividad de dispositivos de las maneras siguientes:

-   **Autenticación por dispositivo y conectividad segura**. Puede aprovisionar cada dispositivo con su propia clave de seguridad para permitirle conectarse al Centro de IoT. El [registro de identidades del Centro de IoT][lnk-devguide-identityregistry] almacena identidades y claves en una solución. Un back-end de soluciones puede crear listas blancas y negras de dispositivos individuales, lo que permite controlar por completo el acceso a los dispositivos.

-   **Supervisión de operaciones de conectividad del dispositivo**. Puede recibir registros de operación detallados sobre operaciones de administración de identidad de dispositivos y eventos de conectividad de dispositivos. Esto permite que la solución de IoT identifique fácilmente los problemas de conectividad, como los dispositivos que intentan conectarse con credenciales incorrectas, envían mensajes con demasiada frecuencia o rechazan todos los mensajes de la nube al dispositivo.

-   **Amplio conjunto de bibliotecas de dispositivos**. Los SDK de dispositivos de IoT de Azure están disponibles y son compatibles con una amplia gama de lenguajes y plataformas: C para muchas distribuciones de Linux, Windows y sistemas operativos en tiempo real. Los SDK de dispositivos IoT de Azure admiten lenguajes administrados como C#, Java y JavaScript.

-   **Extensibilidad y protocolos de IoT**. Si la solución no puede hacer uso de las bibliotecas de dispositivos, el Centro de IoT expone un protocolo público que permite a los dispositivos usar los protocolos MQTT v3.1.1, HTTP 1.1 o AMQP 1.0 de forma nativa. También puede ampliar el Centro de IoT para ofrecer compatibilidad para el protocolo personalizado mediante la personalización de la [puerta de enlace de protocolos de IoT de Azure][protocol-gateway]. Puede ejecutar la puerta de enlace de protocolo de IoT de Azure en la nube o de manera local.

-   **Escalabilidad.** El centro de IoT de Azure se puede escalar a millones de dispositivos conectados de manera simultánea y a millones de eventos por segundo.

Estas ventajas son genéricas para varios patrones de comunicación. El Centro de IoT actualmente permite implementar los siguientes patrones de comunicación específicos:

-   **Ingestión de dispositivos a nube basada en eventos.** El Centro de IoT puede recibir de manera confiable millones de eventos por segundo de los dispositivos. A continuación, puede procesarlos en la ruta de acceso activa mediante un motor procesador de eventos. También puede almacenarlos en su ruta de acceso no activa para someterlos a análisis. El Centro de IoT conserva los datos de eventos hasta siete días para garantizar un procesamiento fiable y absorber picos de carga.

-   **Mensajería fiable de nube a dispositivo (o *comandos*).** El back-end de soluciones puede usar el Centro de IoT para enviar mensajes con garantía de entrega al menos una vez a dispositivos individuales. Cada mensaje tiene una configuración de período de vida individual y el back-end puede solicitar confirmación de entrega y vencimiento. Esto garantiza una visibilidad completa en el ciclo de vida de un mensaje de la nube al dispositivo. Luego puede implementar la lógica de negocios que incluye las operaciones que se ejecutan en dispositivos.

También puede implementar otros patrones comunes, como la carga y descarga de archivos, aprovechando las ventajas de las características específicas de IoT del Centro de IoT. Estas características incluyen la administración de identidades de dispositivo uniforme, la supervisión de conectividad y la escalabilidad.

En el artículo [Comparación del Centro de IoT y los Centros de eventos][lnk-compare] se describen las diferencias clave entre estos dos servicios y se resaltan las ventajas del uso del Centro de IoT en sus soluciones de IoT.

## Puertas de enlace

Una puerta de enlace en una solución IoT es normalmente una [puerta de enlace de protocolo][lnk-gateway] implementada en la nube o una [puerta de enlace de campo][lnk-field-gateway] implementada localmente con sus dispositivos. Una puerta de enlace de protocolo realiza la traducción de protocolos, por ejemplo, de MQTT a AMQP. Una puerta de enlace de campo proporciona servicios de administración local para los dispositivos. Puede ser un dispositivo o software especializado que se ejecuta en un hardware existente. Ambos tipos de puerta de enlace actúan como intermediarios entre los dispositivos y el Centro de IoT.

Una puerta de enlace de campo es diferente de un dispositivo de enrutamiento de tráfico simple (como un firewall o un dispositivo de traducción de direcciones de red (NAT)) porque normalmente desempeña un rol activo en la administración del acceso y del flujo de la información en su solución.

Una solución puede incluir tanto puertas de enlace de protocolo como de campo.

## ¿Cómo funciona el Centro de IoT?

El Centro de IoT de Azure implementa el modelo de [comunicación asistida por servicio][lnk-service-assisted-pattern] para mediar en las interacciones entre los dispositivos y su back-end de soluciones. El objetivo de la comunicación asistida por servicio es establecer rutas de acceso de comunicación bidireccional de confianza entre un sistema de control (como el Centro de IoT) y los dispositivos con una finalidad específica implementados en espacios físicos que no son de confianza. El patrón establece los principios siguientes:

- La seguridad tiene prioridad sobre el resto de las funciones.
- Los dispositivos no aceptan información de redes no solicitadas. Un dispositivo establece todas las conexiones y las rutas en modo de solo salida. Para que un dispositivo reciba comandos desde el back-end, dicho dispositivo debe iniciar una conexión con regularidad para comprobar si hay comandos pendientes de procesar.
- Los dispositivos solo deben conectarse o establecer rutas a servicios conocidos a los que están emparejados, como un Centro de IoT.
- La ruta de comunicación entre el dispositivo y el servicio o el dispositivo y puerta de enlace se protege en el nivel del protocolo de aplicaciones.
- La autenticación y la autorización de nivel de sistema se basan en las identidades de cada dispositivo. Hacen los permisos y las credenciales de acceso revocables casi al instante.
- La comunicación bidireccional de dispositivos con conexión esporádica debido a problemas de alimentación o de conectividad puede realizarse mediante el mantenimiento de comandos y notificaciones en los dispositivos hasta que uno de ellos se conecte para recibirlos. El Centro de IoT mantiene colas específicas de dispositivos para los comandos que envía.
- Los datos de carga de aplicaciones se protegen por separado para proteger el tránsito a través de las puertas de enlace a un servicio determinado.

El sector móvil ha usado correctamente el patrón de comunicación asistida por servicio a gran escala para implementar servicios de notificación push como, por ejemplo, los [Servicios de notificaciones de inserción de Windows ][lnk-wns], el [Servicio de mensajería en la nube de Google][lnk-google-messaging] y el [Servicio de notificaciones push de Apple][lnk-apple-push].

## Pasos siguientes

Para obtener más información sobre el Centro de IoT de Azure, consulte estos vínculos:

* [Introducción al centro de IoT][lnk-get-started]
* [Conectar el dispositivo][lnk-connect-device]
* [Procesamiento de mensajes de dispositivo a la nube][lnk-d2c]

[img-architecture]: media/iot-hub-what-is-iot-hub/hubarchitecture.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-d2c]: iot-hub-csharp-csharp-process-d2c.md
[protocol-gateway]: https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md
[lnk-service-assisted-pattern]: http://blogs.msdn.com/b/clemensv/archive/2014/02/10/service-assisted-communication-for-connected-devices.aspx "Comunicación asistida por servicios, publicación de blog de Clemens Vasters"
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-gateway]: iot-hub-protocol-gateway.md
[lnk-field-gateway]: iot-hub-guidance.md#field-gateways
[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-wns]: https://msdn.microsoft.com/library/windows/apps/mt187203.aspx
[lnk-google-messaging]: https://developers.google.com/cloud-messaging/
[lnk-apple-push]: https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9

<!---HONumber=AcomDC_0218_2016-->