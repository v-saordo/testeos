<properties
	pageTitle="Introducción a .NET de Azure AD | Microsoft Azure"
	description="Creación de una aplicación de escritorio .NET de Windows que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth."
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>


# Integración de Azure AD en una aplicación WPF de escritorio de Windows

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si está desarrollando una aplicación de escritorio, Azure AD le facilita la autenticación de sus usuarios mediante sus cuentas de Active Directory. También permite a la aplicación consumir con seguridad cualquier API web protegida por Azure AD, como las API de Office 365 o la API de Azure.

Para los clientes nativos .NET que necesitan tener acceso a recursos protegidos, Azure AD proporciona la Biblioteca de autenticación de Active Directory (ADAL). El único propósito de ADAL es facilitar a su aplicación la obtención de tokens de acceso. Para demostrar lo fácil que es, crearemos aquí una aplicación de lista de tareas pendientes (ToDo) de .NET WPF que permita realizar las siguientes acciones:

-	Obtener tokens de acceso para llamar a la API Graph de Azure AD utilizando el [protocolo de autenticación OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)
-	Buscar un directorio para los usuarios con un alias determinado
-	Cerrar la sesión de los usuarios

Para crear la aplicación de trabajo completa, deberá realizar estas acciones:

2. Registrar la aplicación con Azure AD
3. Instalar y configurar ADAL
5. Usar ADAL para obtener tokens de Azure AD

Para empezar, [descargue el esquema de la aplicación](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip). También necesitará a un inquilino de Azure AD en el que pueda crear usuarios y registrar una aplicación. Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

## *1. Registro de la aplicación DirectorySearcher*
Para habilitar la aplicación para obtener tokens, primero deberá registrarla en su inquilino de Azure AD y concederle permiso de acceso a la API Graph de Azure AD:

-	Inicie sesión en el Portal de administración de Azure.
-	En el panel de navegación izquierdo, haga clic en **Active Directory**.
-	Seleccione el inquilino en el que va a registrar la aplicación.
-	Haga clic en la pestaña **Aplicaciones** y en **Agregar** en el cajón inferior.
-	Siga las indicaciones y cree una nueva **Aplicación de cliente nativa**.
    -	El **nombre** de la aplicación servirá de descripción de la aplicación para los usuarios finales.
    -	El **URI de redirección** es una combinación de esquema y cadena que utilizará Azure AD para devolver las respuestas de los tokens. Escriba un valor específico para la aplicación, por ejemplo, `http://DirectorySearcher`.
-	Una vez que haya completado el registro, AAD asignará a su aplicación un identificador de cliente único. Necesitará este valor en las secciones siguientes, de modo que cópielo desde la pestaña **Configurar**.
- También en la pestaña **Configurar**, busque la sección "Permisos para otras aplicaciones". Para la aplicación "Azure Active Directory", agregue el permiso de **acceso al directorio de la organización** en **Permisos delegados**. Esto permitirá a su aplicación consultar la API Graph para los usuarios.

## *2. Instalación y configuración de ADAL*
Ahora que tiene una aplicación en Azure AD, puede instalar ADAL y escribir el código relacionado con la identidad. Para que ADAL pueda comunicarse con Azure AD, tiene que proporcionarle información sobre el registro de la aplicación. Comience agregando ADAL al proyecto de DirectorySearcher con la consola del Administrador de paquetes.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-	En el proyecto DirectorySearcher, abra `app.config`. Reemplace los valores de los elementos de la sesión `<appSettings>` para que reflejen los valores especificados en el Portal de Azure. El código hará referencia a estos valores cada vez que use ADAL.
    -	`ida:Tenant` es el dominio del inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com.
    -	`ida:ClientId` es el identificador de cliente de la aplicación que copió del portal.
    -	El `ida:RedirectUri` es la URL de redirección que ha registrado en el portal.

## *3. Uso de ADAL para obtener tokens de AAD*
El principio básico inherente a ADAL es que cada vez que la aplicación necesita un token de acceso, simplemente llama a `authContext.AcquireToken(...)` y ADAL se encarga del resto.

-	En el proyecto `DirectorySearcher`, abra `MainWindow.xaml.cs` y busque el método `MainWindow()`. El primer paso consiste en inicializar el `AuthenticationContext` de la aplicación, que es la clase principal de ADAL. Este es el lugar en el que se pasan a ADAL las coordenadas necesarias para comunicarse con Azure AD e indicarle cómo almacenar en caché los tokens.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());
    ...
}
```

- Ahora busque el método `Search(...)`, que se invoca cuando el usuario hace clic en el botón de búsqueda en la interfaz de usuario de la aplicación. Este método realiza una solicitud GET a la API Graph de Azure AD para realizar una consulta sobre los usuarios cuyo UPN comienza con el término de búsqueda especificado. Sin embargo, para realizar una consulta a la API Graph, tiene que incluir un access\_token en el encabezado `Authorization` de la solicitud, que es donde entra ADAL.

```C#
private void Search(object sender, RoutedEventArgs e)
{
    ...

    // Get an Access Token for the Graph API
    AuthenticationResult result = null;
    try
    {
        result = authContext.AcquireToken(graphResourceId, clientId, redirectUri);
        UserNameLabel.Content = result.UserInfo.DisplayableId;
        SignOutButton.Visibility = Visibility.Visible;
    }
    catch (AdalException ex)
    {
        // An unexpected error occurred, or user canceled the sign in.
        if (ex.ErrorCode != "access_denied")
            MessageBox.Show(ex.Message);

        return;
    }

    ...
}
```
- Cuando la aplicación solicita un token mediante una llamada a `AcquireToken(...)`, ADAL intentará devolver un token sin solicitar al usuario las credenciales. Si ADAL determina que el usuario debe iniciar sesión para obtener un token, mostrará un cuadro de diálogo de inicio de sesión, recopilará las credenciales del usuario y devolverá un token tras una autenticación correcta. Si ADAL no puede devolver un token por alguna razón, mostrará una `AdalException`.
- Observe que el objeto `AuthenticationResult` contiene un objeto `UserInfo` que puede usarse para recopilar información puede necesitar la aplicación. En el DirectorySearcher, se usa `UserInfo` para personalizar la interfaz de usuario de la aplicación con el identificador del usuario.

- Cuando el usuario hace clic en el botón "Cerrar sesión", queremos asegurarnos de que la próxima llamada a `AcquireToken(...)` solicite al usuario que inicie sesión. Con ADAL, esto es tan fácil como borrar la caché de tokens:

```C#
private void SignOut(object sender = null, RoutedEventArgs args = null)
{
    // Clear the token cache
    authContext.TokenCache.Clear();

    ...
}
```

- Sin embargo, si el usuario no hace clic en el botón "Cerrar sesión", puede que desee mantener la sesión del usuario para la próxima vez que ejecute DirectorySearcher. Cuando se inicie la aplicación, puede comprobar la caché de tokens de ADAL para un token existente y actualizar la interfaz de usuario en consecuencia. De nuevo en `MainWindow()`, realice otra llamada a `AcquireToken(...)`, en esta ocasión pasando el parámetro `PromptBehavior.Never`. `PromptBehavior.Never` indicará a ADAL que no se debe solicitar al usuario que inicie sesión. En lugar de ello, ADAL debe iniciar una excepción si no puede devolver un token.

```C#
public MainWindow()
{
    InitializeComponent();

    authContext = new AuthenticationContext(authority, new FileCache());

    // As the application starts, try to get an access token without prompting the user.  If one exists, show the user as signed in.
    AuthenticationResult result = null;
    try
    {
        result = authContext.AcquireToken(graphResourceId, clientId, redirectUri, PromptBehavior.Never);
    }
    catch (AdalException ex)
    {
        if (ex.ErrorCode != "user_interaction_required")
        {
            // An unexpected error occurred.
            MessageBox.Show(ex.Message);
        }

        // If user interaction is required, proceed to main page without singing the user in.
        return;
    }

    // A valid token is in the cache
    SignOutButton.Visibility = Visibility.Visible;
    UserNameLabel.Content = result.UserInfo.DisplayableId;
}
```

¡Enhorabuena! Ahora tiene una aplicación de WPF de .NET operativa que tiene la capacidad de autenticar usuarios, realizar llamadas seguras a las API web que usan OAuth 2.0 y obtener información básica sobre el usuario. Si todavía no lo ha hecho, ahora es el momento de completar el inquilino con algunos usuarios. Ejecute la aplicación DirectorySearcher e inicie sesión con uno de esos usuarios. Busque otros usuarios según su UPN. Cierre la aplicación y vuelva a ejecutarla. Observe cómo la sesión del usuario permanece intacta. Cierre la sesión y vuelva a iniciarla como otro usuario.

ADAL facilita la incorporación de todas estas características comunes de identidad en la aplicación. Se encarga de todo el trabajo duro: administración de la caché, compatibilidad con el protocolo OAuth, presentación del usuario con una interfaz de usuario de inicio de sesión, actualización de los tokens caducados, etc. Todo lo que necesita saber es una única llamada de API, `authContext.AcquireToken(...)`.

Como referencia, [aquí](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip) puede ver el ejemplo finalizado (sin sus valores de configuración). Ahora puede pasar a otros escenarios. También puede probar lo siguiente:

[Protección de una API Web .NET con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0128_2016-->