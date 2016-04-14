Los clientes de Azure pueden desbloquear 25.000 correos electrónicos gratuitos cada mes. Estos 25.000 correos electrónicos mensuales gratuitos le darán acceso a funciones avanzadas de informes y análisis, así como a [todas las API][] (Web, SMTP, Event, Parse, etc.). Para obtener información sobre los servicios adicionales que ofrece SendGrid, consulte la página [Características de SendGrid][].

### Para registrarse y obtener una cuenta SendGrid

1. Inicie sesión en el [Portal de administración de Azure][].

2. En el panel inferior del portal de administración, haga clic en **Nuevo**.

	![command-bar-new][command-bar-new]

3. Haga clic en **Marketplace**.

	![sendgrid-store][sendgrid-store]

4. En el cuadro de diálogo **Elegir una aplicación y servicio**, seleccione **SendGrid** y haga clic en la flecha derecha.

5. En el cuadro de diálogo **Personalizar aplicación y servicio**, seleccione el plan de **SendGrid** al que desea suscribirse.

6. Escriba un nombre para identificar el servicio de **SendGrid** en su configuración de Azure o use el valor predeterminado de **SendGridEmailDelivery.Simplified.SMTPWebAPI**. Los nombres deben tener de 1 a 100 caracteres y contener únicamente caracteres alfanuméricos, guiones, puntos y caracteres de subrayado. El nombre debe ser único en la lista de elementos de la Tienda de Azure.

	![store-screen-2][store-screen-2]

7. Elija un valor para la región; por ejemplo, Oeste de EE. UU.

8. Haga clic en la flecha derecha.

9. En la pestaña **Revisar compra**, revise la información del plan y de precios, y consulte los términos legales. Si acepta los términos, haga clic en la marca de verificación. Después de hacer clic en la marca de verificación, la cuenta SendGrid comenzará el [Proceso de aprovisionamiento de SendGrid].

	![store-screen-3][store-screen-3]

10. Después de confirmar la compra, se le redirigirá al panel de complementos, donde verá el mensaje **Comprando SendGrid**.

	![sendgrid-purchasing-message][sendgrid-purchasing-message]

	Su cuenta SendGrid se aprovisiona inmediatamente y verá el mensaje **Se adquirió correctamente el complemento SendGrid**. Ya están creadas su cuenta y sus credenciales. Y está preparado para enviar correos electrónicos en este momento.

	Para modificar el plan de suscripción o ver la configuración de contacto de SendGrid, haga clic en el nombre de su servicio SendGrid para abrir el panel del Marketplace de SendGrid.

	![sendgrid-add-on-dashboard][sendgrid-add-on-dashboard]

	Para enviar un correo electrónico usando SendGrid, debe proporcionar las credenciales de la cuenta (nombre de usuario y contraseña).

### Para encontrar las credenciales de SendGrid ###

1. Haga clic en **Información de conexión**.

	![sendgrid-connection-info-button][sendgrid-connection-info-button]

2. En el cuadro de diálogo *Información de conexión*, copie los valores de **Contraseña** y Nombre de usuario para utilizarlos más adelante en este tutorial.

	![sendgrid-connection-info][sendgrid-connection-info]

	Para configurar las opciones de entrega de correo electrónico, haga clic en el botón **Administrar**. De esta forma se le redirigirá a su Panel de Control de SendGrid.

	![sendgrid-control-panel][sendgrid-control-panel]

	Para obtener más información sobre cómo comenzar con SendGrid, consulte [Introducción a SendGrid][].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[Características de SendGrid]: http://sendgrid.com/features
[Portal de administración de Azure]: https://manage.windowsazure.com
[Introducción a SendGrid]: http://sendgrid.com/docs
[Proceso de aprovisionamiento de SendGrid]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[todas las API]: https://sendgrid.com/docs/API_Reference/index.html

<!---HONumber=Oct15_HO3-->