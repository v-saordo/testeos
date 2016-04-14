<properties
	pageTitle="Incorporación de autenticación a la aplicación de Windows 8.1 universal | Microsoft Azure"
	description="Obtenga información acerca de cómo utilizar Servicios móviles para autenticar usuarios de la aplicación Universal Windows 8.1 a través de una variedad de proveedores de identidades, incluidos Google, Facebook, Twitter y Microsoft."
	services="mobile-services"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/23/2015"
	ms.author="glenga"/>

# Incorporación de autenticación a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

## Información general

Este tema muestra cómo autenticar usuarios en Servicios móviles de Azure desde una aplicación universal para Windows. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar también el tutorial [Introducción a Servicios móviles] o [Incorporación de Servicios móviles a una aplicación existente](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md).

>[AZURE.NOTE]Este tutorial muestra cómo utilizar autenticación dirigida al servidor para usuarios en aplicaciones de la Tienda Windows y la Tienda de Windows Phone 8.1. Para más información sobre la autenticación dirigida al cliente, vea [Inicio de sesión con los SDK de Google, Microsoft y Facebook en Servicios móviles de Azure](https://azure.microsoft.com/blog/2014/10/27/logging-in-with-google-microsoft-and-facebook-sdks-to-azure-mobile-services/).

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;6. En Visual Studio, haga clic con el botón secundario en el proyecto de la Tienda Windows para la aplicación TodoList y haga clic en **Establecer como proyecto de inicio**.

&nbsp;&nbsp;7. En el proyecto compartido, abra el archivo de proyecto App.xaml.cs, busque la definición de [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) y asegúrese de que se ha configurado para conectar con el servicio móvil que se ejecuta en Azure.

>[AZURE.NOTE]Cuando se conecta una aplicación a un servicio móvil con las herramientas de Visual Studio, la herramienta genera dos conjuntos de definiciones **MobileServiceClient**, una para cada plataforma cliente. Ahora es el momento de simplificar el código generado. Para ello, unifique las definiciones del contenedor `#if...#endif` **MobileServiceClient** en una única definición sin contenedor, que deben usar las dos versiones de la aplicación. No necesitará hacer esto cuando descargue la aplicación de inicio rápido desde el [Portal de Azure clásico].

&nbsp;&nbsp;8. Presione la tecla F5 para ejecutar la aplicación de la Tienda Windows y compruebe que, cuando esta se inicia, se genera una excepción no controlada con el código de estado 401 (No autorizado).

&nbsp;&nbsp;Esto se produce porque la aplicación intenta acceder a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]

>[AZURE.NOTE]Si ha registrado la información del paquete de la aplicación de la Tienda Windows con Servicios móviles, debe llamar al método <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> proporcionando un valor **true** para el parámetro *useSingleSignOn*. Si no realiza este procedimiento, los usuarios seguirán visualizando una solicitud de inicio de sesión cada vez que se llame al método de inicio de sesión.

##<a name="tokens"></a>Almacenamiento de tokens de autorización en el cliente

En el ejemplo anterior se mostró un inicio de sesión estándar, que requiere que el cliente se ponga en contacto con el proveedor de identidades y con el servicio móvil cada vez que se inicia la aplicación. Este método no solo es ineficaz, sino que también puede enfrentarse a problemas relacionados con el uso si varios clientes intentan iniciar la aplicación al mismo tiempo. Un método mejor es almacenar en caché el token de autorización devuelto por los servicios móviles e intentar usarlo primero antes de utilizar un inicio de sesión basado en proveedores.

>[AZURE.NOTE]Puede almacenar en caché el token emitido por los servicios móviles con independencia de si es una autenticación administrada por el cliente o por el servicio. Este tutorial utiliza la autenticación administrada por el servicio.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]


## <a name="next-steps"> </a>Pasos siguientes

En el tutorial siguiente, [Autorización en el servicio de usuarios de Servicios móviles][Authorize users with scripts], usará el valor del identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado y lo usará para filtrar los datos devueltos por Servicios móviles.

##Consulte también

+ [Característica de usuarios mejorada](https://azure.microsoft.com/blog/2014/10/02/custom-login-scopes-single-sign-on-new-asp-net-web-api-updates-to-the-azure-mobile-services-net-backend/)<br/> Puede obtener datos de usuario adicionales mantenidos por el proveedor de identidades del servicio móvil llamando al método **ServiceUser.GetIdentitiesAsync()** en un back-end de .NET.

+ [Referencia conceptual de Servicios móviles con .NET] <br/>Obtenga más información sobre cómo utilizar Servicios móviles con un cliente .NET.


<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a Servicios móviles]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Get started with authentication]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[Portal de Azure clásico]: https://manage.windowsazure.com/
[Referencia conceptual de Servicios móviles con .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=AcomDC_0128_2016-->