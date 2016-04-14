<properties
 pageTitle="Guía de soluciones del Centro de IoT | Microsoft Azure"
 description="Temas de guía sobre las puertas de enlace, el aprovisionamiento de dispositivos y la autenticación para desarrollar soluciones de IoT con el Centro de IoT de Azure."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# Diseño de la solución

Este artículo le ofrece una guía para diseñar las siguientes capacidades en la solución de Internet de las cosas (IoT):

- Aprovisionamiento de dispositivos
- Puertas de enlace de campo
- Autenticación de dispositivos

## Aprovisionamiento de dispositivos

Las soluciones de IoT almacenan datos acerca de los dispositivos individuales, como:

- Claves de autenticación e identidad de dispositivo
- Versión y tipo de dispositivo de hardware
- Estado del dispositivo
- Capacidades y versiones del software
- Historial de comandos de dispositivo

Los datos del dispositivo que almacena una solución de IoT determinada dependen de los requisitos específicos de la solución. Sin embargo, como mínimo, una solución debe almacenar identidades del dispositivo y claves de autenticación. El Centro de IoT incluye un [registro de identidades][lnk-devguide-identityregistry] que puede almacenar valores para cada dispositivo como identificadores, claves de autenticación y códigos de estado. Una posible solución consiste en usar otros servicios de Azure como tablas, blobs o DocumentDB de Azure para almacenar datos de dispositivos adicionales.

*Aprovisionamiento de dispositivos* es el proceso de agregar los datos iniciales del dispositivo a los almacenes de la solución. Para habilitar un nuevo dispositivo para conectarse al centro, debe agregar un nuevo identificador de dispositivo y claves al [registro de identidades del Centro de IoT][lnk-devguide-identityregistry]. Como parte del proceso de aprovisionamiento, es posible que tenga que inicializar datos específicos del dispositivo en otros almacenes de la solución.

El artículo [Guía de administración de dispositivos del Centro de IoT][lnk-device-management] describe algunas estrategias habituales para el aprovisionamiento de dispositivos. Las [API de registro de identidad del Centro de IoT][lnk-devguide-identityregistry] le permiten integrarlo en el proceso de aprovisionamiento.

## Puertas de enlace de campo

En una solución de IoT, un *puerta de enlace de campo* se encuentra entre los dispositivos y el Centro de IoT. Suele encontrarse cerca de los dispositivos. Los dispositivos se comunican directamente con la puerta de enlace de campo mediante un protocolo compatible con los dispositivos. La puerta de enlace de campo se comunica con el Centro de IoT mediante un protocolo que es compatible con el Centro de IoT. Una puerta de enlace de campo puede ser un dispositivo independiente especializado o un software que se ejecuta en un hardware ya existente.

Una puerta de enlace de campo es diferente de un dispositivo de enrutamiento de tráfico simple (como un firewall o un dispositivo de traducción de direcciones de red (NAT)) porque normalmente desempeña un rol activo en la administración del acceso y del flujo de la información en su solución. Por ejemplo, una puerta de enlace de campo puede:

- Administrar dispositivos locales. Por ejemplo, una puerta de enlace de campo puede llevar a cabo procesamiento de reglas de evento y enviar comandos a los dispositivos en respuesta a datos específicos de telemetría.
- Filtrar o agregar los datos de telemetría antes de que los reenvíe al Centro de IoT. Esto puede reducir la cantidad de datos que se envían al Centro de IoT y los costos de la solución.
- Ayudar a aprovisionar dispositivos.
- Transformar los datos de telemetría para facilitar el procesamiento en el back-end de solución.
- Realizar la conversión del protocolo para que los dispositivos puedan comunicarse con el Centro de IoT, incluso cuando no usan los protocolos de transporte que este admite.

> [AZURE.NOTE] Aunque normalmente se implementa una puerta de enlace de campo local en sus dispositivos, en algunos casos podría implementar una [puerta de enlace de protocolo][lnk-gateway] en la nube.

### Tipos de puertas de enlace de campo

Una puerta de enlace de campo puede ser *transparente* u *opaca*:

| &nbsp; | Puerta de enlace transparente | Puerta de enlace opaca|
|--------|-------------|--------|
| Identidades que se almacenan en el registro de identidades del Centro de IoT | Identidades de todos los dispositivos conectados | Solo la identidad de la puerta de enlace de campo |
| El Centro de IoT puede proporcionar [protección contra la suplantación de identidad del dispositivo][lnk-devguide-antispoofing] | Sí | No |
| [Limitaciones y cuotas][lnk-throttles-quotas] | Se aplican a cada dispositivo | Se aplican a la puerta de enlace de campo |

> [AZURE.IMPORTANT]  Cuando se utiliza un patrón de puerta de enlace opaco, todos los dispositivos que se conectan a través de dicha puerta de enlace comparten la misma cola de nube a dispositivo, que puede contener como máximo 50 mensajes. Se supone que el patrón de puerta de enlace opaco debe utilizarse únicamente cuando muy pocos dispositivos se conectan a través de cada puerta de enlace de campo y su tráfico de nube a dispositivo es bajo.

### Otras consideraciones

Puede usar los [SDK de dispositivo IoT de Azure][lnk-device-sdks] para implementar una puerta de enlace de campo. Algunos SDK de dispositivo ofrecen una funcionalidad específica que le ayuda a implementar una puerta de enlace de campo, como la capacidad de multiplexar la comunicación de varios dispositivos en la misma conexión al Centro de IoT. Tal y como se explica en la [Guía para el desarrollador del Centro de IoT - Elegir el protocolo de comunicación][lnk-devguide-protocol], debe evitar usar HTTP/1 como protocolo de transporte para puertas de enlace de campo.

## Personalización de la autenticación de dispositivos

Puede usar el [registro de identidades de dispositivo][lnk-devguide-identityregistry] del Centro de IoT para configurar las credenciales de seguridad de cada dispositivo y el control de acceso. Sin embargo, si una solución IoT ya realizó una inversión importante en un registro personalizado de identidades de dispositivo o en un esquema de autenticación, puede integrar esta infraestructura ya existente con el Centro de IoT mediante la creación de un *servicio de token*. De este modo, puede usar otras características de IoT en la solución.

Un servicio de token es un servicio en la nube personalizado. Usa una *directiva de acceso compartido* del Centro de IoT con permisos **DeviceConnect** para crear tokens *centrados en el dispositivo*. Estos tokens permiten que un dispositivo se conecte al Centro de IoT.

  ![Pasos del modelo de servicio de tokens][img-tokenservice]

Estos son los pasos principales del modelo de servicio de tokens:

1. Cree una [directiva de acceso compartido del Centro de IoT][lnk-devguide-security] con permisos **DeviceConnect** para el Centro de IoT. Puede crear esta directiva en el [portal de Azure][lnk-portal] o mediante programación. El servicio de tokens usará esta directiva para firmar los tokens que crea.
2. Cuando un dispositivo necesita tener acceso al Centro de IoT, solicita un token firmado al servicio de tokens. El dispositivo se puede autenticar mediante el esquema de autenticación o registro de identidades de dispositivos personalizado para determinar la identidad del dispositivo que el servicio de token usa para crear el token.
3. El servicio de token devuelve un token. El token se crea según la [sección de seguridad de la Guía del desarrollador del Centro de IoT][lnk-devguide-security] utilizando `/devices/{deviceId}` como `resourceURI`, con `deviceId` como el dispositivo que se autentica. El servicio de token usa la directiva de acceso compartido para construir el token.
4. El dispositivo usa el token directamente con el Centro de IoT.

> [AZURE.NOTE] Puede usar la clase .NET [SharedAccessSignatureBuilder][lnk-dotnet-sas] o la clase de Java [IotHubServiceSasToken][lnk-java-sas] para crear un token en el servicio correspondiente.

El servicio de token puede establecer la caducidad de los tokens como desee. Cuando expira el token, el Centro de IoT interrumpe la conexión de dispositivo. A continuación, el dispositivo debe solicitar un nuevo token al servicio de token. Si usa un tiempo de expiración corto, aumentará la carga tanto en el dispositivo como en el servicio de token.

Para que un dispositivo se conecte al centro, deberá agregarlo al registro de identidades del dispositivo del Centro de IoT aunque ya use un token y no una clave de dispositivo para conectarse. Por tanto, puede continuar usando el control de acceso por dispositivo habilitando o deshabilitando las identidades del dispositivo en el [registro de identidades de dispositivo del Centro de IoT][lnk-devguide-identityregistry] en aquellos casos en los que el dispositivo se autentique con un token. Esto mitiga los riesgos de usar tokens con tiempos de expiración largos.

### Comparación con una puerta de enlace personalizada

El modelo de servicio de token es el método recomendado para implementar un esquema de autenticación o registro de identidades personalizado en el Centro de IoT. Se recomienda porque el Centro de IoT sigue controlando la mayoría del tráfico de la solución. Hay casos, sin embargo, en los que el esquema de autenticación personalizado está tan imbricado con el protocolo que se necesita un servicio que procese todo el tráfico (*puerta de enlace personalizada*). Un ejemplo de esto es la [seguridad de la capa de transporte (TLS) y las claves previamente compartidas (PSK)][lnk-tls-psk]. Consulte el tema [Puerta de enlace de protocolos][lnk-gateway] para más información.

## Latido de dispositivo <a id="heartbeat"></a>

El [registro de la identidad de Centro de IoT][lnk-devguide-identityregistry] contiene un campo llamado **connectionState**. Solo debe utilizar el campo **connectionState** durante el desarrollo y la depuración, las soluciones de IoT no deben consultar el campo en tiempo de ejecución (por ejemplo, para comprobar si un dispositivo está conectado con el fin de decidir si enviar un mensaje de nube a dispositivo o un SMS). Si la solución de IoT necesita saber si un dispositivo está conectado (ya sea en tiempo de ejecución, o con más precisión que la ofrecida por la propiedad **connectionState**), debe implementar el *patrón de latido*.

En el patrón de latido, el dispositivo envía mensajes de dispositivo a la nube al menos una vez en un período de tiempo predeterminado (por ejemplo, al menos una vez cada hora). Esto significa que incluso si un dispositivo no tiene ningún dato que enviar, seguirá enviando un mensaje de dispositivo a la nube vacío (normalmente con una propiedad que lo identifica como un latido). En el lado del servicio, la solución mantiene un mapa con el último latido recibido para cada dispositivo, y supone que hay un problema con un dispositivo si no recibe un mensaje de latido en el tiempo esperado.

Una implementación más compleja podría incluir la información de [supervisión de operaciones][lnk-devguide-opmon] para identificar los dispositivos que están intentando conectarse o comunicarse sin éxito. Al implementar el patrón de latidos, asegúrese de comprobar [las cuotas y limitaciones de Centro de IoT][].

## Pasos siguientes

Siga estos vínculos para obtener más información sobre el Centro de IoT de Azure:

- [Introducción al Centro de IoT (Tutorial)][lnk-get-started]
- [¿Qué es el Centro de IoT de Azure?][lnk-what-is-hub]

[img-tokenservice]: ./media/iot-hub-guidance/tokenservice.png

[lnk-devguide-identityregistry]: iot-hub-devguide.md#identityregistry
[lnk-device-management]: iot-hub-device-management.md
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-device-sdks]: iot-hub-sdks-summary.md
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-gateway]: iot-hub-protocol-gateway.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-what-is-hub]: iot-hub-what-is-iot-hub.md
[lnk-portal]: https://portal.azure.com
[lnk-throttles-quotas]: ../azure-subscription-service-limits.md/#iot-hub-limits
[lnk-devguide-antispoofing]: iot-hub-devguide.md#antispoofing
[lnk-devguide-protocol]: iot-hub-devguide.md#amqpvshttp
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[las cuotas y limitaciones de Centro de IoT]: iot-hub-devguide.md#throttling

<!---HONumber=AcomDC_0204_2016-->