<properties
	pageTitle="Introducción a la autenticación (Xamarin.iOS) - Servicios móviles"
	description="Obtenga información acerca de cómo usar la autenticación en su aplicación de Servicios móviles de Azure para Xamarin.iOS."
	documentationCenter="xamarin"
	services="mobile-services"
	manager="dwrede"
	authors="lindydonna"
	editor=""/>


<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="donnam"/>

# Incorporación de autenticación a la aplicación de Servicios móviles

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

En este tema se muestra cómo autenticar usuarios en Servicios móviles de Azure desde su aplicación. En este tutorial podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidades compatible con Servicios móviles. Una vez que Servicios móviles haya realizado la autenticación y autorización correctamente, se mostrará el valor de identificador de usuario.

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1. [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2. [Restricción de los permisos de tabla para los usuarios autenticados]
3. [Incorporación de autenticación a la aplicación]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a los Servicios móviles].

Para completar este tutorial necesita [Xamarin.iOS], XCode 6.0 e iOS 7.0 o versiones posteriores.

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


3. En Xcode, abra el proyecto que creó al completar el tutorial [Introducción a los Servicios móviles].

4. Presione el botón **Run** (Ejecutar) para crear el proyecto e iniciar la aplicación en el emulador de iPhone; compruebe que se produce una excepción no controlada con el código de estado 401 (No autorizado) después de iniciarse la aplicación.

   	Esto sucede porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla _TodoItem_ requiere ahora autenticación.

A continuación, actualizará la aplicación para autenticar usuarios antes de solicitar recursos del servicio móvil.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

1. Abra el archivo de proyecto **QSToDoService** y agregue las siguientes variables.

		// Mobile Service logged in user
		private MobileServiceUser user;
		public MobileServiceUser User { get { return user; } }

2. A continuación, agregue un método nuevo con nombre **Authenticate** a **ToDoService** definido como:

        private async Task Authenticate(MonoTouch.UIKit.UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.MicrosoftAccount);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

	> [AZURE.NOTE] Si usa un proveedor de identidades que no sea una cuenta Microsoft, cambie el valor que pasó anteriormente a **LoginAsync** por uno de los siguientes: _Facebook_, _Twitter_, _Google_ o _WindowsAzureActiveDirectory_.

3. Mueva la solicitud para la tabla **ToDoItem** desde el constructor **ToDoService** a un nuevo método llamado **CreateTable**:

        private async Task CreateTable()
        {
            // Create an MSTable instance to allow us to work with the ToDoItem table
            todoTable = client.GetSyncTable<ToDoItem>();
        }

4. Cree un nuevo método público asincrónico llamado **LoginAndGetData** definido como:

        public async Task LoginAndGetData(MonoTouch.UIKit.UIViewController view)
        {
            await Authenticate(view);
            await CreateTable();
        }

5. En **TodoListViewController**, reemplace el método **ViewDidAppear** y defínalo tal como se observa a continuación. Se registra en el usuario si **ToDoService** todavía no tiene control sobre el usuario:

        public override async void ViewDidAppear(bool animated)
        {
            base.ViewDidAppear(animated);

            if (QSTodoService.DefaultService.User == null)
            {
                await QSTodoService.DefaultService.LoginAndGetData(this);
            }

            if (QSTodoService.DefaultService.User == null)
            {
                // TODO:: show error
                return;
            }


            await RefreshAsync();
        }
6. Quite la llamada original a **RefreshAsync** de **TodoListViewController.ViewDidLoad**.

7. Presione el botón **Run** (Ejecutar) para compilar el proyecto, inicie la aplicación en el emulador de iPhone e inicie sesión con el proveedor de identidades de su elección.

   	Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

## Obtener un ejemplo completado
Descargue el [proyecto de ejemplo completado]. Asegúrese de actualizar las variables **applicationURL** y **applicationKey** con su propia configuración de Azure.

## <a name="next-steps"></a>Pasos siguientes

En el siguiente tutorial, [Autorización de usuarios con scripts], tomará el valor de identificador de usuario proporcionado por Servicios móviles en función de un usuario autenticado y lo usará para filtrar los datos que devuelve Servicios móviles.

<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla para los usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-service-uri.png
[13]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. TODO:: update completed example project link with project download -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Introducción a los Servicios móviles]: /develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Get started with push notifications]: /develop/mobile/tutorials/-get-started-with-push-xamarin-ios
[Autorización de usuarios con scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[proyecto de ejemplo completado]: http://go.microsoft.com/fwlink/p/?LinkId=331328
[Xamarin.iOS]: http://xamarin.com/download

<!---HONumber=AcomDC_0218_2016-->