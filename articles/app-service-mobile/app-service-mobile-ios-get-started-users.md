<properties
	pageTitle="Incorporación de autenticación en iOS con Aplicaciones móviles de Azure"
	description="Obtenga información acerca de cómo usar las Aplicaciones móviles de Azure para autenticar a los usuarios de su aplicación iOS en una variedad de proveedores de identidades, incluidos AAD, Google, Facebook, Twitter y Microsoft."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/28/2015"
	ms.author="krisragh"/>

# Incorporación de la autenticación a la aplicación iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

En este tutorial podrá agregar la autenticación al proyecto de [inicio rápido de iOS] mediante un proveedor de identidades compatible. Este tutorial está basado en el tutorial de [inicio rápido de iOS], que debe completar primero.

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración del Servicio de aplicaciones

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

En Xcode, presione **Ejecutar** para iniciar la aplicación. Se genera una excepción porque la aplicación intenta obtener acceso al back-end como usuario sin autenticar, pero la tabla _TodoItem_ ahora requiere autenticación.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[inicio rápido de iOS]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com
 

<!---HONumber=AcomDC_0107_2016--->