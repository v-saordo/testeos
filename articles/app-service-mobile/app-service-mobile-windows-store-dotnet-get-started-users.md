<properties
	pageTitle="Incorporación de autenticación a su aplicación universal de Windows Runtime 8.1 | Aplicaciones móviles de Azure"
	description="Obtenga información acerca de cómo usar las Aplicaciones móviles del Servicio de aplicaciones de Azure para autenticar a los usuarios de su aplicación de Windows en una variedad de proveedores de identidades, incluidos Google, Facebook, Twitter y Microsoft."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="mattchenderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="glenga"/>

# Incorporación de la autenticación a la aplicación de Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

En este tema se muestra cómo agregar la autenticación basada en la nube a su aplicación móvil. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido de Aplicaciones móviles mediante un proveedor de identidades compatible con el Servicio de aplicaciones de Azure. Una vez que el back-end de la aplicación móvil haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

Este tutorial se basa en el inicio rápido de aplicaciones móviles. Primero debe completar el tutorial [Introducción a las aplicaciones móviles](app-service-mobile-windows-store-dotnet-get-started.md).

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración del Servicio de aplicaciones

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Con uno de los proyectos de aplicación de Windows configurado como proyecto de inicio, presione la tecla F5 para ejecutar la aplicación y compruebe que, cuando esta se inicia, se genera una excepción no controlada con el código de estado 401 (No autorizado). Esto se produce porque la aplicación intenta obtener acceso a su Código de aplicación móvil como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del Servicio de aplicaciones.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]


##<a name="tokens"></a>Almacenamiento del token de autorización en el cliente

En el ejemplo anterior se mostró un inicio de sesión estándar, que requiere que el cliente se ponga en contacto con el proveedor de identidades y con el servicio de la aplicación cada vez que se inicia la aplicación. Este método no solo es ineficaz, sino que también puede enfrentarse a problemas relacionados con el uso si varios clientes inician la aplicación al mismo tiempo. Un método mejor es almacenar en caché el token de autorización devuelto por su servicio de aplicación e intentar usarlo primero antes de utilizar un inicio de sesión basado en proveedores.

>[AZURE.NOTE]Puede almacenar en caché el token emitido por los servicios de aplicaciones con independencia de si es una autenticación administrada por el cliente o por el servicio. Este tutorial utiliza la autenticación administrada por el servicio.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##Pasos siguientes

Ahora que ha completado este tutorial de autenticación básica, considere la posibilidad de continuar con uno de los siguientes tutoriales:

+ [Agregar notificaciones push a su aplicación Windows](app-service-mobile-windows-store-dotnet-get-started-push.md) Obtenga información sobre cómo agregar compatibilidad a las notificaciones push a la aplicación y cómo configurar su back-end de aplicación móvil para usar centros de notificaciones de Azure para enviar notificaciones push.

+ [Habilitar la sincronización sin conexión para su aplicación Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Obtenga información sobre cómo agregar compatibilidad sin conexión a su aplicación con un back-end de aplicación móvil. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil (ver, agregar o modificar datos), incluso cuando no hay ninguna conexión de red.


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md

<!---HONumber=AcomDC_0211_2016-->