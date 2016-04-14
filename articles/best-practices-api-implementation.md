<properties
   pageTitle="Guía de implementación de API | Microsoft Azure"
   description="Guía sobre cómo implementar una API."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="rest-api"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/17/2015"
   ms.author="masashin"/>

# Guía de implementación de API

![](media/best-practices-api-implementation/pnp-logo.png)


Algunos temas de esta guía se encuentran bajo examen y podrían cambiar en el futuro. Agradecemos sus comentarios.

## Información general
Una API web de RESTful diseñada cuidadosamente define los recursos, las relaciones y los esquemas de navegación a los que tienen acceso las aplicaciones cliente. Al implementar una API web, debe considerar los requisitos físicos del entorno que hospeda la API web y la forma en que se construye la misma, en lugar de la estructura lógica de los datos. Esta guía se centra en los procedimientos recomendados para implementar una API web y publicarla para que esté disponible para las aplicaciones cliente. Los problemas de seguridad se describen por separado en el documento de guía de seguridad de las API. Puede encontrar información detallada sobre el diseño de la API web en el documento Guía de diseño de una API.

## Consideraciones para la implementación de una API web de RESTful
En las secciones siguientes se muestran prácticas recomendadas para usar la plantilla de API web de ASP.NET a fin de crear una API web de RESTful. Para obtener información detallada sobre el uso de la plantilla API Web, visite la página [Más información sobre API Web de ASP.NET](http://www.asp.net/web-api) del sitio web de Microsoft.

## Consideraciones para la implementación del enrutado de solicitudes

En un servicio implementado mediante la API Web de ASP.NET, cada solicitud se enruta a un método de la clase _controller_. El marco API Web ofrece dos opciones principales para implementar el enrutamiento: _basado en convenciones_ y _basado en atributos_. Al determinar la mejor manera de enrutar las solicitudes de la API web, tenga en cuenta los siguientes puntos:

- **Comprenda las limitaciones y los requisitos del enrutamiento basado en convenciones**.

	De forma predeterminada, el marco API Web usa enrutamiento basado en convenciones. El marco API Web crea una tabla de enrutamiento inicial que contiene la siguiente entrada:

	```C#
	config.Routes.MapHttpRoute(
  		name: "DefaultApi",
	  	routeTemplate: "api/{controller}/{id}",
	  	defaults: new { id = RouteParameter.Optional }
	);
	```

	Las rutas pueden ser genéricas y se componen de los literales como _api_ y variables como _{controller}_ e _{id}_. El enrutamiento basado en convenciones permite que algunos elementos de la ruta sean opcionales. El marco API Web determina el método que se invoca en el controlador mediante la coincidencia del método HTTP de la solicitud con la parte inicial del nombre del método de la API y, después, mediante la coincidencia de los parámetros opcionales. Por ejemplo, si un controlador llamado _orders_ contiene los métodos _GetAllOrders()_ o _GetOrderByInt(int id)_, la solicitud GET\__http://www.adventure-works.com/api/orders/_ se dirigirá al método _GetAllOrders()_ y la solicitud GET\__http://www.adventure-works.com/api/orders/99_ se enrutará al método _GetOrderByInt(int id)_. Si no hay ningún método coincidente disponible que comience por el prefijo Get en el controlador, el marco API Web responde con un mensaje HTTP 405 (método no permitido). Además, el nombre del parámetro (id) especificado en la tabla de enrutamiento debe ser el mismo que el nombre del parámetro del método _GetOrderById_; en caso contrario, el marco API Web le responderá con una respuesta HTTP 404 (no encontrado).

	Las mismas reglas se aplican a las solicitudes HTTP POST, PUT y DELETE; una solicitud PUT que actualice los detalles del pedido 101 se dirigiría al URI\__http://www.adventure-works.com/api/orders/101_, el cuerpo del mensaje contendrá los nuevos detalles del pedido y esta información se pasará como un parámetro a un método en el controlador de pedidos con un nombre que comience con el prefijo _Put_, como _PutOrder_.

	La tabla de enrutamiento predeterminada no coincidirá con una solicitud que haga referencia a recursos secundarios en una API web de RESTful como\__http://www.adventure-works.com/api/customers/1/orders_ (encontrar los detalles de todos los pedidos que realizó el cliente 1). Para controlar estos casos, puede agregar rutas personalizadas a la tabla de enrutamiento:

	```C#
	config.Routes.MapHttpRoute(
	    name: "CustomerOrdersRoute",
	    routeTemplate: "api/customers/{custId}/orders",
	    defaults: new { controller="Customers", action="GetOrdersForCustomer" })
	);
	```

	Esta ruta dirige las solicitudes que coincidan con el URI al método _GetOrdersForCustomer_ del controlador _Customers_. Este método debe tomar un solo parámetro denominado _custI_:

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    public IEnumerable<Order> GetOrdersForCustomer(int custId)
	    {
	        // Find orders for the specified customer
	        var orders = ...
	        return orders;
	    }
	    ...
	}
	```

	> [AZURE.TIP] Use el enrutamiento predeterminado siempre que sea posible y evite la definición de muchas rutas personalizadas complejas, ya que puede provocar fragilidad (es muy fácil agregar métodos en un controlador con rutas ambiguas) y un rendimiento reducido (cuanto mayor sea la tabla de enrutamiento, más trabajo deberá hacer el marco API Web para averiguar qué ruta coincide con un URI determinado). Mantenga la API y las rutas simples. Para obtener más información, consulte la sección Organización de la API web en torno a los recursos en la Guía de diseño de una API. Si debe definir rutas personalizadas, un enfoque preferible es usar el enrutamiento basado en atributos que se describe más adelante en esta sección.

	Para obtener más información sobre el enrutamiento basado en convenciones, consulte la página [Routing in ASP.NET Web API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) del sitio web de Microsoft.

- **Evite la ambigüedad en el enrutamiento**.

	El enrutamiento basado convenciones podría resultar en caminos ambiguos si varios métodos de un controlador coinciden con la misma ruta. En estas situaciones, el marco API Web devuelve un mensaje de respuesta HTTP 500 (error interno del servidor) que contiene el texto "Se encontraron varias acciones que coinciden con la solicitud".

- **Prefiera el enrutamiento basado en atributos**.

	El enrutamiento basado en atributos ofrece una alternativa para conectar las rutas a los métodos de un controlador. En lugar de depender de las características que coinciden con patrones del enrutamiento basado en convenciones, puede anotar los métodos de un controlador con los detalles de la ruta a la que deberían estar asociados. Este enfoque ayuda a quitar posibles ambigüedades. Además, como se definen rutas explícitas en tiempo de diseño, este enfoque es más eficaz que el enrutamiento basado en convenciones en tiempo de ejecución. El código siguiente muestra cómo aplicar el atributo _Route_ a métodos en el controlador Customers. Estos métodos también usan el atributo HttpGet para indicar que deben responder a las solicitudes _HTTP GET_. Este atributo le permite asignar un nombre a los métodos mediante cualquier esquema de nombre conveniente, en lugar del esperado por el enrutamiento basado en convenciones. También puede anotar métodos con los atributos _HttpPost_, _HttpPut_, y _HttpDelete_ para definir métodos que respondan a otros tipos de solicitudes HTTP.

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers/{id}")]
	    [HttpGet]
	    public Customer FindCustomerByID(int id)
	    {
	        // Find the matching customer
	        var customer = ...
	        return customer;
	    }
	    ...
	    [Route("api/customers/{id}/orders")]
	    [HttpGet]
	    public IEnumerable<Order> FindOrdersForCustomer(int id)
	    {
	        // Find orders for the specified customer
	        var orders = ...
	        return orders;
	    }
	    ...
	}
	```

	El enrutamiento basado en atributos también tiene el práctico efecto secundario de actuar como documentación para los desarrolladores que deberán mantener el código en el futuro; queda inmediatamente claro qué método pertenece a cada ruta y el atributo _HttpGet_ aclara el tipo de solicitud HTTP a la que el método responde.

	El enrutamiento basado en atributos le permite definir restricciones que limiten cómo se realiza la coincidencia de los parámetros. Las restricciones pueden especificar el tipo del parámetro y, en algunos casos, también pueden indicar el intervalo aceptable de valores del parámetro. En el ejemplo siguiente, el parámetro id del método _FindCustomerByID_ debe ser un entero no negativo. Si una aplicación envía una solicitud HTTP GET con un número de cliente negativo, el marco API Web responderá con un mensaje HTTP 405 (método no permitido):

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers/{id:int:min(0)}")]
	    [HttpGet]
	    public Customer FindCustomerByID(int id)
	    {
	        // Find the matching customer
	        var customer = ...
	        return customer;
	    }
	    ...
	}
	```

	Para obtener más información sobre el enrutamiento basado en atributos, consulte la página [Attribute Routing in Web API 2](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) del sitio web de Microsoft.

- **Admita caracteres Unicode en las rutas**.

	Las claves utilizadas para identificar los recursos en las solicitudes GET podrían ser cadenas. Por lo tanto, en una aplicación global, es posible que necesite admitir URI que contengan caracteres distintos de los ingleses.

- **Distinga los métodos que no deberían enrutarse**.

	Si usa el enrutamiento basado en convenciones, indique los métodos que no correspondan a acciones HTTP decorándolos con el atributo _NonAction_. Esto se aplica normalmente a los métodos auxiliares definidos para que los usen otros métodos dentro de un controlador, ya que este atributo evitará las coincidencias de estos métodos y que se invoquen mediante una solicitud HTTP errónea.

- **Tenga en cuenta las ventajas e inconvenientes de colocar la API en un subdominio**.

	De forma predeterminada, la API web de ASP.NET organiza las API en el directorio _/api_ de un dominio, como \__http://www.adventure-works.com/api/orders_. Este directorio se encuentra en el mismo dominio que los demás servicios expuestos por el mismo host. Puede resultar útil dividir la API web en su propio subdominio que se ejecuta en un host independiente, con URI como \__http://api.adventure-works.com/orders_. Esta separación le permite crear particiones y escalar la API web de forma más eficaz sin que afecte a otras aplicaciones web o servicios que se ejecuten en el dominio _www.adventure-works.com_.

	Sin embargo, la colocación de una API web en un subdominio distinto puede provocar también problemas de seguridad. Los servicios o aplicaciones web hospedados en _www.adventure-works.com_ que invoquen una API web que se ejecuta en otro lugar pueden infringir la directiva de mismo origen de muchos exploradores web. En este caso, será necesario habilitar el Uso compartido de recursos entre orígenes (CORS) entre los hosts. Para obtener más información, consulte el documento Guía de seguridad de las API.

## Consideraciones para el procesamiento de solicitudes

Cuando una solicitud de una aplicación cliente se ha enrutado correctamente a un método en una API web, la solicitud debe procesarse de manera tan eficaz como sea posible. Tenga en cuenta los puntos siguientes cuando implemente el código para controlar las solicitudes:

- **Las acciones GET, PUT, DELETE, HEAD y PATCH deben ser idempotentes**.

	El código que implementa estas solicitudes no debe imponer efectos secundarios. La misma solicitud repetida en el mismo recurso debe dar como resultado el mismo estado. Por ejemplo, si se envían varias solicitudes DELETE al mismo URI, deben tener el mismo efecto, aunque el código de estado HTTP de los mensajes de respuesta puede ser diferente (la primera solicitud DELETE podría devolver el código de estado 204 (sin contenido), mientras que una solicitud DELETE posterior podría devolver el código de estado 404 (no encontrado)).

> [AZURE.NOTE] En el artículo [Idempotency Patterns](http://blog.jonathanoliver.com/idempotency-patterns/) del blog de Jonathan Oliver se ofrece una visión general de la idempotencia y cómo se relaciona con las operaciones de administración de datos.

- **Las acciones POST que crean nuevos recursos deben hacerlo sin efectos secundarios no relacionados**.

	Si el objetivo de una solicitud POST es crear un recurso nuevo, los efectos de la solicitud deben limitarse al nuevo recurso (y posiblemente cualquier recurso relacionado directamente si hay algún tipo de vinculación implicada). Por ejemplo, en un sistema de comercio electrónico, una solicitud POST que crea un nuevo pedido para un cliente podría también modificar los niveles del inventario y generar información de facturación, pero no debería modificar la información no relacionada directamente con el pedido o tener otros efectos secundarios en el estado general del sistema.

- **Evite la implementación de operaciones POST, PUT y DELETE en fragmentos**.

	Admita las solicitudes POST, PUT y DELETE en las colecciones de recursos. Una solicitud POST puede contener los detalles de varios recursos nuevos y agregarlos todos a la misma colección, una solicitud PUT puede reemplazar todo el conjunto de recursos de una colección y una solicitud DELETE puede eliminar una colección completa.

	Tenga en cuenta que la compatibilidad con OData que se incluye en API Web 2 de ASP.NET ofrece la capacidad de las solicitudes por lotes. Una aplicación cliente puede empaquetar varias solicitudes de API web, enviarlas al servidor en una sola solicitud HTTP y recibir una respuesta HTTP única que contenga las respuestas a cada solicitud. Para obtener más información, consulte la página [Introducing Batch Support in Web API and Web API OData](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx) del sitio web de Microsoft.

- **Cumpla el protocolo HTTP cuando envíe una respuesta de vuelta a una aplicación cliente**.

	Una API web debe devolver mensajes que contengan el código de estado HTTP correcto para que el cliente determine cómo quiere controlar el resultado, los encabezados HTTP adecuados para que el cliente entienda la naturaleza de los resultados y un cuerpo con el formato apropiado para que el cliente analice los resultados. Si está usando la plantilla API Web de ASP.NET, la estrategia predeterminada para la implementación de métodos que respondan a solicitudes HTTP POST es simplemente devolver una copia del recurso recién creado, tal como se muestra en el ejemplo siguiente:

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers")]
	    [HttpPost]
	    public Customer CreateNewCustomer(Customer customerDetails)
	    {
	        // Add the new customer to the repository
	        // This method returns a customer with a unique ID allocated
	        // by the repository
	        var newCust = repository.Add(customerDetails);
	        // Return the newly added customer
	        return newCust;
	    }
	    ...
	}
	```

	Si la operación POST es correcta, el marco de la API Web crea una respuesta HTTP con el código de estado 200 (correcto) y los detalles del cliente como el cuerpo del mensaje. Sin embargo, en este caso, según el protocolo HTTP, una operación POST debe devolver el código de estado 201 (creado) y el mensaje de respuesta debe incluir el URI del recurso recién creado en el encabezado Location del mensaje de respuesta.

	Para proporcionar estas características, devuelva su propio mensaje de respuesta HTTP mediante el uso de la interfaz `IHttpActionResult`. Este enfoque le concede un control exhaustivo del código de estado HTTP, los encabezados del mensaje de respuesta e incluso el formato de los datos en el cuerpo del mensaje de respuesta, como se muestra en el ejemplo de código siguiente. Esta versión del método `CreateNewCustomer` se ajusta mejor a las expectativas del cliente que sigue el protocolo HTTP. El método `Created` de la clase `ApiController` construye el mensaje de respuesta a partir de los datos especificados y agrega el encabezado Location a los resultados:

	```C#
	public class CustomersController : ApiController
	{
	    ...
	    [Route("api/customers")]
	    [HttpPost]
	    public IHttpActionResult CreateNewCustomer(Customer customerDetails)
	    {
	        // Add the new customer to the repository
	        var newCust = repository.Add(customerDetails);

	        // Create a value for the Location header to be returned in the response
	        // The URI should be the location of the customer including its ID,
	        // such as http://adventure-works.com/api/customers/99
	        var location = new Uri(...);

            // Return the HTTP 201 response,
            // including the location and the newly added customer
	        return Created(location, newCust);
	    }
	    ...
	}
	```

- **Admita la negociación de contenido**.

	El cuerpo de un mensaje de respuesta puede contener datos en una variedad de formatos. Por ejemplo, una solicitud HTTP GET podría devolver datos en formato JSON o XML. Cuando el cliente envía una solicitud, puede incluir un encabezado Accept que especifica los formatos de datos que puede controlar. Estos formatos se especifican como tipos de medios. Por ejemplo, un cliente que emite una solicitud GET para recuperar una imagen puede especificar un encabezado Accept que enumere los tipos de medios que el cliente puede administrar, como por ejemplo "image/jpeg, image/gif, image/png". Cuando la API web devuelve el resultado, debe dar formato a los datos con uno de estos tipos de medios y especificar el formato en el encabezado Content-Type de la respuesta.

	Si el cliente no especifica un encabezado Accept, use un formato predeterminado razonable para el cuerpo de respuesta. Por ejemplo, el tipo predeterminado del marco API Web de ASP.NET es JSON para datos basados en texto.

	> [AZURE.NOTE] El marco API Web de ASP.NET realiza cierta detección automática de los encabezados Accept y los controla él mismo según el tipo de datos del cuerpo del mensaje de respuesta. Por ejemplo, si el cuerpo de un mensaje de respuesta contiene un objeto CLR (common language runtime), la API Web de ASP.NET asigna automáticamente el formato JSON a la respuesta con el encabezado Content-Type de la misma establecido en "application/json"; a menos que el cliente indique que requiere que los resultados sean XML, en cuyo caso el marco de la API Web de ASP.NET asigna el formato XML a la respuesta y establece el encabezado Content-Type de la misma en "text/xml". Sin embargo, puede ser necesario controlar los encabezados Accept que especifiquen explícitamente tipos de medios distintos en el código de implementación de una operación.

- **Proporcione vínculos para admitir la navegación y la detección de recursos de estilo HATEOAS**.

	En la Guía de diseño de una API se describe cómo el enfoque de HATEOAS permite que un cliente navegue y detecte recursos desde un punto de partida inicial. Esto se logra mediante el uso de vínculos que contienen URI; Cuando un cliente emite una solicitud HTTP GET para obtener un recurso, la respuesta debe contener URI que permitan que una aplicación cliente localice rápidamente los recursos directamente relacionados. Por ejemplo, en una API web que admita una solución de comercio electrónico, un cliente puede haber realizado muchos pedidos. Cuando una aplicación cliente recupera los detalles de un cliente, la respuesta debe incluir vínculos que permitan que la aplicación cliente envíe solicitudes HTTP GET que pueden recuperar esos pedidos. Además, los vínculos de estilo HATEOAS deben describir las demás operaciones (POST, PUT, DELETE, etc.) que admite cada recurso vinculado junto con el URI correspondiente para realizar cada solicitud. Este enfoque se describe con más detalle en el documento Guía de diseño de una API.

	Actualmente no hay estándares que rijan la implementación de HATEOAS, pero en el ejemplo siguiente se muestra un posible enfoque. En este ejemplo, una solicitud HTTP GET que encuentre los detalles de un cliente devuelve una respuesta que incluye vínculos de HATEOAS que hacen referencia a los pedidos de ese cliente:

	```HTTP
	GET http://adventure-works.com/customers/2 HTTP/1.1
	Accept: text/json
	...
	```

	```HTTP
	HTTP/1.1 200 OK
	...
	Content-Type: application/json; charset=utf-8
	...
	Content-Length: ...
	{"CustomerID":2,"CustomerName":"Bert","Links":[
	  {"rel":"self",
	   "href":"http://adventure-works.com/customers/2",
	   "action":"GET",
	   "types":["text/xml","application/json"]},
	  {"rel":"self",
	   "href":"http://adventure-works.com/customers/2",
	   "action":"PUT",
	   "types":["application/x-www-form-urlencoded"]},
	  {"rel":"self",
	   "href":"http://adventure-works.com/customers/2",
	   "action":"DELETE",
	   "types":[]},
	  {"rel":"orders",
	   "href":"http://adventure-works.com/customers/2/orders",
	   "action":"GET",
	   "types":["text/xml","application/json"]},
	  {"rel":"orders",
	   "href":"http://adventure-works.com/customers/2/orders",
	   "action":"POST",
	   "types":["application/x-www-form-urlencoded"]}
	]}
	```

	En este ejemplo, los datos del cliente se representan mediante la clase `Customer` que se muestra en el siguiente fragmento de código. Los vínculos de HATEOAS se mantienen en la propiedad de la colección `Links`:

	```C#
	public class Customer
	{
    	public int CustomerID { get; set; }
    	public string CustomerName { get; set; }
    	public List<Link> Links { get; set; }
    	...
	}

	public class Link
	{
    	public string Rel { get; set; }
    	public string Href { get; set; }
    	public string Action { get; set; }
    	public string [] Types { get; set; }
	}
	```

	La operación HTTP GET recupera los datos del cliente del almacenamiento, construye un objeto `Customer` y, después, rellena la colección `Links`. El resultado tiene el formato de un mensaje de respuesta JSON. Cada vínculo consta de los siguientes campos:

	- La relación entre el objeto que se devuelve y el objeto que describe el vínculo. En este caso "self" indica que el vínculo es una referencia al propio objeto (similar a un puntero `this` en muchos lenguajes orientados a objetos) y "orders" es el nombre de una colección que contiene la información de pedido relacionada.

	- El hipervínculo (`Href`) del objeto que describe el vínculo tiene la forma de un URI.

	- El tipo de solicitud HTTP (`Action`) que se puede enviar a este URI.

	- El formato de los datos (`Types`) que deben facilitarse en la solicitud HTTP o que se pueden devolver en la respuesta, según el tipo de la solicitud.

	Los vínculos de HATEOAS que se muestran en la respuesta HTTP de ejemplo indican que una aplicación cliente puede realizar las siguientes operaciones:

	- Una solicitud HTTP GET al URI \__http://adventure-works.com/customers/2_ para capturar los detalles del cliente (nuevamente). Los datos se pueden devolver como XML o JSON.

	- Una solicitud HTTP PUT al URI \__http://adventure-works.com/customers/2_ para modificar los detalles del cliente. Los nuevos datos se deben facilitar en el mensaje de solicitud en formato x-www-form-urlencoded.

	- Una solicitud HTTP DELETE al URI \__http://adventure-works.com/customers/2_ para eliminar el cliente. La solicitud no espera ninguna información adicional ni datos devueltos en el cuerpo del mensaje de respuesta.

	- Una solicitud HTTP GET al URI \__http://adventure-works.com/customers/2/orders_ para encontrar todos los pedidos del cliente. Los datos se pueden devolver como XML o JSON.

	- Una solicitud HTTP PUT al URI \__http://adventure-works.com/customers/2/orders_ para crear un nuevo pedido de este cliente. Los datos se deben facilitar en el mensaje de solicitud en formato x-www-form-urlencoded.

## Consideraciones para el control de excepciones
De forma predeterminada, en el marco de la API Web de ASP.NET, si una operación produce una excepción no detectada, el marco devuelve un mensaje de respuesta el con código de estado HTTP 500 (error interno del servidor). En muchos casos, este enfoque simplista no resulta útil por sí solo y resulta difícil determinar la causa de la excepción. Por lo tanto, debe adoptar un enfoque más completo para el control de excepciones y tener en cuenta los siguientes puntos:

- **Capture las excepciones y devuelva una respuesta significativa a los clientes**.

	El código que implementa una operación HTTP debe proporcionar un control total de las excepciones en lugar de permitir que las excepciones no detectadas se propaguen al marco de la API Web. Si una excepción impide que se complete correctamente la operación, puede devolver la excepción en el mensaje de respuesta, pero debe incluir una descripción significativa del error que provocó la excepción. La excepción también debe incluir el código de estado HTTP adecuado, en lugar de simplemente devolver el código de estado 500 para todas las situaciones. Por ejemplo, si una solicitud de usuario provoca una actualización de la base de datos que infringe una restricción (por ejemplo, intenta eliminar un cliente que tiene pedidos pendientes), se debe devolver el código de estado 409 (conflicto) y un cuerpo de mensaje que indique el motivo del conflicto. Si alguna otra condición hace que la solicitud no se pueda procesar, puede devolver un código de estado 400 (solicitud incorrecta). Puede encontrar una lista completa de códigos de estado HTTP en la página [Status Code Definitions](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) del sitio web de W3C.

	El código siguiente muestra un ejemplo que intercepta diferentes condiciones y devuelve una respuesta adecuada.

	```C#
	[HttpDelete]
	[Route("customers/{id:int}")]
	public IHttpActionResult DeleteCustomer(int id)
	{
		try
		{
			// Find the customer to be deleted in the repository
			var customerToDelete = repository.GetCustomer(id);

			// If there is no such customer, return an error response
			// with status code 404 (Not Found)
			if (customerToDelete == null)
			{
					return NotFound();
			}

			// Remove the customer from the repository
			// The DeleteCustomer method returns true if the customer
			// was successfully deleted
			if (repository.DeleteCustomer(id))
			{
				// Return a response message with status code 204 (No Content)
				// To indicate that the operation was successful
				return StatusCode(HttpStatusCode.NoContent);
			}
			else
			{
				// Otherwise return a 400 (Bad Request) error response
				return BadRequest(Strings.CustomerNotDeleted);
			}
		}
		catch
		{
			// If an uncaught exception occurs, return an error response
			// with status code 500 (Internal Server Error)
			return InternalServerError();
		}
	}
	```

	> [AZURE.TIP] No incluya información que pueda ser útil para un atacante que intente penetrar en la API web. Para obtener más información, visite la página [Exception Handling in ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling) del sitio web de Microsoft.

	> [AZURE.NOTE] Muchos servidores web interceptan condiciones de error por sí mismos antes de que lleguen a la API web. Por ejemplo, si configura la autenticación de un sitio web y el usuario no proporciona la información de autenticación correcta, el servidor web debe responder con código de estado 401 (no autorizado). Cuando un cliente se ha autenticado, el código puede realizar sus propias comprobaciones para asegurarse de que el cliente puede obtener acceso al recurso solicitado. Si se produce un error en esta autorización, debe devolver un código de estado 403 (prohibido).

- **Controle las excepciones de una forma coherente y registre la información sobre los errores**.

	Para controlar las excepciones de una manera coherente, considere la posibilidad de implementar una estrategia global de control de errores para toda la API web. Puede conseguirlo en parte mediante la creación de un filtro de excepciones que se ejecute siempre que un controlador produzca cualquier excepción no controlada que no sea una excepción `HttpResponseException`. Este enfoque se describe en la página [Exception Handling in ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling) del sitio web de Microsoft.

	Sin embargo, hay varias situaciones en las que un filtro de excepciones no detectará una excepción, entre las que se incluyen:

	- Excepciones iniciadas por constructores del controlador.

	- Excepciones iniciadas por controladores de mensajes.

	- Excepciones iniciadas durante el enrutamiento.

	- Excepciones iniciadas mientras se serializa el contenido de un mensaje de respuesta.

	Para controlar estos casos, es posible que tenga que implementar un enfoque más personalizado. También debería incluir el registro de errores que captura los detalles completos de cada excepción; este registro de errores puede contener información detallada siempre y cuando los clientes a través de la web no tengan acceso a ella. El artículo [Web API Global Error Handling](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) del sitio web de Microsoft muestra una manera de llevar a cabo esta tarea.

- **Distinga entre los errores del lado cliente y del lado servidor**.

	El protocolo HTTP distingue entre los errores que se producen debido a la aplicación de cliente (los códigos de estado HTTP 4xx) y los errores causados por un problema en el servidor (los códigos de estado HTTP 5xx). Asegúrese de que respeta esta convención en los mensajes de error de respuesta.

<a name="considerations-for-optimizing"></a>
## Consideraciones para optimizar el acceso a los datos del lado cliente

En un entorno distribuido, como los que implican un servidor web y aplicaciones cliente, una de las principales fuentes de preocupación es la red. Puede actuar como un considerable cuello de botella, especialmente si una aplicación cliente envía solicitudes o recibe datos con frecuencia. Por lo tanto, se debe tratar de minimizar la cantidad de tráfico que fluye a través de la red. Tenga en cuenta los puntos siguientes cuando implemente el código para recuperar y mantener datos:

- **Admita el almacenamiento en caché del lado cliente**.

	El protocolo HTTP 1.1 admite el almacenamiento en caché de los clientes y servidores intermedios a través de los cuales se enruta una solicitud mediante el uso del encabezado Cache-Control. Cuando una aplicación cliente envía una solicitud HTTP GET a la API web, la respuesta puede incluir un encabezado Cache-Control que indique si los datos en el cuerpo de la respuesta pueden almacenarse en caché de forma segura por parte del cliente o un servidor intermedio a través del cual se ha enrutado la solicitud, y cuánto tiempo debe pasar antes de que expiren y se consideren caducados. En el ejemplo siguiente se muestra una solicitud HTTP GET y la respuesta correspondiente que incluye un encabezado Cache-Control:

	```HTTP
	GET http://adventure-works.com/orders/2 HTTP/1.1
	...
	```

	```HTTP
	HTTP/1.1 200 OK
	...
	Cache-Control: max-age=600, private
	Content-Type: text/json; charset=utf-8
	Content-Length: ...
	{"orderID":2,"productID":4,"quantity":2,"orderValue":10.00}
	```

	En este ejemplo, el encabezado Cache-Control especifica que los datos devueltos deben expirar a los 600 segundos, solo son adecuados para un solo cliente y no deben almacenarse en una memoria caché compartida que usen otros clientes (es _private_). El encabezado Cache-Control podría especificar _public_ en lugar de _private_, en cuyo caso los datos pueden almacenarse en una memoria caché compartida, o puede especificar _no-store_, en cuyo caso el cliente **no** deben almacenar los datos en memoria caché. En el ejemplo de código siguiente se muestra cómo construir un encabezado Cache-Control en un mensaje de respuesta:

	```C#
	public class OrdersController : ApiController
	{
    	...
    	[Route("api/orders/{id:int:min(0)}")]
    	[HttpGet]
    	public IHttpActionResult FindOrderByID(int id)
    	{
    		// Find the matching order
    		Order order = ...;
    		...
    		// Create a Cache-Control header for the response
    		var cacheControlHeader = new CacheControlHeaderValue();
    		cacheControlHeader.Private = true;
    		cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);
    		...

    		// Return a response message containing the order and the cache control header
    		OkResultWithCaching<Order> response = new OkResultWithCaching<Order>(order, this)
    		{
    			CacheControlHeader = cacheControlHeader
    		};
    		return response;
    	}
    	...
	}
	```

	Este código usa una clase `IHttpActionResult` personalizada denominada `OkResultWithCaching`. Esta clase permite que el controlador establezca el contenido de encabezado de la memoria caché:

	```C#
	public class OkResultWithCaching<T> : OkNegotiatedContentResult<T>
    {
        public OkResultWithCaching(T content, ApiController controller)
            : base(content, controller) { }

        public OkResultWithCaching(T content, IContentNegotiator contentNegotiator, HttpRequestMessage request, IEnumerable<MediaTypeFormatter> formatters)
            : base(content, contentNegotiator, request, formatters) { }

        public CacheControlHeaderValue CacheControlHeader { get; set; }
        public EntityTagHeaderValue ETag { get; set; }

        public override async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            HttpResponseMessage response = await base.ExecuteAsync(cancellationToken);

            response.Headers.CacheControl = this.CacheControlHeader;
            response.Headers.ETag = ETag;

            return response;
        }
    }
	```

	> [AZURE.NOTE] El protocolo HTTP también define la directiva _no-cache_ para el encabezado Cache-Control. De forma bastante confusa, esta directiva no significa "no almacenar en memoria caché", sino "volver a validar la información almacenada en caché con el servidor antes de devolverla"; los datos siguen pudiendo almacenarse en memoria caché, pero se comprueban cada vez que se usan para asegurar que sigan siendo actuales.

	La administración en memoria caché es responsabilidad de la aplicación cliente o el servidor intermedio, pero si se implementa correctamente, puede ahorrar ancho de banda y mejorar el rendimiento, eliminando la necesidad de capturar los datos que ya se ha recuperado recientemente.

	El valor de _max-age_ en el encabezado Cache-Control es solo una guía y no una garantiza de que los datos correspondientes no cambiarán durante el tiempo especificado. La API web debe establecer max-age en un valor adecuado según la volatilidad esperada de los datos. Cuando expire este período, el cliente debería descartar el objeto de la memoria caché.

	> [AZURE.NOTE] La mayoría de los exploradores web modernos admiten el almacenamiento en memoria caché del lado cliente, agregando los encabezados cache-control adecuados a las solicitudes y examinando los encabezados de los resultados, como se describe. Sin embargo, algunos exploradores más antiguos no almacenarán en memoria caché los valores devueltos desde una dirección URL que incluya una cadena de consulta. Normalmente, esto no es un problema para las aplicaciones de cliente personalizadas que implementan su propia estrategia de administración de la memoria caché basada en el protocolo que se trata aquí.
	>
	> Algunos servidores proxy antiguos tienen el mismo comportamiento y podrían no almacenar en memoria caché las solicitudes basadas en direcciones URL con cadenas de consulta. Esto podría ser un problema para las aplicaciones cliente personalizadas que se conectan a un servidor web a través de un proxy de este tipo.

- **Proporcione ETags para optimizar el procesamiento de las consultas**.

	Cuando una aplicación cliente recupera un objeto, el mensaje de respuesta también puede incluir una _ETag_ (etiqueta de entidad). Una ETag es una cadena opaca que indica la versión de un recurso; cada vez que un recurso cambia, también se modifica la Etag. Esta ETag debe almacenarse en memoria caché como parte de los datos de la aplicación cliente. En el ejemplo de código siguiente se muestra cómo agregar una ETag como parte de la respuesta a una solicitud HTTP GET. Este código usa el método `GetHashCode` de un objeto para generar un valor numérico que identifica el objeto (puede reemplazar este método si es necesario y generar su propio hash con un algoritmo como MD5):

	```C#
	public class OrdersController : ApiController
	{
    	...
    	public IHttpActionResult FindOrderByID(int id)
    	{
    		// Find the matching order
    		Order order = ...;
    		...

    		var hashedOrder = order.GetHashCode();
    		string hashedOrderEtag = String.Format(""{0}"", hashedOrder);
    		var eTag = new EntityTagHeaderValue(hashedOrderEtag);

    		// Return a response message containing the order and the cache control header
    		OkResultWithCaching<Order> response = new OkResultWithCaching<Order>(order, this)
    		{
    			...,
    			ETag = eTag
    		};
    		return response;
    	}
    	...
    }
	```

	El mensaje de respuesta que publica la API web tiene este aspecto:

	```HTTP
	HTTP/1.1 200 OK
	...
	Cache-Control: max-age=600, private
	Content-Type: text/json; charset=utf-8
	ETag: "2147483648"
	Content-Length: ...
	{"orderID":2,"productID":4,"quantity":2,"orderValue":10.00}
	```

	> [AZURE.TIP] Por motivos de seguridad, no permita que los datos confidenciales o los que se devuelvan a través de una conexión autenticada (HTTPS) se almacenen en memoria caché.

	Una aplicación cliente puede emitir una solicitud GET posterior para recuperar el mismo recurso en cualquier momento y, si ha cambiado el recurso (tiene una ETag diferente), debe descartarse la versión en caché y agregarse la nueva versión a la memoria caché. Si un recurso es grande y requiere una gran cantidad de ancho de banda para transmitirse de vuelta al cliente, las solicitudes repetidas para capturar los mismos datos pueden resultar ineficaces. Para combatir esta situación, el protocolo HTTP define el proceso siguiente para optimizar las solicitudes GET que se deben admitir en una API web:

	- El cliente crea una solicitud GET que contiene la ETag de la versión actualmente en caché del recurso al que hace referencia en un encabezado If-None-Match HTTP:

	```HTTP
	GET http://adventure-works.com/orders/2 HTTP/1.1
	If-None-Match: "2147483648"
	...
	```

	- La operación GET de la API web obtiene la ETag actual de los datos solicitados (el pedido 2 en el ejemplo anterior) y la compara con el valor del encabezado If-None-Match.

	- Si la ETag actual de los datos solicitados coincide con la que proporciona la solicitud, el recurso no ha cambiado y la API web debe devolver una respuesta HTTP con un cuerpo de mensaje vacío y un código de estado 304 (no modificado).

	- Si la ETag actual de los datos solicitados no coincide con la que proporciona la solicitud, los datos han cambiado y la API web debe devolver una respuesta HTTP con los datos nuevos en el cuerpo del mensaje y un código de estado 200 (correcto).

	- Si los datos solicitados ya no existen, la API web debe devolver una respuesta HTTP con el código de estado 404 (no encontrado).

	- El cliente usa el código de estado para mantener la memoria caché. Si los datos no ha cambiado (código de estado 304), el objeto puede permanecer en la memoria caché y la aplicación cliente debe seguir usando esta versión del objeto. Si los datos han cambiado (código de estado 200), debe descartarse el objeto almacenado en caché e insertarse uno nuevo. Si los datos ya no están disponibles (código de estado 404), el objeto debe quitarse de la memoria caché.

	> [AZURE.NOTE] Si el encabezado de la respuesta contiene el encabezado Cache-Control no-store, el objeto debe quitarse siempre de la memoria caché sin tener en cuenta el código de estado HTTP.

	El código siguiente muestra el método `FindOrderByID` extendido para admitir el encabezado If-None-Match. Tenga en cuenta que si se omite el encabezado If-None-Match, siempre se recupera el pedido especificado:

	```C#
	public class OrdersController : ApiController
	{
   		...
    	[Route("api/orders/{id:int:min(0)}")]
    	[HttpGet]
    	public IHttpActionResult FindOrderById(int id)
        {
            try
            {
                // Find the matching order
    		    Order order = ...;

                // If there is no such order then return NotFound
                if (order == null)
                {
                    return NotFound();
                }

                // Generate the ETag for the order
                var hashedOrder = order.GetHashCode();
                string hashedOrderEtag = String.Format(""{0}"", hashedOrder);

                // Create the Cache-Control and ETag headers for the response
                IHttpActionResult response = null;
                var cacheControlHeader = new CacheControlHeaderValue();
                cacheControlHeader.Public = true;
                cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);
                var eTag = new EntityTagHeaderValue(hashedOrderEtag);

                // Retrieve the If-None-Match header from the request (if it exists)
                var nonMatchEtags = Request.Headers.IfNoneMatch;

                // If there is an ETag in the If-None-Match header and
                // this ETag matches that of the order just retrieved,
                // then create a Not Modified response message
                if (nonMatchEtags.Count > 0 &&
                    String.Compare(nonMatchEtags.First().Tag, hashedOrderEtag) == 0)
                {
                    response = new EmptyResultWithCaching()
                    {
                        StatusCode = HttpStatusCode.NotModified,
                        CacheControlHeader = cacheControlHeader,
                        ETag = eTag
                    };
                }
                // Otherwise create a response message that contains the order details
                else
                {
                    response = new OkResultWithCaching<Order>(order, this)
                    {
                        CacheControlHeader = cacheControlHeader,
                        ETag = eTag
                    };
                }

                return response;
            }
            catch
            {
                return InternalServerError();
            }
        }
    ...
    }
	```

	Este ejemplo incorpora una clase `IHttpActionResult` personalizada adicional denominada `EmptyResultWithCaching`. Esta clase solo actúa como un contenedor alrededor de un objeto `HttpResponseMessage` que no contiene un cuerpo de respuesta:

	```C#
    public class EmptyResultWithCaching : IHttpActionResult
    {
        public CacheControlHeaderValue CacheControlHeader { get; set; }
        public EntityTagHeaderValue ETag { get; set; }
        public HttpStatusCode StatusCode { get; set; }
		public Uri Location { get; set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            HttpResponseMessage response = new HttpResponseMessage(StatusCode);
            response.Headers.CacheControl = this.CacheControlHeader;
            response.Headers.ETag = this.ETag;
            response.Headers.Location = this.Location;
            return response;
        }
    }
	```

	> [AZURE.TIP] En este ejemplo, la ETag para los datos se genera mediante la aplicación de un algoritmo hash a los datos recuperados del origen de datos subyacente. Si la ETag se puede calcular de alguna otra manera, es posible optimizar aún más el proceso y los datos solo deben capturarse desde el origen de datos si han cambiado. Este enfoque es especialmente útil si los datos son grandes o el acceso al origen de datos puede provocar una latencia significativa (por ejemplo, si el origen de datos es una base de datos remota).

- **Use ETags para admitir la simultaneidad optimista**.

	Para habilitar las actualizaciones en datos anteriormente almacenados en caché, el protocolo HTTP admite una estrategia de simultaneidad optimista. Si, después de capturar y almacenar en caché un recurso, la aplicación cliente envía posteriormente una solicitud PUT o DELETE para cambiar o quitar el recurso, debe incluir un encabezado If-Match que haga referencia a la ETag. La API web puede entonces usar esta información para determinar si otro usuario ha cambiado ya el recurso desde que se recuperó y enviar una respuesta adecuada de vuelta a la aplicación cliente, como se indica a continuación:

	- El cliente crea una solicitud PUT que contiene los nuevos detalles para el recurso y la ETag de la versión actualmente en caché del recurso al que hace referencia en un encabezado If-Match HTTP: En el ejemplo siguiente se muestra una solicitud PUT que actualiza un pedido:

	```HTTP
	PUT http://adventure-works.com/orders/1 HTTP/1.1
	If-None-Match: "2282343857"
	Content-Type: application/x-www-form-urlencoded
	...
	Date: Fri, 12 Sep 2014 09:18:37 GMT
	Content-Length: ...
	productID=3&quantity=5&orderValue=250
	```

	- La operación PUT de la API web obtiene la ETag actual de los datos solicitados (el pedido 1 en el ejemplo anterior) y la compara con el valor del encabezado If-Match.

	- Si la ETag actual de los datos solicitados coincide con la que proporciona la solicitud, el recurso no ha cambiado y la API web debe realizar la actualización y devolver un mensaje con el código de estado HTTP 204 (sin contenido). La respuesta puede incluir los encabezados Cache-Control y ETag de la versión actualizada del recurso. La respuesta siempre debe incluir el encabezado Location que hace referencia al URI del recurso recién actualizado.

	- Si la ETag actual de los datos solicitados no coincide con la que proporciona la solicitud, otro usuario ha cambiado los datos desde que se capturaron y la API web debe devolver una respuesta HTTP con un cuerpo de mensaje vacío y un código de estado 412 (error de condición previa).

	- Si el recurso que se va a actualizar ya no existe, la API web debe devolver una respuesta HTTP con el código de estado 404 (no encontrado).

	- El cliente usa el código de estado y los encabezados de las respuestas para mantener la memoria caché. Si los datos se han actualizado (código de estado 204), el objeto puede permanecer en la memoria caché (siempre y cuando el encabezado Cache-Control no especifique no-store), pero debe actualizarse la ETag. Si otro usuario ha cambiado los datos (código de estado 412) o no se encuentran (código de estado 404), el objeto en caché debe descartarse.

	En el ejemplo de código siguiente se muestra una implementación de la operación PUT para el controlador Orders:

	```C#
	public class OrdersController : ApiController
	{
   		...
    	[HttpPut]
    	[Route("api/orders/{id:int}")]
    	        public IHttpActionResult UpdateExistingOrder(int id, DTOOrder order)
        {
            try
            {
                var baseUri = Constants.GetUriFromConfig();
                var orderToUpdate = this.ordersRepository.GetOrder(id);
                if (orderToUpdate == null)
                {
                    return NotFound();
                }

                var hashedOrder = orderToUpdate.GetHashCode();
                string hashedOrderEtag = String.Format(""{0}"", hashedOrder);

                // Retrieve the If-Match header from the request (if it exists)
                var matchEtags = Request.Headers.IfMatch;

                // If there is an Etag in the If-Match header and
                // this etag matches that of the order just retrieved,
                // or if there is no etag, then update the Order
                if (((matchEtags.Count > 0 &&
                     String.Compare(matchEtags.First().Tag, hashedOrderEtag) == 0)) ||
                     matchEtags.Count == 0)
                {
                    // Modify the order
                    orderToUpdate.OrderValue = order.OrderValue;
                    orderToUpdate.ProductID = order.ProductID;
                    orderToUpdate.Quantity = order.Quantity;

                    // Save the order back to the data store
                    // ...

                    // Create the No Content response with Cache-Control, ETag, and Location headers
                    var cacheControlHeader = new CacheControlHeaderValue();
                    cacheControlHeader.Private = true;
                    cacheControlHeader.MaxAge = new TimeSpan(0, 10, 0);

                    hashedOrder = order.GetHashCode();
                    hashedOrderEtag = String.Format(""{0}"", hashedOrder);
                    var eTag = new EntityTagHeaderValue(hashedOrderEtag);

                    var location = new Uri(string.Format("{0}/{1}/{2}", baseUri, Constants.ORDERS, id));
                    var response = new EmptyResultWithCaching()
                    {
                        StatusCode = HttpStatusCode.NoContent,
                        CacheControlHeader = cacheControlHeader,
                        ETag = eTag,
                        Location = location
                    };

                    return response;
                }

                // Otherwise return a Precondition Failed response
                return StatusCode(HttpStatusCode.PreconditionFailed);
            }
            catch
            {
                return InternalServerError();
            }
        }
        ...
    }
	```

	> [AZURE.TIP] El uso del encabezado If-Match es totalmente opcional y, si se omite, la API web siempre intentará actualizar el pedido especificado, posiblemente sobrescribiendo ciegamente una actualización que otro usuario haya realizado. Para evitar problemas debidos a actualizaciones perdidas, facilite siempre un encabezado If-Match.

<a name="considerations-for-handling-large"></a>
## Consideraciones para controlar respuestas y solicitudes de gran tamaño

Puede haber ocasiones en que una aplicación cliente necesite emitir solicitudes que envíen o reciban datos que pueden tener un tamaño de varios megabytes (o mayor). Si se espera mientras se transmite esa cantidad de datos, podría provocar que la aplicación cliente deje de responder. Tenga en cuenta los siguientes puntos cuando necesite controlar las solicitudes que incluyen grandes cantidades de datos:

- **Optimice las solicitudes y respuestas que impliquen objetos grandes**.

	Algunos recursos pueden ser objetos grandes o incluir campos de gran tamaño, como imágenes gráficas u otros tipos de datos binarios. Una API web debe admitir streaming para habilitar la carga y descarga optimizadas de estos recursos.

	El protocolo HTTP ofrece el mecanismo de codificación de transferencia fragmentada para hacer streaming de objetos de datos de gran tamaño a un cliente. Cuando el cliente envía una solicitud HTTP GET para un objeto grande, la API web puede enviar de vuelta la respuesta por _fragmentos_ de forma gradual a través de una conexión HTTP. Es posible que la longitud de los datos de la respuesta no se conozca inicialmente (se puede generar), por lo que el servidor que hospeda la API web debe enviar un mensaje de respuesta con cada fragmento que especifique el encabezado Transfer-Encoding: Chunked en lugar de Content-Length. La aplicación cliente puede a su vez recibir cada fragmento para construir la respuesta completa. La transferencia de datos se completa cuando el servidor devuelve un fragmento final con tamaño cero. Puede implementar la fragmentación en la API Web de ASP.NET mediante el uso de la clase `PushStreamContent`.

	En el ejemplo siguiente se muestra una operación que responde a las solicitudes HTTP GET de las imágenes de producto:

	```C#
	public class ProductImagesController : ApiController
	{
    	...
    	[HttpGet]
        [Route("productimages/{id:int}")]
        public IHttpActionResult Get(int id)
        {
            try
            {
                var container = ConnectToBlobContainer(Constants.PRODUCTIMAGESCONTAINERNAME);

                if (!BlobExists(container, string.Format("image{0}.jpg", id)))
                {
                    return NotFound();
                }
                else
                {
                    return new FileDownloadResult()
                    {
                        Container = container,
                        ImageId = id
                    };
                }
            }
            catch
            {
                return InternalServerError();
            }
        }
    	...
	}
	```

	En este ejemplo, `ConnectBlobToContainer` es un método auxiliar que se conecta a un contenedor especificado (el nombre no se muestra) en almacenamiento de blobs de Azure. `BlobExists` es otro método auxiliar que devuelve un valor booleano que indica si existe un blob con el nombre especificado en el contenedor del almacenamiento de blobs.

	Cada producto tiene su propia imagen contenida en el almacenamiento de blobs. La clase `FileDownloadResult` es una clase `IHttpActionResult` personalizada que usa un objeto `PushStreamContent` para leer los datos de imagen del blob adecuado y transmitirlos de forma asincrónica como el contenido del mensaje de respuesta:

	```C#
	public class FileDownloadResult : IHttpActionResult
    {
        public CloudBlobContainer Container { get; set; }
        public int ImageId { get; set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            var response = new HttpResponseMessage();
            response.Content = new PushStreamContent(async (outputStream, _, __) =>
            {
                try
                {
                    CloudBlockBlob blockBlob = Container.GetBlockBlobReference(String.Format("image{0}.jpg", ImageId));
                    await blockBlob.DownloadToStreamAsync(outputStream);
                }
                finally
                {
                    outputStream.Close();
                }
            });

            response.StatusCode = HttpStatusCode.OK;
            response.Content.Headers.ContentType = new MediaTypeHeaderValue("image/jpeg");
            return response;
        }
    }
	```

	También puede aplicar streaming para cargar operaciones si un cliente debe realizar una operación POST en un recurso nuevo que incluye un objeto grande. En el ejemplo siguiente se muestra el método Post para el controlador `ProductImages`. Este método permite que el cliente cargue una nueva imagen de producto:

	```C#
	public class ProductImagesController : ApiController
	{
    	...
        [HttpPost]
        [Route("productimages")]
        public async Task<IHttpActionResult> Post()
        {
            try
            {
                if (!Request.Content.Headers.ContentType.MediaType.Equals("image/jpeg"))
                {
                    return StatusCode(HttpStatusCode.UnsupportedMediaType);
                }
                else
                {
                    var id = new Random().Next(); // Use a random int as the key for the new resource. Should probably check that this key has not already been used
                    var container = ConnectToBlobContainer(Constants.PRODUCTIMAGESCONTAINERNAME);
                    return new FileUploadResult()
                    {
                        Container = container,
                        ImageId = id,
                        Request = Request
                    };
                }
            }
            catch
            {
                return InternalServerError();
            }
        }
    	...
	}
	```

	Este código usa otra clase `IHttpActionResult` personalizada que se llama `FileUploadResult`. Esta clase contiene la lógica para cargar los datos de forma asincrónica:

	```C#
    public class FileUploadResult : IHttpActionResult
    {
        public CloudBlobContainer Container { get; set; }
        public int ImageId { get; set; }
        public HttpRequestMessage Request { get; set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            var response = new HttpResponseMessage();
            CloudBlockBlob blockBlob = Container.GetBlockBlobReference(String.Format("image{0}.jpg", ImageId));
            await blockBlob.UploadFromStreamAsync(await Request.Content.ReadAsStreamAsync());
            var baseUri = string.Format("{0}://{1}:{2}", Request.RequestUri.Scheme, Request.RequestUri.Host, Request.RequestUri.Port);
            response.Headers.Location = new Uri(string.Format("{0}/productimages/{1}", baseUri, ImageId));
            response.StatusCode = HttpStatusCode.OK;
            return response;
        }
    }
	```

	> [AZURE.TIP] El volumen de datos que se pueden cargar en un servicio web no está restringido por el streaming y una sola solicitud posiblemente podría producir un objeto masivo que consumiera recursos considerables. Si durante el proceso de streaming la API web determina que la cantidad de datos de una solicitud ha superado algunos límites aceptables, puede anular la operación y devolver un mensaje de respuesta con el código de estado 413 (entidad solicitada demasiado grande).

	Puede minimizar el tamaño de los objetos grandes transmitidos por la red con la compresión HTTP. Este enfoque ayuda a reducir la cantidad de tráfico de red y la latencia de red asociada, pero a costa de requerir un procesamiento adicional en el cliente y en el servidor que hospeda la API web. Por ejemplo, una aplicación cliente que espera recibir datos comprimidos puede incluir un encabezado de solicitud Accept-Encoding: gzip (también se pueden especificar otros algoritmos de compresión de datos). Si el servidor admite la compresión, debe responder con el contenido almacenado en formato gzip en el cuerpo del mensaje y el encabezado de respuesta Content-Encoding: gzip.

	> [AZURE.TIP] Puede combinar la compresión codificada con streaming; comprima los datos antes de hacer streaming y especifique la codificación de contenido gzip y la codificación de transferencia fragmentada en los encabezados de los mensajes. Tenga en cuenta también que algunos servidores web (por ejemplo, Internet Information Server) pueden configurarse para comprimir automáticamente las respuestas HTTP sin tener en cuenta si la API web comprime los datos o no.

- **Implemente respuestas parciales para los clientes que no admitan operaciones asincrónicas**.

	Como alternativa al streaming asincrónico, una aplicación cliente puede solicitar explícitamente los datos de objetos grandes en fragmentos, que se conocen como respuestas parciales. La aplicación cliente envía una solicitud HTTP HEAD para obtener información sobre el objeto. Si la API web admite respuestas parciales, debe responder a la solicitud HEAD con un mensaje de respuesta que contenga un encabezado Accept-Ranges y un encabezado Content-Length que indique el tamaño total del objeto, pero el cuerpo del mensaje debe estar vacío. La aplicación cliente puede usar esta información para construir una serie de solicitudes GET que especifiquen un intervalo de bytes que se recibirán. La API web debería devolver un mensaje de respuesta con el código de estado HTTP 206 (contenido parcial), un encabezado Content-Length que especifique la cantidad de datos incluidos en el cuerpo del mensaje de respuesta y un encabezado Content-Range que indique la parte (por ejemplo, los bytes del 4000 al 8000) del objeto que representan esos datos.

	Las respuestas parciales y solicitudes HTTP HEAD se describen con más detalle en el documento Guía de diseño de una API.

- **Evite enviar mensajes de estado Continuar en las aplicaciones cliente**.

	Una aplicación de cliente que vaya a enviar una gran cantidad de datos a un servidor puede determinar primero si el servidor está realmente dispuesto a aceptar la solicitud. Antes de enviar los datos, la aplicación cliente puede enviar una solicitud HTTP con un encabezado Expect: 100-Continue, un encabezado Content-Length que indique el tamaño de los datos y un cuerpo de mensaje vacío. Si el servidor está dispuesto a atender la solicitud, debe responder con un mensaje que especifique el código de estado HTTP 100 (continuar). La aplicación cliente puede entonces continuar y enviar la solicitud completa, incluidos los datos en el cuerpo del mensaje.

	Si hospeda un servicio mediante IIS, el controlador HTTP.sys detecta y controla automáticamente los encabezados Expect: 100-Continue antes de pasar las solicitudes a la aplicación web. Esto significa que no es probable que vea estos encabezados en el código de la aplicación y se puede suponer que IIS ya ha filtrado los mensajes que considera como no aptos o demasiado grandes.

	Si está creando aplicaciones cliente mediante .NET Framework, todos los mensajes POST y PUT enviarán primero mensajes con encabezados Expect: 100-Continue de forma predeterminada. Al igual que con el lado servidor, el proceso lo controla de forma transparente .NET Framework. Sin embargo, este proceso hace que cada solicitud POST y PUT necesite 2 envíos de ida y vuelta al servidor, incluso para las solicitudes pequeñas. Si la aplicación no envía solicitudes con grandes cantidades de datos, puede deshabilitar esta característica mediante la clase `ServicePointManager` para crear objetos `ServicePoint` en la aplicación cliente. Un objeto `ServicePoint` controla las conexiones que el cliente realiza en un servidor basado en el esquema y los fragmentos de host de los URI que identifican recursos en el servidor. Después, puede establecer la propiedad `Expect100Continue` del objeto `ServicePoint` en false. Todas las solicitudes POST y PUT posteriores que realice el cliente a través de un URI que coincida con el esquema y los fragmentos de host del objeto `ServicePoint` se enviarán sin encabezados Expect: 100-Continue. El código siguiente muestra cómo configurar un objeto `ServicePoint` que configura todas las solicitudes enviadas a los URI con un esquema de `http` y un host de `www.contoso.com`.

	```C#
	Uri uri = new Uri("http://www.contoso.com/");
	ServicePoint sp = ServicePointManager.FindServicePoint(uri);
	sp.Expect100Continue = false;
	```

	También puede establecer la propiedad estática `Expect100Continue` de la clase `ServicePointManager` para especificar el valor predeterminado de esta propiedad para todos los objetos `ServicePoint` creados posteriormente. Para obtener más información, consulte la página [ServicePoint (Clase)](https://msdn.microsoft.com/library/system.net.servicepoint.aspx) del sitio web de Microsoft.

- **Admita la paginación de las solicitudes que pueden devolver grandes cantidades de objetos**.

	Si una colección contiene un gran número de recursos, al emitir una solicitud GET en el URI correspondiente podría producirse un procesamiento importante en el servidor que hospeda la API web, que afectaría al rendimiento y generaría una cantidad significativa de tráfico de red, lo que provocaría una mayor latencia.

	Para controlar estos casos, la API web debe admitir las cadenas de consulta que permiten que la aplicación cliente refina las solicitudes o capture los datos en bloques (o páginas) discretos más fáciles de administrar. El marco API Web de ASP.NET analiza las cadenas de consulta y las divide en una serie de pares de valores y parámetros que se pasan al método apropiado, siguiendo las reglas de enrutamiento que describieron anteriormente. El método debe implementarse para aceptar estos parámetros con los mismos nombres que especificó en la cadena de consulta. Además, estos parámetros deben ser opcionales (en caso de que el cliente omita la cadena de consulta de una solicitud) y tener valores predeterminados significativos. El código siguiente muestra el método `GetAllOrders` en el controlador `Orders`. Este método recupera los detalles de los pedidos. Si este método no tenía restricciones, posiblemente podría devolver una gran cantidad de datos. Los parámetros `limit` y `offset` están diseñados para reducir el volumen de datos a un subconjunto más pequeño, en este caso solo los 10 primeros pedidos de forma predeterminada:

	```C#
	public class OrdersController : ApiController
	{
    	...
    	[Route("api/orders")]
    	[HttpGet]
    	public IEnumerable<Order> GetAllOrders(int limit=10, int offset=0)
    	{
    	    // Find the number of orders specified by the limit parameter
    	    // starting with the order specified by the offset parameter
    	    var orders = ...
    	    return orders;
    	}
    	...
	}
	```

	Una aplicación cliente puede emitir una solicitud para recuperar 30 pedidos empezando con un desplazamiento de 50 mediante el URI \__http://www.adventure-works.com/api/orders?limit=30&offset=50_.

	> [AZURE.TIP] Evite permitir que las aplicaciones de cliente especifiquen las cadenas de consulta que resultan en un URI que tenga más de 2000 caracteres. Muchos servidores y clientes web no pueden controlar los URI así de largos.

<a name="considerations-for-maintaining-responsiveness"></a>
## Consideraciones para mantener la capacidad de respuesta, la escalabilidad y la disponibilidad

La misma API web pueden usarla muchas aplicaciones cliente que se ejecuten en cualquier lugar del mundo. Es importante asegurarse de que la API web se implementa para mantener la capacidad de respuesta bajo cargas intensas, para que se pueda escalar a fin de admitir una carga de trabajo que varíe mucho y para garantizar la disponibilidad para los clientes que realizan operaciones esenciales para el negocio. Tenga en cuenta los puntos siguientes al determinar cómo cumplir estos requisitos:

- **Ofrezca compatibilidad asincrónica para las solicitudes de ejecución prolongada**.

	Una solicitud que podría tardar mucho tiempo en procesarse debe realizarse sin bloquear al cliente que la envió. La API web puede realizar ciertas comprobaciones iniciales para validar la solicitud, iniciar una tarea independiente para realizar el trabajo y, después, devolver un mensaje de respuesta con el código HTTP 202 (aceptado). La tarea podría ejecutarse de forma asincrónica como parte del procesamiento de la API web o podría descargarse en un trabajo web de Azure (si la API web está alojada en un sitio web de Azure) o en un rol de trabajo (si la API web se implementa como un servicio en la nube de Azure).

	> [AZURE.NOTE] Para obtener más información sobre el uso de trabajos web con el sitio web de Azure, visite la página [Uso de trabajos web para ejecutar tareas en segundo plano en Sitios web Microsoft Azure](web-sites-create-web-jobs.md) del sitio web de Microsoft.

	La API web también debe proporcionar un mecanismo para devolver los resultados del procesamiento a la aplicación cliente. Puede lograr esto si proporciona un mecanismo de sondeo de aplicaciones cliente para consultar periódicamente si ha finalizado el procesamiento y se ha obtenido el resultado o si habilita la API para que envíe una notificación cuando la operación se haya completado.

	Puede implementar un mecanismo de sondeo sencillo si ofrece una URI _polling_ que actúe como un recurso virtual con el siguiente enfoque:

	1. La aplicación cliente envía la solicitud inicial a la API web.

	2. La API web almacena información sobre la solicitud en una tabla que se conserva en almacenamiento de tablas o en Caché de Microsoft Azure y genera una clave única para esta entrada, posiblemente en forma de un GUID.

	3. La API web inicia el procesamiento como una tarea independiente. La API web registra el estado de la tarea en la tabla como _Running_.

	4. La API web devuelve un mensaje de respuesta con el código de estado HTTP 202 (aceptado) y el GUID de la entrada de la tabla en el cuerpo del mensaje.

	5. Cuando se completa la tarea, la API web almacena los resultados en la tabla y establece el estado de la tarea en _Complete_. Tenga en cuenta que si se produce un error en la tarea, la API web también podría almacenar información sobre el error y establecer el estado en _Failed_.

	6. Mientras se ejecuta la tarea, el cliente puede continuar realizando su propio procesamiento. Puede enviar periódicamente una solicitud al URI _/polling/{guid}_ donde _{guid}_ es el GUID devuelto en el mensaje de respuesta 202 de la API web.

	7. La API web en el URI _/polling{guid}_ consulta el estado de la tarea correspondiente en la tabla y devuelve un mensaje de respuesta con el código de estado HTTP 200 (correcto) que contiene este estado (_Running_, _Complete_ o _Failed_). Si la tarea se ha completado o ha producido un error, el mensaje de respuesta también puede incluir los resultados del procesamiento o la información disponible sobre el motivo del error.

	Si prefiere implementar notificaciones, entre las opciones disponibles figuran:

	- El uso de un Centro de notificaciones de Azure para insertar respuestas asíncronas en las aplicaciones cliente. La página [Notificación a los usuarios con los Centros de notificaciones de Azure](notification-hubs-aspnet-backend-windows-dotnet-notify-users.md) del sitio web de Microsoft ofrece más detalles.

	- El uso del modelo Comet para conservar una conexión de red persistente entre el cliente y el servidor que hospeda la API web y el uso de esta conexión para insertar mensajes desde el servidor de nuevo en el cliente. El artículo de MSDN magazine [Creación de una aplicación Comet sencilla en Microsoft .NET Framework](https://msdn.microsoft.com/magazine/jj891053.aspx) describe una solución de ejemplo.

	- El uso de SignalR para insertar datos en tiempo real desde el servidor web en el cliente a través de una conexión de red persistente. SignalR está disponible para las aplicaciones web de ASP.NET como un paquete de NuGet. Puede encontrar más información en el sitio web de [ASP.NET SignalR](http://signalr.net/).

	> [AZURE.NOTE] Comet y SignalR usan conexiones de red persistentes entre el servidor web y la aplicación cliente. Esto puede afectar a la escalabilidad, ya que un gran número de clientes puede requerir un número igual de grande de conexiones simultáneas.

- **Asegúrese de que cada solicitud no tiene estado**.

	Cada solicitud debe considerarse atómica. No debería haber dependencias entre una solicitud que realiza una aplicación de cliente y las siguientes solicitudes que envía el mismo cliente. Este enfoque ayuda a la escalabilidad; las instancias del servicio web se pueden implementar en varios servidores. Las solicitudes de cliente se pueden dirigir a cualquiera de estas instancias y los resultados siempre deben ser los mismos. También mejora la disponibilidad por un motivo similar; si se produce un error en un servidor web, las solicitudes se pueden enrutar a otra instancia (mediante el Administrador de tráfico de Azure) mientras se reinicia el servidor, sin que se produzcan efectos negativos en las aplicaciones cliente.

- **Realice un seguimiento de los clientes e implemente la limitación para reducir las posibilidades de ataques de denegación de servicio**.

	Si un cliente específico realiza un gran número de solicitudes en un período determinado de tiempo, podría monopolizar el servicio y afectar al rendimiento de otros clientes. Para mitigar este problema, una API web puede supervisar las llamadas de las aplicaciones de cliente mediante el seguimiento de la dirección IP de todas las solicitudes entrantes o mediante el registro de cada acceso autenticado. Puede usar esta información para limitar el acceso a los recursos. Si un cliente supera un límite definido, la API web puede devolver un mensaje de respuesta con el estado 503 (servicio no disponible) e incluir un encabezado Retry-After que especifique cuándo puede enviar el cliente la siguiente solicitud sin que se rechace. Esta estrategia puede ayudarle a reducir las posibilidades de un ataque de denegación de servicio (DOS) de un conjunto de clientes que quieran detener el sistema.

- **Administre con cuidado las conexiones HTTP persistentes**.

	El protocolo HTTP admite conexiones HTTP persistentes si están disponibles. La especificación HTTP 1.0 agrega el encabezado Connection:Keep-Alive, que permite que una aplicación cliente indique al servidor que puede usar la misma conexión para enviar solicitudes posteriores, en lugar de abrir nuevas. La conexión se cierra automáticamente si el cliente no vuelve a utilizarla en un período definido por el host. Este comportamiento es el predeterminado en HTTP 1.1 como lo usan los servicios de Azure, por lo que no es necesario incluir encabezados Keep-Alive en los mensajes.

	Si se mantiene abierta una conexión, puede ayudar a mejorar la capacidad de respuesta al reducir la latencia y la congestión de la red, pero puede ser perjudicial para la escalabilidad porque se mantienen conexiones innecesarias abiertas durante más tiempo del necesario, lo que limita la capacidad de conexión de otros clientes simultáneos. También puede afectar a la duración de la batería si la aplicación cliente se ejecuta en un dispositivo móvil; si la aplicación solo realiza solicitudes ocasionales al servidor, mantener abierta una conexión puede hacer que la batería se gaste con mayor rapidez. Para asegurarse de que una conexión no se hace persistente con HTTP 1.1, el cliente puede incluir un encabezado Connection:Close con los mensajes para reemplazar el comportamiento predeterminado. De forma similar, si un servidor está controlando un gran número de clientes, puede incluir un encabezado Connection:Close en los mensajes de respuesta, lo que debería cerrar la conexión y ahorrar recursos del servidor.

	> [AZURE.NOTE] Las conexiones HTTP persistentes son una característica puramente opcional para reducir la sobrecarga de la red asociada al restablecimiento repetido de un canal de comunicaciones. Ni la API web ni la aplicación cliente deben depender de que haya una conexión HTTP persistente disponible. No use conexiones HTTP persistentes para implementar sistemas de notificación de estilo Comet; en su lugar, debería usar sockets (o websockets si están disponible) en la capa TCP. Por último, tenga en cuenta que los encabezados Keep-Alive son de uso limitado si una aplicación cliente se comunica con un servidor mediante un proxy; solo la conexión con el cliente y el proxy será persistente.

## Consideraciones para la publicación y administración de una API web

Para que una API web esté disponible para las aplicaciones cliente, la API web debe implementarse en un entorno de host. Normalmente este entorno es un servidor web, aunque puede ser otro tipo de proceso de host. Debe considerar los siguientes puntos cuando publique una API web:

- Todas las solicitudes deben autenticarse y autorizarse, y debe aplicarse el nivel de control de acceso adecuado.
- Una API web comercial puede estar sujeta a diversas garantías de calidad relativas a los tiempos de respuesta. Es importante asegurarse de que ese entorno de host es escalable si la carga puede variar considerablemente con el tiempo.
- Puede ser necesario realizar mediciones de las solicitudes para fines de monetización.
- Es posible que sea necesario regular el flujo de tráfico a la API web e implementar la limitación para clientes concretos que hayan agotado sus cuotas.
- Los requisitos normativos podrían requerir un registro y una auditoría de todas las solicitudes y respuestas.
- Para garantizar la disponibilidad, puede ser necesario supervisar el estado del servidor que hospeda la API web y reiniciarlo si hiciera falta.

Es útil poder separar estos problemas de los problemas técnicos relativos a la implementación de la API web. Por este motivo, considere la creación de una [fachada](http://en.wikipedia.org/wiki/Facade_pattern),que se ejecute como un proceso independiente y que enrute las solicitudes a la API web. La fachada puede proporcionar las operaciones de administración y redirigir las solicitudes validadas a la API web. El uso de una fachada también puede aportar muchas ventajas funcionales, entre las que se incluyen:

- Actuar como un punto de integración para varias API web.
- Transformar los mensajes y traducir protocolos de comunicaciones de los clientes creados mediante tecnologías distintas.
- Almacenar en caché solicitudes y respuestas para reducir la carga en el servidor que hospeda la API web.

## Consideraciones para probar una API web
Una API web debe probarse tan exhaustivamente como cualquier otro producto de software. Considere la posibilidad de crear pruebas unitarias para validar la funcionalidad de cada operación, como lo haría con cualquier otro tipo de aplicación. Para obtener más información, vea la página [Comprobar código utilizando pruebas unitarias](https://msdn.microsoft.com/library/dd264975.aspx) del sitio web de Microsoft.

> [AZURE.NOTE] La API web de ejemplo disponible con esta guía incluye un proyecto de prueba que muestra cómo realizar pruebas unitarias sobre operaciones seleccionadas.

La naturaleza de una API web incorpora sus propios requisitos adicionales para comprobar que funciona correctamente. Debe prestar especial atención a los siguientes aspectos:

- Pruebe todas las rutas para comprobar que invocan las operaciones correctas. Preste especial atención al código de estado HTTP 405 (método no permitido) que se devuelven de forma inesperada, ya que esto puede indicar una desigualdad entre una ruta y los métodos HTTP (GET, POST, PUT, DELETE) que se pueden enviar a esa ruta.

	Envíe solicitudes HTTP a las rutas que no las admitan, como el envío de una solicitud POST a un recurso concreto (las solicitudes POST solo se pueden enviar a las colecciones de recursos). En estos casos, la única respuesta válida _debería_ ser el código de estado 405 (no permitido).

- Compruebe que todas las rutas estén protegidas correctamente y que estén sujetas a las comprobaciones de autenticación y autorización apropiadas.

	> [AZURE.NOTE] Algunos aspectos de seguridad, como la autenticación de los usuarios, tienen más probabilidades de ser responsabilidad del entorno de host y no de la API web, pero sigue siendo necesario incluir las pruebas de seguridad como parte del proceso de implementación.

- Pruebe el control de excepciones que realiza cada operación y compruebe que se pasa una respuesta HTTP adecuada y significativa de vuelta a la aplicación cliente.
- Compruebe que los mensajes de solicitud y respuesta están formados correctamente. Por ejemplo, si una solicitud HTTP POST contiene los datos de un recurso nuevo en formato x-www-form-urlencoded, confirme que la operación correspondiente analiza correctamente los datos, crea los recursos y devuelve una respuesta que contiene los detalles del nuevo recurso, incluido el encabezado Location correcto.
- Compruebe todos los vínculos y los URI de los mensajes de respuesta. Por ejemplo, un mensaje HTTP POST debe devolver el URI del recurso recién creado. Todos los vínculos HATEOAS deben ser válidos.

	> [AZURE.IMPORTANT] Si publica la API web a través de un servicio de administración de API, estos URI deben reflejar la dirección URL del servicio de administración y no la del servidor web que hospeda la API web.

- Asegúrese de que cada operación devuelve los códigos de estado correctos para diferentes combinaciones de entradas. Por ejemplo:
	- Si una consulta es correcta, debe devolver el código de estado 200 (correcto).
	- Si no se encuentra un recurso, la operación debe devolver el código de estado HTTP 404 (no encontrado).
	- Si el cliente envía una solicitud que elimina correctamente un recurso, el código de estado debe ser 204 (sin contenido).
	- Si el cliente envía una solicitud que crea un nuevo recurso, el código de estado debe ser 201 (creado).

Tenga cuidado con los códigos de estado de respuestas inesperados en el intervalo 5xx. Normalmente, el servidor host informa de estos mensajes para indicar que no pudo completar una solicitud válida.

- Pruebe las distintas combinaciones de encabezados de solicitud que una aplicación cliente puede especificar y asegúrese de que la API web devuelve la información esperada en los mensajes de respuesta.

- Pruebe las cadenas de consultas. Si una operación puede tomar parámetros opcionales (por ejemplo, las solicitudes de paginación), pruebe las diferentes combinaciones y ordenaciones de los parámetros.

- Compruebe que las operaciones asincrónicas se completan correctamente. Si la API web admite streaming para las solicitudes que devuelven objetos binarios de gran tamaño (como vídeo o audio), asegúrese de que las solicitudes de cliente no se bloquean mientras se transmiten los datos. Si la API web implementa sondeo para las operaciones de modificación de datos de ejecución prolongada, compruebe que las operaciones informan de su estado a medida que se procesan.

También debería crear y ejecutar pruebas de rendimiento para comprobar que la API web funciona satisfactoriamente bajo presión. Puede crear un proyecto de pruebas de carga y rendimiento web con Visual Studio Ultimate. Para obtener más información, consulte la página [Ejecutar pruebas de rendimiento en la aplicación](https://msdn.microsoft.com/library/dn250793.aspx) del sitio web de Microsoft.

## Publicación y administración de una API web mediante el servicio de administración de API de Azure

Azure ofrece el [servicio de administración de API](https://azure.microsoft.com/documentation/services/api-management/) que puede usar para publicar y administrar una API web. Con esta utilidad, puede generar un servicio que actúe como una fachada para una o varias API web. El servicio es en sí mismo un servicio web escalable que puede crear y configurar mediante el Portal de administración de Azure. Puede utilizar este servicio para publicar y administrar una API web como se indica a continuación:

1. Implemente la API web en un sitio web, un servicio en la nube de Azure o una máquina virtual de Azure.

2. Conecte el servicio de administración de API a la API web. Las solicitudes enviadas a la dirección URL de la API de administración se asignan a URI de la API web. El mismo servicio de administración de API puede enrutar las solicitudes a más de una API web. Esto le permite agregar varias API web en un servicio de administración único. De forma similar, es posible hacer referencia a la misma API web desde más de un servicio de administración de API si necesita restringir o hacer particiones de la funcionalidad disponible para distintas aplicaciones.

	> [AZURE.NOTE] Los URI de vínculos HATEOAS generados como parte de la respuesta de solicitudes HTTP GET deben hacer referencia a la dirección URL del servicio de administración de API y no al servidor web que hospeda la API web.

3. Para cada API web, especifique las operaciones HTTP que expone la API web junto con los parámetros opcionales que una operación puede tomar como entrada. También puede configurar si el servicio de administración de API debe almacenar en caché la respuesta recibida de la API web para optimizar las solicitudes repetidas para los mismos datos. Registre los detalles de las respuestas HTTP que puede generar cada operación. Esta información se usa para generar la documentación para desarrolladores, por lo que es importante que sea precisa y completa.

	Puede definir operaciones manualmente mediante los asistentes que se ofrecen en el Portal de administración de Azure o puede importarlas desde un archivo que contenga las definiciones en formato WADL o Swagger.

4. Establezca la configuración de seguridad para las comunicaciones entre el servicio de administración de API y el servidor web que hospeda la API web. El servicio de administración de API actualmente admite la autenticación básica y mutua mediante certificados y la autorización de usuario de OAuth 2.0.

5. Cree un producto. Un producto es la unidad de publicación; agregue las API web que había conectado previamente al servicio de administración al producto. Cuando se publica el producto, las API web están disponibles para los desarrolladores.

	> [AZURE.NOTE] Antes de publicar un producto, también puede definir grupos de usuarios que pueden tener acceso al producto y agregar usuarios a esos grupos. Esto le da control sobre los desarrolladores y las aplicaciones que puede usar la API web. Si una API web está sujeta a aprobación, antes de poder tener acceso a ella, un desarrollador debe enviar una solicitud al administrador del producto. El administrador puede conceder o denegar el acceso al desarrollador. Los desarrolladores existentes también se pueden bloquear si cambian las circunstancias.

6.	Configure directivas para cada API web. Las directivas rigen aspectos como si se deben permitir las llamadas entre dominios, cómo autenticar los clientes, si se debe hacer una conversión entre los formatos de datos XML y JSON de forma transparente, si se quieren restringir las llamadas de un determinado intervalo de IP, las cuotas de uso y si se quiere limitar la tasa de llamadas. Las directivas pueden aplicarse globalmente a todo el producto, a una API web única de un producto o a operaciones individuales de una API web.

Puede encontrar información detallada sobre cómo realizar estas tareas en la página [Administración de API](https://azure.microsoft.com/services/api-management/) del sitio web de Microsoft. El servicio de administración de API de Azure también ofrece su propia interfaz REST, que permite crear una interfaz personalizada para simplificar el proceso de configuración de una API web. Para obtener más información, visite la página [Referencia de la API REST de administración de API de Azure](https://msdn.microsoft.com/library/azure/dn776326.aspx) del sitio web de Microsoft.

> [AZURE.TIP] Azure ofrece el Administrador de tráfico de Azure que le permite implementar la conmutación por error y equilibrio de carga y reducir la latencia en varias instancias de un sitio web hospedado en distintas ubicaciones geográficas. Puede usar el Administrador de tráfico de Azure junto con el servicio de administración de API, que puede enrutar solicitudes a instancias de un sitio web a través del Administrador de tráfico de Azure. Para obtener más información, visite la página [Acerca de los métodos de equilibrio de carga de Traffic Manager](../traffic-manager/traffic-manager-load-balancing-methods.md) del sitio web de Microsoft.

> En esta estructura, si está utilizando nombres DNS personalizados para sus sitios web, debe configurar el registro CNAME adecuado para que cada sitio web apunte al nombre DNS del sitio web del Administrador de tráfico de Azure.

## Compatibilidad con desarrolladores que crean aplicaciones cliente
Los desarrolladores que crean aplicaciones cliente normalmente necesitan información sobre cómo obtener acceso a la API web y a la documentación relativa a los parámetros, los tipos de datos, los tipos de valores devueltos y los códigos de retorno que describen las distintas solicitudes y respuestas entre el servicio web y la aplicación cliente.

### Documentación de las operaciones REST para una API web
El servicio de administración de API de Azure incluye un portal para desarrolladores que describe las operaciones REST que expone una API web. Cuando un producto se ha publicado, aparece en este portal. Los desarrolladores pueden usar este portal para registrarse y obtener acceso; el administrador puede después aprobar o denegar la solicitud. Si se aprueba el desarrollador, se le asigna una clave de suscripción que se usa para autenticar las llamadas de las aplicaciones cliente que desarrolla. Esta clave debe facilitarse con cada llamada de API web; en caso contrario, se rechazará.

Este portal también ofrece:

- Documentación del producto, donde se enumeran las operaciones que expone, los parámetros necesarios y las distintas respuestas que se pueden devolver. Tenga en cuenta que esta información se genera a partir de los detalles proporcionados en el paso 3 de la lista en la sección [Publicación de una API web mediante el servicio de administración de API de Microsoft Azure](#publishing-a-web-API).

- Fragmentos de código que muestran cómo invocar operaciones de varios lenguajes, incluidos JavaScript, C#, Java, Ruby, Python y PHP.

- Una consola para desarrolladores que permite que un desarrollador envíe una solicitud HTTP para probar cada una de las operaciones del producto y ver los resultados.

- Una página donde el desarrollador puede informar de los problemas encontrados.

El Portal de administración de Azure le permite personalizar el portal para desarrolladores para cambiar el estilo y el diseño, de forma que coincida con la marca de su organización.

### Implementación de un SDK de cliente
La creación de una aplicación cliente que invoque solicitudes REST para tener acceso a una API web requiere que se escriba una cantidad significativa de código para construir cada solicitud y se le dé el formato adecuado, que se envié la solicitud al servidor que hospeda el servicio web y se analice la respuesta para determinar si la solicitud se realizó correctamente o no y que se extraigan los datos devueltos. Para aislar la aplicación cliente de estas cuestiones, puede proporcionar un SDK que encapsule la interfaz REST y abstraiga estos detalles de bajo nivel dentro de un conjunto de métodos más funcional. Una aplicación cliente usa estos métodos, que convierten las llamadas en solicitudes REST de forma transparente y, luego, vuelve a convertir las respuestas a los valores que el método ha devuelto. Se trata de una técnica común que se implementa en muchos servicios, incluidos el SDK de Azure.

La creación de un SDK del lado cliente es una tarea de una envergadura considerable, ya que debe implementarse de forma coherente y probarse cuidadosamente. Sin embargo, gran parte de este proceso se puede realizar de forma mecánica y muchos proveedores ofrecen herramientas que pueden automatizar muchas de estas tareas.

## Supervisión de una API web

Según cómo haya publicado e implementado la API web, podrá supervisar la API web directamente o podrá recopilar información del uso y del estado, mediante un análisis del tráfico que pasa a través del servicio de administración de API.

### Supervisión de una API web directamente
Si ha implementado la API web mediante la plantilla de API Web de ASP.NET (ya sea como un proyecto de API Web o como un rol web en un servicio en la nube de Azure) y Visual Studio 2013, puede recopilar datos de uso, rendimiento y disponibilidad con Application Insights de ASP.NET. Application Insights es un paquete que, de forma transparente, realiza un seguimiento y registra información sobre las solicitudes y respuestas cuando se implementa la API web en la nube; cuando el paquete está instalado y configurado, no es necesario modificar nada del código en la API web para usarlo. Cuando implementa la API web en un sitio web de Azure, se examina todo el tráfico y se recopilan las estadísticas siguientes:

- El tiempo de respuesta del servidor.

- El número de solicitudes del servidor y los detalles de cada una.

- Las solicitudes más lentas en cuanto al tiempo de respuesta medio.

- Los detalles de las solicitudes con errores.

- El número de sesiones que han iniciado otros exploradores y agentes de usuario.

- Las páginas visitadas con más frecuencia (lo que es útil principalmente para las aplicaciones web en lugar de las API web).

- Los distintos roles de usuario que obtienen acceso a la API web.

Puede ver estos datos en tiempo real desde el Portal de administración de Azure. También puede crear pruebas web que supervisen el estado de la API web. Una prueba web envía una solicitud periódica a un URI especificado en la API web y captura la respuesta. Puede especificar la definición de una respuesta correcta (por ejemplo, el código de estado HTTP 200) y, si la solicitud no devuelve esta respuesta, puede establecer que se envíe una alerta a un administrador. Si es necesario, el administrador puede reiniciar el servidor que hospeda la API web en caso de que se haya producido un error.

En la página [Application Insights: comience a supervisar el estado y el uso de la aplicación](app-insights-start-monitoring-app-health-usage/) del sitio web de Microsoft puede encontrar más información.

### Supervisión de una API web a través del servicio de administración de API

Si ha publicado la API web mediante el servicio de administración de API, la página de administración de API del Portal de administración de Azure contiene un panel que le permite ver el rendimiento general del servicio. La página de análisis le permite profundizar en los detalles de cómo se usa el producto. Esta página contiene los siguientes campos:

- **Uso**. Esta pestaña proporciona información sobre el número de llamadas API realizadas y el ancho de banda que se ha usado para controlar esas llamadas en el tiempo. Puede filtrar los detalles de uso por producto, API y operación.

- **Estado**. Esta pestaña permite ver el resultado de las solicitudes de API (los códigos de estado HTTP devueltos), la eficacia de la directiva de almacenamiento en caché, el tiempo de respuesta de las API y el tiempo de respuesta del servicio. De nuevo, puede filtrar los datos del estado por producto, API y operación.

- **Actividad**. Esta pestaña ofrece un resumen de texto de los números de llamadas correctas, erróneas y bloqueadas, el tiempo medio de respuesta y los tiempos de respuesta de cada producto, API web y operación. Esta página también muestra el número de llamadas que ha realizado cada desarrollador.

- **En un vistazo**. Esta pestaña muestra un resumen de los datos de rendimiento, incluidos los desarrolladores responsables de realizar la mayoría de las llamadas a API y los productos, API web y operaciones que recibieron estas llamadas.

Puede usar esta información para determinar si una operación o API web determinadas están causando un cuello de botella y si es necesario escalar el entorno de host y agregar más servidores. También puede determinar si una o varias aplicaciones usan un volumen de recursos desproporcionado y aplican las directivas adecuadas para establecer las cuotas y limitar las tasas de llamadas.

> [AZURE.NOTE] Puede cambiar los detalles de un producto publicado y los cambios se aplicarán inmediatamente. Por ejemplo, puede agregar o quitar una operación de una API web sin necesidad de volver a publicar el producto que contenga la API web.

## Patrones relacionados
- El patrón de [fachada](http://en.wikipedia.org/wiki/Facade_pattern) describe cómo proporcionar una interfaz a una API web.

## Más información
- En la página [Learn About ASP.NET Web API](http://www.asp.net/web-api) del sitio web de Microsoft se ofrece una introducción detallada a la creación de servicios web RESTful mediante la API Web.
- En la página [Routing in ASP.NET Web API](http://www.asp.net/web-api/overview/web-api-routing-and-actions/routing-in-aspnet-web-api) del sitio web de Microsoft se describe cómo funciona el enrutamiento basado en convenciones en el marco de API Web de ASP.NET.
- Para obtener más información sobre el enrutamiento basado en atributos, consulte la página [Attribute Routing in Web API 2](http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2) del sitio web de Microsoft.
- En la página [Basic Tutorial](http://www.odata.org/getting-started/basic-tutorial/) del sitio web de OData se ofrece una introducción a las características del protocolo OData.
- La página [ASP.NET Web API OData](http://www.asp.net/web-api/overview/odata-support-in-aspnet-web-api) del sitio web de Microsoft contiene ejemplos e información adicional sobre la implementación de una API web de OData mediante ASP.NET.
- En la página [Introducing batch support in Web API and Web API OData](http://blogs.msdn.com/b/webdev/archive/2013/11/01/introducing-batch-support-in-web-api-and-web-api-odata.aspx) del sitio web de Microsoft se describe cómo se implementan las operaciones por lotes en una API web mediante OData.
- En el artículo [Idempotency Patterns](http://blog.jonathanoliver.com/idempotency-patterns/) del blog de Jonathan Oliver se ofrece una visión general de la idempotencia y cómo se relaciona con las operaciones de administración de datos.
- La página [Status Code Definitions](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) del sitio web de W3C contiene una lista completa de códigos de estado HTTP y sus descripciones.
- Para obtener información detallada sobre el control de excepciones de HTTP con la API Web de ASP.NET, visite la página [Exception Handling in ASP.NET Web API](http://www.asp.net/web-api/overview/error-handling/exception-handling) del sitio web de Microsoft.
- En el artículo [Web API Global Error Handling](http://www.asp.net/web-api/overview/error-handling/web-api-global-error-handling) del sitio web de Microsoft se describe cómo implementar una estrategia global de registro y control de errores para una API web.
- En la página [Uso de trabajos web para ejecutar tareas en segundo plano en Sitios web Microsoft Azure](web-sites-create-web-jobs.md) del sitio web de Microsoft se ofrece información y ejemplos sobre el uso de trabajos web para realizar operaciones en segundo plano en un sitio web de Azure.
- En la página [Notificación a los usuarios con los Centros de notificaciones de Azure](notification-hubs-aspnet-backend-windows-dotnet-notify-users/) del sitio web de Microsoft se muestra cómo se puede usar un Centro de notificaciones de Azure para insertar respuestas asíncronas en las aplicaciones cliente.
- En la página [Administración de API](https://azure.microsoft.com/services/api-management/) del sitio web de Microsoft se describe cómo publicar un producto que ofrezca un acceso seguro y controlado a una API web.
- En la página [Referencia de la API REST de administración de API de Azure](https://msdn.microsoft.com/library/azure/dn776326.aspx) del sitio web de Microsoft se describe cómo usar la API de REST de administración de API para crear aplicaciones de administración personalizadas.
- En la página [Acerca de los métodos de equilibrio de carga de Traffic Manager](../traffic-manager/traffic-manager-load-balancing-methods.md) del sitio web de Microsoft se resume cómo se puede usar el Administrador de tráfico de Azure para realizar un equilibrio de carga de las solicitudes entre varias instancias de un sitio web que hospeda una API web.
- En la página [Application Insights: comience a supervisar el estado y el uso de la aplicación](app-insights-start-monitoring-app-health-usage.md) del sitio web de Microsoft se ofrece información detallada sobre la instalación y configuración de Application Insights en un proyecto API Web de ASP.NET.
- En la página [Comprobar código utilizando pruebas unitarias](https://msdn.microsoft.com/library/dd264975.aspx) del sitio web de Microsoft se ofrece información detallada sobre la creación y administración de las pruebas unitarias con Visual Studio.
- En la página [Ejecutar pruebas de rendimiento en la aplicación](https://msdn.microsoft.com/library/dn250793.aspx) del sitio web de Microsoft se describe cómo usar Visual Studio Ultimate para crear un proyecto de pruebas de carga y rendimiento web.

<!---HONumber=AcomDC_0128_2016-->