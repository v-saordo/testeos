<properties 
	pageTitle="Crear una aplicación ASP.NET MVC con la autenticación y Base de datos SQL e implementar al Servicio de aplicaciones de Azure" 
	description="Obtenga información sobre cómo desarrollar una aplicación ASP.NET MVC 5 con una base de datos SQL back-end, agregar autenticación y autorización e implementarla en Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="riande"/>

# Crear una aplicación ASP.NET MVC con la autenticación y Base de datos SQL e implementar al Servicio de aplicaciones de Azure

Este tutorial muestra cómo desarrollar una aplicación web ASP.NET MVC 5 segura que permita a los usuarios iniciar sesión con las credenciales de Facebook o Google. La aplicación es una simple lista de contactos que utiliza ADO.NET Entity Framework para el acceso a bases de datos. Va a implementar la aplicación en el [Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Cuando acabe el tutorial, tendrá una aplicación web controlada por datos segura que se ejecutará en la nube y utilizará una base de datos en la nube. La ilustración siguiente muestra la página de inicio de sesión de la aplicación final.

![página de inicio de sesión][rxb]

Aprenderá a realizar los siguientes procedimientos:

* Cómo crear un proyecto web de ASP.NET MVC 5 seguro en Visual Studio.
* Cómo autenticar y autorizar a los usuarios que inician sesión con credenciales de sus cuentas de Google o Facebook (autenticación de proveedor social mediante [OAuth 2.0](http://oauth.net/2 "http://oauth.net/2")).
* Cómo autenticar y autorizar a los usuarios que se registran en una base de datos administrada por la aplicación (autenticación local mediante [ASP.NET Identity](http://asp.net/identity/)).
* Cómo utilizar ADO.NET Entity Framework 6 Code First para leer y escribir datos en una base de datos SQL.
* Cómo usar Migraciones de Entity Framework Code First para implementar una base de datos.
* Cómo almacenar datos relacionales en la nube mediante el uso de Base de datos SQL de Azure.
* Cómo implementar un proyecto web que usa una base de datos en una [aplicación web](http://go.microsoft.com/fwlink/?LinkId=529714) en el Servicio de aplicaciones de Azure.

>[AZURE.NOTE] Este es un tutorial largo. Si desea una rápida introducción a los proyectos web del Servicio de aplicaciones de Azure y Visual Studio, consulte [Creación de una aplicación web ASP.NET en el Servicio de aplicaciones de Azure](web-sites-dotnet-get-started.md). Para obtener información sobre la solución de problemas, consulte la sección [Solución de problemas](#troubleshooting).
>
>O bien, si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Pruebe el Servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Requisitos previos

Necesita una cuenta de Microsoft Azure para completar este tutorial. Si aún no la tiene, puede [activar los beneficios de suscripción a Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) o bien [registrarse para obtener una evaluación gratuita](/pricing/free-trial/?WT.mc_id=A261C142F).

Para configurar el entorno de desarrollo, tiene que instalar [Visual Studio 2013 Update 5](http://go.microsoft.com/fwlink/?LinkId=390521) o superior y la versión más reciente del [SDK de Azure para .NET](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409). Este artículo se escribió para Visual Studio Update 4 y SDK 2.8.1. Las mismas instrucciones funcionan para Visual Studio 2015 con la versión más reciente del [SDK de Azure para .NET](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409) instalado, aunque algunas pantallas serán diferentes de las ilustraciones.

## Creación de una aplicación ASP.NET MVC 5

### Creación del proyecto

1. En el menú **Archivo**, haga clic en **Nuevo proyecto**.

	![Nuevo proyecto en el menú Archivo](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/gs13newproj.png)

1. En el cuadro de diálogo **Nuevo proyecto**, expanda **C#** y seleccione **Web** debajo de **Plantillas instaladas**; a continuación, seleccione **Aplicación web ASP.NET**.

1. Asigne a la aplicación el nombre **ContactManager** y después haga clic en **Aceptar**.

	![Cuadro de diálogo Nuevo proyecto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13newprojdb.png)
 
	**Nota:** asegúrese de escribir "ContactManager". En los bloques de código que copiará más tarde se supone que el nombre del proyecto es ContactManager.

1. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione la plantilla **MVC**. Compruebe que la opción **Autenticación** esté establecida en **Cuentas de usuario individuales**, que la casilla **Hospedar en la nube** esté activada y que **Servicio de aplicaciones** esté seleccionado.

	![Cuadro de diálogo New ASP.NET Project](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newproject.png)

1. Haga clic en **Aceptar**.

3. Cuando aparezca el cuadro de diálogo **Establecer configuración de la aplicación web de Microsoft Azure**, asegúrese de que ha iniciado sesión en Azure; inicie sesión si aún no lo ha hecho o, si la sesión ha expirado, vuelva a escribir sus credenciales.

	![Volver a escribir las credenciales](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/reentercredentials.png)

2. Si desea especificar un nombre para la aplicación web, cambie el valor en el cuadro **Nombre de aplicación web**.

	La dirección URL de la aplicación web será {nombre}.azurewebsites.net, por lo que este nombre debe ser único en el dominio azurewebsites.net. El Asistente para configuración sugiere un nombre único compuesto por el nombre del proyecto "ContactManager" con un número anexado, lo que es suficiente para este tutorial.

5. En el menú desplegable **Plan de servicio de aplicaciones**, seleccione **Crear nuevo plan de servicio de aplicaciones** y escriba un nombre, por ejemplo, "StandardWeb" como se muestra en la ilustración.

	Si lo prefiere, puede seleccionar un plan de Servicio de aplicaciones que ya tenga. Para obtener más información sobre los planes de Servicio de aplicaciones, consulte [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. En el menú desplegable **Grupo de recursos**, seleccione **Crear nuevo grupo de recursos** y escriba un nombre, como "ExampleMVC", tal como se muestra en la ilustración.

	Si lo prefiere, puede seleccionar un grupo de recursos que ya tenga. No obstante, si crea un nuevo grupo de recursos y solo lo usa para este tutorial, será fácil eliminar todos los recursos de Azure que creó para el tutorial cuando haya terminado con ellos. Para obtener más información sobre los grupos de recursos, consulte [Información general del Administrador de recursos de Azure](../resource-group-overview.md).

7. Seleccione una región cerca de usted.

	No haga clic en **Aceptar** todavía. En el siguiente paso, configurará el recurso de base de datos. El cuadro de diálogo ahora tiene el aspecto de la siguiente ilustración.

	![Nuevo plan de y grupo de recursos](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newplanandgroup.png)
 
2. Seleccione **Crear nuevo servidor** y escriba un nombre de servidor, un nombre de usuario y una contraseña.

	El nombre de servidor debe ser único. Puede contener minúsculas, números y guiones, pero no un guión al final. El nombre de usuario y la contraseña son nuevas credenciales que se crean para el nuevo servidor.

	Si ya tiene un servidor de base de datos, puede seleccionarlo en lugar de crear uno. Los servidores de base de datos son un valioso recurso y, por lo general, deseará crear varias bases de datos en el mismo servidor de pruebas y desarrollo en lugar de crear un servidor de base de datos por base de datos. Sin embargo, para este tutorial solo necesita el servidor temporalmente y, al crear el servidor en el mismo grupo de recursos que el sitio web, resulta más fácil eliminar los recursos de base de datos y de aplicación web con solo borrar el grupo de recursos cuando haya terminado con el tutorial.

	Si selecciona un servidor de base de datos existente, asegúrese de que su aplicación web y la base de datos estén en la misma región.

	![Uso de base de datos nueva](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/newdb.png)

4. Haga clic en **Aceptar**.

	Se crea el proyecto web ContactManager en Visual Studio, se crea el grupo de recursos y el plan de Servicio de aplicaciones que ha especificado y se crea una aplicación web en Servicio de aplicaciones de Azure con el nombre especificado.

### Establecimiento del encabezado y pie de página

1. En el **Explorador de soluciones**, abra el archivo *Layout.cshtml* de la carpeta *Views\\Shared*.

	![\_Layout.cshtml in Solution Explorer][newapp004]

1. Reemplace el contenido del archivo *Layout.cshtml* por el código siguiente.

		<!DOCTYPE html>
		<html>
		<head>
		    <meta charset="utf-8" />
		    <meta name="viewport" content="width=device-width, initial-scale=1.0">
		    <title>@ViewBag.Title - Contact Manager</title>
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		
		</head>
		<body>
		    <div class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		                @Html.ActionLink("CM Demo", "Index", "Cm", new { area = "" }, new { @class = "navbar-brand" })
		            </div>
		            <div class="navbar-collapse collapse">
		                <ul class="nav navbar-nav">
		                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
		                    <li>@Html.ActionLink("About", "About", "Home")</li>
		                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
		                </ul>
		                @Html.Partial("_LoginPartial")
		            </div>
		        </div>
		    </div>
		    <div class="container body-content">
		        @RenderBody()
		        <hr />
		        <footer>
		            <p>&copy; @DateTime.Now.Year - Contact Manager</p>
		        </footer>
		    </div>
		
		    @Scripts.Render("~/bundles/jquery")
		    @Scripts.Render("~/bundles/bootstrap")
		    @RenderSection("scripts", required: false)
		</body>
		</html>

	Este código cambia el nombre de la aplicación en el encabezado y el pie de página de "My ASP.NET Application" y "Application name" a "Contact Manager" y "CM Demo".
 
### Ejecución de la aplicación de forma local

1. Presione CTRL+F5 para ejecutar la aplicación.

	Aparece la página principal de la aplicación en el explorador predeterminado.

	![Aplicación web que se ejecuta localmente](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr2.png)

Esto es todo lo que necesita hacer por ahora para crear la aplicación que va a implementar en Azure.

## Implementación de la aplicación en Azure

1. En Visual Studio, haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones** y seleccione **Publicar** en el menú contextual.

	![Opción Publicar del menú contextual del proyecto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
	Se abre el asistente para **publicación web**.

1. En el cuadro de diálogo **Publicación web**, haga clic en **Publicar**.

	![Publicar](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr3.png)

	La aplicación creada ahora se ejecuta en la nube. La próxima vez que implemente la aplicación, solo se implementarán los archivos modificados o nuevos.

	![Ejecutar en la nube](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss4.PNG)

## Habilitación de SSL para el proyecto ##

1. En el **Explorador de soluciones**, haga clic en el proyecto **ContactManager** y después pulse F4 para mostrar la ventana **Propiedades**.

3. Cambie **SSL habilitado** a **Verdadero**.

4. Copie la **URL de SSL**.

	Esta dirección URL será https://localhost:44300/ a menos que haya creado antes aplicaciones web con SSL.

	![habilitar SSL][rxSSL]
 
1. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto **Contact Manager** y elija **Propiedades**.

1. Haga clic en la pestaña **Web**.

1. Cambie la **URL del proyecto** por la **dirección URL de SSL** y guarde la página (Control S).

	![habilitar SSL](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr1.png)
 
1. Compruebe que Internet Explorer sea el explorador que inicia Visual Studio, como se muestra en la imagen siguiente:

	![explorador predeterminado](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss12.PNG)

	El selector de explorador le permite especificar el explorador que inicia Visual Studio. Puede seleccionar varios exploradores y hacer que Visual Studio actualice cada explorador cuando haga cambios. Para obtener más información, vea [Uso de vínculos del explorador en Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).

 	![selector de explorador](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss13.png)

1. Presione CTRL+F5 para ejecutar la aplicación. Haga clic en **Sí** para iniciar el proceso de confianza en el certificado autofirmado¿ que IIS Express generó.

	 ![instrucciones para confiar en el certificado autofirmado que ha generado IIS Express.](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss26.PNG)

1. Lea el cuadro de diálogo **Advertencia de seguridad** y haga clic en **Sí** si desea instalar el certificado que representa **localhost**.

 	![advertencia del certificado localhost de IIS Express](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss27.PNG)

1. IE muestra la página *Inicio*, sin ninguna advertencia de SSL.

	 ![IE con SSL de localhost y sin advertencias](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss28.PNG)

	 Internet Explorer es una buena elección cuando se usa SSL porque acepta el certificado y muestra el contenido HTTPS sin advertencia. Microsoft Edge y Google Chrome también aceptan el certificado. Firefox usa su propio almacén de certificados, así que mostrará una advertencia.

	 ![Advertencia de certificado de FireFox](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss30.PNG)

## Incorporación de una base de datos a la aplicación

A continuación, actualizará la aplicación para agregar la capacidad para mostrar y actualizar los contactos y almacenar los datos en una base de datos. La aplicación utilizará Entity Framework (EF) para crear la base de datos y para leer y actualizar datos.

### Incorporación de clases de modelos de datos para los contactos

Se empieza por crear un modelo de datos sencillo en código.

1. En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta Models, a continuación en **Agregar** y, por último, en **Clase**.

	![Agregar clase en el menú contextual de la carpeta Models](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr5.png)

2. En el cuadro de diálogo **Agregar nuevo elemento**, ponga al archivo de la nueva clase el nombre *Contact.cs* y, a continuación, haga clic en **Agregar**.

	![Cuadro de diálogo Add New Item][adddb002]

3. Reemplace el contenido del archivo Contact.cs por el código siguiente.

        using System.ComponentModel.DataAnnotations;
        using System.Globalization;
        namespace ContactManager.Models
        {
            public class Contact
            {
                public int ContactId { get; set; }
                public string Name { get; set; }
                public string Address { get; set; }
                public string City { get; set; }
                public string State { get; set; }
                public string Zip { get; set; }
                [DataType(DataType.EmailAddress)]
                public string Email { get; set; }
            }
        }
La clase **Contact** define qué datos se almacenarán para cada contacto, así como una clave principal, *ContactID*, necesaria para la base de datos.

### Creación de páginas web que permiten a los usuarios de aplicaciones utilizar los contactos

La característica de scaffolding de ASP.NET MVC puede generar automáticamente código que realiza acciones de creación, lectura, actualización y eliminación (CRUD).

1. Creación del proyecto **(Ctrl+Mayús+B)**. (Debe crear el proyecto antes de usar el mecanismo de scaffolding).
 
1. En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta Controladores, a continuación en **Agregar** y, por último, en **Controlador**.

	![Agregar controlador en el menú contextual de la carpeta Controllers][addcode001]

5. En el cuadro de diálogo **Agregar scaffold**, seleccione **Controlador MVC 5 con vistas, con EF** y, a continuación, haga clic en **Agregar**.
	
	![Cuadro de diálogo Add Scaffold](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rr6.png)

1. En el cuadro desplegable **Clase de modelo**, seleccione **Contact (ContactManager.Models)**. (Consulte la imagen que aparece a continuación).

1. En **Clase de contexto de datos**, seleccione **ApplicationDbContext (ContactManager.Models)**. El valor **ApplicationDbContext** se utilizará tanto para la base de datos de suscripciones como para los datos de nuestros contactos.

1. En el cuadro de la entrada de texto **Nombre de controlador**, escriba "CmController" como nombre del controlador.

	![Cuadro de diálogo New Data Context](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss5.PNG)

1. Haga clic en **Agregar**.

   Visual Studio crea un controlador con métodos y vistas para las operaciones CRUD de base de datos que afectan a los objetos **Contact**.

## Habilitación de las migraciones, creación de la base de datos e incorporación de datos de ejemplo y un inicializador de datos ##

La siguiente tarea consiste en habilitar la función [Migraciones de Code First](http://msdn.microsoft.com/library/hh770484.aspx) para crear tablas de base de datos a partir del modelo de datos ya creado.

1. En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** y después **Consola del Administrador de paquetes**.

	![Package Manager Console en el menú Herramientas](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/SS6.png)

2. En la ventana **Consola del Administrador de paquetes**, escriba el siguiente comando:

		enable-migrations

	El comando **enable-migrations** crea una carpeta *Migrations* y guarda en ella un archivo *Configuration.cs* que puede editar para inicializar la base de datos y configurar las migraciones.

2. En la ventana **Consola del Administrador de paquetes**, escriba el siguiente comando:

		add-migration Initial


	El comando **add-migration Initial** genera un archivo llamado **&lt;fecha&gt;Initial** en la carpeta *Migrations*. El código de este archivo crea las tablas de base de datos. El primer parámetro (**Initial**) se usa para crear el nombre del archivo. Puede ver los archivos de las nuevas clases en el **Explorador de soluciones**.

	En la clase **Initial**, el método **Up** crea la tabla Contacts y el método **Down** (que se utiliza cuando se desea volver al estado anterior) la anula.

3. Abra el archivo *Migrations\\Configuration.cs*.

4. Agregue la siguiente instrucción `using`.

    	 using ContactManager.Models;

5. Reemplace el método *Seed* por el código siguiente:

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                }
                );
        }

	Este código inicializa la base de datos con la información de contactos. Para obtener más información acerca de la inicialización de la base de datos, consulte [Inicialización y depuración de bases de datos de Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).

6. En **Consola del Administrador de paquetes**, escriba el comando:

		update-database

	![Comandos de Package Manager Console][addcode009]

	El comando **update-database** ejecuta la primera migración, lo que crea la base de datos. De manera predeterminada, la base de datos que se crea es una base de datos LocalDB de SQL Server Express.

7. Pulse CTRL+F5 para ejecutar la aplicación y, a continuación, haga clic en el enlace C**CM Demo**; o bien visite https://localhost:(port#)/Cm.

	La aplicación muestra los datos de inicialización y ofrece enlaces de edición, detalles y eliminación. Puede crear, editar, eliminar y ver los datos.

	![Vista MVC de los datos][rx2]

## Incorporación de un proveedor de OAuth2

>[AZURE.NOTE] Para obtener instrucciones detalladas sobre cómo usar los sitios de portal de desarrollador de Google y Facebook, este tutorial le ofrece vínculos a tutoriales en el sitio ASP.NET. De todas formas, los cambios en los sitios de Google y Facebook se producen con más frecuencia que la actualización de los tutoriales y estos son ahora obsoletos. Si tiene problemas para seguir las instrucciones, consulte los comentarios al final de este tutorial para obtener una lista de lo que ha cambiado.

[OAuth](http://oauth.net/ "http://oauth.net/") es un protocolo abierto que ofrece autorización segura a través de un método estándar sencillo para aplicaciones web, móviles y de escritorio. La plantilla de Internet ASP.NET MVC utiliza OAuth para ofrecer Facebook, Twitter, Google y Microsoft como proveedores de autenticación. Aunque este tutorial solo utiliza Google como proveedor de autenticación, puede modificar fácilmente el código para utilizar cualquiera de los proveedores. Los pasos necesarios para implementar otros proveedores son muy similares a los que verá en este tutorial. Para usar Facebook como proveedor de autenticación, consulte la página sobre la [aplicación MVC 5 con inicio de sesión OAuth2 de Facebook, Twitter, LinkedIn y Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).

Además de la autenticación, este tutorial también usa roles para implementar la autorización. Los únicos usuarios que pueden cambiar los datos (es decir, crear, editar o eliminar contactos) serán aquellos que agregue al rol *canEdit*.

1. Siga las instrucciones en [aplicación MVC 5 App con inicio de sesión OAuth2 en Facebook, Twitter, LinkedIn y Google](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog) en el apartado **Creación y configuración de una aplicación de Google para OAuth2**.

3. Ejecute y pruebe la aplicación para asegurarse de que puede iniciar sesión con la autenticación de Google.

2. Si desea crear botones de inicio de sesión a través de redes sociales con iconos específicos del proveedor, consulte la página sobre [botones de inicio de sesión a través de redes sociales para ASP.NET MVC 5](http://www.jerriepelser.com/blog/pretty-social-login-buttons-for-asp-net-mvc-5).

## Uso de la API de suscripción

En esta sección, agregará un usuario local y el rol *canEdit* a la base de datos de suscripciones. Únicamente los usuarios incluidos en el rol *canEdit* podrán editar los datos. Es recomendable nombrar los roles en función de las acciones que pueden realizar, por lo que el nombre *canEdit* es más aconsejable que *admin*. A medida que la aplicación evoluciona, puede agregar nuevos roles del tipo *canDeleteMembers*, en lugar de *superAdmin*, que no es muy descriptivo.

1. Abra el archivo *migrations\\configuration.cs* y agregue las siguientes instrucciones `using`:

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. Agregue el siguiente método **AddUserAndRole** a la clase:

		bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
		{
		    IdentityResult ir;
		    var rm = new RoleManager<IdentityRole>
		        (new RoleStore<IdentityRole>(context));
		    ir = rm.Create(new IdentityRole("canEdit"));
		    var um = new UserManager<ApplicationUser>(
		        new UserStore<ApplicationUser>(context));
		    var user = new ApplicationUser()
		    {
		        UserName = "user1@contoso.com",
		    };
		    ir = um.Create(user, "P_assw0rd1");
		    if (ir.Succeeded == false)
		        return ir.Succeeded;
		    ir = um.AddToRole(user.Id, "canEdit");
		    return ir.Succeeded;
		}

1. Llame al nuevo método desde el método **Seed**:

		protected override void Seed(ContactManager.Models.ApplicationDbContext context)
		{
		    AddUserAndRole(context);
		    context.Contacts.AddOrUpdate(p => p.Name,
		        // Code removed for brevity
		}

	En las imágenes siguientes, se muestran los cambios en el método *Seed*:

	![imagen de código](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss24.PNG)

	Este código crea un nuevo rol llamado *canEdit*, crea un nuevo usuario local *user1@contoso.com* y agrega *user1@contoso.com* al rol *canEdit*. Para obtener más información, consulte los [tutoriales de ASP.NET Identity](http://www.asp.net/identity/overview/features-api) en el sitio de ASP.NET.

## Uso de código temporal para agregar a los nuevos usuarios que inician sesión a través de redes sociales al rol canEdit  ##

En esta sección, modificará temporalmente el método **ExternalLoginConfirmation** del controlador de cuentas para agregar a los nuevos usuarios que se registran con un proveedor de OAuth en el rol *canEdit*. En el futuro, esperamos ofrecer una herramienta similar a [WSAT](http://msdn.microsoft.com/library/ms228053.aspx) que le permita crear y editar cuentas y roles de usuario. Hasta entonces, puede realizar la misma función mediante código temporal.

1. Abra el archivo **Controllers\\AccountController.cs** y desplácese hasta el método **ExternalLoginConfirmation**.

1. Agregue la siguiente llamada a **AddToRoleAsync** justo antes de la llamada a **SignInAsync**.

		await UserManager.AddToRoleAsync(user.Id, "canEdit");

   El código anterior agrega el usuario recién registrado al rol “canEdit”, lo que le permite obtener acceso a los métodos de acción que cambian (editan) los datos. El siguiente fragmento muestra la nueva línea de código en contexto.

		  // POST: /Account/ExternalLoginConfirmation
		  [HttpPost]
		  [AllowAnonymous]
		  [ValidateAntiForgeryToken]
		  public async Task ExternalLoginConfirmation(ExternalLoginConfirmationViewModel model, string returnUrl)
		  {
		     if (User.Identity.IsAuthenticated)
		     {
		        return RedirectToAction("Index", "Manage");
		     }
		     if (ModelState.IsValid)
		     {
		        // Get the information about the user from the external login provider
		        var info = await AuthenticationManager.GetExternalLoginInfoAsync();
		        if (info == null)
		        {
		           return View("ExternalLoginFailure");
		        }
		        var user = new ApplicationUser { UserName = model.Email, Email = model.Email };
		        var result = await UserManager.CreateAsync(user);
		        if (result.Succeeded)
		        {
		           result = await UserManager.AddLoginAsync(user.Id, info.Login);
		           if (result.Succeeded)
		           {
		              await UserManager.AddToRoleAsync(user.Id, "canEdit");
		              await SignInManager.SignInAsync(user, isPersistent: false, rememberBrowser: false);
		              return RedirectToLocal(returnUrl);
		           }
		        }
		        AddErrors(result);
		     }
		     ViewBag.ReturnUrl = returnUrl;
		     return View(model);
		  }

Más adelante en este tutorial implementará la aplicación en Azure, donde iniciará sesión con Google u otro proveedor de autenticación de terceros. De este modo, la cuenta recién registrada se agregará al rol *canEdit*. Cualquiera que encuentre la URL de la aplicación web y tenga un identificador de Google podrá registrarse y actualizar su base de datos. Para evitar que otros usuarios hagan esto, puede detener el sitio. Podrá comprobar quién está incluido en el rol *canEdit* examinando la base de datos.

En **Consola del Administrador de paquetes**, toque la tecla de flecha arriba para que aparezca el siguiente comando:

		Update-Database

El comando **Update-Database** ejecuta el método **Seed**, el cual ejecuta el método **AddUserAndRole** que agregó antes. El método **AddUserAndRole** crea la usuaria **user1@contoso.com* y la agrega al rol *canEdit*.

## Protección de la aplicación con SSL y el atributo Authorize ##

En esta sección, se aplica el atributo [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) para restringir el acceso a los métodos de acción. Los usuarios anónimos solo podrán ver el método de acción **Index** del controlador principal. Los usuarios registrados verán los datos de contacto (páginas **Index** y **Details** del controlador Cm) y las páginas About y Contact. Únicamente los usuarios incluidos en el rol *canEdit* podrán obtener acceso a los métodos de acción que cambian datos.

1. Abra el archivo *App\_Start\\FilterConfig.cs* y reemplace el método *RegisterGlobalFilters* con el siguiente código (que agrega los dos filtros):

		public static void
		RegisterGlobalFilters(GlobalFilterCollection filters)
		{
		    filters.Add(new HandleErrorAttribute());
		    filters.Add(new System.Web.Mvc.AuthorizeAttribute());
		    filters.Add(new RequireHttpsAttribute());
		}
		
	Este código agrega el filtro [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) y el filtro [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) a la aplicación. El filtro [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) impide que los usuarios anónimos accedan a ningún método de la aplicación. Utilizará el atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) para cancelar el requisito de autorización en un par de métodos, de tal forma que los usuarios anónimos puedan iniciar sesión y ver la página principal. El atributo [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) requiere que todo acceso a la aplicación web se realice a través de HTTPS.

	Otra opción es agregar el atributo [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) y el atributo [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) a cada controlador, pero por motivos de seguridad es recomendable aplicarlos a toda la aplicación. De esta manera, todos los controladores y métodos de acción nuevos que agregue están automáticamente protegidos, sin necesidad de aplicar los atributos otra vez. Para obtener más información, consulte [Protección de su aplicación ASP.NET MVC y el nuevo atributo AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx).

1. Agregue el atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) al método **Index** del controlador principal. El atributo [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx) le permite incluir en una lista blanca los métodos que desee excluir de la autorización.

		public class HomeController : Controller
		{
		  [AllowAnonymous]
		  public ActionResult Index()
		  {
		     return View();
		  }

	Si realiza una búsqueda global de *AllowAnonymous*; puede ver que se usa en los métodos de inicio de sesión y registro del controlador de cuentas.

1. En *CmController.cs*, agregue `[Authorize(Roles = "canEdit")]` a los métodos HttpGet y HttpPost que cambian los datos (todos los métodos de acción [Create, Edit, Delete] excepto Index y Details) del controlador *Cm*. A continuación, se muestra un fragmento del código final:

		// GET: Cm/Create
		[Authorize(Roles = "canEdit")]
		public ActionResult Create()
		{
		   return View(new Contact { Address = "123 N 456 W",
		    City="Great Falls", Email = "ab@cd.com", Name="Joe Smith", State="MT",
		   Zip = "59405"});
		}
		// POST: Cm/Create
		// To protect from overposting attacks, please enable the specific properties you want to bind to, for 
		// more details see http://go.microsoft.com/fwlink/?LinkId=317598.
		[HttpPost]
		[ValidateAntiForgeryToken]
		 [Authorize(Roles = "canEdit")]
		public ActionResult Create([Bind(Include = "ContactId,Name,Address,City,State,Zip,Email")] Contact contact)
		{
		    if (ModelState.IsValid)
		    {
		        db.Contacts.Add(contact);
		        db.SaveChanges();
		        return RedirectToAction("Index");
		    }
		    return View(contact);
		}
		// GET: Cm/Edit/5
		[Authorize(Roles = "canEdit")]
		public ActionResult Edit(int? id)
		{
		    if (id == null)
		    {
		        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
		    }
		    Contact contact = db.Contacts.Find(id);
		    if (contact == null)
		    {
		        return HttpNotFound();
		    }
		    return View(contact);
		}
		
1. Presione CTRL+F5 para ejecutar la aplicación.

1. Si todavía tiene abierta una sesión anterior, haga clic en el enlace **Cerrar sesión**.

1. Haga clic en los enlaces **Acerca de** o **Contacto**. Se le redirigirá a la página de inicio de sesión porque los usuarios anónimos no pueden ver esas páginas.

1. Haga clic en el vínculo **Registrar como nuevo usuario** y agregue un usuario local con el correo electrónico *joe@contoso.com*. Compruebe que *Joe* pueda ver las páginas principal, About y Contact.

	![login](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss14.PNG)

1. Haga clic en el vínculo *CM Demo* y compruebe que ve los datos.

1. Haga clic en un vínculo de edición de la página y se le redirigirá a la página de inicio de sesión (porque no se agrega un nuevo usuario local al rol *canEdit*).

1. Inicie sesión como *user1@contoso.com* con la contraseña "P\_assw0rd1" (el "0" en "word" es un cero). Se le redirigirá a la página de edición que seleccionó anteriormente.

	Si no puede iniciar sesión con esa cuenta y contraseña, pruebe a copiar la contraseña del código fuente y luego pegarla. Si todavía no puede iniciar sesión, consulte la columna **UserName** de la tabla **AspNetUsers** para comprobar que se agregó *user1@contoso.com*.

1. Compruebe que pueda modificar los datos.

## Implementación de la aplicación en Azure

1. En Visual Studio, haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones** y seleccione **Publicar** en el menú contextual.

	![Opción Publicar del menú contextual del proyecto][firsdeploy003]

	Se abre el asistente para **publicación web**.

1. Haga clic en la pestaña **Configuración** a la izquierda del cuadro de diálogo **Publicación web**.

2. Haga clic en el icono **v** para seleccionar la **cadena de conexión remota** de **ApplicationDbContext** y seleccione la base de datos que creó con el proyecto.
   
	![configuración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc2.png)

1. Debajo de **ContactManagerContext**, seleccione **Ejecutar migraciones de Code First**.

	![configuración](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrc3.png)

1. Haga clic en **Publicar**.

1. Inicie sesión como *user1@contoso.com* (con la contraseña "P\_assw0rd1") y compruebe que puede editar los datos.

1. Cierre sesión.

1. Vaya a la [consola de desarrolladores de Google](https://console.developers.google.com/) y, en la pestaña **Credenciales**, actualice los URI de redirección y los orígenes de JavaScript para usar la dirección URL de Azure.

1. Inicie sesión utilizando Google o Facebook. De este modo, se agregará la cuenta de Google o Facebook al rol **canEdit**. Si recibe un error HTTP 400 con el mensaje *El URI de redirección de la solicitud: https://contactmanager{my https://contactmanager {mi versión}.azurewebsites.net/signin-google no coincide con una redirección URI registrada.*, tendrá que esperar hasta que se propaguen los cambios realizados. Si recibe este error después de transcurridos unos minutos, compruebe que los URI son correctos.

### Detención de la aplicación web para evitar que se registren otros usuarios  

1. En el **Explorador de servidores**, vaya a **Azure > Servicio de aplicaciones > {su grupo de recursos} > {su aplicación web}**.

4. Haga clic con el botón derecho en la aplicación web y seleccione **Detener**.

	También existe la opción de seleccionar la hoja de la aplicación web en el [Portal de Azure](https://portal.azure.com/) y después hacer clic en el icono **Detener** situado en la parte superior de la hoja.

	![detener el portal de aplicaciones web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/stopweb.png)

### Eliminación de AddToRoleAsync, Publish y Test

1. Convierta en comentario o elimine el código siguiente del método **ExternalLoginConfirmation** en el controlador de cuentas:

		await UserManager.AddToRoleAsync(user.Id, "canEdit");

1. Desarrolle el proyecto (lo que guarda los cambios del archivo) y compruebe que no haya errores de compilación.

5. Haga clic con el botón derecho en el proyecto, en el **Explorador de soluciones**, y seleccione **Publicar**.

	   ![Opción Publicar del menú contextual del proyecto](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/GS13publish.png)
	
4. Haga clic en el botón **Iniciar vista previa**. Solo se implementan los archivos que deben actualizarse.

5. Inicie la aplicación web desde Visual Studio o el Portal. **No podrá publicar contenido mientras la aplicación web esté detenida**.

	![detener aplicación web](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss15.png)

5. Vuelva a Visual Studio y haga clic en **Publicar**.

3. La aplicación de Azure se abre en el explorador predeterminado. Si ha iniciado sesión, ciérrela para ver la página principal como un usuario anónimo.

4. Haga clic en el enlace **Acerca de**. Se le redirigirá a la página de inicio de sesión.

5. Haga clic en el enlace **Registrar** de la página de inicio de sesión y cree una cuenta local. Utilizaremos esta cuenta local para comprobar que pueda obtener acceso a las páginas de solo lectura pero no a las páginas que cambian datos (están protegidas por el rol *canEdit*). Más adelante en el tutorial eliminará el acceso de cuenta local.

	![Registro](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss16.PNG)

1. Compruebe que pueda desplazarse a las páginas *Acerca de* y *Contacto*.

	![Cerrar sesión](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/ss17.PNG)

1. Haga clic en el enlace **CM Demo** para obtener acceso al controlador **Cm**. Como alternativa, puede anexar *Cm* a la dirección URL.

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr4.png)
 
1. Haga clic en el vínculo de edición.

	Se le redirige a la página de inicio de sesión.

2. En **Usar otro servicio para iniciar sesión**, haga clic en Google o Facebook e inicie sesión con la cuenta que registró anteriormente. (Si está trabajando con rapidez y la cookie de la sesión aún no ha expirado, iniciará sesión automáticamente con la cuenta de Google o Facebook utilizada anteriormente).

2. Compruebe que pueda editar datos mientras esté iniciada la sesión en esa cuenta.

	**Nota:** no puede cerrar sesión en Google desde esta aplicación e iniciar sesión en otra cuenta de Google con el mismo explorador. Si utiliza un solo explorador, debe ir al sitio de Google y cerrar sesión desde allí. Puede iniciar sesión con otra cuenta del mismo autenticador externo (como Google) si utiliza un explorador diferente.

	Si no ha proporcionado aún el nombre y apellido en la información de su cuenta de Google, se producirá una excepción NullReferenceException.

## Examen de la base de datos SQL de Azure ##

1. En el **Explorador de servidores**, vaya a **Azure > Bases de datos SQL > {su base de datos}**.

2. Haga clic con el botón derecho en la base de datos y seleccione **Abrir en el Explorador de objetos de SQL Server**.
 
	![abrir en SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr12.png)
 
3. Si previamente no se ha conectado a esta base de datos, es posible que deba agregar una regla de firewall para permitir el acceso para la dirección IP actual. La dirección IP se rellenará automáticamente. Simplemente, haga clic en **Agregar regla de firewall** para habilitar el acceso.

	![agregar regla de firewall](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/addfirewallrule.png)

3. Inicie sesión en la base de datos con el nombre de usuario y la contraseña que especificó al crear el servidor de base de datos.
 
1. Haga clic con el botón derecho en la tabla **AspNetUsers** y seleccione **Ver datos**.

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rrr8.png)
 
1. Observe que el identificador de la cuenta de Google con la que se registró estará incluido en el rol **canEdit**, al igual que el identificador de *user1@contoso.com*. Estos deberían ser los únicos usuarios del rol **canEdit**. (Esto se comprobará en el paso siguiente).

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/s2.png)
 
2. En el **Explorador de objetos de SQL Server**, haga clic con el botón derecho en **AspNetUserRoles** y seleccione **Ver datos**.

	![Página CM](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rs1.png)
 
3. Compruebe que **UserId** procede de *user1@contoso.com* y la cuenta de Google que registró.

## Solución de problemas

Si se encuentra con problemas, estas son algunas sugerencias de lo que puede probar.

* Errores en el aprovisionamiento de la Base de datos SQL: asegúrese de que tiene instalado el SDK actual. Las versiones anteriores a 2.8.1 presentan un error en algunos escenarios que provocan errores cuando VS intenta crear el servidor de base de datos o la base de datos.
* Mensaje de error: "la operación no se admite para el tipo de oferta de suscripción" al crear recursos de Azure (igual que el anterior).
* Errores al realizar la implementación: considere leer el artículo [Creación de una aplicación web ASP.NET en el Servicio de aplicaciones de Azure](web-sites-dotnet-get-started.md) artículo. Ese escenario de implementación es más sencillo y si tienen el mismo problema, puede ser más fácil de aislar. Por ejemplo, en algunos entornos empresariales un firewall corporativo puede impedir que Web Deploy realice los tipos de conexiones a Azure que necesita.
* Ninguna opción para seleccionar la cadena de conexión en el asistente de publicación web al implementar: si usó un método diferente para crear los recursos de Azure (por ejemplo, si intenta implementar en una aplicación web y una base de datos SQL creada en el Portal), la base de datos SQL puede no estar asociada a la aplicación web. La solución más sencilla es crear una nueva aplicación web y base de datos mediante el uso de VS como se muestra en el tutorial. No tiene que volver al principio del tutorial: en el Asistente de publicación web puede optar por crear una nueva aplicación web y obtendrá el mismo cuadro de diálogo de creación de recursos de Azure que aparece al crear el proyecto.
* Las instrucciones para el portal para desarrolladores de Google o Facebook no están actualizadas, consulte los comentarios al final de este tutorial.

## Pasos siguientes

Ha creado una aplicación web ASP.NET MVC básica que autentica a usuarios. Para obtener más información acerca de las tareas habituales de autenticación y cómo proteger los datos confidenciales, consulte los siguientes tutoriales.

- [Creación de una aplicación web de ASP.NET MVC 5 segura con inicio de sesión, confirmación de correo electrónico y restablecimiento de contraseña](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
- [Aplicación de ASP.NET MVC 5 con autenticación en dos fases de SMS y correo electrónico](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
- [Prácticas recomendadas para implementar las contraseñas y otros datos confidenciales en ASP.NET y Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure) 
- [Creación de una aplicación de ASP.NET MVC 5 con Facebook y Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on) Este tema incluye instrucciones sobre cómo agregar datos de perfil a la base de datos de registro de usuario y para obtener instrucciones detalladas sobre el uso de Facebook como proveedor de autenticación.
- [Introducción a ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Para ver un tutorial más avanzado sobre cómo usar Entity Framework, consulte la página de [introducción a EF y MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application).

Este tutorial es obra de [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)), con la colaboración de Tom Dykstra y Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)).

***Es importante que haga comentarios*** sobre lo que le gustó o lo que le gustaría que mejorásemos, no solo en relación al tutorial en sí, sino a los productos sobre los que trata. Sus comentarios nos ayudarán a clasificar las mejoras por orden de prioridad. También puede solicitar y votar nuevos temas en [Mostrarme cómo con el código](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

## Lo que ha cambiado

* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Using the Membership API]: #mbrDB
[Create a Data Deployment Script]: #ppd
[Update the Membership Database]: #ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/rxSSL.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT2.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rr1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png

[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png

[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png

[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/dntutmobile-migrations-package-manager-console.png


[Important information about ASP.NET in Azure web apps]: #aspnetwindowsazureinfo
[Next steps]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png
 

<!---HONumber=AcomDC_0302_2016-->