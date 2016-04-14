

1. Si aún no ha registrado la aplicación, vaya a la página [Enviar una aplicación] en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión en su cuenta Microsoft y, a continuación, haga clic en **Nombre de la aplicación**.

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Escriba el nombre de la aplicación en **Nombre de la aplicación**, haga clic en **Reservar nombre de aplicación** y, a continuación, haga clic en **Guardar**.

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png)

   	Se crea un nuevo registro de la Tienda Windows para su aplicación.

3. En Visual Studio, abra el proyecto de aplicación de la Tienda Windows que creó al completar el tutorial **Introducción a Servicios móviles**.

4. En el Explorador de soluciones, haga clic con el botón secundario en el proyecto, haga clic en **Almacén** y, a continuación, haga clic en **Asociar aplicación con la Tienda...**.

  	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-store-association.png)

   	Se muestra el asistente para **asociar la aplicación con la Tienda Windows**.

5. En el asistente, haga clic en **Iniciar sesión** y, a continuación, inicie sesión con su cuenta de Microsoft.

6. Seleccione la aplicación que ha registrado en el paso 2, haga clic en **Siguiente** y luego en **Asociar**.

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png)

   	Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.

7. (Opcional) Repita los pasos 4 a 6 para también registrar el proyecto de la aplicación de la Tienda de Windows Phone de una aplicación universal de Windows.

8. De nuevo en la página del Centro de desarrollo de Windows de su nueva aplicación, haga clic en **Servicios**.

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png)

9. En la página Servicios, haga clic en el **sitio Servicios Live** de **Servicios móviles de Azure**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

10. Haga clic en **Autenticación del servicio** y anote los valores de **Secreto de cliente** e **Identificador de seguridad de paquete (SID)**.

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    > [AZURE.NOTE]El secreto de cliente y el SID del paquete son credenciales de seguridad importantes. No comparta esta información con nadie ni la distribuya con su aplicación.

11. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y luego haga clic en la aplicación.

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-selection.png)

12. Haga clic en la pestaña **Insertar** y escriba los valores **Secreto del cliente** y **SID del paquete** obtenidos de WNS en el paso 4 y, a continuación, haga clic en **Guardar**.

   	![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-push-tab.png)

	>[AZURE.NOTE]Al configurar las credenciales de WNS para las notificaciones de inserción mejoradas en la pestaña **Insertar** del portal, se comparten con los Centros de notificaciones para configurar el centro de notificaciones para la aplicación.

<!-- URLs. -->
[Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582

<!---HONumber=AcomDC_1203_2015-->