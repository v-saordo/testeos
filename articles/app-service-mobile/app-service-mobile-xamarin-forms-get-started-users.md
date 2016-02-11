<properties 
	pageTitle="Introducción a la autenticación de Aplicaciones móviles en la aplicación de Xamarin.Forms" 
	description="Obtenga información acerca de cómo utilizar Aplicaciones móviles para autenticar usuarios de la aplicación de Xamarin Forms a través de una variedad de proveedores de identidad, incluidos AAD, Google, Facebook, Twitter y Microsoft." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="12/07/2015" 
	ms.author="wesmc"/>

# Agregar autenticación a la aplicación de Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Información general

En este tema se muestra cómo autenticar usuarios de una Aplicación móvil del Servicio de aplicaciones desde la aplicación cliente. En este tutorial podrá agregar autenticación al proyecto de inicio rápido de Xamarin.Forms mediante un proveedor de identidades compatible con Servicio de aplicaciones. Cuando la aplicación móvil haya realizado la autenticación y la autorización correctamente, se mostrará el valor de identificador de usuario y podrá acceder a datos de tablas restringidas.

Primero debe completar el tutorial de inicio rápido [Creación de una aplicación Xamarin.Forms](app-service-mobile-xamarin-forms-get-started.md). Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar el paquete de extensión de autenticación al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).


##Registro de la aplicación para la autenticación y configuración de Servicios de aplicaciones

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##Restricción de los permisos para los usuarios autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##Agregar autenticación a la biblioteca de clases portables 

Las aplicaciones móviles utilizan un método `MobileServiceClient.LoginAsync` específico de la plataforma para mostrar la interfaz de inicio de sesión y los datos de la memoria caché. Para autenticarse con un proyecto de Xamarin Forms, definirá una interfaz `IAuthenticate` en la biblioteca de clases portables. Cada plataforma que quiera admitir implementará esta interfaz en el proyecto específico de la plataforma.

También actualizará la interfaz de usuario definida en la biblioteca de clases portable, agregar un botón de inicio de sesión. El usuario deberá hacer clic en este botón para autenticar después de que se inicie la aplicación.

1. En Visual Studio o Xamarin Studio, abra el archivo App.cs del proyecto **portable**. Agregue la siguiente instrucción `using` al archivo:

		using System.Threading.Tasks;

2. En el archivo App.cs, agregue la siguiente definición de interfaz `IAuthenticate` inmediatamente antes de la definición de la clase `App`.

	    public interface IAuthenticate
	    {
	        Task<bool> Authenticate();
	    };

3. En el archivo App.cs, agregue los siguientes miembros estáticos para inicializar la interfaz con una implementación específica de la plataforma.

		public class App : Application
		{
	
	        public static IAuthenticate Authenticator { get; private set; }
	
	        public static void Init(IAuthenticate authenticator)
	        {
	            Authenticator = authenticator;
	        }
	
			...


4. Abra TodoList.xaml.cs desde el proyecto **portable**. Agregue la siguiente marca a la clase `TodoList` para indicar si se ha autenticado o no el usuario.

        bool authenticated = false;


5. En TodoList.xaml.cs actualice el método `OnAppearing` para actualizar únicamente los elementos si se ha autenticado el usuario.


        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }

6. En TodoList.xaml.cs, en la parte superior del constructor para la clase `TodoList`, defina el siguiente botón de inicio de sesión y haga clic en controlador...

        public TodoList()
        {
            InitializeComponent();

            manager = TodoItemManager.DefaultManager;

            var loginButton = new Button
            {
                Text = "Login",
                TextColor = Xamarin.Forms.Color.Black,
                BackgroundColor = Xamarin.Forms.Color.Lime,
            };
            loginButton.Clicked += loginButton_Clicked;

            Xamarin.Forms.StackLayout bp = buttonsPanel as StackLayout;
            Xamarin.Forms.StackLayout bpParentStack = bp.Parent.Parent as StackLayout;

            bpParentStack.Padding = new Xamarin.Forms.Thickness(10, 30, 10, 20);
            bp.Orientation = StackOrientation.Vertical;
            bp.Children.Add(loginButton);

			...

7. En TodoList.xaml.cs, agregue el siguiente controlador para el evento Click del botón de inicio de sesión.

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }


8. Guarde los cambios, compile el proyecto de biblioteca de clases portable y compruebe que no hay errores.


##Agregar autenticación a la aplicación de Android

En esta sección, agregará autenticación para el proyecto droid. Puede omitir esta sección si no está trabajando con dispositivos Android.

1. En Visual Studio o Xamarin Studio, haga clic con el botón secundario en el proyecto **droid** y haga clic en **Establecer como proyecto de inicio**.

2. Continúe y ejecute el proyecto en el depurador para comprobar que, cuando la aplicación se inicia, se genera una excepción no controlada con el código de estado 401 (No autorizado). Esto ocurre porque ha restringido el acceso en el back-end solo para usuarios autorizados.

3. A continuación, abra el archivo MainActivity.cs en el proyecto droid y agregue la siguiente instrucción `using`.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Actualice la clase `MainActivity` para implementar la interfaz `IAuthenticate`.

		public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Para actualizar la clase `MainActivity`, agregue un campo `MobileServiceUser` y el método `Authenticate` que se muestra a continuación para admitir la interfaz `IAuthenticate`.
 
	Si quiere usar un elemento `MobileServiceAuthenticationProvider` distinto en lugar de Facebook, realice también ese cambio.

		// Define a authenticated user.
		private MobileServiceUser user;
	
        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                CreateAndShowDialog(string.Format("you are now logged in - {0}",
                    user.UserId), "Logged in!");

                success = true;
            }
            catch (Exception ex)
            {
                CreateAndShowDialog(ex.Message, "Authentication failed");
            }
            return success;
        }

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage(message);
            builder.SetTitle(title);
            builder.Create().Show();
        }


6. Actualice el método `OnCreate` de la clase `MainActivity` para inicializar el autenticador antes de cargar la aplicación.

        App.Init((IAuthenticate)this);

        LoadApplication(new App());



7. Vuelva a compilar la aplicación y ejecútela. Inicie sesión con el proveedor de autenticación que eligió y compruebe que puede acceder a la tabla como un usuario autenticado.



##Agregar autenticación a la aplicación de iOS

En esta sección, agregará autenticación al proyecto de iOS. Puede omitir esta sección si no está trabajando con dispositivos iOS.

1. En Visual Studio o Xamarin Studio, haga clic con el botón secundario en el proyecto **iOS** y haga clic en **Establecer como proyecto de inicio**.

2. Continúe y ejecute el proyecto en el depurador para comprobar que, cuando la aplicación se inicia, se genera una excepción no controlada con el código de estado 401 (No autorizado). Esto ocurre porque ha restringido el acceso en el back-end solo para usuarios autorizados.

3. A continuación, abra el archivo AppDelegate.cs en el proyecto de iOS y agregue la siguiente instrucción `using`.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Actualice la clase `AppDelegate` para implementar la interfaz `IAuthenticate`.

		public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate


5. Para actualizar la clase `AppDelegate`, agregue un campo `MobileServiceUser` y el método `Authenticate` que se muestra a continuación para admitir la interfaz `IAuthenticate`.
 
	Si quiere usar un elemento `MobileServiceAuthenticationProvider` distinto en lugar de Facebook, realice también ese cambio.

		// Define a authenticated user.
		private MobileServiceUser user;
	
        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                    if (user != null)
                    {
                        UIAlertView avAlert = new UIAlertView("Authentication", "You are now logged in " + user.UserId, null, "OK", null);
                        avAlert.Show();
                    }
                }

                success = true;
            }
            catch (Exception ex)
            {
                UIAlertView avAlert = new UIAlertView("Authentication failed", ex.Message, null, "OK", null);
                avAlert.Show();
            }
            return success;
        }

6. Actualice el método `FinishedLaunching` de la clase `AppDelegate` para inicializar el autenticador antes de cargar la aplicación.

        App.Init(this);

		LoadApplication (new App ());



7. Vuelva a compilar la aplicación y ejecútela. Inicie sesión con el proveedor de autenticación que eligió y compruebe que puede acceder a la tabla como un usuario autenticado.




##Agregar autenticación a la aplicación de Windows

En esta sección, agregará autenticación al proyecto de WinApp. Puede omitir esta sección si no está trabajando con dispositivos Windows.

1. En Visual Studio, haga clic con el botón secundario en el proyecto de **WinApp** y haga clic en **Establecer como proyecto de inicio**.

2. Continúe y ejecute el proyecto en el depurador para comprobar que, cuando la aplicación se inicia, se genera una excepción no controlada con el código de estado 401 (No autorizado). Esto ocurre porque ha restringido el acceso en el back-end solo para usuarios autorizados.

3. A continuación, abra el archivo MainPage.xaml.cs en el proyecto WinApp y agregue la siguiente instrucción `using`. Reemplace <*Your portable class library namespace*> por el espacio de nombres de la biblioteca de clases portable.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. Actualice la clase `MainPage` para implementar la interfaz `IAuthenticate`.

	    public sealed partial class MainPage : IAuthenticate


5. Para actualizar la clase `MainPage`, agregue un campo `MobileServiceUser` y el método `Authenticate` que se muestra a continuación para admitir la interfaz `IAuthenticate`.
 
	Si quiere usar un elemento `MobileServiceAuthenticationProvider` distinto en lugar de Facebook, realice también ese cambio.

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
					if (user != null)
					{
	                    var messageDialog = new Windows.UI.Popups.MessageDialog(
								string.Format("you are now logged in - {0}", user.UserId), "Authentication");
	                    messageDialog.ShowAsync();
					}
                }

                success = true;
            }
            catch (Exception ex)
            {
                var messageDialog = new Windows.UI.Popups.MessageDialog(ex.Message, "Authentication Failed");
                messageDialog.ShowAsync();
            }
            return success;
        }

6. Actualice el constructor de la clase `MainPage` para inicializar el autenticador antes de cargar la aplicación. Reemplace <*Your portable class library namespace*> por el espacio de nombres de la biblioteca de clases portable.

        public MainPage()
        {
            this.InitializeComponent();

            <Your portable class library namespace>.App.Init(this);
            
            LoadApplication(new <Your portable class library namespace>.App());
        }



7. Vuelva a compilar la aplicación y ejecútela. Inicie sesión con el proveedor de autenticación que eligió y compruebe que puede acceder a la tabla como un usuario autenticado.


##Agregar autenticación a la aplicación de Windows Phone 8.1

En esta sección, agregará autenticación para el proyecto de WinPhone81. Puede omitir esta sección si no está trabajando con dispositivos Windows Phone 8.1.

1. En Visual Studio, haga clic con el botón secundario en el proyecto de **WinPhone81** y haga clic en **Establecer como proyecto de inicio**.

2. Continúe y ejecute el proyecto en el depurador para comprobar que, cuando la aplicación se inicia, se genera una excepción no controlada con el código de estado 401 (No autorizado). Esto ocurre porque ha restringido el acceso en el back-end solo para usuarios autorizados.


3. A continuación, abra el archivo MainPage.xaml.cs en el proyecto WinPhone81 y agregue la siguiente instrucción `using`. Reemplace <*Your portable class library namespace*> por el espacio de nombres de la biblioteca de clases portable.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. Actualice la clase `MainPage` para implementar la interfaz `IAuthenticate`.

	    public sealed partial class MainPage : IAuthenticate


5. Para actualizar la clase `MainPage`, agregue un campo `MobileServiceUser` y el método `Authenticate` que se muestra a continuación para admitir la interfaz `IAuthenticate`.
 
	Si quiere usar un elemento `MobileServiceAuthenticationProvider` distinto en lugar de Facebook, realice también ese cambio.

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
					if (user != null)
					{
	                    var messageDialog = new Windows.UI.Popups.MessageDialog(
								string.Format("you are now logged in - {0}", user.UserId), "Authentication");
	                    messageDialog.ShowAsync();
					}
                }

                success = true;
            }
            catch (Exception ex)
            {
                var messageDialog = new Windows.UI.Popups.MessageDialog(ex.Message, "Authentication Failed");
                messageDialog.ShowAsync();
            }
            return success;
        }

6. Actualice el constructor de la clase `MainPage` para inicializar el autenticador antes de cargar la aplicación. Reemplace <*Your portable class library namespace*> por el espacio de nombres de la biblioteca de clases portable.

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;

            <Your portable class library namespace>.App.Init(this);

            LoadApplication(new <Your portable class library namespace>.App());
        }

7. En Windows Phone debe completar además el inicio de sesión. Abra App.xaml.cs y agregue la siguiente instrucción `using` y el código al controlador `OnActivated` en la clase `App`.

	```
		using Microsoft.WindowsAzure.MobileServices;
	```

		protected override void OnActivated(IActivatedEventArgs args)
		{
		    base.OnActivated(args);
		
		    if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
		    {
		        var client = TodoItemManager.DefaultManager.CurrentClient as MobileServiceClient;
		        client.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
		    }
		}



8. Vuelva a compilar la aplicación y ejecútela. Inicie sesión con el proveedor de autenticación que eligió y compruebe que puede acceder a la tabla como un usuario autenticado.

<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333


 

<!---HONumber=AcomDC_1210_2015--->