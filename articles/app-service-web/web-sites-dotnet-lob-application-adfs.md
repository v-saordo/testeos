<properties 
	pageTitle="Crear una aplicación web de .NET MVC en Servicio de aplicaciones de Azure con autenticación de AD FS" 
	description="Aprenda a crear una aplicación de línea de negocio de ASP.NET MVC en Aplicaciones web del Servicio de aplicaciones de Azure que se autentica con STS local. En este tutorial se dirige a AD FS como STS local." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="02/26/2016" 
	ms.author="cephalin"/>

# Crear una aplicación web de .NET MVC en Servicio de aplicaciones de Azure con autenticación de AD FS

En este artículo, aprenderá a crear una aplicación de línea de negocio de ASP.NET MVC en [Aplicaciones web del Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) mediante el uso de [Servicios de federación de Active Directory (ADFS)](http://technet.microsoft.com/library/hh831502.aspx) locales como el proveedor de identidades. Este escenario puede funcionar cuando desea crear aplicaciones de línea de negocio en Aplicaciones web del Servicio de aplicaciones de Azure, pero su organización requiere que todos los datos se almacenen en el sitio.

>[AZURE.NOTE] Para obtener información general de las distintas opciones de autorización y autenticación empresarial para Aplicaciones web del Servicio de aplicaciones de Azure, consulte [Usar Active Directory para la autenticación en el Servicio de aplicaciones de Azure](web-sites-authentication-authorization.md).

<a name="bkmk_build"></a>
## Lo que va a crear ##

Creará una aplicación ASP.NET básica en Aplicaciones web del Servicio de aplicaciones de Azure con las siguientes características:

- Autentica a los usuarios con AD FS
- Usa `[Authorize]` para autorizar a los usuarios para distintas acciones
- Configuración estática tanto para depurar en Visual Studio como para publicar en Aplicaciones web del Servicio de aplicaciones (configurar una vez, depurar y publicar en cualquier momento)  

<a name="bkmk_need"></a>
## Qué necesita ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de suscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Necesita lo siguiente para completar este tutorial:

- Una implementación de AD FS local (para obtener un tutorial de un extremo a otro del entorno de pruebas que uso, vea [Entorno de pruebas: STS independiente con AD FS en VM de Azure (solo para prueba)](TODO))
- Permisos para crear relaciones de confianza para usuarios autenticados en la administración de AD FS
- Visual Studio 2013
- [SDK de Azure 2.5.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) o posterior

<a name="bkmk_sample"></a>
## Usar la aplicación de ejemplo para la plantilla de línea de negocio ##

La aplicación de muestra de este tutorial, [WebApp-WSFederation-DotNet)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet), la crea el equipo de Azure Active Directory. Debido a que AD FS es compatible con WS-Federation, puede usarla como una plantilla para crear nuevas aplicaciones de línea de negocio con facilidad. Tiene las siguientes características:

- Usa [WS-Federation](http://msdn.microsoft.com/library/bb498017.aspx) para autenticar con una implementación de AD FS local
- Funcionalidad de inicio y de cierre de sesión
- Usa [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (en lugar de Windows Identity Foundation, es decir, WIF), que es el futuro de ASP.NET y mucho más simple de configurar que WIF para la autenticación y autorización

<a name="bkmk_setup"></a>
## Configurar la aplicación de muestra ##

2.	Clone o descargue la solución de muestra de [WebApp-WSFederation-DotNet](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) en su directorio local.

	> [AZURE.NOTE] Las instrucciones de [README.md](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet/blob/master/README.md) muestran cómo configurar la aplicación con Azure Active Directory, pero en este tutorial lo configurará con AD FS, por lo que, en su lugar, debe seguir los pasos siguientes.

3.	Abra la solución y, a continuación, Controllers\\AccountController.cs en el **Explorador de soluciones**.

	Verá que el código simplemente emite un desafío de autenticación para autenticar al usuario con WS-Federation. Toda la autenticación se configura en App\_Start\\Startup.Auth.cs.

4.  Abra App\_Start\\Startup.Auth.cs. En el método `ConfigureAuth`, observe la línea:

        app.UseWsFederationAuthentication(
            new WsFederationAuthenticationOptions
            {
                Wtrealm = realm,
                MetadataAddress = metadata                                      
            });

	En el mundo de OWIN, realmente es lo mínimo que se necesita para configurar la autenticación de WS-Federation. Esto es mucho más sencillo y elegante que WIF, donde Web.config se inyecta con XML por todas partes. La única información que necesita es el identificador del usuario de confianza y la dirección URL del archivo de metadatos del servicio AD FS. Este es un ejemplo:

	-	Identificador del usuario de confianza: `https://contoso.com/MyLOBApp`
	-	Dirección de metadatos: `http://adfs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`

5.	En App\_Start\\Startup.Auth.cs, cambie las definiciones de cadena estática como se resalta a continuación:
	<pre class="prettyprint">
	private static string realm = ConfigurationManager.AppSettings["ida:<mark>RPIdentifier</mark>"];
	<mark><del>private static string aadInstance = ConfigurationManager.AppSettings["ida:AADInstance"];</del></mark>
	<mark><del>private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];</del></mark>
	<mark><del>private static string metadata = string.Format("{0}/{1}/federationmetadata/2007-06/federationmetadata.xml", aadInstance, tenant);</del></mark>
	<mark>private static string metadata = string.Format("https://{0}/federationmetadata/2007-06/federationmetadata.xml", ConfigurationManager.AppSettings["ida:ADFS"]);</mark>
	
	<mark><del>string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant);</del></mark>
	</pre>

6.	Ahora realizará los cambios correspondientes en Web.config. Abra Web.config y modifique la configuración de la aplicación como se resalta a continuación:
	<pre class="prettyprint">
	&lt;appSettings>
	  &lt;add key="webpages:Version" value="3.0.0.0" />
	  &lt;add key="webpages:Enabled" value="false" />
	  &lt;add key="ClientValidationEnabled" value="true" />
	  &lt;add key="UnobtrusiveJavaScriptEnabled" value="true" />
	  <mark><del>&lt;add key="ida:Wtrealm" value="[Enter the App ID URI of WebApp-WSFederation-DotNet https://contoso.onmicrosoft.com/WebApp-WSFederation-DotNet]" /></del></mark>
	  <mark><del>&lt;add key="ida:AADInstance" value="https://login.windows.net" /></del></mark>
	  <mark><del>&lt;add key="ida:Tenant" value="[Enter tenant name, e.g. contoso.onmicrosoft.com]" /></del></mark>
	  <mark>&lt;add key="ida:RPIdentifier" value="[Enter the relying party identifier as configured in AD FS, e.g. https://localhost:44320/]" /></mark>
	  <mark>&lt;add key="ida:ADFS" value="[Enter the FQDN of AD FS service, e.g. adfs.contoso.com]" /></mark>
	
	&lt;/appSettings>
	</pre>

	Rellene los valores de clave en función de su entorno respectivo.

7.	Compile la aplicación para asegurarse de que no hay ningún error.

Eso es todo. Ahora la aplicación de muestra está lista para trabajar con AD FS. Todavía tendrá que configurar una relación de confianza para usuario autenticado con esta aplicación en AD FS más tarde.

<a name="bkmk_deploy"></a>
## Implemente la aplicación de muestra en Aplicaciones web del Servicio de aplicaciones de Azure

Aquí publicará la aplicación en una aplicación web en Aplicaciones web del Servicio de aplicaciones, a la vez que conserva el entorno de depuración. Tenga en cuenta que va a publicar la aplicación antes de que tenga una relación de confianza para usuario autenticado con AD FS, por lo que la autenticación no funciona todavía. Sin embargo, si lo hace ahora puede tener la dirección URL de la aplicación web que también usará para configurar la confianza para usuario autenticado más tarde.

1. Haga clic con el botón derecho en el proyecto y seleccione **Publicar**.

	![](./media/web-sites-dotnet-lob-application-adfs/01-publish-website.png)

2. Seleccione **Servicio de aplicaciones de Microsoft Azure**.
3. Si no ha iniciado sesión en Azure, haga clic en **Iniciar sesión** y use la cuenta de Microsoft de su suscripción de Azure para iniciar sesión.
4. Cuando haya iniciado sesión, haga clic en **Nuevo** para crear una nueva aplicación web.
5. Rellene todos los campos obligatorios. Se conectará a datos locales más tarde, por lo que no creará una base de datos para esta aplicación web.

	![](./media/web-sites-dotnet-lob-application-adfs/02-create-website.png)

6. Haga clic en **Crear**. Una vez que se haya creado la aplicación web, se abrirá el cuadro de diálogo Publicación web.
7. En **Dirección URL de destino**, cambie **http** a **https**. Copie toda la dirección URL en un editor de texto. La usará más adelante. A continuación, haga clic en **Publicar**.

	![](./media/web-sites-dotnet-lob-application-adfs/03-destination-url.png)

11. En Visual Studio, abra **Web.Release.config** en el proyecto. Introduzca el siguiente código XML en la etiqueta `<configuration>` y reemplace el valor de la clave por la dirección URL de la aplicación web de publicación.
	<pre class="prettyprint">
	&lt;appSettings>
	   &lt;add key="ida:RPIdentifier" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
	&lt;/appSettings></pre>

Cuando haya terminado, tendrá dos identificadores de usuario de confianza configurados en el proyecto, uno para el entorno de depuración en Visual Studio y otro para el sitio web publicado en Azure. Configurará una relación de confianza para usuario autenticado para cada uno de los dos entornos de AD FS. Durante la depuración, la configuración de la aplicación del archivo Web.config se usa para que su configuración de **Debug** funcione con AD FS, y cuando se publique (de forma predeterminada, se publica la configuración de **Release**), se carga un archivo Web.config transformado que incorpora los cambios de la configuración de la aplicación en Web.Release.config.

Si desea asociar la aplicación web publicada en Azure al depurador (es decir, debe cargar los símbolos de depuración del código en el sitio web publicado), puede crear un clon de la configuración de depuración para la depuración de Azure, pero con su propia transformación de Web.config personalizada (por ejemplo, Web.AzureDebug.config) que usa la configuración de la aplicación de aplicación de Web.Release.config. Esto le permite mantener una configuración estática en los distintos entornos.

<a name="bkmk_rptrusts"></a>
## Configurar las relaciones de confianza para usuarios autenticados en la administración de AD FS ##

Ahora debe configurar una relación de confianza para usuario autenticado en la administración de AD FS para poder usar su aplicación de muestra y que se pueda autenticar realmente con AD FS. Deberá configurar dos relaciones de confianza para usuarios autenticados independientes, una para su entorno de depuración y otra para su aplicación web publicada.

> [AZURE.NOTE] Asegúrese de repetir los pasos siguientes para ambos entornos.

4.	En su servidor de AD FS, inicie sesión con credenciales que tengan derechos de administración para AD FS.
5.	Abra Administración de AD FS. Haga clic con el botón derecho en **AD FS\\Trusted Relationships\\Relying Party Trusts** y seleccione **Agregar relación de confianza para usuario de confianza**.

	![](./media/web-sites-dotnet-lob-application-adfs/1-add-rptrust.png)

5.	En la página **Seleccionar origen de datos**, seleccione **Especificar datos acerca del usuario de confianza manualmente**.

	![](./media/web-sites-dotnet-lob-application-adfs/2-enter-rp-manually.png)

6.	En la página **Especificar nombre para mostrar**, escriba un nombre para mostrar para la aplicación y haga clic en **Siguiente**.
7.	En la página **Elegir protocolo**, haga clic en **Siguiente**.
8.	En la página **Configurar certificado**, haga clic en **Siguiente**.

	> [AZURE.NOTE] Puesto que ya debería estar usando HTTPS, los tokens cifrados son opcionales. Si desea realmente cifrar tokens desde AD FS en este página, debe agregar también lógica de descifrado de tokens en su código. Para obtener más información, consulte [Configurar manualmente middleware de WS-Federation de OWIN y aceptar tokens cifrados](http://chris.59north.com/post/2014/08/21/Manually-configuring-OWIN-WS-Federation-middleware-and-accepting-encrypted-tokens.aspx).
  
5.	Antes de pasar al siguiente paso, necesita algo de información de su proyecto de Visual Studio. En las propiedades del proyecto, observe la **Dirección URL de SSL** de la aplicación.

	![](./media/web-sites-dotnet-lob-application-adfs/3-ssl-url.png)

6.	De nuevo en la administración de AD FS, en la página **Configurar URL** del **Asistente para agregar relación de confianza para usuario de confianza**, seleccione **Habilitar la compatibilidad para el protocolo WS-Federation Passive** y escriba la dirección URL de SSL del proyecto de Visual Studio que anotó en el paso anterior. A continuación, haga clic en **Siguiente**.

	![](./media/web-sites-dotnet-lob-application-adfs/4-configure-url.png)

	> [AZURE.NOTE] La dirección URL especifica dónde enviar al cliente después de que la autenticación se realice correctamente. Para el entorno de depuración, debe ser <code>https://localhost:&lt;port&gt;/</code>. En el caso de la aplicación web publicada, debe ser la dirección URL de la aplicación web.

7.	En la página **Configurar identificadores**, compruebe que la dirección URL de SSL del proyecto ya aparece en la lista y haga clic en **Siguiente**. Haga clic en **Siguiente** hasta el final del asistente con las selecciones predeterminadas.

	> [AZURE.NOTE] En App\_Start\\Startup.Auth.cs del proyecto de Visual Studio, este identificador se compara con el valor de <code>WsFederationAuthenticationOptions.Wtrealm</code> durante la autenticación federada. De forma predeterminada, se agrega la dirección URL de la aplicación del paso anterior como un identificador de usuario de confianza.

8.	Ahora ha terminado de configurar la aplicación de usuario de confianza para su proyecto en AD FS. A continuación, configurará esta aplicación para enviar las notificaciones necesarias para la aplicación. El cuadro de diálogo **Editar reglas de notificación** se abre de forma predeterminada al final del Asistente para que pueda empezar inmediatamente. Vamos a configurar al menos las siguientes notificaciones (con esquemas entre paréntesis):

	-	Nombre (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name): usado por ASP.NET para hidratar `User.Identity.Name`.
	-	Nombre principal del usuario (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn): se usa para identificar de manera única a los usuarios de la organización.
	-	Pertenencias a grupos como roles (http://schemas.microsoft.com/ws/2008/06/identity/claims/role): se pueden utilizar con la representación `[Authorize(Roles="role1, role2,...")]` para autorizar a los controladores/acciones. En realidad, puede que este no sea el enfoque de mayor rendimiento para la autorización de roles, especialmente si sus usuarios de AD pertenecen con regularidad a cientos de grupos de seguridad, lo que se traduce en cientos de notificaciones de rol en el token de SAML. Un método alternativo consiste en enviar una notificación de rol única condicionalmente en función de la pertenencia del usuario a un grupo concreto. Sin embargo, lo mantendremos sencillo para este tutorial.
	-	Id. de nombre (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier): se puede usar para la validación de antifalsificación. Para obtener más información sobre cómo hacerla funcionar con validación de antifalsificación, consulte la sección **Agregar funcionalidad de línea de negocio a la aplicación de ejemplo** de [Crear una aplicación web de .NET MVC en Servicio de aplicaciones de Azure con autenticación de Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md#bkmk_crud).

	> [AZURE.NOTE] Los tipos de notificación que necesita configurar para su aplicación dependen de las necesidades de su aplicación. Para ver la lista de notificaciones admitidas por las aplicaciones de Azure Active Directory (es decir, relaciones de confianza para usuarios autenticados), por ejemplo, consulte [Tipos de notificaciones y tokens admitidos](http://msdn.microsoft.com/library/azure/dn195587.aspx).

8.	En el cuadro de diálogo Editar reglas de notificación, haga clic en **Agregar regla**.
9.	Configure las notificaciones de nombre, UPN y rol, como se muestra a continuación y haga clic en **Finalizar**.

	![](./media/web-sites-dotnet-lob-application-adfs/5-ldap-claims.png)

	A continuación, creará una notificación de identificador de nombre transitorio mediante los pasos que se muestran en [Identificadores de nombres en aserciones de SAML](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx).

9.	Haga clic en **Agregar regla** de nuevo.
10.	Seleccione **Enviar notificaciones con una regla personalizada** y haga clic en **Siguiente**.
11.	Pegue el siguiente lenguaje de regla en el cuadro **Regla personalizada**, asigne el nombre **Por identificador de sesión** a la regla y haga clic en **Finalizar**.  
	<pre class="prettyprint">
	c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] &amp;&amp;
	c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant"]
		=> add(
			store = "_OpaqueIdStore",
			types = ("<mark>http://contoso.com/internal/sessionid</mark>"),
			query = "{0};{1};{2};{3};{4}",
			param = "useEntropy",
			param = c1.Value,
			param = c1.OriginalIssuer,
			param = "",
			param = c2.Value);
	</pre>

	Su regla personalizada debe tener un aspecto similar al siguiente:

	![](./media/web-sites-dotnet-lob-application-adfs/6-per-session-identifier.png)

9.	Haga clic en **Agregar regla** de nuevo.
10.	Seleccione **Transformar una notificación entrante** y haga clic en **Siguiente**.
11.	Configure la regla como se muestra a continuación (con el tipo de notificación que creó en la regla personalizada) y haga clic en **Finalizar**.

	![](./media/web-sites-dotnet-lob-application-adfs/7-transient-name-id.png)

	Para obtener información detallada sobre los pasos para la notificación de identificador de nombre transitorio anterior, consulte [Identificadores de nombres en aserciones de SAML](http://blogs.msdn.com/b/card/archive/2010/02/17/name-identifiers-in-saml-assertions.aspx).

12.	Haga clic en **Aplicar** en el cuadro de diálogo **Editar reglas de notificación**. Ahora debería tener un aspecto similar al de la siguiente captura de pantalla:

	![](./media/web-sites-dotnet-lob-application-adfs/8-all-claim-rules.png)

	> [AZURE.NOTE] Nuevamente, asegúrese de repetir estos pasos tanto para el entorno de depuración como para la aplicación web publicada.

<a name="bkmk_test"></a>
## Probar la autenticación federada para la aplicación

Está preparado para probar la lógica de autenticación de la aplicación con AD FS. En mi entorno de laboratorio de AD FS, tengo un usuario de prueba que pertenece a un grupo de prueba en Active Directory (AD).

![](./media/web-sites-dotnet-lob-application-adfs/10-test-user-and-group.png)

Para probar la autenticación en el depurador, todo lo que tiene que hacer ahora es escribir `F5`. Si desea probar la autenticación de prueba en la aplicación web publicada, navegue hasta la dirección URL.

Cuando se cargue la aplicación web, haga clic en **Iniciar sesión**. Ahora debería obtener un cuadro de diálogo de inicio de sesión o la página de inicio de sesión atendida por AD FS, en función del método de autenticación seleccionado por AD FS. Esto es lo que aparece en Internet Explorer 11.

![](./media/web-sites-dotnet-lob-application-adfs/9-test-debugging.png)

Cuando inicie sesión con un usuario en el dominio de AD de la implementación de AD FS, debería ver la página principal de nuevo con **Hello, <User Name>!** en la esquina. Esto es lo que vemos.

![](./media/web-sites-dotnet-lob-application-adfs/11-test-debugging-success.png)

Hasta ahora, ha llevado a cabo correctamente lo siguiente:

- La aplicación ha alcanzado AD FS correctamente y hay un identificador de usuario de confianza coincidente en la base de datos de AD FS
- AD FS ha autenticado correctamente un usuario de AD y le redirige de nuevo a la página principal de la aplicación
- AD FS ha enviado correctamente la notificación del nombre (http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name) a la aplicación, como se indica por el hecho de que el nombre de usuario aparece en la esquina. 

Si falta la notificación de nombre, habría visto **Hello, !**. Si echa un vistazo a Views\\Shared\_LoginPartial.cshtml, verá que usa `User.Identity.Name` para mostrar el nombre del usuario. Como se mencionó anteriormente, ASP.NET hidrata esta propiedad con la notificación del nombre de usuario autenticado, si está disponible en el token de SAML. Para ver todas las notificaciones que se envían por AD FS, coloque un punto de interrupción en Controllers\\HomeController.cs, en el método de acción de índice. Cuando se autentique el usuario, inspeccione la colección `System.Security.Claims.Current.Claims`.

![](./media/web-sites-dotnet-lob-application-adfs/12-test-debugging-all-claims.png)

<a name="bkmk_authorize"></a>
## Autorizar a los usuarios para acciones o controladores concretos

Puesto que ha incluido las pertenencias a grupos como notificaciones de rol en la configuración de relación de confianza para usuario autenticado, ahora puede usarlas directamente en la representación `[Authorize(Roles="...")]` para controladores y acciones. En una aplicación de línea de negocio con el patrón Create-Read-Update-Delete (CRUD), puede autorizar a roles específicos para acceder a cada acción. Por ahora, solo probará esta característica en el controlador principal existente.

1. Abra Controllers\\HomeController.cs.
2. Represente los métodos de acción `About` y `Contact` similares a los siguientes, con las pertenencias a grupos de seguridad que su usuario autenticado tiene.  
	<pre class="prettyprint">
	<mark>[Authorize(Roles="Grupo de prueba")]</mark>
	public ActionResult About()
	{
	    ViewBag.Message = "Su página de descripción de la aplicación.";
	
	    return View();
	}
	
	<mark>[Authorize(Roles="Admins. del dominio")]</mark>
	public ActionResult Contact()
	{
	    ViewBag.Message = "Su página de contacto.";
	
	    return View();
	}
	</pre>

	Puesto que he agregado **Usuario de prueba** a **Grupo de prueba** en mi entorno de laboratorio de AD FS, usaré el grupo de prueba para probar la autorización en `About`. Para `Contact`, probaré el caso negativo de **Admins. del dominio**, al que no pertenece el **Usuario de prueba**.

3. Para iniciar el depurador, escriba `F5` e inicie sesión y, a continuación, haga clic en **Acerca de**. Ahora debería ver la página `~/About/Index` correctamente, si el usuario autenticado tiene autorización para esa acción.
4. Ahora haga clic en **Contacto**, lo que en mi caso no debería autorizar a **Usuario de prueba** para la acción. Sin embargo, el explorador se redirige a AD FS, que finalmente muestra este mensaje:

	![](./media/web-sites-dotnet-lob-application-adfs/13-authorize-adfs-error.png)

	Si investiga este error en el Visor de eventos del servidor de AD FS, verá este mensaje de excepción:
	<pre class="prettyprint">
	Microsoft.IdentityServer.Web.InvalidRequestException: MSIS7042: <mark>The same client browser session has made '6' requests in the last '11' seconds.</mark> Contact your administrator for details.  (La misma sesión del explorador cliente realizó "6" solicitudes en los últimos "11" segundos. Póngase en contacto con el administrador para obtener información detallada).
	   at Microsoft.IdentityServer.Web.Protocols.PassiveProtocolHandler.UpdateLoopDetectionCookie(WrappedHttpListenerContext context)
	   at Microsoft.IdentityServer.Web.Protocols.WSFederation.WSFederationProtocolHandler.SendSignInResponse(WSFederationContext context, MSISSignInResponse response)
	   at Microsoft.IdentityServer.Web.PassiveProtocolListener.ProcessProtocolRequest(ProtocolContext protocolContext, PassiveProtocolHandler protocolHandler)
	   at Microsoft.IdentityServer.Web.PassiveProtocolListener.OnGetContext(WrappedHttpListenerContext context)
	</pre>

	El motivo de que esto suceda es que, de forma predeterminada, MVC devuelve un mensaje 401 No autorizado cuando los roles de un usuario no están autorizados. Esto desencadena una solicitud de reautenticación para su proveedor de identidades (AD FS). Puesto que el usuario ya está autenticado, AD FS vuelve a la misma página, que a continuación emite otro mensaje 401, creando un bucle de redirección. Ahora reemplazará el método `HandleUnauthorizedRequest` de AuthorizeAttribute con una lógica sencilla para mostrar algo que tenga sentido, en lugar de continuar el bucle de redirección.

5. Cree un archivo en el proyecto llamado AuthorizeAttribute.cs y pegue el código siguiente en él.

		using System;
		using System.Web.Mvc;
		using System.Web.Routing;
		
		namespace WebApp_WSFederation_DotNet
		{
		    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
		    public class AuthorizeAttribute : System.Web.Mvc.AuthorizeAttribute
		    {
		        protected override void HandleUnauthorizedRequest(AuthorizationContext filterContext)
		        {
		            if (filterContext.HttpContext.Request.IsAuthenticated)
		            {
		                filterContext.Result = new System.Web.Mvc.HttpStatusCodeResult((int)System.Net.HttpStatusCode.Forbidden);
		            }
		            else
		            {
		                base.HandleUnauthorizedRequest(filterContext);
		            }
		        }
		    }
		}

	El código de reemplazo envía un mensaje HTTP 403 (Prohibido) en lugar de HTTP 401 (No autorizado) en los casos autenticado pero no autorizados.

6. Ejecute nuevamente el depurador con `F5`. Si se hace clic en **Contacto** aparece ahora un mensaje de error más informativo (aunque poco atractivo):

	![](./media/web-sites-dotnet-lob-application-adfs/14-unauthorized-forbidden.png)

7. Publique nuevamente la aplicación en Aplicaciones web del Servicio de aplicaciones de Azure y pruebe el comportamiento de la aplicación activa.

<a name="bkmk_data"></a>
## Conectarse a datos locales

Un motivo por el que desearía implementar su aplicación de línea de negocio con AD FS en lugar de Azure Active Directory son los problemas de cumplimiento a la hora de mantener los datos de la organización remotos. Esto también puede significar que su aplicación web de Azure deba acceder a bases de datos remotas, ya que no se le permite usar [Base de datos SQL](/services/sql-database/) como la capa de datos para sus aplicaciones web.

Aplicaciones web del Servicio de aplicaciones de Azure admite el acceso a bases de datos locales con dos enfoques: [Conexiones híbridas](../biztalk-services/integration-hybrid-connection-overview.md) y [Redes virtuales](web-sites-integrate-with-vnet.md). Para obtener más información, consulte [Uso de integración VNET y conexiones híbridas con Aplicaciones web del Servicio de aplicaciones de Azure](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/).

<a name="bkmk_resources"></a>
## Recursos adicionales

- [Protección de la aplicación con SSL y el atributo Authorize](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Usar Active Directory para la autenticación en Servicio de aplicaciones de Azure](web-sites-authentication-authorization.md)
- [Crear una aplicación web de .NET MVC en Servicio de aplicaciones de Azure con autenticación de Azure Active Directory](web-sites-dotnet-lob-application-azure-ad.md)
- [Usar la opción de autenticación de organización profesional local (ADFS) con ASP.NET en Visual Studio 2013](http://www.cloudidentity.com/blog/2014/02/12/use-the-on-premises-organizational-authentication-option-adfs-with-asp-net-in-visual-studio-2013/)
- [El blog de Vittorio Bertocci](http://blogs.msdn.com/b/vbertocci/)
- [Migrar un proyecto web de VS2013 de WIF a Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Información general de Servicios de federación de Active Directory](http://technet.microsoft.com/library/hh831502.aspx)
- [Especificación de WS-Federation 1.1](http://download.boulder.ibm.com/ibmdl/pub/software/dw/specs/ws-fed/WS-Federation-V1-1B.pdf?S_TACT=105AGX04&S_CMP=LP)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 
 

<!---HONumber=AcomDC_0302_2016-->