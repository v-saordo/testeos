<properties
 pageTitle="Soluciones de Azure para EL Internet de las cosas | Microsoft Azure"
 description="Descripción general de IoT en Azure que incluye la arquitectura de una solución de ejemplo y cómo se relaciona con el Centro de IoT de Azure, los SDK de dispositivos y las soluciones preconfiguradas"
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

[AZURE.INCLUDE [iot-azure-and-iot](../../includes/iot-azure-and-iot.md)]

## Pasos siguientes

El Centro de IoT de Azure es un servicio de Azure que permite la comunicación bidireccional fiable y segura entre el back-end de la aplicación y millones de dispositivos. Además, hace posible que el back-end de la aplicación reciba telemetría a escala de los dispositivos, enrute esos datos a un procesador de eventos de transmisión y envíe comandos de nube a dispositivo a dispositivos específicos. Puede usar el Centro de IoT para implementar su propio back-end de soluciones. Además, el Centro de IoT incluye un registro de identidades de dispositivo que se usa para aprovisionar dispositivos, sus credenciales de seguridad y sus derechos para conectarse al Centro. Para obtener más información, consulte:

- [¿Qué es el Centro de IoT?][lnk-iot-hub]
- [Introducción al centro de IoT][lnk-getstarted]

Para implementar aplicaciones cliente que se ejecuten en una gran variedad de plataformas de hardware de dispositivos y sistemas operativos, puede usar los SDK de dispositivos IoT. Los SDK de dispositivos IoT incluyen bibliotecas que facilitan el envío de telemetría a un Centro de IoT y la recepción de comandos de nube a dispositivo. Al usar los SDK, puede elegir entre una serie de protocolos de red para comunicarse con el Centro de IoT. Para más información, vea la [información sobre los SDK de dispositivo][lnk-device-sdks].

También puede interesarle el [Conjunto de aplicaciones de IoT de Azure][lnk-iot-suite], que es una colección de soluciones preconfiguradas. El Conjunto de aplicaciones de IoT le permite comenzar rápidamente y escalar proyectos IoT en escenarios comunes de IoT como, por ejemplo, supervisión remota, administración de activos y mantenimiento predictivo.

[lnk-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_0218_2016-->