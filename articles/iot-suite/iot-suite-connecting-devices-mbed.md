<properties
   pageTitle="Conectar un dispositivo mediante C en mbed | Microsoft Azure"
   description="Se describe cómo conectar un dispositivo a la solución de supervisión remota preconfigurada del Conjunto de IoT de Azure mediante una aplicación creada en C que se ejecuta en un dispositivo de mbed."
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
   ms.date="02/05/2016"
   ms.author="dobett"/>


# Conexión del dispositivo a la solución preconfigurada de supervisión remota del conjunto de aplicaciones de IoT

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Compilar y ejecutar la solución de ejemplo de C en mbed

Las instrucciones siguientes describen los pasos para conectar un dispositivo [Freescale FRDM-K64F habilitado para mbed][lnk-mbed-home] a la solución de supervisión remota.

### Conectar el dispositivo al equipo de escritorio y a la red

1. Conecte el dispositivo mbed a la red mediante un cable Ethernet. Este paso es necesario porque la aplicación de ejemplo requiere acceso a Internet.

2. Vea [Introducción a mbed][lnk-mbed-getstarted] para conectar el dispositivo de mbed al equipo de escritorio.

3. Si el equipo de escritorio está ejecutando Windows, vea [Configuración del equipo][lnk-mbed-pcconnect] para configurar el acceso al puerto serie para el dispositivo mbed.

### Crear un proyecto de mbed e importar el código de ejemplo

1. En el explorador web, vaya al [sitio para desarrolladores](https://developer.mbed.org/) mbed.org. Si no se registró, verá una opción para crear una nueva cuenta (es gratis). De lo contrario, inicie sesión con las credenciales de su cuenta. Luego haga clic en **Compilador** en la esquina superior derecha de la página. Esto debería llevarle a la interfaz de administración del área de trabajo.

2. Asegúrese de que la plataforma de hardware que está usando aparezca en la esquina superior derecha de la ventana, o bien haga clic en el icono de la esquina derecha para seleccionar la plataforma de hardware.

3. Haga clic en **Importar** en el menú principal. Luego haga clic en **Haga clic aquí** para importar desde el vínculo de dirección URL situado junto al logotipo de globo terráqueo de mbed.

    ![][6]

4. En la ventana emergente, especifique el vínculo para el código de ejemplo https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ y haga clic en **Importar**.

    ![][7]

5. Puede ver en la ventana del compilador de mbed que se importaron varias bibliotecas durante la importación de este proyecto. El equipo de IoT de Azure proporciona y mantiene algunas ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [proton-c-mbed](https://developer.mbed.org/users/AzureIoTClient/code/proton-c-mbed/)), mientras que otras son bibliotecas de terceros que están disponibles en el catálogo de bibliotecas de mbed.

    ![][8]

6. Abra remote\_monitoring\\remote\_monitoring.c y busque el código siguiente en el archivo:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

7. Reemplace "[Device Id]" y "[Device Key] por los datos de su dispositivo. Use el Nombre de host del Centro de IoT para reemplazar los marcadores de posición [IoTHub Name] y [IoTHub Suffix, i.e. azure-devices.net]. Por ejemplo, si el nombre de host del Centro de IoT es contoso.azure-devices.net, Contoso será el **hubName** y todo lo que aparece detrás será el **hubSuffix**:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

    ![][9]

### Compilar y ejecutar el programa

1. Haga clic en **Compilar** para compilar el programa. Puede omitir con seguridad las advertencias, pero si la compilación genera errores, corríjalos antes de continuar.

2. Si la compilación es correcta, el sitio web del compilador de mbed genera un archivo .bin con el nombre del proyecto y lo descarga en el equipo local. Copie el archivo .bin en el dispositivo. Si guarda el archivo .bin en el dispositivo, este último se reiniciará y ejecutará el programa incluido en el archivo .bin. Puede reiniciar manualmente el programa en cualquier momento presionando el botón Restablecer en el dispositivo mbed.

3. Conecte con el dispositivo mediante una aplicación cliente de SSH, como PuTTY. Puede determinar el puerto serie que usa el dispositivo comprobando el Administrador de dispositivos de Windows:

    ![][11]

4. En PuTTY, haga clic en el tipo de conexión **serie**. Normalmente, el dispositivo se conecta a 115.200 baudios, por lo que introduzca 115.200 en el cuadro **Velocidad**. A continuación, haga clic en **Abrir**.

5. El programa comienza a ejecutarse. Puede que tenga que restablecer la placa (presione CTRL+pausa o el botón de reinicio de la placa) si el programa no se inicia automáticamente cuando se conecta.

    ![][10]

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration

<!---HONumber=AcomDC_0218_2016-->