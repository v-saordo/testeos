<properties 
   pageTitle="Contactar al soporte técnico de Microsoft | Microsoft Azure"
   description="Aprenda a crear una solicitud de soporte técnico y a iniciar una sesión de soporte técnico en su dispositivo StorSimple."
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

# Contactar al soporte técnico de Microsoft

Si tiene algún problema con la solución de Microsoft Azure StorSimple, puede crear una solicitud de servicio de soporte técnico. En una sesión en línea con su ingeniero de soporte técnico, también deberá iniciar una sesión de soporte técnico en su dispositivo StorSimple. Este artículo le enseñará a:

- Crear una solicitud de soporte.
- Iniciar una sesión de soporte en la interfaz de Windows PowerShell del dispositivo de StorSimple.

Revise la sección [Información y contratos de nivel de servicio de soporte de la serie StorSimple 8000](https://msdn.microsoft.com/library/mt433077.aspx) antes de crear una solicitud de soporte técnico.

## Crear una solicitud de soporte

Lleve a cabo los siguientes pasos para crear una solicitud de soporte.

#### Para crear una solicitud de soporte

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en el extremo superior derecho, en el nombre de cuenta y, a continuación, haga clic en **Póngase en contacto con el Soporte técnico de Microsoft**.

	![Contactar al soporte técnico de MS a través del Portal de administración](./media/storsimple-contact-microsoft-support/Ibiza1.png)

2. Se le redirigirá al nuevo portal de Azure (ms.portal.azure.com). Haga clic en el icono **Nueva solicitud de soporte técnico**.

	![Contactar al soporte técnico de MS a través del nuevo portal](./media/storsimple-contact-microsoft-support/Ibiza2.png)

    En el lado derecho de la pantalla, aparecerá el panel **Nueva solicitud de soporte técnico**.

	![Panel Nueva solicitud de soporte](./media/storsimple-contact-microsoft-support/Ibiza3a.png)

3. En el cuadro de diálogo **Aspectos básicos**, haga lo siguiente:
	1. Desde la lista desplegable **Tipo de problema**, seleccione **Técnico**.
	2. Seleccione una **Suscripción** en la lista desplegable.
	3. Desde la lista desplegable **Servicio**, seleccione **StorSimple**. 
	4. Seleccione un **Plan de soporte técnico** en la lista desplegable. Para habilitar el soporte técnico, debe contar con un plan de soporte técnico de pago.

4. Haga clic en **Siguiente**. Aparecerá el cuadro de diálogo **Problema**.

	![Panel Nueva solicitud de soporte](./media/storsimple-contact-microsoft-support/Ibiza5a.png)

5. En el cuadro de diálogo **Problema**, haga lo siguiente:

    1.  Seleccione un nivel de **Gravedad** en la lista desplegable.
    2.  Seleccione un **Tipo de problema** en la lista desplegable.
    3.  Seleccione una **Categoría** en la lista desplegable. 
    4.  En el campo **Detalles**, escriba una descripción breve del problema.
    5.  En el cuadro **Período de tiempo**, indique la fecha, la hora y la zona horaria correspondiente a la aparición más reciente del problema.
    6.  En **Carga de archivos**, haga clic en el icono de carpeta para buscar el paquete de soporte técnico.
    7.  Seleccione la casilla de verificación **Compartir información de diagnóstico**.

6. Haga clic en **Siguiente**. Aparecerá el cuadro de diálogo **Información de contacto**.

	![Panel Nueva solicitud de soporte](./media/storsimple-contact-microsoft-support/Ibiza6a.png)

7. Escriba la información de contacto y seleccione un método de contacto (teléfono o correo electrónico).

8. Seleccione la casilla de verificación **Guardar cambios de contacto para solicitudes futuras de soporte técnico**.

9. Haga clic en **Crear**.

Una vez que ha enviado la solicitud, un ingeniero de soporte se pondrá en contacto con usted tan pronto como sea posible para procesar su solicitud.

## Iniciar una sesión de soporte en Windows PowerShell para StorSimple

Para solucionar los problemas que pudiera experimentar con el dispositivo StorSimple, deberá ponerse en contacto con el equipo de soporte técnico de Microsoft. Es posible que el soporte técnico de Microsoft necesite utilizar una sesión de soporte para iniciar sesión en su dispositivo.

Lleve a cabo los siguientes pasos para iniciar una sesión de soporte.

#### Para iniciar una sesión de soporte

1. Acceda al dispositivo directamente desde la consola serie o a través de una sesión de telnet desde un equipo remoto. Para ello, siga los pasos descritos en [Uso de PuTTy para conectarse a la consola serie del dispositivo](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. En la sesión que se abre, presione la tecla **Intro** para obtener un símbolo del sistema.

3. En el menú de la consola serie, seleccione la opción 1, **iniciar sesión con acceso completo**.

4. En el símbolo del sistema, escriba la siguiente contraseña:

	`Password1`

5. En el símbolo del sistema, escriba el siguiente comando:

	`Enable-HcsSupportAccess`

6. Aparecerá una cadena cifrada. Copie esta cadena en un editor de texto como el Bloc de notas.

7. Guarde esta cadena y envíela por correo electrónico al soporte técnico de Microsoft.

> [AZURE.IMPORTANT] Puede deshabilitar el acceso al soporte técnico ejecutando `Disable-HcsSupportAccess`. El dispositivo StorSimple también intenta deshabilitar el acceso al soporte técnico 8 horas después de iniciada la sesión. Es un procedimiento recomendado cambiar las credenciales de su dispositivo StorSimple después de una sesión de soporte técnico.

<!---HONumber=AcomDC_0224_2016-->