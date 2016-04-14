<properties
 pageTitle="Soluciones preconfiguradas de IoT de Azure | Microsoft Azure"
 description="Descripción de las soluciones preconfiguradas de IoT de Azure y de sus arquitecturas con vínculos a recursos adicionales."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="03/02/2016"
 ms.author="dobett"/>

# ¿Qué son las soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure?

Las soluciones preconfiguradas del Conjunto de aplicaciones de IoT de Azure son implementaciones de patrones de soluciones de IoT comunes que se pueden implementar en Azure con su suscripción. Puede usar las soluciones preconfiguradas:

- Como punto de partida para sus propias soluciones IoT.
- Para más información sobre los patrones comunes en el desarrollo y diseño de la solución de IoT.

Cada solución preconfigurada es una implementación completa que usa dispositivos simulados para generar telemetría.

Además de implementar y ejecutar las soluciones en Azure, puede descargar el código fuente completo y, después, personalizar y ampliar la solución para satisfacer sus necesidades de IoT específicas.

> [AZURE.NOTE] Para implementar una de las soluciones preconfiguradas, visite [Microsoft Azure IoT Suite][lnk-azureiotsuite] (Conjunto de aplicaciones de IoT de Microsoft Azure). En el artículo [Introducción a las soluciones de IoT preconfiguradas][lnk-preconf-get-started] se describe cómo implementar y ejecutar una de las soluciones.

La tabla siguiente muestra cómo se asignan estas soluciones a las características específicas de IoT:

| Solución | Ingesta de datos | Identidad de dispositivos | Comando y control | Reglas y acciones | Análisis predictivo |
|------------------------|-----|-----|-----|-----|-----|
| [Supervisión remota][lnk-remote-monitoring] | Sí | Sí | Sí | Sí | - | 
| [Mantenimiento predictivo][lnk-predictive-maintenance] | Sí | Sí | Sí | Sí | Sí |
- *Ingesta de datos*: entrada de datos a escala en la nube.
- *Identidad de dispositivo*: administración de las identidades únicas de cada dispositivo conectado.
- *Comando y control*: envío de mensajes a un dispositivo desde la nube para que el dispositivo realice alguna acción.
- *Reglas y acciones*: el back-end de la solución usa reglas para actuar sobre los datos específicos de dispositivo a la nube.
- *Análisis predictivo*: el back-end de la solución analiza los datos del dispositivo a la nube para predecir cuándo se deben llevar a cabo acciones específicas. Por ejemplo, análisis de la telemetría del motor de un avión para determinar cuándo hay que realizar el mantenimiento del motor.

## Información general sobre la solución preconfigurada de supervisión remota

En este artículo decidimos analizar la solución preconfigurada de supervisión remota porque muestra elementos de diseño comunes que comparte con las demás soluciones.

El siguiente diagrama ilustra los elementos clave de la solución de supervisión remota. Las secciones siguientes proporcionan más información sobre estos elementos.

![Arquitectura de la solución preconfigurada de supervisión remota][img-remote-monitoring-arch]

## Dispositivos

Al implementar la solución preconfigurada de supervisión remota, se aprovisionan previamente cuatro dispositivos simulados en la solución que simulan un dispositivo de refrigeración. Estos dispositivos simulados tienen un modelo de temperatura y humedad integrado que emite telemetría. Estos dispositivos simulados se incluyen para ilustrar el flujo completo de los datos a través de la solución, así como para proporcionar un origen de telemetría conveniente y un destino para los comandos si es un desarrollador de back-end que usa la solución como punto de partida para una implementación personalizada.

La primera vez que un dispositivo se conecta al Centro de IoT en la solución preconfigurada de supervisión remota, el mensaje de información de dispositivo que se envía al Centro de IoT enumera la lista de comandos a los que el dispositivo puede responder. En la solución preconfigurada de supervisión remota, los comandos son:

- *Ping Device*: el dispositivo responde a este comando con una confirmación. Es útil para comprobar que el dispositivo sigue activo y a la escucha.
- *Start Telemetry*: indica al dispositivo que empiece a enviar telemetría.
- *Stop Telemetry*: indica al dispositivo que detenga el envío de telemetría.
- *Change Set Point Temperature*: controla los valores de telemetría de temperatura simulados que envía el dispositivo. Resulta útil para probar la lógica del back-end.
- *Diagnostic Telemetry*: controla si el dispositivo debe enviar la temperatura exterior como telemetría.
- *Change Device State*: establece la propiedad de metadatos de estado del dispositivo de la que el dispositivo informa. Resulta útil para probar la lógica del back-end.

Puede agregar más dispositivos simulados a la solución que emitan la misma telemetría y respondan a los mismos comandos.

## Centro de IoT

En esta solución preconfigurada, la instancia del Centro de IoT corresponde a la *puerta de enlace en la nube* de una [arquitectura de soluciones de IoT][lnk-what-is-azure-iot] típica.

Un Centro de IoT recibe la telemetría de los dispositivos en un único punto de conexión. Un Centro de IoT también mantiene puntos de conexión específicos de dispositivo donde cada dispositivo puede recuperar los comandos que se le envían.

El Centro de IoT hace que la telemetría recibida esté disponible mediante el punto de conexión de lectura de telemetría del lado del servicio.

## Análisis de transmisiones de Azure

La solución preconfigurada usa tres trabajos de [Análisis de transmisiones de Azure][lnk-asa] (ASA) para filtrar la transmisión de la telemetría procedente de los dispositivos.


- *Trabajo DeviceInfo*: envía los datos a un Centro de eventos que enruta los mensajes específicos del registro del dispositivo que se envían cuando un dispositivo se conecta por primera vez o en respuesta a un comando **Change device state**, al registro de dispositivos de la solución (una base de datos de DocumentDB). 
- *Trabajo Telemetría*: envía toda la telemetría sin procesar a Almacenamiento de blobs de Azure para su almacenamiento en frío y calcula las agregaciones de telemetría que se muestran en el panel de la solución.
- *Trabajo Reglas*: filtra del flujo de telemetría los valores que superan los umbrales de las reglas, y envía los datos a un Centro de eventos. Cuando se activa una regla, la vista de panel del portal de solución muestra este evento como una nueva fila en la tabla de historial de alarma y desencadena una acción basada en la configuración definida en las vistas Reglas y Acciones del portal de solución.

En esta solución preconfigurada, los trabajos ASA forman parte del **back-end de la solución de IoT** en una [arquitectura de soluciones de IoT][lnk-what-is-azure-iot] típica.

## Procesador de eventos

En esta solución preconfigurada, el procesador de eventos forma parte del **back-end de la solución de IoT** en una [arquitectura de soluciones de IoT][lnk-what-is-azure-iot] típica.

Los trabajos ASA **DeviceInfo** y **Reglas** envían su salida a los Centros de eventos para entregarlos a otros servicios de back-end. La solución usa una instancia de [EventPocessorHost][lnk-event-processor], que se ejecuta en un [WebJob][lnk-web-job], para leer los mensajes de estos Centros de eventos. **EventProcessorHost** usa los datos de **DeviceInfo** para actualizar los datos del dispositivo en la base de datos de DocumentDB, y usa los datos de **Reglas** para invocar la aplicación lógica y actualizar la visualización de alertas en el portal de la solución.

## Registro de identidades de dispositivo y DocumentDB

Cada Centro de IoT incluye un [registro de identidades de dispositivo][lnk-identity-registry] que almacena las claves de dispositivo. El Centro de IoT usa esta información para autenticar dispositivos; un dispositivo debe estar registrado y tener una clave válida para poder conectarse al centro.

Esta solución almacena información adicional acerca de los dispositivos, como su estado, los comandos que admiten y otros metadatos. La solución usa una base de datos de DocumentDB para almacenar estos datos de dispositivo específicos de la solución, y el portal de la solución recupera los datos de esta base de datos DocumentDB para su presentación y edición.

La solución también debe mantener sincronizada la información del registro de identidades de dispositivo con el contenido de la base de datos de DocumentDB. **EventProcessorHost** usa los datos del trabajo de análisis de transmisiones **DeviceInfo** para administrar la sincronización.

## Portal de solución

![Panel de soluciones][img-dashboard]

El portal de solución es una interfaz de usuario basada en web que se implementa en la nube como parte de la solución preconfigurada. Le permite:

- Ver la telemetría y el historial de alarmas en un panel.
- Aprovisionar dispositivos nuevos.
- Administrar y supervisar los dispositivos.
- Enviar comandos a dispositivos específicos.
- Administrar reglas y acciones.

En esta solución preconfigurada, el portal de la solución forma parte tanto del **back-end de la solución de IoT** como de la **conectividad de procesamiento y empresarial** en una [arquitectura de soluciones de IoT][lnk-what-is-azure-iot] típica.

## Pasos siguientes

Examine estos recursos para obtener más información sobre las soluciones IoT preconfiguradas:

- [Introducción a las soluciones de IoT preconfiguradas][lnk-preconf-get-started]
- [Información general de la solución preconfigurada de mantenimiento predictivo][lnk-predictive-maintenance]

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: web-sites-create-web-jobs.md
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-identity-registry]: iot-hub-devguide.md#device-identity-registry
[lnk-suite-overview]: iot-suite-overview.md
[lnk-preconf-get-started]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/

<!---HONumber=AcomDC_0309_2016-->