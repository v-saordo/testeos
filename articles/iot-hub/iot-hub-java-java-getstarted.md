<properties
	pageTitle="Introducción al Centro de IoT de Azure para Java | Microsoft Azure"
	description="Siga este tutorial para comenzar a usar el Centro de IoT de Azure con Java."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="12/21/2015"
     ms.author="dobett"/>

# Introducción al Centro de IoT de Azure para Java

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Introducción

El Centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y un back-end de soluciones. Uno de los mayores desafíos que plantean los proyectos de IoT es cómo conectar dispositivos al back-end de la solución de manera segura y confiable. Para abordar este desafío, el Centro de IoT:

- Ofrece una mensajería confiable de gran escala de dispositivo a nube y de nube a dispositivo.
- Habilita las comunicaciones seguras con las credenciales de seguridad de cada dispositivo y el control de acceso.
- Incluye bibliotecas de dispositivos para las plataformas y los lenguajes más populares.

En este tutorial se muestra cómo realizar las siguientes acciones:

- Usar el Portal de Azure para crear un Centro de IoT.
- Crear una identidad de dispositivo en el Centro de IoT.
- Crear un dispositivo simulado que envía la telemetría al back-end en la nube.

Al final de este tutorial tendrá tres aplicaciones de consola de Java:

* **create-device-identity**, que crea una identidad de dispositivo y una clave de seguridad asociada para conectar el dispositivo simulado.
* **read-d2c-messages**, que muestra los datos de telemetría enviados por el dispositivo simulado.
* **simulated-device**, que se conecta con el Centro de IoT con la identidad de dispositivo creada anteriormente y envía un mensaje de telemetría cada segundo.

> [AZURE.NOTE] El artículo [SDK de Centro de IoT][lnk-hub-sdks] proporciona información acerca de los SDK que puede usar para crear dos aplicaciones para ejecutarse en dispositivos y en el back-end de la solución.

Para completar este tutorial, necesitará lo siguiente:

+ Java SE 8. <br/> [Prepare your development environment][lnk-dev-setup] (Preparación del entorno de desarrollo) describe cómo instalar Java para este tutorial en Windows o Linux.

+ Maven 3. <br/> [Prepare your development environment][lnk-dev-setup] (Preparación del entorno de desarrollo) describe cómo instalar Maven para este tutorial en Windows o Linux.

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

7. Haga clic en **Configuración** en la hoja Centro de IoT y luego en **Mensajes** en la hoja **Configuración**. En la hoja **Mensajes**, anote los valores de **Nombre compatible con Centro de eventos** y **Punto de conexión compatible de Centro de eventos**. Necesitará estos valores al crear su la aplicación **read-d2c-messages**.

    ![][6]

Ahora que ha creado un Centro de IoT y que tiene el nombre de host del Centro de IoT, la cadena de conexión del Centro de IoT, el nombre compatible con el Centro de eventos y el punto de conexión compatible con el Centro de eventos, es preciso que complete el resto del tutorial.

[AZURE.INCLUDE [iot-hub-get-started-cloud-java](../../includes/iot-hub-get-started-cloud-java.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-java](../../includes/iot-hub-get-started-device-java.md)]

## Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1. En un símbolo del sistema, en la carpeta read-d2c, ejecute el siguiente comando para empezar la supervisión del Centro de IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][7]

2. En un símbolo del sistema, en la carpeta simulated-device, ejecute el siguiente comando para empezar el envío de datos de telemetría al Centro de IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][8]

## Pasos siguientes

En este tutorial, configuró un nuevo Centro de IoT en el portal y después creó una identidad de dispositivo en el registro de identidades del centro. Esta identidad de dispositivo se usó en un dispositivo simulado que envía al concentrador los mensajes de dispositivo a la nube y se creó otra aplicación que muestra los mensajes recibidos por el centro. Puede seguir explorando las características del Centro de IoT y otros escenarios en los tutoriales siguientes:

- [Envío de mensajes de nube a dispositivo con el Centro de IoT][lnk-c2d-tutorial] muestra cómo enviar mensajes a dispositivos y procesar los comentarios de entrega generados por el Centro de IoT.
- [Procesamiento de mensajes de dispositivo a la nube][lnk-process-d2c-tutorial] muestra cómo procesar de forma confiable la telemetría y los mensajes interactivos procedentes de los dispositivos.
- [Cómo cargar archivos desde dispositivos a la nube ][lnk-upload-tutorial] describe un patrón que usa mensajes de nube a dispositivo para facilitar la carga de archivos desde los dispositivos.

<!-- Images. -->
[1]: ./media/iot-hub-java-java-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-java-java-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-java-java-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-java-java-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-java-java-getstarted/create-iot-hub5.png
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-resource-groups]: resource-group-portal.md
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0204_2016-->