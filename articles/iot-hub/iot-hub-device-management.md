<properties
 pageTitle="Administración de dispositivos IoT | Microsoft Azure"
 description="Visión general del uso del Centro de IoT y el Conjunto de IoT para administrar sus dispositivos IoT"
 services="iot-hub,iot-suite"
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
 ms.date="01/21/2016"
 ms.author="dobett"/>

# Administración de dispositivos IoT usando Suite IoT de Azure y Centro de IoT de Azure

[Conjunto de aplicaciones de IoT de Azure][lnk-iot-suite] y Centro de IoT de Azure proporcionan las capacidades fundamentales para habilitar la administración de dispositivos para soluciones IoT a escala y para todo un conjunto de dispositivos y topologías de dispositivo. Las referencias que contienen este artículo sobre administración de dispositivos están relacionadas de forma específica con la administración de dispositivos IoT.

## Introducción

Las empresas y los proveedores de servicios o cualquier organización que mantenga una población de dispositivos IoT, utilizan las funcionalidades de administración de los dispositivos para realizar los siguientes procesos empresariales:

* Inscribir y detectar dispositivos IoT.
* Habilitar la conectividad.
* Configurar y actualizar el software en los dispositivos de forma remota. Por ejemplo, para administrar dispositivos de plantas de fabricación o yacimientos petrolíferos, hay directivas para configurar y actualizar dispositivos de forma remota, con cadenas de aprobación, requisitos de auditoría a nivel reglamentario, presencia de medidas de seguridad físicas y más.

Cuando se trate de habilitar la administración de dispositivos IoT para su solución IoT, tenga en cuenta las siguientes capacidades y determine la importancia de cada una tiene en relación con sus objetivos empresariales:

* **[Detección y aprovisionamiento de dispositivos](#device-provisioning-and-discovery)**: proceso por el cual un dispositivo se inscribe en el sistema.
* **[Registro de dispositivos y modelos de dispositivo](#device-registry-and-device-models)**: la forma en la que los modelos de dispositivo representan el uso estructurado de metadatos para las relaciones de dispositivo, roles en el sistema y métodos de comprobación.
* **[Administración de acceso de dispositivo](#device-access-management)**: la forma en la que los dispositivos controlan el acceso a los recursos de dispositivo desde los servicios en la nube.
* **[Control remoto](#remote-control)**: la forma en la que los usuarios remotos obtienen acceso a los dispositivos y dan instrucciones a los dispositivos para cambiar.
* **[Administración remota](#remote-administration-and-monitoring)**: proceso por el cual un administrador define el estado de la población de dispositivos.  
* **[Configuración remota](#remote-configuration)**: método que usan los administradores para cambiar la configuración de los dispositivos.
* **[Actualización remota de firmware y software](#remote-firmware-and-software-update)**: el proceso que usan los administradores para actualizar el firmware y el software del dispositivo.

Las siguientes secciones proporcionan una cobertura más profunda de cada una de estas funcionalidades de administración de dispositivos y un modelo de alto nivel para implementar estas funcionalidades con Centro de IoT de Azure.

## Detección y aprovisionamiento de dispositivos

El aprovisionamiento de un dispositivo con Centro de IoT de Azure se realiza a través de la API del registro. Una vez que registre el dispositivo y proporcione o reciba una clave, puede habilitarlo para que se conecte al Centro de IoT con esa clave. El Centro de IoT de Azure solo se comunicará con los dispositivos registrados que presenten una credencial autorizada. Los administradores pueden deshabilitar el acceso del dispositivo al Centro de IoT de Azure a través del portal de administración de dispositivos.

Se puede utilizar un proceso de arranque dependiendo de cómo están fabricados, aprovisionados e implementados los dispositivos IoT. Puede crear un servicio de arranque como parte de la solución para proporcionar conectividad simple y retrasar el proceso de asignación de un dispositivo a un centro de IoT específico. La ubicación en la que operará el dispositivo puede ser desconocida en el momento en el que se fabrica el dispositivo. Este es solo un ejemplo de un gran número de flujos de trabajo potencialmente complejos, que ayudan a que un dispositivo sea conocido por el Centro de IoT de Azure y a que se integre con los procesos empresariales ya existentes.

Cuando se usa un servicio de arranque se inicia un dispositivo IoT que envía una solicitud que en última instancia, puede proporcionar acceso a un Centro de IoT de Azure asignado. La solicitud debe incluir las credenciales de arranque de dispositivos y otros datos necesarios. Para los dispositivos autorizados, el servicio de arranque debe registrar el dispositivo con un Centro de IoT de Azure asignado y proporcionar detalles de conectividad para el dispositivo que solicita un arranque. Centro de IoT proporciona los detalles de conectividad para el dispositivo que solicita un arranque.

## Registro de dispositivo y modelos de dispositivo

El término *modelo de dispositivo* hace referencia al modelo que incluye las propiedades del dispositivo que un servicio en la nube puede leer o escribir. También incluye los comandos de dispositivo que un servicio en la nube puede ejecutar de forma remota en un dispositivo. En un modelo basado en el servicio, que se describe en la sección siguiente, el dispositivo no tiene conocimiento del modelo, pero los servicios en la nube pueden realizar un seguimiento y usar los metadatos de los dispositivos.

El almacenamiento de la información del dispositivo y de los metadatos asociados es fundamental para el sistema IoT y el rol de registro del dispositivo. Esto es especialmente cierto para los dispositivos heredados que no pueden cambiarse o para los dispositivos que no usan un modelo de dispositivo. Un modelo basado en el servicio puede habilitar un servicio IoT para interactuar con una población de dispositivos. Cuando los dispositivos funcionan con un modelo definido, el registro de dispositivos actúa como una vista coherente de los datos de dispositivo en la que este atiende al maestro. En este caso, el servicio informa al dispositivo de los cambios que se quieren realizar y dichos cambios serán válidos solo después de que el dispositivo confirme el cambio.

### Modelo controlado por el servicio

Si un dispositivo se conecta a Centro de IoT de Azure y no proporciona un modelo de dispositivo, es importante implementar un registro de dispositivos que realice el seguimiento de los dispositivos registrados y de sus metadatos asociados. En este caso, el registro del dispositivo es la única ubicación de almacenamiento para los metadatos del dispositivo. Ejemplos de los metadatos de dispositivo de los que puede interesarle realizar un seguimiento son la topología lógica (por ejemplo, planta 4, edificio 109), la función del dispositivo (por ejemplo, el sensor de la puerta) o cualquier otra información de etiqueta.

### Modelo controlado por el dispositivo

Para habilitar la solución IoT y aprovechar las funcionalidades del dispositivo IoT, los dispositivos tienen que describirse a sí mismos ante la nube después de conectarse con el Centro de IoT. Estos son dos tipos de modelos de dispositivo que se pueden utilizar en una solución IoT:

#### Modelo de dispositivo autodefinido

Un ingeniero de dispositivos (o el desarrollador con un simulador de dispositivos) usa el modelo de dispositivo autodefinido a través de la iteración en las capacidades del dispositivo a medida que lo crea. El ingeniero puede empezar creando un dispositivo que tenga algunas propiedades y comandos admitidos y más adelante agregar más. De forma similar, el ingeniero de ese dispositivo podría tener muchos dispositivos, cada uno de los cuales proporciona capacidades únicas y usa el modelo de definición propia. En este caso, no se necesita al ingeniero de dispositivo para registrar la estructura del modelo de dispositivo. Una variación significativa en los modelos autodefinidos puede resultar un problema cuando se escala a un gran número de dispositivos.

#### Modelo de dispositivo predefinido

Una implementación IoT de producción que opere con restricciones en la red y en la energía o el procesamiento, obtiene grandes ventajas de un modelo de dispositivo predefinido que ayuda a reducir el procesamiento y el consumo de energía del dispositivo. Igualmente, un tráfico de red mínimo permite a los dispositivos transmitir a través de redes heterogéneas (wifi, 2G/3G/4G, BLE, Sat, etc.) especialmente cuando se usa una infraestructura limitada y costosa (por ejemplo, satélite). Al implementar un modelo de dispositivo predefinido, el ingeniero de dispositivo puede enviar información del dispositivo codificada en uno o dos bytes que sirva como clave en el modelo de dispositivo predefinido. La brevedad de este enfoque da como resultado aumento de eficiencias de uno o dos órdenes de magnitud en comparación con el modelo de dispositivo autodefinido.

### La solución preconfigurada de seguimiento remoto y su modelo de dispositivo

La [solución preconfigurada de seguimiento remoto][lnk-remote-monitoring] del Conjunto de aplicaciones de IoT de Azure implementa un modelo de dispositivo autodefinido. Puede utilizar este modelo para permitir la iteración rápida a medida que define y evolucionan las funcionalidades del dispositivo.

Puede encontrar el código fuente para esta solución preconfigurada en el repositorio de GitHub [azure-iot-solution][lnk-azure-iot-solution].

El código en el que la solución preconfigurada de supervisión remota crea un objeto de dispositivo y lo envía al servicio está definido en el archivo **Simulator/Simulator.WorkerRole/SimulatorCore/Devices/DeviceBase.cs**. En los métodos **SendDeviceInfo** y **GetDeviceInfo**, puede ver cómo se crea y se envía al Centro de IoT de Azure el modelo de dispositivo autodescriptivo.

El código den el que el servicio en la nube procesa los eventos relacionados con el modelo de dispositivo se encuentra en el archivo **EventProcessor/EventProcessor.WorkerRole/Processors/DeviceManagementProcessor.cs**. La mayor parte del trabajo que se realiza para la actuación sobre los mensajes relacionados con el modelo de dispositivo que dicho dispositivo envía al lado de servicio de la solución preconfigurada, se produce en el método **ProcessJToken**.

Una vez que se recibe un mensaje relacionado con el modelo de dispositivo, los responsables de actualizar el registro del dispositivo son los métodos **UpdateDeviceFromSimulatedDeviceInfoPacketAsync** y **UpdateDeviceFromRealDeviceInfoPacketAsync** en el archivo **DeviceManagement/Infrastructure/BusinessLogic/DeviceLogic.cs**. Las API de registro de dispositivos de la solución preconfigurada de supervisión remota se pueden encontrar en el archivo **DeviceManagement/Web/WebApiControllers/DeviceApiController.cs**.

### Modelos de dispositivos de puerta de enlace de campo

Las puertas de enlace de campo se suelen usar para habilitar la conectividad y la traducción de protocolos para los dispositivos que no pueden o no deben conectarse directamente a Internet. Si el dispositivo que se va a crear es una puerta de enlace de campo, puede representar los dispositivos que están conectados a través de la puerta de enlace de campo en todas las interacciones con el Centro de IoT de Azure. Como fabricante de la puerta de enlace de campo, es su responsabilidad implementar la traducción entre los protocolos de dispositivo y los protocolos admitidos por el servicio IoT. Si quiere habilitar la puerta de enlace de campo para conectar dispositivos Bluetooth baja energía (BLE), tendrá que implementar la interfaz BLE para dispositivos y la interfaz para el Centro de IoT de Azure.

## Administración de acceso de dispositivo

La solución preconfigurada Suite de IoT controla el acceso a diferentes aspectos del dispositivo, incluidos los derechos de acceso de escritura y de lectura de las propiedades del dispositivo y los derechos de ejecución para los comandos de dispositivo. En las soluciones preconfiguradas este acceso está controlado mediante Azure Active Directory (AAD) en el portal de administración del dispositivo. Puede habilitar la seguridad de acceso basada en roles extendiendo el uso de AAD en la solución preconfigurada.

## Control remoto

El control remoto está fuera de ámbito como un escenario para las soluciones preconfiguradas de Suite de IoT de Azure. Sin embargo, el siguiente es un análisis de alto nivel de las opciones disponibles para habilitar el control remoto en la solución de IoT.

En escenarios de TI, control remoto a menudo se usa para ayudar a los usuarios remotos o configurar de forma remota los servidores remotos. En escenarios de IoT, casi todos los dispositivos no tienen usuarios conectados, por lo tanto, se usa el control remoto en escenarios de diagnóstico y configuración remota. El control remoto puede implementarse a través de dos modelos diferentes:

* **Conexión directa**: para habilitar el control remoto a través de una conexión directa a un dispositivo (por ejemplo, SSH en Linux, Escritorio remoto en Windows o a través de las herramientas de depuración remotas) tiene que poder crear una conexión con el dispositivo. Debido a los riesgos de seguridad que supone la exposición de un dispositivo a la Internet abierta, se recomienda utilizar un servicio de retransmisión (como el [servicio de retransmisión de bus de servicio][service-bus-relay] de Azure) para habilitar la conexión y enrutar el tráfico. Ya que una conexión de retransmisión es una conexión saliente desde el dispositivo, esto ayuda a limitar la superficie de ataque en los puertos TCP abiertos en el dispositivo.

* **Comando de dispositivo**: el control remoto a través del control de dispositivo aprovecha la conexión existente y el canal de comunicaciones establecido entre el dispositivo y el Centro de IoT de Azure. Para habilitar el control remoto basado en el comando de dispositivo, es necesario implementar los siguientes requisitos:
  * El software que se ejecuta en el dispositivo tiene que informar al servicio IoT de que los comandos de dispositivo están disponibles en el dispositivo. Normalmente, esto se define como parte del modelo de dispositivo.
  * El software que se ejecuta en el dispositivo tiene que implementar los comandos de control remoto. Estos comandos de dispositivo deben seguir una solicitud (desde el servicio IoT al dispositivo) y un patrón de respuesta (del dispositivo al servicio IoT). La ejecución de comandos de dispositivo puede afectar el estado del dispositivo y ese nuevo estado tendrá que actualizarse en el registro del mismo.

Cuando el dispositivo es el almacén maestro para la configuración del dispositivo y su estado, el registro del dispositivo usa un modelo finalmente coherente y los cambios realizados en el estado del dispositivo tienen que enviarse al registro del mismo. La actualización del estado del registro del dispositivo se puede forzar mediante una solicitud del servicio IoT al dispositivo o el dispositivo puede actualizar automáticamente el servicio tras reconocer un cambio en el estado del sistema. La actualización automática del servicio desde el dispositivo puede generar mucho tráfico de red y aumentar el uso del procesador del dispositivo y de la energía disponible.

## Supervisión y administración remota

La mayoría de los dispositivos IoT diferencian los roles operativos de los de usuario de administración. La administración remota es el método en el que los administradores pueden supervisar el estado de sus dispositivos, configurar el flujo de datos de dispositivos en los procesos y aplicaciones empresariales (por ejemplo: CRM, ERP, sistemas de mantenimiento) y actualizar de forma remota el estado o configuración de dispositivos mediante el uso de comandos de dispositivo.

Las soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure incluyen un sitio web de Azure que establece un punto de partida para una experiencia de administración de dispositivos que se puede ampliar a escenarios en la solución IoT vertical.

Los administradores pueden determinar el estado de los dispositivos mediante la observación de los datos operativos del dispositivo (normalmente de evolución rápida) y los metadatos de dispositivo (normalmente de evolución lenta). Puede utilizar reglas para habilitar el sistema de alertas de estado de dispositivo, implementado a través de un motor de procesamiento de transmisión (por ejemplo, [Análisis de transmisiones de Azure](https://azure.microsoft.com/services/stream-analytics/)) en lugar de una estrategia de sondeo.

## Configuración remota

Cambiar la configuración de un dispositivo de forma remota puede ser importante en varias etapas del ciclo de vida del mismo: aprovisionamiento, diagnóstico o integración con procesos empresariales. Los cambios de configuración de forma remota se habilitan mediante una combinación del modelo del dispositivo y la capacidad del servicio IoT para actualizar de forma remota el estado de la instancia del modelo de un dispositivo.

En la solución preconfigurada de supervisión remota, el dispositivo describe que es compatible con los siguientes comandos para la configuración remota:

* ChangeKey
* ChangeConfig
* ChangeSystemProperties

Estos comandos de dispositivo no aparecen como disponibles en la solución prefigurada del portal de administración ya que este las ejecuta remotamente en determinadas circunstancias. Cuando un dispositivo recibe un comando de dispositivo, el dispositivo envía una respuesta de confirmación al servicio. Después de procesar el comando de dispositivo, este envía una respuesta de resultado que informa al servicio de que la ejecución del comando de dispositivo se realizó correctamente (o si hubo un error, incluyendo el código de error). En este punto, se confirma el estado deseado para el dispositivo en el dispositivo y se actualiza el registro del dispositivo.

## Actualización remota de firmware y software

Las actualizaciones remotas de firmware y software son una funcionalidad importante de un sistema IoT. Las actualizaciones permiten a los dispositivos ejecutar el software más reciente y más seguro. La actualización remota de firmware y software en un dispositivo es un ejemplo de un proceso de larga ejecución distribuido que normalmente implica la interacción con otros procesos empresariales. Por ejemplo, la actualización del firmware en un dispositivo que controla una bomba de combustible de alta potencia puede requerir pasos en otros sistemas para redirigir el combustible a otro lugar mientras se realiza y se comprueba la actualización.

### Un análisis de las actualizaciones de firmware

El siguiente ejemplo es una forma posible de implementar las actualizaciones de firmware que se ajustan a sus necesidades empresariales. El objetivo de este ejemplo es describir las consideraciones del proceso, no imponer una implementación específica. Cómo diseñar sus requisitos de actualización para que se tengan en cuenta muchos factores empresariales y técnicos está fuera del ámbito de este artículo.

Las actualizaciones de dispositivo se inician en el servicio IoT y se informa a los dispositivos en un momento determinado a través de un comando de dispositivo. Cuando un dispositivo admite de forma explícita la actualización remota de firmware o software, el servicio IoT debe entregar los comandos de actualización basados en directivas y procesos empresariales definidos. Al recibir el comando de dispositivo para realizar la actualización, el dispositivo debe:

1. Descargar e implementar el paquete de actualización.
2. Reiniciar la configuración recién implementada (en el caso de una actualización de firmware) o iniciar el nuevo paquete de software.
3. Comprobar que el nuevo firmware o software se ejecuta según lo previsto.

Durante este proceso de varios pasos, el dispositivo informará al servicio IoT sobre el estado del dispositivo a medida que progresa a través de los pasos.

Un servicio de almacenamiento, como Almacenamiento de Azure o una red de entrega de contenido (CDN) pueden entregar el paquete de actualización. Es importante comprobar la integridad del paquete descargado y que proviene del origen esperado antes de aplicar la actualización de firmware o software.

Después de completar una actualización del firmware, el dispositivo debe poder comprobar e identificar un estado correcto. Si el dispositivo no alcanzó ese estado correcto, el software del dispositivo debe iniciar una reversión hasta un estado válido conocido. El estado correcto conocido puede ser el último conocido o una imagen de firmware del dispositivo a la que se conoce como *estado dorado* que se guarda en una partición de almacenamiento.

## Pasos siguientes

Para obtener más información sobre el Centro de IoT de Azure, consulte estos vínculos:

* [Introducción al centro de IoT][]
* [¿Qué es el Centro de IoT de Azure?][]
* [Conectar el dispositivo][]

[Introducción al centro de IoT]: iot-hub-csharp-csharp-getstarted.md
[¿Qué es el Centro de IoT de Azure?]: iot-hub-what-is-iot-hub.md
[service-bus-relay]: ../service-bus/service-bus-relay-overview.md
[Conectar el dispositivo]: https://azure.microsoft.com/develop/iot/
[lnk-azure-iot-solution]: https://github.com/Azure/azure-iot-solution
[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-remote-monitoring]: ../iot-suite/iot-suite-remote-monitoring-sample-walkthrough.md

<!---HONumber=AcomDC_0211_2016-->