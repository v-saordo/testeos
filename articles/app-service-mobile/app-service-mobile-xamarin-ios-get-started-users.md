<properties
	pageTitle="Introducción a la autenticación de Aplicaciones móviles en Xamarin iOS"
	description="Obtenga información acerca de cómo utilizar Aplicaciones móviles para autenticar usuarios de su aplicación Xamarin iOS a través de una variedad de proveedores de identidad, incluidos AAD, Google, Facebook, Twitter y Microsoft."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="mattchenderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="mahender"/>

# Adición de la autenticación a la aplicación Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

En este tema se muestra cómo autenticar usuarios de una Aplicación móvil del Servicio de aplicaciones desde la aplicación cliente. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido Xamarin.iOS mediante un proveedor de identidades compatible con Servicio de aplicaciones. Una vez que la aplicación móvil haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario y podrá tener acceso a datos de tabla restringida.

Primero debe completar el tutorial [Creación de una aplicación Xamarin.iOS]. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de autenticación al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Registro de la aplicación para la autenticación y configuración de Servicios de aplicaciones

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. En Visual Studio o Xamarin Studio, ejecute el proyecto de cliente en un dispositivo o emulador. Compruebe que se produce una excepción no controlada con el código de estado 401 (No autorizado) después de iniciarse la aplicación. El error se registra en la consola del depurador. De esta forma, en Visual Studio, debería ver el error en la ventana de salida.

&nbsp;&nbsp;Este error no autorizado ocurre porque la aplicación intenta obtener acceso al back-end de la aplicación móvil como usuario autenticado. La tabla *TodoItem* ahora requiere autenticación.

Luego, actualizará la aplicación cliente para solicitar recursos del back-end de la aplicación móvil con un usuario autenticado.

##Incorporación de autenticación a la aplicación

En esta sección, modificará la aplicación para mostrar una pantalla de inicio de sesión antes de mostrar los datos. Cuando se inicie la aplicación, no se conectará a Servicios de aplicaciones y no mostrará datos. Después de que el usuario intente actualizar una vez, aparecerá la pantalla de inicio y, una vez que haya iniciado sesión, se mostrará la lista de tareas pendientes.

1. En el proyecto de cliente, abra el archivo **QSTodoService.cs** y agregue la siguiente instrucción Using y `MobileServiceUser`, con descriptor de acceso a la clase QSTodoService:

	```
		using UIKit;
	```

		// Logged in user
		private MobileServiceUser user;
		public MobileServiceUser User { get { return user; } }

2. Agregue un nuevo método denominado **Authenticate** a **QSTodoService** con la siguiente definición:


        public async Task Authenticate(UIViewController view)
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

	>[AZURE.NOTE] Si usa un proveedor de identidades que no sea una cuenta de Facebook, cambie el valor que pasó anteriormente a **LoginAsync** por uno de los siguientes: _MicrosoftAccount_, _Twitter_, _Google_ o _WindowsAzureActiveDirectory_.

3. Abra **QSTodoListViewController.cs**. Modifique la definición del método de **ViewDidLoad** para quitar la llamada a **RefreshAsync()** cerca del final:

		public override async void ViewDidLoad ()
		{
			base.ViewDidLoad ();

			todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

			// Comment out the call to RefreshAsync
			// await RefreshAsync ();
		}


4. Modifique el método **RefreshAsync** para autenticar si la propiedad **User** es null. Agregue el siguiente código al principio de la definición del método:

		// start of RefreshAsync method
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("couldn't login!!");
				return;
			}
		}
		// rest of RefreshAsync method

5. En Visual Studio o Xamarin Studio conectado a su host de compilación de Xamarin en el equipo Mac, ejecute el proyecto de cliente destinado a un dispositivo o un emulador. Compruebe que la aplicación no muestra ningún dato.

	Despliegue la lista de tareas para actualizar la pantalla, lo que dará lugar a que aparezca la pantalla de inicio de sesión. Una vez que haya proporcionado credenciales válidas, la aplicación mostrará la lista de tareas pendientes y podrá actualizar los datos.


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Creación de una aplicación Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md

<!---HONumber=AcomDC_0211_2016-->