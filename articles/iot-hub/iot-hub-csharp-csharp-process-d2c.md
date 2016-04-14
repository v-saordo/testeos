<properties
	pageTitle="Procesamiento de mensajes de dispositivo a la nube del centro de IoT | Microsoft Azure"
	description="Siga este tutorial para aprender sobre patrones útiles para procesar mensajes de dispositivo a nube del centro de IoT."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="01/05/2016"
     ms.author="dobett"/>

# Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT

## Introducción

El centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y una aplicación back-end. Otros tutoriales ([Introducción al Centro de IoT] y [Envío de mensajes de nube a dispositivo con el Centro de IoT]) muestran cómo usar la funcionalidad básica de mensajería de dispositivo a nube y viceversa del Centro de IoT.

Este tutorial se basa en el código que se muestra en el tutorial [Introducción al Centro de IoT] y muestra dos patrones escalables que se pueden usar para procesar mensajes del dispositivo a la nube:

- Almacenamiento confiable de mensajes de dispositivo a nube de [Almacenamiento de blobs de Azure]. Se trata de un escenario muy frecuente al implementar análisis de *ruta de acceso inactiva*, donde los datos se almacenan en blobs para su uso como entrada en los procesos de análisis impulsados por herramientas como [Factoría de datos] o la pila de [HDInsight (Hadoop)].

- Procesamiento confiable de mensajes de dispositivo a nube *interactivos*. Los mensajes de dispositivo a nube son interactivos cuando son desencadenantes inmediatos de un conjunto de acciones en el back-end de la aplicación, a diferencia de los mensajes de *punto de datos*, que se envían a un motor de análisis. Por ejemplo, una alarma procedente de un dispositivo que tiene que desencadenar la inserción de un vale en un sistema CRM es un mensaje de dispositivo a nube interactivo, a diferencia de, por ejemplo, un mensaje de telemetría que contiene muestras de temperatura, que es un mensaje de punto de datos.

Puesto que el Centro de IoT expone un punto de conexión compatible con centros de eventos para recibir mensajes de dispositivo a nube, este tutorial usa una instancia de [EventProcessorHost], que:

* Almacena de manera confiable mensajes de *punto de datos* en Blobs de Azure.
* Reenvía mensajes de dispositivo a nube *interactivos* a una [cola del Bus de servicio] para su procesamiento inmediato.

El Bus de servicio es una excelente forma de asegurar un procesamiento confiable de mensajes interactivos, ya que ofrece puntos de comprobación de cada mensaje y desduplicación basada en periodos de tiempo.

Al final de este tutorial ejecutará tres aplicaciones de consola de Windows:

* **SimulatedDevice**, una versión modificada de la aplicación creada en el tutorial [Introducción al Centro de IoT], que envía mensajes de dispositivo a nube de punto de datos cada segundo y mensajes de dispositivo a nube interactivos cada 10 segundos.
* **ProcessDeviceToCloudMessages**, que usa la clase [EventProcessorHost] para recuperar mensajes desde el punto de conexión compatible con el Centro de eventos y luego almacenar mensajes de punto de datos de forma confiable en un blob de Azure y reenviar mensajes interactivos a una cola del Bus de servicio.
* **ProcessD2CInteractiveMessages**, que quita los mensajes interactivos de la cola del Bus de servicio.

> [AZURE.NOTE] El Centro de IoT ofrece compatibilidad con el SDK para muchas plataformas de dispositivos y lenguajes, entre los que se incluyen C, Java y JavaScript. Consulte el [Centro para desarrolladores de IoT de Azure] para obtener instrucciones paso a paso sobre cómo reemplazar el dispositivo simulado de este tutorial por un dispositivo físico y, en general, sobre cómo conectar dispositivos al Centro de IoT de Azure.

Este tutorial se puede aplicar directamente a otras formas de consumir mensajes compatibles con centros de eventos como, por ejemplo, proyectos de [Hadoop]. Consulte la [Guía del desarrollador del Centro de IoT de Azure] para más información.

Para completar este tutorial, necesitará lo siguiente:

+ Microsoft Visual Studio 2015.

+ Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdevelop%2Fiot%2Ftutorials%2Fprocess-d2c%2F target="\_blank").

También se dan por sentados ciertos conocimientos sobre el [Almacenamiento de Azure] y el [Bus de servicio de Azure].


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## Ejecución de las aplicaciones

Ya está preparado para ejecutar las aplicaciones.

1.	En el Explorador de soluciones en Visual Studio, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio**. Seleccione **Proyectos de inicio múltiples** y luego la acción **Iniciar** como acción para los proyectos **ProcessDeviceToCloudMessages**, **SimulatedDevice** y **ProcessD2CInteractiveMessages**.

2.	Presione **F5** para iniciar las tres aplicaciones de consola. La aplicación **ProcessD2CInteractiveMessages** debe procesar cada mensaje interactivo enviado desde la aplicación **SimulatedDevice**.

  ![][50]

> [AZURE.NOTE] Para ver las actualizaciones en el archivo blob, debe reducir la constante **MAX\_BLOCK\_SIZE** de la clase **StoreEventProcessor** a un valor inferior como **1024**. Esto se debe a que se tarda algún tiempo en alcanzar el límite de tamaño de bloque con los datos enviados por el dispositivo simulado. Con un tamaño de bloque menor, no tendrá que esperar tanto tiempo para ver el blob que se crea y se actualiza. Aunque un tamaño de bloque mayor hace que la aplicación sea más escalable.

## Pasos siguientes

En este tutorial, ha aprendido a procesar de manera confiable mensajes de dispositivo a nube interactivos y de punto de datos mediante la clase [EventProcessorHost].

El tutorial [Carga de archivos desde dispositivos] se basa en este tutorial con una lógica de procesamiento de mensajes análoga y describe un patrón que hace uso de mensajes de nube a dispositivo para facilitar la carga de archivos desde dispositivos

Información adicional sobre el centro de IoT:

* [Información general sobre el centro de IoT]
* [Guía del desarrollador del centro de IoT]
* [Directrices sobre el centro de IoT]
* [Lenguajes y plataformas de dispositivos compatibles][Supported devices]
* [Centro para desarrolladores de Azure]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Almacenamiento de blobs de Azure]: https://azure.microsoft.com/es-ES/documentation/articles/storage-dotnet-how-to-use-blobs/
[Factoría de datos]: https://azure.microsoft.com/es-ES/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/es-ES/documentation/services/hdinsight/
[Hadoop]: https://azure.microsoft.com/es-ES/documentation/services/hdinsight/
[cola del Bus de servicio]: https://azure.microsoft.com/es-ES/documentation/articles/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx



[Guía del desarrollador del Centro de IoT de Azure]: https://azure.microsoft.com/es-ES/documentation/articles/iot-hub-devguide/#d2c

[Almacenamiento de Azure]: https://azure.microsoft.com/es-ES/documentation/services/storage/
[Bus de servicio de Azure]: https://azure.microsoft.com/es-ES/documentation/services/service-bus/



[Envío de mensajes de nube a dispositivo con el Centro de IoT]: iot-hub-csharp-csharp-c2d.md
[Carga de archivos desde dispositivos]: iot-hub-csharp-csharp-file-upload.md

[Información general sobre el centro de IoT]: iot-hub-what-is-iot-hub.md
[Directrices sobre el centro de IoT]: iot-hub-guidance.md
[Guía del desarrollador del centro de IoT]: iot-hub-devguide.md
[Introducción al Centro de IoT]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: iot-hub-tested-configurations.md
[Centro para desarrolladores de Azure]: https://azure.microsoft.com/develop/iot
[Centro para desarrolladores de IoT de Azure]: https://azure.microsoft.com/develop/iot

<!---HONumber=AcomDC_0224_2016-->