<properties 
	pageTitle="Aplicación de API personalizada ASP.NET de conector SaaS en el Servicio de aplicaciones de Azure" 
	description="Obtenga información sobre cómo escribir código que se conecte a una plataforma de SaaS desde una aplicación de API y cómo llamar a la aplicación de API desde un cliente. NET." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Conexión a una plataforma de SaaS desde una aplicación de API de ASP.NET en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Información general

Este tutorial muestra cómo codificar y configurar una [aplicación de API](app-service-api-apps-why-best-platform.md) que se conecte a una [plataforma de Software como-servicio (SaaS)](../app-service/app-service-authentication-overview.md#obotosaas) con el [SDK de Aplicaciones de API de Servicio de aplicaciones para .NET](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/). En el tutorial también se muestra cómo llamar a la aplicación de API desde un cliente de .NET mediante el [SDK de Servicio de aplicaciones para .NET](http://www.nuget.org/packages/Microsoft.Azure.AppService). Al final del tutorial tendrá un cliente de aplicación de consola .NET que llama a una aplicación de API de .NET en ejecución en el Servicio de aplicaciones de Azure. La aplicación de API llama a la API de Dropbox y devuelve una lista de archivos y carpetas de la cuenta de Dropbox del usuario.

Como alternativa a escribir código que llame a una API de SaaS directamente desde una aplicación de API personalizada, puede llamar a una [aplicación de API de conector](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md) previamente empaquetado. Para obtener información sobre cómo hacerlo, vea [Implementación y configuración de una aplicación de API de conector SaaS](app-service-api-connnect-your-app-to-saas-connector.md).

Este tutorial le guiará por los siguientes pasos:

* Creación de un proyecto de aplicación de API en Visual Studio. 
* Configuración del archivo *apiapp.json* para habilitar la conexión de la aplicación de API con el servicio Dropbox.
* Incorporación del código que llama a Dropbox y devuelve los resultados.
* Creación de una aplicación de API en Azure.
* Implementación del proyecto en la aplicación de API.
* Configuración de la aplicación de API.
* Configurar la puerta de enlace.
* Creación de un cliente de prueba.
* Ejecución del cliente de prueba.

## Requisitos previos

En el tutorial se parte de estos supuestos:

* Ha completado los tutoriales [Creación de una aplicación de API](app-service-dotnet-create-api-app.md) e [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md).
  
* Tiene conocimientos básicos de la arquitectura de la puerta de enlace del Servicio de aplicaciones de Azure para autenticación, tal y como se presenta en [Autenticación para aplicaciones de API y aplicaciones móviles](app-service-authentication-overview.md).

* Sabe cómo trabajar con Aplicaciones de API en el Portal de vista previa de Azure, tal como se explica en [Navegación a las hojas de puerta de enlace y aplicación de API](app-service-api-manage-in-portal.md#navigate).

## Creación del proyecto de aplicación de API
 
Cuando las instrucciones le indiquen que escriba un nombre para el proyecto, escriba *SimpleDropbox*.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

## Configuración del archivo *apiapp.json*

Para que una aplicación de API realice llamadas salientes a una plataforma de SaaS, se debe especificar la plataforma de SaaS en el archivo *apiapp.json*.

1. Abra el archivo *apiapp.json* y agregue una propiedad `authentication` tal y como se muestra a continuación (también tendrá que agregar una coma después de la propiedad anterior):

		"authentication": [
		  {
		    "type": "dropbox"
		  }
		]

	El archivo apiapp.json completo será similar a este ejemplo:

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "SimpleDropBox",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "SimpleDropBox",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    },
		    "authentication": [
		      {
		        "type": "dropbox"
		      }
		    ]
		}

2. Guarde el archivo .

El establecimiento de la propiedad `authentication` tiene un par de consecuencias:

* Hace que el portal muestre la interfaz de usuario del portal en la hoja de aplicación de API que le permite escribir los valores de secreto de cliente e identificador de cliente de la plataforma de SaaS.

	![](./media/app-service-api-dotnet-connect-to-saas/authblade.png)

* Habilita la aplicación de API para que recupere el token de acceso del proveedor de SaaS desde la puerta de enlace que se usa al llamar a la API del proveedor de SaaS.

La propiedad `authentication` es una matriz, pero esta versión preliminar no admite la especificación de varios proveedores.

Para obtener una lista de las plataformas compatibles, vea [Obtención del consentimiento del usuario para tener acceso a otras plataformas de SaaS](../app-service/app-service-authentication-overview.md#obotosaas).

También puede especificar los ámbitos, como en este ejemplo:

		"authentication": [
		  {
		    "type": "google",
		    "scopes": ["https://www.googleapis.com/auth/userinfo.email", "https://www.googleapis.com/auth/userinfo.profile"]
		  }
		]

Los ámbitos disponibles los define cada proveedor de SaaS y pueden encontrarse en el Portal para desarrolladores del proveedor.

## Incorporación del código que llama a Dropbox

1. Instale el paquete [DropboxRestAPI](https://www.nuget.org/packages/DropboxRestAPI) de NuGet en el proyecto SimpleDropbox.

	* En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet > Consola del Administrador de paquetes**.

	* En la ventana **Consola del Administrador de paquetes**, escriba este comando:
	 
			install-package DropboxRestAPI  

1. Abra *Controllers\\ValuesController.cs* reemplace todo el código en el archivo por el código siguiente.

		using DropboxRestAPI;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace SimpleDropBox2.Controllers
		{
		    public class ValuesController : ApiController
		    {
		        public async Task<IEnumerable<string>> Get()
		        {
		            // Retrieve the token from the gateway
		            var runtime = Runtime.FromAppSettings(Request);
		            var dropboxTokenResult = await runtime.CurrentUser.GetRawTokenAsync("dropbox");
		
		            // Create a Dropbox client object that will send the token
		            // with REST API calls to Dropbox.
		            var dropboxClient = new Client(
		                new Options
		                {
		                    AccessToken = dropboxTokenResult.Properties["AccessToken"]
		                }
		            );
		
		            // Call the Dropbox API
		            var metadata = await dropboxClient.Core.Metadata.MetadataAsync("/");
		
		            // Return a list of files and folders.
		            return metadata.contents.Select(md => md.path);
		        }
		    }
		}

	Antes de que el cliente llame a este método, el usuario debe iniciar sesión en Dropbox y conceder consentimiento a la aplicación de API para que tenga acceso a la cuenta de Dropbox del usuario. Dropbox confirma ese consentimiento proporcionando un token de acceso a la puerta de enlace del Servicio de aplicaciones. Este código recupera el token de la puerta de enlace y lo incluye en una llamada a la API de Dropbox, tal como se muestra en los pasos 6 y 7 del diagrama siguiente.

	![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

2. Compile el proyecto.

## Creación de una aplicación de API en Azure

En esta sección, use el asistente para **Publicación web** de Visual Studio a fin de crear una nueva aplicación de API en Azure. Cuando las instrucciones le indiquen que escriba un nombre para la aplicación de API, escriba *SimpleDropbox*.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## Implementación del código

Use el mismo asistente para **Publicación web** a fin de implementar el código en la nueva aplicación de API.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Configuración de autenticación para llamadas entrantes

Para que el Servicio de aplicaciones de Azure permita las llamadas salientes autenticadas en la aplicación de API, la aplicación de API debe exigir también que las llamadas entrantes procedan de usuarios autenticados. Esto no es un requisito general de OAuth 2.0, pero es un requisito de la arquitectura de la puerta de enlace del Servicio de aplicaciones tal como está implementada actualmente.

Las capturas de pantalla de esta sección muestran una aplicación de API de ContactsList, pero el proceso es el mismo para la aplicación de API de SimpleDropbox que se crea en este tutorial.

### Configuración de la aplicación de API para que exija que las llamadas entrantes estén autenticadas

[AZURE.INCLUDE [app-service-api-config-auth](../../includes/app-service-api-config-auth.md)]

### Configuración de un proveedor de identidades en la puerta de enlace

[AZURE.INCLUDE [app-service-api-gateway-config-auth](../../includes/app-service-api-gateway-config-auth.md)]

## Configuración de autenticación para llamadas salientes

A fin de habilitar la aplicación de API para que llame a la API de Dropbox, tiene que intercambiar configuraciones entre la aplicación de API y una aplicación de Dropbox que cree en el sitio para desarrolladores de Dropbox.

### Creación de una aplicación de Dropbox en el sitio Dropbox.com

[AZURE.INCLUDE [app-service-api-create-dropbox-app](../../includes/app-service-api-create-dropbox-app.md)]

### Intercambio de configuraciones entre Dropbox y la aplicación de API

Los pasos siguientes hacen referencia a una aplicación de API de conector de Dropbox, pero los procedimientos y la interfaz de usuario son los mismos para la aplicación de API SimpleDropbox que se crea en este tutorial.

> **Nota:** Si no ve los campos para el secreto de cliente y el identificador de cliente en la hoja **Autenticación** de la aplicación de API SimpleDropbox, tal y como se muestra en la captura de pantalla, asegúrese de que reinició la puerta de enlace como se indica después de implementar el proyecto de aplicación de API en la aplicación de API. El valor "dropbox" de la propiedad `authentication` del archivo *apiapp.json* que implementó anteriormente es lo que activa el portal para mostrar estos campos.

[AZURE.INCLUDE [app-service-api-exchange-dropbox-settings](../../includes/app-service-api-exchange-dropbox-settings.md)]

## Creación de un cliente de prueba

En esta sección creará un proyecto de aplicación de consola que usa el código de cliente generado por Visual Studio para llamar a la aplicación de API SimpleDropbox. La aplicación de consola crea una instancia de un control de explorador de Windows Forms para administrar la interacción del usuario con la puerta de enlace y las páginas web de inicio de sesión de Dropbox.

### Creación del proyecto

1. En Visual Studio, cree un proyecto de aplicación de consola y asígnele el nombre *SimpleDropboxTest*.

2. Establezca una referencia a System.Windows.Forms.
 
	* En el **Explorador de soluciones**, haga clic con el botón derecho en **Referencias** y después haga clic en **Agregar referencia**.

	* Active la casilla situada a la izquierda de **System.Windows.Forms** y haga clic en **Aceptar**.
	 
	![](./media/app-service-api-dotnet-connect-to-saas/setref.png)

	La aplicación de consola usará el ensamblado de Windows Forms para crear una instancia de un control de explorador cuando sea necesario permitir al usuario que inicie sesión en la puerta de enlace y en Dropbox.

### Incorporación del código de cliente generado

Las capturas de pantalla de esta sección muestran un proyecto y una aplicación de API de ContactsList, pero para este tutorial, seleccione el proyecto de SimpleDropboxTest y la aplicación de API de SimpleDropbox.

[AZURE.INCLUDE [app-service-api-dotnet-add-generated-client](../../includes/app-service-api-dotnet-add-generated-client.md)]

### Agregar código para llamar a la aplicación de API

3. Abra el archivo *Program.cs* y sustituya el código existente por el siguiente.
		
		using Microsoft.Azure.AppService;
		using Newtonsoft.Json;
		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Windows.Forms;
		
		namespace SimpleDropboxTest
		{
		    enum Step
		    {
		        GatewayLogin,
		        GetSaaSConsentLink,
		        GetUserConsent
		    }
		
		    class Program
		    {
		        private const string GATEWAY_URL = @"{gateway url}";
		        private const string URL_TOKEN = "#token=";
		        private const string SAAS_URL = "dropbox.com";
		        private static Form frm = new Form();
		        private static string responseURL = "";
		        private static Step step;
		        [STAThread]
		        static void Main(string[] args)
		        {
		            // Create the web browser control
		            WebBrowser browser = new WebBrowser();
		            browser.Dock = DockStyle.Fill;
		            browser.Navigated += CheckResponseURL;
		            frm.Controls.Add(browser);
		            frm.Width = 640;
		            frm.Height = 480;
		
		            // Create the gateway and API app clients.
		            AppServiceClient appServiceClient = new AppServiceClient(GATEWAY_URL);
		            SimpleDropbox simpleDropboxClient = appServiceClient.CreateSimpleDropbox();
		
		            // Navigate browser to gateway login URL for configured identity provider.
		            // Identity provider for this example is Azure Active Directory.
		            step = Step.GatewayLogin;
		            browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));
		            frm.ShowDialog();
		            Console.WriteLine("Logged in to gateway, response URL=" + responseURL);
		
		            // Get user ID and Zumo token from return URL, then call 
		            // the gateway URL to log in the gateway client.
		            var encodedJson = responseURL.Substring(responseURL.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		            var decodedJson = Uri.UnescapeDataString(encodedJson);
		            var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		            string userId = result.user.userId;
		            string userToken = result.authenticationToken;
		            appServiceClient.SetCurrentUser(userId, userToken);
		
		            // Call gateway API to get consent link URL for target SaaS platform.
		            // SaaS platform for this example is Dropbox.
		            // See the tutorial for an explanation of
		            // the redirectURL parameter for GetConsentLinkAsync
		            var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;
		            Console.WriteLine("\nGot gateway consent link, URL=" + gatewayConsentLink);
		
		            // Navigate browser to consent link URL returned from gateway.
		            // Response URL will be the SaaS logon link
		            step = Step.GetSaaSConsentLink;
		            browser.Navigate(gatewayConsentLink);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot SaaS consent link response, URL=" + responseURL);
		
		            // Navigate browser to login/consent link for SaaS platform.
		            step = Step.GetUserConsent;
		            browser.Navigate(responseURL);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot Dropbox login response, URL=" + responseURL);
		
		            Console.WriteLine("\nResponse from SaaS Provider");
		            var response = simpleDropboxClient.Values.Get();
		            foreach (string s in response)
		            {
		                Console.WriteLine(s);
		            }
		            Console.Read();
		        }
		
		        static void CheckResponseURL(object sender, WebBrowserNavigatedEventArgs e)
		        {
		            if ((step == Step.GatewayLogin && e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		                || (step == Step.GetSaaSConsentLink && e.Url.AbsoluteUri.IndexOf(SAAS_URL) > -1)
		                || (step == Step.GetUserConsent && e.Url.AbsoluteUri.IndexOf(GATEWAY_URL) > -1))
		            {
		                responseURL = e.Url.AbsoluteUri;
		                frm.Close();
		            }
		        }
		
		    }
		}

1. Reemplace {url de puerta de enlace} por la dirección URL real de la puerta de enlace.
 
	La URL de la puerta de enlace puede obtenerse en la hoja **Puerta de enlace** del portal.

	![](./media/app-service-api-dotnet-connect-to-saas/gwurl.png)

		private const string GATEWAY_URL = @"https://sd1aeb4ae60b7cb4f3d966dfa43b660.azurewebsites.net";

	> **Importante**: asegúrese de que la dirección URL de la puerta de enlace comienza por `https://`, no por `http://`. **Si copia http:// en el portal, tendrá que cambiarlo a https:// al pegarlo en el código.**

### Explicación del código

Esta aplicación de consola está diseñada para que use una cantidad mínima de código para ilustrar los pasos que debe recorrer una aplicación cliente. Una aplicación de producción normalmente no sería una aplicación de consola y podría implementar el registro y control de errores.

Le presentamos una visión general de lo que hace el código:

* Abre un explorador a en la dirección URL de inicio de sesión de la puerta de enlace para el proveedor de identidades configurado, en este caso Azure Active Directory. 
	 
* Controla la dirección URL de respuesta prevista después de que el usuario inicie sesión: extraer el identificador del usuario y el token Zumo, y proporcionarlos al objeto cliente de Servicio de aplicaciones.

* Usa el objeto cliente de Servicio de aplicaciones para recuperar una dirección URL de puerta de enlace que se redirigirá al vínculo Dropbox para inicio de sesión y consentimiento. Paso 1 del diagrama.

* Abre un explorador en la dirección URL de consentimiento de la puerta de enlace. El explorador se redirige al vínculo Dropbox para inicio de sesión y consentimiento. Paso 2 del diagrama.
	 
* Cierra el explorador después de que el usuario inicia sesión y da su consentimiento a Dropbox.com. Paso 3 del diagrama.
 
* Llama a la aplicación de API. Paso 5 del diagrama. (El paso 4 se produce en segundo plano entre Dropbox.com y la puerta de enlace, los pasos 6 y 7 se realizan en la aplicación de API, no en el cliente.)

![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

Notas adicionales:

* El atributo `STAThread` del método `Main` es necesario para el control del explorador web y no se relaciona con la configuración ni con la llamada a la aplicación de API.

* La dirección URL de inicio de sesión de la puerta de enlace que se muestra finaliza en `/aad` para Azure Active Directory.

		browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));

	Estos son los valores que se usarán para los demás proveedores:
	* "microsoftaccount"
	* "facebook"
	* "twitter"
	* "google"
<br/><br/>

* El segundo parámetro del método `GetConsentLinkAsync()` es la URL de devolución de llamada a la que se redirige el servidor de consentimiento después de que el usuario inicia sesión en Dropbox y da su consentimiento de acceso a la cuenta del usuario.

		var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;

	Para este parámetro, normalmente se especificaría la siguiente página web a la que el usuario debe ir en la aplicación cliente. Puesto que este código de demostración está en una aplicación de consola, no hay ninguna página de aplicación a la que ir y el código especifica la dirección URL de la puerta de enlace como una página de aterrizaje conveniente.

	La aplicación cliente debe comprobar que se redirige a esta dirección URL y que no hay ningún mensaje de error. Si el proceso de inicio de sesión/consentimiento no se realiza, es posible que la dirección URL de redireccionamiento contenga un mensaje de error en la cadena de consulta. Para más información, vea la sección [Solución de problemas](#troubleshooting).

## Prueba

1. Ejecute la aplicación de consola SimpleDropboxTest.

2. En la primera página de inicio de sesión, conéctese con sus credenciales de Azure Active Directory (o las credenciales de otro proveedor de identidades, como Google o Twitter, si eso es lo que configuró en la puerta de enlace).

	![](./media/app-service-api-dotnet-connect-to-saas/aadlogon.png)

3. En la página de inicio de sesión Dropbox.com, conéctese con sus credenciales de Dropbox.

	![](./media/app-service-api-dotnet-connect-to-saas/dblogon.png)

4. En la página de consentimiento de Dropbox, dé permiso a la aplicación para que tenga acceso a sus datos.

	![](./media/app-service-api-dotnet-connect-to-saas/dbconsent.png)

	La aplicación de consola llama después a la aplicación de API y esta devuelve una lista de los archivos que hay en su cuenta de Dropbox.

	![](./media/app-service-api-dotnet-connect-to-saas/testclient.png)

## Solución de problemas

En esta sección se incluyen los temas siguientes:

* [Error HTTP 405 después del inicio de sesión en la puerta de enlace](#405)
* [Error HTTP 400 en lugar de la página de inicio de sesión de Dropbox](#400)
* [Error HTTP 403 al llamar a la aplicación de API](#403)

### <a id="405"></a>Error HTTP 405 después del inicio de sesión en la puerta de enlace

Si recibe el error HTTP 405 cuando el código llama a GetConsentLinkAsync, compruebe que usó https://, no http://, en la dirección URL de la puerta de enlace.

![](./media/app-service-api-dotnet-connect-to-saas/http405.png)

El error 405 de método no permitido se recibe porque el cliente intenta realizar una solicitud HTTP POST sin SSL, la puerta de enlace se redirige a **https://* y la redirección provoca una solicitud GET. La dirección URL para la recuperación de un vínculo de consentimiento solo acepta solicitudes POST.

### <a id="400"></a> Error HTTP 400 en lugar de la página de inicio de sesión de Dropbox

Asegúrese de que tiene el **identificador de cliente** correcto en hoja **Autenticación** de la aplicación de API y de que no hay espacios iniciales ni finales.

### <a id="403"></a> Error HTTP 403 al llamar a la aplicación de API

* Asegúrese de que el **Nivel de acceso** de la aplicación de API está establecido en **Público (autenticado)** y no en **Interno**.

* Asegúrese de que tiene el **secreto de cliente** correcto en hoja **Autenticación** de la aplicación de API y de que no haya espacios iniciales ni finales.

Es posible que la URL de redirección tras el inicio de sesión tenga un aspecto similar a este ejemplo:

	https://sd1aeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/?error=RmFpbGVkIHRvIGV4Y2hhbmdlIGNvZGUgZm9yIHRva2VuLiBEZXRhaWxzOiB7ImVycm9yX2Rlc2NyaXB0aW9uIjogIkludmFsaWQgY2xpZW50X2lkIG9yIGNsaWVudF9zZWNyZXQiLCAiZXJyb3IiOiAiaW52YWxpZF9jbGllbnQifQ%3d%3d

Si quita %3d%3d del final del valor de cadena de consulta `error`, esta es una cadena válida codificada en base64. Descodifique la cadena para obtener el mensaje de error:

	Failed to exchange code for token. Details: {"error_description": "Invalid client_id or client_secret", "error": "invalid_client"}

## Pasos siguientes

Vio como se codifica y configura una aplicación de API que se conecta a una plataforma de SaaS. Para obtener vínculos a otros tutoriales sobre cómo controlar la autenticación de aplicaciones de API, vea [Autenticación para aplicaciones de API y aplicaciones móviles: pasos siguientes](../app-service/app-service-authentication-overview.md#next-steps).

[Portal de vista previa de Azure]: https://portal.azure.com/
[Portal de Azure]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0114_2016-->