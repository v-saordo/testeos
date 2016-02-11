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
	ms.date="01/26/2016"
	ms.author="tdykstra"/>

# Consumo de una aplicación de API desde JavaScript con CORS

## Información general

Este artículo contiene dos secciones:

* La sección sobre la [configuración de CORS](#corsconfig) explica de forma general cómo configurar CORS para cualquier aplicación de API y se aplica igualmente a todos los marcos de trabajo compatibles con el Servicio de aplicaciones, como. NET, Node.js y Java. 

* El [resto del artículo](#tutorialstart) le guía en la implementación de una aplicación de ejemplo de .NET y en la configuración de CORS para que el front-end de JavaScript pueda llamar al back-end de API web.

## <a id="corsconfig"></a> Configuración de CORS en el Servicio de aplicaciones de Azure

### ¿Qué es CORS?

Por motivos de seguridad, los exploradores impiden que JavaScript realice llamadas de API a otro dominio distinto del que procede el código JavaScript. Por ejemplo, puede realizar una llamada desde una página web de contoso.com a un punto de conexión de la API de contoso.com, pero no a un punto de conexión de fabrikam.com. Uso compartido de recursos de origen cruzado (CORS) es un protocolo de Internet diseñado para habilitar escenarios donde es necesario realizar llamadas API entre dominios. En el Servicio de aplicaciones de Azure, un ejemplo de este escenario es cuando se ejecuta el cliente de JavaScript en una aplicación web mientras se ejecuta la API en una aplicación de API.

### Compatibilidad con CORS en el Servicio de aplicaciones

El Servicio de aplicaciones de Azure ofrece una manera fácil de configurar los dominios que tienen permiso para llamar a una aplicación de API, y la característica CORS funciona igual en todos los lenguajes que admite el Servicio aplicaciones de API.

### Configuración de CORS en el Portal de Azure

8. En el explorador, vaya al [Portal de Azure](https://portal.azure.com/).

9. Haga clic en **Examinar > Aplicaciones de API**.

	![](./media/app-service-api-cors-consume-javascript/browseapiapps.png)

11. Seleccione la aplicación de API de destino.

	![](./media/app-service-api-cors-consume-javascript/selectapiapp.png)

10. En la hoja **Aplicación de API**, haga clic en **Configuración**.

	![](./media/app-service-api-cors-consume-javascript/clicksettings.png)

11. Busque la sección **API** y, después, haga clic en **CORS**.

12. En el cuadro de texto, escriba la dirección URL desde la que desea permitir que procedan las llamadas de JavaScript.

	Por ejemplo, si ha implementado la aplicación de JavaScript en una aplicación web denominada todolistangular, escriba "https://todolistangular.azurewebsites.net". Como alternativa, puede escribir un asterisco (*) para especificar que se acepten todos los dominios de origen.

13. Haga clic en **Guardar**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

	Tras hacer clic en **Guardar**, la aplicación de API aceptará llamadas de JavaScript desde las direcciones URL especificadas.

### Configuración de CORS mediante las herramientas del Administrador de recursos de Azure

También puede configurar CORS para una aplicación de API con herramientas de línea de comandos, como Azure PowerShell o la interfaz de línea de comandos entre plataformas de Azure, o mediante el [Explorador de recursos](https://resources.azure.com/).

En estas herramientas, establezca la propiedad `cors` en el tipo de recurso Microsoft.Web/sites/config para el recurso <site name>/web. Por ejemplo, en el **Explorador de recursos**, vaya a **suscripciones > {su suscripción} > resourceGroups > {su grupo de recursos} > proveedores > Microsoft.Web > sitios > {su sitio} > configuración > web** y verá la propiedad cors:

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

### Configuración del proyecto ToDoListAngular para llamar a la aplicación de API ToDoListAPI 

Antes de implementar el front-end en Azure, tiene que cambiar el punto de conexión de API del proyecto de AngularJS para que el código llame a la aplicación de API de Azure, ToDoListAPI, que creó en el tutorial anterior.

1. En el proyecto ToDoListAngular, abra el archivo *app/scripts/todoListSvc.js*.

2. Convierta en comentario la línea que establece `apiEndpoint` en la dirección URL de localhost, elimine el comentario de la línea que establece `apiEndPoint` en la dirección URL azurewebsites.net y sustituya el marcador de posición por el nombre real de la aplicación de API que creó anteriormente. Si asignó a la aplicación de API el nombre ToDoListAPI0125, el código es ahora similar al ejemplo siguiente.

		var apiEndPoint = 'https://todolistapi0125.azurewebsites.net';
		//var apiEndPoint = 'http://localhost:45914';

3. Guarde los cambios.

### Creación de una nueva aplicación web para el proyecto ToDoListAngular

El procedimiento para crear una nueva aplicación web e implementar un proyecto en ella es el mismo que vio en el primer tutorial de esta serie, excepto que no cambió el tipo de **Aplicación web** a **Aplicación de API**.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto ToDoListAngular y, a continuación, haga clic en **Publicar**.

3.  En la pestaña **Perfil** del Asistente para **publicación web**, haga clic en **Servicio de aplicaciones de Microsoft Azure**.

5. En el cuadro de diálogo **Servicio de aplicaciones**, haga clic en **Nuevo**.

3. En la pestaña **Hospedaje** del cuadro de diálogo **Crear servicio de aplicaciones**, asegúrese de que el tipo sea **Aplicación web**.

4. En **Nombre de aplicación web**, escriba un nombre que sea único en el dominio *azurewebsites.net*.

5. Elija la **suscripción** de Azure con la que desea trabajar.

6. En la lista desplegable **Grupo de recursos**, elija el grupo de recursos que creó anteriormente.

4. En la lista desplegable **Plan del Servicio de aplicaciones**, elija el plan que creó anteriormente.

7. Haga clic en **Crear**.

	Visual Studio crea la aplicación web, crea un perfil de publicación para ella y muestra el paso **Conexión** del asistente **Publicación web**.

### Implementación del proyecto web ToDoListAngular en la nueva aplicación web

*  En el paso **Conexión** del Asistente para **publicación web**, haga clic en **Publicar**.

	Visual Studio implementa el proyecto ToDoListAngular en la aplicación web y abre un explorador en la dirección URL de la aplicación web.

### Prueba de la aplicación sin CORS habilitado 

2. En la instancia de Developer Tools del explorador, abra la ventana de la consola.

3. En la ventana del explorador que muestra la UI de AngularJS, haga clic en el vínculo **To Do List**.

	El código de JavaScript intenta llamar a la aplicación de API de nivel intermedio, pero la llamada da error porque el front-end se está ejecutando en un dominio diferente (la URL de la aplicación web) al del back-end (la URL de la aplicación de API). La ventana de la consola de Developer Tools del explorador muestra un mensaje de error entre orígenes.

	![](./media/app-service-api-cors-consume-javascript/consoleaccessdenied.png)

## Configuración de CORS en el Servicio de aplicaciones de Azure

En esta sección, configurará la aplicación de API de nivel intermedio para permitir llamadas de JavaScript desde la aplicación web que creó para el proyecto ToDoListAngular.
 
8. En el explorador, vaya al [Portal de Azure](https://portal.azure.com/).

9. Vaya a la aplicación de API ToDoListAPI (nivel intermedio).

10. En la hoja **Aplicación de API**, haga clic en **Configuración**.

11. Busque la sección **API** y, después, haga clic en **CORS**.

12. En el cuadro de texto, escriba la dirección URL de la aplicación web ToDoListAngular (front-end). Por ejemplo, si implementó el proyecto ToDoListAngular en una aplicación web denominada todolistangular0121, permita llamadas desde la dirección URL `https://todolistangular0121.azurewebsites.net`.

	Como alternativa, puede escribir un asterisco (*) para especificar que se acepten todos los dominios de origen.

13. Haga clic en **Guardar**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)


### Prueba de la aplicación con CORS habilitado

* Abra un explorador en la dirección URL HTTPS de la aplicación web. 

	Esta vez la aplicación le permite ver, agregar, editar y eliminar elementos de tareas pendientes.

	![](./media/app-service-api-cors-consume-javascript/corssuccess.png)

## CORS de Servicio de aplicaciones frente a CORS de API web

En un proyecto de API web puede instalar el paquete NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) para especificar en el código de qué dominios aceptará la API llamadas de JavaScript.
 
No intente usar Web API CORS y CORS del Servicio de aplicaciones en una aplicación de API. CORS de Servicio de aplicaciones tendrá prioridad y Web API CORS no tendrá ningún efecto. Por ejemplo, si habilita un dominio de origen en Servicio de aplicaciones y habilita todos los dominios de origen en el código de la API web, la aplicación de API de Azure solo aceptará llamadas del dominio especificado en Azure.

La compatibilidad con Web API CORS es más flexible que la compatibilidad con CORS de Servicio de aplicaciones. Por ejemplo, en el código puede especificar diferentes orígenes aceptados para diferentes métodos de acción, mientras que para CORS del servicio de aplicaciones especifica un conjunto de orígenes aceptados para todos los métodos de una aplicación de API.

### Habilitación de CORS en el código de la API web.

Los pasos siguientes resumen el proceso para habilitar la compatibilidad con Web API CORS. Para más información, consulte [Enabling Cross-Origin Requests in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api) (Habilitación de solicitudes entre orígenes en ASP.NET Web API 2).

1. En un proyecto de API web, incluya una línea de código `config.EnableCors()` en el método **Register** de la clase **WebApiConfig**, como en el ejemplo siguiente. 

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

1. En el controlador de la API web, agregue el atributo `EnableCors` a la clase de controlador o a métodos de acción individuales. En el ejemplo siguiente, la compatibilidad con CORS se aplica a todo el controlador.

		namespace ToDoListAPI.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ToDoListController : ApiController
 
	> **Nota**: el uso de caracteres comodín para todos los parámetros con el atributo `EnableCors` está previsto solo para demostración y abrirá la API a todos los orígenes y a todas las solicitudes HTTP. Use este atributo con precaución.

## Pasos siguientes 

En este tutorial se ha explicado cómo habilitar la compatibilidad con CORS del Servicio de aplicaciones para que el código JavaScript de cliente pueda llamar a una API de un dominio diferente. En el siguiente artículo de la serie de introducción a Aplicaciones de API, obtendrá información sobre [la autenticación de aplicaciones de API del Servicio de aplicaciones](app-service-api-authentication.md).

<!---HONumber=AcomDC_0204_2016-->