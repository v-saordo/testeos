<properties 
   pageTitle="Administrar controladores de dispositivos StorSimple | Microsoft Azure"
   description="Aprenda cómo detener, reiniciar, apagar o restablecer los controladores de su dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="alkohli" />

# Administrar controladores de su dispositivo StorSimple

## Información general

En este tutorial se describen las distintas operaciones que pueden llevarse a cabo en los controladores de su dispositivo StorSimple. Los controladores de su dispositivo StorSimple son controladores redundantes (del mismo nivel) en una configuración activo-pasivo. En un momento dado, solo un controlador está activo y es el que procesa todas las operaciones de disco y red. El otro controlador está en el modo pasivo. Si se produce un error en el controlador activo, el segundo controlador se activa automáticamente.

Este tutorial incluye instrucciones paso a paso para administrar los controladores del dispositivo mediante:

- La sección **Controladores** de la página **Mantenimiento** del servicio de Administrador de StorSimple
- Windows PowerShell para StorSimple 

Le recomendamos que administre los controladores del dispositivo mediante el servicio Administrador de StorSimple. Si una acción solo puede realizarse mediante Windows PowerShell para StorSimple, el tutorial se refiere a ello en una nota.

Después de leer este tutorial, podrá:

- Reiniciar o apagar un controlador de dispositivo StorSimple
- Apagar un dispositivo StorSimple
- Restablecer el dispositivo StorSimple a los valores predeterminados de fábrica.


## Reiniciar o apagar un solo controlador

No es necesario reiniciar o apagar un controlador como parte del funcionamiento normal del sistema. Las operaciones de apagado de un solo controlador de dispositivo son comunes solo en los casos en que un componente de hardware de dispositivo con error requiere reemplazo. También puede ser necesario reiniciar un controlador en situaciones en que el rendimiento se vea afectado por el uso excesivo de memoria o en la que un controlador no funcione correctamente. Asimismo, debe reiniciar un controlador después de la correcta sustitución del mismo, si desea habilitar y probar el controlador reemplazado.

El reinicio de un dispositivo no es problemático para los iniciadores conectados, siempre que el controlador pasivo esté disponible. Si un controlador pasivo no está disponible o está desactivado, el reinicio del controlador activo puede resultar en la interrupción del servicio y en tiempo de inactividad.

> [AZURE.IMPORTANT] 

> - **Un controlador en ejecución nunca debe moverse físicamente ya que esto podría provocar una pérdida de redundancia y un mayor riesgo de tiempo de inactividad.**

> - El siguiente procedimiento se aplica solo al dispositivo de StorSimple físico. Para obtener información sobre cómo iniciar, detener y reiniciar el dispositivo virtual, consulte [Trabajar con el dispositivo virtual](storsimple-virtual-device-u1.md#work-with-the-storsimple-virtual-device).

Puede reiniciar o apagar un solo controlador de dispositivo mediante el Portal de Azure clásico del servicio StorSimple Manager o Windows PowerShell para StorSimple

Para administrar los controladores de su dispositivo desde el Portal de Azure clásico, lleve a cabo los siguientes pasos.

#### Reiniciar o apagar un controlador en el portal clásico

1. Vaya a **Dispositivos > Mantenimiento**.

1. Vaya a **Estado del hardware** y verifique que el estado de los dos controladores de su dispositivo sea **Correcto**.

	![Comprobar que los controladores del dispositivo StorSimple están en buen estado](./media/storsimple-manage-device-controller/IC766017.png)

1. En la parte inferior de la página **Mantenimiento**, haga clic en**Administrar controladores**.

	![Administrar controladores de dispositivo StorSimple](./media/storsimple-manage-device-controller/IC766018.png)</br>

	>[AZURE.NOTE] Si no ve **Administrar controladores**, debe instalar las actualizaciones. Para obtener más información, consulte [Actualizar dispositivo StorSimple](storsimple-update-device.md).

1. En el cuadro de diálogo **Cambiar configuración de controlador**, haga lo siguiente:


	- En la lista desplegable **Seleccionar controlador**, seleccione el controlador que desea administrar. Las opciones son Controlador 0 y Controlador 1. Estos controladores también se identifican como activo o pasivo.

		>[AZURE.NOTE] Si un controlador no está disponible o está apagado, el mismo no podrá administrarse y tampoco aparecerá en la lista desplegable.
	


	- En la lista desplegable **Seleccionar acción**, elija **Reiniciar controlador** o **Apagar controlador**.
	
		![Reiniciar el controlador pasivo del dispositivo StorSimple](./media/storsimple-manage-device-controller/IC766020.png)
 

	- Haga clic en el icono de marca de verificación ![Icono de marca de verificación](./media/storsimple-manage-device-controller/IC740895.png).

El controlador se reiniciará o apagará. En la siguiente tabla se resumen los detalles de lo que sucede según las selecciones realizadas en el cuadro de diálogo **Cambiar configuración de controlador**.
													

|Número de selección|Si decide|Sucederá esto|
|---|---|---|
|1\.|Reiniciar el controlador pasivo.|Se creará un trabajo para reiniciar el controlador y se le notificará una vez que el mismo se haya creado correctamente. Se iniciará el reinicio del controlador. Puede supervisar el proceso de reinicio si accede a **Servicio > Panel > Ver registros de operaciones** y filtra los datos según los parámetros específicos de su servicio.|
|2\.|Reiniciar el controlador activo.|Verá la siguiente advertencia: "Si reinicia el controlador activo, el dispositivo conmutará por error al controlador pasivo. ¿Desea continuar?" </br>Si decide continuar con esta operación, los pasos siguientes serán idénticos a los utilizados para reiniciar el controlador pasivo (consulte selección 1).|
|3\.|Apagar el controlador pasivo.|Verá el siguiente mensaje: "Una vez apagado, deberá presionar el botón de encendido en el controlador para activarlo. ¿Está seguro de que desea apagar este controlador?" </br>Si decide continuar con esta operación, los pasos siguientes serán idénticos a los usados para reiniciar el controlador pasivo (consulte selección 1).|
|4\.|Apagar el controlador activo.|Verá el siguiente mensaje: "Una vez apagado, deberá presionar el botón de encendido en el controlador para activarlo. ¿Está seguro de que desea apagar este controlador?" </br>Si decide continuar con esta operación, los pasos siguientes serán idénticos a los usados para reiniciar el controlador pasivo (consulte selección 1).|


#### Para reiniciar o apagar un controlador en Windows PowerShell para StorSimple
Lleve a cabo los siguientes pasos para apagar o reiniciar un solo controlador de su dispositivo StorSimple desde el Portal de Azure clásico.


1. Acceda al dispositivo desde la consola serie o a través de una sesión de telnet desde un equipo remoto. Conéctese al Controlador 0 o al Controlador 1 siguiendo los pasos detallados en [Uso de PuTTY para conectarse a la consola de serie del dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

1. En el menú de la consola serie, seleccione la opción 1, **Iniciar sesión con acceso completo**.

1. En el mensaje de pancarta que aparece, anote el controlador al que está conectado (Controlador 0 o Controlador 1) y si es el controlador activo o el pasivo (en espera).
	

	- Para apagar un solo controlador, en el símbolo del sistema, escriba:

		`Stop-HcsController`

		Esto apagará el controlador al que está conectado. Si detiene el controlador activo, este conmutará por error al controlador pasivo antes de apagarse.


	- Para reiniciar un controlador, en el símbolo del sistema, escriba:

		`Restart-HcsController`

		Esto reiniciará el controlador al que está conectado. Si reinicia el controlador activo, este conmutará por error al controlador pasivo antes del reinicio.


## Apagar un dispositivo StorSimple

En esta sección se explica cómo apagar un dispositivo StorSimple en ejecución o con errores desde un equipo remoto. Un dispositivo se apaga después de apagar sus dos controladores. El dispositivo se apaga cuando se lo va a trasladar físicamente o cuando se lo va a dejar fuera de servicio.

> [AZURE.IMPORTANT] Antes de apagar el dispositivo, compruebe el estado de los componentes del dispositivo. Vaya a **Dispositivos > Mantenimiento > Estado del hardware** y compruebe que el estado de los LED de todos los componentes sea verde. Solo los dispositivos correctos tendrán un estado verde. Si el dispositivo se apaga para sustituir un componente que no funciona correctamente, verá un estado de error (rojo) o de degradado (amarillo) para los componentes correspondientes.

#### Para apagar un dispositivo StorSimple

1. Use el procedimiento [reiniciar o apagar un controlador](#restart-or-shut-down-a-single-controller) para identificar y apagar el controlador pasivo del dispositivo. Puede realizar esta operación en el Portal de Azure clásico o en Windows PowerShell para StorSimple.
2. Repita el paso anterior para apagar el controlador activo.
3. Ahora debe mirar el plano posterior del dispositivo. Una vez apagados por completo los dos controladores, los LED de estado de ambos controladores deben parpadear en rojo. Si necesita apagar el dispositivo por completo en este momento, cambie los interruptores de alimentación de los módulos de alimentación y refrigeración (PCM) a la posición de apagado. Esto debe apagar el dispositivo.


<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

	`Stop-HCSController`

	This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## Restablecer el dispositivo a los valores predeterminados de fábrica.

Este procedimiento contiene los pasos detallados requeridos para restablecer su dispositivo Microsoft Azure StorSimple a los valores predeterminados de fábrica mediante Windows PowerShell para StorSimple.

Lleve a cabo los siguientes pasos para restablecer su dispositivo Microsoft Azure StorSimple a los valores predeterminados de fábrica:

### Para restablecer el dispositivo a los valores predeterminados de fábrica en Windows PowerShell para StorSimple

1. Acceda al dispositivo mediante su consola serie. Consulte el mensaje de pancarta que aparece para asegurarse de que está conectado al controlador activo.

1. En el menú de la consola serie, seleccione la opción 1, **Iniciar sesión con acceso completo**.

1. En el símbolo del sistema, escriba el siguiente comando:

	`Reset-HcsFactoryDefault`

	El sistema se reiniciará varias veces. Se le notificará cuando el restablecimiento se haya completado correctamente. Según el modelo del sistema, un dispositivo 8100 puede tardar de 45 a 60 minutos y un 8600 de 60 a 90 minutos en finalizar este proceso.

	> [AZURE.TIP] 
	
	> - Use el comando `Reset-HcsFactoryDefault –SkipFirmwareVersionCheck` para omitir la comprobación de la versión de firmware si el cmdlet de restablecimiento de fábrica (según se usó anteriormente) notifica el siguiente error de coincidencia de firmware: El restablecimiento de fábrica no puede continuar debido a una incoherencia en las versiones de firmware. Debe omitir la comprobación de firmware (mediante el uso de opción `–SkipFirmwareCheck`) al realizar un restablecimiento de fábrica en un dispositivo que se actualizó previamente mediante Microsoft Update o un mecanismo de revisión.
	
	> - El procedimiento de restablecimiento de fábrica puede producir un error en los dispositivos StorSimple que ejecuten la actualización 1 o 1.1 en el Portal de Government y han llevado a cabo un reemplazo de controlador único o doble correcto (con los controladores de reemplazo que se enviaron con el software anterior a la actualización 1). Esto sucede cuando la imagen del restablecimiento de fábrica se valida para la presencia de un archivo SHA1 del controlador que no existe para el software anterior a la actualización 1. Si se produce un error en el restablecimiento de fábrica, póngase en contacto con Microsoft Support para ayudarle con los siguientes pasos. Este problema no se produce con los controladores de reemplazo enviados desde fábrica con la actualización 1 o posterior del software.

	> - Para obtener más información sobre cómo usar este cmdlet, vaya a [Referencia de cmdlet de Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).


## Preguntas y respuestas sobre cómo administrar los controladores de dispositivo

En esta sección, hemos resumido algunas de las preguntas más frecuentes sobre la administración de los controladores de dispositivo de StorSimple.

**P.** ¿Qué ocurre si los dos controladores de mi equipo están activados y figuran como correctos y reinicio o apago el controlador activo?

**R.** Si los dos controladores del dispositivo están activados y figuran como correctos, se le solicitará confirmación. Puede elegir:

- **Reiniciar el controlador activo**: se le notificará que reiniciar un controlador activo provocará la conmutación por error del dispositivo al controlador pasivo. El controlador se reiniciará.

- **Apagar un controlador activo**: se le notificará que apagar un controlador activo resultará en tiempo de inactividad. También deberá presionar el botón de encendido en el dispositivo para activar el controlador.

**P.** ¿Qué ocurre si el controlador pasivo del dispositivo no está disponible o está apagado y reinicio o apago el controlador activo?

**R.** Si el controlador pasivo del dispositivo no está disponible o está desactivado y decide:

- **Reiniciar el controlador activo**: se le notificará que continuar con la operación provocará una interrupción temporal del servicio y se le solicitará confirmación.

- **Apagar un controlador active**: se le notificará que continuar con la operación dará como resultado un tiempo de inactividad y que debe presionar el botón de encendido en uno o ambos controladores para activar el dispositivo. Se le pedirá confirmación.

**P.** ¿Cuándo se produciría un error en el reinicio o el apagado del controlador?

**R.** El reinicio o apagado de un controlador puede producir un error si:

- Ye está en curso una actualización del dispositivo.

- Ya está en curso un reinicio del controlador.

- Ya está en curso un apagado del controlador.

**P.** ¿Cómo se puede saber si se ha reiniciado o apagado un controlador?

**R.** Puede comprobar el estado del controlador en la página Mantenimiento. El estado del controlador indicará si se ha reiniciado o apagado un controlador. Además, la página Alertas contendrá una alerta informativa si se reinicia o apaga el controlador. También se registran las operaciones de reinicio y apagado del controlador en los registros de operaciones. Para obtener más información sobre registros de operaciones, vaya a [Ver el registro de operaciones](storsimple-service-dashboard.md#view-the-operations-logs).

**P.** ¿Qué impacto tiene para las operaciones de E/S una conmutación por error del controlador?

**R.** Las conexiones TCP entre los iniciadores y el controlador activo se reiniciarán como resultado de la conmutación por error del controlador, pero se restablecerán cuando el controlador pasivo asuma la operación. Puede haber una pausa temporal (de menos de 30 segundos) en la actividad de E/S entre los iniciadores y el dispositivo en el transcurso de esta operación.

**P.** ¿Cómo puedo volver a poner en servicio mi controlador después de haberlo apagado y quitado?

**R.** Para que un controlador vuelva a funcionar, se debe insertar en el chasis tal como se describe en [Reemplazar un módulo de controlador en el dispositivo StorSimple](storsimple-controller-replacement.md).

## Pasos siguientes

- Si tiene algún problema con los controladores de su dispositivo StorSimple que no pueda resolver mediante el uso de los procedimientos descritos en este tutorial,[póngase en contacto con el servicio técnico de Microsoft](storsimple-contact-microsoft-support.md).

- Para obtener información sobre el uso del servicio StorSimple Manager, vaya a [Uso del servicio StorSimple Manager para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0224_2016-->