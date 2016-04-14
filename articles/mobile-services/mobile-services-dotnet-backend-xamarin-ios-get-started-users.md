<properties
	pageTitle="Introducción a la autenticación en Servicios móviles para aplicaciones de Xamarin iOS | Microsoft Azure"
	description="Obtenga información acerca de cómo utilizar Servicios móviles para autenticar usuarios de su aplicación Xamarin iOS a través de una variedad de proveedores de identidad, incluidos Google, Facebook, Twitter y Microsoft."
	services="mobile-services"
	documentationCenter="xamarin"
	authors="lindydonna"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/25/2015" 
	ms.author="donnam"/>

# Incorporación de autenticación a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

>[AZURE.NOTE]Se trata de un tema de Servicios móviles de Azure. Microsoft Azure recomienda usar Aplicaciones móviles del Servicio de aplicaciones de Azure para todas las nuevas implementaciones de back-end móvil. Para más información, consulte el [tutorial equivalente en la documentación de Aplicaciones móviles](../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md).

En este tema se muestra cómo autenticar usuarios en Servicios móviles desde su aplicación. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1. [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2. [Restricción de los permisos de tabla para los usuarios autenticados]
3. [Incorporación de autenticación a la aplicación]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles].

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;&nbsp;6. En Visual Studio o Xamarin Studio, ejecute el proyecto de cliente en un dispositivo o simulador. Compruebe que se produce una excepción no controlada con el código de estado 401 (No autorizado) después de iniciarse la aplicación.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Esto se produce porque la aplicación intenta acceder a Servicios móviles como usuario sin autenticar, pero la tabla *TodoItem* requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

En esta sección, modificará la aplicación para mostrar una pantalla de inicio de sesión antes de mostrar los datos. Cuando se inicie la aplicación, no se conectará a su servicio móvil y no mostrará datos. Después de que el usuario intente actualizar una vez, aparecerá la pantalla de inicio y, una vez que haya iniciado sesión, se mostrará la lista de tareas pendientes.

1. En el proyecto de cliente, abra el archivo **QSTodoService.cs** y agregue las siguientes declaraciones a QSTodoService:

		// Mobile Service logged in user
		private MobileServiceUser user;
		public MobileServiceUser User { get { return user; } }

2. Agregue un método **Authenticate** a **QSTodoService** con la siguiente definición:

        private async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

	> [AZURE.NOTE] Cuando usa un proveedor de identidades que no sea una cuenta de Facebook, cambie el valor que pasó anteriormente a **LoginAsync** por uno de los siguientes: _MicrosoftAccount_, _Twitter_, _Google_ o _WindowsAzureActiveDirectory_.

3. Abra **QSTodoListViewController.cs** y modifique la definición del método **ViewDidLoad** para quitar o convertir en comentario la llamada a **RefreshAsync()** cerca del final.

4. Agregue el siguiente código al principio de la definición del método **RefreshAsync**:

		// Add at the start of the RefreshAsync method.
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("You must sign in.");
				return;
			}
		}
		
	Se muestra una pantalla de inicio de sesión para intentar la autenticación cuando la propiedad **User** es null. Cuando el inicio de sesión se ha realizado correctamente, se establece **User**.

5. Presione el botón **Ejecutar** para compilar el proyecto e iniciar la aplicación en el simulador de iPhone. Compruebe que la aplicación no muestra ningún dato. **RefreshAsync()** no se ha llamado.

6. Realice el gesto de actualización desplegando la lista de elementos, que llama a **RefreshAsync()**. Se llama a **Authenticate()** para iniciar la pantalla de inicio de sesión y de autenticación que se muestra. Después de haberse autenticado correctamente, la aplicación mostrará la lista de tareas pendientes y podrá realizar actualizaciones en los datos.

## <a name="next-steps"> </a>Pasos siguientes

En el tutorial siguiente, [Autorización en el servicio de usuarios de Servicios móviles][Authorize users with scripts], usará el valor del identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado y lo usará para filtrar los datos devueltos por Servicios móviles.


<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla para los usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introducción a los Servicios móviles]: mobile-services-dotnet-backend-xamarin-ios-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-xamarin-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-xamarin-ios-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts.md
[JavaScript and HTML]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

<!---HONumber=AcomDC_0128_2016-->