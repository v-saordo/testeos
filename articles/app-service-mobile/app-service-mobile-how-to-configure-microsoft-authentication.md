<properties
	pageTitle="Configuración de la autenticación mediante la cuenta Microsoft para la aplicación de Servicios de aplicaciones"
	description="Obtenga información acerca de cómo configurar la autenticación mediante la cuenta Microsoft para la aplicación de Servicios de aplicaciones."
	authors="mattchenderson" 
	services="app-service\mobile"
	documentationCenter=""
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

# Configuración de la aplicación Servicio de aplicaciones para usar el inicio de sesión de la cuenta Microsoft

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

En este tema se muestra cómo configurar Servicio de aplicaciones de Azure para usar la cuenta Microsoft como proveedor de autenticación.


> [AZURE.NOTE]En este tema se muestra el uso de la característica Autenticación/autorización del Servicio de aplicaciones. Esto reemplaza a la puerta de enlace del Servicio de aplicaciones en la mayoría de las aplicaciones. Las diferencias que se aplican al uso de la puerta de enlace se indican con notas a lo largo de ese tema.


## <a name="register"> </a>Registro de la aplicación con la cuenta Microsoft

1. Inicie sesión en el [Portal de Azure] y vaya a la aplicación. Copie la **Dirección URL**. La usará para configurar la aplicación de la cuenta Microsoft.

2. Vaya a la página [Mis aplicaciones] del Centro para desarrolladores de la cuenta Microsoft e inicie sesión con su cuenta Microsoft, si procede.

4. Haga clic en **Crear aplicación** y, a continuación, escriba el **nombre de la aplicación** y haga clic en **Acepto**.

5. Haga clic en **Configuración de API**. Seleccione **Sí** para **Aplicación cliente móvil o de escritorio**. En el **dirección URL de redireccionamiento** introduzca su aplicación **dirección URL de redireccionamiento** y haga clic en **Guardar**. El URI de redireccionamiento es la dirección URL de la aplicación anexada a la ruta de acceso _/.auth/login/microsoftaccount/callback_. Por ejemplo, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Asegúrese de que está utilizando el esquema HTTPS.

	![][0]


	> [AZURE.NOTE]Si usa la puerta de enlace en lugar de la característica Autenticación o autorización del Servicio de aplicaciones, la URL de redireccionamiento usará en su lugar la URL de la puerta de enlace con la ruta de acceso _/signin-microsoft_.


6. Haga clic en **Configuración de aplicaciones** y tome nota de los valores de **Id. de cliente** y **Secreto de cliente**.


    > [AZURE.NOTE]El secreto de cliente es una credencial de seguridad importante, No comparta este secreto del cliente con nadie ni lo distribuya en una aplicación cliente.
	

## <a name="secrets"> </a>Adición de información de la cuenta de Microsoft a la aplicación

> [AZURE.NOTE]Si usa la puerta de enlace del Servicio de aplicaciones, omita esta sección y en su lugar, vaya a la puerta de enlace en el portal. Seleccione **Configuración**, **Identidad** y, luego, **Cuenta Microsoft**. Pegue los valores que obtuvo anteriormente y haga clic en **Guardar**.


7. Vuelva al [Portal de Azure] y vaya a la aplicación. Haga clic en **Configuración** y luego en **Autenticación o autorización**.

8. Si esta característica no está habilitada, mueva el interruptor a la posición de **activada**.

9. Haga clic en **Cuenta Microsoft**. Pegue los valores de identificador de la aplicación y de secreto de la aplicación que obtuvo previamente y habilite opcionalmente los ámbitos que requiere la aplicación. y, a continuación, haga clic en **Aceptar**.

    ![][1]
	
	De forma predeterminada, el Servicio de aplicaciones ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.

17. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante la cuenta Microsoft, establezca **Acción por realizar cuando no se autentique la solicitud** en **Cuenta Microsoft**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a la cuenta Microsoft para la autenticación.

11. Haga clic en **Guardar**.


De este modo ya estará listo para usar la cuenta Microsoft para realizar la autenticación en la aplicación.

## <a name="related-content"> </a>Contenido relacionado

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Mis aplicaciones]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Portal de Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_1203_2015-->