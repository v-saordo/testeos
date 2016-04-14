<properties
	pageTitle="Incorporación de autenticación a una aplicación de Servicios móviles de Azure existente (iOS) | Back-end de .NET | Microsoft Azure"
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

# Incorporación de autenticación a una aplicación de Servicios móviles de Azure existente

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades admitido. Este tutorial está basado en el [Tutorial de introducción a Servicios móviles], que debe completar primero.

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

En Xcode, abra el proyecto. Presione el botón **Ejecutar** para iniciar la aplicación. Compruebe que se produce una excepción con el código de estado 401 (No autorizado) después de iniciarse la aplicación. Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla _TodoItem_ requiere ahora autenticación.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Almacenamiento de tokens de autenticación en la aplicación

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Pasos siguientes

En el siguiente tutorial, [Autorización en el servicio de usuarios de Servicios móviles], podrá usar el valor de identificador de usuario para filtrar los datos devueltos.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps
[Storing authentication tokens in your app]: #store-authentication

<!-- URLs. -->
[Autorización en el servicio de usuarios de Servicios móviles]: mobile-services-dotnet-backend-service-side-authorization.md
[Tutorial de introducción a Servicios móviles]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md
[Authorize users with scripts]: mobile-services-dotnet-backend-service-side-authorization.md
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=AcomDC_0114_2016-->