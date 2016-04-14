<properties
	pageTitle="Registro para la autenticación de Twitter | Microsoft Azure"
	description="Obtenga información acerca de cómo usar la autenticación de Twitter con su aplicación de Servicios móviles de Azure."
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
	ms.date="02/07/2016" 
	ms.author="glenga"/>

#Registro de aplicaciones para el inicio de sesión en Twitter con Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

En este tema se muestra cómo registrar las aplicaciones a fin de poder usar Twitter para la autenticación con Servicios móviles de Azure.

>[AZURE.NOTE] En este tutorial se describen los [Servicios móviles de Azure](https://azure.microsoft.com/services/mobile-services/), una solución que le ayuda a crear aplicaciones móviles escalables para cualquier plataforma. La solución Servicios móviles facilita la sincronización de datos, la autenticación de usuarios y el envío de notificaciones de inserción. En esta página se incluye el tutorial [Agregar autenticación a la aplicación](mobile-services-ios-get-started-users.md) en el que se muestra cómo registrar usuarios en la aplicación. Si esta es la primera vez que usa Servicios móviles, complete el tutorial [Introducción a los Servicios móviles](mobile-services-ios-get-started.md).

Para llevar a cabo el procedimiento descrito en este tema, debe tener una cuenta de Twitter con una dirección de correo electrónico verificada. Para crear una cuenta de Twitter, vaya a <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

1. Vaya al sitio web para [desarrolladores de Twitter](http://go.microsoft.com/fwlink/p/?LinkId=268300), inicie sesión con las credenciales de la cuenta de Twitter y haga clic en **Crear nueva aplicación**.

2. Escriba los valores **Nombre**, **Descripción** y **Sitio web** para su aplicación, y luego escriba uno de los siguientes formatos de direcciones URL en **URL de devolución de llamada**.

	+ **Back-end de .NET**: `https://<mobile_service>.azure-mobile.net/signin-twitter`
	+ **Back-end de JavaScript**: `https://<mobile_service>.azure-mobile.net/login/twitter`

	 >[AZURE.NOTE] Asegúrese de usar el formato correcto de ruta de acceso a dirección URL de redireccionamiento para su tipo de back-end de Servicios móviles. Si es incorrecto, la autenticación no se realizará correctamente. &nbsp;

   	![][2]

3.  En la parte inferior de la página, lea y acepte los términos y, luego, haga clic en **Crear su aplicación de Twitter**.

   	De esta forma, la aplicación se registra y se muestran los detalles correspondientes.

6. Haga clic en la pestaña **Claves y tokens de acceso** en el panel de la aplicación y anote los valores de **Clave del consumidor** y **Secreto del consumidor**.

    > [AZURE.NOTE] El secreto de consumidor es una credencial de seguridad importante, por lo que no debe compartirlo con nadie ni distribuirlo con su aplicación.

7. Haga clic en la pestaña **Configuración**, desplácese hacia abajo y asegúrese de que la casilla **Permitir que se use esta aplicación para iniciar sesión en Twitter** esté activada y haga clic en **Actualizar configuración**.

De este modo ya estará listo para usar un inicio de sesión de Twitter para autenticarse en su aplicación proporcionando los valores de clave de usuario y secreto de usuario a Servicios móviles.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet/

<!---HONumber=AcomDC_0211_2016-->