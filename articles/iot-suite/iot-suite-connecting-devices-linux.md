<properties
   pageTitle="Conectar un dispositivo mediante C en Linux | Microsoft Azure"
   description="Se describe cómo conectar un dispositivo a la solución de supervisión remota preconfigurada del Conjunto de IoT de Azure mediante una aplicación creada en C que se ejecuta en Linux."
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

## Compilar y ejecutar la solución de ejemplo de C en Linux

1. Para clonar el repositorio de GitHub del *SDK de Microsoft Azure IoT* e instalar el *SDK de dispositivo de Microsoft Azure IoT para C* en su entorno de escritorio de Linux, siga las instrucciones de [Configuración de un entorno de desarrollo de Linux][lnk-setup-linux].

2. Abra el archivo **c/serializer/samples/remote\_monitoring/remote\_monitoring.c** en un editor de texto.

3. Busque el siguiente código en el archivo:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

4. Reemplace **[Device Id]** y **[Device Key]** por los valores para el dispositivo desde el panel de solución de supervisión remota.

5. Use el **Nombre de host del Centro de IoT** en el panel para reemplazar **[IoTHub Name]** y **[IoTHub Suffix, i.e. azure-devices.net]**. Por ejemplo, si su **Nombre de host del Centro de IoT** es **contoso.azure-devices.net**, reemplace **[IoTHub Name]** por **contoso** y reemplace **[IoTHub Suffix, i.e. azure-devices.net]** por **azure devices.net** como se muestra a continuación:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. Guarde los cambios y compile los ejemplos. Para compilar el ejemplo, puede ejecutar el script build.sh en el directorio **build\_all/c/linux**. El script de compilación crea una carpeta **cmake** para almacenar los programas de ejemplo compilados.

7. Ejecute la aplicación de ejemplo **cmake/serializer/samples/remote\_monitoring/remote\_monitoring**.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-linux]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux

<!---HONumber=AcomDC_0211_2016-->