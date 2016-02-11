<properties 
	pageTitle="Crear una aplicación web de .NET MVC en Servicio de aplicaciones de Azure con autenticación de Azure Active Directory" 
	description="Obtenga información sobre cómo crear una aplicación de línea de negocio de ASP.NET MVC en Servicio de aplicaciones de Azure que se autentica con Azure Active Directory" 
	services="app-service\web, active-directory" 
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
	ms.date="12/10/2015" 
	ms.author="cephalin"/>

# Crear una aplicación web de .NET MVC en Servicio de aplicaciones de Azure con autenticación de Azure Active Directory #

En este artículo, aprenderá a crear una aplicación de línea de negocio de ASP.NET MVC en [Aplicaciones web del Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) mediante el uso de [Azure Active Directory](/services/active-directory/) como proveedor de identidades. También aprenderá a usar la [biblioteca de cliente de Azure Active Directory Graph](http://blogs.msdn.com/b/aadgraphteam/archive/2014/06/02/azure-active-directory-graph-client-library-1-0-publish.aspx) para consultar los datos de directorio en la aplicación.

El inquilino de Azure Active Directory que usa puede ser un directorio solo de Azure, o puede estar sincronizado con directorios con su Active Directory (AD) local para crear una experiencia de inicio de sesión único para los trabajadores que sean locales o remotos.

>[AZURE.NOTE]Para las Aplicaciones web del Servicio de aplicaciones de Azure, puede configurar la autenticación contra un inquilino de Azure Active Directory con unos pocos clics de un botón. Para obtener más información, consulte [Uso de Active Directory para autenticación en Servicio de aplicaciones de Azure](web-sites-authentication-authorization.md).

<a name="bkmk_build"></a>
## Lo que va a crear ##

Creará una aplicación crear-leer-actualizar-eliminar (CRUD) de línea de negocio simple en Aplicaciones web del Servicio de aplicaciones que realiza un seguimiento de los elementos de trabajo con las siguientes características:

- Autentica los usuarios contra Azure Active Directory
- Implementa la funcionalidad de inicio y de cierre de sesión
- Usa `[Authorize]` para autorizar a los usuarios para distintas acciones CRUD
- Consulta los datos de Azure Active Directory con la [API de Azure Active Directory Graph](http://msdn.microsoft.com/library/azure/hh974476.aspx)
- Usa [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (en lugar de Windows Identity Foundation, es decir, WIF), que es el futuro de ASP.NET y mucho más simple de configurar que WIF para la autenticación y autorización

<a name="bkmk_need"></a>
## Qué necesita ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de suscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

Necesita lo siguiente para completar este tutorial:

- Un inquilino de Azure Active Directory con usuarios en varios grupos
- Permisos para crear aplicaciones en el inquilino de Azure Active Directory
- Visual Studio 2013 o posterior.
- [SDK de Azure 2.8.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) o posterior.

<a name="bkmk_sample"></a>
## Usar la aplicación de ejemplo para la plantilla de línea de negocio ##

La aplicación de ejemplo de este tutorial, [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims), la crea el equipo de Azure Active Directory y se puede usar como plantilla para crear con facilidad nuevas aplicaciones de línea de negocio. Tiene las siguientes características integradas:

- Usa [OpenID Connect](http://openid.net/connect/) para autenticarse con Azure Active Directory
- Contiene un controlador `TaskTracker` de ejemplo que muestra cómo puede autorizar distintos roles para acciones específicas en una aplicación, incluido el uso estándar de `[Authorize]`. 
- Es una aplicación multiinquilino con roles predefinidos que puede asignar inmediatamente a usuarios y grupos. 

<a name="bkmk_run" />
## Ejecutar la aplicación de muestra ##

1.	Clone o descargue la solución de muestra en [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) en el directorio local.

2.	Siga las instrucciones de [Cómo ejecutar la muestra como aplicación de inquilino único](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#how-to-run-the-sample-as-a-single-tenant-app) para configurar el proyecto y la aplicación de Azure Active Directory. Asegúrese de seguir todas las instrucciones para convertir la aplicación de multiinquilino a un solo inquilino.

3.	En la vista del [Portal de Azure clásico](https://manage.windowsazure.com) de la aplicación de Azure Active Directory que acaba de crear, haga clic en la pestaña **USUARIOS**. A continuación, asigne los usuarios que quiera a las funciones que desee.

	>[AZURE.NOTE]Si quiere asignar roles a grupos además de a usuarios, debe actualizar su inquilino de Azure Active Directory a [Azure Active Directory Premium](/pricing/details/active-directory/). En la interfaz de usuario del portal clásico de la aplicación, si ve la pestaña **USUARIOS** en lugar de la pestaña **USUARIOS Y GRUPOS, puede probar Azure Active Directory Premium si se dirige a la pestaña **LICENCIAS** de su inquilino de Azure Active Directory.

3.	Cuando haya terminado de configurar la aplicación, escriba `F5` en Visual Studio para ejecutar la aplicación ASP.NET.

4.	Cuando se cargue la aplicación, haga clic en **Iniciar sesión** e inicie sesión con un usuario que tenga el rol de administrador en el portal de Azure clásico.

5.	Si configuró la aplicación de Azure Active Directory correctamente y estableció los valores correspondientes en el archivo Web.config, se le debería redirigir al inicio de sesión. Solo tiene que iniciar sesión con la cuenta utilizada para crear la aplicación Azure Active Directory en el portal de Azure clásico, puesto que es el propietario predeterminado de la aplicación Azure Active Directory.
	
<a name="bkmk_deploy"></a>
## Implementar la aplicación de ejemplo en Aplicaciones web del Servicio de aplicaciones

A continuación, publicará la aplicación en una aplicación web en Servicio de aplicaciones de Azure. Ya hay instrucciones en [README.md](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims/blob/master/README.md) para implementar Aplicaciones web del Servicio de aplicaciones, pero esos pasos también anulan la configuración para su entorno de depuración local. Le mostraré cómo implementar y conservar la configuración de depuración.

1. Haga clic con el botón derecho en el proyecto y seleccione **Publicar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/publish-app.png)

2. Seleccione **Aplicaciones web de Microsoft Azure**.

3. Si no ha iniciado sesión en Azure, haga clic en **Agregar una cuenta** y use la cuenta de Microsoft de su suscripción de Azure para iniciar sesión.

4. Cuando haya iniciado sesión, haga clic en **Nuevo** para crear una nueva aplicación web en Azure.

5. En **Hospedaje**, rellene todos los campos obligatorios.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-website.png)

5. Necesitará una conexión de base de datos para que esta aplicación almacene las asignaciones de rol, los tokens almacenados en memoria caché y los datos de aplicación. En el cuadro de diálogo **Crear servicio de aplicaciones**, haga clic en **Servicios**. Junto a **Base de datos SQL** haga clic en el signo más para agregar una nueva base de datos.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-database.png)

5. En el cuadro de diálogo **Configurar base de datos SQL**, seleccione o cree un servidor, establezca un nombre y haga clic en **Aceptar**.

	 ![](./media/web-sites-dotnet-lob-application-azure-ad/4-config-database.png)

6. Haga clic en **Crear**. Una vez que se haya creado la aplicación web, se abrirá el cuadro de diálogo **Publicación web**.

7. En **Dirección URL de destino**, cambie **http** a **https**. Copie toda la dirección URL en un editor de texto. La usará más adelante. A continuación, haga clic en **Siguiente**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-change-to-https.png)

8. Desactive la casilla de verificación **Habilitar autenticación de organización**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-enable-code-first-migrations.png)

8. Expanda **RoleClaimContext** y seleccione **Ejecutar migraciones Code First (se ejecuta al iniciar la aplicación)**. [Migraciones de Code First](https://msdn.microsoft.com/data/jj591621.aspx) ayuda a actualizar el esquema de base de datos de la aplicación en Azure al definir modelos de datos de Code First adicionales más adelante.

9. En lugar de hacer clic en **Publicar** para pasar por la publicación web, haga clic en **Cerrar**. Haga clic en **Sí** para guardar los cambios en el perfil de publicación.

2. En el [Portal de Azure clásico](https://manage.windowsazure.com), vaya a su inquilino de Azure Active Directory y haga clic en la pestaña **Aplicaciones**.

2. Haga clic en **Agregar** en la parte inferior de la página.

2. Haga clic en **Agregar una aplicación que mi organización está desarrollando**.

3. Seleccione **Aplicación web y/o API web**.

4. Asigne un nombre a la aplicación y haga clic en **Siguiente**.

5. En Propiedades de la aplicación, establezca **Dirección URL de inicio de sesión** en la dirección URL de la aplicación web que guardó anteriormente (por ejemplo, `https://<site-name>.azurewebsites.net/`)y el **URI del id. de aplicación** en `https://<aad-tenanet-name>/<app-name>`. A continuación, haga clic en **Completar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-app-properties.png)

2.	Cuando se cree la aplicación, actualice el manifiesto de la aplicación de la misma manera que lo hizo antes a partir de las instrucciones de [Definir sus roles de aplicación](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#step-2-define-your-application-roles).

3.	En la vista del [Portal de Azure clásico](https://manage.windowsazure.com) de la aplicación de Azure Active Directory que acaba de crear, haga clic en la pestaña **USUARIOS**. A continuación, asigne los usuarios que quiera a las funciones que desee.

6. Haga clic en la pestaña **Configurar**.

7. En **Claves**, cree una nueva clave seleccionando **1 año** en la lista desplegable.

8. En **Permisos para otras aplicaciones**, en la entrada **Azure Active Directory**, seleccione **Perfil de usuario de lectura e inicio de sesión** y **Leer datos de directorio** en la lista desplegable **Permisos delegados**.

	> [AZURE.NOTE]Los permisos exactos que necesita aquí dependen de la funcionalidad que desee de la aplicación. Algunos permisos requieren que se establezca el rol **Administrador global**, pero los permisos necesarios para este tutorial solo requieren el rol **Usuario**.

9.  Haga clic en **Guardar**.

10.  Antes de salir de la página de configuración guardada, copie la siguiente información en un editor de texto.

	-	Id. de cliente
	-	Clave (si sale de la página, no podrá ver la clave de nuevo)

11. En Visual Studio, abra **Web.Release.config** en el proyecto. Inserte el siguiente código XML en la etiqueta `<configuration>` y reemplace el valor de cada clave por la información que guardó para la nueva aplicación de Azure Active Directory.
	<pre class="prettyprint">
&lt;appSettings>
   &lt;add key="ida:ClientId" value="<mark>[e.g. 82692da5-a86f-44c9-9d53-2f88d52b478b]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
   &lt;add key="ida:AppKey" value="<mark>[e.g. rZJJ9bHSi/cYnYwmQFxLYDn/6EfnrnIfKoNzv9NKgbo=]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
   &lt;add key="ida:PostLogoutRedirectUri" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
&lt;/appSettings></pre>

	Asegúrese de que el valor de ida:PostLogoutRedirectUri termina con una barra diagonal "/".

1. Haga clic con el botón derecho en el proyecto y seleccione **Publicar**.

2. Haga clic en **Publicar** para publicar en Aplicaciones web del Servicio de aplicaciones de Azure.

Cuando haya terminado, tendrá dos aplicaciones de Azure Active Directory configuradas en el Portal de Azure clásico: una para el entorno de depuración en Visual Studio y otra para la aplicación web publicada en Azure. Durante la depuración, la configuración de la aplicación en Web.config se usa para que su configuración de **Debug** funcione con Azure Active Directory, y cuando se publique (de forma predeterminada, se publica la configuración de **Release**), se carga un archivo Web.config que incorpora los cambios de configuración de la aplicación en Web.Release.config.

Si desea asociar la aplicación web publicada al depurador (debe cargar los símbolos de depuración del código en el sitio web publicado), puede crear un clon de la configuración de depuración para la depuración de Azure, pero con su propia transformación de Web.config personalizada (por ejemplo, Web.AzureDebug.config) que usa la configuración de la aplicación de Azure Active Directory de Web.Release.config. Esto le permite mantener una configuración estática en los distintos entornos.

<a name="bkmk_crud"></a>
## Agregar funcionalidad de línea de negocio a la aplicación de ejemplo

En esta parte del tutorial, aprenderá a crear la funcionalidad de línea de negocio deseada según la aplicación de ejemplo. Creará un rastreador de elementos de trabajo CRUD sencillo, similar al controlador TaskTracker pero utilizando el modelo de diseño y scaffolding de CRUD estándar. También utilizará el archivo Scripts\\AadPickerLibrary.js incluido para enriquecer su aplicación con datos de la API de Azure Active Directory Graph.

5.	En la carpeta Models, cree un nuevo modelo llamado [Code First](http://www.asp.net/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application) y reemplace el código por el código siguiente:

		using System.ComponentModel.DataAnnotations;
		
		namespace WebApp_RoleClaims_DotNet.Models
		{
		    public class WorkItem
		    {
		        [Key]
		        public int ItemID { get; set; }
		        public string AssignedToID { get; set; }
		        public string AssignedToName { get; set; }
		        public string Description { get; set; }
		        public WorkItemStatus Status { get; set; }
		    }
		
		    public enum WorkItemStatus
		    {
		        Open, 
		        Investigating, 
		        Resolved, 
		        Closed
		    }
		}

6.	Abra DAL\\RoleClaimContext.cs y agregue el código resaltado:
	<pre class="prettyprint">
public class RoleClaimContext : DbContext
{
    public RoleClaimContext() : base("RoleClaimContext") { }

    public DbSet&lt;Task> Tasks { get; set; }
    <mark>public DbSet&lt;WorkItem> WorkItems { get; set; }</mark>
    public DbSet&lt;TokenCacheEntry> TokenCacheEntries { get; set; }
}</pre>

7.	Compile el proyecto para que su nuevo modelo sea accesible a la lógica de scaffolding en Visual Studio.

8.	Agregue el nuevo elemento `WorkItemsController` con scaffold a la carpeta Controladores. Para ello, haga clic con el botón derecho en **Controladores**, seleccione **Agregar** y **Nuevo elemento con scaffold**.

9.	Seleccione **Controlador MVC 5 con vistas, usando Entity Framework** y haga clic en **Agregar**.

10.	Seleccione el modelo que acaba de crear y haga clic en **Agregar**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-add-scaffolded-controller.png)

9.	Abrir Controllers\WorkItemsController.cs

11. Agregue las representaciones [Authorize] resaltadas a las acciones respectivas siguientes.
	<pre class="prettyprint">
	...

    <mark>[Authorize(Roles = "Admin, Observer, Writer, Approver")]</mark>
    public class WorkItemsController : Controller
    {
		...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public ActionResult Create()
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult> Create([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult> Edit(int? id)
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult> Edit([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
        ...

        <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
        public async Task&lt;ActionResult> Delete(int? id)
        ...

        <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
        public async Task&lt;ActionResult> DeleteConfirmed(int id)
        ...
	}</pre>
	Como usted se encarga de las asignaciones de roles en la interfaz de usuario del Portal de Azure clásico, lo único que tiene que hacer es asegurarse de que cada acción autoriza los roles adecuados.

	> [AZURE.NOTE]Es posible que haya observado la representación <code>[ValidateAntiForgeryToken]</code> en algunas de las acciones. Debido al comportamiento descrito por [Brock Allen](https://twitter.com/BrockLAllen) en [MVC 4, AntiForgeryToken and Claims](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/) (MVC 4, AntiForgeryToken y notificaciones), su HTTP POST puede generar un error de validación de token antifalsificación porque: 
	> + Azure Active Directory no envía http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, que el token antifalsificación requiere de manera predeterminada. 
	>+ Si Azure Active Directory está sincronizado con directorios con AD FS, la confianza de AD FS tampoco envía de manera predeterminada la notificación de http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, aunque puede configurar AD FS manualmente para enviar esta notificación.
	>Se encargará de esto en el siguiente paso.

12.  En App\_Start\\Startup.Auth.cs, agregue la siguiente línea de código en el método `ConfigureAuth`. Haga clic con el botón secundario en cada error de resolución de nombres para corregirlo.

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	`ClaimTypes.NameIdentifies` especifica la notificación `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, que proporciona Azure Active Directory. Ahora que se ha encargado de la parte de la autorización (en serio, eso no tardó mucho), puede dedicar su tiempo a la funcionalidad real de las acciones.

13.	En Create() y Edit() agregue el siguiente código para que algunas de las variables se pongan a su disposición en el código JavaScript más adelante. Haga clic con el botón secundario en cada error de resolución de nombres para corregirlo.

        ViewData["token"] = AcquireToken(ClaimsPrincipal.Current.FindFirst(Globals.ObjectIdClaimType).Value);
        ViewData["tenant"] = ConfigHelper.Tenant;

13.	El método `AcquireToken()` no está definido aún, por tanto, defínalo en la clase `WorkItemsController` ahora. Haga clic con el botón secundario en cada error de resolución de nombres para corregirlo.

        static string AcquireToken(string userObjectId)
        {
            ClientCredential cred = new ClientCredential(ConfigHelper.ClientId, ConfigHelper.AppKey);
            Claim tenantIdClaim = ClaimsPrincipal.Current.FindFirst(Globals.TenantIdClaimType);
            AuthenticationContext authContext = new AuthenticationContext(String.Format(CultureInfo.InvariantCulture, ConfigHelper.AadInstance, tenantIdClaim.Value), new TokenDbCache(userObjectId));
            AuthenticationResult result = authContext.AcquireTokenSilent(ConfigHelper.GraphResourceId, cred, new UserIdentifier(userObjectId, UserIdentifierType.UniqueId));
            return result.AccessToken;
        }
14.	En Views\WorkItems\Create.cshtml (un elemento con scaffold automático), encuentre el método auxiliar `Html.BeginForm` y modifíquelo de la siguiente manera:
	<pre class="prettyprint">@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>))
{
    @Html.AntiForgeryToken()

    &lt;div class="form-horizontal">
        &lt;h4>WorkItem&lt;/h4>
        &lt;hr />
        @Html.ValidationSummary(true, "", new { @class = "text-danger" })

        &lt;div class="form-group">
            &lt;div class="col-md-10">
                @Html.EditorFor(model => model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type="hidden"</mark> } })
                @Html.ValidationMessageFor(model => model.AssignedToID, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            @Html.LabelFor(model => model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
            &lt;div class="col-md-10">
                @Html.EditorFor(model => model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.AssignedToName, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
            &lt;div class="col-md-10">
                @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            @Html.LabelFor(model => model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
            &lt;div class="col-md-10">
                @Html.EnumDropDownListFor(model => model.Status, htmlAttributes: new { @class = "form-control" })
                @Html.ValidationMessageFor(model => model.Status, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            &lt;div class="col-md-offset-2 col-md-10">
                &lt;input type="submit" value="Create" class="btn btn-default" <mark>id="submit-button"</mark> />
            &lt;/div>
        &lt;/div>
    &lt;/div>

    <mark>&lt;script>
            // Código de selector de personas/grupos
            var maxResultsPerPage = 14;
            var input = document.getElementById("AssignedToName");
            var token = "@ViewData["token"]";
            var tenant = "@ViewData["tenant"]";

            var picker = new AadPicker(maxResultsPerPage, input, token, tenant);

            // Enviar el usuario/grupo seleccionado que se debe asignar.
            $("#submit-button").click({ picker: picker }, function () {
                if (!picker.Selected())
                    return;
                $("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
            });
    &lt;/script></mark>

	}</pre>

	En el script, el objeto AadPicker llama a la [API Graph de Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) para que busque usuarios y grupos que coincidan con la entrada.

15. Abra la [Consola del administrador de paquetes](http://docs.nuget.org/Consume/Package-Manager-Console) y ejecute **Enable-Migrations –EnableAutomaticMigrations**. De forma similar a la opción que seleccionó al publicar la aplicación en Azure, este comando ayuda a actualizar el esquema de base de datos de su aplicación en [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) al depurarlo en Visual Studio.

15. Ahora, ejecute la aplicación en el depurador de Visual Studio o publique de nuevo en Aplicaciones web del Servicio de aplicaciones. Inicie sesión como el propietario de la aplicación y navegue hasta `https://<webappname>.azurewebsites.net/WorkItems/Create`. Verá que ahora que puede elegir un usuario o grupo de Azure Active Directory en la lista desplegable o escribir algo para filtrar la lista.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-workitem.png)

16. Complete el resto del formulario y haga clic en **Crear**. La página ~/WorkItems/Index muestra ahora el elemento de trabajo recién creado. También observará en la siguiente captura de pantalla que eliminé la columna `AssignedToID` en Views\\WorkItems\\Index.cshtml.

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-workitem-index.png)

11.	Ahora, realice cambios similares en la vista **Editar**. En Views\\WorkItems\\Edit.cshtml, realice los cambios en el método auxiliar `Html.BeginForm` que sean idénticos a los cambios en Views\\WorkItems\\Create.cshtml del paso anterior (reemplace "Create" por "Edit" en el código resaltado anterior).

Eso es todo.

Ahora que ha configurado las autorizaciones y la funcionalidad de línea de negocio para las distintas acciones en el controlador de elementos de trabajo, puede intentar iniciar sesión como usuarios de diferentes roles de aplicación para ver cómo responde la aplicación.

![](./media/web-sites-dotnet-lob-application-azure-ad/11-edit-unauthorized.png)

<a name="bkmk_resources"></a>
## Recursos adicionales

- [Protección de la aplicación con SSL y el atributo Authorize](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Usar Active Directory para la autenticación en Servicio de aplicaciones de Azure](web-sites-authentication-authorization.md)
- [Crear una aplicación web de .NET MVC en Servicio de aplicaciones de Azure con autenticación de AD FS](web-sites-dotnet-lob-application-adfs.md)
- [Ejemplos y documentación de Microsoft Azure Active Directory](https://github.com/AzureADSamples)
- [El blog de Vittorio Bertocci](http://blogs.msdn.com/b/vbertocci/)
- [Migrar un proyecto web de VS2013 de WIF a Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Las nuevas conexiones híbridas de Azure no #hybridCloud de tu padre](/documentation/videos/new-hybrid-connections-not-your-fathers-hybridcloud/)
- [Similitudes entre Active Directory y Azure Active Directory](http://technet.microsoft.com/library/dn518177.aspx)
- [Sincronización de directorios con el escenario de inicio de sesión único](http://technet.microsoft.com/library/dn441213.aspx)
- [Tipos de notificaciones y tokens admitidos de Azure Active Directory](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!----HONumber=AcomDC_1217_2015-->