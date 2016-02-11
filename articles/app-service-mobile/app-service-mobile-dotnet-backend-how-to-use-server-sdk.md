<properties
	pageTitle="Cómo trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles | Servicio de aplicaciones de Azure"
	description="Obtenga información sobre cómo trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles del Servicio de aplicaciones de Azure"
	keywords="servicio de aplicaciones, servicio de aplicaciones de azure, aplicación móvil, servicio móvil, escala, escalable, implementación de aplicaciones, implementación de aplicaciones de azure"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/24/2016"
	ms.author="glenga"/>

# Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

En este tema se muestra cómo usar el SDK del servidor back-end de .NET en escenarios clave de Aplicaciones móviles del Servicio de aplicaciones de Azure. El SDK de Aplicaciones móviles de Azure le permite trabajar con clientes móviles de su aplicación ASP.NET.

>[AZURE.TIP] El [SDK de servidor de .NET para Aplicaciones móviles de Azure](https://github.com/Azure/azure-mobile-apps-net-server) es de código abierto en GitHub. El repositorio contiene el conjunto de pruebas de unidad SDK del servidor completo, así como algunos proyectos de ejemplo.

## Documentación de referencia

La documentación de referencia del SDK del servidor se encuentra aquí: [Referencia de .NET de Aplicaciones móviles de Azure](https://msdn.microsoft.com/library/azure/dn961176.aspx).

## <a name="create-app"></a>Creación de un back-end de .NET para la aplicación móvil

Si va a iniciar un nuevo proyecto, puede crear una aplicación del Servicio de aplicaciones mediante el [Portal de Azure] o Visual Studio. Esta sección le ayudará a usar una de estas opciones para crear un nuevo back-end de aplicación móvil que hospeda una API sencilla de lista de tareas. Puede ejecutar el proyecto localmente o publicarlo en la aplicación móvil del Servicio de aplicaciones basada en la nube.

Si va a agregar funciones de movilidad a un proyecto existente, vea la sección [Descargar e inicializar el SDK](#install-sdk) que se encuentra más adelante.

### Creación de un back-end .NET mediante el Portal de Azure

Puede crear un nuevo derecho de aplicación móvil en el [Portal de Azure]. Puede seguir los pasos que se muestran a continuación o crear un cliente y un servidor nuevos mediante el tutorial [Creación de una aplicación móvil](app-service-mobile-ios-get-started.md).

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

&nbsp;&nbsp;9. De nuevo en la hoja _Comenzar_, en **Crear una API de tabla**, elija **C#** como el valor de **Lenguaje de back-end**.

&nbsp;&nbsp;10. Haga clic en Descargar, extraiga los archivos de proyecto comprimidos en el equipo local y abra la solución en Visual Studio.

### Creación de un back-end .NET con Visual Studio 2013 y Visual Studio 2015

Para crear un proyecto de Aplicaciones móviles en Visual Studio, deberá instalar la versión 2.8.1 o posterior del [SDK de Azure para .NET](https://azure.microsoft.com/downloads/). Después de instalar el SDK, cree una nueva aplicación de ASP.NET:

1. Abra el cuadro de diálogo **Nuevo proyecto** (desde *Archivo* > **Nuevo** > **Proyecto...**).

2. Expanda **Plantillas** > **Visual C#** y seleccione **Web**.

3. Seleccione **Aplicación web ASP.NET**.

4. Rellene el nombre del proyecto. A continuación, haga clic en **Aceptar**.

5. En _Plantillas de ASP.NET 4.5.2_, seleccione **Aplicación móvil de Azure**. Active **Host en la nube** para crear una nueva aplicación móvil en la nube en la que puede publicar este proyecto.

6. Haga clic en **Aceptar**. Se creará la aplicación y aparecerá en el Explorador de soluciones.

## <a name="install-sdk"></a>Descarga e inicialización del SDK

El SDK está disponible en [NuGet.org]. Este paquete incluye la funcionalidad básica necesaria para comenzar a usar el SDK. Para inicializar el SDK, tendrá que realizar acciones en el objeto **HttpConfiguration**.

###Instalación del SDK

Para instalar el SDK, haga clic con el botón derecho en el proyecto de servidor en Visual Studio, seleccione **Administrar paquetes de NuGet**, busque el paquete [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) y haga clic en **Instalar**.

###<a name="server-project-setup"></a> Inicializar el proyecto de servidor

Un proyecto de servidor backend de .NET se inicializa de manera similar a otros proyectos ASP.NET mediante la inclusión de una clase de inicio OWIN. Asegúrese de que ha hecho referencia al paquete de NuGet `Microsoft.Owin.Host.SystemWeb`. Para agregar esta clase en Visual Studio, haga clic con el botón derecho en el proyecto de servidor y seleccione **Agregar** -> **Nuevo elemento** y luego **Web** -> **General** -> **Clase de inicio OWIN**.

Esto generará una clase con el atributo siguiente:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

En el método `Configuration()` de la clase de inicio OWIN, configure el proyecto de servidor mediante un objeto **HttpConfiguration** que represente las opciones de configuración del servicio. En el ejemplo siguiente se inicializa el proyecto de servidor, sin características agregadas:

	// in OWIN startup class
	public void Configuration(IAppBuilder app)
	{
	    HttpConfiguration config = new HttpConfiguration();
	   
	    new MobileAppConfiguration()
	        // no added features
	        .ApplyTo(config);  
	    
	    app.UseWebApi(config);
	}

Para habilitar características individuales, debe llamar a los métodos de extensión del objeto **MobileAppConfiguration** antes de llamar a **ApplyTo**. Por ejemplo, el siguiente código agrega las rutas predeterminadas a todos los controladores de API que tengan el atributo `[MobileAppController]` durante la inicialización:

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

Tenga en cuenta que `MapApiControllers` solo asigna los controladores al atributo `[MobileAppController]`.

Muchos de los métodos de extensión de características están disponibles a través de paquetes de NuGet adicionales que puede incluir, que se describen en la sección siguiente.

El inicio rápido de servidor desde el Portal de Azure llama a **UseDefaultConfiguration()**. Es equivalente a la siguiente configuración:
    
		new MobileAppConfiguration()
			.AddMobileAppHomeController()             // from the Home package
			.MapApiControllers()
			.AddTables(                               // from the Tables package
				new MobileAppTableConfiguration()
					.MapTableControllers()
					.AddEntityFramework()             // from the Entity package
				)
			.AddPushNotifications()                   // from the Notifications package
			.MapLegacyCrossDomainController()         // from the CrossDomain package
			.ApplyTo(config);


### Extensiones de SDK

Los siguientes paquetes de extensión basados en NuGet proporcionan diversas características móviles que puede usar la aplicación. Las extensiones se habilitan durante la inicialización mediante el objeto **MobileAppConfiguration**.

- [Microsoft.Azure.Mobile.Server.Quickstart] admite la configuración básica de Aplicaciones móviles. Se agrega a la configuración mediante una llamada al método de extensión **UseDefaultConfiguration** durante la inicialización. Esta extensión incluye las siguientes extensiones: notificaciones, autenticación, entidad, tablas y paquetes principales y entre dominios . Esto es equivalente al proyecto de servidor de inicio rápido que se descarga desde el Portal de Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) Implementa el valor predeterminado de *esta página de aplicación móvil está funcionando* para la raíz del sitio web. Se agrega a la configuración mediante una llamada al método de extensión **AddMobileAppHomeController**.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) incluye clases para trabajar con datos y configura la canalización de datos. Se agrega a la configuración mediante una llamada al método de extensión **AddTables**.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) permite a Entity Framework obtener acceso a los datos de la base de datos SQL. Se agrega a la configuración mediante una llamada al método de extensión **AddTablesWithEntityFramework**.

- [Microsoft.Azure.Mobile.Server.Authentication] habilita la autenticación y configura el middleware OWIN que se usa para validar los tokens. Se agrega a la configuración mediante una llamada a los métodos de extensión **AddAppServiceAuthentication** e **IAppBuilder**.**UseAppServiceAuthentication**.

- [Microsoft.Azure.Mobile.Server.Notifications] habilita las notificaciones push y define un punto de conexión de registro de inserción. Se agrega a la configuración mediante una llamada al método de extensión **AddPushNotifications**.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) crea un controlador que sirve datos de la aplicación móvil a los exploradores web heredados. Se agrega a la configuración mediante una llamada al método de extensión **MapLegacyCrossDomainController**.

- [Microsoft.Azure.Mobile.Server.Login] ofrece soporte de vista previa para la autenticación personalizada mediante el método AppServiceLoginHandler.CreateToken(). Este es un método estático y no es necesario que esté habilitado en la configuración.

## <a name="publish-server-project"></a>Procedimientos: Publicación del proyecto de servidor

En esta sección se muestra cómo publicar el proyecto de back-end de .NET desde Visual Studio. También puede implementar el proyecto de back-end mediante Git o cualquiera de los otros métodos descritos en la [documentación de implementación del Servicio de aplicaciones de Azure](../app-service-web/web-site-deploy.md).

1. En Visual Studio, vuelva a generar el proyecto para restaurar los paquetes de NuGet.

2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y luego haga clic en **Publicar**. La primera vez que publique, debe definir un perfil de publicación. Si ya tiene un perfil definido, solo tiene que seleccionarlo y hacer clic en **Publicar**.

2. Si se le pide que seleccione un destino de publicación, haga clic en **Servicio de aplicaciones de Microsoft Azure** > **Siguiente**, y luego (si es necesario) inicie sesión con sus credenciales de Azure. Visual Studio descarga y almacena la configuración de publicación directamente desde Azure.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)

3. Elija su suscripción en **Suscripción**, seleccione **Tipo de recurso** en **Vista**, expanda **Aplicación móvil**, haga clic en el back-end de aplicación móvil y. luego haga clic en **Aceptar**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)

4. Compruebe la información del perfil de publicación y haga clic en **Publicar**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

	Cuando el back-end de la aplicación móvil se ha publicado correctamente, verá una página de aterrizaje que indica el éxito.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## Cómo definir un controlador de tabla

Un controlador de tabla proporciona acceso a datos de entidad en un almacén de datos basado en tabla, como la base de datos SQL o el almacenamiento de tablas de Azure. Los controladores de tabla se heredan de la clase genérica **TableController**, en la que el tipo genérico es una entidad del modelo que representa el esquema de tabla, de la siguiente manera:

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

Los controladores de tabla se inicializan mediante el método de extensión **AddTables**. Con ello se agregan rutas en `/tables/` para todas las subclases de `TableController`.

En el ejemplo siguiente se inicializa un controlador de tabla que usa Entity Framework para el acceso a los datos:

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
Para obtener un ejemplo de un controlador de tabla que usa Entity Framework para acceder a los datos desde una base de datos SQL de Azure, consulte la clase **TodoItemController** en la descarga del proyecto de servidor de inicio rápido del Portal de Azure.

## Cómo definir un controlador de API personalizada

El controlador de API personalizada proporciona la funcionalidad más básica al back-end de la aplicación móvil mediante la exposición de un extremo. Puede registrar un controlador de API específico de dispositivos móviles con el atributo [MobileAppController]. Este atributo registra la ruta y también configura el serializador JSON de Aplicaciones móviles.

1. En Visual Studio, haga clic con el botón derecho en la carpeta Controladores y, luego, haga clic en **Agregar** > **Controlador**, seleccione **Controlador Web API 2&mdash;Vacío** y haga clic en **Agregar**.

2. Facilite un valor de **Nombre de controlador**, como `CustomController`, y haga clic en **Agregar**. Se crea una nueva clase **CustomController**, que hereda de **ApiController**.

3. En el archivo de clase de controlador nuevo, agregue la siguiente instrucción Using:

		using Microsoft.Azure.Mobile.Server.Config;

4. Aplique el atributo **[MobileAppControllerAttribute]** a la definición de clase del controlador de API, como en el ejemplo siguiente:

		[MobileAppController] 
		public class CustomController : ApiController
		{
		      //...
		}

4. En el archivo App\_Start/Startup.MobileApp.cs, agregue una llamada al método de extensión **MapApiControllers**, como en el ejemplo siguiente:

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);
    
	Tenga en cuenta que no es necesario llamar a **MapApiControllers** si en su lugar llama a **UseDefaultConfiguration**, ya que se inicializan todas las características.

Los clientes pueden tener acceso a un controlador aunque este no tenga un elemento **MobileAppControllerAttribute** aplicado, pero puede que no lo consuman correctamente si usan un SDK de cliente de aplicación móvil.

## Procedimiento: Autenticación

El servicio Aplicaciones móviles utiliza la autenticación del Servicio de aplicaciones y ASP.NET para simplificar el proceso de habilitar la autenticación para sus aplicaciones. En esta sección se muestra cómo realizar las siguientes tareas relacionadas con la autenticación en el proyecto de servidor back-end. NET:

+ [Cómo agregar autenticación a un proyecto de servidor](#add-auth) 
+ [Procedimiento: Uso de autenticación personalizada en la aplicación](#custom-auth) 
+ [Procedimiento: Recuperación de la información de usuario de autenticado](#user-info)

### <a name="add-auth"></a>Procedimiento: Incorporación de la autenticación a un proyecto de servidor

Para agregar autenticación al proyecto de servidor, extienda el objeto **MobileAppConfiguration** y configure el middleware OWIN. Cuando instale el paquete [Microsoft.Azure.Mobile.Server.Quickstart] y llame al método de extensión **UseDefaultConfiguration**, puede continuar desde el paso 3.

1. En Visual Studio, instale el paquete [Microsoft.Azure.Mobile.Server.Authentication]. 

2. En el archivo de proyecto Startup.cs, agregue la siguiente línea de código al principio del método **Configuration**:

		app.UseAppServiceAuthentication(config);

	Esto agrega el componente middleware OWIN que permite que su aplicación móvil de Azure valide los tokens que emite la puerta de enlace asociada del Servicio de aplicaciones.

3. Agregue el atributo `[Authorize]` a cualquier controlador o método que requiera autenticación. Ahora, los usuarios deben autenticarse para tener acceso a ese punto de conexión o a API específicas.

Para más información sobre cómo autenticar a los clientes en el back-end de Aplicaciones móviles, consulte [Incorporación de la autenticación a la aplicación](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Procedimiento: Uso de autenticación personalizada en la aplicación

Puede proporcionar su propio sistema de inicio de sesión si no desea usar uno de los proveedores de autenticación/autorización del Servicio de aplicaciones. Para ello, instale el paquete [Microsoft.Azure.Mobile.Server.Login].

Deberá proporcionar su propia lógica para determinar si se debe iniciar la sesión de un usuario. Por ejemplo, podría comprobar las contraseñas con sal y hash de una base de datos. En el ejemplo siguiente, el método `isValidAssertion()` es responsable de estas comprobaciones y se define en otra parte.

La autenticación personalizada se expone mediante la creación de un nuevo ApiController y la exposición de acciones de registro e inicio de sesión como la siguiente. El cliente puede intentar iniciar sesión mediante la recopilación de la información pertinente del usuario y el envío de una solicitud HTTPS POST a la API con la información del usuario en el cuerpo. Una vez que el servidor valida la aserción, se puede emitir un token con el método `AppServiceLoginHandler.CreateToken()`.

Una acción de inicio de sesión de ejemplo podría ser:

		public IHttpActionResult Post([FromBody] JObject assertion)
		{
			if (isValidAssertion(assertion)) // user-defined function, checks against a database
			{
				JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
					mySigningKey,
					myAppURL,
					myAppURL,
					TimeSpan.FromHours(24) );
				return Ok(new LoginResult()
				{
					AuthenticationToken = token.RawData,
					User = new LoginResultUser() { UserId = userName.ToString() }
				});
			}
			else // user assertion was not valid
			{
				return this.Request.CreateUnauthorizedResponse();
			}
		}

LoginResult y LoginResultUser son simples objetos que exponen las propiedades mostradas. El cliente espera que las repuestas de inicio de sesión vuelvan como objetos JSON con esta forma:

		{
			"authenticationToken": "<token>",
			"user": {
				"userId": "<userId>"
			}
		}

El método `MobileAppLoginHAppServiceLoginHandlerandler.CreateToken()` incluye un parámetro _audience_ y un parámetro _issuer_. Normalmente, ambos se establecen en la dirección URL de la raíz de la aplicación, mediante el esquema HTTPS. De igual modo, debe establecer _secretKey_ como el valor de la clave de firma de la aplicación. Se trata de un valor confidencial que nunca se comparte ni se incluye en un cliente. Puede obtener este valor mientras se hospeda en el Servicio de aplicaciones mediante la referencia a la variable de entorno _WEBSITE\_AUTH\_SIGNING\_KEY_. Si es necesario en un contexto de depuración local, siga las instrucciones de la sección [Depuración local con autenticación](#local-debug) para recuperar la clave y almacenarla como un valor de configuración de la aplicación.

También deberá proporcionar una duración para el token emitido, así como las notificaciones que le gustaría incluir. Es necesario proporcionar una notificación de asunto, como se muestra en el código de ejemplo.

También puede simplificar el código de cliente para usar el método `loginAsync()` (la nomenclatura puede variar entre plataformas) en lugar de un HTTP POST manual. Usaría la sobrecarga que toma un parámetro de token adicional, que se pone en correlación con el objeto de aserción en el que ejecutaría POST. En este caso el proveedor debería ser un nombre personalizado de su elección. A continuación en el servidor, la acción de inicio de sesión debería estar en la ruta de acceso _/.auth/login/{customProviderName}_ que incluye este nombre personalizado. Para colocar el controlador en esta ruta de acceso, agregue una ruta a HttpConfiguration antes de aplicar MobileAppConfiguration.

		config.Routes.MapHttpRoute("CustomAuth", ".auth/login/CustomAuth", new { controller = "CustomAuth" }); 
		
Reemplace la cadena "CustomAuth" anterior por el nombre del controlador que hospeda su acción de inicio de sesión.

>[AZURE.TIP] Con el enfoque loginAsync() se garantiza que el token de autenticación se adjunta a cada llamada posterior al servicio.

###<a name="user-info"></a>Procedimiento: Recuperación de la información de usuario autenticada

Cuando un usuario se autentica mediante el Servicio de aplicaciones, se puede tener acceso al id. de usuario asignado y otra información en el código del back-end. NET. Esto resulta útil para tomar decisiones de autorización para un usuario determinado en el back-end, por ejemplo, si un usuario específico puede acceder a una fila de la tabla o a otro recurso. El código siguiente muestra cómo obtener el identificador de usuario para un usuario que ha iniciado sesión:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

El SID se deriva del identificador de usuario específico del proveedor y es estático para un usuario y un proveedor de inicio de sesión dados.

El Servicio de aplicaciones también le permite solicitar notificaciones específicas de su proveedor de inicio de sesión. De esta forma puede solicitar más información del proveedor, por ejemplo, mediante las API Graph de Facebook. Puede especificar notificaciones en la hoja de proveedor en el portal. Algunas notificaciones requieren configuración adicional con el proveedor.

El código siguiente llama al método de extensión **GetAppServiceIdentityAsync** para obtener las credenciales de inicio de sesión, que incluyen el token de acceso necesario para realizar solicitudes en la API Graph de Facebook:

    // Get the credentials for the logged-in user.
    var credentials = 
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token=" 
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Tenga en cuenta que debe agregar una instrucción using a `System.Security.Principal` para que el método de extensión **GetAppServiceIdentityAsync** funcione.


## Cómo agregar notificaciones push a un proyecto de servidor

Para agregar notificaciones push al proyecto de servidor, extienda el objeto **MobileAppConfiguration** y cree un cliente de Centros de notificaciones. Cuando instale el paquete [Microsoft.Azure.Mobile.Server.Quickstart] y llame al método de extensión **UseDefaultConfiguration**, puede continuar desde el paso 3.

1. En Visual Studio, haga clic con el botón derecho en el proyecto de servidor y luego haga clic en **Administrar paquetes de NuGet**, busque Microsoft.Azure.Mobile.Server.Notifications` y después haga clic en **Instalar**. Se instala el paquete [Microsoft.Azure.Mobile.Server.Notifications].
 
3. Repita este paso para instalar el paquete `Microsoft.Azure.NotificationHubs`, que incluye la biblioteca de cliente de Centros de notificaciones.

2. En el archivo App\_Start/Startup.MobileApp.cs, agregue una llamada al método de extensión **AddPushNotifications** durante la inicialización, que tiene el siguiente aspecto:

		new MobileAppConfiguration()
			// other features...
			.AddPushNotifications()
			.ApplyTo(config);

	Esto crea el extremo de registro de notificaciones push en el proyecto de servidor. Los clientes usan este extremo para registrarse en el centro de notificaciones asociado. Ahora, deberá agregar el cliente de Centros de notificaciones que se usa para enviar notificaciones.

3. En un controlador desde el que desee enviar notificaciones push, agregue la siguiente instrucción Using:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;

4. Agregue el siguiente código, que crea un nuevo cliente de Centros de notificaciones:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

En este momento, puede usar el cliente de Centros de notificaciones para enviar notificaciones push a dispositivos registrados. Para más información, vea [Incorporación de notificaciones push a la aplicación](app-service-mobile-ios-get-started-push.md). Para más información sobre todo lo que puede hacer con los Centros de notificaciones, vea [Información general de los Centros de notificaciones](../notification-hubs/notification-hubs-overview.md).

##<a name="tags"></a>Procedimiento: Incorporación de etiquetas a la instalación de un dispositivo para habilitar la inserción en etiquetas

Después de la sección anterior **Procedimiento: Definición de un controlador de API personalizado**, querrá configurar una API personalizada en el back-end para que funcione con los Centros de notificaciones con el fin de agregar etiquetas a la instalación de un dispositivo específico. Asegúrese de pasar el identificador de instalación almacenado en el almacenamiento local del cliente y las etiquetas que desee agregar (opcional, ya que también puede especificar etiquetas directamente en el back-end). El fragmento siguiente debe agregarse al controlador para trabajar con los Centros de notificaciones para agregar una etiqueta a un identificador de instalación de dispositivo.

Mediante [NuGet de Centros de notificaciones de Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) ([referencia](https://msdn.microsoft.com/library/azure/mt414893.aspx)):

		var hub = NotificationHubClient.CreateClientFromConnectionString("my-connection-string", "my-hub");

		hub.PatchInstallation("my-installation-id", new[]
		{
		    new PartialUpdateOperation
		    {
		        Operation = UpdateOperationType.Add,
		        Path = "/tags",
		        Value = "{my-tag}"
		    }
		});

Para insertar en estas etiquetas, trabaje con las [API de Centros de notificaciones](https://msdn.microsoft.com/library/azure/dn495101.aspx).

Además, podrá construir su API personalizada para registrar instalaciones de dispositivos con centros de notificaciones directamente en el back-end.

## Procedimientos: Depuración y solución de problemas del SDK de .NET Server

El Servicio de aplicaciones de Azure proporciona varias técnicas de depuración y solución de problemas para las aplicaciones ASP.NET.

- [Supervisión de Aplicaciones web en el Servicio de aplicaciones de Azure](../app-service-web/web-sites-monitor.md)
- [Habilitación del registro de diagnóstico para aplicaciones web en el Servicio de aplicaciones de Azure](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Solución de problemas de una aplicación web en el Servicio de aplicaciones de Azure con Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### Registro

Puede escribir en registros de diagnóstico del Servicio de aplicaciones mediante la escritura de seguimiento estándar de ASP.NET. Antes de poder escribir en los registros, debe habilitar los diagnósticos en su back-end de aplicación móvil.

Para habilitar los diagnósticos y escribir en los registros:

1. Siga los pasos que se indican en [Habilitación de diagnósticos](../app-service-web/web-sites-enable-diagnostic-log.md#enablediag).

2. Agregue la siguiente instrucción using en el archivo de código:

		using System.Web.Http.Tracing;

3. Cree un escritor de seguimiento para escribir desde el back-end de .NET en los registros de diagnóstico, de la manera siguiente:

		ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
		traceWriter.Info("Hello, World");  

4. Vuelva a publicar el proyecto de servidor y acceda al back-end de aplicación móvil para ejecutar la ruta de acceso del código con el registro.

5. Descargue y evalúe los registros, como se describe en [Procedimiento: Descarga de registros](../app-service-web/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Depuración local con autenticación

Puede ejecutar la aplicación localmente para probar los cambios antes de publicarlos en la nube. En muchas aplicaciones, solo es cuestión de presionar *F5* mientras se está en Visual Studio. Sin embargo, hay algunas consideraciones adicionales cuando se usa la autenticación.

Debe tener una aplicación móvil basada en la nube que tenga configurada la característica Autenticación/autorización del Servicio de aplicaciones, y su cliente debe haber especificado el punto de conexión de nube como host de inicio de sesión alternativo. Consulte la documentación de la plataforma cliente elegida ([iOS](app-service-mobile-ios-how-to-use-client-library.md), [Windows/Xamarin](app-service-mobile-dotnet-how-to-use-client-library.md)) para conocer los pasos específicos requeridos.

Asegúrese de que la aplicación tenga instalado [Microsoft.Azure.Mobile.Server.Authentication]. Después, en la clase de inicio OWIN de la aplicación, agregue lo siguiente, después de aplicar `MobileAppConfiguration` a `HttpConfiguration`:
		
		app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
		{
			SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
			ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
			ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
			TokenHandler = config.GetMobileAppTokenHandler()
		});

En el ejemplo anterior, debe configurar los parámetros de la aplicación _authAudience_ y _authIssuer_ en el archivo Web.config para que cada uno sea la dirección URL de la raíz de la aplicación; para ello, usará el esquema HTTPS. De igual modo, debe establecer _authSigningKey_ como el valor de la clave de firma de la aplicación. Se trata de un valor confidencial que nunca se comparte ni se incluye en un cliente. Para obtenerlo, vaya a la aplicación en el [Portal de Azure] y haga clic en **Herramientas**. Luego seleccione **Kudu** y haga clic en **Ir**. Llegará al punto de conexión de administración de Kudu de su sitio. Haga clic en **Entorno** y busque el valor en _WEBSITE\_AUTH\_SIGNING\_KEY_. Este es el valor que debería usar para _authSigningKey_ en la configuración de la aplicación local.

El servidor de ejecución local está ahora preparado para validar los tokens que el cliente obtiene del punto de conexión basado en la nube.


[Portal de Azure]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=AcomDC_0128_2016-->