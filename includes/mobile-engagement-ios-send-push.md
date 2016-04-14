###Conceda acceso al certificado push para Mobile Engagement.

Para permitir que Mobile Engagement envíe notificaciones push en su nombre, deberá concederle acceso al certificado. Para ello, hay que configurar y registrar su certificado en el portal de Mobile Engagement. Asegúrese de obtener el certificado. p12, tal y como se explica en la [documentación de Apple](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6).

1. Desplácese hasta el portal de Mobile Engagement. Asegúrese de que se encuentra en el lugar correcto y haga clic en el botón **Interactuar** en la parte inferior:

	![](./media/mobile-engagement-ios-send-push/engage-button.png)

2. Haga clic en la página **Configuración** en el Portal de Engagement. Desde allí, haga clic en la sección **Inserción nativa** para cargar el certificado p12:

	![](./media/mobile-engagement-ios-send-push/engagement-portal.png)

3. Elija el certificado p12, cárguelo y escriba la contraseña:

	![](./media/mobile-engagement-ios-send-push/native-push-settings.png)

##<a id="send"></a>Enviar una notificación a su aplicación

Ahora crearemos una campaña sencilla de notificación push que enviará una inserción a nuestra aplicación:

1. Vaya a la pestaña **Cobertura** en el portal de Mobile Engagement.

2. Haga clic en **Nuevo anuncio** para crear la campaña de inserción

	![](./media/mobile-engagement-ios-send-push/new-announcement.png)

3. Configure los primeros campos de la campaña:

	![](./media/mobile-engagement-ios-send-push/campaign-first-params.png)

	- 	Proporcione un **Nombre** para la campaña. 
	- 	Especifique la **Hora de entrega** como **Fuera de aplicación solo**: este es el tipo de notificación de inserción sencilla de Apple que incluye texto.
	- 	En el texto de notificación, escriba primero el **Título** que será la primera línea del anuncio push.
	- 	A continuación, escriba el **Mensaje** que será la segunda línea.

4. Desplácese hacia abajo y en la sección de contenido, elija **Solo notificación**.

	![](./media/mobile-engagement-ios-send-push/campaign-content.png)

5. Ya ha terminado de configurar la campaña más básica. Ahora desplácese hacia abajo y haga clic en el botón **Crear** para guardar la notificación de inserción.

6. Por fin, haga clic en **Activar** para enviar la notificación de inserción.

	![](./media/mobile-engagement-ios-send-push/campaign-activate.png)

7. Podrá recibir una notificación en el dispositivo iOS en el centro de notificaciones como la siguiente:

	![](./media/mobile-engagement-ios-send-push/iphone-notification.png)

8. Si tiene un Apple Watch conectado a este dispositivo iOS, verá la notificación en el Apple Watch:

	![](./media/mobile-engagement-ios-send-push/apple-watch.png)


 

 

<!---HONumber=Nov15_HO2-->