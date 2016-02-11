<properties 
	pageTitle="Consumo de una aplicación de API interna en el Servicio de aplicaciones de Azure desde un cliente .NET" 
	description="Aprenda a consumir una aplicación de API desde una aplicación de API .NET del mismo grupo de recursos, mediante el SDK del Servicio de aplicaciones." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="bradyg"/>

# Consumo de una aplicación de API interna en el Servicio de aplicaciones de Azure desde un cliente .NET 

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Información general

En este tutorial se muestra cómo escribir código para una [aplicación de API](app-service-api-apps-why-best-platform.md) de ASP.NET que llama a otra aplicación de API configurada para el nivel de acceso **Interno**. Ambas aplicaciones de API deben estar en el mismo grupo de recursos. Puede usarse el mismo código para llamar a una aplicación de API interna desde una [aplicación móvil](../app-service-mobile/app-service-mobile-value-prop-preview.md).

Va a crear una API web de ContactNames. El método Get de la API web llamará a una aplicación de API ContactsList y devolverá solo los nombres de la información de contacto proporcionada por dicha aplicación. Esta es la pantalla de interfaz de usuario de Swagger para una llamada correcta al método Get de ContactNames.

![](./media/app-service-api-dotnet-consume-internal/tryitout.png)

Para obtener información sobre cómo llamar a aplicaciones de API que se configuran para los niveles de acceso **Público (anónimo)** o **Público (autenticado)**, consulte [Consumo de una aplicación de API desde un cliente .NET en el Servicio de aplicaciones de Azure](app-service-api-dotnet-consume.md).

## Requisitos previos

En este tutorial se supone que sabe cómo crear proyectos y agregarles código en Visual Studio, y también cómo [administrar aplicaciones de API en el Portal de vista previa de Azure](../app-service-api-apps-manage-in-portal.md).

El proyecto y los ejemplos de código de este artículo se basan en el proyecto de aplicaciones de API que crea e implementa en estos artículos:

- [Creación de una clave de API](app-service-dotnet-create-api-app.md)
- [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Este tutorial requiere la versión 2.6 o posterior de SDK de Azure para .NET.

### Configuración de la aplicación de API de destino

1. Si aún no lo ha hecho, siga los pasos del tutorial [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md) para implementar el proyecto de ejemplo ContactsList en una aplicación de API en su suscripción de Azure.

2. En el [Portal de vista previa de Azure](https://portal.azure.com/), en la hoja **Aplicación de API** de la aplicación de API ContactsList que implementó anteriormente, haga clic en **Configuración > Configuración de la aplicación** y establezca **Nivel de acceso** en **Interno** y luego haga clic en **Guardar**.

	![](./media/app-service-api-dotnet-consume-internal/setinternal.png)
 
## Creación de una nueva aplicación de API que llamará a la aplicación de API existente

- En Visual Studio, cree un proyecto de aplicación de API denominado ContactNames mediante la plantilla de proyecto de aplicación de API de Azure.

	Este es el mismo proceso que siguió en [Creación de una aplicación de API](app-service-dotnet-create-api-app.md), pero ahora agregará un código diferente al proyecto más adelante en este tutorial.
 
## Agregar código de cliente generado por el SDK del Servicio de aplicaciones

Los siguientes pasos se explican con más detalle en [Consumo de una aplicación de API desde un cliente .NET en el Servicio de aplicaciones de Azure](app-service-api-dotnet-consume.md).

3. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto (no en la solución) y seleccione **Agregar > Cliente de aplicación de API de Azure**. 

3. En el cuadro de diálogo **Agregar cliente de aplicación de API de Azure**, haga clic en **Descargar de la aplicación de API de Azure**.

5. En la lista desplegable, seleccione la aplicación de API que desea llamar. Para este tutorial, seleccione la aplicación de API ContactsList que creó anteriormente.

7. Haga clic en **Aceptar**.

## Habilitación de la IU de Swagger

De forma predeterminada, los proyectos de aplicación de API se habilitan con la generación automática de metadatos de [Swagger](http://swagger.io/ "Información oficial de Swagger"), pero la plantilla de nuevo proyecto de aplicación de API de Azure deshabilita la página de prueba de API. En esta sección, habilitará la página de prueba.

1. Abra el archivo *App\_Start/SwaggerConfig.cs* y busque **EnableSwaggerUI**:

2. Quite la marca de comentario de las líneas de código siguientes:

	        })
	    .EnableSwaggerUi(c =>
	        {

## Creación de un controlador

5. Haga clic con el botón secundario en la carpeta **Controladores** y seleccione **Agregar > Controlador**. 

6. En el cuadro de diálogo **Agregar scaffold**, seleccione la opción **Controlador de Web API 2 - Vacío** y haga clic en **Agregar**.

7. Asigne el nombre **ContactsNamesController** y haga clic en **Agregar**.

## Agregar código para llamar a la aplicación de API

Para llamar a una aplicación de API que se ha protegido estableciendo su nivel de acceso en **Interno**, tiene que agregar encabezados de autenticación interna a las solicitudes HTTP. Estos encabezados informan a la aplicación de API de destino que el origen de la llamada es una aplicación de API del mismo nivel que llama desde el mismo grupo de recursos.

El SDK del Servicio de aplicaciones genera clases de cliente que simplifican el código que escribe para llamar a la aplicación de API. Para llamar a la aplicación de API con un nivel de acceso **Público (anónimo)**, lo único que debe hacer es crear un objeto de cliente y llamar a los métodos que hay en él, como en este ejemplo:

		var client = new ContactsList();
		var contacts = await client.Contacts.GetAsync();

Sin embargo, para agregar encabezados de autenticación, deberá acceder al objeto `HttpRequestMessage`, y este objeto no lo tiene ahí. Para obtener acceso a la solicitud y agregar los encabezados, debe crear una clase `DelegatingHandler` y pasar una instancia de ella al constructor del cliente generado.

1. Agregue al proyecto un archivo de clase denominado *InternalCredentialHandler.cs*, y sustituya el código de plantilla por el código siguiente.

		using Microsoft.Azure.AppService.ApiApps.Service;
		using System.Net.Http;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace ContactNames
		{
		    public class InternalCredentialHandler : DelegatingHandler
		    {
		        protected override Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
		        {
		            Runtime.FromAppSettings(request).SignHttpRequest(request);
		            return base.SendAsync(request, cancellationToken);
		        }
		    }
		}

	Este código llama a `SignHttpRequest` para agregar los encabezados de autenticación a cada solicitud enviada por la clase de cliente generada:

		Runtime.FromAppSettings(this.Request).SignHttpRequest

1. En *ContactNamesController.cs*, sustituya el código de plantilla por el siguiente código.

		using ContactNames.Models;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactNames.Controllers
		{
		    public class ContactNamesController : ApiController
		    {
		        [HttpGet]
		        public async Task<IEnumerable<string>> Get()
		        {
		            var names = new List<string>();

		            var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });
		            var contacts = await client.Contacts.GetAsync();
		
		            foreach (Contact contact in contacts)
		            {
		                names.Add(contact.Name);
		            }		
		            return names;
		        }
		    }
		}

	Este código pasa el controlador al constructor de la clase de cliente generada:

		var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });

### Implementación

No se puede probar ejecutándolo localmente. Debe implementar el código y ejecutarlo en una aplicación de API de Azure; en caso contrario, no podrá agregar los encabezados de autenticación correctos y se rechazarán las llamadas.

Los siguientes pasos de implementación se explican con más detalle en [Implementación de una aplicación de API](app-service-dotnet-deploy-api-app.md).

1. Cree una aplicación de API ContactNames.

	* En el **Explorador de soluciones**, haga clic en el proyecto (no en la solución) y en **Publicar**. 

	* Haga clic en la pestaña **Perfil** y en **Aplicaciones de API de Microsoft Azure**.

	* Haga clic en **Nuevo** para aprovisionar una nueva aplicación de API en su suscripción de Azure.

	* En el cuadro de diálogo **Crear una aplicación de API**, escriba ContactNames como el **Nombre de la aplicación de API**.

	* Para los demás valores del cuadro de diálogo **Crear una aplicación de API**, especifique los mismos valores que especificó anteriormente para [Implementar una aplicación de API](app-service-dotnet-deploy-api-app.md).

		Lo más importante es asegurarse de que la nueva aplicación de API se cree en el mismo grupo de recursos que la aplicación de API que se va a llamar.

	* Haga clic en **Aceptar**.

2. Implemente el código en la nueva aplicación de API.

	* Una vez que se ha aprovisionado la aplicación de API, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y seleccione **Publicar** para volver a abrir el cuadro de diálogo de publicación.

	* En el cuadro de diálogo **Publicar en Web**, haga clic en **Publicar** para comenzar el proceso de implementación.

### Prueba

En esta sección se usa la interfaz de usuario de Swagger para probar la nueva aplicación de API y comprobar que puede llamar a la aplicación de API que creó anteriormente.

1. Abra la dirección URL de la nueva aplicación de API en el explorador.

	Con la configuración de publicación predeterminada, cuando Visual Studio completa el proceso de publicación, se abre automáticamente un explorador que lleva a la dirección URL de la aplicación de API. Si no es así, o ha cerrado esa ventana del explorador, siga estos pasos para llegar a la misma dirección URL:

	* En el Portal de vista previa de Azure, vaya a la hoja Aplicación de API para su nueva aplicación de API ContactsName.

	* Haga clic en **URL**.

		![](./media/app-service-api-dotnet-consume-internal/clickurl.png)
  
5. En la barra de direcciones del explorador, agregue `/swagger` al final de la URL y presione Entrar.

	Por ejemplo, la dirección URL resultante será similar a la siguiente:

		https://microsoft-apiapp214f26e673e5449a214f26e673e5449a.azurewebsites.net/swagger

1. En la página de la interfaz de usuario de Swagger, haga clic en **ContactNames > Get (Obtener) > Try it out (Probarlo).**

	![](./media/app-service-api-dotnet-consume-internal/tryitout.png)
  
	La página muestra los nombres de contacto en la sección de cuerpo de respuesta, que comprueba que la aplicación de API ContactNames recuperó correctamente los datos de la aplicación de API ContactsList.

	Si desea comprobar que el valor de configuración **Interno** protege la aplicación de API ContactsList, convierta en comentario la llamada `SignHttpRequest` en *ContactNamesController.cs*, vuelva a implementarla, ejecute de nuevo **Try ir now (Probar ahora)** de Swagger y recibirá un mensaje de error.


## Agregar código para llamar a la aplicación de API mediante HttpClient
 
El SDK del Servicio de aplicaciones depende de las definiciones de API de Swagger para generar clases de cliente. Si quiere llamar a una aplicación de API que no ha implementado definiciones de API de Swagger, puede hacerlo mediante `HttpClient`. En este caso, sigue usando el método `SignHttpRequest`, pero lo llama directamente en el objeto `HttpRequestMessage`.

1. En *ContactNamesController.cs*, agregue el siguiente código antes de la instrucción `return names;` en el método `Get`.

		var httpClient = new HttpClient();
		HttpRequestMessage httpRequest = new HttpRequestMessage();
		httpRequest.Method = HttpMethod.Get;
		httpRequest.RequestUri = new Uri("https://{yourapiappurl}/api/contacts");
		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);
		var response = await httpClient.SendAsync(httpRequest); 
		var contacts2 = await response.Content.ReadAsAsync<List<Contact>>();
		foreach (Contact contact in contacts2)
		{
		    names.Add(contact.Name);
		}

	Este código pasa el objeto de solicitud entrante al método `SignHttpRequest` para firmar el objeto de solicitud saliente:

		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);

2. En el Portal de vista previa de Azure, vaya a la hoja Aplicación de API de la aplicación ContactsList y copie la URL.

	![](./media/app-service-api-dotnet-consume-internal/clurl.png)
 
4. Sustituya la cadena de marcador de posición "{yourapiappurl}" en el código de controlador por la dirección URL real. Cuando haya terminado, esa línea de código será similar al ejemplo siguiente.

		httpRequest.RequestUri = new Uri("https://microsoft-apiappd99e684d99e684d99e684d99e684.azurewebsites.net/api/contacts");

### Implementación y prueba

1. Siga el mismo procedimiento que realizó anteriormente para implementar el código de la aplicación de API.

	**Sugerencia:** puede omitir el cuadro de diálogo **Publicar en Web** y volver a implementarlo haciendo clic en un solo botón en la barra de herramientas. En Visual Studio, haga clic en **Ver > Barras de herramientas**, y habilite la barra de herramientas **Publicación en Web con un solo clic**.
 
2. Siga el procedimiento que realizó anteriormente para usar la interfaz de usuario de Swagger.

	Puesto que salió en el código HttpClient, la salida muestra esta vez un conjunto de nombres duplicado.

	![](./media/app-service-api-dotnet-consume-internal/dupnames.png)
  
## Pasos siguientes

En este artículo se ha mostrado cómo consumir una aplicación de API interna de un cliente. NET. Para obtener información sobre cómo consumir aplicaciones de API establecidas en los niveles de acceso **Público (anónimo)** o **Público (autenticado)**, consulte [Consumo de una aplicación de API desde un cliente .NET en el Servicio de aplicaciones de Azure](app-service-api-dotnet-consume.md).

Para obtener más ejemplos de código que llama a una aplicación de API de clientes. NET, descargue la aplicación de ejemplo [Tarjetas de Azure](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample).

Para obtener información sobre la autenticación en el Servicio de aplicaciones, consulte [Autenticación para aplicaciones de API y aplicaciones móviles](../app-service/app-service-authentication-overview.md).
 

<!---HONumber=AcomDC_0114_2016-->