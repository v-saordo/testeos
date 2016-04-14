## Revisión de metadatos de la aplicación de API

Los metadatos que permiten la implementación de un proyecto de API web como aplicación de API se encuentran en un archivo *apiapp.json* dentro de una carpeta *Metadatos*.

![](./media/app-service-api-review-metadata/metadatainse.png)

El contenido predeterminado del archivo *apiapp.json* es similar al ejemplo siguiente:

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "ContactsList",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "ContactsList",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    }
		}

Observe el `apiDefinition` extremo `/swagger/docs/v1`: de forma predeterminada, los proyectos de aplicación de API usan el paquete NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) para ofrecer la generación automática de metadatos de [Swagger](http://swagger.io/).

Para este tutorial, puede aceptar los valores predeterminados. En la sección [Metadatos de la aplicación de API](#api-app-metadata), más adelante en este tutorial, se explica cómo personalizar estos metadatos.

<!---HONumber=Oct15_HO3-->