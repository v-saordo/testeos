<properties
	pageTitle="Introducción al Centro de IoT de Azure para C# | Microsoft Azure"
	description="Siga este tutorial para aprender a usar el centro de IoT de Azure con C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="12/14/2015"
     ms.author="dobett"/>

# Introducción al Centro de IoT de Azure para .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Introducción

El Centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y un back-end de soluciones. Uno de los mayores desafíos que plantean los proyectos de IoT es cómo conectar dispositivos al back-end de la solución de manera segura y confiable. Para abordar este desafío, el Centro de IoT:

- Ofrece una mensajería confiable de gran escala de dispositivo a nube y de nube a dispositivo.
- Habilita las comunicaciones seguras con las credenciales de seguridad de cada dispositivo y el control de acceso.
- Incluye bibliotecas de dispositivos para las plataformas y los lenguajes más populares.

En este tutorial se muestra cómo realizar las siguientes acciones:

- Usar el Portal de Azure para crear un Centro de IoT.
- Crear una identidad de dispositivo en el Centro de IoT.
- Crear un dispositivo simulado que envía la telemetría a su back-end en la nube y recibe los comandos del back-end en la nube.

Al final de este tutorial tendrá tres aplicaciones de consola de Windows:

* **CreateDeviceIdentity**, que crea una identidad de dispositivo y una clave de seguridad asociada para conectar el dispositivo simulado.
* **ReadDeviceToCloudMessages**, que muestra los datos de telemetría enviados por el dispositivo simulado.
* **SimulatedDevice**, que se conecta con su Centro de IoT con la identidad del dispositivo creada anteriormente y envía un mensaje de telemetría cada segundo.

> [AZURE.NOTE] El artículo [SDK de Centro de IoT][lnk-hub-sdks] proporciona información acerca de los SDK que puede usar para crear dos aplicaciones para ejecutarse en dispositivos y en el back-end de la solución.

Para completar este tutorial, necesitará lo siguiente:

+ Microsoft Visual Studio 2015.

+ Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][lnk-free-trial].

## Crear un centro de IoT

Debe crear un Centro de IoT al que se conectará el dispositivo simulado. En los siguientes pasos se muestra cómo completar esta tarea con el Portal de Azure.

1. Inicie sesión en el [Portal de Azure][lnk-portal].

2. En la barra de accesos directos, haga clic en **Nuevo**. A continuación, haga clic en **Internet de las cosas** y en **Centro de IoT de Azure**.

    ![][1]

3. En la hoja **Centro de IoT**, elija la configuración para su Centro de IoT.

    ![][2]

    * En el cuadro **Nombre**, escriba un nombre para identificar el Centro de IoT. Si el **Nombre** es válido y está disponible, aparecerá una marca de verificación verde en el cuadro **Nombre**.
    * Seleccione un **Plan de tarifa y escalado**. Este tutorial no requiere ningún nivel determinado.
    * En **Grupo de recursos**, cree un grupo de recursos o seleccione uno existente. Para obtener más información, consulte [Uso de grupos de recursos para administrar los recursos de Azure][lnk-resource-groups].
    * En **Ubicación**, seleccione la ubicación para hospedar su Centro de IoT.  

4. Cuando haya elegido las opciones de configuración del Centro de IoT, haga clic en **Crear**. La creación del Centro de IoT puede tardar unos minutos. Para comprobar el estado, puede supervisar el progreso en el panel de inicio o en el panel de notificaciones.

    ![][3]

5. Una vez creado correctamente el Centro de IoT, abra la hoja del nuevo Centro de IoT, anote el **Nombre de host** y, después, en el icono **Llaves**.

    ![][4]

6. Haga clic en la directiva **iothubowner** y copie y anote la cadena de conexión en la hoja **iothubowner**.

    ![][5]

Ya se creó el Centro de IoT y tiene el nombre de host y la cadena de conexión que necesita para completar el resto del tutorial.

[AZURE.INCLUDE [iot-hub-get-started-cloud-csharp](../../includes/iot-hub-get-started-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-csharp](../../includes/iot-hub-get-started-device-csharp.md)]

## Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1.	En el Explorador de soluciones en Visual Studio, haga clic con el botón derecho en el proyecto de la aplicación cliente y, después, haga clic en **Establecer como proyecto de inicio**. Seleccione **Proyectos de inicio múltiples** y seleccione la **Acción** **Iniciar** para los proyectos **ReadDeviceToCloudMessages** y **SimulatedDevice**.

   	![][41]

2.	Presione **F5** para iniciar la ejecución de ambas aplicaciones. La salida de la consola de la aplicación **SimulatedDevice** muestra los mensajes que el dispositivo simulado envía a su Centro de IoT y la salida de la consola de la aplicación **ReadDeviceToCloudMessages** muestra los mensajes que el Centro de IoT recibe.

   	![][42]

## Pasos siguientes

En este tutorial, configuró un nuevo Centro de IoT en el portal y después creó una identidad de dispositivo en el registro de identidades del centro. Esta identidad de dispositivo se usó en un dispositivo simulado que envía al concentrador los mensajes de dispositivo a la nube y se creó otra aplicación que muestra los mensajes recibidos por el centro. Puede seguir explorando las características del Centro de IoT y otros escenarios en los tutoriales siguientes:

- [Envío de mensajes de nube a dispositivo con el Centro de IoT][lnk-c2d-tutorial] muestra cómo enviar mensajes a dispositivos y procesar los comentarios de entrega generados por el Centro de IoT.
- [Procesamiento de mensajes de dispositivo a la nube][lnk-process-d2c-tutorial] muestra cómo procesar de forma confiable la telemetría y los mensajes interactivos procedentes de los dispositivos.
- [Cómo cargar archivos desde dispositivos a la nube ][lnk-upload-tutorial] describe un patrón que usa mensajes de nube a dispositivo para facilitar la carga de archivos desde los dispositivos.

<!-- Images. -->
[1]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub5.png
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png

<!-- Links -->
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-resource-groups]: resource-group-portal.md
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0204_2016-->