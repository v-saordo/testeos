<properties
	pageTitle="Configuración de la autenticación mediante la cuenta Microsoft para la aplicación de Servicios de aplicaciones"
	description="Obtenga información acerca de cómo configurar la autenticación mediante la cuenta Microsoft para la aplicación de Servicios de aplicaciones."
	authors="mattchenderson"
	services="app-service"
	documentationCenter=""
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/24/2016"
	ms.author="mahender"/>

# Configuración de la aplicación Servicio de aplicaciones para usar el inicio de sesión de la cuenta Microsoft

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este tema se muestra cómo configurar Servicio de aplicaciones de Azure para usar la cuenta Microsoft como proveedor de autenticación.

## <a name="register-windows-dev-center"> </a>Registrar la aplicación de Windows en el Centro de desarrollo de Windows

Las aplicaciones universales de Windows y las aplicaciones de la Tienda Windows deben registrarse mediante el Centro de desarrollo de Windows. Esto le permitirá configurar más fácilmente en el futuro las notificaciones push para la aplicación.

>[AZURE.NOTE]Omita esta sección para las aplicaciones de Windows Phone 8, Windows Phone 8.1 Silverlight y las que no pertenezcan a Windows. Si ya configuró las notificaciones push para la aplicación de Windows, también puede omitir esta sección.

1. Inicie sesión en el [Portal de Azure] y vaya a la aplicación. Copie la **Dirección URL**. La usará para configurar la aplicación con la cuenta Microsoft.

2. Si aún no ha registrado la aplicación, vaya al [Centro de desarrollo de Windows](https://dev.windows.com/dashboard/Application/New), inicie sesión en su cuenta Microsoft, escriba un nombre para su aplicación, compruebe su disponibilidad y haga clic en **Reservar nombre de aplicación**.

3. Abra su proyecto de aplicación de Windows en Visual Studio y en el Explorador de soluciones, haga clic con el botón derecho en el proyecto de la aplicación de la Tienda Windows, haga clic en **Tienda** > **Asociar aplicación con la Tienda...**.

  	![](./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-windows-store-association.png)

4. En el asistente, haga clic en **Iniciar sesión** e inicie sesión con su cuenta de Microsoft, seleccione el nombre de la aplicación que haya reservado y luego haga clic en **Siguiente** > **Asociar**. Para una aplicación universal de Windows 8.1, debe repetir los pasos 4 y 5 para el proyecto de la Tienda de Windows Phone.

6. De nuevo en la página del Centro de desarrollo de Windows de su nueva aplicación, haga clic en **Servicios** > **Notificaciones de inserción**.

7. En la página **Notificaciones push**, haga clic en **Sitio de Servicios Live** en **Servicios de notificaciones de Windows (WNS) y Servicios móviles de Microsoft Azure**.

A continuación, configurará la autenticación de la cuenta Microsoft para la aplicación de Windows.


## <a name="register-microsoft-account"> </a>Registrar la aplicación con la cuenta Microsoft

Si ya registró su aplicación de Windows en la sección anterior, puede omitir el paso 4.

1. Inicie sesión en el [Portal de Azure] y vaya a la aplicación. Copie la **Dirección URL**. La usará para configurar la aplicación con la cuenta Microsoft.

2. Vaya a la página [Mis aplicaciones] del Centro para desarrolladores de la cuenta Microsoft e inicie sesión con su cuenta Microsoft, si procede.

3. Haga clic en **Crear aplicación** y, a continuación, escriba el **nombre de la aplicación** y haga clic en **Acepto**.

4. Haga clic en **Configuración de API**, seleccione **Sí** para **Aplicación cliente móvil o de escritorio**, proporcione la **URL de redireccionamiento** para la aplicación y haga clic en **Guardar**.
 
	![][0]

	>[AZURE.NOTE]El URI de redireccionamiento es la dirección URL de la aplicación anexada a la ruta de acceso _/.auth/login/microsoftaccount/callback_. Por ejemplo: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Asegúrese de que está utilizando el esquema HTTPS.

6. Haga clic en **Configuración de aplicaciones** y tome nota de los valores de **Id. de cliente** y **Secreto de cliente**.

    > [AZURE.IMPORTANT] El secreto de cliente es una credencial de seguridad importante, No comparta este secreto del cliente con nadie ni lo distribuya en una aplicación cliente.

## <a name="secrets"> </a>Adición de información de la cuenta Microsoft a la aplicación Servicio de aplicaciones

1. En el [Portal de Azure], vaya a la aplicación, haga clic en **Configuración** > **Autenticación/autorización**.

2. Si esta característica no está habilitada, **actívela**.

3. Haga clic en **Cuenta Microsoft**. Pegue los valores de identificador de la aplicación y de secreto de la aplicación que obtuvo previamente y habilite opcionalmente los ámbitos que requiere la aplicación. y, a continuación, haga clic en **Aceptar**.

    ![][1]

	De forma predeterminada, el Servicio de aplicaciones ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.

4. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante la cuenta Microsoft, establezca **Acción por realizar cuando no se autentique la solicitud** en **Cuenta Microsoft**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a la cuenta Microsoft para la autenticación.

5. Haga clic en **Guardar**.

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

<!---HONumber=AcomDC_0302_2016-->