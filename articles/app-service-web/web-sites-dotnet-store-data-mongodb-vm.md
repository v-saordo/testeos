<properties 
	pageTitle="Creación de una aplicación web de Azure que se conecta a MongoDB en una máquina virtual" 
	description="Un tutorial que le enseña a usar Git para implementar una aplicación ASP.NET en un Servicio de aplicaciones de Azure conectado a MongoDB en una máquina virtual de Azure."
	tags="azure-portal" 
	services="app-service\web, virtual-machines" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="cephalin"/>


# Creación de una aplicación web de Azure que se conecta a MongoDB en una máquina virtual

Puede utilizar Git para implementar una aplicación ASP.NET en Aplicaciones web de Servicio de aplicaciones de Azure. En este tutorial, generará una aplicación de lista de tareas ASP.NET MVC front-end simple que se conecta a una base de datos MongoDB en una máquina virtual en Azure. [MongoDB][MongoDB] es una conocida base de datos NoSQL de alto rendimiento de código abierto. Después de ejecutar y realizar la prueba de la aplicación ASP.NET en el equipo de desarrollo, cargará la aplicación en Aplicaciones web de Servicio de aplicaciones con Git.

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.


## Conocimientos previos ##

El conocimiento de los siguientes aspectos es útil para este tutorial, aunque no es obligatorio:

* El controlador C# para MongoDB. Para obtener más información sobre el desarrollo de aplicaciones C# en relación con MongoDB, consulte el [Centro de lenguaje CSharp de MongoDB][MongoC#LangCenter]. 
* El marco de la aplicación web ASP.NET. Puede obtener toda la información en el [sitio web de ASP.net][ASP.NET].
* El marco de la aplicación web ASP .NET MVC. Puede obtener toda la información en el [sitio web de ASP.NET MVC][MVCWebSite].
* Azure. Puede comenzar leyendo [Azure][WindowsAzure].

## Requisitos previos ##

- [Visual Studio Express 2013 para Web][VSEWeb] o [Visual Studio 2013][VSUlt]
- [SDK de Azure para .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
- Una suscripción de Microsoft Azure activa

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<a id="virtualmachine"></a>
## Creación de una máquina virtual e instalación de MongoDB ##

En este tutorial se presupone que ha creado una máquina virtual en Azure. Después de crear la máquina virtual, tiene que instalar MongoDB en la misma:

* Para crear una máquina virtual de Windows e instalar MongoDB, consulte [Instalación de MongoDB en una máquina virtual con Windows Server en Azure][InstallMongoOnWindowsVM].


Después de haber creado la máquina virtual en Azure e instalado MongoDB, asegúrese de recordar el nombre de DNS de la máquina virtual ("testlinuxvm.cloudapp.net", por ejemplo) y el puerto externo de MongoDB que especificó en el extremo. Necesitará esta información posteriormente en el tutorial.

<a id="createapp"></a>
## Creación de la aplicación ##

En esta sección creará una aplicación ASP.NET denominada "My Task List" mediante Visual Studio y realizará una implementación inicial en las Aplicaciones web del Servicio de aplicaciones de Azure. Ejecutará la aplicación localmente, pero se conectará con la máquina virtual en Azure y usará la instancia de MongoDB creada ahí.

1. En Visual Studio, haga clic en **Nuevo proyecto**.

	![Página de inicio de nuevo proyecto][StartPageNewProject]

1. En la ventana **Nuevo proyecto**, en el panel izquierdo, seleccione **Visual C#** y, a continuación, seleccione **Web**. En el panel intermedio, seleccione **Aplicación web ASP.NET**. En la parte inferior, utilice el nombre "MyTaskListApp" para el proyecto y, a continuación, haga clic en **Aceptar**

	![Cuadro de diálogo Nuevo proyecto][NewProjectMyTaskListApp]

1. En el cuadro de diálogo **Nuevo proyecto de ASP.NET**, seleccione **MVC** y, a continuación, haga clic en **Aceptar**.

	![Seleccione la plantilla MVC][VS2013SelectMVCTemplate]

1. Si ya no está registrado en Microsoft Azure, se le solicitará que inicie sesión. Siga las indicaciones para iniciar sesión en Azure.
2. Una vez que ha iniciado sesión, puede comenzar a configurar la aplicación web de Servicio de aplicaciones. Especifique el **nombre de aplicación web**, el **plan de Servicio de aplicaciones**, **Grupo de recursos** y **Región** y luego haga clic en **Crear**.

	![](./media/web-sites-dotnet-store-data-mongodb-vm/VSConfigureWebAppSettings.png)

1. Una vez completada la creación del proyecto, se espera para que la aplicación web que se creará en el servicio de aplicación de Azure como se indica en la ventana **Actividad de Servicio de aplicaciones de Azure**. A continuación, haga clic en **Publicar MyTaskListApp en esta aplicación web ahora**.

1. Haga clic en **Publicar**.

	![](./media/web-sites-dotnet-store-data-mongodb-vm/VSPublishWeb.png)

	Una vez publicada la aplicación ASP.NET de forma predeterminada en las aplicaciones web de Servicio de aplicaciones de Azure, se iniciará en el explorador.

## Instalación del controlador C# de MongoDB

MongoDB ofrece soporte de cliente para aplicaciones C# a través de un controlador, que tiene que instalar en el equipo de desarrollo local. El controlador C# se encuentra disponible a través de NuGet.

Para instalar el controlador C# de MongoDB:

1. En el **Explorador de soluciones**, en el proyecto **MyTaskListApp**, haga clic con el botón secundario en Referencias y seleccione **Administrar paquetes de NuGet**.

	![Administración de paquetes de NuGet][VS2013ManageNuGetPackages]

2. En la ventana **Administrar paquetes de NuGet**, en el panel izquierdo, haga clic en **En línea**. En el cuadro **Buscar en línea** de la derecha, escriba "mongodb.driver". Haga clic en **Instalar** para instalar el controlador.

	![Búsqueda del controlador C# de MongoDB][SearchforMongoDBCSharpDriver]

3. Haga clic en **Acepto** para aceptar los términos de licencia de 10gen, Inc.

4. Haga clic en **Cerrar** una vez que se haya instalado el controlador. ![Se ha instalado el controlador C# de MongoDB][MongoDBCsharpDriverInstalled]


El controlador C# de MongoDB está ahora instalado. Se han agregado al proyecto referencias a las bibliotecas **MongoDB.Bson**, **MongoDB.Driver** y **MongoDB.Driver.Core**.

![Referencias del controlador C# de MongoDB][MongoDBCSharpDriverReferences]

## Adición de un modelo ##
En el **Explorador de soluciones**, haga clic con el botón secundario en la carpeta *Models* y seleccione **Agregar** una nueva **Clase** y asígnele el nombre *TaskModel.cs*. En *TaskModel.cs*, reemplace el código existente por el siguiente código:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MongoDB.Bson.Serialization.Attributes;
	using MongoDB.Bson.Serialization.IdGenerators;
	using MongoDB.Bson;
	
	namespace MyTaskListApp.Models
	{
	    public class MyTask
	    {
	        [BsonId(IdGenerator = typeof(CombGuidGenerator))]
	        public Guid Id { get; set; }
	
	        [BsonElement("Name")]
	        public string Name { get; set; }
	
	        [BsonElement("Category")]
	        public string Category { get; set; }
	
	        [BsonElement("Date")]
	        public DateTime Date { get; set; }
	
	        [BsonElement("CreatedDate")]
	        public DateTime CreatedDate { get; set; }
	
	    }
	}

## Incorporación de un nivel de acceso de datos ##
En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto *MyTaskListApp* y seleccione **Agregar** una **Carpeta nueva** llamada *DAL*. Haga clic con el botón secundario en la carpeta *DAL* y utilice la opción **Agregar** una nueva **Clase**. Utilice el nombre de archivo de clase *Dal.cs*. En *Dal.cs*, reemplace el código existente por el siguiente código:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MyTaskListApp.Models;
	using MongoDB.Driver;
	using MongoDB.Bson;
	using System.Configuration;
	
	
	namespace MyTaskListApp
	{
	    public class Dal : IDisposable
	    {
	        private MongoServer mongoServer = null;
	        private bool disposed = false;
	
	        // To do: update the connection string with the DNS name
	        // or IP address of your server. 
	        //For example, "mongodb://testlinux.cloudapp.net"
	        private string connectionString = "mongodb://mongodbsrv20151211.cloudapp.net";
	
	        // This sample uses a database named "Tasks" and a 
	        //collection named "TasksList".  The database and collection 
	        //will be automatically created if they don't already exist.
	        private string dbName = "Tasks";
	        private string collectionName = "TasksList";
	
	        // Default constructor.        
	        public Dal()
	        {
	        }
	
	        // Gets all Task items from the MongoDB server.        
	        public List<MyTask> GetAllTasks()
	        {
	            try
	            {
	                var collection = GetTasksCollection();
	                return collection.Find(new BsonDocument()).ToList();
	            }
	            catch (MongoConnectionException)
	            {
	                return new List<MyTask>();
	            }
	        }
	
	        // Creates a Task and inserts it into the collection in MongoDB.
	        public void CreateTask(MyTask task)
	        {
	            var collection = GetTasksCollectionForEdit();
	            try
	            {
	                collection.InsertOne(task);
	            }
	            catch (MongoCommandException ex)
	            {
	                string msg = ex.Message;
	            }
	        }
	
	        private IMongoCollection<MyTask> GetTasksCollection()
	        {
	            MongoClient client = new MongoClient(connectionString);
	            var database = client.GetDatabase(dbName);
	            var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
	        }
	
	        private IMongoCollection<MyTask> GetTasksCollectionForEdit()
	        {
	            MongoClient client = new MongoClient(connectionString);
	            var database = client.GetDatabase(dbName);
	            var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
	        }
	
	        # region IDisposable
	
	        public void Dispose()
	        {
	            this.Dispose(true);
	            GC.SuppressFinalize(this);
	        }
	
	        protected virtual void Dispose(bool disposing)
	        {
	            if (!this.disposed)
	            {
	                if (disposing)
	                {
	                    if (mongoServer != null)
	                    {
	                        this.mongoServer.Disconnect();
	                    }
	                }
	            }
	
	            this.disposed = true;
	        }
	
	        # endregion
	    }
	}

## Adición de un controlador ##
Abra el archivo *Controllers\\HomeController.cs* en el **Explorador de soluciones** y reemplace el código existente por lo siguiente:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.Mvc;
	using MyTaskListApp.Models;
	using System.Configuration;
	
	namespace MyTaskListApp.Controllers
	{
	    public class HomeController : Controller, IDisposable
	    {
	        private Dal dal = new Dal();
	        private bool disposed = false;
	        //
	        // GET: /MyTask/
	
	        public ActionResult Index()
	        {
	            return View(dal.GetAllTasks());
	        }
	
	        //
	        // GET: /MyTask/Create
	
	        public ActionResult Create()
	        {
	            return View();
	        }
	
	        //
	        // POST: /MyTask/Create
	
	        [HttpPost]
	        public ActionResult Create(MyTask task)
	        {
	            try
	            {
	                dal.CreateTask(task);
	                return RedirectToAction("Index");
	            }
	            catch
	            {
	                return View();
	            }
	        }
	
	        public ActionResult About()
	        {
	            return View();
	        }
	
	        # region IDisposable
	
	        new protected void Dispose()
	        {
	            this.Dispose(true);
	            GC.SuppressFinalize(this);
	        }
	
	        new protected virtual void Dispose(bool disposing)
	        {
	            if (!this.disposed)
	            {
	                if (disposing)
	                {
	                    this.dal.Dispose();
	                }
	            }
	
	            this.disposed = true;
	        }
	
	        # endregion
	
	    }
	}

## Configuración de estilos ##
Para cambiar el título en la parte superior de la página, abra el archivo *Views\\Shared\\_Layout.cshtml* en el **Explorador de soluciones** y reemplace "Application name" en el encabezado de la barra de exploración por "My Task List Application" de manera que tenga la siguiente apariencia:

 	@Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

Para configurar el menú Task List, abra el archivo *\\Views\\Home\\Index.cshtml* y reemplace el código existente por el siguiente código:
	
	@model IEnumerable<MyTaskListApp.Models.MyTask>
	
	@{
	    ViewBag.Title = "My Task List";
	}
	
	<h2>My Task List</h2>
	
	<table border="1">
	    <tr>
	        <th>Task</th>
	        <th>Category</th>
	        <th>Date</th>
	        
	    </tr>
	
	@foreach (var item in Model) {
	    <tr>
	        <td>
	            @Html.DisplayFor(modelItem => item.Name)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Category)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Date)
	        </td>
	        
	    </tr>
	}
	
	</table>
	<div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>


Para agregar la capacidad de crear una nueva tarea, haga clic con el botón secundario en la carpeta *Views\\Home\* y utilice la opción **Agregar** para agregar una vista en **Vista**. Póngale a la vista el nombre *Create*. Reemplace el código por lo siguiente:

	@model MyTaskListApp.Models.MyTask
	
	<script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
	
	@using (Html.BeginForm("Create", "Home")) {
	    @Html.ValidationSummary(true)
	    <fieldset>
	        <legend>New Task</legend>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Name)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Name)
	            @Html.ValidationMessageFor(model => model.Name)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Category)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Category)
	            @Html.ValidationMessageFor(model => model.Category)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Date)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Date)
	            @Html.ValidationMessageFor(model => model.Date)
	        </div>
	
	        <p>
	            <input type="submit" value="Create" />
	        </p>
	    </fieldset>
	}

El **Explorador de soluciones** debe tener la siguiente apariencia:

![Explorador de soluciones][SolutionExplorerMyTaskListApp]

## Establecimiento de la cadena de conexión de MongoDB ##
En el **Explorador de soluciones**, abra el archivo *DAL/Dal.cs*. Busque la siguiente línea de código:

	private string connectionString = "mongodb://<vm-dns-name>";

Reemplace "`<vm-dns-name>`" por el nombre de DNS de la máquina virtual que ejecuta el MongoDB que creó en el paso [Creación de una máquina virtual e instalación de MongoDB de este tutorial][]. Para buscar el nombre de DNS de la máquina virtual, diríjase al Portal de Azure, seleccione **Máquinas virtuales** y busque **Nombre DNS**.

Si el nombre de DNS de la máquina virtual es "testlinuxvm.cloudapp.net" y MongoDB está escuchando en el puerto predeterminado 27017, la línea de la cadena de conexión del código tendrá la siguiente apariencia:

	private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Si el extremo de la máquina virtual especifica un puerto externo distinto para MongoDB, puede especificar el puerto en la cadena de conexión:

 	private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Para obtener más información sobre las cadenas de conexión de MongoDB, consulte [Conexiones][MongoConnectionStrings].

## Prueba de la implementación local ##

Para ejecutar la aplicación en el equipo de desarrollo, seleccione **Iniciar depuración** en el menú **Depurar** o presione **F5**. IIS Express se iniciará y se abrirá un explorador y se iniciará la página principal de la aplicación. Puede agregar una nueva tarea, que se agregará a la base de datos de MongoDB que se ejecuta en la máquina virtual en Azure.

![Aplicación My Task List][TaskListAppBlank]

## Publicación de aplicaciones web del Servicio de aplicaciones de Azure

En esta sección publicará los cambios en las aplicaciones web del Servicio de aplicaciones de Azure.

1. En el Explorador de soluciones, haga clic con el botón secundario de nuevo en **MyTaskListApp** y, a continuación, haga clic en **Publicar**.
2. Haga clic en **Publicar**.

	Ahora debe ver la aplicación web ejecutándose en Servicio de aplicaciones de Azure y accediendo a la base de datos MongoDB en máquinas virtuales de Azure.

## Resumen ##

Ha implementado correctamente la aplicación ASP.NET en aplicaciones web de Servicio de aplicaciones de Azure. Para ver la aplicación web:

1. Inicie sesión en el Portal de Azure.
2. Haga clic en **Aplicaciones web**. 
3. Seleccione la aplicación web en la lista **Aplicaciones web**.

Para obtener más información sobre el desarrollo de aplicaciones C# en relación con MongoDB, consulte el [Centro de lenguaje CSharp][MongoC#LangCenter].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 

<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnWindowsVM]: ../virtual-machines/virtual-machines-install-mongodb-windows-server.md
[VSEWeb]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
[VSUlt]: http://www.microsoft.com/visualstudio/eng/2013-downloads

<!-- IMAGES -->


[StartPageNewProject]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
[NewProjectMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
[VS2013SelectMVCTemplate]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
[VS2013DefaultMVCApplication]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
[VS2013ManageNuGetPackages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
[SearchforMongoDBCSharpDriver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
[MongoDBCsharpDriverInstalled]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
[MongoDBCSharpDriverReferences]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
[SolutionExplorerMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
[TaskListAppBlank]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
[WAWSCreateWebSite]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
[WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
[Image9]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
[Image10]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
[Image11]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png

<!-- TOC BOOKMARKS -->
[Creación de una máquina virtual e instalación de MongoDB de este tutorial]: #virtualmachine
[Create and run the My Task List ASP.NET application on your development computer]: #createapp
[Create an Azure web site]: #createwebsite
[Deploy the ASP.NET application to the web site using Git]: #deployapp
 

<!---HONumber=AcomDC_0302_2016-->