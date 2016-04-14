<properties
   pageTitle="Conectar un dispositivo mediante Node.js | Microsoft Azure"
   description="Se describe cómo conectar un dispositivo a la solución de supervisión remota preconfigurada del Conjunto de IoT de Azure mediante una aplicación creada en Node.js."
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

## Compilar y ejecutar la solución de ejemplo de node.js

1. Para clonar el repositorio de GitHub del *SDK de Microsoft Azure IoT* e instalar el *SDK de dispositivo de Microsoft Azure IoT para Node.js* en su entorno de escritorio, siga las instrucciones de [Preparación del entorno de desarrollo][lnk-github-prepare].

2. Desde su copia local del repositorio [azure-iot-sdks][lnk-github-repo], copie los siguientes dos archivos de la carpeta node/device/samples a una carpeta de su dispositivo:

  - packages.json
  - remote\_monitoring.js

3. Abra el archivo remote-monitoring.js y busque la siguiente variable:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. Reemplace **[IoT Hub device connection string]** con la cadena de conexión al dispositivo. Puede encontrar los valores para su nombre de host del Centro de IoT, el id. de dispositivo y la clave de dispositivo en el panel de solución de supervisión remota. Una cadena de conexión de dispositivo tiene el siguiente formato:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Si su nombre de host del Centro de IoT es **contoso** y el id. del dispositivo es **mydevice**, su cadena de conexión tendrá este aspecto:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

5. Guarde el archivo . Para instalar los paquetes necesarios, ejecute los siguientes comandos en un símbolo del sistema de la carpeta que contiene estos archivos y, a continuación, ejecute la aplicación de ejemplo:

    ```
    npm install
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-node-installers]: https://nodejs.org/download/
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

<!---HONumber=AcomDC_0211_2016-->