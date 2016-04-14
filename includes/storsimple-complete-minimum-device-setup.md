<!--author=alkohli last changed: 9/17/15-->

#### Para completar la instalación mínima del dispositivo StorSimple

1. En la página **Dispositivos**, seleccione el dispositivo y haga clic en la flecha situada junto al nombre del dispositivo para ir a la página específica del dispositivo. 

	![Página de dispositivos con el dispositivo en línea](./media/storsimple-complete-minimum-device-setup/HCS_DevicesPageM-include.png)

2. Haga clic en el icono de inicio rápido ![Icono de inicio rápido](./media/storsimple-complete-minimum-device-setup/HCS_QuickStartIcon-include.png) para tener acceso a la página de inicio rápido del dispositivo. Haga clic en **Completar la instalación del dispositivo** para iniciar el **Asistente para configurar dispositivos**.

	![Página de inicio rápido del dispositivo](./media/storsimple-complete-minimum-device-setup/Device_Quick_Start_page_1M.png)

2. En la página **Configuración básica** haga lo siguiente:
  1. Proporcione un **nombre descriptivo** para el dispositivo. El nombre de dispositivo predeterminado refleja información como el modelo de dispositivo y el número de serie. Puede asignar un nombre descriptivo de hasta 64 caracteres para administrar el dispositivo.
  2. Establezca la **zona horaria** según la ubicación geográfica en la que se va a implementar el dispositivo. El dispositivo usará esta zona horaria para todas las operaciones programadas.
  3. En **Configuración DNS**, proporcione una dirección para el **Servidor DNS secundario**. Si usa IPv6, el campo se rellenará en función del prefijo IPv6 proporcionado en la interfaz de Windows PowerShell. Si el servidor DNS secundario no está configurado, no podrá guardar la configuración del dispositivo.
  4. En Interfaces habilitadas para iSCSI, habilite al menos una red para iSCSI. Al menos una interfaz de red debe estar habilitada para la nube y una interfaz debe estar habilitada para iSCSI. DATA 0 se habilita para la nube automáticamente.
 
      ![Configuración básica de la instalación mínima del dispositivo StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupBasicSettings1-include.png)

3. Haga clic en el icono de flecha. ![Icono de flecha de StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)

4. En la página de **Interfaces de red**, proporciona las direcciones IP fijas para el Controlador 0 y el Controlador 1. Si la interfaz DATA 0 estaba configurada para IPv4, las direcciones IP fijas deben suministrarse en formato IPv4. Si proporcionó un prefijo para la configuración de IPv6, las direcciones IP fijas se rellenarán automáticamente en estos campos.


	> [AZURE.NOTE] 
 	> 
 	> - Las direcciones IP fijas del controlador deben ser direcciones IP libres dentro de la subred y accesibles mediante la dirección IP del dispositivo.
 	> - Las direcciones IP fijas del controlador se usan para el mantenimiento de las actualizaciones del dispositivo y, por tanto, las direcciones IP fijas deben ser enrutables y poder conectarse a Internet.

    ![Interfaces de red de la instalación mínima del dispositivo StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

5. Haga clic en el icono de marca de verificación ![Icono de verificación de StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png). Volverá a la página **Inicio rápido** del dispositivo.

 >[AZURE.NOTE] Puede modificar la configuración restante del dispositivo en cualquier momento accediendo a la página **Configurar**.

![Vídeo disponible](./media/storsimple-complete-minimum-device-setup/Video_icon.png) **Vídeo disponible**

Para ver un vídeo que muestra cómo completar la configuración mínima del dispositivo, haga clic [aquí](https://azure.microsoft.com/documentation/videos/minimum-storsimple-device-setup/).

<!---HONumber=AcomDC_0128_2016-->