<properties
	pageTitle="Introducción a Windows Phone de Azure AD | Microsoft Azure"
	description="Creación de una aplicación de Windows Phone que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth."
	services="active-directory"
	documentationCenter="windows"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>



# Integración de Azure AD con una aplicación de Windows Phone

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Si está desarrollando una aplicación de Windows Phone, Azure AD le facilita la autenticación de sus usuarios con sus cuentas de Active Directory. También permite a la aplicación consumir con seguridad cualquier API web protegida por Azure AD, como las API de Office 365 o la API de Azure.

Para los clientes nativos .NET que necesitan tener acceso a recursos protegidos, Azure AD proporciona la biblioteca de autenticación de Active Directory (ADAL). El único propósito de ADAL es facilitar a su aplicación la obtención de tokens de acceso. Para demostrar lo sencillo que es, crearemos a continuación una aplicación de Windows Phone 8.1 de "buscador de directorios" que:

-	Obtenga tokens de acceso para llamar a la API de gráficos de Azure AD utilizando el [protocolo de autenticación OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Busque un directorio para los usuarios con un UPN determinado.
-	Cierre la sesión de los usuarios.

Para crear la aplicación de trabajo completa, deberá:

2. Registrar la aplicación con Azure AD.
3. Instalar y configurar ADAL
5. Usar ADAL para obtener tokens de Azure AD.

Para empezar, [descargue el proyecto del esquema](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip). Cualquiera de los dos es una solución de Visual Studio 2013. También necesitará a un inquilino de Azure AD en el que pueda crear usuarios y registrar una aplicación. Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

## *1. Registro de la aplicación de buscador de directorios*
Para habilitar la aplicación para obtener tokens, primero deberá registrarla en su inquilino de Azure AD y concederle permiso de acceso a la API de gráficos de Azure AD:

-	Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
-	En el panel de navegación izquierdo, haga clic en **Active Directory**.
-	Seleccione el inquilino en el que va a registrar la aplicación.
-	Haga clic en la pestaña **Aplicaciones** y en **Agregar** en el cajón inferior.
-	Siga las indicaciones y cree una nueva **Aplicación de cliente nativa**.
    -	El **nombre** de la aplicación servirá de descripción de la aplicación para los usuarios finales.
    -	El **URI de redirección** es una combinación de esquema y cadena que utilizará Azure AD para devolver las respuestas de los tokens. Escriba un valor de marcador de posición por ahora, por ejemplo, `http://DirectorySearcher`. Reemplazaremos este valor más adelante.
-	Una vez que haya completado el registro, AAD asignará a su aplicación un identificador de cliente único. Necesitará este valor en las secciones siguientes, de modo que cópielo desde la pestaña **Configurar**.
- También en la pestaña **Configurar**, busque la sección "Permisos para otras aplicaciones". Para la aplicación "Azure Active Directory", agregue el permiso de **acceso al directorio de la organización** en **Permisos delegados**. Esto permitirá a su aplicación consultar la API Graph para los usuarios.

## *2. Instalación y configuración de ADAL*
Ahora que tiene una aplicación en Azure AD, puede instalar ADAL y escribir el código relacionado con la identidad. Para que ADAL pueda comunicarse con Azure AD, tiene que proporcionarle información sobre el registro de la aplicación. Comience agregando ADAL al proyecto de DirectorySearcher con la consola del Administrador de paquetes.

```
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

-	En el proyecto de buscador de directorios, abra `MainPage.xaml.cs`. Reemplace los valores de la región `Config Values` para que reflejen los valores especificados en el Portal de Azure. El código hará referencia a estos valores siempre que use ADAL.
    -	`tenant` es el dominio del inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com.
    -	`clientId` es el identificador de cliente de la aplicación que copió del portal.
-	Ahora deberá detectar el URI de devolución de llamada para la aplicación de Windows Phone. Establezca un punto de interrupción en esta línea en el método `MainPage`:

```
redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker.GetCurrentApplicationCallbackUri();
```
- Ejecute la aplicación y copie aparte el valor de `redirectUri` cuando se alcance el punto de interrupción. Debe tener un aspecto similar al siguiente.

```
ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/
```

- En la pestaña **Configurar** de la aplicación en el Portal de administración de Azure, reemplace el valor de **RedirectUri** por este valor.  

## *3. Uso de ADAL para obtener tokens de AAD*
El principio básico inherente a ADAL es que cada vez que la aplicación necesita un token de acceso, simplemente llama a `authContext.AcquireToken(…)` y ADAL se encarga del resto.

-	El primer paso consiste en inicializar `AuthenticationContext` de la aplicación: clase principal de ADAL. Este es el lugar en el que pasa a ADAL las coordenadas que necesita para comunicarse con Azure AD e indicarle cómo almacenar en caché los tokens.

```C#
public MainPage()
{
    ...

    // ADAL for Windows Phone 8.1 builds AuthenticationContext instances through a factory
    authContext = AuthenticationContext.CreateAsync(authority).GetResults();
}
```

- Ahora busque el método `Search(...)`, que se invoca cuando el usuario hace clic en el botón de búsqueda en la interfaz de usuario de la aplicación. Este método realiza una solicitud GET a la API Graph de Azure AD para realizar una consulta sobre los usuarios cuyo UPN comienza con el término de búsqueda especificado. Sin embargo, para realizar una consulta a la API Graph, tiene que incluir un access\_token en el encabezado `Authorization` de la solicitud, que es donde entra ADAL.

```C#
private async void Search(object sender, RoutedEventArgs e)
{
    ...

    // Try to get a token without triggering any user prompt.
    // ADAL will check whether the requested token is in ADAL's token cache or can otherwise be obtained without user interaction.
    AuthenticationResult result = await authContext.AcquireTokenSilentAsync(graphResourceId, clientId);
    if (result != null && result.Status == AuthenticationStatus.Success)
    {
        // A token was successfully retrieved.
        QueryGraph(result);
    }
    else
    {
        // Acquiring a token without user interaction was not possible.
        // Trigger an authentication experience and specify that once a token has been obtained the QueryGraph method should be called
        authContext.AcquireTokenAndContinue(graphResourceId, clientId, redirectURI, QueryGraph);
    }
}
```
- Si es necesaria la autenticación interactiva, ADAL utilizará el agente de autenticación Web (WAB) de Windows Phone y el [modelo de continuación](http://www.cloudidentity.com/blog/2014/06/16/adal-for-windows-phone-8-1-deep-dive/) para mostrar la página de inicio de sesión de Azure AD. Cuando el usuario inicia sesión, la aplicación necesita pasar a ADAL los resultados de la interacción de WAB. Esto es tan sencillo como implementar la interfaz `ContinueWebAuthentication`:

```C#
// This method is automatically invoked when the application
// is reactivated after an authentication interaction through WebAuthenticationBroker.
public async void ContinueWebAuthentication(WebAuthenticationBrokerContinuationEventArgs args)
{
    // pass the authentication interaction results to ADAL, which will
    // conclude the token acquisition operation and invoke the callback specified in AcquireTokenAndContinue.
    await authContext.ContinueAcquireTokenAsync(args);
}
```

- Ahora es el momento de usar el `AuthenticationResult` que ADAL ha devuelto a la aplicación. En la devolución de llamada `QueryGraph(...)`, adjunte access\_token adquirido a la solicitud GET en el encabezado de autorización:

```C#
private async void QueryGraph(AuthenticationResult result)
{
    if (result.Status != AuthenticationStatus.Success)
    {
        MessageDialog dialog = new MessageDialog(string.Format("If the error continues, please contact your administrator.\n\nError: {0}\n\nError Description:\n\n{1}", result.Error, result.ErrorDescription), "Sorry, an error occurred while signing you in.");
        await dialog.ShowAsync();
    }

    // Add the access token to the Authorization Header of the call to the Graph API, and call the Graph API.
    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

    ...
}
```
- También puede utilizar el objeto `AuthenticationResult` para mostrar información sobre el usuario en la aplicación. En el método `QueryGraph(...)`, utilice el resultado para mostrar el identificador del usuario en la página:

```C#
// Update the Page UI to represent the signed in user
ActiveUser.Text = result.UserInfo.DisplayableId;
```
- Por último, también puede usar ADAL para cerrar la sesión del usuario en la aplicación. Cuando el usuario hace clic en el botón "Cerrar sesión", queremos asegurarnos de que la próxima llamada a `AcquireTokenSilentAsync(...)` fallará. Con ADAL, esto es tan fácil como borrar la caché de tokens:

```C#
private void SignOut()
{
    // Clear session state from the token cache.
    authContext.TokenCache.Clear();

    ...
}
```

¡Enhorabuena! Ahora tiene una aplicación de Windows Phone en funcionamiento que tiene la capacidad de autenticar usuarios, realizar llamadas seguras a las API Web que usan OAuth 2.0 e y obtener información básica sobre el usuario. Si no lo ha hecho ya, ahora es el momento de completar el inquilino con algunos usuarios. Ejecute la aplicación DirectorySearcher e inicie sesión con uno de esos usuarios. Busque otros usuarios según su UPN. Cierre la aplicación y vuelva a ejecutarla. Observe cómo la sesión del usuario permanece intacta. Cierre la sesión y vuelva a iniciarla como otro usuario.

ADAL facilita la incorporación de todas estas características comunes de identidad en la aplicación. Se encarga de todo el trabajo duro: administración de la caché, compatibilidad con el protocolo OAuth, presentación del usuario con una interfaz de usuario de inicio de sesión, actualización de los tokens caducados, etc. Todo lo que necesita saber es una única llamada de API, `authContext.AcquireToken*(…)`.

Como referencia, se proporciona el ejemplo finalizado (sin sus valores de configuración) [aquí](https://github.com/AzureADQuickStarts/NativeClient-WindowsPhone/archive/complete.zip). Ahora puede trasladarse a escenarios de identidad adicionales. Es posible que desee probar:

[Protección de una API Web .NET con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0128_2016-->