<properties
	pageTitle="Carga de archivos desde dispositivos mediante el centro de IoT | Microsoft Azure"
	description="Siga este tutorial para aprender a cargar archivos desde dispositivos con el centro de IoT de Azure con C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# Tutorial: cómo cargar archivos desde dispositivos a la nube con un centro de IoT

## Introducción

El centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y una aplicación back-end. Existen tutoriales anteriores ([Get started with IoT Hub] [Comenzar a usar el centro de IoT] y [Send Cloud-to-Device messages with IoT Hub] [Envío de mensajes de nube a dispositivo con el centro de IoT]) que muestran la funcionalidad básica de comunicación de dispositivo a nube y viceversa mediante el centro de IoT, así como el modo de tener acceso a ellos desde componentes de dispositivos y la nube. En [Process Device-to-Cloud messages] (Procesamiento de mensajes de dispositivo a nube) se describe una forma de almacenar de manera fiable mensajes enviados del dispositivo a la nube en el almacenamiento de blobs de Azure. Existen casos, sin embargo, donde los datos procedentes de dispositivos no se asignan fácilmente a mensajes de dispositivo a nube relativamente pequeños. Algunos ejemplos son los archivos de gran tamaño que contienen imágenes, vídeos, muestras de datos de vibración a alta frecuencia, o bien archivos que contienen algún tipo de datos preprocesados. Dichos archivos se suelen procesar por lotes mediante herramientas como, por ejemplo, [Factoría de datos de Azure] o la pila [Hadoop]. Cuando se prefiera cargar un archivo desde un dispositivo en lugar de enviar eventos, es posible usar la funcionalidad de fiabilidad y seguridad del centro de IoT.

Este tutorial se basa en el código que se muestra en [Send Cloud-to-Device messages with IoT Hub] (Envío de mensajes de nube a dispositivo con el centro de IoT) que explica cómo usar mensajes de nube a dispositivo para proporcionar de manera segura al dispositivo un URI de blob de Azure que se usará para cargar el archivo. Asimismo, explica cómo usar las confirmaciones de entrega del centro de IoT para iniciar el procesamiento del archivo desde el back-end de la aplicación. Las ventajas de este enfoque son la posibilidad de volver a usar la identidad del dispositivo del centro de IoT y la confirmación de entrega de los mensajes enviados de nube a dispositivo para informar al back-end de la aplicación de que el archivo se ha cargado correctamente.

> [AZURE.NOTE] Puede usarse este mismo enfoque para que los dispositivos descarguen de manera segura los archivos desde la nube.

Encontrará más información sobre los mensajes enviados de nube a dispositivo y sobre la seguridad del centro de IoT en la [IoT Hub Developer Guide] (Guía para desarrolladores del centro de IoT).

Al final de este tutorial ejecutará dos aplicaciones de consola de Windows:

* **SimulatedDevice**; una versión modificada de la aplicación creada en [Send Cloud-to-Device messages with IoT Hub] (Envío de mensajes de nube a dispositivo con el centro de IoT), que se conecta a su centro de IoT, recibe los mensajes enviados de nube a dispositivo que contienen URI de blobs de Azure. Para cada mensaje de nube a dispositivo recibido, se desencadenará una carga de archivo al URI del blob especificado.
* **SendCloudToDevice**; que genera un URI de blob de Azure (tal como se explica en [Creación y uso de una firma de acceso compartido con el servicio blob](../storage/storage-dotnet-shared-access-signature-part-2.md)), envía un mensaje de nube a dispositivo al dispositivo simulado mediante el centro de IoT y, a continuación, recibe la confirmación de entrega.

> [AZURE.NOTE] El centro de IoT ofrece compatibilidad con el SDK para muchas plataformas de dispositivos y lenguajes (incluido C, Java y Javascript), mediante los SDK del dispositivo de IoT de Azure. Consulte el [Centro para desarrolladores de IoT de Azure] para obtener instrucciones paso a paso sobre cómo conectar el dispositivo al código de este tutorial y, en general, al Centro de IoT de Azure. Próximamente estarán disponibles SDK de servicios IoT de Azure para Java y Node.

Para completar este tutorial, necesitará lo siguiente:

+ Microsoft Visual Studio 2015

+ Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdevelop%2Fiot%2Ftutorials%2Ffile-upload%2F target="\_blank").


[AZURE.INCLUDE [iot-hub-file-upload-cloud-csharp](../../includes/iot-hub-file-upload-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-file-upload-device-csharp](../../includes/iot-hub-file-upload-device-csharp.md)]

## Ejecución de las aplicaciones

Ya está preparado para ejecutar las aplicaciones.

1.  Desde Visual Studio, haga clic con el botón secundario del mouse en la solución y seleccione **Establecer proyectos de inicio...** Seleccione **Proyectos de inicio múltiples**, a continuación, seleccione la acción **Iniciar** para las aplicaciones **SimulatedDevice** y **SendCloudToDevice**.

2.  Presione **F5** y verá cómo se inician todas las aplicaciones. Seleccione la ventana **SendCloudToDevice** y presione una tecla. Verá que el dispositivo simulado emite muestra un mensaje cuando se carga el archivo y la aplicación **SendCloudToDevice** muestra que los comentarios se han recibido correctamente. Puede usar el [Portal de Azure] o el Explorador de servidores de Visual Studio para comprobar la presencia del archivo en su cuenta de almacenamiento.

  ![][50]


## Pasos siguientes

En este tutorial ha aprendido a usar los mensajes de nube a dispositivo simplificar la carga de archivos desde los dispositivos. Puede explorar las características del centro de IoT y distintos escenarios con el tutorial siguiente:

- [Process Device-to-Cloud messages] (Procesamiento de mensajes de dispositivo a nube) muestra cómo procesar de forma fiable la telemetría y los mensajes interactivos procedentes de los dispositivos.

Información adicional sobre el centro de IoT:

* [Información general sobre el centro de IoT]
* [Guía del desarrollador del centro de IoT]
* [Directrices sobre el centro de IoT]
* [Lenguajes y plataformas de dispositivos compatibles][Supported devices]
* [Centro para desarrolladores de Azure]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png

<!-- Links -->

[Envío de mensajes de nube a dispositivo con el Centro de IoT]: iot-hub-csharp-csharp-c2d.md

[Portal de Azure]: https://portal.azure.com/

[Factoría de datos de Azure]: https://azure.microsoft.com/es-ES/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/es-ES/documentation/services/hdinsight/

[Comenzar a usar el centro de IoT]: iot-hub-csharp-csharp-getstarted.md
[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[Información general sobre el centro de IoT]: iot-hub-what-is-iot-hub.md
[Directrices sobre el centro de IoT]: iot-hub-guidance.md
[IoT Hub Developer Guide]: iot-hub-devguide.md
[Guía del desarrollador del centro de IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro para desarrolladores de Azure]: http://www.azure.com/develop/iot
[Centro para desarrolladores de IoT de Azure]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_0128_2016-->