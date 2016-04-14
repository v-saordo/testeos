<properties
	pageTitle="Agregar autenticación en Apache Cordova con Aplicaciones móviles | Servicio de aplicaciones de Azure"
	description="Obtenga información sobre cómo usar Aplicaciones móviles en el Servicio de aplicaciones de Azure para autenticar usuarios de su aplicación de Apache Cordova a través de una variedad de proveedores de identidades, incluidos Google, Facebook, Twitter y Microsoft."
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="ggailey777"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="adrianha"/>

# Agregar autenticación a su aplicación de Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## Resumen

En este tutorial podrá agregar la autenticación al proyecto de inicio rápido todolist en Apache Cordova con un proveedor de identidades admitido. Este tutorial está basado en el tutorial [Introducción a Aplicaciones móviles], que debe completar primero.

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración del Servicio de aplicaciones

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ En Visual Studio, abra el proyecto que creó cuando completó el tutorial [Get started with Mobile Apps] Introducción a las aplicaciones móviles, ejecute la aplicación en el **Emulador de Android de Google** y compruebe que se muestra un error de conexión inesperado después de que se inicie la aplicación.

    Esto sucede porque la aplicación intenta obtener acceso al back-end como usuario sin autenticar. El back-end redirige al usuario a una página de autenticación mediante OAuth. Sin embargo, la aplicación no confía en el punto de conexión de OAuth.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del back-end de aplicación móvil.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

1. Abra el proyecto en **Visual Studio** y, a continuación, abra el archivo `www/index.html` para editarlo.

2. Busque la etiqueta META `Content-Security-Policy` en la sección de encabezado. Debe agregar el host de OAuth a la lista de orígenes permitidos.

    | Proveedor | Nombre del proveedor del SDK | Host de OAuth |
    | :--------------------- | :---------------- | :-------------------------- |
    | Azure Active Directory | aad | https://login.windows.net |
    | Facebook | facebook | https://www.facebook.com |
    | Google | google | https://accounts.google.com |
    | Microsoft | microsoftaccount | https://login.live.com |
    | Twitter | twitter | https://api.twitter.com |

    A continuación se muestra un ejemplo de Content-Security-Policy (implementado para Azure Active Directory):

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' 
			data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    Debe reemplazar `https://login.windows.net` con el host de OAuth de la tabla anterior. Consulte la [documentación de Content-Security-Policy] para obtener más información sobre esta etiqueta META.

    Tenga en cuenta que algunos proveedores de autenticación no requieren cambios en Content-Security-Policy cuando se usa en dispositivos móviles adecuados. Por ejemplo, no se requiere ningún cambio en Content-Security-Policy cuando se usa la autenticación de Google en un dispositivo Android.

3. Abra el archivo `www/js/index.js` para editarlo, busque el método `onDeviceReady()` y, en el código de creación del cliente, agregue lo siguiente:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Tenga en cuenta que este código reemplaza el código existente que crea la referencia de tabla y actualiza la interfaz de usuario.

    El método login() inicia la autenticación con el proveedor. El método login() es una función asincrónica que devuelve una promesa de JavaScript. El resto de la inicialización se coloca dentro de la respuesta de la promesa para que no se ejecute hasta que se complete el método login().

4. En el código que acaba de agregar, reemplace `SDK_Provider_Name` por el nombre de su proveedor de inicio de sesión. Por ejemplo, para Azure Active Directory, use `client.login('aad')`.

4. Ejecute el proyecto. Cuando el proyecto acabe de inicializarse, la aplicación mostrará la página de inicio de sesión de OAuth del proveedor de autenticación seleccionado.

##<a name="next-steps"></a>Pasos siguientes

* Obtenga más información [sobre la autenticación] con el Servicio de aplicaciones de Azure.
* Prosiga el tutorial agregando [notificaciones push] a la aplicación de Apache Cordova.

<!-- URLs. -->
[Get started with Mobile Apps]: app-service-mobile-cordova-get-started.md
[Introducción a Aplicaciones móviles]: app-service-mobile-cordova-get-started.md
[documentación de Content-Security-Policy]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[notificaciones push]: app-service-mobile-cordova-get-started-push.md
[sobre la autenticación]: app-service-mobile-auth.md

<!---HONumber=AcomDC_0302_2016-->