<properties
	pageTitle="Configuración de la autenticación mediante Facebook para la aplicación de Servicios de aplicaciones"
	description="Obtenga información acerca de cómo configurar la autenticación mediante Facebook para la aplicación de Servicios de aplicaciones."
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
	ms.date="02/28/2016"
	ms.author="mahender"/>

# Configuración de la aplicación Servicio de aplicaciones para usar el inicio de sesión de Facebook

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este tema se muestra cómo configurar Servicio de aplicaciones de Azure para usar Facebook como proveedor de autenticación.

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Facebook asociada a una dirección de correo electrónico verificada y a un número de teléfono móvil. Para crear una cuenta de Facebook, vaya a [facebook.com].

## <a name="register"> </a>Registro de la aplicación con Facebook

1. Inicie sesión en el [Portal de Azure] y vaya a la aplicación. Copie la **Dirección URL**. La usará para configurar la aplicación de Facebook.

2. En otra ventana del explorador, navegue hasta el sitio web de [Desarrolladores de Facebook] e inicie sesión con las credenciales de su cuenta de Facebook.

3. (Opcional) Si aún no está registrado, haga clic en **Aplicaciones** > **Registrarse como desarrollador**, acepte la directiva y siga los pasos de registro.

4. Haga clic en **Mis aplicaciones** > **Agregar una nueva aplicación** > **Sitio web** > **Omitir y crear id. de aplicación**.

5. En **Nombre para mostrar**, escriba un nombre único para la aplicación, elija una **Categoría** para la aplicación, haga clic en **Crear id. de aplicación** y complete la comprobación de seguridad. Esto le llevará al panel del desarrollador de la nueva aplicación de Facebook.

6. En el campo **Secreto de la aplicación**, haga clic en **Mostrar**, escriba la contraseña si se le solicita y, a continuación, anote los valores de **Id. de aplicación** y **Secreto de la aplicación**. Más adelante lo usará para configurar la aplicación en Azure.

	> [AZURE.IMPORTANT] El secreto de aplicación es una credencial de seguridad importante, No comparta este secreto con nadie ni lo distribuya en una aplicación cliente.

7. En la barra de navegación de la izquierda, haga clic en **Configuración**, escriba la **Dirección URL** de la aplicación móvil en **Dominios de aplicación** y escriba un **Correo electrónico de contacto**.

    ![][0]

8. Si no ve una sección del sitio web a continuación, haga clic en **Agregar plataforma** > **Sitio web**, escriba la **Dirección URL** de la aplicación móvil en el campo **Dirección URL del sitio** y luego haga clic en **Guardar cambios**.

9. Haga clic en la pestaña **Avanzado**, agregue el **URI de redireccionamiento** de la aplicación a **URI de redireccionamiento de OAuth válidos** y haga clic en **Guardar cambios**.

	> [AZURE.NOTE] El URI de redireccionamiento es la dirección URL de la aplicación anexada a la ruta de acceso, _/.auth/login/facebook/callback_. Por ejemplo: `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Asegúrese de que está utilizando el esquema HTTPS.

10. La cuenta de Facebook que se utilizó para registrar la aplicación es un administrador de la aplicación. En este momento, solo los administradores pueden iniciar sesión en esta aplicación. Para autenticar otras cuentas de Facebook, haga clic en **Revisión de aplicaciones** y habilite **Hacer público todolist-complete-nodejs** para habilitar el acceso público general mediante la autenticación de Facebook.


## <a name="secrets"> </a>Agregar información de Facebook a la aplicación

1. Vuelva al [Portal de Azure] y vaya a la aplicación. Haga clic en **Configuración** > **Autenticación/autorización** y asegúrese de que **Autenticación del servicio de aplicaciones** está **Activada**.

2. Haga clic en **Facebook**, pegue los valores de identificador de la aplicación y de secreto de la aplicación que obtuvo anteriormente, habilite opcionalmente los ámbitos que requiera la aplicación y después haga clic en **Aceptar**.

    ![][1]

	De forma predeterminada, el Servicio de aplicaciones ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.

3. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante Facebook, establezca el valor de **Acción que se debe realizar cuando no se autentique la solicitud** en **Facebook**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Facebook para la autenticación.

4. Cuando termine de configurar la autenticación, haga clic en **Guardar**.

De este modo ya estará listo para usar Facebook para realizar la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Desarrolladores de Facebook]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /es-ES/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal de Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0302_2016-->