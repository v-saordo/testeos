<properties
	pageTitle="Configuración de la autenticación mediante Twitter para la aplicación de Servicios de aplicaciones"
	description="Obtenga información acerca de cómo configurar la autenticación mediante Twitter para la aplicación de Servicios de aplicaciones."
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
	ms.date="01/13/2016"
	ms.author="mahender"/>

# Configuración de la aplicación Servicio de aplicaciones para usar el inicio de sesión de Twitter

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

En este tema se muestra cómo configurar Servicio de aplicaciones de Azure para usar Twitter como proveedor de autenticación.

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Twitter con una dirección de correo electrónico verificada. Para crear una cuenta de Twitter, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.


> [AZURE.NOTE]
En este tema se muestra el uso de la característica Autenticación/autorización del Servicio de aplicaciones. Esto reemplaza a la puerta de enlace del Servicio de aplicaciones en la mayoría de las aplicaciones. Las diferencias que se aplican al uso de la puerta de enlace se indican con notas a lo largo de ese tema.


## <a name="register"> </a>Registro de la aplicación con Twitter


1. Inicie sesión en el [Portal de Azure] y vaya a la aplicación. Copie la **Dirección URL**. La usará para configurar la aplicación de Twitter.

2. Desplácese hasta el sitio web para [desarrolladores de Twitter], inicie sesión con las credenciales de la cuenta de Twitter y haga clic en **Crear nueva aplicación**.

3. Escriba el **Nombre** y una **Descripción** para la nueva aplicación. Pegue la **URL** de su aplicación en el valor de **Sitio web**. Después, en **URL de devolución de llamada**, pegue la **URL de devolución de llamada** que copió anteriormente. Se trata de la puerta de enlace de la aplicación móvil anexada a la ruta de acceso, _/.auth/login/twitter/callback_. Por ejemplo: `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Asegúrese de que está utilizando el esquema HTTPS.

	> [AZURE.NOTE]
	Si usa la puerta de enlace en lugar de la característica Autenticación o autorización del Servicio de aplicaciones, la URL de redireccionamiento usará en su lugar la URL de la puerta de enlace con la ruta de acceso _/signin-twitter_.

3.  En la parte inferior de la página, lea y acepte los términos. A continuación, haga clic en **Crear aplicación de Twitter**. De esta forma, la aplicación se registra y se muestran los detalles correspondientes.

4. Haga clic en la pestaña **Configuración**, active **Permitir que esta aplicación se use para iniciar sesión en Twitter** y, a continuación, haga clic en **Actualizar la configuración de esta aplicación de Twitter**.

5. Seleccione la pestaña **Claves y tokens de acceso**. Tome nota de los valores de **Clave de consumidor (clave de API)** y **Secreto del consumidor (secreto de API)**.

    > [AZURE.NOTE] El secreto de consumidor es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.


## <a name="secrets"> </a>Adición de información de Twitter a su aplicación

> [AZURE.NOTE]
Si usa la puerta de enlace del Servicio de aplicaciones, omita esta sección y en su lugar, vaya a la puerta de enlace en el portal. Seleccione **Configuración**, **Identidad** y, luego, **Twitter**. Pegue los valores que obtuvo anteriormente y haga clic en **Guardar**.


13. Vuelva al [Portal de Azure] y vaya a la aplicación. Haga clic en **Configuración** y luego en **Autenticación o autorización**.

14. Si esta característica no está habilitada, mueva el interruptor a la posición de **activada**.

15. Haga clic en **Twitter**. Pegue los valores de Id. de aplicación y Secreto de la aplicación que obtuvo anteriormente. y, a continuación, haga clic en **Aceptar**.

    ![][1]

	De forma predeterminada, el Servicio de aplicaciones ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.

17. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante Twitter, establezca **Acción por realizar cuando no se autentique la solicitud** en **Twitter**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Twitter para la autenticación.

17. Haga clic en **Guardar**.

De este modo ya estará listo para usar Twitter para realizar la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[desarrolladores de Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Portal de Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md

<!---HONumber=AcomDC_0128_2016-->