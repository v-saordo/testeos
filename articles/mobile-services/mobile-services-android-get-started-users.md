<properties
	pageTitle="Introducción a la autenticación en Android (back-end JavaScript) | Microsoft Azure"
	description="Obtenga información acerca de cómo usar Servicios móviles para autenticar a los usuarios de su aplicación Android en una variedad de proveedores de identidad, incluidos Google, Facebook, Twitter y Microsoft (back-end JavaScript)."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="ricksal"/>

# Incorporación de autenticación a la aplicación Android de Servicios móviles (back-end JavaScript)

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

## Resumen

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

> [AZURE.VIDEO android-getting-started-with-authentication-in-windows-azure-mobile-services]

Este tutorial le guiará a través de los pasos básicos para habilitar la autenticación en su aplicación.


##Requisitos previos

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## Registro de la aplicación para la autenticación y configuración de Servicios móviles

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

## Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

1. En Android Studio, abra el proyecto que ha creado al completar el tutorial [Introducción a Servicios móviles].

2. En el menú **Run** (Ejecutar), haga clic en **Run app** (Ejecutar aplicación). A continuación, compruebe que se lleva a cabo una excepción no controlada con el código de estado 401 (No autorizado) después de que se inicie la aplicación.

	 Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla _TodoItem_ requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

## Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Almacenamiento en caché de tokens de autenticación en el cliente

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="refresh-tokens"></a>Actualización de la memoria caché de los tokens

[AZURE.INCLUDE [mobile-android-authenticate-app-refresh-token](../../includes/mobile-android-authenticate-app-refresh-token.md)]



## <a name="next-steps"></a>Pasos siguientes

En el siguiente tutorial, [Autorización de usuarios con scripts], tomará el valor de identificador de usuario proporcionado por Servicios móviles en función de un usuario autenticado y lo usará para filtrar los datos que devuelve Servicios móviles.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]: #next-steps

<!-- Images. -->




[4]: ./media/mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-android-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->

[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a Servicios móviles]: mobile-services-android-get-started.md
[Autorización de usuarios con scripts]: mobile-services-javascript-backend-service-side-authorization.md

<!---HONumber=AcomDC_0121_2016-->