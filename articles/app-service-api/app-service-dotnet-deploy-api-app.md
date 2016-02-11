<properties 
	pageTitle="Implementación de una aplicación de API en el Servicio de aplicaciones de Azure" 
	description="Aprenda a implementar un proyecto de aplicación de API en su suscripción de Azure." 
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
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Implementación de una aplicación de API en el Servicio de aplicaciones de Azure 

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Información general

En este tutorial, implementará el proyecto Web API que creó en el [tutorial anterior](app-service-dotnet-create-api-app.md) en una nueva [aplicación de API](app-service-api-apps-why-best-platform.md). Usará Visual Studio para crear el recurso de aplicación de API en el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md) e implementará su código Web API en la aplicación de API de Azure.

### Otras opciones de implementación

Existen muchas otras formas de implementar aplicaciones de API. Una aplicación de API es una [aplicación web](../app-service-web/app-service-web-overview.md) con características adicionales para hospedar servicios web, y todos los [métodos de implementación que están disponibles para aplicaciones web](../app-service-web/web-sites-deploy.md) también pueden usarse con aplicaciones de API. La aplicación web que hospeda una aplicación de API se denomina host de aplicación de API en el Portal de vista previa de Azure y puede configurar la implementación mediante la hoja del portal de host de aplicación de API. Para obtener información sobre la hoja del host de aplicación de API, consulte [Administración de una aplicación de API](app-service-api-manage-in-portal.md).

El hecho de que las aplicaciones de API se basen en aplicaciones web también significa que puede implementar el código escrito para plataformas que no sean ASP.NET en las aplicaciones de API. Para obtener un ejemplo que usa Git para implementar código de Node.js en una aplicación de API, consulte [Creación de una aplicación de API Node.js en el Servicio de aplicaciones de Azure](app-service-api-nodejs-api-app.md).
 
## <a id="provision"></a>Creación de la aplicación de API en Azure 

En esta sección, use el asistente **Publicación web** de Visual Studio para crear una nueva aplicación de API en Azure. Cuando las instrucciones indican que escriba un nombre para la aplicación de API, escriba *ContactsList*.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## <a id="deploy"></a>Implemente el código en la nueva aplicación de API de Azure

Usará el mismo asistente **Publicar web** para implementar el código en la nueva aplicación de API.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Llamada a la aplicación de API de Azure 

Como habilitó la interfaz de usuario de Swagger en el tutorial anterior, puede usarlo para comprobar que la aplicación de API se ejecuta en Azure.

1. En el [Portal de vista previa de Azure](https://portal.azure.com), vaya a la hoja **Aplicación de API** de la aplicación de API que ha implementado.

2. Haga clic en la dirección URL de la aplicación de API.

	![Haga clic en la dirección URL](./media/app-service-dotnet-deploy-api-app/clickurl.png)

	Aparece una página que indica "La aplicación de API se creó correctamente".

3. En la barra de direcciones del explorador, agregue "/swagger" al final de la dirección URL.

4. En la página de Swagger que aparece, haga clic en **Contacts > Get > Try it Out** (Contactos > Obtener > Probarlo).

	![Prueba](./media/app-service-dotnet-deploy-api-app/swaggerui.png)

## Visualización de la definición de API en el portal

1. En el [Portal de vista previa de Azure](https://portal.azure.com), vaya a la hoja **Aplicación de API** de la aplicación de API que implementó.

4. Haga clic en **Definición de API**.
 
	La hoja **Definición de API** de la aplicación muestra la lista de operaciones de API que definió al crear la aplicación.

	![Definición de la API](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

A continuación, realizará un cambio en la definición de API y verá el cambio reflejado en el portal.

5. Vuelva al proyecto de Visual Studio y agregue el código siguiente al archivo **ContactsController.cs**.   

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	Este código agrega un método **Post** que se puede usar para registrar nuevas instancias de `Contact` en la API.

	El código de la clase Contacts ahora es similar al ejemplo siguiente.

		public class ContactsController : ApiController
		{
		    [HttpGet]
		    public IEnumerable<Contact> Get()
		    {
		        return new Contact[]{
		                    new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                    new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                    new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
		                };
		    }
		
		    [HttpPost]
		    public HttpResponseMessage Post([FromBody] Contact contact)
		    {
		        // todo: save the contact somewhere
		        return Request.CreateResponse(HttpStatusCode.Created);
		    }
		}

7. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y, a continuación, seleccione **Publicar**.

9. Haga clic en la pestaña **Vista previa**.

10. Haga clic en **Iniciar vista previa** para ver cuáles son los archivos que se copiarán en Azure.

	![Cuadro de diálogo Publicación web](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

11. Haga clic en **Publicar**.

6. Reinicie la puerta de enlace tal como lo hizo la primera vez que publicó.

12. Cuando haya completado el proceso de publicación, vuelva al portal y cierre y vuelva a abrir la hoja **Definición de API**. Verá el nuevo extremo de API que acaba de crear e implementar directamente en su suscripción de Azure.

	![Definición de la API](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## Pasos siguientes

Ha visto cómo las capacidades de implementación directa en Visual Studio facilitan las pruebas de que la API funciona correctamente. En el [siguiente tutorial](../app-service-dotnet-remotely-debug-api-app.md), aprenderá a depurar la aplicación de API mientras se ejecuta en Azure.

Las aplicaciones de API son aplicaciones web con características adicionales para hospedar API, lo que significa que puede usar cualquier método de implementación que funcione con aplicaciones web. Para obtener más información sobre las opciones de implementación para aplicaciones web, vea [Implementación de una aplicación web en el Servicio de aplicaciones de Azure](../app-service-web/web-sites-deploy.md).

Para obtener información sobre las características de las aplicaciones de API, consulte [¿Qué son las aplicaciones de API?](app-service-api-apps-why-best-platform.md).

<!---HONumber=AcomDC_0128_2016-->