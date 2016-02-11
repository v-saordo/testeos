<properties
	pageTitle="Vista previa de Azure AD B2C | Microsoft Azure"
	description="Cómo crear una aplicación de escritorio Windows con inicio de sesión, registro y administración de perfiles con Azure AD B2C."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Vista previa de Azure AD B2C: Creación de una aplicación de escritorio de Windows

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

Con Azure AD B2C, puede agregar las características de administración de identidades de autoservicio eficaces a su aplicación de escritorio en unos cuantos pasos. Este artículo le mostrará cómo crear una aplicación .NET MPF de "Lista de tareas pendientes" que incluya el registro de usuario, el inicio de sesión y la administración de perfiles. Incluirá compatibilidad para el registro y el inicio de sesión con un nombre de usuario o correo electrónico, así como cuentas sociales como Facebook y Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\. Obtener un directorio de Azure AD B2C

Para poder usar Azure AD B2C, debe crear un directorio o inquilino. Un directorio es un contenedor para todos los usuarios, aplicaciones, grupos, etc. Si no tiene uno todavía, vaya a [crear un directorio de B2C](active-directory-b2c-get-started.md) antes de continuar.

## 2\. Creación de una aplicación

Ahora debe crear una aplicación en su directorio de B2C, que ofrece a Azure AD información que necesita para comunicarse de forma segura con su aplicación. Para crear una aplicación, siga [estas instrucciones](active-directory-b2c-app-registration.md). Asegúrese de

- Incluir un **cliente nativo** en la aplicación.
- Escribir el **URI de redirección** `urn:ietf:wg:oauth:2.0:oob`: es la dirección URL predeterminada para este ejemplo de código.
- Escribir el **Id. de aplicación** asignado a la aplicación. Lo necesitará en breve.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Crear sus directivas

En Azure AD B2C, cada experiencia del usuario se define mediante una [**directiva**](active-directory-b2c-reference-policies.md). Este ejemplo de código contiene tres experiencias de identidad: registro, inicio de sesión y editar perfil. Debe crear una directiva de cada tipo, como se describe en el [artículo de referencia de directiva](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Al crear sus tres directivas, asegúrese de:

- Elegir **Registro de id. de usuario** o **Registro de correo electrónico** en la hoja de proveedores de identidades.
- Seleccionar **Nombre para mostrar** y algunos otros atributos de registro en la directiva de registro.
- Elegir las notificaciones **Nombre para mostrar** e **Id. de objeto** como notificación de aplicación en todas las directivas. Puede elegir también otras notificaciones.
- Copiar el **Nombre** de cada directiva después de crearla. Debe tener el prefijo `b2c_1_`. Necesitará esos nombres de directivas en breve.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Cuando tenga tres directivas creadas correctamente, estará listo para crear su aplicación.

## 4\. Descargar el código

El código de este tutorial se conserva [en GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Para generar el ejemplo a medida que avance, puede [descargar un proyecto de esqueleto como .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip) o clonar el esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

La aplicación completada también estará [disponible como .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) o en la rama `complete` del mismo repositorio.

Una vez descargado el código de ejemplo, abra el archivo `.sln` de Visual Studio para empezar. Observará que hay dos proyectos en la solución: un proyecto `TaskClient` y un proyecto `TaskService`. La `TaskClient` es una aplicación web MVC con la que el usuario interactúa. `TaskService` es la API web de back-end de la aplicación que almacena la lista de tareas pendientes de cada usuario. En este caso, tanto el `TaskClient` como el `TaskService` se representarán mediante un solo **Id. de aplicación**, ya que ambos conforman una aplicación lógica.

## 5\. Configurar el servicio de tarea

Cuando `TaskService` recibe solicitudes de `TaskClient`, busca un token de acceso válido para autenticar la solicitud. Para validar el token de acceso, debe proporcionar a `TaskService` algo de información sobre la aplicación. En el proyecto `TaskService`, abra el archivo `web.config` en la raíz del proyecto y reemplace los valores de la sección `<appSettings>`:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
  </appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Si quiere saber de qué forma una aplicación web autentica de forma segura las solicitudes con Azure AD B2C, consulte nuestro [artículo de introducción a la API web](active-directory-b2c-devquickstarts-api-dotnet.md).

## 6. Ejecución de directivas
Ahora que `TaskService` está listo para autenticar las solicitudes, podemos implementar `TaskClient`. Su aplicación se comunica con Azure AD B2C mediante el envío de solicitudes de autenticación HTTP, especificando la directiva que desea ejecutar como parte de la solicitud. Para aplicaciones de escritorio .NET, puede usar la **Biblioteca de autenticación de Active Directory (AAL)** para enviar mensajes de autenticación OAuth 2.0, ejecutar directivas y obtener tokens para llamar a las API web.

#### Instalación de AAL
Para comenzar, agregue ADAL al proyecto TaskClient mediante la Consola del Administrador de paquetes de Visual Studio.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

#### Especificación de detalles de B2C
Abra el archivo `Globals.cs` y reemplace cada uno de los valores de propiedad por lo suyos propios. Esta clase se usa en todo `TaskClient` para hacer referencia a valores de uso frecuente.

```C#
public static class Globals
{
	public static string tenant = "{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}";
	public static string clientId = "{Enter the Application ID assigned to your app by the Azure Portal}";
	public static string signInPolicy = "{Enter the name of your sign in policy, e.g. b2c_1_sign_in}";
	public static string signUpPolicy = "{Enter the name of your sign up policy, e.g. b2c_1_sign_up}";
	public static string editProfilePolicy = "{Enter the name of your edit profile policy, e.g. b2c_1_edit_profile}";

	public static string taskServiceUrl = "https://localhost:44332";
	public static string aadInstance = "https://login.microsoftonline.com/";
	public static string redirectUri = "urn:ietf:wg:oauth:2.0:oob";

}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


#### Creación de un AuthenticationContext
La clase principal de ADAL es `AuthenticationContext`, que representa la conexión de su aplicación con el directorio B2C. Cuando se inicie la aplicación, cree una instancia de `AuthenticationContext` en `MainWindow.xaml.cs`, que se pueda usar en la ventana.

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app,
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

#### Inicio de un flujo de registro
Cuando el usuario hace clic en el botón de registro, deseamos iniciar un flujo de registro mediante la directiva de registro creada. Con ADAL, basta con llamar a `authContext.AcquireTokenAsync(...)`. Los parámetros que se pasan a `AcquireTokenAsync(...)` determinarán el token que recibe, la directiva usada en la solicitud de autenticación, etc.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that we want a token to the our own backend API
		// Use the PromptBehavior.Always flag to indicate to ADAL that it should show a sign-up UI no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;

		// When the request completes successfully, you can get user information form the AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign up successfully completes, display the user's To-Do List
		GetTodoList();
	}

	// Handle any exeptions that occurred during execution of the policy.
	catch (AdalException ex)
	{
		if (ex.ErrorCode == "authentication_canceled")
		{
			MessageBox.Show("Sign up was canceled by the user");
		}
		else
		{
			// An unexpected error occurred.
			string message = ex.Message;
			if (ex.InnerException != null)
			{
				message += "Inner Exception : " + ex.InnerException.Message;
			}

			MessageBox.Show(message);
		}

		return;
	}
}
```

#### Inicio de un flujo de inicio de sesión
Se puede iniciar un flujo de inicio de sesión del mismo modo que el flujo de registro. Cuando el usuario hace clic en el botón de inicio de sesión, realiza la misma llamada a ADAL, esta vez mediante la directiva de inicio de sesión:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.signInPolicy);
		...			
```

#### Inicio de un flujo de edición de perfil
De nuevo, puede ejecutar la directiva de edición del perfil de la misma manera:

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.editProfilePolicy);
```

En todos estos casos, ADAL devolverá un token en su `AuthenticationResult` o generará una excepción. Cada vez que reciba un token de ADAL, puede usar el objeto `AuthenticationResult.UserInfo` para actualizar los datos de usuario en la aplicación, como la interfaz de usuario. ADAL también almacenará en memoria caché el token para usarlo en otras partes de la aplicación.

## 7\. Llamar a API
Ya hemos usado ADAL para ejecutar directivas y obtener tokens. En muchos casos, sin embargo, le interesará comprobar si existe un token almacenado en caché sin ejecutar ninguna directiva. Uno de estos casos se da cuando la aplicación intenta recuperar la lista de tareas pendientes del usuario en el `TaskService`. Puede usar el mismo método `authContext.AcquireTokenAsync(...)` para hacer esto, para ello usará de nuevo el `clientId` como parámetro de ámbito pero esta vez con `PromptBehavior.Never`:

```C#
private async void GetTodoList()
{
	AuthenticationResult result = null;
	try
	{
		// Here we want to check for a cached token, independent of whatever policy was used to acquire it.
		TokenCacheItem tci = authContext.TokenCache.ReadItems().Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
		string existingPolicy = tci == null ? null : tci.Policy;

		// We use the PromptBehavior.Never flag to indicate that ADAL should throw an exception if a token
		// could not be acquired from the cache, rather than automatically prompting the user to sign in.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);

	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign-in.
		if (ex.ErrorCode == "user_interaction_required")
		{
			MessageBox.Show("Please sign up or sign in first");
			SignInButton.Visibility = Visibility.Visible;
			SignUpButton.Visibility = Visibility.Visible;
			EditProfileButton.Visibility = Visibility.Collapsed;
			SignOutButton.Visibility = Visibility.Collapsed;
			UsernameLabel.Content = string.Empty;

		}
		else
		{
			// An unexpected error occurred.
			string message = ex.Message;
			if (ex.InnerException != null)
			{
				message += "Inner Exception : " + ex.InnerException.Message;
			}
			MessageBox.Show(message);
		}

		return;
	}
	...
```

Cuando la llamada a `AcquireTokenAsync(...)` se realiza correctamente y se encuentra un token en la memoria caché, puede agregar el token al encabezado `Authorization` de la solicitud HTTP para que el `TaskService` puede autenticar la solicitud de lectura de la lista de tareas pendientes del usuario:

```C#
	...
	// Once the token has been returned by ADAL, add it to the http authorization header, before making the call to the TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the To Do list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
```

Puede usar este mismo patrón cada vez que desee comprobar la caché de tokens para ver los tokens sin solicitar al usuario que inicie sesión. Por ejemplo, cuando se inicia la aplicación, queremos buscar los tokens existentes en la `FileCache`, para que la sesión iniciada del usuario se mantenga siempre que se ejecute la aplicación. Puede ver el mismo código en el evento `OnInitialized` de `MainWindow`, que controla este caso de primera ejecución.

## 8\. Cerrar la sesión del usuario
Por último, puede usar ADAL para finalizar la sesión del usuario con la aplicación cuando el usuario hace clic en el botón "Cerrar sesión". Con ADAL, resulta tan sencillo como borrar todos los tokens de la caché de tokens:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses, it is not part of ADAL.
	ClearCookies();

	// Update the UI to show the user as signed out.
	TaskList.ItemsSource = string.Empty;
	SignInButton.Visibility = Visibility.Visible;
	SignUpButton.Visibility = Visibility.Visible;
	EditProfileButton.Visibility = Visibility.Collapsed;
	SignOutButton.Visibility = Visibility.Collapsed;
	return;
}
```

## 9\. Ejecutar la aplicación de ejemplo

Por último, compile y ejecute tanto `TaskClient` como `TaskService`. Regístrese para la aplicación con una dirección de correo electrónico o nombre de usuario. Cierre la sesión y vuelva a iniciarla como otro usuario. Edite el perfil de ese usuario. Cierre la sesión y regístrese con otro usuario.

## 10\. Agregar IDP sociales

Actualmente, la aplicación solo admite el registro y el inicio de sesión de usuario con lo que se denominan **cuentas locales** (cuentas almacenadas en el directorio B2C con un nombre de usuario y una contraseña). Con Azure AD B2C, puede agregar compatibilidad para otros **proveedores de identidades**, o IDP, sin cambiar el código.

Para agregar IDP sociales a su aplicación, comience siguiendo las instrucciones detalladas en uno o dos de estos artículos. Para cada IDP que quiere admitir, necesitará registrar una aplicación en su sistema y obtener un Id. de cliente.

- [Configurar Facebook como una IDP](active-directory-b2c-setup-fb-app.md)
- [Configurar Google como una IDP](active-directory-b2c-setup-goog-app.md)
- [Configurar Amazon como una IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurar LinkedIn como una IDP](active-directory-b2c-setup-li-app.md)

Cuando haya agregado los proveedores de identidades a su directorio B2C, necesitará regresar y editar cada una de las tres directivas para incluir los nuevos IDP, como se describe en el [artículo de referencia de directiva](active-directory-b2c-reference-policies.md). Cuando haya guardado las directivas, solo tiene que volver a ejecutar la aplicación. Debería ver los IDP nuevos agregados como una opción de inicio de sesión y registro en cada una de sus experiencias de identidad.

Puede experimentar con su directivas con libertad y observar el efecto en su aplicación de ejemplo: agregar o quitar IDP, manipular las notificaciones de la aplicación, cambiar los atributos de registro. Pruebe hasta que comience a comprender de qué manera se vinculan entre sí las directivas, las solicitudes de autenticación y ADAL.

Como referencia, el ejemplo completado [se ofrece aquí en forma de archivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip), aunque también puede clonarlo desde GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=AcomDC_0128_2016-->