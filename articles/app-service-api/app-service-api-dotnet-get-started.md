<properties
	pageTitle="Introducción a Aplicaciones de API y ASP.NET en el Servicio de aplicaciones de Azure | Microsoft Azure"
	description="Obtenga información acerca de cómo crear, implementar y consumir una aplicación de API de ASP.NET en el Servicio de aplicaciones de Azure con Visual Studio 2015."
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
	ms.topic="hero-article"
	ms.date="03/09/2016"
	ms.author="tdykstra"/>

# Introducción a Aplicaciones de API y ASP.NET en el Servicio de aplicaciones de Azure

[AZURE.INCLUDE [selector](../../includes/app-service-api-get-started-selector.md)]

## Información general

Este es el primero de una serie de tutoriales que muestran cómo utilizar características del Servicio de aplicaciones de Azure que resultan útiles para el desarrollo y el hospedaje de API:

* Compatibilidad integrado con metadatos de API
* Compatibilidad con CORS
* Compatibilidad con autenticación y autorización

Va a implementar una aplicación de ejemplo en dos [aplicaciones de API](app-service-api-apps-why-best-platform.md) y una aplicación web del Servicio de aplicaciones de Azure. La aplicación de ejemplo es una lista de tareas pendientes que tiene un front-end de aplicación de una sola página (SPA) de AngularJS, un nivel intermedio de ASP.NET Web API y una capa de datos de ASP.NET Web API, tal como se muestra en el diagrama.

![](./media/app-service-api-dotnet-get-started/noauthdiagram.png)

Esta es una captura de pantalla del front-end de SPA.

![](./media/app-service-api-dotnet-get-started/todospa.png)

Al finalizar este tutorial, tendrá las dos API web funcionando en aplicaciones de API del Servicio de aplicaciones. Después de completar el siguiente tutorial, tendrá toda la aplicación ejecutándose en la nube, con la SPA en una aplicación web del Servicio de aplicaciones. En los tutoriales posteriores, agregará autenticación y autorización.

## Temas que se abordarán

En este tutorial, aprenderá a:

* Trabajar con Aplicaciones de API y aplicaciones web en el Servicio de aplicaciones de Azure usando las herramientas integradas en Visual Studio 2015.
* Automatizar la detección de la API mediante el paquete de NuGet de Swashbuckle para generar dinámicamente la definición JSON de la API de Swagger.
* Usar código de cliente generado automáticamente para consumir una aplicación de API desde un cliente .NET.
* Usar el portal de Azure para configurar el punto de conexión de los metadatos de la aplicación de API.

## Requisitos previos

[AZURE.INCLUDE [Requisitos previos](../../includes/app-service-api-dotnet-get-started-prereqs.md)]

## Descarga de la aplicación de ejemplo 

1. Descargue el repositorio [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list).

	Puede hacer clic en el botón **Descargar zip** o clonar el repositorio en la máquina local.

2. Abra la solución ToDoList en Visual Studio 2015 o 2013.

	La solución de Visual Studio es una aplicación de ejemplo que funciona con elementos de tareas pendientes sencillos que constan de una descripción y un propietario.

		public class ToDoItem 
		{ 
		    public int ID { get; set; } 
		    public string Description { get; set; } 
		    public string Owner { get; set; } 
		} 
 
	La solución incluye tres proyectos:

	![](./media/app-service-api-dotnet-get-started/projectsinse.png)

	* **ToDoListAngular**, el front-end: una SPA de AngularJS que llama al nivel intermedio. 

	* **ToDoListAPI**, el nivel intermedio: un proyecto de ASP.NET Web API que llama a la capa de datos para realizar operaciones de CRUD en elementos de tareas pendientes.

	* **ToDoListDataAPI**, la capa de datos: un proyecto de ASP.NET Web API que realiza operaciones de CRUD en elementos de tareas pendientes. Los elementos de tareas pendientes se almacenan en memoria, lo que significa que cada vez que se reinicia la aplicación se pierden todos los cambios.

	El nivel intermedio proporciona el identificador de usuario en el campo `Owner` cuando se llama a la capa de datos. En el código que se descarga, el identificador de usuario es siempre "*". Cuando agregue autenticación en tutoriales posteriores, el nivel intermedio proporcionará el id. de usuario real a la capa de datos.

2. Compile la solución para restaurar los paquetes de NuGet.

### Opcional: ejecute la aplicación localmente.

En esta sección compruebe que puede ejecutar localmente el cliente y que puede llamar a la API mientras se ejecuta también de manera local.

**Nota:** Estas instrucciones funcionan con los exploradores Internet Explorer y Edge ya que estos permiten llamadas de JavaScript entre orígenes a las direcciones URL `http://localhost`. Si usa Chrome, inicie el explorador con el modificador `--disable-web-security`. Si utiliza Firefox, ignore esta sección.

1. Establezca los tres proyectos como proyectos de inicio, primero se inicia ToDoListDataAPI, luego ToDoListAPI y finalmente ToDoListAngular. (En el **Explorador de soluciones**, haga clic con el botón derecho en la solución, haga clic en **Propiedades**, seleccione **Proyectos de inicio múltiples**, coloque los proyectos en el orden correcto y establezca **Acción** en **Iniciar** para cada uno).  

2. Presione F5 para iniciar los proyectos.

	Se abren tres ventanas del explorador. Dos ventanas del explorador muestran páginas de error HTTP 403 (no se permite el examen de directorios), que es normal para proyectos de Web API. La tercera ventana del explorador muestra la interfaz de usuario de AngularJS.

3. En la ventana del explorador que muestra la interfaz de usuario de AngularJS, haga clic en la pestaña **To Do List**.

	La interfaz de usuario muestra dos elementos de tareas pendientes predeterminados.

	![](./media/app-service-api-dotnet-get-started/todospa.png)

4. Agregue, edite y elimine elementos de tareas pendientes para ver cómo funciona la aplicación.

	Los cambios que realice se almacenan en memoria y se pierden cuando se reinicia la aplicación.

3. Cierre las ventanas del explorador.

## Uso de metadatos e interfaz de usuario de Swagger

La compatibilidad con los metadatos de la API de [Swagger 2.0](http://swagger.io/) está integrada en el Servicio de aplicaciones de Azure. Cada aplicación de API especifica un punto de conexión de dirección URL que devuelve metadatos para la API en formato JSON de Swagger. Los metadatos que devuelve dicho punto de conexión pueden utilizarse para generar código de cliente.

Un proyecto de ASP.NET Web API puede generar dinámicamente los metadatos de Swagger mediante el paquete NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle). El paquete NuGet Swashbuckle ya está instalado en los proyectos ToDoListDataAPI y ToDoListAPI que descargó.

En esta sección del tutorial verá los metadatos de Swagger 2.0 generados y, después, probará una interfaz de usuario que se basa en los metadatos de Swagger.

2. Establezca el proyecto ToDoListDataAPI como proyecto de inicio. 
 
4. Presione F5 para ejecutar el proyecto en modo de depuración.

	El explorador se abre y muestra la página de error HTTP 403.

12. En la barra de direcciones del explorador, agregue `swagger/docs/v1` al final de la línea y presione Entrar. (La dirección URL será `http://localhost:45914/swagger/docs/v1`.)

	Esta es la dirección URL predeterminada que Swashbuckle usa para devolver los metadatos JSON de Swagger 2.0 para la API.

	Si usa Internet Explorer, el explorador le pide que descargue un archivo *v1.json*.

	![](./media/app-service-api-dotnet-get-started/iev1json.png)

	Si usa Chrome, Firefox o Edge, el explorador muestra el archivo JSON en la ventana del explorador.

	![](./media/app-service-api-dotnet-get-started/chromev1json.png)

	El ejemplo siguiente muestra la primera sección de los metadatos de Swagger para la API, con la definición del método Get. Estos son los metadatos subyacentes a la interfaz de usuario de Swagger que usará en los pasos siguientes, y los usará en una sección posterior del tutorial para generar automáticamente el código de cliente.

		{
		  "swagger": "2.0",
		  "info": {
		    "version": "v1",
		    "title": "ToDoListDataAPI"
		  },
		  "host": "localhost:45914",
		  "schemes": [ "http" ],
		  "paths": {
		    "/api/ToDoList": {
		      "get": {
		        "tags": [ "ToDoList" ],
		        "operationId": "ToDoList_GetByOwner",
		        "consumes": [ ],
		        "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
		        "parameters": [
		          {
		            "name": "owner",
		            "in": "query",
		            "required": true,
		            "type": "string"
		          }
		        ],
		        "responses": {
		          "200": {
		            "description": "OK",
		            "schema": {
		              "type": "array",
		              "items": { "$ref": "#/definitions/ToDoItem" }
		            }
		          }
		        },
		        "deprecated": false
		      },

1. Cierre el explorador.

3. En el proyecto ToDoListDataAPI, en el **Explorador de soluciones**, abra el archivo *App\_Start\\SwaggerConfig.cs* y, después, desplácese hacia abajo hasta el siguiente código y quite los comentarios.

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	El archivo *SwaggerConfig.cs* se crea cuando se instala el paquete Swashbuckle en un proyecto. El archivo ofrece varias maneras de configurar Swashbuckle.

	El código para el que quitó los comentarios habilita la interfaz de usuario de Swagger que usará en los pasos siguientes. Al crear un proyecto de Web API con la plantilla de proyecto de aplicación de API, este código aparece como comentarios de forma predeterminada como medida de seguridad.

5. Vuelva a ejecutar el proyecto.

3. En la barra de direcciones del explorador, agregue `swagger` al final de la línea y presione Entrar. (La dirección URL será `http://localhost:45914/swagger`.)

4. Cuando aparezca la página de la interfaz de usuario de Swagger, haga clic en **ToDoList** para ver los métodos disponibles.

	![](./media/app-service-api-dotnet-get-started/methods.png)

5. Haga clic en **Get**.

6. Escriba un asterisco como valor del parámetro `owner` y luego haga clic en **Pruébelo**.

	![](./media/app-service-api-dotnet-get-started/gettryitout1.png)

	La interfaz de usuario de Swagger llama al método Get de ToDoList y muestra la respuesta del código y los resultados de JSON.

	![](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. Haga clic en **Post** (Publicar) y, a continuación, haga clic en el cuadro situado debajo de **Model Schema** (Esquema de modelo).

	Al hacer clic en el esquema del modelo se rellena el cuadro de entrada donde puede especificar el valor del parámetro para el método Post. (Si esto no funciona en Internet Explorer, use un explorador diferente o escriba el valor del parámetro en el paso siguiente de forma manual).

	![](./media/app-service-api-dotnet-get-started/post.png)

7. Cambie el código JSON en el cuadro de entrada del parámetro `contact` para que se parezca al ejemplo siguiente, o sustituya su propio texto descriptivo:

		{
		  "ID": 2,
		  "Description": "buy the dog a toy",
		  "Owner": "*"
		}

10. Haga clic en **Try it out** (Probar).

	La API de ToDoList devuelve un código de respuesta HTTP 204 que indica que todo es correcto.

11. Haga clic en **Get > Try it out** (Obtener > Probar).

	La respuesta del método Get ahora incluye el nuevo elemento de tareas pendientes.

12. Pruebe también los métodos Put, Delete y Get by ID.

14. Cierre el explorador.

Swashbuckle funciona con cualquier proyecto de ASP.NET Web API. Si desea agregar generación de metadatos de Swagger a un proyecto existente, simplemente instale el paquete de Swashbuckle.

**Nota:** metadatos de Swagger incluyen un identificador único para cada operación de API. De manera predeterminada, Swashbuckle puede generar identificadores de operación de Swagger duplicados para los métodos del controlador de la API web. Esto sucede si el controlador tiene métodos HTTP sobrecargados tales como `Get()` y `Get(id)`. Para obtener información sobre cómo controlar las sobrecargas, consulte [Personalización de definiciones de API generadas por Swashbuckle](app-service-api-dotnet-swashbuckle-customize.md). Si crea un proyecto de API web en Visual Studio con la plantilla Aplicación de API de Azure, el código que genera identificadores de operaciones únicos se agrega automáticamente al archivo *SwaggerConfig.cs*.

## Creación de una aplicación de API en Azure e implementación del proyecto ToDoListAPI en ella

En esta sección, se usan las herramientas de Azure integradas en el Asistente para **publicación web** de Visual Studio para crear una nueva aplicación de API en Azure. A continuación, se implementa el proyecto ToDoListDataAPI en la nueva aplicación de API y se llama a la API volviendo a ejecutar la UI de Swagger, pero esta vez mientras se ejecuta en la nube.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto ToDoListDataAPI y, luego, haga clic en **Publicar**.

	![](./media/app-service-api-dotnet-get-started/pubinmenu.png)

3.  En el paso **Perfil** del Asistente para **publicación web**, haga clic en **Servicio de aplicaciones de Microsoft Azure**.

	![](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Inicie sesión en su cuenta de Azure si aún no lo hizo o actualice sus credenciales si expiraron.

4. En el cuadro de diálogo Servicio de aplicaciones, elija la **suscripción** de Azure que desee usar y, a continuación, haga clic en **Nuevo**.

	![](./media/app-service-api-dotnet-get-started/clicknew.png)

	Se muestra la pestaña **Hospedaje** del cuadro de diálogo **Crear servicio de aplicaciones**.

	Dado que va a implementar un proyecto de Web API que tiene instalado Swashbuckle, Visual Studio asume que quiere crear una aplicación de API. Esto se indica mediante el título **Nombre de aplicación de API** y por el hecho de que la lista desplegable **Cambiar tipo** está establecida en **Aplicación de API**.

	![](./media/app-service-api-dotnet-get-started/apptype.png)

	La definición del tipo no determina las características que estarán disponibles para la nueva aplicación de API, aplicación web o aplicación móvil. Todas las características de la aplicación de API que se muestra en estos tutoriales están disponibles para los tres tipos. La única diferencia está en el icono y el texto que el Portal de Azure muestra para identificar el tipo de aplicación, y el orden en que se enumeran las características en algunas páginas del Portal. El Portal de Azure es una interfaz web para administrar recursos de Azure; lo verá más adelante en el tutorial.

4. Escriba un **Nombre de aplicación de API** que sea único en el dominio *azurewebsites.net*, por ejemplo, ToDoListDataAPI junto con un número para hacerlo único.

	Visual Studio propone un nombre único en el que se anexa una cadena de fecha y hora al nombre del proyecto. Si lo prefiere, puede aceptar ese nombre.

	Si escribe un nombre que alguien ya usó, verá un signo de exclamación rojo a la derecha en lugar de una marca de verificación verde y deberá escribir un nombre diferente.

	Azure usará este nombre como prefijo de la dirección URL de la aplicación. La dirección URL completa consistirá en este nombre más *.azurewebsites.net*. Por ejemplo, si el nombre es `ToDoListDataAPI`, la dirección URL será `todolistdataapi.azurewebsites.net`.

6. En la lista desplegable **Grupo de recursos**, haga clic en **Nuevo** y escriba "ToDoListGroup" u otro nombre que prefiera.

	Un grupo de recursos es una colección de recursos de Azure tales como aplicaciones de API, bases de datos, máquinas virtuales, etc. Para este tutorial, es mejor crear un nuevo grupo de recursos porque así podrá eliminar fácilmente y en un solo paso todos los recursos de Azure que cree para el tutorial.

	Este cuadro permite seleccionar un [grupo de recursos](../azure-preview-portal-using-resource-groups.md) existente o crear uno nuevo, para lo que se debe escribir un nombre de grupo recursos que no exista en la suscripción.

4. Haga clic en el botón **Nuevo** situado junto a la lista desplegable **Plan de servicio de aplicaciones**.

	La captura de pantalla muestra los valores de ejemplo de **Nombre de aplicación de API**, **Suscripción** y **Grupo de recursos**. Sus valores serán diferentes.

	![](./media/app-service-api-dotnet-get-started/createas.png)

	En los siguientes pasos se crea un plan del Servicio de aplicaciones para el nuevo grupo de recursos. Un plan del Servicio de aplicaciones especifica los recursos de proceso en los que se ejecuta la aplicación de API. Por ejemplo, si elige el nivel Gratis, la aplicación de API se ejecuta en máquinas virtuales compartidas, mientras que para algunos niveles de pago, se ejecuta en máquinas virtuales dedicadas. Para más información sobre los planes del Servicio de aplicaciones, consulte [Introducción detallada sobre los planes del Servicio de aplicaciones de Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. En el cuadro de diálogo **Configurar el plan de servicio de aplicaciones**, escriba "ToDoListPlan" u otro nombre que prefiera.

5. En la lista desplegable **Ubicación**, elija la ubicación más cercana.

	Esta opción especifica en qué centro de datos de Azure se ejecutará su aplicación. Para este tutorial, puede seleccionar cualquier región y no habrá una diferencia notable, Pero para una aplicación de producción, desea que el servidor esté lo más próximo posible a los clientes que acceden a él con el fin de minimizar la [latencia](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. En la lista desplegable **Tamaño**, haga clic en **Gratis**.

	Para este tutorial, el plan de tarifa Gratis proporcionará un rendimiento suficiente.

6. En el cuadro de diálogo **Configurar plan del servicio de aplicaciones**, haga clic en **Aceptar**.

	![](./media/app-service-api-dotnet-get-started/configasp.png)

7. En el cuadro de diálogo **Crear servicio de aplicaciones**, haga clic en **Crear**.

	![](./media/app-service-api-dotnet-get-started/clickcreate.png)

	Visual Studio crea la aplicación de API.

	**Nota:** hay otras maneras de crear aplicaciones de API en el Servicio de aplicaciones de Azure. Por ejemplo, cuando se crea un nuevo proyecto en Visual Studio, puede crear recursos de Azure para él de la misma manera que acabamos de ver para un proyecto existente. También se pueden crear aplicaciones de API mediante el [Portal de Azure](https://portal.azure.com/), [cmdlets de Azure para Windows PowerShell](../powershell-install-configure.md) o la [interfaz de línea de comandos multiplataforma](../xplat-cli.md).

	Cuando Visual Studio termina de crear la aplicación de API, crea un perfil de publicación que tiene toda la configuración necesaria para la nueva aplicación de API. En los pasos siguientes se usa el nuevo perfil de publicación para implementar el proyecto.

8. En la pestaña **Conexión** del asistente para **publicación web**, haga clic en **Siguiente**.

	También podría continuar y hacer clic en **Publicar** ahora para implementar inmediatamente el proyecto en la nueva aplicación de API, pero en el tutorial recorreremos las otras pestañas de este cuadro de diálogo para ver qué puede hacer en ellas.

	![](./media/app-service-api-dotnet-get-started/connnext.png)

	La pestaña siguiente es **Configuración**. Aquí puede cambiar la pestaña de configuración de compilación para implementar una compilación de depuración para la [depuración remota](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). La pestaña también ofrece varias **Opciones de publicación de archivos**:

	* Quitar archivos adicionales en el destino.
	* Precompilar durante la publicación.
	* Excluir archivos de la carpeta App\_Data.

	Para este tutorial no necesita ninguna de ellas. Para obtener explicaciones detalladas de lo que hacen, consulte [Implementación de un proyecto web utilizando publicación con un solo clic en Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx).

14. Haga clic en **Siguiente**.

	![](./media/app-service-api-dotnet-get-started/settingsnext.png)

	La pestaña **Vista previa** ofrece la posibilidad de ver qué archivos se van a copiar desde el proyecto a la aplicación de API. Al implementar un proyecto en una aplicación de API para la que ya implementó anteriormente, solo se copian los archivos modificados. Si desea ver una lista de lo que se va a copiar, haga clic en el botón **Comenzar previsualización**.

15. Haga clic en **Publicar**.

	![](./media/app-service-api-dotnet-get-started/clickpublish.png)

	Visual Studio implementa el proyecto ToDoListDataAPI en la nueva aplicación de API. La ventana **Salida** registra la implementación correcta y aparece una página "se creó correctamente" en una ventana del explorador que se abre con la dirección URL de la aplicación de API.

	![](./media/app-service-api-dotnet-get-started/deploymentoutput.png)

	![](./media/app-service-api-dotnet-get-started/appcreated.png)

11. En la barra de direcciones del explorador, agregue "swagger" a la URL y presione Entrar. (La dirección URL será `http://{apiappname}.azurewebsites.net/swagger`).

	El explorador muestra la misma interfaz de usuario de Swagger que vio anteriormente, pero ahora se ejecuta en la nube. Pruebe el método Get y verá que aparecen de nuevo los dos elementos de tareas pendientes predeterminados, ya que los cambios realizados antes se guardaron en la memoria en la máquina local.

12. Abra el [Portal de Azure](https://portal.azure.com/).

	El Portal de Azure es una interfaz web para administrar recursos de Azure tales como aplicaciones de API.
 
14. Haga clic en **Examinar > Servicios de aplicaciones**.

	![](./media/app-service-api-dotnet-get-started/browseas.png)

15. En la hoja **Servicios de aplicaciones**, busque y haga clic en la nueva aplicación de API. (En el Portal de Azure, las ventanas que se abren a la derecha se llaman *hojas*).

	![](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

	Se abren dos hojas, una con información general sobre la aplicación de API y otra con una larga lista de valores que puede ver y cambiar.

16. En la hoja **Configuración**, busque la sección **API** y haga clic en **Definición de API**.

	![](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	La hoja **Definición de API** le permite especificar la dirección URL que devuelve los metadatos de Swagger 2.0 en formato JSON. Cuando Visual Studio crea la aplicación de API, establece la dirección URL de la definición de API en el valor predeterminado de los metadatos generados por Swashbuckle que vio anteriormente, que es la URL base de la aplicación de API más `/swagger/docs/v1`.

	![](./media/app-service-api-dotnet-get-started/apidefurl.png)

	Cuando se selecciona una aplicación de API para la que se va a generar código de cliente, Visual Studio recupera los metadatos de esta dirección URL.

## <a id="codegen"></a> Consumo de la aplicación de API mediante código de cliente generado

Una de las ventajas de integrar Swagger en aplicaciones de API de Azure es la generación automática de código. Las clases de cliente generadas hacen que sea más fácil escribir código que llame a una aplicación de API.

En esta sección verá cómo consumir una aplicación de API partir de código de ASP.NET Web API.

### Generación de código cliente

Puede generar código cliente para una aplicación de API con Visual Studio o desde la línea de comandos. En este tutorial se usa Visual Studio. Para más información sobre cómo hacerlo desde la línea de comandos, consulte el archivo Léame del repositorio [Azure/autorest](https://github.com/azure/autorest) en GitHub.com.

El proyecto ToDoListAPI ya tiene el código de cliente generado, pero lo eliminará y lo volverá a crear para ver cómo se hace.

1. En el **Explorador de soluciones** de Visual Studio, en el proyecto ToDoListAPI, elimine la carpeta *ToDoListDataAPI*.

	Esta carpeta se creó mediante el proceso de generación de código que está punto de recorrer.

	![](./media/app-service-api-dotnet-get-started/deletecodegen.png)

2. Haga clic con el botón derecho en el proyecto ToDoListAPI y después en **Agregar > Cliente de API de REST**.

	![](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. En el cuadro de diálogo **Agregar cliente de API de REST**, haga clic en **URL de Swagger** y luego haga clic en **Seleccionar activo de Azure**.

	![](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. En el cuadro de diálogo **Servicio de aplicaciones**, expanda el grupo de recursos que utiliza en este tutorial y seleccione la aplicación de API y, a continuación, haga clic en **Aceptar**.

	![](./media/app-service-api-dotnet-get-started/codegenselect.png)

	Este cuadro de diálogo ofrece más de una manera de organizar las aplicaciones de API en la lista si tiene demasiadas para desplazarse por ellas. También permite especificar una cadena de búsqueda para filtrar las aplicaciones de API por nombre.

	Observe que al volver al cuadro de diálogo **Agregar cliente de API de REST**, el cuadro de texto se ha rellenado con la dirección URL de la definición de API que vio anteriormente en el portal.

	![](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	Como alternativa para obtener los metadatos para la generación del código, puede escribir la dirección URL directamente en lugar de utilizar el cuadro de diálogo Examinar. Otra alternativa es usar la opción **Seleccionar un archivo de metadatos de Swagger existente**. Por ejemplo, si desea generar el código de cliente antes de implementarlo en Azure, puede ejecutar el proyecto de Web API localmente, ir a la dirección URL que proporciona el archivo JSON de Swagger, guardar el archivo y seleccionarlo aquí.

9. En el cuadro de diálogo **Agregar cliente de API de REST**, haga clic en **Aceptar**.

	Visual Studio crea una carpeta con el nombre de la aplicación de API y genera clases cliente.

	![](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. En el proyecto ToDoListAPI, abra *Controllers\\ToDoListController.cs* para ver el código que llama a la API mediante el cliente generado.

	El siguiente fragmento de código muestra cómo el código crea instancias del objeto de cliente y llama al método Get.

		private ToDoListDataAPI db = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

	El parámetro de constructor obtiene la dirección URL del punto de conexión de la configuración de la aplicación `toDoListDataAPIURL`. En el archivo Web.config, ese valor se establece en la dirección URL local de IIS Express del proyecto de API para que pueda ejecutar la aplicación localmente. Si se omite el parámetro del constructor, el punto de conexión predeterminado es la dirección URL a partir de la cual se genera el código.

6. La clase de cliente se generará con un nombre diferente según el nombre de la aplicación de API; cambie este código en *Controllers\\ToDoListController.cs* para que el nombre del tipo coincida con lo que se generó en el proyecto. Por ejemplo, si asignó el nombre ToDoListDataAPI0121 a la aplicación de API, el código sería similar al del ejemplo siguiente:

		private ToDoListDataAPI0121 db = new ToDoListDataAPI0121(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

### Creación de una aplicación de API para hospedar el nivel intermedio

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto ToDoListAPI (no ToDoListDataAPI) y, luego, haga clic en **Publicar**.

3.  En la pestaña **Perfil** del Asistente para **publicación web**, haga clic en **Servicio de aplicaciones de Microsoft Azure**.

5. En el cuadro de diálogo **Servicio de aplicaciones**, haga clic en **Nuevo**.

3. En la pestaña **Hospedaje** del cuadro de diálogo **Crear servicio de aplicaciones**, escriba un **Nombre de la aplicación de API** que sea único en el dominio *azurewebsites.net*.

5. Elija la **suscripción** de Azure con la que desea trabajar.

6. En la lista desplegable **Grupo de recursos**, elija el grupo de recursos que creó anteriormente.

4. En la lista desplegable **Plan del Servicio de aplicaciones**, elija el plan que creó anteriormente. Usará ese valor de forma predeterminada.

7. Haga clic en **Crear**.

	Visual Studio crea la aplicación de API, crea un perfil de publicación para ella y muestra el paso **Conexión** del asistente **Publicación web**.

3.  En el paso **Conexión** del Asistente para **publicación web**, haga clic en **Publicar**.

	Visual Studio implementa el proyecto ToDoListAPI en la nueva aplicación de API y abre un explorador en la dirección URL de la aplicación de API. Aparece una página "creado correctamente".

### Configuración de la dirección URL de aplicación de API de nivel de datos en la aplicación de API de nivel intermedio

Si llamara ahora a la aplicación de API de nivel intermedio, intentaría llamar a la capa de datos usando la dirección URL localhost que todavía está en el archivo Web.config. En esta sección agregará la dirección URL de la aplicación de API de nivel de datos a una configuración del entorno de la aplicación de API de nivel intermedio. Cuando el código de la aplicación de API de nivel medio recupera la configuración de URL del nivel de datos, la configuración del entorno invalida lo que hay en el archivo Web.config.
 
1. Vaya al [Portal de Azure](https://portal.azure.com/) y, después, vaya a la hoja **Aplicación de API** de la aplicación de API que creó para hospedar el proyecto TodoListAPI (nivel intermedio).

2. En la hoja **Configuración** de la aplicación de API, haga clic en **Configuración de la aplicación**.
 
4. En la hoja **Configuración de la aplicación** de la aplicación de API, vaya a la sección **Configuración de la aplicación** y agregue la siguiente clave y valor:

	| **Clave** | toDoListDataAPIURL |
	|---|---|
	| **Valor** | https://{your nombre de aplicación de API de nivel de datos}.azurewebsites.net |
	| **Ejemplo** | https://todolistdataapi0121.azurewebsites.net |

4. Haga clic en **Guardar**.

	![](./media/app-service-api-dotnet-get-started/asinportal.png)

	Cuando el código se ejecuta en Azure, este valor anulará ahora la dirección URL de host local que se encuentra en el archivo Web.config.

### Prueba para comprobar que ToDoListAPI llama a ToDoListDataAPI

11. En una ventana del explorador, vaya a la dirección URL de la nueva aplicación de API de nivel intermedio que acaba de crear (para llegar allí, haga clic en la dirección URL en la hoja principal de la aplicación de API en el Portal).

13. En la barra de direcciones del explorador, agregue "swagger" a la URL y presione Entrar. (La dirección URL será `http://{apiappname}.azurewebsites.net/swagger`).

	El explorador muestra la misma interfaz de usuario de Swagger que vio anteriormente para ToDoListDataAPI, pero ahora `owner` no es un campo obligatorio para la operación Get, ya que la aplicación de API de nivel intermedio envía dicho valor a la aplicación de API de capa de datos automáticamente. (Al realizar los tutoriales de autenticación, el nivel intermedio enviará los identificadores de usuario reales al parámetro `owner`; por ahora, se incluye un asterisco en el código).

12. Pruebe el método Get y los otros métodos para validar que la aplicación de API de nivel intermedio llama correctamente a la aplicación de API de capa de datos.

	![](./media/app-service-api-dotnet-get-started/midtierget.png)

## <a id="creating"></a> Opcional: creación de un proyecto de aplicación de API desde cero

En este tutorial descargará proyectos de ASP.NET Web API para implementar en el Servicio de aplicaciones, en lugar de crear nuevos proyectos desde cero. Para crear un proyecto que desee implementar en una aplicación de API, puede crear un proyecto de Web API típico e instalar el paquete Swashbuckle, o bien puede usar la plantilla **Aplicación de API de Azure** para nuevos proyectos. Para usar dicha plantilla, haga clic en **Archivo > Nuevo > Proyecto > Aplicación web ASP.NET > Aplicación de API de Azure**.

![](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

La elección de la plantilla de proyecto **Aplicación de API de Azure** equivale a elegir la plantilla **Vacía** de ASP.NET 4.5.2, hacer clic en la casilla para agregar compatibilidad con Web API e instalar el paquete Swashbuckle. Además, la plantilla agrega algún código de configuración de Swashbuckle diseñado para evitar la creación de identificadores de operación de Swagger duplicados.

## Opcional: Cambio de un tipo de aplicación

Como se explicó anteriormente, la única diferencia entre las aplicaciones de API, las aplicaciones web y aplicaciones móviles es la manera en que se representan en el Portal. Como todas tienen las mismas características, nunca es necesario cambiar un tipo de aplicación.

Sin embargo, si desea cambiar la representación en el Portal, es fácil de hacer. Por ejemplo, puede cambiar una de las aplicaciones de API que acaba de crear a una aplicación web; para ello, siga estos pasos.

1. Abra el Explorador de recursos.

2. En el panel de navegación izquierdo, expanda las **suscripciones** y luego expanda la suscripción con la que ha estado trabajando.

4. Expanda **resourceGroups** y, después, expanda el grupo de recursos con el que ha estado trabajando.

5. Expanda **Microsoft.Web**, expanda los **sitios** y seleccione la aplicación de API que desea cambiar.

6. Haga clic en **Editar**.

8. Busque la propiedad `kind` y cámbiela de "api" a "WebApp".

	![](./media/app-service-api-dotnet-get-started/resexp.png)

9. Haga clic en **Put**.

10. Vaya al Portal de Azure y verá que el icono ha cambiado para reflejar el nuevo tipo de aplicación.

## Opcional: Dirección URL de definición de API en plantillas del Administrador de recursos de Azure

En este tutorial, vimos la dirección URL de la definición de API en Visual Studio y en el Portal de Azure. La dirección URL de la definición de API de una aplicación de API también se puede configurar mediante las [plantillas del Administrador de recursos de Azure](../powershell-install-configure.md) de las herramientas de línea de comandos como [Azure PowerShell](../xplat-cli-install.md) y la [CLI de Azure](../resource-group-authoring-templates.md).

Para ver un ejemplo de una plantilla del Administrador de recursos de Azure que establece la propiedad de definición de API, abra el archivo [azuredeploy.json del repositorio de la aplicación de ejemplo de este tutorial](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json). Busque la sección de la plantilla similar a la del ejemplo siguiente:

		"apiDefinition": {
		  "url": "https://todolistdataapi.azurewebsites.net/swagger/docs/v1"
		}

## Pasos siguientes

En este tutorial vimos cómo crear aplicaciones de API, implementar código en ellas, generar código de cliente para ellas y consumirlas desde clientes .NET. El siguiente tutorial de la serie de introducción a Aplicaciones de API muestra cómo [consumir aplicaciones de API desde clientes de JavaScript mediante CORS](app-service-api-cors-consume-javascript.md).

<!---HONumber=AcomDC_0309_2016-->