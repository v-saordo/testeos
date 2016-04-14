## Ver la telemetría de dispositivo en el panel

El panel de la solución de supervisión remota permite ver la telemetría que los dispositivos envían al Centro de IoT.

1. En el explorador, vuelva al panel de solución de supervisión remota, haga clic en **Dispositivos** en el panel izquierdo para navegar hasta la **Lista de dispositivos**.

2. En el **Lista de dispositivos**, debería ver que el estado del dispositivo es ahora **En ejecución**.

    ![][18]

3. Haga clic en **Panel** para volver a él, seleccione el dispositivo en la lista desplegable **Dispositivo para ver** para ver su telemetría. La telemetría de la aplicación de ejemplo es 50 unidades para la temperatura interior, 55 unidades para la temperatura exterior y 50 unidades para la humedad. Tenga en cuenta que, de forma predeterminada, en el panel solo se muestran los valores de temperatura y humedad.

    ![][img-telemetry]

## Envío de un comando a su dispositivo

El panel de la solución de supervisión remota le permite solicitar el Centro de IoT para enviar comandos a sus dispositivos. Por ejemplo, en la solución de supervisión remota puede enviar un comando para establecer la temperatura interna de un dispositivo.

1. En el panel de solución de supervisión remota, haga clic en **Dispositivos** en el panel izquierdo para navegar hasta la **Lista de dispositivos**.

2. Haga clic en **Id. de dispositivo** para el dispositivo en la **Lista de dispositivos**.

3. En el panel **Detalles del dispositivo**, haga clic en **Comandos**.

    ![][13]

4. En la lista desplegable **Comando**, seleccione **SetTemperature** y, luego, en **Temperatura** escriba un nuevo valor de la temperatura. Haga clic en **Enviar comando** para enviar el comando al dispositivo.

    ![][14]

    > [AZURE.NOTE] Inicialmente, el historial de comandos muestra el estado del comando como **Pendiente**. Cuando el dispositivo reconoce el comando, el estado cambia a **Correcto**.

5. En el panel, compruebe que el dispositivo está enviando ahora 75 como el nuevo valor de temperatura.

## Pasos siguientes

En el artículo [Personalización de soluciones preconfiguradas][lnk-customize] se describen algunas formas de ampliar este ejemplo. Entre las posibles extensiones se incluyen el uso de sensores reales y la implementación de comandos adicionales.

[13]: ./media/iot-suite-visualize-connecting/suite4.png
[14]: ./media/iot-suite-visualize-connecting/suite7-1.png
[18]: ./media/iot-suite-visualize-connecting/suite10.png
[img-telemetry]: ./media/iot-suite-visualize-connecting/telemetry.png
[lnk-customize]: ../articles/iot-suite/iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-dev-messaging]: ../articles/iot-hub/iot-hub-devguide.md#messaging

<!---HONumber=AcomDC_0211_2016-->