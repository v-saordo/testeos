
Tiene la posibilidad de probar las notificaciones de inserción con su servicio móvil que se ejecuta en el equipo o máquina virtual local antes de publicar en Azure. Para ello, debe establecer información acerca del centro de notificaciones utilizado por su aplicación en el archivo web.config. Esta información se utiliza solo cuando se ejecuta localmente para conectar con el centro de notificaciones; se ignora cuando se publica en Azure.

1. De nuevo en la pestaña **Insertar** de su servicio móvil, haga clic en el vínculo **Centro de notificaciones**.

	![](./media/mobile-services-dotnet-backend-configure-local-push/link-to-notification-hub.png)

	Esto le lleva al centro de notificaciones que utiliza su servicio móvil.

2. En la página del centro de notificaciones, anote el nombre de su centro de notificaciones y, a continuación, haga clic en **Ver cadena de conexión**.

	![](./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-page.png)

3. En la página **Información de conexión de acceso**, copie la cadena de conexión **DefaultFullSharedAccessSignature**.

	![](./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-connection-string.png)

4. En su proyecto de servicio móvil de Visual Studio, abra el archivo Web.config del servicio y, en **connectionStrings**, sustituya la cadena de conexión para **MS\_NotificationHubConnectionString** por la cadena de conexión del paso anterior.

5. En **appSettings**, sustituya el valor de la configuración de aplicación **MS\_NotificationHubName** por el nombre del centro de notificaciones.

Ahora, el proyecto de servicio móvil está configurado para conectarse al centro de notificaciones en Azure cuando se ejecuta localmente. Tenga en cuenta que es importante utilizar el mismo nombre de centro de notificaciones y la misma cadena de conexión que el portal porque esta configuración de proyecto de Web.config se sobrescribe con la configuración del portal cuando se ejecuta en Azure.

<!---HONumber=Oct15_HO3-->