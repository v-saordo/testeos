
<properties 
	pageTitle="Personalización de definiciones de API generadas por Swashbuckle" 
	description="Aprenda a personalizar las definiciones de la API de Swagger que genera Swashbuckle para una aplicación de API del Servicio de aplicaciones de Azure." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/22/2016" 
	ms.author="bradyg"/>

# Personalización de definiciones de API generadas por Swashbuckle 

## Información general

Este artículo explica cómo personalizar Swashbuckle para administrar escenarios comunes donde desea alterar el comportamiento predeterminado:

* Swashbuckle genera identificadores de operación duplicados para las sobrecargas de los métodos de controlador
* Swashbuckle asume que la única respuesta válida de un método es HTTP 200 (OK) 
 
## Personalización de la generación de identificadores de operación

Swashbuckle genera identificadores de operación de Swagger concatenando el nombre del controlador y el nombre del método. Este patrón ocasiona un problema cuando se tienen varias sobrecargas de un método: Swashbuckle genera identificadores de operación duplicados, lo cual supone un JSON de Swagger no válido.

Por ejemplo, el siguiente código de controlador hace que Swashbuckle genere tres identificadores de operación Contact\_Get.

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsincode.png)

![](./media/app-service-api-dotnet-swashbuckle-customize/multiplegetsinjson.png)

Puede solucionar el problema manualmente proporcionando a los métodos nombres únicos, como el siguiente en este ejemplo:

* Obtener
* GetById
* GetPage

La alternativa es ampliar Swashbuckle para que genere automáticamente identificadores de operación únicos.

Los pasos siguientes muestran cómo personalizar Swashbuckle mediante el archivo *SwaggerConfig.cs*, que se incluye en el proyecto, usando la plantilla de proyecto de vista previa de aplicaciones de API de Visual Studio. También puede personalizar Swashbuckle en un proyecto de API web que configure para implementarlo como una aplicación de API.

1. Creación de una implementación de `IOperationFilter` personalizada 

	La interfaz de `IOperationFilter` proporciona un punto de extensibilidad para los usuarios de Swashbuckle que quieran personalizar varios aspectos del proceso de metadatos de Swagger. El código siguiente demuestra un método para cambiar el comportamiento de generación de identificadores de operación. El código anexa los nombres de parámetro al nombre de identificador de operación.

		using Swashbuckle.Swagger;
		using System.Web.Http.Description;
		
		namespace ContactsList
		{
		    public class MultipleOperationsWithSameVerbFilter : IOperationFilter
		    {
		        public void Apply(
		            Operation operation,
		            SchemaRegistry schemaRegistry,
		            ApiDescription apiDescription)
		        {
		            if (operation.parameters != null)
		            {
		                operation.operationId += "By";
		                foreach (var parm in operation.parameters)
		                {
		                    operation.operationId += string.Format("{0}",parm.name);
		                }
		            }
		        }
		    }
		}

2. En el archivo *App\_Start\\SwaggerConfig.cs*, llame al método `OperationFilter` para hacer que Swashbuckle use la nueva implementación de `IOperationFilter`.

		c.OperationFilter<MultipleOperationsWithSameVerbFilter>();

	![](./media/app-service-api-dotnet-swashbuckle-customize/usefilter.png)

	El archivo *SwaggerConfig.cs* que incluye el paquete NuGet de Swashbuckle contiene muchos ejemplos comentados de puntos de extensibilidad. Aquí no se muestran los comentarios adicionales.

	Después de realizar este cambio, se usa su implementación de `IOperationFilter` y da lugar a que se generen identificadores de operación únicos.
 
	![](./media/app-service-api-dotnet-swashbuckle-customize/uniqueids.png)

<a id="multiple-response-codes" name="multiple-response-codes"></a>
	
## Permitir códigos de respuesta distintos de 200

De forma predeterminada, Swashbuckle asume que una respuesta HTTP 200 (OK) es la *única* respuesta válida de un método de API web. En algunos casos, puede que desee devolver otros códigos de respuesta sin hacer que el cliente provoque una excepción. Por ejemplo, el siguiente código de API web muestra un escenario en el que se desea que el cliente acepte 200 o 404 como respuestas válidas.

	[ResponseType(typeof(Contact))]
    public HttpResponseMessage Get(int id)
    {
        var contacts = GetContacts();

        var requestedContact = contacts.FirstOrDefault(x => x.Id == id);

        if (requestedContact == null)
        {
            return Request.CreateResponse(HttpStatusCode.NotFound);
        }
        else
        {
            return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
        }
    }

En este escenario, el Swagger que Swashbuckle genera de forma predeterminada especifica un solo código de estado HTTP válido, HTTP 200.

![](./media/app-service-api-dotnet-swashbuckle-customize/http-200-output-only.png)

Puesto que Visual Studio utiliza la definición de la API de Swagger para generar código para el cliente, crea código de cliente que provoca una excepción en cualquier respuesta que no sea HTTP 200. El código siguiente es de un cliente de C# generado para este método de API web de muestra.

	if (statusCode != HttpStatusCode.OK)
    {
        HttpOperationException<object> ex = new HttpOperationException<object>();
        ex.Request = httpRequest;
        ex.Response = httpResponse;
        ex.Body = null;
        if (shouldTrace)
        {
            ServiceClientTracing.Error(invocationId, ex);
        }
        throw ex;
    } 

Swashbuckle proporciona dos maneras de personalizar la lista de códigos de respuesta HTTP esperados que genera, mediante comentarios XML o el atributo `SwaggerResponse`. El atributo es más sencillo, pero solo está disponible en Swashbuckle 5.1.5, o en cualquier versión posterior. La plantilla de proyecto nuevo de vista previa de Aplicaciones de API de Visual Studio 2013 incluye Swashbuckle versión 5.0.0, por lo que si usó dicha plantilla y no desea actualizar Swashbuckle, su única opción es utilizar comentarios XML.

### Personalización de códigos de respuesta esperados mediante comentarios XML

Este método se utiliza para especificar códigos de respuesta si la versión de Swashbuckle es anterior a la 5.1.5.

1. En primer lugar, agregue comentarios de documentación XML a través de los métodos para los que desee especificar códigos de respuesta HTTP. Si se toma de la acción de la API web de muestra anterior y se le aplica la documentación XML, se generaría un código similar al del ejemplo siguiente. 

		/// <summary>
		/// Returns the specified contact.
		/// </summary>
		/// <param name="id">The ID of the contact.</param>
		/// <returns>A contact record with an HTTP 200, or null with an HTTP 404.</returns>
		/// <response code="200">OK</response>
		/// <response code="404">Not Found</response>
		[ResponseType(typeof(Contact))]
		public HttpResponseMessage Get(int id)
		{
		    var contacts = GetContacts();
		
		    var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
		
		    if (requestedContact == null)
		    {
		        return Request.CreateResponse(HttpStatusCode.NotFound);
		    }
		    else
		    {
		        return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
		    }
		}

1. Agregue instrucciones al archivo *SwaggerConfig.cs* que indiquen a Swashbuckle que haga del archivo de documentación XML.

	* Abra *SwaggerConfig.cs* y cree un método en la clase *SwaggerConfig* para especificar la ruta de acceso al archivo de documentación XML. 

			private static string GetXmlCommentsPath()
			{
			    return string.Format(@"{0}\XmlComments.xml", 
			        System.AppDomain.CurrentDomain.BaseDirectory);
			}

	* Desplácese hacia abajo por el archivo *SwaggerConfig.cs* hasta que vea la línea de código con comentario similar a la de la captura de pantalla siguiente.

		![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-commented-out.png)
	
	* Quite la marca de comentario de la línea para habilitar el procesamiento de comentarios XML durante la generación de Swagger.
	
		![](./media/app-service-api-dotnet-swashbuckle-customize/xml-comments-uncommented.png)
	
1. Para generar el archivo de documentación XML, vaya a las propiedades del proyecto y habilite el archivo de documentación XML como se muestra en la captura de pantalla siguiente.

	![](./media/app-service-api-dotnet-swashbuckle-customize/enable-xml-documentation-file.png)

Una vez que realice estos pasos, el JSON de Swagger que ha generado Swashbuckle reflejará los códigos de respuesta HTTP especificados en los comentarios XML. La captura de pantalla siguiente muestra esta nueva carga JSON.

![](./media/app-service-api-dotnet-swashbuckle-customize/swagger-multiple-responses.png)

Si se utiliza Visual Studio para volver a generar el código de cliente de la API de REST, el código C# acepta los códigos de estado HTTP OK y No encontrado sin provocar una excepción, lo que permite que el código utilizado tome decisiones sobre cómo tratar la devolución de un registro Contact nulo.

		if (statusCode != HttpStatusCode.OK && statusCode != HttpStatusCode.NotFound)
		{
		    HttpOperationException<object> ex = new HttpOperationException<object>();
		    ex.Request = httpRequest;
		    ex.Response = httpResponse;
		    ex.Body = null;
		    if (shouldTrace)
		    {
		        ServiceClientTracing.Error(invocationId, ex);
		    }
        	    throw ex;
		}

El código de esta demostración se puede encontrar en [este repositorio de GitHub](https://github.com/Azure-Samples/app-service-api-dotnet-swashbuckle-swaggerresponse). Junto con el proyecto de API web marcado con comentarios de documentación XML hay un proyecto de Aplicación de consola que contiene un cliente generado para esta API.

### Personalización de códigos de respuesta esperados mediante el atributo SwaggerResponse

El atributo [SwaggerResponse](https://github.com/domaindrivendev/Swashbuckle/blob/master/Swashbuckle.Core/Swagger/Annotations/SwaggerResponseAttribute.cs) está disponible en Swashbuckle 5.1.5, y en las versiones posteriores. Si tiene una versión anterior en su proyecto, al principio de esta sección se explica cómo actualizar el paquete de NuGet de Swashbuckle para que pueda usar este atributo.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto de la API web y haga clic en **Administrar paquetes de NuGet**. 

	![](./media/app-service-api-dotnet-swashbuckle-customize/manage-nuget-packages.png)

1. Haga clic en el botón *Actualizar* situado junto al paquete de NuGet de *Swashbuckle*.

	![](./media/app-service-api-dotnet-swashbuckle-customize/update-nuget-dialog.png)

1. Agregue los atributos *SwaggerResponse* atributos a los métodos de acción de la API web para los que desea especificar códigos de respuesta HTTP válidos.

		[SwaggerResponse(HttpStatusCode.OK)]
		[SwaggerResponse(HttpStatusCode.NotFound)]
		[ResponseType(typeof(Contact))]
		public HttpResponseMessage Get(int id)
		{
		    var contacts = GetContacts();

		    var requestedContact = contacts.FirstOrDefault(x => x.Id == id);
		    if (requestedContact == null)
		    {
		        return Request.CreateResponse(HttpStatusCode.NotFound);
		    }
		    else
		    {
		        return Request.CreateResponse<Contact>(HttpStatusCode.OK, requestedContact);
		    }
		}

2. Agregue una instrucción `using` para el espacio de nombres del atributo:

		using Swashbuckle.Swagger.Annotations;
		
1. Navegue a la dirección URL */swagger/docs/v1* del proyecto y los distintos códigos de respuesta HTTP se verán en el JSON de Swagger.

	![](./media/app-service-api-dotnet-swashbuckle-customize/multiple-responses-post-attributes.png)

El código de esta demostración se puede encontrar en [este repositorio de GitHub](https://github.com/Azure-Samples/API-Apps-DotNet-Swashbuckle-Customization-MultipleResponseCodes-With-Attributes). Junto con el proyecto de API web que contiene el atributo *SwaggerResponse* hay un proyecto de Aplicación de consola que contiene un cliente generado para esta API.

## Pasos siguientes

En este artículo se ha mostrado cómo personalizar la forma en que Swashbuckle genera identificadores de operación y códigos de respuesta válidos. Para obtener más información, consulte [Swashbuckle en GitHub](https://github.com/domaindrivendev/Swashbuckle).
 

<!---HONumber=AcomDC_0302_2016-->