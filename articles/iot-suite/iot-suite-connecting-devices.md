<properties
   pageTitle="Conectar un dispositivo mediante C en Windows | Microsoft Azure"
   description="Se describe cómo conectar un dispositivo a la solución de supervisión remota preconfigurada del Conjunto de IoT de Azure mediante una aplicación creada en C que se ejecuta en Windows."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="dobett"/>


# Conexión del dispositivo a la solución preconfigurada de supervisión remota del conjunto de aplicaciones de IoT

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Compilar y ejecutar la solución de ejemplo de C en Windows

1. Para clonar el repositorio de GitHub del *SDK de Microsoft Azure IoT* e instalar el *SDK de dispositivo de Microsoft Azure IoT para C* en su entorno de escritorio de Windows, siga las instrucciones de [Configuración de un entorno de desarrollo de Windows][lnk-setup-windows].

2. En Visual Studio 2015, abra la solución **remote\_monitoring.sln** en la carpeta **c\\serializer\\samples\\remote\_monitoring\\windows** de la copia local del repositorio.

3. En el **Explorador de soluciones**, en el proyecto **remote\_monitoring**, abra el archivo **remote\_monitoring.c**.

4. Busque el siguiente código en el archivo:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

5. Reemplace **[Device Id]** y **[Device Key]** por los valores para el dispositivo desde el panel de solución de supervisión remota.

6. Use el **Nombre de host del Centro de IoT** en el panel para reemplazar **[IoTHub Name]** y **[IoTHub Suffix, i.e. azure-devices.net]**. Por ejemplo, si su **Nombre de host del Centro de IoT** es **contoso.azure-devices.net**, reemplace **[IoTHub Name]** por **contoso** y reemplace **[IoTHub Suffix, i.e. azure-devices.net]** por **azure devices.net** como se muestra a continuación:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

7. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **remote\_monitoring**, haga clic en **Depurar** y, luego, haga clic en **Iniciar nueva instancia** para compilar y ejecutar el ejemplo. En la consola se muestran mensajes a medida que la aplicación envía telemetría de ejemplo al Centro de IoT.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-windows]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows

<!---HONumber=AcomDC_0211_2016-->