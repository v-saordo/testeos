
1. Si aún no ha registrado la aplicación, vaya a la página [Enviar una aplicación] en el Centro de desarrollo de aplicaciones de la Tienda Windows, inicie sesión en su cuenta Microsoft y, a continuación, haga clic en **Nombre de la aplicación**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Seleccione **Crear una nueva aplicación reservando un nombre único**, haga clic en **Continuar** y, a continuación, escriba un nombre para la aplicación en **Nombre de la aplicación**, haga clic en **Reservar nombre de aplicación**, y, a continuación, haga clic en **Guardar**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	Se crea un nuevo registro de la Tienda Windows para su aplicación.

3. En Visual Studio, abra el proyecto que ha creado al completar el tutorial [Introducción a Servicios móviles].

4. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto de la aplicación para la Tienda Windows, haga clic en **Tienda** y, a continuación, en **Asociar aplicación con la Tienda...**.

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	Se muestra el asistente para **asociar la aplicación con la Tienda Windows**.

5. En el asistente, haga clic en **Iniciar sesión** y, a continuación, inicie sesión con su cuenta de Microsoft, seleccione la aplicación registrada en el paso 2, haga clic en **Siguiente** y, a continuación, haga clic en **Asociar**.

   	Se agrega la información de registro necesaria de la Tienda Windows al manifiesto de aplicación.

6. (Opcional) Para una aplicación Windows universal, repita los pasos 4 y 5 para el proyecto de la Tienda de Windows Phone.

6. De nuevo en la página del Centro de desarrollo de Windows de su nueva aplicación, haga clic en **Servicios**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png)

7. En la página Servicios, haga clic en el **sitio Servicios Live** de **Servicios móviles de Azure**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png)

8. Haga clic en **Configuración de API**, seleccione habilitar la **Aplicación cliente de escritorio o móvil**, proporcione la dirección URL de servicio móvil como el **Dominio de destino**, proporcione un valor de `https://<mobile_service>.azure-mobile.net/login/microsoftaccount/` en **Dirección URL de redireccionamiento** y, a continuación, haga clic en **Guardar**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

9. En **Configuración de aplicaciones**, anote los valores de **Id. de cliente**, **Secreto de cliente** e **Identificador de seguridad de paquete (SID)**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]El secreto de cliente y el SID del paquete son credenciales de seguridad importantes. No comparta esta información con nadie ni la distribuya con su aplicación.

10. Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Servicios móviles** y luego haga clic en la aplicación.

11. Haga clic en la pestaña **Identidad**, escriba los valores **Secreto del cliente** e **Identificador de seguridad de paquete (SID)** obtenidos de WNS en el paso 4 y, a continuación, haga clic en **Guardar**.

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. Haga clic en la pestaña **Identidad**. Observe que los valores de **Secreto del cliente** y **SID del paquete** ya se establecieron en el paso anterior. Especifique el **Identificador de cliente** que anotó anteriormente y haga clic en **Guardar**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
De este modo ya estará listo para usar una cuenta Microsoft para autenticarse en su aplicación.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Introducción a Servicios móviles]: /develop/mobile/tutorials/get-started/#create-new-service
[Enviar una aplicación]: http://go.microsoft.com/fwlink/p/?LinkID=266582

<!---HONumber=AcomDC_1203_2015-->