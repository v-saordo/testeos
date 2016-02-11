<properties
	pageTitle="Configuración de la autenticación mediante Google para la aplicación de Servicios de aplicaciones"
	description="Obtenga información acerca de cómo configurar la autenticación mediante Google para la aplicación de Servicios de aplicaciones."
    services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="11/20/2015"
	ms.author="mahender"/>

# Configuración de la aplicación Servicio de aplicaciones para usar el inicio de sesión de Google

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

En este tema se muestra cómo configurar Servicio de aplicaciones de Azure para usar Google como proveedor de autenticación.

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Google asociada a una dirección de correo electrónico verificada. Para crear una cuenta de Google, vaya a [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).

> [AZURE.NOTE]En este tema se muestra el uso de la característica Autenticación/autorización del Servicio de aplicaciones. Esto reemplaza a la puerta de enlace del Servicio de aplicaciones en la mayoría de las aplicaciones. Las diferencias que se aplican al uso de la puerta de enlace se indican con notas a lo largo de ese tema.


## <a name="register"> </a>Registro de la aplicación con Google

1. Inicie sesión en el [Portal de Azure] y vaya a la aplicación. Copie la **Dirección URL**. La usará para configurar la aplicación de Google.
 
2. Diríjase al sitio web [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303), inicie sesión con las credenciales de su cuenta de Google, haga clic en **Crear proyecto**, proporcione un **Nombre de proyecto**, y haga clic en **Crear**.

3. En la barra de navegación de la izquierda, haga clic en **API y autenticación** y luego, en **API sociales**, haga clic en **API de Google+** > **Habilitar API**.

4. Haga clic en **API y autenticación** > **Credenciales** > **Pantalla de consentimiento de OAuth**, seleccione su **Dirección de correo electrónico**, escriba un **Nombre de producto** y haga clic en **Guardar**.

5. En la pestaña **Credenciales**, haga clic en **Agregar credenciales** > **Id. de cliente de OAuth 2.0** y luego seleccione **Aplicación web**.

6. Pegue la **URL** del Servicio de aplicaciones que copió anteriormente en **Orígenes de JavaScript autorizados** y luego pegue el **URI de redirección** que copió antes en el **URI de redirección autorizado**. El URI de redireccionamiento es la dirección URL de la aplicación anexada a la ruta de acceso, _/.auth/login/google/callback_. Por ejemplo: `https://contoso.azurewebsites.net/.auth/login/google/callback`. Asegúrese de que está utilizando el esquema HTTPS. A continuación, haga clic en **Crear**.


	> [AZURE.NOTE]Si usa la puerta de enlace en lugar de la característica Autenticación o autorización del Servicio de aplicaciones, la URL de redireccionamiento usará en su lugar la URL de la puerta de enlace con la ruta de acceso _/signin-google_.


7. En la siguiente pantalla, tome nota de los valores de id. de cliente y el secreto del cliente.


    > [AZURE.IMPORTANT]El secreto de cliente es una credencial de seguridad importante, No comparta este secreto con nadie ni lo distribuya en una aplicación cliente.


## <a name="secrets"> </a>Adición de información de Google a la aplicación

> [AZURE.NOTE]Si usa la puerta de enlace del Servicio de aplicaciones, omita esta sección y en su lugar, vaya a la puerta de enlace en el portal. Seleccione **Configuración**, **Identidad** y, luego, **Google**. Pegue los valores que obtuvo anteriormente y haga clic en **Guardar**.


8. Vuelva al [Portal de Azure] y vaya a la aplicación. Haga clic en **Configuración** y luego en **Autenticación o autorización**.

9. Si esta característica no está habilitada, mueva el interruptor a la posición de **activada**.

10. Haga clic en **Google**. Pegue los valores de identificador de la aplicación y de secreto de la aplicación que obtuvo previamente y habilite opcionalmente los ámbitos que requiere la aplicación. y, a continuación, haga clic en **Aceptar**.

    ![][1]

	De forma predeterminada, el Servicio de aplicaciones ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.

17. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante Google, establezca **Acción por realizar cuando no se autentique la solicitud** en **Google**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Google para la autenticación.

12. Haga clic en **Guardar**.

De este modo ya estará listo para usar Google para realizar la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal de Azure]: https://portal.azure.com/
 

<!---HONumber=AcomDC_1203_2015-->