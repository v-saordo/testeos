<properties 
	pageTitle="Acceso a una aplicación de API de Azure con HTML y JavaScript" 
	description="Obtenga información sobre cómo tener acceso a su back-end de aplicación de API con HTML y JavaScript." 
	services="app-service\api" 
	documentationCenter=".net"
	authors="bradygaster"
	manager="mohisri" 
	editor="tdykstra"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="bradygaster"/>

# Uso de una aplicación de API de Azure con HTML y JavaScript

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Información general

Este artículo muestra cómo crear un cliente HTML y JavaScript para una [aplicación de API](app-service-api-apps-why-best-platform.md) en el [Servicio de aplicaciones de Azure](/documentation/services/app-service/). En este artículo, se presupone que tiene conocimientos prácticos de HTML y JavaScript, y que usa el marco JavaScript [AngularJS](https://angularjs.org/) para realizar llamadas de REST a la aplicación de API.

Estos son artículos que debe recorrer antes de este para adquirir conocimientos previos.

1. En [Creación de una aplicación de API](app-service-dotnet-create-api-app.md), creó un proyecto de aplicación de API.
2. En [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md), se implementa la aplicación de API en la suscripción de Azure.
3. En [Depuración de una aplicación de API](../app-service-dotnet-remotely-debug-api-app.md), se usa Visual Studio para depurar el código de forma remota mientras se ejecuta en Azure.

Este artículo se basa en estos artículos anteriores al demostrar el uso que pueden hacer de JavaScript las aplicaciones HTML para obtener acceso a sus aplicaciones de API de back-end.

## Habilitación de CORS

Normalmente, CORS (uso compartido de recursos entre orígenes) es necesario en las aplicaciones HTML a las que se ofrecerá servicio desde diferentes hosts que la propia API. Con las aplicaciones de API, hay al menos dos opciones para habilitar CORS. En esta sección se describen estas opciones.

### Habilitación de CORS para puertas de enlace de aplicación de API

Es posible configurar puertas de enlace de aplicación de API para habilitar CORS mediante el portal de vista previa de Azure. Agregando el *appSetting* **MS_CrossDomainOrigins** puede especificar qué direcciones URL pueden llamar a la aplicación de API. Esta sección explicará cómo utilizar este *appSetting* para habilitar CORS en el nivel de puerta de enlace de API.

1. Navegue hasta la hoja del portal de vista previa de Azure para la aplicación de API en la que quiere habilitar CORS. Una vez allí, haga clic en el icono *Puerta de enlace* de la aplicación de API. 

	![Clic en el botón Puerta de enlace de aplicación de API](./media/app-service-api-javascript-client/19-api-app-blade.png)

1. Haga clic en el vínculo **Host de puerta de enlace** en la hoja del portal.

	![Clic en el vínculo de host Puerta de enlace de aplicación de API](./media/app-service-api-javascript-client/20-gateway-host-blade.png)

1. Haga clic en el vínculo **Toda la configuración** en la hoja del portal.

	![Vínculo Toda la configuración de puerta de enlace](./media/app-service-api-javascript-client/21-gateway-blade-all-settings.png)

1. Haga clic en el botón **Configuración de la aplicación** en la hoja del portal.

	![Configuración de la aplicación de puerta de enlace](./media/app-service-api-javascript-client/22-gateway-app-settings-blade.png)

1. Agregue el valor de configuración de aplicación **MS_CrossDomainOrigins**. Convierta el valor de configuración en la lista de hosts HTTP separados por comas a los que desea proporcionar acceso a su aplicación de API. Si desea proporcionar acceso a varios hosts, el valor de *appSetting* se puede establecer en algo similar al código siguiente.

		http://foo.azurewebsites.net, https://foo.azurewebsites.net, http://contactlistwebapp.azurewebsites.net

	La siguiente sección le guiará a través del proceso de creación de una aplicación web independiente que contiene una página HTML sencilla que llama a la aplicación de API desde una aplicación web que se ejecuta en el mismo grupo de recursos de Azure que la aplicación de API. Puesto que este es el único host que tendrá acceso permitido a la aplicación de API, el valor se configurará para que contenga un host.

		http://contactlistwebapp.azurewebsites.net

	La captura de pantalla siguiente muestra el aspecto que tendrá esta configuración una vez que la haya guardado en el portal de vista previa de Azure.

	![](./media/app-service-api-javascript-client/23-app-settings-set.png)

El valor de configuración **MS_CrossDomainOrigins** de la aplicación se describe detalladamente en la entrada de blog [Actualizaciones de .NET de Servicios móviles de Azure](https://azure.microsoft.com/blog/2014/07/28/azure-mobile-services-net-updates/); así pues, consúltela para obtener más información sobre dicho valor de configuración.

### Habilitación de CORS en código de API web

El proceso de habilitación CORS en API web se documenta en profundidad en el artículo de ASP.NET [Habilitación de solicitudes entre orígenes en API web 2 de ASP.NET](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api). Para las aplicaciones de API compiladas con la API web de ASP.NET, el proceso es exactamente el mismo (se resume a continuación). Omita esta sección si ya ha habilitado CORS, ya que la aplicación de API ya debe estar configurada correctamente.

1. La funcionalidad de CORS se proporciona por parte del paquete de NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/). Para instalarlo, abra la **Consola del administrador de paquetes** y ejecute el siguiente script de PowerShell. 

		Install-Package Microsoft.AspNet.WebApi.Cors

1. Una vez que se ejecuta el comando, la Consola del administrador de paquetes y **packages.config** reflejarán la adición del nuevo paquete de NuGet.

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/01-cors-installed.png)

1. Abra el archivo la *App_Start/WebApiConfig.cs*. Agregue la línea de código siguiente al método **Register** de la clase **WebApiConfig** en el archivo.

		config.EnableCors();

	Una vez que el archivo se actualice, el código debería tener un aspecto similar al siguiente:

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
				config.EnableCors(); /* -- NEW CODE -- */
	
	            // Web API routes
	            config.MapHttpAttributeRoutes();
	
	            config.Routes.MapHttpRoute(
	                name: "DefaultApi",
	                routeTemplate: "api/{controller}/{id}",
	                defaults: new { id = RouteParameter.Optional }
	            );
	        }
	    }

1. El paso final para habilitar CORS es delimitar los métodos de acción individuales que desea habilitar. Agregue el atributo **EnableCors** sobre cada uno de los métodos o en todo el controlador, como se muestra en el código siguiente.

	> **Nota**: el uso de caracteres comodín para todos los parámetros con el atributo EnableCors está previsto solo para fines de demostración, y abrirá la API hasta todos los orígenes y todas las solicitudes de HTTP. Utilice este atributo con precaución y entienda las implicaciones.

		using ContactList.Models;
		using System.Collections.Generic;
		using System.Web.Http;
		using System.Web.Http.Cors;
	
		namespace ContactList.Controllers
		{
		    [EnableCors(origins:"*", headers:"*", methods: "*")] /* -- NEW CODE -- */
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]
		            {
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
		            };
		        }
		
		        [HttpPost]
		        public Contact Post([FromBody] Contact contact)
		        {
		            return contact;
		        }
		    }
		}
1. Si ya había implementado la aplicación de API en Azure antes de agregar la compatibilidad con CORS, vuelva a implementar el código para que CORS se habilite en la API hospedada de Azure. 

## Creación de una aplicación web para utilizar la aplicación de API

En esta sección, podrá crear una nueva aplicación web vacía, instalar y usar AngularJS y enlazar un front-end HTML sencillo a la aplicación de API. Implementará la aplicación web que se utiliza en el Servicio de aplicaciones de Azure. La aplicación web HTML se enlazará y mostrará los datos recuperados desde la aplicación de API, y pondrá a disposición de los usuarios una sencilla interfaz de usuario para la API de contactos.

1. Haga clic en la solución que creó anteriormente en [Creación de una aplicación de API](app-service-dotnet-create-api-app.md) y, a continuación, seleccione **Agregar -> Nuevo proyecto**.

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/02-add-project.png)

1. Seleccione la plantilla **Aplicación Web ASP.NET**.

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/03-new-web-project.png)

1. Seleccione la plantilla **Vacía** desde el cuadro de diálogo One ASP.NET.

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/04-empty-web.png)

1. Mediante el **Administrador de paquetes** o el elemento del menú contextual **Administrar paquetes de NuGet**, instale el paquete de NuGet [AngularJS](https://www.nuget.org/packages/angularjs).

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/05-install-angular.png)

1. Mediante el **Administrador de paquetes** o el elemento del menú contextual **Administrar paquetes de NuGet**, instale el paquete de NuGet [Bootstrap](https://www.nuget.org/packages/bootstrap).

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/05-install-bootstrap.png)

1. Agregue un nuevo archivo HTML al proyecto de aplicación web.

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/06-new-html-file.png)

1. Ponga al archivo el nombre *index.html*.

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/07-index-html.png)

1. Agregue los archivos de JavaScript de CSS y AngularJS de arranque a la página HTML, y además utilice una plantilla sencilla de Bootstrap ([como esta](http://getbootstrap.com/examples/starter-template/)) y cree una etiqueta de script vacía para preparar la página.
	
	> Nota: los comentarios en el siguiente código HTML y JavaScript sirven como antecedente para los pasos posteriores de esta sección.

		<!DOCTYPE html>
		<html xmlns="http://www.w3.org/1999/xhtml">
		<head>
		    <title>Contacts HTML Client</title>
		    <link href="Content/bootstrap.css" rel="stylesheet" />
		    <style type="text/css">
		        body {
		            margin-top: 60px;
		        }
		    </style>
		</head>
		<body>
		
		    <nav class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
		                    <span class="sr-only">Toggle navigation</span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		                <a class="navbar-brand" href="#">Contacts</a>
		            </div>
		            <div id="navbar" class="collapse navbar-collapse">
		                <ul class="nav navbar-nav"></ul>
		            </div>
		        </div>
		    </nav>
		
		    <div class="container">
		        <!-- contacts ui here -->
		    </div>
		
		    <script src="Scripts/angular.js" type="text/javascript"></script>
		    <script type="text/javascript">
		        /* client javascript code here */
		    </script>
		
		</body>
		</html>

1. Agregue el código de tabla HTML que se muestra a continuación al elemento *div* **container** en el código HTML.

		<!-- contacts ui here -->
        <table class="table table-striped" ng-app="myApp" ng-controller="contactListCtrl">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                    <th></th>
                </tr>
            </thead>
            <tbody>
                <tr ng-repeat="con in contacts">
                    <td>[[con.Id]]</td>
                    <td>[[con.Name</td>
                    <td>[[con.EmailAddress]]</td>
                    <td></td>
                </tr>
            </tbody>
            <tfoot>
                <tr>
                    <th>Create a new Contact</th>
                    <th colspan="2">API Status: [[status]]</th>
                    <th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>
                </tr>
                <tr>
                    <td><input type="text" placeholder="ID" ng-model="newId" /></td>
                    <td><input type="text" placeholder="Name" ng-model="newName" /></td>
                    <td><input type="text" placeholder="Email Address" ng-model="newEmail" /></td>
                    <td><button class="btn btn-sm btn-success" ng-click="create()">Create</button></td>
                </tr>
            </tfoot>
        </table>

1. En los elementos `tbody` y `tfoot`, sustituya cada [ por { y cada ] por }. (Este sitio no puede mostrar actualmente expresiones con llaves en los bloques de código).

2. Haga clic con el botón derecho en el archivo *index.html* y haga clic en **Establecer como página de inicio**.

3. Haga clic con el botón derecho en el archivo *index.html* y haga clic en **Ver en el explorador**.

	Observe los elementos handlebars de plantilla en la salida de HTML. Podrá enlazar esos elementos HTML utilizando AngularJS en el paso siguiente.

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/09-template-ui.png)

1. Agregue el código JavaScript siguiente al archivo *index.html* para llamar a la API y enlazar los datos de la UI de HTML a la salida de la API.

		/* client javascript code here */
        angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
            $scope.baseUrl = 'http://localhost:1578';

            $scope.refresh = function () {
                $scope.status = "Refreshing Contacts...";
                $http({
                    method: 'GET',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    }
                }).success(function (data) {
                    $scope.contacts = data;
                    $scope.status = "Contacts loaded";
                }).error(function (data, status) {
                    $scope.status = "Error loading contacts";
                });
            };

            $scope.create = function () {
                $scope.status = "Creating a new contact";

                $http({
                    method: 'POST',
                    url: $scope.baseUrl + '/api/contacts',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    data: {
                        'Id': $scope.newId,
                        'Name': $scope.newName,
                        'EmailAddress': $scope.newEmail
                    }
                }).success(function (data) {
                    $scope.status = "Contact created";
                    $scope.refresh();
                    $scope.newId = 0;
                    $scope.newName = '';
                    $scope.newEmail = '';
                });
            };

            $scope.refresh();
        });

1, en el código que acaba de agregar a index.html, reemplace el número de puerto en la dirección URL base (`http://localhost:1578`) por el número de puerto real para el proyecto de API.

>[AZURE.NOTE] **Nota** No use el número de puerto del proyecto del cliente HTML. Puede hacer clic con el botón derecho en el proyecto de la API y, a continuación, hacer clic en **Depurar > Iniciar nueva instancia** para que aparezca una ventana del navegador que muestre el número de puerto

1. Asegúrese de que también se ejecute el proyecto de aplicación de API cuando ejecute el cliente HTML. De lo contrario, el HTML de JavaScript no funcionará correctamente. Haga clic con el botón derecho en la solución y seleccione **Propiedades**. A continuación, establezca ambos proyectos web en **Iniciar sin depurar**, y haga que el proyecto de API se ejecute primero. 

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/10-run-both-web-projects.png)

1. Ejecute la solución y el cliente HTML/JavaScript se conectará al proyecto de aplicación de API y mostrará sus datos.

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/11-web-client-running.png)

## Implementación de la aplicación web

En esta sección implementará al cliente HTML/JavaScript como una aplicación web del Servicio de aplicaciones. Una vez finalizada la implementación, cambiará la dirección URL que utiliza JavaScript para utilizar la aplicación de API implementada.

> Nota: en esta sección se supone que ha leído y completado el artículo [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md) o que previamente ha implementado su propia aplicación de API.

1. Abra la hoja de aplicación de API el portal de vista previa de Azure. Haga clic en la dirección URL de la hoja para abrirla en el explorador. Una vez que se abra, copie la dirección URL de la aplicación de API desde la barra de direcciones del explorador. 

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/12-open-api-app-from-blade.png)

1. Pegue la dirección URL de la aplicación de API para sobrescribir el valor anterior de la propiedad **$scope.baseUrl** en el código de JavaScript.

		$scope.baseUrl = 'https://microsoft-apiappf7e042ba8e5233ab4312021d2aae5d86.azurewebsites.net';

	Observe que la dirección URL incluye HTTPS. El uso de HTTPS no es opcional. Las aplicaciones de API no son compatibles con HTTP.

1. Haga clic con el botón derecho en el proyecto web HTML/JavaScript y seleccione el elemento del menú contextual **Publicar**.

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/13-publish-web-app.png)

1. Seleccione la opción **Aplicaciones web de Microsoft Azure** en el cuadro de diálogo de publicación web.

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/14-publish-web-dialog.png)

1. Haga clic en el botón **Nuevo** para crear una nueva aplicación web.

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/15-new-web-app.png)

1. Seleccione el mismo plan de hospedaje de aplicaciones y el mismo grupo de recursos en el que se está ejecutando la aplicación de API.

	> **Nota**: esto no es obligatorio, pero para fines de demostración es más fácil limpiar los recursos de Azure más adelante si todo se encuentra en un grupo de recursos.

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/16-new-web-app-creation-dialog.png)

1. Complete los pasos de publicación web para implementar el cliente HTML/JavaScript en las Aplicaciones web del Servicio de aplicaciones.
1. Una vez implementada la aplicación web, esta debe abrirse automáticamente en el explorador web y mostrar los datos de la aplicación de API. 

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/17-web-app-running-in-ie.png)

1. En este momento, si examina el grupo de recursos verá la nueva aplicación web ejecutándose junto con la aplicación de API.

	![apiapp.json y metadatos en el Explorador de soluciones](./media/app-service-api-javascript-client/18-web-app-visible-in-resource-group.png)

## Pasos siguientes 

Este ejemplo muestra cómo puede utilizar AngularJS como plataforma de JavaScript para tener acceso a los back-end de aplicación de API. Puede cambiar la funcionalidad de acceso de REST para utilizar cualquier otro marco JavaScript.

En este ejemplo se muestra el acceso no autenticado a una aplicación de API. Para obtener información sobre la autenticación en el Servicio de aplicaciones, consulte [Autenticación para aplicaciones de API y aplicaciones móviles](../app-service/app-service-authentication-overview.md).

<!---HONumber=AcomDC_0128_2016-->