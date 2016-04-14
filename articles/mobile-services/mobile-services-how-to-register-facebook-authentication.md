<properties
	pageTitle="Registro para autenticación de Facebook | Servicios móviles de Azure"
	description="Aprenda a usar la autenticación de Facebook en la aplicación de Servicios móviles de Azure."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/28/2016"
	ms.author="glenga"/>

# Registro de las aplicaciones para la autenticación de Facebook con Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]&nbsp;


[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

En este tema se muestra cómo registrar las aplicaciones a fin de poder usar Facebook para la autenticación con Servicios móviles de Azure. En esta página se incluye el tutorial [Introducción a la autenticación](mobile-services-ios-get-started-users.md) en el que se muestra cómo registrar usuarios en la aplicación. Si esta es la primera vez que usa Servicios móviles, complete el tutorial [Introducción a los Servicios móviles](mobile-services-ios-get-started.md).

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Facebook asociada a una dirección de correo electrónico verificada y a un número de teléfono móvil. Para crear una cuenta de Facebook, vaya a [facebook.com](http://go.microsoft.com/fwlink/p/?LinkId=268285).

1. Desplácese hasta el sitio web para [desarrolladores de Facebook](http://go.microsoft.com/fwlink/p/?LinkId=268285) e inicie sesión con las credenciales de su cuenta de Facebook.

2. (Opcional) Si aún no está registrado, haga clic en **Mis aplicaciones**, luego en **Registrarse como desarrollador**, acepte la política y siga los pasos de registro.

3. Haga clic en **Mis aplicaciones** > **Agregar una nueva aplicación** > **Sitio web** > **Omitir y crear id. de aplicación**.

4. En **Nombre para mostrar**, escriba un nombre único para la aplicación, elija una **Categoría** para la aplicación, haga clic en **Crear id. de aplicación** y complete la comprobación de seguridad. Esto le llevará al panel del desarrollador de la nueva aplicación de Facebook.

5. En el campo **Secreto de la aplicación**, haga clic en **Mostrar**, escriba la contraseña si se le solicita y, a continuación, anote los valores de **Id. de aplicación** y **Secreto de la aplicación**. Más adelante los usará para configurar la aplicación en Azure.

	> [AZURE.NOTE] **Nota de seguridad** El secreto de la aplicación es una credencial de seguridad importante, No comparta este secreto con nadie ni lo distribuya en una aplicación cliente.

5. En la barra de navegación izquierda, haga clic en **Configuración**, escriba el dominio de su servicio móvil en **App Domains**, escriba un **correo electrónico de contacto** opcional y, a continuación, haga clic en **Añadir plataforma** y seleccione **Página web**.

   	![][3]

6. Escriba la URL de su servicio móvil en **Site URL** (URL del sitio) y haga clic en **Save Changes** (Guardar cambios).

7. Haga clic en **Show** (Mostrar), proporcione la contraseña si se le solicita y anote los valores de **App ID** (Identificador de aplicación) y **App Secret** (Secreto de aplicación).

   	![][5] &nbsp;

    >[AZURE.IMPORTANT] El secreto de aplicación es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación. &nbsp;

8. Haga clic en la pestaña **Avanzadas**, escriba uno de los siguientes formatos de direcciones URL en **URI de redireccionamiento de OAuth válidos** y haga clic en **Guardar cambios**:

	+ **Back-end de .NET**: `https://<mobile_service>.azure-mobile.net/signin-facebook`
	+ **Back-end de JavaScript**: `https://<mobile_service>.azure-mobile.net/login/facebook`

	 >[AZURE.NOTE]Asegúrese de usar el formato correcto en la ruta de la dirección URL de redireccionamiento para su tipo de back-end de Servicios móviles, usando el esquema *HTTPS*. Si es incorrecto, la autenticación no se realizará correctamente.


12. La cuenta de Facebook que se utilizó para registrar la aplicación es un administrador de la aplicación. En este momento, solo los administradores pueden iniciar sesión en esta aplicación. Para autenticar otras cuentas de Facebook, haga clic en **Revisión de aplicaciones** y habilite **Hacer público todolist-complete-nodejs** para habilitar el acceso del público general mediante la autenticación de Facebook.

Ahora ya puede habilitar el uso del inicio de sesión de Facebook para autenticarse en la aplicación facilitando el identificador y el secreto de la aplicación a Servicios móviles.

<!-- Anchors. -->

<!-- Images. -->
[3]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-configure-app.png
[5]: ./media/mobile-services-how-to-register-facebook-authentication/mobile-services-facebook-completed.png

<!-- URLs. -->
[Facebook Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Mobile Services]: http://azure.microsoft.com/services/mobile-services/

<!---HONumber=AcomDC_0302_2016-->