Los pasos siguientes permiten registrar su aplicación en la Tienda Windows, configurar su servicio móvil para habilitar las notificaciones push y agregar código a su aplicación para registrar un canal de dispositivo en el centro de notificaciones. Visual Studio 2013 se conecta a Azure y a la Tienda Windows mediante las credenciales que proporcione el usuario.

1. En Visual Studio 2013, abra el Explorador de soluciones, haga clic con el botón derecho en el proyecto de aplicación de la Tienda Windows, haga clic en **Agregar** y, a continuación, en **Notificación push...**. 

	![Agregar el Asistente para agregar notificaciones push en Visual Studio 2013](./media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)

	De este modo, se inicia el Asistente para agregar notificaciones push.

2. Haga clic en **Siguiente**, inicie sesión en su cuenta de la Tienda Windows, proporcione un nombre en **Reservar un nombre nuevo** y haga clic en **Reservar**.

	Se crea un nuevo registro de aplicaciones.

3. Haga clic en el nuevo registro en la lista **Nombre de aplicación** y, a continuación, en **Siguiente**.

4. En la página **Seleccionar un servicio** y haga clic en el nombre del servicio móvil; a continuación, haga clic en **Siguiente** y en **Finalizar**.

	El centro de notificaciones que su servicio móvil usa se actualiza con el registro de Servicios de notificaciones de inserción de Windows (WNS). Ahora podrá usar Centros de notificaciones de Azure para enviar notificaciones desde Servicios móviles a su aplicación mediante WNS.

	>[AZURE.NOTE]En este tutorial se muestra el envío de notificaciones desde un back-end de servicio móvil. Puede usar el mismo registro del centro de notificaciones para enviar notificaciones desde cualquier servicio back-end. Para obtener más información, consulte [Introducción a los Centros de notificaciones](http://msdn.microsoft.com/library/azure/jj927170.aspx).

5. Al completar el asistente, se abre la página **La configuración de las inserciones casi se ha completado** en Visual Studio. En esta página se incluyen detalles sobre un método alternativo con el fin configurar el proyecto de servicio móvil para enviar notificaciones que difiere de este tutorial.

	El código que el Asistente para agregar notificaciones push agrega a su solución de aplicación de Windows es específico de la plataforma. Más adelante en esta sección, quitará esta redundancia al compartir el código del cliente de Servicios móviles, lo que facilita el mantenimiento de la aplicación universal.

<!-- URLs. -->
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet/

<!---HONumber=Oct15_HO3-->