<properties
 pageTitle="Información del Centro de IoT de Azure para profesionales de TI | Microsoft Azure"
 description="Información de ayuda para que los profesionales de TI trabajen con el Centro de IoT de Azure como, por ejemplo, requisitos de puerto e información de seguridad."
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

# Configuración y administración del acceso al Centro de IoT

En este artículo se ofrece información que ayuda a los profesionales de TI a configurar un entorno donde los dispositivos IoT se comuniquen con el Centro de IoT a través de una infraestructura de red.

## Conectividad de red

Los dispositivos pueden comunicarse con el Centro de IoT en Azure mediante una variedad de protocolos. Normalmente, la elección del protocolo se basa en los requisitos específicos de la solución. En la tabla siguiente se indican los puertos de salida que deben estar abiertos para que un dispositivo pueda usar un protocolo concreto:

| Protocolo | Puertos |
| -------- | ------- |
| HTTPS | 443 |
| AMQP | 5671 |
| AMQP sobre WebSockets | 443 |
| MQTT | 8883 |

Una vez creado un Centro de IoT en una región de Azure, el centro mantendrá la misma dirección IP durante toda su existencia. Sin embargo, en un escenario de recuperación ante desastres, si Microsoft mueve el Centro de IoT a una unidad de escalado diferente, se le asignará una nueva dirección IP.

## Centro de IoT y seguridad

Solo los dispositivos registrados con un Centro de IoT están autorizados a comunicarse con ese Centro de IoT. A un dispositivo registrado se le debe conceder el permiso *DeviceConnect*. Un dispositivo se identifica mediante la inclusión de un token que encapsula el identificador único de los dispositivos en cada solicitud que realiza, y el centro comprueba la validez del token y que el dispositivo no está en la lista negra (permiso *DeviceConnect* revocado).

El acceso a otros extremos de administración en un Centro de IoT también se controla a través de un conjunto de permisos: *iothubowner*, *service*, *registryRead*, y *registryReadWrite*. Cualquier aplicación de administración cliente que se conecta a un Centro de IoT debe incluir un token con los permisos adecuados.

## Pasos siguientes

Este artículo contiene información específica para que los desarrolladores y profesionales de TI configuren sus entornos de desarrollo y pruebas. Siga estos vínculos para obtener más información sobre el servicio Centro de IoT de Azure:

- [¿Qué es el Centro de IoT de Azure?][lnk-iothub]
- La [sección de seguridad de la Guía del desarrollador del Centro de IoT de Azure][lnk-devguide] ofrece información adicional sobre los tokens y el sistema de permisos del Centro de IoT.
- [Administración del Centro de IoT a través del Portal de Azure][lnk-manage-portal] describe el uso del Portal de Azure para administrar el Centro de IoT.

[lnk-iothub]: iot-hub-what-is-iot-hub.md
[lnk-devguide]: iot-hub-devguide.md#security
[lnk-manage-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0204_2016-->