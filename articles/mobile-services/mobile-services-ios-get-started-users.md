<properties
	pageTitle="Incorporación de autenticación a una aplicación de Servicios móviles de Azure existente (iOS) | Back-end de JavaScript | Microsoft Azure"
	description="Obtenga información acerca de cómo utilizar Servicios móviles para autenticar usuarios de su aplicación iOS a través de una variedad de proveedores de identidad, incluidos Google, Facebook, Twitter y Microsoft."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="krisragh"/>

# Incorporación de Servicios móviles a una aplicación existente

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

En este tutorial podrá agregar la autenticación al [tutorial de inicio rápido de Servicios móviles] mediante un proveedor de identidades admitido.

Se recomienda completar el [Tutorial de inicio rápido de Servicios móviles] primero. Como alternativa, descargue simplemente el proyecto de iOS de inicio rápido del [Portal de Azure clásico], haga clic en **Servicios móviles** > su servicio móvil > signo de la nube en la parte superior izquierda > **iOS** > **Crear una nueva aplicación iOS** > **Descargar y ejecutar la aplicación** > **Objective-C** > **Descargar**. Recuerde hacer clic en **Crear tabla TodoItem** antes de hacer clic en **Descargar** si aún no ha creado la tabla.

##<a name="register"></a>Registro de la aplicación para la autenticación

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Almacenamiento de tokens de autenticación en la aplicación

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Pasos siguientes

A continuación, sepa [cómo utilizar el valor de identificador de usuario para filtrar los datos devueltos](mobile-services-javascript-backend-service-side-authorization.md).

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps
[Storing authentication tokens in your app]: #store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Service-side authorization of Mobile Services users]: mobile-services-javascript-backend-service-side-authorization.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[tutorial de inicio rápido de Servicios móviles]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios

[Portal de Azure clásico]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0114_2016-->