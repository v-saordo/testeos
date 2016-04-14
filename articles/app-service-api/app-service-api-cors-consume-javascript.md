<properties
	pageTitle="Consumo de una aplicación de API de JavaScript con CORS | Microsoft Azure"
	description="Aprenda a usar una aplicación de API del Servicio de aplicaciones de Azure, desde un cliente de JavaScript y mediante CORS."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/04/2016"
	ms.author="tdykstra"/>

# Consumo de una aplicación de API desde JavaScript con CORS

## Información general

El Servicio de aplicaciones ofrece compatibilidad integrada con el uso compartido de recursos entre orígenes (CORS), que permite a los clientes de JavaScript realizar llamadas entre dominios a aplicaciones de API del Servicio de aplicaciones.

Por motivos de seguridad, los exploradores impiden que JavaScript realice llamadas de API a otro dominio distinto del que procede el código JavaScript. Por ejemplo, puede realizar una llamada desde una página web de contoso.com a un punto de conexión de la API de contoso.com, pero no a un punto de conexión de fabrikam.com. CORS es un protocolo de Internet diseñado para habilitar escenarios donde es necesario realizar llamadas API entre dominios. En el Servicio de aplicaciones de Azure, un ejemplo de este escenario es cuando se ejecuta el cliente de JavaScript en una aplicación web mientras se ejecuta la API en una aplicación de API.

Este artículo contiene dos secciones:

* La sección sobre la [configuración de CORS](#corsconfig) explica de forma general cómo configurar CORS para cualquier aplicación de API y se aplica igualmente a todos los marcos de trabajo compatibles con el Servicio de aplicaciones, como. NET, Node.js y Java. 

* Empezando por la sección [Continuación del tutorial de introducción de .NET](#tutorialstart), este tutorial le guía en la implementación de una aplicación de ejemplo de .NET y en la configuración de CORS para que el front-end de JavaScript pueda llamar al back-end de API web.

## <a id="corsconfig"></a> Configuración de CORS en el Servicio de aplicaciones de Azure

Puede configurar CORS en el Portal de Azure o mediante las herramientas de [Azure Resource Manager](../resource-group-overview.md).

#### Configuración de CORS en el Portal de Azure

8. En el explorador, vaya al [Portal de Azure](https://portal.azure.com/).

2. Haga clic en **Servicios de aplicaciones** y, después, en el nombre de la aplicación de API.

	![](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. En la hoja **Configuración** que se abre a la derecha de la hoja **Aplicación de API**, busque la sección **API** y, después, haga clic en **CORS**.

	![](./media/app-service-api-cors-consume-javascript/clicksettings.png)

11. En el cuadro de texto, escriba la dirección URL desde la que desea permitir que procedan las llamadas de JavaScript.


	Por ejemplo, si ha implementado la aplicación de JavaScript en una aplicación web denominada todolistangular, escriba "https://todolistangular.azurewebsites.net". Como alternativa, puede escribir un asterisco (*) para especificar que se acepten todos los dominios de origen.


13. Haga clic en **Guardar**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

	Tras hacer clic en **Guardar**, la aplicación de API aceptará llamadas de JavaScript desde las direcciones URL especificadas.

### Configuración de CORS mediante las herramientas del Administrador de recursos de Azure

También puede configurar CORS para una aplicación de API mediante las [plantillas de Azure Resource Manager](../resource-group-authoring-templates.md) de las herramientas de línea de comandos como [Azure PowerShell](../powershell-install-configure.md) y la [CLI de Azure](../xplat-cli-install.md).

Para ver un ejemplo de una plantilla de Azure Resource Manager que establece la propiedad de CORS, abra el archivo [azuredeploy.json del repositorio de la aplicación de ejemplo de este tutorial](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Busque la sección de la plantilla similar a la del ejemplo siguiente:

		"cors": {
		    "allowedOrigins": [
		        "todolistangular.azurewebsites.net"
		    ]
		}

## <a id="tutorialstart"></a> Continuación del tutorial de introducción de .NET

Si está siguiendo la serie de introducción de Node.js o Java para aplicaciones de API, vaya al siguiente artículo sobre la [autenticación de aplicaciones de API del Servicio de aplicaciones](app-service-api-authentication.md).

El resto de este artículo es una continuación de la serie de introducción de .NET y se supone que ha completado correctamente [el primer tutorial](app-service-api-dotnet-get-started.md).

## Implementación del proyecto ToDoListAngular en una nueva aplicación web

En [el primer tutorial](app-service-api-dotnet-get-started.md) creó una aplicación de API de nivel intermedio y una aplicación de API de capa de datos. En este tutorial creará una aplicación web de una página (SPA) que llame a la aplicación de API de nivel intermedio. Para que la SPA funcione, tendrá que habilitar CORS en la aplicación de API de nivel intermedio.

En la [aplicación de ejemplo ToDoList](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list), el proyecto ToDoListAngular es un cliente de AngularJS sencillo que llama al proyecto de API web ToDoListAPI de nivel intermedio. El código JavaScript del archivo *app/scripts/todoListSvc.js* llama a la API mediante el proveedor HTTP de AngularJS.

		angular.module('todoApp')
		.factory('todoListSvc', ['$http', function ($http) {
		    var apiEndpoint = "http://localhost:46439";
		
		    $http.defaults.useXDomain = true;
		    delete $http.defaults.headers.common['X-Requested-With']; 
		
		    return {
		        getItems : function(){
		            return $http.get(apiEndpoint + '/api/TodoList');
		        },

		        /* Get by ID, Put, and Delete methods not shown */

		        postItem : function(item){
		            return $http.post(apiEndpoint + '/api/TodoList', item);
		        }
		    };
		}]);

### Creación de una nueva aplicación web para el proyecto ToDoListAngular

El procedimiento para crear una nueva aplicación web e implementar un proyecto en ella es el mismo que vio en el primer tutorial de esta serie, excepto que el tipo de aplicación es **Aplicación web** en lugar de **Aplicación de API**.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto ToDoListAngular y, después, haga clic en **Publicar**.

3.  En la pestaña **Perfil** del Asistente para **publicación web**, haga clic en **Servicio de aplicaciones de Microsoft Azure**.

5. En el cuadro de diálogo **Servicio de aplicaciones**, haga clic en **Nuevo**.

3. En la pestaña **Hospedaje** del cuadro de diálogo **Crear servicio de aplicaciones**, escriba un **Nombre de la aplicación web** que sea único en el dominio *azurewebsites.net*.

5. Elija la **suscripción** de Azure con la que desea trabajar.

6. En la lista desplegable **Grupo de recursos**, elija el grupo de recursos que creó anteriormente.

4. En la lista desplegable **Plan del Servicio de aplicaciones**, elija el plan que creó anteriormente.

7. Haga clic en **Crear**.

	Visual Studio crea la aplicación web, crea un perfil de publicación para ella y muestra el paso **Conexión** del asistente **Publicación web**.

	Antes de hacer clic en **Publicar** en el asistente de **Publicación web**, deberá configurar la nueva aplicación web para que llame a la aplicación de API de nivel intermedio en ejecución en el Servicio de aplicaciones.

### Establecimiento de la dirección URL de nivel intermedio en la configuración de la aplicación web

1. Vaya al [Portal de Azure](https://portal.azure.com/) y, después, vaya a la hoja **Aplicación web** de la aplicación web que creó para hospedar el proyecto TodoListAngular (front-end).

2. Haga clic en **Configuración > Configuración de la aplicación**.

3. En la sección **Configuración de la aplicación**, agregue la siguiente clave y valor:

	|Clave|Valor|Ejemplo
	|---|---|---|
	|toDoListAPIURL|https://{your nombre de la aplicación de API de nivel intermedio}.azurewebsites.net|https://todolistapi0121.azurewebsites.net|

4. Haga clic en **Guardar**.

	Cuando el código se ejecuta en Azure, este valor anulará ahora la dirección URL de host local que se encuentra en el archivo Web.config.

	El código que obtiene el valor de configuración se encuentra en *index.cshtml*:

		<script type="text/javascript">
		    var apiEndpoint = "@System.Configuration.ConfigurationManager.AppSettings["toDoListAPIURL"]";
		</script>
		<script src="app/scripts/todoListSvc.js"></script>

	El código de *todoListSvc.js* utiliza la configuración:

		return {
		    getItems : function(){
		        return $http.get(apiEndpoint + '/api/TodoList');
		    },
		    getItem : function(id){
		        return $http.get(apiEndpoint + '/api/TodoList/' + id);
		    },
		    postItem : function(item){
		        return $http.post(apiEndpoint + '/api/TodoList', item);
		    },
		    putItem : function(item){
		        return $http.put(apiEndpoint + '/api/TodoList/', item);
		    },
		    deleteItem : function(id){
		        return $http({
		            method: 'DELETE',
		            url: apiEndpoint + '/api/TodoList/' + id
		        });
		    }
		};

### Implementación del proyecto web ToDoListAngular en la nueva aplicación web

*  En Visual Studio, en el paso **Conexión** del Asistente para **publicación web**, haga clic en **Publicar**.

	Visual Studio implementa el proyecto ToDoListAngular en la nueva aplicación web y abre un explorador en la dirección URL de la aplicación web.

### Prueba de la aplicación sin CORS habilitado 

2. En la instancia de Developer Tools del explorador, abra la ventana de la consola.

3. En la ventana del explorador que muestra la interfaz de usuario de AngularJS, haga clic en el vínculo **To Do List**.

	El código de JavaScript intenta llamar a la aplicación de API de nivel intermedio, pero la llamada da error porque el front-end se está ejecutando en un dominio diferente (la URL de la aplicación web) al del back-end (la URL de la aplicación de API). La ventana de la consola de Developer Tools del explorador muestra un mensaje de error entre orígenes.

	![](./media/app-service-api-cors-consume-javascript/consoleaccessdenied.png)

## Configuración de CORS para la aplicación de API de nivel intermedio

En esta sección, configurará la aplicación de API ToDoListAPI para permitir llamadas de JavaScript desde la aplicación web que creó para el proyecto ToDoListAngular.
 
8. En el explorador, vaya al [Portal de Azure](https://portal.azure.com/).

2. Haga clic en **Servicios de aplicaciones** y, después, haga clic en la aplicación de API ToDoListAPI (nivel intermedio).

	![](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

10. En la hoja **Configuración** que se abre a la derecha de la hoja **Aplicación de API**, busque la sección **API** y, después, haga clic en **CORS**.

	![](./media/app-service-api-cors-consume-javascript/clicksettings.png)

12. En el cuadro de texto, escriba la dirección URL de la aplicación web ToDoListAngular (front-end). Por ejemplo, si implementó el proyecto ToDoListAngular en una aplicación web denominada todolistangular0121, permita llamadas desde la dirección URL `https://todolistangular0121.azurewebsites.net`.

	Como alternativa, puede escribir un asterisco (*) para especificar que se acepten todos los dominios de origen.

13. Haga clic en **Guardar**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

	Tras hacer clic en **Guardar**, la aplicación de API aceptará llamadas de JavaScript desde las direcciones URL especificadas. En esta captura de pantalla, la aplicación de API ToDoListAPI0223 aceptará llamadas de cliente de JavaScript de la aplicación web ToDoListAngular.

### Prueba de la aplicación con CORS habilitado

* Abra un explorador en la dirección URL HTTPS de la aplicación web. 

	Esta vez la aplicación le permite ver, agregar, editar y eliminar elementos de tareas pendientes.

	![](./media/app-service-api-cors-consume-javascript/corssuccess.png)

## CORS de Servicio de aplicaciones frente a CORS de API web

En un proyecto de API web puede instalar el paquete NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) para especificar en el código de qué dominios aceptará la API llamadas de JavaScript.
 
La compatibilidad con Web API CORS es más flexible que la compatibilidad con CORS de Servicio de aplicaciones. Por ejemplo, en el código puede especificar diferentes orígenes aceptados para diferentes métodos de acción, mientras que para CORS del servicio de aplicaciones especifica un conjunto de orígenes aceptados para todos los métodos de una aplicación de API.

> [AZURE.NOTE] No intente usar Web API CORS y CORS del Servicio de aplicaciones en una aplicación de API. CORS de Servicio de aplicaciones tendrá prioridad y Web API CORS no tendrá ningún efecto. Por ejemplo, si habilita un dominio de origen en Servicio de aplicaciones y habilita todos los dominios de origen en el código de la API web, la aplicación de API de Azure solo aceptará llamadas del dominio especificado en Azure.

### Habilitación de CORS en el código de la API web.

Los pasos siguientes resumen el proceso para habilitar la compatibilidad con Web API CORS. Para más información, consulte [Enabling Cross-Origin Requests in ASP.NET Web API 2 (Habilitación de solicitudes entre orígenes en API web de ASP.NET)](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

1. En un proyecto Web API, instale el paquete NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/).

1. Incluya una `config.EnableCors()` línea de código en el método **Register** de la clase **WebApiConfig**, como en el ejemplo siguiente.

		public static class WebApiConfig
		{
		    public static void Register(HttpConfiguration config)
		    {
		        // Web API configuration and services
	            
		        // The following line enables you to control CORS by using Web API code
		        config.EnableCors();
	
		        // Web API routes
		        config.MapHttpAttributeRoutes();
	
		        config.Routes.MapHttpRoute(
		            name: "DefaultApi",
		            routeTemplate: "api/{controller}/{id}",
		            defaults: new { id = RouteParameter.Optional }
		        );
		    }
		}

1. En el controlador de la Web API, agregue una instrucción `using` para el espacio de nombres `System.Web.Http.Cors` y agregue el atributo `EnableCors` a la clase de controlador o a los métodos de acción individuales. En el ejemplo siguiente, la compatibilidad con CORS se aplica a todo el controlador.

		namespace ToDoListAPI.Controllers 
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ToDoListController : ApiController
 
	> **Nota**: el uso de caracteres comodín para todos los parámetros con el atributo `EnableCors` está previsto solo para demostración y abrirá la API a todos los orígenes y a todas las solicitudes HTTP. Use este atributo con precaución.

## Pasos siguientes 

En este tutorial se ha explicado cómo habilitar la compatibilidad con CORS del Servicio de aplicaciones para que el código JavaScript de cliente pueda llamar a una API de un dominio diferente. En el siguiente artículo de la serie de introducción a Aplicaciones de API, obtendrá información sobre [la autenticación de aplicaciones de API del Servicio de aplicaciones](app-service-api-authentication.md).

<!---HONumber=AcomDC_0309_2016-->