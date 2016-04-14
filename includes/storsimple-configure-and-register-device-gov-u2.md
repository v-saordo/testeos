<!--author=SharS last changed: 02/22/2016-->

### Para configurar y registrar el dispositivo

1. Acceder a la interfaz de Windows PowerShell en la consola serie del dispositivo StorSimple. Consulte [Uso de PuTTY para conectarse a la consola serie del dispositivo](#use-putty-to-connect-to-the-device-serial-console) para obtener instrucciones. **Siga el procedimiento exactamente como se indica o no podrá acceder a la consola.**

2. En la sesión que se abre, presione ENTRAR una vez para obtener un símbolo del sistema.

3. Se le pedirá que elija el idioma que desee establecer para el dispositivo. Especifique el idioma y, a continuación, presione ENTRAR.

    ![Configurar y registrar el dispositivo 1 de StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)

4. En el menú de la consola serie que se muestra, seleccione la opción 1 para iniciar sesión con acceso completo.

    ![Registrar el dispositivo 2 de StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
  
5. Realice los siguientes pasos para configurar los valores de red mínimos necesarios para su dispositivo.

    > [AZURE.IMPORTANT] Estos pasos de configuración deben realizarse en el controlador activo del dispositivo. El menú de la consola serie indica el estado del controlador en el mensaje del banner. Si no está conectado al controlador activo, desconéctese y, a continuación, conéctese al controlador activo.

      1. En el símbolo del sistema, escriba su contraseña. La contraseña predeterminada del dispositivo es **Password1**.

      2. Escriba el siguiente comando:

           `Invoke-HcsSetupWizard`

      3. Aparecerá un Asistente para instalación que le ayudará a configurar las opciones de red para el dispositivo. Proporcione la siguiente información:

       - Dirección IP para la interfaz de red DATA 0
       - Máscara de subred
       - Puerta de enlace
       - Dirección IP para el servidor DNS principal
       - Dirección IP para el servidor NTP principal
 
        > [AZURE.NOTE] Tendrá que esperar unos minutos a que se aplique la configuración de máscara de subred y DNS.

      4. De manera opcional, configure el servidor proxy web.

      > [AZURE.IMPORTANT] Aunque la configuración del proxy web es opcional, tenga en cuenta que, si usa un proxy web, solo puede configurarlo aquí. Para obtener más información, vaya a [Configurar el proxy web para el dispositivo](storsimple-configure-web-proxy.md).

6. Presione Ctrl + C para salir del asistente de configuración.
 
7. Instale las actualizaciones de la siguiente manera:
      1. Use el siguiente cmdlet para establecer direcciones IP en ambos controladores:

         `Set-HcsNetInterface -InterfaceAlias Data0 -Controller0IPv4Address <Controller0 IP> -Controller1IPv4Address <Controller1 IP>`

      2. En el símbolo del sistema, ejecute `Get-HcsUpdateAvailability`. Debe recibir una notificación para avisarle de que hay actualizaciones disponibles.

      3. Ejecute `Start-HcsUpdate`. Puede ejecutar este comando en cualquier nodo. Las actualizaciones se aplicarán en el primer controlador, el controlador se conmutará por error y luego las actualizaciones se aplicarán en el otro controlador.

      Para supervisar el progreso de la actualización, ejecute `Get-HcsUpdateStatus`.

       La siguiente salida de ejemplo muestra la actualización en curso.
  
        ````
        Controller0>Get-HcsUpdateStatus
        RunInprogress       : True
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   : 
        ````
 
     La siguiente salida de ejemplo indica que ha finalizado la actualización.

        ````
        Controller1>Get-HcsUpdateStatus

        RunInprogress       : False
        LastHotfixTimestamp : 4/13/2015 10:56:13 PM
        LastUpdateTimestamp : 4/13/2015 10:35:25 PM
        Controller0Events   :
        Controller1Events   :

      Las actualizaciones pueden tardar hasta 11 horas en aplicarse, incluidas las de Windows.

10. Ejecute el siguiente cmdlet para que el dispositivo apunte al Portal de Microsoft Azure Government (ya que, de forma predeterminada, apunta al Portal de Azure clásico público). Ambos controladores se reiniciarán. Recomendamos que use dos sesiones PuTTY para conectarse simultáneamente a ambos controladores para así poder ver cuándo se reinicia cada uno.

     `Set-CloudPlatform -AzureGovt_US`

    Aparecerá un mensaje de confirmación. Acepte el valor predeterminado (**Y**).

11. Ejecute el siguiente cmdlet para reanudar la instalación:

     `Invoke-HcsSetupWizard`

     ![Asistente para reanudar la instalación](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)

    Cuando se reanude la instalación, el asistente será la versión de Actualización 2.

12. Acepte la configuración de red. Verá un mensaje de validación después de aceptar cada configuración.
 
13. Por motivos de seguridad, la contraseña del administrador del dispositivo expira después de la primera sesión y deberá cambiarla ahora. Cuando se le solicite, proporcione una contraseña de administrador del dispositivo. Una contraseña de administrador del dispositivo válida debe tener entre 8 y 15 caracteres. La contraseña debe contener tres de los siguientes elementos: minúsculas, mayúsculas, números y caracteres especiales.

	<br/>![Registrar el dispositivo 5 de StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)

14. El último paso del Asistente para instalación registra el dispositivo con el servicio de Administrador de StorSimple. Para ello, necesitará la clave de registro del servicio que obtuvo en [Paso 2: Obtener la clave de registro del servicio](storsimple-get-service-registration-key-gov.md). Después de proporcionar la clave de registro, puede que tenga que esperar entre 2 y 3 minutos hasta que el dispositivo se registre.

      > [AZURE.NOTE] También puede presionar Ctrl+C en cualquier momento para salir del Asistente para instalación. Si ha especificado toda la configuración de red (dirección IP para Data 0, máscara de subred y puerta de enlace), se conservarán las entradas.

	![Progreso de registro de StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)

15. Una vez registrado el dispositivo, aparecerá una clave de cifrado de datos de servicio. Copie esta clave y guárdela en un lugar seguro. **Esta clave se solicitará junto con la clave de registro de servicio para registrar dispositivos adicionales con el servicio de Administrador de StorSimple.** Consulte [Seguridad de StorSimple](../articles/storsimple/storsimple-security.md) para obtener más información sobre esta clave.
	
	![Registrar el dispositivo 7 de StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)

      > [AZURE.IMPORTANT] Para copiar el texto de la ventana de la consola serie, simplemente seleccione el texto. A continuación, podrá pegarlo en el Portapapeles o en cualquier editor de texto.
      > 
      > No use Ctrl+C para copiar la clave de cifrado de datos de servicio. Si usa Ctrl+C, saldrá del Asistente para instalación. Como resultado, no se cambiará la contraseña del administrador del dispositivo y el dispositivo volverá a usar la contraseña predeterminada.

16. Salga de la consola serie.

17. Vuelva al Portal de Azure Government y siga estos pasos:
  1. Haga doble clic en el servicio de Administrador de StorSimple para acceder a la página **Inicio rápido**.
  2. Haga clic en **Ver los dispositivos conectados**.
  3. En la página **Dispositivos**, compruebe que el dispositivo se conectó correctamente al servicio consultando el estado. El estado del dispositivo debe ser **Conectado**.
   
    	![StorSimple Devices page](./media/storsimple-configure-and-register-device-gov-u2/HCS_DeviceOnline-gov-include.png) 
  
        Si el estado del dispositivo es Desconectado, espere un par de minutos hasta que el dispositivo se conecte. 

        Si el dispositivo sigue sin conexión tras unos minutos, hay que asegurarse de que la red de firewall se haya configurado tal como se describe en [Software de StorSimple, alta disponibilidad y requisitos de red](../articles/storsimple/storsimple-system-requirements.md). 

        Compruebe que el puerto 9354 está abierto para la comunicación saliente, ya que lo usa el Bus de servicio para la comunicación de servicio al dispositivo de StorSimple Manager.
     
        

<!---HONumber=AcomDC_0224_2016-->
