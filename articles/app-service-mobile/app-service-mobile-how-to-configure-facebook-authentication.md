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
	ms.date="11/20/2015"
	ms.author="mahender"/>

# Configuración de la aplicación Servicio de aplicaciones para usar el inicio de sesión de Facebook

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

En este tema se muestra cómo configurar Servicio de aplicaciones de Azure para usar Facebook como proveedor de autenticación.

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Facebook asociada a una dirección de correo electrónico verificada y a un número de teléfono móvil. Para crear una cuenta de Facebook, vaya a [facebook.com].

> [AZURE.NOTE]En este tema se muestra el uso de la característica Autenticación/autorización del Servicio de aplicaciones. Esto reemplaza a la puerta de enlace del Servicio de aplicaciones en la mayoría de las aplicaciones. Las diferencias que se aplican al uso de la puerta de enlace se indican con notas a lo largo de ese tema.


## <a name="register"> </a>Registro de la aplicación con Facebook

1. Inicie sesión en el [Portal de Azure] y vaya a la aplicación. Copie la **Dirección URL**. La usará para configurar la aplicación de Facebook.
 
2. En otra ventana del explorador, navegue hasta el sitio web de [Desarrolladores de Facebook] e inicie sesión con las credenciales de su cuenta de Facebook.

3. (Opcional) Si aún no está registrado, haga clic en **Aplicaciones** > **Registrarse como desarrollador**, acepte la directiva y siga los pasos de registro.

4. Haga clic en **Mis aplicaciones** > **Agregar una nueva aplicación** > **Sitio web**, escriba un nombre único para la aplicación y luego haga clic en **Crear un nuevo identificador de Facebook**.

6. Elija una categoría para la aplicación de la lista desplegable y haga clic en **Crear Id. de aplicación** y, en la siguiente página, haga clic en **Omitir inicio rápido**. Esto le llevará al panel del desarrollador de la aplicación.

8. En el campo **Secreto de la aplicación**, haga clic en **Mostrar**, escriba la contraseña si se le solicita y, a continuación, anote los valores de **Id. de aplicación** y **Secreto de la aplicación**. Más adelante configurará la aplicación para usar estos valores.

	> [AZURE.NOTE]**Nota de seguridad** El secreto de la aplicación es una credencial de seguridad importante, No comparta este secreto con nadie ni lo distribuya en una aplicación cliente.

9. En la barra de navegación de la izquierda, haga clic en **Configuración**, escriba la **Dirección URL** de la aplicación móvil en **Dominios de aplicación** y escriba un **Correo electrónico de contacto**.

    ![][0]

10. Si no ve una sección del sitio web a continuación, haga clic en **Agregar plataforma** > **Sitio web**, escriba la **Dirección URL** de la aplicación móvil en el campo **Dirección URL del sitio** y luego haga clic en **Guardar cambios**.

11. Haga clic en la pestaña **Avanzado**, agregue el **URI de redireccionamiento** de la aplicación a **URI de redireccionamiento de OAuth válidos** y haga clic en **Guardar cambios**. El URI de redireccionamiento es la dirección URL de la aplicación anexada a la ruta de acceso, _/.auth/login/facebook/callback_. Por ejemplo: `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Asegúrese de que está utilizando el esquema HTTPS.


> [AZURE.NOTE]Si usa la puerta de enlace en lugar de la característica Autenticación o autorización del Servicio de aplicaciones, la URL de redireccionamiento usará en su lugar la URL de la puerta de enlace con la ruta de acceso _/signin-facebook_.


12. La cuenta de Facebook que se utilizó para registrar la aplicación es un administrador de la aplicación. En este momento, solo los administradores pueden iniciar sesión en esta aplicación. Para autenticar otras cuentas de Facebook, haga clic en **Estado y revisión** en el panel de navegación de la izquierda. A continuación, haga clic en **Sí** para habilitar el acceso del público en general.


## <a name="secrets"> </a>Agregar información de Facebook a la aplicación

> [AZURE.NOTE]Si usa la puerta de enlace del Servicio de aplicaciones, omita esta sección y en su lugar, vaya a la puerta de enlace en el portal. Seleccione **Configuración**, **Identidad** y luego **Facebook**. Pegue los valores que obtuvo anteriormente y haga clic en **Guardar**.


13. Vuelva al [Portal de Azure] y vaya a la aplicación. Haga clic en **Configuración** > **Autenticación/Autorización** y asegúrese de que **Autenticación del Servicio de aplicaciones** está **Activada**.

15. Haga clic en **Facebook**, pegue los valores de identificador de la aplicación y de secreto de la aplicación que obtuvo anteriormente, habilite opcionalmente los ámbitos que requiera la aplicación y después haga clic en **Aceptar**.

    ![][1]
	
	De forma predeterminada, el Servicio de aplicaciones ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.

17. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante Facebook, establezca el valor de **Acción que se debe realizar cuando no se autentique la solicitud** en **Facebook**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Facebook para la autenticación.

17. Haga clic en **Guardar**.

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

<!----HONumber=AcomDC_1203_2015-->