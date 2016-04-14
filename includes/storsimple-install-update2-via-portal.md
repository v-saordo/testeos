<!--author=alkohli last changed: 01/26/16-->

#### Instalar la actualización 2 desde el Portal de Azure

1. En la página de servicio de StorSimple, seleccione el dispositivo. Vaya a **Dispositivos** > **Mantenimiento**.

2. En la parte inferior de la página, haga clic en **Buscar actualizaciones**. Para buscar actualizaciones disponibles, se creará un trabajo. Recibirá una notificación cuando el trabajo esté completado correctamente.

3. En la sección **Actualizaciones de software** en la misma página, verá que hay nuevas actualizaciones de software disponibles. Se recomienda revisar las notas de la versión antes de aplicar la actualización 2 en el dispositivo.

    ![Instalación de actualizaciones de software](./media/storsimple-install-update2-via-portal/scanupdate1.png)

4. En la parte inferior de la página, haga clic en **Instalar actualizaciones**.

5. Se le pedirá confirmación. Haga clic en **Aceptar**.

6. Aparecerá el cuadro de diálogo **Instalar actualizaciones**. El dispositivo debe cumplir las comprobaciones que se muestran en este cuadro de diálogo. Estos pasos se completaron antes de la actualización. Seleccione **Comprendo el requisito anterior y estoy listo para actualizar el dispositivo**. Haga clic en el icono de marca de verificación.

    ![Mensaje de confirmación](./media/storsimple-install-update2-via-portal/InstallUpdate12_2M.png)

7. Ahora se iniciará un conjunto de comprobaciones previas automáticas. Entre ellos se incluyen los siguientes:

	- **Comprobaciones del estado del controlador** para comprobar que los controladores del dispositivo están en buen estado y en línea.
	
	- **Comprobaciones de mantenimiento de componentes de hardware** para comprobar que todos los componentes de hardware del dispositivo StorSimple están en buen estado.
	
	- **Comprobaciones de DATA 0** para comprobar DATA 0 está habilitado en el dispositivo. Si la interfaz no está habilitada, tendrá que habilitarla y, a continuación, volver a intentarlo.
	
	- **Comprobaciones de DATA 2 y DATA 3** para comprobar que las interfaces de red de DATA 2 y DATA 3 no están habilitadas. Si estas interfaces están habilitadas, tendrá que deshabilitarlas y, a continuación, intentar actualizar el dispositivo. Esta comprobación solo se realiza se va a realizar una actualización de un dispositivo que ejecuta software de disponibilidad general. Los dispositivos que ejecuten las versiones 0.1, 0.2, o 0.3 no necesitarán esta comprobación.
	
	- **Comprobación de la puerta de enlace** de todos los dispositivos que ejecuten una versión anterior a la actualización 1. Esta comprobación se realiza en todos los dispositivos que ejecutan el software de la versión previa a la actualización 1 pero genera error en los dispositivos que tienen una puerta de enlace configurada para una interfaz de red distinta de DATA 0.
 
	La actualización 2 solo se aplicará si se completaron correctamente todas las comprobaciones anteriores a la actualización que se muestran arriba. Se le notificará que las comprobaciones anteriores a la actualización están en curso.
  
    ![Notificación de comprobación previa](./media/storsimple-install-update2-via-portal/InstallUpdate12_3M.png)

    El siguiente es un ejemplo en el que se produjo un error en la comprobación anterior a la actualización. Deberá comprobar que los controladores del dispositivo están en buen estado y en línea. También necesitará comprobar el estado de los componentes de hardware. En este ejemplo, los componentes del controlador 0 y del controlador 1 necesitan atención. Puede que necesite ponerse en contacto con el soporte técnico de Microsoft si no puede resolver estos problemas por sí mismo.

   	 ![Error de comprobación previa](./media/storsimple-install-update2-via-portal/HCS_PreUpgradeChecksFailed-include.png)

	
	> [AZURE.NOTE] Si está actualizando desde software anterior a la actualización 1, después de haber aplicado la actualización 2 en el dispositivo de StorSimple, las comprobaciones de DATA 2 y DATA 3, así como la comprobación de la puerta de enlace, ya no será necesarias para futuras actualizaciones. Las demás comprobaciones previas seguirán siendo necesarias. Si actualiza desde la actualización 1 o posterior, las comprobaciones previas DATA 2, DATA 3 y de puerta de enlace no se realizan.


8. Una vez completadas correctamente las comprobaciones anteriores a la actualización, se creará un trabajo de actualización. Recibirá una notificación cuando el trabajo de actualización esté correctamente creado.
 
    ![Creación del trabajo de actualización](./media/storsimple-install-update2-via-portal/InstallUpdate12_44M.png)

    La actualización se aplicará en el dispositivo.
 
9. Para supervisar el progreso del trabajo de actualización, haga clic en **Ver trabajo**. En la página **Trabajos**, puede ver el progreso de la actualización.
    
10. La actualización tardará unas horas en completarse. Seleccione el trabajo de actualización y haga clic en **Detalles** para ver los detalles del trabajo en cualquier momento.
  
11. Una vez completado el trabajo, vaya a la página **Mantenimiento** página y desplácese hacia abajo hasta **Actualizaciones de software**.

12. Compruebe que el dispositivo ejecuta **StorSimple 8000 Series Update 2 (6.3.9600.17673)**. También se debe modificar **Fecha de última actualización:**.


13. Ahora verá que las actualizaciones del modo de mantenimiento están disponibles. En algunos casos, puede que el firmware de disco ya se haya actualizado cuando se esté ejecutando la actualización 1.2. En estos casos, el portal lo determinará automáticamente y no solicitará las actualizaciones del modo de mantenimiento.

	Las actualizaciones del modo de mantenimiento son actualizaciones perturbadoras que provocan tiempos de inactividad del dispositivo y solo pueden aplicarse a través de la interfaz de Windows PowerShell del dispositivo. Siga los pasos enumerados en [Instalar y comprobar las revisiones de modo de mantenimiento](#to-install-and-verify-maintenance-mode-hotfix) para instalar estas actualizaciones del Modo de mantenimiento.

> [AZURE.NOTE] En determinados casos, el mensaje que indica que las actualizaciones del modo de mantenimiento están disponibles pueden mostrarse hasta 24 horas después de que las actualizaciones del modo de mantenimiento se apliquen correctamente en el dispositivo.

<!---HONumber=AcomDC_0128_2016-->