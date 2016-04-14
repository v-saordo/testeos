<properties
	pageTitle="Agregar autenticación en Android con Aplicaciones móviles| Servicio de aplicaciones de Azure"
	description="Obtenga información sobre cómo usar Aplicaciones móviles en el Servicio de aplicaciones de Azure para autenticar usuarios de su aplicación Android a través de una variedad de proveedores de identidad, incluidos Google, Facebook, Twitter y Microsoft."
	services="app-service\mobile"
	documentationCenter="android"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="glenga"/>

# Agregar autenticación a su aplicación de Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## Resumen

En este tutorial podrá agregar la autenticación al proyecto de inicio rápido todolist en Android con un proveedor de identidades admitido. Este tutorial está basado en el tutorial [Introducción a Aplicaciones móviles], que debe completar primero.

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración del Servicio de aplicaciones

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ En Android Studio, abra el proyecto que creó al completar el tutorial [Introducción a aplicaciones móviles], luego, en el menú **Ejecutar**, haga clic en **Ejecutar aplicación** y compruebe que se produce una excepción no controlada con el código de estado 401 (No autorizado) después de iniciarse la aplicación.

	 Esto se produce porque la aplicación intenta obtener acceso al back-end como usuario sin autenticar, pero la tabla _TodoItem_ requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del back-end de aplicación móvil.

## Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Almacenamiento en caché de tokens de autenticación en el cliente

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##Pasos siguientes

Ahora que ha completado este tutorial de autenticación básica, considere la posibilidad de continuar con uno de los siguientes tutoriales:

+ [Agregar notificaciones push a su aplicación Android](app-service-mobile-android-get-started-push.md) Obtenga información sobre cómo agregar compatibilidad a las notificaciones push a la aplicación y cómo configurar su back-end de aplicación móvil para usar centros de notificaciones de Azure para enviar notificaciones push.

+ [Habilitar la sincronización sin conexión para su aplicación Android](app-service-mobile-android-get-started-offline-data.md) Obtenga información sobre cómo agregar compatibilidad sin conexión a su aplicación con un back-end de aplicación móvil. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos), incluso cuando no hay ninguna conexión de red.



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Introducción a Aplicaciones móviles]: app-service-mobile-android-get-started.md

<!---HONumber=AcomDC_0211_2016-->