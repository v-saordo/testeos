<properties
	pageTitle="Introducción a Xamarin de Azure AD | Microsoft Azure"
	description="Creación de una aplicación Xamarin que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth."
	services="active-directory"
	documentationCenter="xamarin"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>


# Integración de Azure AD en una aplicación Xamarin

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Xamarin le permite escribir aplicaciones en C# que se pueden ejecutar en distintas plataformas, incluidos los dispositivos móviles y equipos similares. Si está creando una aplicación con Xamarin, Azure AD le facilita la autenticación de sus usuarios con sus cuentas de Active Directory. También permite a la aplicación consumir con seguridad cualquier API web protegida por Azure AD, como las API de Office 365 o la API de Azure.

Para las aplicaciones Xamarin que necesitan tener acceso a recursos protegidos, Azure AD proporciona la biblioteca de autenticación de Active Directory o ADAL. El único propósito de ADAL es facilitar a su aplicación la obtención de tokens de acceso. Para demostrar lo sencillo que es, crearemos a continuación una aplicación de "buscador de directorios" que:

-	Se ejecute en iOS, Android, Escritorio de Windows, Windows Phone y Tienda Windows.
- Utilice una biblioteca de clases portables (PCL) única para autenticar los usuarios y obtener tokens para Azure AD Graph API.
-	Busque un directorio para los usuarios con un UPN determinado.

Para crear la aplicación de trabajo completa, deberá:

2. Configuración de su entorno de desarrollo de Xamarin
2. Registrar la aplicación con Azure AD.
3. Instalar y configurar ADAL
5. Usar ADAL para obtener tokens de Azure AD.

Para empezar, [descargue el proyecto del esquema](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Cualquiera de los dos es una solución de Visual Studio 2013. También necesitará a un inquilino de Azure AD en el que pueda crear usuarios y registrar una aplicación. Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

## *0. Configuración de su entorno de desarrollo de Xamarin*
Hay varias maneras diferentes en las que puede desear configurar Xamarin, dependiendo de las plataformas específicas a las que quiera dirigirse. Puesto que este tutorial incluye proyectos para iOS, Android y Windows, seleccionaremos usar Visual Studio 2013 y el [host de compilación Xamarin.iOS](http://developer.xamarin.com/guides/ios/getting_started/installation/windows/), que requerirá: - Una máquina Windows para ejecutar Visual Studio y las aplicaciones Windows - Una máquina OSX (si desea ejecutar la aplicación iOS) - Una suscripción Xamarin Business (una [prueba gratuita](http://developer.xamarin.com/guides/cross-platform/getting_started/beginning_a_xamarin_trial/) es suficiente) - [Xamarin para Windows](https://xamarin.com/download), que incluye Xamarin.iOS, Xamarin.Android y la integración de Visual Studio (recomendada para este ejemplo). - [Xamarin para OS X](https://xamarin.com/download), que incluye Xamarin.iOS (y el host de compilación de Xamarin.iOS), Xamarin.Android, Xamarin.Mac y Xamarin Studio.

Se recomienda comenzar con la [página de descargas de Xamarin](https://xamarin.com/download) e instalar Xamarin en su Mac y PC. Si no dispone de los equipos disponibles, puede ejecutar el ejemplo, pero tendrán que omitirse determinados proyectos. Siga el [guías detalladas de instalación](http://developer.xamarin.com/guides/cross-platform/getting_started/installation/) para iOS y Android, y si desea saber más acerca de las opciones disponibles para el desarrollo, eche un vistazo a la guía [Creación de aplicaciones de plataformas cruzadas](http://developer.xamarin.com/guides/cross-platform/application_fundamentals/building_cross_platform_applications/part_1_-_understanding_the_xamarin_mobile_platform/). No es necesario configurar un dispositivo para el desarrollo en este momento, ni necesita una suscripción al programa para desarrolladores de Apple (a menos que, por supuesto, desee ejecutar la aplicación iOS en un dispositivo).

Una vez que haya completado la configuración necesaria, abra la solución en Visual Studio para comenzar. Encontrará seis proyectos: cinco proyectos específicos de la plataforma y una biblioteca de clases portable que se compartirá entre todas las plataformas, `DirectorySearcher.cs`.


## *1. Registro de la aplicación de buscador de directorios*
Para habilitar la aplicación para obtener tokens, primero deberá registrarla en su inquilino de Azure AD y concederle permiso de acceso a la API de gráficos de Azure AD:

-	Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
-	En el panel de navegación izquierdo, haga clic en **Active Directory**.
-	Seleccione el inquilino en el que va a registrar la aplicación.
-	Haga clic en la pestaña **Aplicaciones** y en **Agregar** en el cajón inferior.
-	Siga las indicaciones y cree una nueva **Aplicación de cliente nativa**.
    -	El **nombre** de la aplicación servirá de descripción de la aplicación para los usuarios finales.
    -	El **URI de redirección** es una combinación de esquema y cadena que utilizará Azure AD para devolver las respuestas de los tokens. Escriba un valor, por ejemplo `http://DirectorySearcher`.
-	Una vez que haya completado el registro, AAD asignará a su aplicación un identificador de cliente único. Necesitará este valor en las secciones siguientes, de modo que cópielo desde la pestaña **Configurar**.
- También en la pestaña **Configurar**, busque la sección "Permisos para otras aplicaciones". Para la aplicación "Azure Active Directory", agregue el permiso de **acceso al directorio de la organización** en **Permisos delegados**. Esto permitirá a su aplicación consultar la API Graph para los usuarios.

## *2. Instalación y configuración de ADAL*
Ahora que tiene una aplicación en Azure AD, puede instalar ADAL y escribir el código relacionado con la identidad. Para que ADAL pueda comunicarse con Azure AD, tiene que proporcionarle información sobre el registro de la aplicación. Comience agregando ADAL a cada uno de los proyectos de la solución con la Consola del Administrador de paquetes.

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirectorySearcherLib -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Android -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Desktop -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-iOS -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal.Windows -IncludePrerelease
`

`
PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -ProjectName DirSearchClient-Universal.WindowsPhone -IncludePrerelease
`

- Debe observar que se agregan dos referencias de biblioteca a cada proyecto: la parte PCL de ADAL y una parte específica de la plataforma.

-	En el proyecto DirectorySearcherLib, abra `DirectorySearcher.cs`. Cambie los valores del miembro de clase para que reflejen los valores especificados en el Portal de Azure. El código hará referencia a estos valores siempre que use ADAL.
    -	`tenant` es el dominio del inquilino de Azure AD, por ejemplo, contoso.onmicrosoft.com.
    -	`clientId` es el identificador de cliente de la aplicación que copió del portal.
    - `returnUri` es el valor redirectUri que especificó en el portal, por ejemplo, `http://DirectorySearcher`.

## *3. Uso de ADAL para obtener tokens de AAD*
*Casi* toda la lógica de autenticación de la aplicación se encuentra en `DirectorySearcher.SearchByAlias(...)`. Todo lo necesario en los proyectos específicos de la plataforma es pasar un parámetro contextual al PCL `DirectorySearcher`.

- En primer lugar, abra `DirectorySearcher.cs` y agregue un nuevo parámetro para el `SearchByAlias(...)` método. `IPlatformParameters` es el parámetro contextuales que encapsula los objetos específicos de la plataforma que ADAL necesita para realizar la autenticación.

```C#
public static async Task<List<User>> SearchByAlias(string alias, IPlatformParameters parent)
{
```

-	A continuación, inicialice `AuthenticationContext`: la clase principal de ADAL. Este es el lugar en el que pasa a ADAL las coordenadas que necesita para comunicarse con Azure AD. A continuación, llame a `AcquireTokenAsync(...)`, que acepta el objeto `IPlatformParameters` y que invocará el flujo de autenticación necesario para devolver un token a la aplicación.

```C#
...
AuthenticationResult authResult = null;

try
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    authResult = await authContext.AcquireTokenAsync(graphResourceUri, clientId, returnUri, parent);
}
...
```
- `AcquireTokenAsync(...)` primero intentará devolver un token al recurso solicitado (Graph API en este caso) sin solicitar al usuario que escriba sus credenciales (mediante el almacenamiento en caché o la actualización de tokens antiguos). Solo si es necesario, mostrará al usuario la página de inicio de sesión de Azure AD antes de adquirir el token solicitado.


- También puede adjuntar el token de acceso a la solicitud de Graph API en el encabezado de autorización:

```C#
...
request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
...
```

Eso es todo para el PCL `DirectorySearcher` y el código relacionado con la identidad de la aplicación. Todo lo que queda es llamar al método `SearchByAlias(...)` en cada vista de plataforma, y cuando se necesario, agregar código para la administración correcta del ciclo de vida de la interfaz de usuario.

####Android:
- En `MainActivity.cs`, agregue una llamada a `SearchByAlias(...)` en el administrador de clics de botones:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(searchTermText.Text, new PlatformParameters(this));
```
- También necesitará reemplazar el método de ciclo de vida `OnActivityResult` para volver a enviar redirecciones de autenticación al método apropiado. ADAL proporciona un método auxiliar para esto en Android:

```C#
...
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
}
...
```

####Escritorio de Windows:
- En `MainWindow.xaml.cs`, simplemente realice una llamada a `SearchByAlias(...)` pasando un `WindowInteropHelper` en el objeto `PlatformParameters` de escritorio:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####iOS:
- En `DirSearchClient_iOSViewController.cs`, el objeto `PlatformParameters` de iOS simplemente toma una referencia para el Controlador de vista:

```C#
List<User> results = await DirectorySearcher.SearchByAlias(
  SearchTermText.Text,
  new PlatformParameters(PromptBehavior.Auto, this.Handle));
```

####Tienda Windows
- En Tienda Windows, abra `MainPage.xaml.cs` e implemente el método `Search` , que utiliza un método auxiliar en un proyecto compartido para actualizar la interfaz de usuario según sea necesario.

```C#
await UnivDirectoryHelper.Search(
  sender, e,
  SearchResults,
  SearchTermText,
  StatusResult,
  new PlatformParameters(PromptBehavior.Auto, false));
```

####Windows Phone
- En Windows Phone, abra `MainPage.xaml.cs` e implemente el método `Search` , que utiliza el mismo método auxiliar en un proyecto compartido para actualizar la interfaz de usuario.

```C#
await UnivDirectoryHelper.Search(
  sender, e,
  SearchResults,
  SearchTermText,
  StatusResult,
  new PlatformParameters());
```

¡Enhorabuena! Ahora dispone de una aplicación de Xamarin en funcionamiento que tiene la capacidad de autenticar usuarios y llamar de forma segura a las API de Web mediante OAuth 2.0 en cinco plataformas distintas. Si no lo ha hecho ya, ahora es el momento de completar el inquilino con algunos usuarios. Ejecute la aplicación DirectorySearcher e inicie sesión con uno de esos usuarios. Busque otros usuarios según su UPN.

ADAL facilita la incorporación de características comunes de identidad en la aplicación. Hace el trabajo sucio por usted: administración en caché, compatibilidad con protocolo OAuth, presentación del usuario con una interfaz de usuario de inicio de sesión, actualización de tokens expirados, etc. Todo lo que necesita saber es una única llamada de API, `authContext.AcquireToken*(…)`.

Como referencia, se proporciona el ejemplo finalizado (sin sus valores de configuración) [aquí](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-DotNet/archive/complete.zip). Ahora puede trasladarse a escenarios de identidad adicionales. Es posible que desee probar:

[Protección de una API Web .NET con Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_0128_2016-->