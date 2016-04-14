<!--author=SharS last changed: 01/15/2016-->

#### Para instalar la actualización 1.2 desde el Portal de Azure clásico

1. En la página de servicio de StorSimple, seleccione el dispositivo. Vaya a **Dispositivos** > **Mantenimiento**.

2. En la parte inferior de la página, haga clic en **Buscar actualizaciones**. Para buscar actualizaciones disponibles, se creará un trabajo. Recibirá una notificación cuando el trabajo esté completado correctamente.

3. En la sección **Actualizaciones de software** en la misma página, verá que hay nuevas actualizaciones de software disponibles. Se recomienda que revise las notas de la versión antes de aplicar la actualización 1.2 en el dispositivo.

    ![Instalación de actualizaciones de software](./media/storsimple-install-update-via-portal/InstallUpdate12_11M.png)

4. En la parte inferior de la página, haga clic en **Instalar actualizaciones**.

5. Se le pedirá confirmación. Haga clic en **Aceptar**.

6. Aparecerá el cuadro de diálogo **Instalar actualizaciones**. El dispositivo debe cumplir las comprobaciones que se muestran en este cuadro de diálogo. Estos pasos se completaron antes de la actualización. Seleccione **Comprendo el requisito anterior y estoy listo para actualizar el dispositivo**. Haga clic en el icono de marca de verificación.

    ![Mensaje de confirmación](./media/storsimple-install-update-via-portal/InstallUpdate12_2M.png)

7. Ahora se iniciará un conjunto de comprobaciones previas automáticas. Entre ellos se incluyen los siguientes:

	- **Comprobaciones del estado del controlador** para comprobar que los controladores del dispositivo están en buen estado y en línea.
	
	- **Comprobaciones de mantenimiento de componentes de hardware** para comprobar que todos los componentes de hardware del dispositivo StorSimple están en buen estado.
	
	- **Comprobaciones de DATA 0** para comprobar DATA 0 está habilitado en el dispositivo. Si la interfaz no está habilitada, tendrá que habilitarla y, a continuación, volver a intentarlo.
	
	- **Comprobaciones de DATA 2 y DATA 3** para comprobar que las interfaces de red de DATA 2 y DATA 3 no están habilitadas. Si estas interfaces están habilitadas, tendrá que deshabilitarlas y, a continuación, intentar actualizar el dispositivo. Esta comprobación solo se realiza se va a realizar una actualización de un dispositivo que ejecuta software de disponibilidad general. Los dispositivos que ejecuten las versiones 0.1, 0.2, o 0.3 no necesitarán esta comprobación.
	
	- **Comprobación de la puerta de enlace** de todos los dispositivos que ejecuten una versión anterior a la actualización 1. Esta comprobación se realiza en todos los dispositivos que ejecutan el software de la versión previa a la actualización 1 pero genera error en los dispositivos que tienen una puerta de enlace configurada para una interfaz de red distinta de DATA 0.
 
	La actualización 1.2 solo se aplicará si se han completado correctamente todas las comprobaciones que se muestran arriba anteriores a la actualización. Se le notificará que las comprobaciones anteriores a la actualización están en curso.
  
    ![Notificación de comprobación previa](./media/storsimple-install-update-via-portal/InstallUpdate12_3M.png)

    El siguiente es un ejemplo en el que se produjo un error en la comprobación anterior a la actualización. Deberá comprobar que los controladores del dispositivo están en buen estado y en línea. También necesitará comprobar el estado de los componentes de hardware. En este ejemplo, los componentes del controlador 0 y del controlador 1 necesitan atención. Puede que necesite ponerse en contacto con el soporte técnico de Microsoft si no puede resolver estos problemas por sí mismo.

   	 ![Error de comprobación previa](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

	> [AZURE.NOTE]Después de haber aplicado la actualización 1.2 en el dispositivo StorSimple, las comprobaciones de DATA 2 y DATA 3 y la comprobación de la puerta de enlace ya no será necesaria para futuras actualizaciones. Las demás comprobaciones previas seguirán siendo necesarias.


8. Una vez completadas correctamente las comprobaciones anteriores a la actualización, se creará un trabajo de actualización. Recibirá una notificación cuando el trabajo de actualización esté correctamente creado.
 
    ![Creación del trabajo de actualización](./media/storsimple-install-update-via-portal/InstallUpdate12_44M.png)

    La actualización se aplicará en el dispositivo.
 
9. Para supervisar el progreso del trabajo de actualización, haga clic en **Ver trabajo**. En la página **Trabajos**, puede ver el progreso de la actualización.

    ![Actualización del progreso del trabajo](./media/storsimple-install-update-via-portal/InstallUpdate12_5M.png)

10. La actualización tardará unas horas en completarse. Puede ver los detalles del trabajo en cualquier momento.

    ![Actualización de detalles del trabajo](./media/storsimple-install-update-via-portal/InstallUpdate12_6M.png)

11. Una vez completado el trabajo, vaya a la página **Mantenimiento** página y desplácese hacia abajo hasta **Actualizaciones de software**.

12. Compruebe que el dispositivo está ejecutando **StorSimple 8000 Series Update 1.2 (6.3.9600.17584)**. También se debe modificar **Fecha de última actualización:**.

    ![Página de mantenimiento](./media/storsimple-install-update-via-portal/InstallUpdate12_10M.png)

13. Ahora verá que las actualizaciones del modo de mantenimiento están disponibles. Estas actualizaciones son actualizaciones perjudiciales que provocan tiempos de inactividad del dispositivo y solo pueden aplicarse a través de la interfaz de Windows PowerShell del dispositivo. Siga las instrucciones que encontrará en [Instalar actualizaciones en el modo de mantenimiento](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple), para instalar estas actualizaciones mediante Windows PowerShell para StorSimple.

> [AZURE.NOTE]En determinados casos, el mensaje que indica que las actualizaciones del modo de mantenimiento están disponibles pueden mostrarse hasta 24 horas después de que las actualizaciones del modo de mantenimiento se apliquen correctamente en el dispositivo.

<!---HONumber=AcomDC_0121_2016-->