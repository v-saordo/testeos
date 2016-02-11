<properties 
	pageTitle="Cree e implemente una aplicación de formularios web ASP.NET con membresía, OAuth y Base de datos SQL a Servicio de aplicaciones de Azure" 
	description="Este tutorial muestra cómo compilar una aplicación de ASP.NET Web Forms 4.5 segura que incorpora una base de datos SQL e implementar la aplicación en Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Erikre" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/10/2015" 
	ms.author="erikre"/>


# Cree e implemente una aplicación de formularios web ASP.NET con membresía, OAuth y Base de datos SQL a Servicio de aplicaciones de Azure

##Información general
Este tutorial muestra cómo compilar una aplicación de ASP.NET Web Forms 4.5 segura que incorpora una base de datos SQL e implementar la aplicación en Azure.

>[AZURE.NOTE] 
Para una versión para MVC de este tutorial, consulte [Creación de una aplicación ASP.NET MVC con la autenticación y Base de datos SQL e implementar al Servicio de aplicaciones de Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).

Puede abrir una cuenta de Azure de manera gratuita y, si todavía no tiene Visual Studio 2013, el SDK instala automáticamente Visual Studio 2013 Express para Web. Puede empezar a desarrollar contenido para Azure sin coste alguno.

En este tutorial se supone que no tiene ninguna experiencia previa con Microsoft Azure. Al terminar el tutorial, tendrá una aplicación web ejecutándose en la nube que usa una base de datos en la nube.

Aprenderá a realizar los siguientes procedimientos:

- Creación de un proyecto de ASP.NET 4.5 Web Forms y publicación en un sitio web de Azure.
- Usar OAuth y las suscripciones a ASP.NET para proteger la aplicación.
- Usar una sola base de datos para datos de suscripción y de aplicación.
- Usar Web Forms Scaffolding para crear páginas web que le permitan modificar datos.
- Utilizar la nueva API de suscripción para agregar usuarios y roles
- Utilizar una Base de datos SQL para almacenar datos en Azure

Va a desarrollar una aplicación web de lista de contactos sencilla basada en ASP.NET 4.5 Web Forms que utiliza Entity Framework para el acceso a la base de datos. La imagen siguiente muestra la página de formularios Web Forms que permiten acceso de lectura y escritura a la base de datos:

![Página Contactos - Editar](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms00.png)

>[AZURE.NOTE] 
Para completar este tutorial, deberá tener una cuenta de Azure. Si aún no la tiene, puede <a href="/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">activar los beneficios de suscripción a Visual Studio</a> o bien <a href="/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">registrarse para obtener una evaluación gratuita</a>. Si desea obtener una introducción a Azure antes de inscribirse para abrir una cuenta, vaya a [Prueba del Servicio de aplicaciones](https://tryappservice.azure.com/), donde puede crear inmediatamente y de forma gratuita un sitio básico de ASP.NET de corta duración en Azure. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

##Configuración del entorno de desarrollo 
Para comenzar, configure el entorno de desarrollo con la instalación de Visual Studio 2013 y el SDK de Azure para .NET.

1. Instale [Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=306566), si aún no lo tiene instalado.  
2. Instale el [SDK de Azure para Visual Studio 2013](http://go.microsoft.com/fwlink/?linkid=324322&clcid=0x409). Este tutorial requiere Visual Studio 2013 antes de instalar el SDK de Azure para Visual Studio 2013. Según la cantidad de dependencias de SDK que tenga ya en la máquina, la instalación del SDK puede tardar un período largo, desde unos minutos a media hora o más.  

3. Si el sistema le pregunta si desea ejecutar o guardar el archivo ejecutable de instalación, haga clic en **Ejecutar**.
4. En la ventana del **Instalador de plataforma web**, haga clic en **Instalar** y continúe con la instalación.  
	![Instalador de plataforma web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-01.png)  

      Si ya tiene el SDK instalado, no habrá ningún elemento para instalar. El número de elementos para instalar se puede ver en la zona inferior izquierda de la ventana **Instalador de plataforma web**.

5. Si aún no tiene **Visual Studio Update 2**, descargue e instale **[Visual Studio 2013 Update 2](http://www.microsoft.com/download/details.aspx?id=42666)** o una versión posterior.

	>[AZURE.NOTE]  
	Debe instalar Visual Studio 2013 Update 2 o posterior para usar Goggle OAuth 2.0 y SSL en modo local sin advertencias. Asimismo, necesita la versión Update 2 para usar Web Forms Scaffolding.

Cuando la instalación se complete, dispondrá de todo lo necesario para iniciar el desarrollo.

##Configuración del entorno de Azure
En esta sección, va a configurar el entorno de Azure con la creación de una base de datos SQL y de Azure.

###Creación de una aplicación web y una Base de datos SQL en Azure 
En este tutorial, su aplicación web se ejecutará en un entorno de hospedaje compartido, es decir, se ejecutará en máquinas virtuales (VM) que se comparten con otras aplicaciones web en Servicio de aplicaciones de Azure. Los entornos de hospedaje compartidos ofrecen un coste reducido a los nuevos usuarios de servicios en la nube. Más adelante, si el tráfico web aumenta, es posible escalar la aplicación para atender la demanda ejecutándola en máquinas virtuales dedicadas. Si necesita una arquitectura más compleja, puede migrar la aplicación a un Servicio en la nube de Azure. Los servicios en la nube se ejecutan en MV dedicadas que se pueden configurar según sus necesidades.

Base de datos SQL de Azure es un servicio de base de datos relacional en la nube que se basa en la tecnología de SQL Server. Las herramientas y aplicaciones que funcionan con SQL Server también funcionan con Base de datos SQL.

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Aplicaciones web** en la pestaña izquierda y después en **Nuevo**. 
![Instalador de plataforma web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-02.png)
2. Haga clic en **Aplicación web** y, a continuación, en **Creación personalizada**. 
![Creación personalizada](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-03.png) Se abre el asistente para la**creación personalizada de una nueva aplicación web**.  

3. En el paso **Crear aplicación web** del asistente, escriba en el cuadro **Dirección URL** el valor que se va a utilizar como URL única de la aplicación. La URL completa consistirá en el valor que escriba más el sufijo que aparece junto al cuadro de texto. La ilustración muestra una dirección URL que probablemente se haya usado, por lo que **debe elegir una dirección URL diferente**.  
	![Contactos: Creación de un nuevo sitio web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-04.png)
4. En la lista desplegable Plan de hospedaje web, elija la región más cercana a su ubicación. Este valor especifica el centro de datos en el que se ejecutará la máquina virtual.
5. En la lista desplegable **Base de datos**, elija **Crear una base de datos SQL de 20 MB gratuita**.
6. En el cuadro **Nombre de la cadena de conexión de BD**, no modifique el valor predeterminado *DefaultConnection*.
7. Haga clic en la flecha situada en la parte inferior del cuadro. El asistente avanza hasta el paso **Especificación de la configuración de la base de datos**.
8. En el cuadro **Nombre**, escriba *`ContactDB`*.  
	![Database Settings](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/Intro-SecureWebForms-05.png)  
9. En el cuadro **Servidor**, seleccione **Nuevo servidor de bases de datos SQL**. Si anteriormente creó una base de datos de SQL Server, también tiene la opción de seleccionar ese SQL Server en el control desplegable.
10. Establezca la opción **Región** en la misma zona en la que creó la aplicación web.
11. Complete los campos **Nombre de inicio de sesión** y **Contraseña** con los datos de un administrador. Si seleccionó **Nuevo servidor de bases de datos SQL**, no debe escribir un nombre y una contraseña existentes en estos campos, sino definir unos nuevos que volverá a utilizar más adelante para obtener acceso a la base de datos. Si seleccionó un SQL Server creado anteriormente, se le pedirá la contraseña de la cuenta de SQL Server que abrió para crearlo. Para este tutorial, no active la casilla **Avanzadas**.
12. Haga clic en la marca de verificación que aparece en la esquina inferior derecha del cuadro para indicar que finalizó la configuración.

El **Portal de Azure clásico** vuelve a la página **Aplicaciones web** y la columna **Estado** muestra el sitio que se va a crear. Poco después (normalmente, menos de un minuto), la columna **Estado** muestra que el sitio se creó correctamente. En la barra de navegación situada a la izquierda, el número de sitios que tiene en su cuenta aparece junto al icono **Aplicación web** y el número de bases de datos, junto al icono **Bases de datos SQL**.
##Creación de una aplicación de ASP.NET Web Forms 
Ya ha creado una aplicación web, pero todavía no hay contenido en él. El siguiente paso consiste en crear la aplicación web de Visual Studio que luego publicará en Azure.
###Creación del proyecto 
1. Seleccione **Nuevo proyecto** desde el menú **Archivo** de Visual Studio.  
	![Menú Archivo: Nuevo proyecto](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms01.png)  
2. Seleccione el grupo de plantillas **Plantillas** -> **Visual C#** -> **Web**, a la izquierda. 
3. Elija la plantilla **Aplicación web ASP.NET** en la columna central.
4. Denomine al proyecto *ContactManager* y haga clic en **Aceptar**.  
	![Cuadro de diálogo Nuevo proyecto](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms02.png)  

      El nombre del proyecto en esta serie de tutoriales es **ContactManager**. Se recomienda utilizar este nombre de proyecto tal cual para que el código proporcionado en el tutorial funcione correctamente.

5. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione la plantilla **Formularios Web Forms**. Desactive la casilla **Host en la nube** si está activada y haga clic en **Aceptar**.  
	![Cuadro de diálogo New ASP.NET Project](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms03.png)  
	Se creará la aplicación de formularios Web Forms.

###Actualización de la página maestra
En ASP.NET Web Forms, las páginas maestro permiten crear un diseño coherente de las páginas en la aplicación. Una sola página maestro define la apariencia y el comportamiento estándar que desea para todas las páginas (o un grupo de páginas) en su aplicación. Después, puede crear páginas de contenido individuales con el contenido que desee mostrar. Cuando los usuarios solicitan las páginas de contenido, ASP.NET las combina con la página maestra para producir un resultado que combine el diseño de la página maestra con el contenido de la página de contenido. El nuevo sitio necesita el nombre de la aplicación y un vínculo actualizado. Este vínculo apuntará a una página que mostrará los datos de contacto. Para realizar estos cambios, debe modificar el código HTML en la página maestro.

1. En el **Explorador de soluciones**, busque y abra la página *Site.Master*. 
2. Si la página está en vista **Diseño**, cambie a la vista **Código fuente**.
3. Actualice la página maestra; para ello, modifique o agregue el marcado para que el marcado de la página aparezca de la manera siguiente:

		<%@ Master Language="C#" AutoEventWireup="true" CodeBehind="Site.master.cs" Inherits="ContactManager.SiteMaster" %>
		
		<!DOCTYPE html>
		
		<html lang="en">
		<head runat="server">
		    <meta charset="utf-8" />
		    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
		    <title><%: Page.Title %> - Contact Manager</title>
		
		    <asp:PlaceHolder runat="server">
		        <%: Scripts.Render("~/bundles/modernizr") %>
		    </asp:PlaceHolder>
		    <webopt:bundlereference runat="server" path="~/Content/css" />
		    <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
		
		</head>
		<body>
		    <form runat="server">
		        <asp:ScriptManager runat="server">
		            <Scripts>
		                <%--To learn more about bundling scripts in ScriptManager see http://go.microsoft.com/fwlink/?LinkID=301884 --%>
		                <%--Framework Scripts--%>
		                <asp:ScriptReference Name="MsAjaxBundle" />
		                <asp:ScriptReference Name="jquery" />
		                <asp:ScriptReference Name="bootstrap" />
		                <asp:ScriptReference Name="respond" />
		                <asp:ScriptReference Name="WebForms.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebForms.js" />
		                <asp:ScriptReference Name="WebUIValidation.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebUIValidation.js" />
		                <asp:ScriptReference Name="MenuStandards.js" Assembly="System.Web" Path="~/Scripts/WebForms/MenuStandards.js" />
		                <asp:ScriptReference Name="GridView.js" Assembly="System.Web" Path="~/Scripts/WebForms/GridView.js" />
		                <asp:ScriptReference Name="DetailsView.js" Assembly="System.Web" Path="~/Scripts/WebForms/DetailsView.js" />
		                <asp:ScriptReference Name="TreeView.js" Assembly="System.Web" Path="~/Scripts/WebForms/TreeView.js" />
		                <asp:ScriptReference Name="WebParts.js" Assembly="System.Web" Path="~/Scripts/WebForms/WebParts.js" />
		                <asp:ScriptReference Name="Focus.js" Assembly="System.Web" Path="~/Scripts/WebForms/Focus.js" />
		                <asp:ScriptReference Name="WebFormsBundle" />
		                <%--Site Scripts--%>
		            </Scripts>
		        </asp:ScriptManager>
		
		        <div class="navbar navbar-inverse navbar-fixed-top">
		            <div class="container">
		                <div class="navbar-header">
		                    <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
		                        <span class="icon-bar"></span>
		                        <span class="icon-bar"></span>
		                        <span class="icon-bar"></span>
		                    </button>
		                    <a class="navbar-brand" runat="server" id="ContactDemoLink" href="~/Contacts/Default.aspx">Contact Demo</a>
		                </div>
		                <div class="navbar-collapse collapse">
		                    <ul class="nav navbar-nav">
		                        <li><a runat="server" href="~/">Home</a></li>
		                        <li><a runat="server" href="~/About">About</a></li>
		                        <li><a runat="server" href="~/Contact">Contact</a></li>
		                    </ul>
		                    <asp:LoginView runat="server" ViewStateMode="Disabled">
		                        <AnonymousTemplate>
		                            <ul class="nav navbar-nav navbar-right">
		                                <li><a runat="server" href="~/Account/Register">Register</a></li>
		                                <li><a runat="server" href="~/Account/Login">Log in</a></li>
		                            </ul>
		                        </AnonymousTemplate>
		                        <LoggedInTemplate>
		                            <ul class="nav navbar-nav navbar-right">
		                                <li><a runat="server" href="~/Account/Manage" title="Manage your account">Hello, <%: Context.User.Identity.GetUserName()  %> !</a></li>
		                                <li>
		                                    <asp:LoginStatus runat="server" LogoutAction="Redirect" LogoutText="Log off" LogoutPageUrl="~/" OnLoggingOut="Unnamed_LoggingOut" />
		                                </li>
		                            </ul>
		                        </LoggedInTemplate>
		                    </asp:LoginView>
		                </div>
		            </div>
		        </div>
		        <div class="container body-content">
		            <asp:ContentPlaceHolder ID="MainContent" runat="server">
		            </asp:ContentPlaceHolder>
		            <hr />
		            <footer>
		                <p>&copy; <%: DateTime.Now.Year %> - Contact Manager</p>
		            </footer>
		        </div>
		    </form>
		</body>
		</html>

	Más adelante en este tutorial, agregará funcionalidad de scaffolding para formularios Web Forms. La función de scaffolding creará la página a la que hace referencia el vínculo "Contact Demo" anterior.
 
###Ejecución de la aplicación de forma local 
 
1. En el **Explorador de soluciones**, haga clic con el botón secundario en la página *Default.aspx* y seleccione **Establecer como página de inicio**. 
2. A continuación, presione **CTRL+F5** para ejecutar la aplicación.  
	La página de aplicación predeterminada aparece en la ventana del explorador predeterminado.  
	![Contactos: Creación de un nuevo sitio web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms04.png)  

Esto es todo lo que necesita hacer por ahora para crear la aplicación que va a implementar en Azure. Más adelante, agregará funcionalidad de base de datos, así como las páginas necesarias para mostrar y editar datos de contacto.
###Implementación de la aplicación en Azure
Ahora que ya ha creado y ejecutado la aplicación de forma local, es el momento de implementarla en Azure.

1. En Visual Studio, haga clic con el botón secundario en el proyecto, en el **Explorador de soluciones** y seleccione **Publicar** en el menú contextual.  
	![Selección de publicación](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms05.png)  
	Aparecerá el cuadro de diálogo **Publicación web**.  

2. En la pestaña **Perfil** del cuadro de diálogo **Publicación web**, haga clic en **Aplicación web de Azure**.
	  
3. Si aún no ha iniciado sesión, haga clic en el botón **Iniciar sesión** en el cuadro de diálogo **Seleccionar aplicación web existente**. Cuando haya iniciado sesión, seleccione la aplicación web que creó en la primera parte de este tutorial. Haga clic en **Aceptar** para continuar. 
![Selección del cuadro de diálogo Sitio web existente](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms07.png) Visual Studio descargará la configuración de publicación.
4. En el cuadro de diálogo **Publicación web**, haga clic en **Publicar**. 
![Cuadro de diálogo Publicación web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms08.png) Podrá ver el estado de publicación global en la ventana **Actividad de publicación web** en Visual Studio: ![Actividad de publicación web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms09.png)  

La aplicación creada ahora se ejecuta en la nube. La próxima vez que implemente la aplicación desde Visual Studio, solo se implementarán los archivos modificados o nuevos.  
	![Aplicación en el explorador](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms10.png)

>[AZURE.NOTE] 
Si se encuentra con un error al publicar en una aplicación web ya establecida, puede desactivar la ubicación antes de agregar los nuevos archivos. Vuelva a publicar su aplicación, pero en el cuadro de diálogo **Publicación web**, seleccione la pestaña **Configuración**. A continuación, establezca la configuración en **Depurar** y seleccione la opción **Quitar archivos adicionales en destino**. Seleccione **Publicar** para implementar de nuevo la aplicación.  
	![Cuadro de diálogo Publicación web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms11.png)

##Incorporación de una base de datos a la aplicación 
Ahora va a actualizar la aplicación de formularios Web Forms para agregarle funcionalidad para mostrar y actualizar contactos, así como almacenar datos en la base de datos predeterminada. Cuando creó el proyecto de formularios Web Forms, la base de datos se creó también de forma predeterminada. La aplicación utilizará Entity Framework para acceder a la base de datos y leer y actualizar los datos que contiene.
###Incorporación de una clase de modelo de datos 
Comience por crear un modelo de datos sencillo con código. Este modelo de datos se incluirá en una clase llamada `Contacts`. El nombre de clase `Contacts` se eligió para evitar un conflicto de nombres de clase con la clase `Contact` contenida en el archivo class Contact.aspx.cs creado por la plantilla de formularios Web Forms.

1. En el **Explorador de soluciones**, haga clic en la carpeta *Models* y, a continuación, seleccione **Agregar** -> **Clase**.  
	![Selección de clase](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms12.png)  
	Se mostrará el cuadro de diálogo **Agregar nuevo elemento**.  

2. Denomine a esta nueva clase *Contacts.cs*.  
	![Cuadro de diálogo Add New Item](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13.png)
3. Reemplace el código predeterminado por el siguiente:  

		using System.ComponentModel.DataAnnotations;
		using System.Globalization;
		
		namespace ContactManager.Models
		{
		    public class Contacts
		    {
		        [ScaffoldColumn(false)]
		        [Key]
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

La clase **Contacts** define qué datos de los contactos va a almacenar, además de una clave primaria (`ContactID`), necesaria para la base de datos. La clase **Contacts** representa los datos del contacto que se mostrarán. Cada instancia de un objeto Contacts corresponde a una fila de una tabla de la base de datos relacional y cada propiedad de la clase Contacts se asigna a una columna de esa tabla. Más adelante en este tutorial, revisará los datos del contacto que contiene la base de datos.

###Web Forms Scaffolding 
En la sección anterior, ha creado la clase de modelos **Contacts**. Ahora, puede usar el Scaffolder de Web Forms para generar las páginas *Enumerar*, *Insertar*, *Editar* y *Eliminar* que se usarán al trabajar con estos datos. El generador de IU de formularios Web Forms utiliza datos de Entity Framework, de arranque y dinámicos. De forma predeterminada, el generador de IU (scaffolder) de formularios Web Forms se instala como una extensión del proyecto, como parte de la plantilla del proyecto cuando se usa Visual Studio 2013.

Los pasos siguientes permiten usar el generador de IU (scaffolder) de formularios Web Forms.

1. En Visual Studio, en la barra de menús, seleccione **Herramientas** -> **Extensiones y actualizaciones**.  
	Aparecerá el cuadro de diálogo **Extensiones y actualizaciones**.
2. En el panel izquierdo del cuadro de diálogo, seleccione **En línea** -> **Galería de Visual Studio** -> **Herramientas** -> **Scaffolding**.
3. Si no ve 'Web Forms Scaffolding' en la lista, escriba 'Web Forms Scaffolding' en el cuadro de búsqueda de la derecha en el cuadro de diálogo.  
4. Si el generador de IU (scaffolder) de formularios Web Forms no está instalado, seleccione **Descargar** para descargar e instalar 'Web Forms Scaffolding'. Reinicie Visual Studio si es necesario. Asegúrese de guardar los cambios en el proyecto cuando se le solicite.  
	![Cuadro de diálogo Extensiones y actualizaciones](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/ExtensionsAndUpdatesDB.png)  
5. Compile el proyecto **(Ctrl+Mayús+B)**. Debe crear el proyecto antes de usar el mecanismo de scaffolding.  
6. En el **Explorador de soluciones**, haga clic en el *proyecto* y, a continuación, seleccione **Agregar** -> **Nuevo elemento de scaffolding**. Aparecerá el cuadro de diálogo **Agregar scaffold**.
7. Seleccione **Web Forms** en el panel izquierdo y elija **Páginas de Web Forms que usan Entity Framework** en el panel central. A continuación, haga clic en **Agregar**.  
	![Cuadro de diálogo de Agregar scaffold](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13a.png)  
	Aparecerá el cuadro de diálogo **Agregar páginas de Web Forms**.  

8. En el cuadro de diálogo **Agregar páginas de Web Forms**, establezca **Clase de modelo** en `Contacts (ContactManager.Models)`. Establezca **Clase de contexto de datos** en `ApplicationDbContext (ContactManager.Models)`. A continuación, haga clic en **Agregar**.  
	![Cuadro de diálogo Agregar páginas de Web Forms](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13b.png)  

El generador de IU (scaffolder) de formularios Web Forms agrega una nueva carpeta que contiene las páginas *Default.aspx*, *Delete.aspx*, *Edit.aspx* e *Insert.aspx* El generador de IU (scaffolder) de formularios Web Forms crea también una carpeta *DynamicData* que contiene una carpeta *EntityTemplates* y una carpeta *FieldTemplates*. `ApplicationDbContext` se usará tanto para la base de datos de suscripciones como para los datos de contactos.

###Configuración de la aplicación para que use el modelo de datos 
La siguiente tarea consiste en habilitar la función Migraciones de Code First para crear la base de datos a partir del modelo de datos ya establecido. También agregará datos de ejemplo y un inicializador de datos.

1. En el menú **Herramientas**, seleccione **Administrador de paquetes NuGet** y, a continuación **Consola del Administrador de paquetes**.  
	![Cuadro de diálogo Agregar páginas de Web Forms](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13c.png).  
2. En la ventana Package Manager Console, escriba el siguiente comando:  

		enable-migrations
 
	El comando enable-migrations crea una carpeta *Migrations* y pone en ella un archivo *Configuration.cs* que puede editar para inicializar la base de datos y configurar migraciones de datos.  
3. En la ventana **Consola del Administrador de paquetes**, escriba el siguiente comando:  

		add-migration Initial

	El comando `add-migration Initial` genera un archivo llamado <date_stamp>Initial en la carpeta *Migrations* que crea la base de datos. El primer parámetro (Initial) es arbitrario y se utiliza para crear el nombre del archivo. Puede ver los archivos de las nuevas clases en el **Explorador de soluciones**. En la clase `Initial`, el método `Up` crea la tabla `Contact` y el método `Down` (que se utiliza cuando se desea volver al estado anterior) la anula.  
4. Abra el archivo *Migrations\Configuration.cs*. 

5. Agregue el siguiente espacio de nombres:

		using ContactManager.Models;

6. Reemplace el método `Seed` por el código siguiente:

		protected override void Seed(ContactManager.Models.ApplicationDbContext context)
		{
		    context.Contacts.AddOrUpdate(p => p.Name,
		       new Contacts
		       {
		           ContactId = 1,
		           Name = "Ivan Irons",
		           Address = "One Microsoft Way",
		           City = "Redmond",
		           State = "WA",
		           Zip = "10999",
		           Email = "ivani@wideworldimporters.com",
		       },
		       new Contacts
		        {
		            ContactId = 2,
		            Name = "Brent Scholl",
		            Address = "5678 1st Ave W",
		            City = "Redmond",
		            State = "WA",
		            Zip = "10999",
		            Email = "brents@wideworldimporters.com",
		        },
		        new Contacts
		        {
		            ContactId = 3,
		            Name = "Terrell Bettis",
		            Address = "9012 State St",
		            City = "Redmond",
		            State = "WA",
		            Zip = "10999",
		            Email = "terrellb@wideworldimporters.com",
		        },
		        new Contacts
		        {
		            ContactId = 4,
		            Name = "Jo Cooper",
		            Address = "3456 Maple St",
		            City = "Redmond",
		            State = "WA",
		            Zip = "10999",
		            Email = "joc@wideworldimporters.com",
		        },
		        new Contacts
		        {
		            ContactId = 5,
		            Name = "Ines Burnett",
		            Address = "7890 2nd Ave E",
		            City = "Redmond",
		            State = "WA",
		            Zip = "10999",
		            Email = "inesb@wideworldimporters.com",
		        }
		        );
		}

	Este código inicializa la base de datos con la información de contacto. Para obtener más información acerca de la inicialización de la base de datos, consulte [Inicialización y depuración de bases de datos de Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).  
7. En **Consola del Administrador de paquetes**, escriba el comando:  

		update-database

`update-database` ejecuta la primera migración que crea la base de datos. De manera predeterminada, la base de datos que se crea es una base de datos LocalDB de SQL Server Express. ![Consola del Administrador de paquetes](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms13d.png)

###Ejecución de la aplicación en modo local y visualización de los datos 
Ejecute ahora la aplicación para ver cómo puede visualizar los contactos.

1. En primer lugar, compile el proyecto (**Ctrl+Mayús+B**).  
2. Presione **Ctrl+F5** para ejecutar la aplicación. El explorador se abrirá y mostrará la página *Default.aspx*.
3. Seleccione el vínculo **Demostración de Contact** en la parte superior de la página para mostrar la página *Lista de Contact*.  
	![Página Lista de contactos](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms17.png)  

##Habilitación de SSL para el proyecto 
Capa de sockets seguros (SSL) es un protocolo definido para permitir a los servidores y clientes web comunicarse de forma más segura mediante el uso de cifrado. Cuando no se usa SSL, cualquiera que tenga acceso físico a la red puede abrir los datos que se envían entre el cliente y el servidor para examinar el paquete. Además, varios esquemas de autenticación habituales no son seguros en HTTP plano. En particular, la autenticación básica y la autenticación mediante formularios envían credenciales no cifradas. Para ser seguros, estos esquemas de autenticación deben usar SSL.

1. En el **Explorador de soluciones**, haga clic en el proyecto **ContactManager** y presione **F4** para mostrar la ventana **Propiedades**. 
2. Cambie **SSL habilitado** a `true`. 
3. Copie la **Dirección URL de SSL** para usarla más adelante.  
	Esta dirección será `https://localhost:44300/` a menos que haya creado antes sitios web con SSL (como se muestra a continuación). ![Project Properties](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms18.png)  
4. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto **Contact Manager** y elija **Propiedades**.
5. En el panel izquierdo, haga clic en **Web**.
6. Cambie la **dirección URL del proyecto** para utilizar la dirección **URL de SSL** que guardó anteriormente.  
	![Propiedades de Project Web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms19.png)  
7. Presione **CTRL+S** para guardar la página.
8. Presione **Ctrl+F5** para ejecutar la aplicación. Visual Studio mostrará una opción que le permite evitar advertencias de SSL.  
9. Haga clic en **Sí** para confiar en el certificado SSL de IIS Express y continúe.  
	![Detalles del certificado SSL de IIS Express](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms20.png)  
	Se mostrará una advertencia de seguridad.  

10. Haga clic en **Sí** para instalar el certificado en el host local.  
	![Cuadro de diálogo Advertencia de seguridad](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21.png)  
	Se mostrará la ventana del explorador.

Puede probar fácilmente la aplicación web de forma local usando SSL.



##Incorporación de un proveedor de OAuth 2.0 
ASP.NET Web Forms proporciona opciones mejoradas para suscripciones y autenticación. Estas mejoras incluyen OAuth. OAuth es un protocolo abierto que ofrece autorización segura a través de un método estándar sencillo para aplicaciones web, móviles y de escritorio. La plantilla de Internet ASP.NET MVC utiliza OAuth para ofrecer Facebook, Twitter, Google y Microsoft como proveedores de autenticación. Aunque este tutorial solo utiliza Google como proveedor de autenticación, puede modificar fácilmente el código para utilizar cualquiera de los proveedores. Los pasos que hay que seguir para implementar otros proveedores son muy similares a los que verá en este tutorial.

Además de la autenticación, este tutorial también utiliza roles para implementar la autorización. Únicamente los usuarios que agregue al rol `canEdit` podrán cambiar datos (crear, editar o eliminar contactos).

Los pasos siguientes permiten agregar un proveedor de autenticación de Google.

1. Abra el archivo *App_Start\Startup.Auth.cs*. 
2. Quite los caracteres de comentario del método `app.UseGoogleAuthentication()` para que tenga el siguiente aspecto:  

		app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
		{
		    ClientId = "",
		    ClientSecret = ""
		});

3. Navegue a la [consola de desarrolladores de Google](https://console.developers.google.com/). Debe iniciar sesión también con su cuenta de correo electrónico de desarrollador de Google (gmail.com). Si no tiene una cuenta de Google, seleccione el vínculo **Crear una cuenta**.  
	A continuación, verá la **consola de desarrolladores de Google**.
	![Consola de desarrolladores de Google](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21a.png)  

4. Haga clic en **Crear proyecto** > **Crear proyecto** y escriba un nombre de proyecto y un id. (puede usar los valores predeterminados). A continuación, haga clic en la **casilla de acuerdo** y en el botón **Crear**. 
![Google: Nuevo proyecto](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21b.png) En unos segundos se creará el nuevo proyecto y el explorador mostrará la nueva página de proyectos.
5. En el menú desplegable **Consola de desarrolladores de Google**, haga clic en **Administrador de la API** y luego en **Credenciales**.
6. Haga clic en **Crear nuevo id. de cliente** en **OAuth**. Se mostrará el cuadro de diálogo **Crear id. de cliente**.
 ![Google: Creación de Id. de cliente](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21c.png)  
7. En el cuadro de diálogo **Crear id. de cliente**, no modifique el valor predeterminado **Aplicación web** para el tipo de aplicación.  
8. Establezca los **Orígenes de JavaScript autorizados** en la dirección URL de SSL que usó anteriormente en este tutorial (**https://localhost:44300/** a menos que haya creado otros proyectos de SSL).  
	Esta dirección URL es el origen de su aplicación. Para este ejemplo, solo proporcionará la dirección URL de prueba del localhost. Sin embargo, puede especificar varias direcciones URL para localhost y producción.  

9. Establezca el **URI de redireccionamiento autorizado** en lo siguiente:

		https://localhost:44300/signin-google  

	Este valor es el URI que utiliza ASP.NET OAuth para comunicarse con el servidor OAuth de Google. Recuerde utilizar la dirección URL de SSL que usó antes (****https://localhost:44300/** a menos que haya creado otros proyectos con SSL).
 
10. Haga clic en el botón **Crear**.
11. En Visual Studio, actualice el método `UseGoogleAuthentication` de la página *Startup.Auth.cs*. Para ello, copie y pegue la información de **Id. de aplicación** y de **Secreto de la aplicación** en el método. Los valores **AppId** y **App Secret** que se muestran a continuación son ejemplos y no funcionarán.  

		using System;
		using Microsoft.AspNet.Identity;
		using Microsoft.AspNet.Identity.EntityFramework;
		using Microsoft.AspNet.Identity.Owin;
		using Microsoft.Owin;
		using Microsoft.Owin.Security.Cookies;
		using Microsoft.Owin.Security.DataProtection;
		using Microsoft.Owin.Security.Google;
		using Owin;
		using ContactManager.Models;
		
		namespace ContactManager
		{
		    public partial class Startup {
		
		        // For more information on configuring authentication, please visit http://go.microsoft.com/fwlink/?LinkId=301883
		        public void ConfigureAuth(IAppBuilder app)
		        {
		            // Configure the db context and user manager to use a single instance per request
		            app.CreatePerOwinContext(ApplicationDbContext.Create);
		            app.CreatePerOwinContext(ApplicationUserManager.Create);
		
		            // Enable the application to use a cookie to store information for the signed in user
		            // and to use a cookie to temporarily store information about a user logging in with a third party login provider
		            // Configure the sign in cookie
		            app.UseCookieAuthentication(new CookieAuthenticationOptions
		            {
		                AuthenticationType = DefaultAuthenticationTypes.ApplicationCookie,
		                LoginPath = new PathString("/Account/Login"),
		                Provider = new CookieAuthenticationProvider
		                {
		                    OnValidateIdentity = SecurityStampValidator.OnValidateIdentity(
		                        validateInterval: TimeSpan.FromMinutes(20),
		                        regenerateIdentity: (manager, user) => user.GenerateUserIdentityAsync(manager))
		                }
		            });
		            // Use a cookie to temporarily store information about a user logging in with a third party login provider
		            app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);
		
		            // Uncomment the following lines to enable logging in with third party login providers
		            //app.UseMicrosoftAccountAuthentication(
		            //    clientId: "",
		            //    clientSecret: "");
		
		            //app.UseTwitterAuthentication(
		            //   consumerKey: "",
		            //   consumerSecret: "");
		
		            //app.UseFacebookAuthentication(
		            //   appId: "",
		            //   appSecret: "");
		
		            app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
		            {
		                ClientId = "000000000000.apps.googleusercontent.com",
		                ClientSecret = "00000000000"
		            });
		        }
		    }
		}

12. Presione **CTRL+F5** para compilar y ejecutar la aplicación. Haga clic en el vínculo **Iniciar sesión**.
13. En **Usar otro servicio para iniciar sesión**, haga clic en **Google**.
 ![Inicio de sesión](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21d.png)  
14. Si tiene que proporcionar credenciales, se le redirigirá al sitio de Google donde podrá especificarlas. 
![Google: Inicio de sesión](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21e.png)  
15. Una vez que haya proporcionado sus credenciales, el sistema le pedirá que le dé permisos a la aplicación web que acaba de crear:
 ![Cuenta de servicio de proyecto predeterminada](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21f.png)  
16. Haga clic en **Aceptar**. Se le redirigirá ahora a la página **Registrar** de la aplicación **ContactManager**, donde puede registrar su cuenta de Google. 
![Registro con una cuenta de Google](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms21g.png)  
17. Tiene la opción de cambiar el nombre de registro de correo electrónico local que usa para su cuenta de Gmail, pero suele ser más práctico mantener el mismo alias de correo electrónico predeterminado (es decir, el que usó para la autenticación). Haga clic en **Iniciar sesión**.

##Uso de la API de suscripción para restringir el acceso 
ASP.NET Identity es el sistema de suscripción utilizado para autenticación cuando se compila una aplicación web ASP.NET. Facilita la integración de los datos de perfil específicos del usuario con los datos de aplicación. Además, ASP.NET Identity permite elegir el modelo de persistencia para perfiles de usuario en la aplicación. Puede almacenar los datos en una base de datos de SQL Server o en otro almacén de datos, incluidos almacenes de datos *NoSQL* como las tablas de almacenamiento de Azure.

Al usar la plantilla predeterminada de ASP.NET Web Forms, dispone de funcionalidad de suscripción integrada que puede usar de forma inmediata cuando se ejecuta la aplicación. Deberá usar ASP.NET Identity para agregar un rol de administrador y asignar un usuario a ese rol. A continuación, verá cómo restringir el acceso a la carpeta de administración y a las páginas de esa carpeta que se usan para modificar los datos de contactos.

###Incorporación de un administrador 
Con ASP.NET Identity, puede agregar un rol de administrador y asignar un usuario a ese rol usando código.

1. En el **Explorador de soluciones**, abra el archivo *Configuration.cs* de la carpeta *Migrations*.
2. Agregue las siguientes instrucciones `using` dentro del espacio de nombres `ContactManger.Migrations`:  

		using Microsoft.AspNet.Identity;
		using Microsoft.AspNet.Identity.EntityFramework;

3. Agregue el siguiente método `AddUserAndRole` a la clase `Configuration` después del método `Seed`:

		public void AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
		{
		    IdentityResult IdRoleResult;
		    IdentityResult IdUserResult;
		
		    var roleStore = new RoleStore<IdentityRole>(context);
		    var roleMgr = new RoleManager<IdentityRole>(roleStore);
		
		    if (!roleMgr.RoleExists("canEdit"))
		    {
		        IdRoleResult = roleMgr.Create(new IdentityRole { Name = "canEdit" });
		    }
		
		    //var userStore = new UserStore<ApplicationUser>(context);
		    //var userMgr = new UserManager<ApplicationUser>(userStore);
		    var userMgr = new UserManager<ApplicationUser>(new UserStore<ApplicationUser>(context));
		
		    var appUser = new ApplicationUser
		    {
		        UserName = "canEditUser@wideworldimporters.com",
		        Email = "canEditUser@wideworldimporters.com"
		    };
		    IdUserResult = userMgr.Create(appUser, "Pa$$word1");
		
		    if (!userMgr.IsInRole(userMgr.FindByEmail("canEditUser@wideworldimporters.com").Id, "canEdit"))
		    {
		      //  IdUserResult = userMgr.AddToRole(appUser.Id, "canEdit");
		        IdUserResult = userMgr.AddToRole(userMgr.FindByEmail("canEditUser@wideworldimporters.com").Id, "canEdit");
		    }
		}

4. Agregue una llamada al método `AddUserAndRole` desde el principio del método `Seed`. Tenga en cuenta que solo se muestra el principio del método `Seed`.

		protected override void Seed(ContactManager.Models.ApplicationDbContext context)
		{
		    AddUserAndRole(context);

5. Tras guardar todos los cambios, ejecute el siguiente comando en la ventana **Consola del Administrador de paquetes**:

		Update-Database

Este código crea un nuevo rol denominado `canEdit` y luego un nuevo usuario local con el correo electrónico de canEditUser@wideworldimporters.com. A continuación, el código agrega canEditUser@wideworldimporters.com al rol `canEdit`. Para obtener más información, consulte la página de recursos de [ASP.NET Identity](http://www.asp.net/identity).

###Restricción del acceso a la carpeta de administración 
La aplicación de ejemplo **ContactManager** permite ver los contactos tanto a usuarios anónimos como a usuarios que han iniciado sesión. Sin embargo, cuando complete esta sección, los usuarios que hayan iniciado sesión y que tengan asignado el rol "canEdit" serán los únicos que podrán modificar los contactos.

Creará una carpeta denominada *Admin* a la que solo tienen acceso los usuarios que tienen asignado el rol "canEdit".

1. En el **Explorador de soluciones**, agregue una subcarpeta a la carpeta *Contacts* y póngale el nombre *Admin*.
2. Mueva los archivos siguientes de la carpeta *Contacts* a la carpeta *Contacts/Admin*:  
	- *Delete.aspx *y* Delete.aspx.cs*
	- *Edit.aspx *y* Edit.aspx.cs*
	- *Insert.aspx *y* Insert.aspx.cs*
3. Actualice las referencias de vínculo de *Contacts/Default.aspx* agregando "Admin/" antes de las referencias de páginas que se vinculan a *Insert.aspx*, *Edit.aspx* y *Delete.aspx*, tal como aparece a continuación:  

		<%@ Page Title="ContactsList" Language="C#" MasterPageFile="~/Site.Master" CodeBehind="Default.aspx.cs" Inherits="ContactManager.Contacts.Default" ViewStateMode="Disabled" %>
		<%@ Register TagPrefix="FriendlyUrls" Namespace="Microsoft.AspNet.FriendlyUrls" %>
		
		<asp:Content runat="server" ContentPlaceHolderID="MainContent">
		    <h2>Contacts List</h2>
		    <p>
		        <asp:HyperLink runat="server" NavigateUrl="Admin/Insert.aspx" Text="Create new" />
		    </p>
		    <div>
		        <asp:ListView runat="server"
		            DataKeyNames="ContactId" ItemType="ContactManager.Models.Contacts"
		            AutoGenerateColumns="false"
		            AllowPaging="true" AllowSorting="true"
		            SelectMethod="GetData">
		            <EmptyDataTemplate>
		                There are no entries found for Contacts
		            </EmptyDataTemplate>
		            <LayoutTemplate>
		                <table class="table">
		                    <thead>
		                        <tr>
		                            <th>Name</th>
		                            <th>Address</th>
		                            <th>City</th>
		                            <th>State</th>
		                            <th>Zip</th>
		                            <th>Email</th>
		                            <th>&nbsp;</th>
		                        </tr>
		                    </thead>
		                    <tbody>
		                        <tr runat="server" id="itemPlaceholder" />
		                    </tbody>
		                </table>
		            </LayoutTemplate>
		            <ItemTemplate>
		                <tr>
		                    <td>
		                        <asp:DynamicControl runat="server" DataField="Name" ID="Name" Mode="ReadOnly" />
		                    </td>
		                    <td>
		                        <asp:DynamicControl runat="server" DataField="Address" ID="Address" Mode="ReadOnly" />
		                    </td>
		                    <td>
		                        <asp:DynamicControl runat="server" DataField="City" ID="City" Mode="ReadOnly" />
		                    </td>
		                    <td>
		                        <asp:DynamicControl runat="server" DataField="State" ID="State" Mode="ReadOnly" />
		                    </td>
		                    <td>
		                        <asp:DynamicControl runat="server" DataField="Zip" ID="Zip" Mode="ReadOnly" />
		                    </td>
		                    <td>
		                        <asp:DynamicControl runat="server" DataField="Email" ID="Email" Mode="ReadOnly" />
		                    </td>
		                    <td>
		                        <a href="Admin/Edit.aspx?ContactId=<%#: Item.ContactId%>">Edit</a> | 
		                        <a href="Admin/Delete.aspx?ContactId=<%#: Item.ContactId%>">Delete</a>
		                    </td>
		                </tr>
		            </ItemTemplate>
		        </asp:ListView>
		    </div>
		</asp:Content>

4. Actualice las seis referencias del código `Response.Redirect("Default.aspx")` a `Response.Redirect("~/Contacts/Default.aspx")` para los tres archivos siguientes:
	- *Delete.aspx.cs*
	- *Edit.aspx.cs*
	- *Insert.aspx.cs*  

	Ahora los vínculos funcionarán todos correctamente cuando muestre y actualice los datos de contactos.
5. Para restringir el acceso a la carpeta *Admin*, en el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta *Admin* y seleccione **Agregar nuevo elemento**.
6. En la lista de plantillas web de Visual C#, seleccione **Archivo de configuración web** en la lista central, acepte el nombre predeterminado *Web.config* y seleccione **Agregar**.
7. Reemplace el contenido XML del archivo *Web.config* por el siguiente:

		<?xml version="1.0"?>
		<configuration>
		  <system.web>
		    <authorization>
		      <allow roles="canEdit"/>
		      <deny users="*"/>
		    </authorization>
		  </system.web>
		</configuration>

8. Guarde el archivo *Web.config*. El archivo *Web.config* especifica que solo los usuarios asignados al rol "canEdit" pueden tener acceso a las páginas de contenido en la carpeta *Admin*.

Cuando un usuario que no forma parte del rol "canEdit" intenta modificar los datos, se le redirigirá a la página *Iniciar sesión*.

##Implementación de la aplicación con la base de datos de Azure 
Ahora que la aplicación web está completa, puede publicarla en Azure.

###Publicación de la aplicación 
1. En Visual Studio, compile el proyecto (**Ctrl + Mayús + B**).
2. Haga clic con el botón derecho en el proyecto, en el **Explorador de soluciones**, y seleccione **Publicar**.  
	![Opción de menú de publicación](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms22.png)  
	Aparecerá el cuadro de diálogo **Publicación web**.  
	![Cuadro de diálogo Publicación web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms22a.png)  
3. Desde la pestaña **Perfil**, seleccione **Aplicaciones web de Azure** como el destino de publicación si aún no está seleccionado.  
	![Cuadro de diálogo Publicación web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms23.png)  
4. Haga clic en **Iniciar sesión** si no ha iniciado sesión aún.
5. Seleccione la aplicación web existente que creó anteriormente en este tutorial desde el cuadro desplegable **Aplicaciones web existentes** y haga clic en el botón **Aceptar**.  
	![Selección del cuadro de diálogo Sitio web existente](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms25.png)  
	Si se le solicita que guarde los cambios realizados en el perfil, seleccione **Sí**.
6. Haga clic en la pestaña **Configuración**.  
	![Selección del cuadro de diálogo Sitio web existente](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms26.png)  
7. Establezca el cuadro de lista desplegable **Configuración** en **Depurar**.
8. Haga clic en el icono de **flecha abajo** que aparece junto a **ApplicationDbContext** y establézcalo en **ContactDB**.
9. Active la casilla **Ejecutar migraciones de Code First**. En este ejemplo, debe activar esta casilla solo la primera vez que publique la aplicación. De este modo, el método *Seed* en el archivo *Configuration.cs* solo se llamará una vez.  

10. A continuación, haga clic en **Publicar**.  
	La aplicación se publicará en Azure.

>[AZURE.NOTE]  
Si cerró y volvió a abrir Visual Studio después de crear el perfil de publicación, es posible que no vea la cadena de conexión en la lista desplegable. En ese caso, en lugar de editar el perfil de publicación que había creado, cree otro nuevo de igual modo que hizo antes y siga estos pasos en la pestaña **Configuración**.

###Revisión de la aplicación en Azure 
1. En el explorador, haga clic en el vínculo **Demostración de Contact**. Se mostrará la lista de contactos.  
	![Contactos que aparecen en el explorador](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms27.png)  

2. Seleccione **Crear nuevo** en la página **Lista de contactos**.  
	![Contactos que aparecen en el explorador](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms29.png)  
	Se le redirigirá a la página **Inicio de sesión**, ya que no ha iniciado la sesión con una cuenta que puede modificar contactos.
3. Cuando haya proporcionado la dirección de correo electrónico y la contraseña que se indican a continuación, haga clic en el botón **Iniciar sesión**.  
	**Correo electrónico**: `canEditUser@wideworldimporters.com`  
	**Contraseña**: `Pa$$word1`  
	![Página Iniciar sesión](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms28.png)  

4. Escriba nuevos datos para cada campo y, a continuación, presione el botón **Insertar**.  
	![Página Agregar nuevo contacto](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms30.png)  
	Se abrirá la página *EditContactList.aspx* con el nuevo registro.  
	![Página Agregar nuevo contacto](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms31.png)

5. Haga clic en el vínculo **Cerrar sesión**.

###Detención de la aplicación 
Con el fin de impedir que otras personas se registren y usen su aplicación de ejemplo, debe detener la aplicación web.

1. En Visual Studio, en el **menú Ver**, seleccione **Explorador de servidores**. 
2. En el **Explorador de servidores**, vaya a **Aplicaciones web**.
3. Haga clic con el botón derecho en cada sesión del sitio web y seleccione **Detener aplicación web**. 
	![Elemento de menú Detener sitio web](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms26a.png)  

	También existe la opción de seleccionar la aplicación web en el Portal de Azure clásico y después hacer clic en el icono **stop** situado en la parte inferior de la página.  
	![Página Agregar nuevo contacto](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms26b.png)  

##Revisión de la base de datos 
Es importante saber cómo ver y modificar la base de datos directamente. Saber cómo trabajar directamente con la base de datos le permite confirmar los datos de la base de datos y comprender cómo se almacenan en cada tabla.

###Examen de la base de datos SQL de Azure 
1. En Visual Studio, abra el **Explorador de servidores** y vaya a **ContactDB**.
2. Haga doble clic en **ContactDB** y seleccione **Abrir en el Explorador de objetos de SQL Server**.  
	![Apertura del elemento de menú Explorador de objetos de SQL Server](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms32.png)  
3. Si se muestra el cuadro de diálogo **Agregar regla de firewall**, seleccione **Agregar regla de firewall**.  
      Si no puede expandir **Bases de datos SQL** y no puede ver **ContactDB** en Visual Studio, siga estas instrucciones para abrir un puerto o intervalo de puertos de firewall. Para ello, siga las instrucciones que se proporcionan en **Configuración de reglas de firewall** de Azure casi al final del [Tutorial de MVC](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). También puede revisar los datos de la base de datos local compilando, ejecutando y agregando datos a la aplicación en modo local (**CTRL+F5** de Visual Studio).  

4. Si se muestra el cuadro de diálogo **Conectar con el servidor**, escriba la **contraseña** que creó al principio de este tutorial y presione el botón **Conectar**.  
      Si no recuerda la contraseña, puede buscarla en el archivo de proyecto local. En el **Explorador de soluciones**, expanda las carpetas *Properties* y *PublishProfiles*. Abra el archivo *contactmanager.pubxml* (su archivo puede tener otro nombre). Busque su contraseña de publicación en el archivo.

5. Expanda la base de datos **contactDB** y después **Tablas**.
6. Haga clic en la tabla **dbo.AspNetUsers** y seleccione **Ver datos**.  
	![Elemento de menú Ver datos](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms34.png)  
	Puede ver los datos asociados con el usuario canEditUser@contoso.com.  
	![Ventana ContactManager](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms35.png)  

###Incorporación de un usuario al rol Admin editando la base de datos 
Anteriormente en este tutorial, utilizamos código para agregar usuarios al rol canEdit. Existe un método alternativo que consiste en manipular directamente los datos de las tablas de suscripciones. Los pasos siguientes muestran cómo utilizar este método alternativo para agregar un usuario a un rol.

1. En el **Explorador de objetos de SQL Server**, haga clic con el botón derecho en **dbo.AspNetUserRoles** y seleccione **Ver datos**.  
	![Datos AspNetUserRoles](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms36.png)  
2. Copie el *Id. de rol* y péguelo en la fila vacía (nueva).  
	![Datos AspNetUserRoles](./media/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/SecureWebForms37.png)  
3. En la tabla **dbo.AspNetUsers**, busque el usuario que desea agregar al rol y copie su *Id.*.
4. Pegue el *Id.* copiado en el campo **Id. de usuario** de la nueva fila en la tabla **AspNetUserRoles**.  

>[AZURE.NOTE]  
Estamos trabajando en una herramienta que facilitará mucho la tarea de administrar los usuarios y roles.

##Pasos siguientes
Para obtener más información acerca de ASP.NET Web Forms, vea [ASP.NET Web Forms](http://www.asp.net/web-forms) en la aplicación web de ASP.NET y [Tutoriales y guías de Microsoft Azure](https://azure.microsoft.com/documentation/services/web-sites/#net).

Este tutorial está basado en el tutorial para MVC [Creación de una aplicación ASP.NET MVC con auth y Base de datos SQL e implementación en un Servicio de aplicaciones de Azure](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) escrito por Rick Anderson (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) con ayuda de Tom Dykstra y Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)).

Es importante que haga comentarios acerca de lo que le gustó o lo que le gustaría que mejorásemos, no solo en relación al tutorial en sí sino a los productos sobre los que trata. Sus comentarios nos ayudarán a clasificar las mejoras por orden de prioridad. También puede solicitar y votar nuevos temas en [Mostrarme cómo con el código](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code).

 

<!---HONumber=AcomDC_0128_2016-->