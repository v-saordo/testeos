<!--author=alkohli last changed: 02/22/2016-->


### Para configurar y registrar el dispositivo

1. Acceder a la interfaz de Windows PowerShell en la consola serie del dispositivo StorSimple. Consulte [Uso de PuTTY para conectarse a la consola serie del dispositivo](#use-putty-to-connect-to-the-device-serial-console) para obtener instrucciones. **Siga el procedimiento exactamente como se indica o no podrá acceder a la consola.**

2. En la sesión que se abre, presione ENTRAR una vez para obtener un símbolo del sistema.

3. Se le pedirá que elija el idioma que desee establecer para el dispositivo. Especifique el idioma y, a continuación, presione ENTRAR.

    ![Configurar y registrar el dispositivo 1 de StorSimple](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice1-U1-include.png)

4. En el menú de la consola serie que se muestra, seleccione la opción 1 para iniciar sesión con acceso completo.

    ![Registrar el dispositivo 2 de StorSimple](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice2_U1-include.png)
  
     Complete los pasos 5-12 para configurar las opciones de red necesarias mínimas para el dispositivo. **Estos pasos de configuración deben realizarse en el controlador activo del dispositivo.** El menú de la consola serie indica el estado del controlador en el mensaje del banner. Si no está conectado al controlador activo, desconéctese y, a continuación, conéctese al controlador activo.

5. En el símbolo del sistema, escriba su contraseña. La contraseña predeterminada del dispositivo es **Password1**.

6. Escriba el siguiente comando: `Invoke-HcsSetupWizard`.

7. Aparecerá un Asistente para instalación que le ayudará a configurar las opciones de red para el dispositivo. Proporcione la siguiente información:
   - Dirección IP para la interfaz de red DATA 0
   - Máscara de subred
   - Puerta de enlace
   - Dirección IP para el servidor DNS principal
    
	Tenga en cuenta que el sistema valida la configuración de red después de cada paso del proceso.
		   
	> [AZURE.NOTE] Tendrá que esperar unos minutos para que se apliquen la máscara de subred y la configuración de DNS. Si recibe el mensaje de error «Compruebe la conectividad de red para Data 0», compruebe la conexión de red física en la interfaz de red DATA 0 de su controlador activo.

8. (Opcional) Configure el servidor proxy web. Aunque la configuración del proxy web es opcional, **tenga en cuenta que, si usa un proxy web, solo puede configurarlo aquí**. Para obtener más información, vaya a [Configurar el proxy web para el dispositivo](../articles/storsimple/storsimple-configure-web-proxy.md).

9. Configure un servidor NTP principal para el dispositivo. Se requieren servidores NTP, dado que el dispositivo debe sincronizar la hora para que pueda autenticarse con los proveedores de servicios en la nube. Asegúrese de que su red permite que el tráfico NTP pase del centro de datos a Internet. Si esto no es posible, especifique un servidor NTP interno.
 
10. Por motivos de seguridad, la contraseña del administrador del dispositivo expira después de la primera sesión y deberá cambiarla ahora. Cuando se le solicite, proporcione una contraseña de administrador del dispositivo. Una contraseña de administrador del dispositivo válida debe tener entre 8 y 15 caracteres. La contraseña debe contener tres de los siguientes elementos: minúsculas, mayúsculas, números y caracteres especiales.

	<br/>![Registrar el dispositivo 5 de StorSimple](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice5_U1-include.png)

11. El último paso del Asistente para instalación registra el dispositivo con el servicio de Administrador de StorSimple. Para ello, necesitará la clave de registro del servicio que obtuvo en el paso 2. Después de proporcionar la clave de registro, puede que tenga que esperar entre 2 y 3 minutos hasta que el dispositivo se registre.

      > [AZURE.NOTE] También puede presionar Ctrl+C en cualquier momento para salir del Asistente para instalación. Si ha especificado toda la configuración de red (dirección IP para Data 0, máscara de subred y puerta de enlace), se conservarán las entradas.

	![Registrar el dispositivo 6 de StorSimple](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice6_U1-include.png)

12. Una vez registrado el dispositivo, aparecerá una clave de cifrado de datos de servicio. Copie esta clave y guárdela en un lugar seguro. **Esta clave se solicitará junto con la clave de registro de servicio para registrar dispositivos adicionales con el servicio de Administrador de StorSimple.** Consulte [Seguridad de StorSimple](../articles/storsimple/storsimple-security.md) para obtener más información sobre esta clave.
	
	![Registrar el dispositivo 7 de StorSimple](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice7_U1-include.png)

      > [AZURE.NOTE] Para copiar el texto de la ventana de la consola serie, simplemente seleccione el texto. A continuación, podrá pegarlo en el Portapapeles o en cualquier editor de texto. No use Ctrl+C para copiar la clave de cifrado de datos de servicio. Si usa Ctrl+C, saldrá del Asistente para instalación. Como resultado, no se cambiará la contraseña del administrador del dispositivo y el dispositivo volverá a usar la contraseña predeterminada.

13. Salga de la consola serie.

14. Vuelva al Portal de Azure clásico y siga estos pasos:
  1. Haga doble clic en el servicio de Administrador de StorSimple para acceder a la página **Inicio rápido**.
  2. Haga clic en **Ver los dispositivos conectados**.
  3. En la página **Dispositivos**, compruebe que el dispositivo se conectó correctamente al servicio consultando el estado. El estado del dispositivo debe ser **Conectado**.
   
	![Página de dispositivos de StorSimple](./media/storsimple-configure-and-register-device-u1/HCS_DevicesPageM_U1-include.png)
  
	Si el estado del dispositivo es **Desconectado**, espere un par de minutos para que el servicio vuelva a estar en línea.

	Si el dispositivo sigue sin conexión tras unos minutos, hay que asegurarse de que la red de firewall se haya configurado tal como se describe en [Software de StorSimple, alta disponibilidad y requisitos de red](../articles/storsimple/storsimple-system-requirements.md).

	Compruebe que el puerto 9354 está abierto para la comunicación saliente, ya que lo usa el Bus de servicio para la comunicación de servicio al dispositivo de StorSimple Manager.
     
       

<!---HONumber=AcomDC_0224_2016-->