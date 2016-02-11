<properties 
	pageTitle="Desencadenadores de aplicaciones de API del Servicio de aplicaciones API | Microsoft Azure" 
	description="Cómo implementar desencadenadores en una aplicación de API del Servicio de aplicaciones de Azure" 
	services="app-service\logic" 
	documentationCenter=".net" 
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="na" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/04/2016" 
	ms.author="guayan"/>

# Desencadenadores de aplicación de API del Servicio de aplicaciones de Azure

## Información general

En este artículo, se explica cómo implementar desencadenadores de aplicación de API y consumirlos desde una aplicación lógica.

Si no está familiarizado con las [aplicaciones de API](app-service-api-apps-why-best-platform.md) del el [Servicio de aplicaciones de Azure](../app-service/app-service-value-prop-what-is.md), le recomendamos que lea la serie formada por varias partes en [Creación de aplicaciones de API](app-service-dotnet-create-api-app.md).

Además, todos los fragmentos de código de este tema proceden del [ejemplo de código de la aplicación de API de FileWatcher](http://go.microsoft.com/fwlink/?LinkId=534802).

Tenga en cuenta que tendrá que descargar el siguiente paquete de NuGet para el código en este artículo para realizar la compilación y ejecución: [http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/).

## ¿Qué son los desencadenadores de aplicación de API?

Es frecuente que una aplicación de API deba desencadenar un evento de forma que los clientes de la aplicación de API realicen la acción apropiada como respuesta al evento. El mecanismo basado en la API de REST que hace posible este escenario se denomina "desencadenador de aplicación de API".

Por ejemplo, supongamos que el código de su cliente utiliza la [aplicación API de conector de Twitter](../app-service-logic/app-service-logic-connector-twitter.md), y el código necesita realizar una acción basada en nuevos tweets que contengan palabras específicas. En este caso, puede configurar un desencadenador de inserción o de extracción para facilitar esta tarea.

## Desencadenador de sondeo frente a desencadenador de inserción

Actualmente, se admiten dos tipos de desencadenadores:

- Desencadenador de sondeo: el cliente sondea la aplicación de API para la notificación de un evento que se ha activado. 
- Desencadenador de inserción: se notifica al cliente mediante la aplicación de API cuando se activa un evento. 

### Desencadenador de sondeo

Un desencadenador de sondeo se implementa como una API de REST normal y espera que sus clientes (por ejemplo, una aplicación lógica) efectúen un sondeo para obtener notificaciones. El cliente puede conservar el estado, pero desencadenador de sondeo en sí no tiene estado.

En la información siguiente sobre los paquetes de solicitud y respuesta, se ilustran algunos aspectos claves del contrato del desencadenador de sondeo:

- Solicitud
    - Método HTTP: GET
    - Parámetros
        - triggerState: este parámetro opcional permite a los clientes especificar su estado para que el desencadenador de sondeo pueda decidir correctamente si debe devolver la notificación o no según el estado especificado.
        - Parámetros específicos de la API
- Respuesta
    - Código de estado **200**: la solicitud es válida y hay una notificación desde el desencadenador. El contenido de la notificación será el cuerpo de la respuesta. El encabezado "Retry-After" en la respuesta indica que hay que recuperar datos de notificación adicionales con una llamada de solicitud posterior.
    - Código de estado **202**: la solicitud es válida, pero no hay una notificación nueva desde el desencadenador.
    - Código de estado **4xx**: la solicitud no es válida. El cliente no debe intentar efectuar la solicitud de nuevo.
    - Código de estado **5xx**: la solicitud ha producido un error interno en el servidor o un problema temporal. El cliente debe intentar efectuar la solicitud de nuevo.

El siguiente fragmento de código es un ejemplo de cómo se debe implementar un desencadenador de sondeo.

    // Implement a poll trigger.
    [HttpGet]
    [Route("api/files/poll/TouchedFiles")]
    public HttpResponseMessage TouchedFilesPollTrigger(
        // triggerState is a UTC timestamp
        string triggerState,
        // Additional parameters
        string searchPattern = "*")
    {
        // Check to see whether there is any file touched after the timestamp.
        var lastTriggerTimeUtc = DateTime.Parse(triggerState).ToUniversalTime();
        var touchedFiles = Directory.EnumerateFiles(rootPath, searchPattern, SearchOption.AllDirectories)
            .Select(f => FileInfoWrapper.FromFileInfo(new FileInfo(f)))
            .Where(fi => fi.LastAccessTimeUtc > lastTriggerTimeUtc);

        // If there are files touched after the timestamp, return their information.
        if (touchedFiles != null && touchedFiles.Count() != 0)
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventTriggered(new { files = touchedFiles });
        }
        // If there are no files touched after the timestamp, tell the caller to poll again after 1 mintue.
        else
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventWaitPoll(new TimeSpan(0, 1, 0));
        }
    }

Para probar este desencadenador de sondeo, siga estos pasos:

1. Implemente la aplicación de API con una configuración de autenticación de **anónimo público**.
2. Ejecute la operación **touch** para tocar un archivo. La siguiente imagen muestra una solicitud de ejemplo a través de Postman. ![Llamada a la operación Touch mediante Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
3. Efectúe una llamada al desencadenador de sondeo con el parámetro **triggerState** establecido en una marca de tiempo anterior al paso 2. La siguiente imagen muestra la solicitud de ejemplo a través de Postman. ![Llamada al desencadenador de sondeo mediante Postman](./media/app-service-api-dotnet-triggers/callpolltriggerfrompostman.PNG)

### Desencadenador de inserción

Un desencadenador de inserción se implementa como una API de REST normal que envía notificaciones a los clientes que se han registrado para recibir notificaciones cuando se activan determinados eventos.

En la información siguiente sobre los paquetes de solicitud y respuesta, se ilustran algunos aspectos claves del contrato del desencadenador de inserción.

- Solicitud
    - Método HTTP: PUT
    - Parámetros
        - triggerId: (obligatorio) cadena opaca (por ejemplo, un GUID) que representa el registro de un desencadenador de inserción.
        - callbackUrl: (obligatorio) dirección URL de la devolución de llamada que se ejecuta al activarse el evento. La ejecución es una llamada HTTP POST simple.
        - Parámetros específicos de la API
- Respuesta
    - Código de estado **200**: solicitud de registro de cliente correcta.
    - Código de estado **4xx**: la solicitud no es válida. El cliente no debe intentar efectuar la solicitud de nuevo.
    - Código de estado **5xx**: la solicitud ha producido un error interno en el servidor o un problema temporal. El cliente debe intentar efectuar la solicitud de nuevo.
- Devolución de llamada
    - Método HTTP: POST
    - Cuerpo de la solicitud: contenido de la notificación.

El siguiente fragmento de código es un ejemplo de cómo se debe implementar un desencadenador de inserción.

    // Implement a push trigger.
    [HttpPut]
    [Route("api/files/push/TouchedFiles/{triggerId}")]
    public HttpResponseMessage TouchedFilesPushTrigger(
        // triggerId is an opaque string.
        string triggerId,
        // A helper class provided by the AppService service SDK.
        // Here it defines the input of the push trigger is a string and the output to the callback is a FileInfoWrapper object.
        [FromBody]TriggerInput<string, FileInfoWrapper> triggerInput)
    {
        // Register the trigger to some trigger store.
        triggerStore.RegisterTrigger(triggerId, rootPath, triggerInput);

        // Extension method provided by the AppService service SDK indicating the registration is completed.
        return this.Request.PushTriggerRegistered(triggerInput.GetCallback());
    }

    // A simple in-memory trigger store.
    public class InMemoryTriggerStore
    {
        private static InMemoryTriggerStore instance;

        private IDictionary<string, FileSystemWatcher> _store;

        private InMemoryTriggerStore()
        {
            _store = new Dictionary<string, FileSystemWatcher>();
        }

        public static InMemoryTriggerStore Instance
        {
            get
            {
                if (instance == null)
                {
                    instance = new InMemoryTriggerStore();
                }
                return instance;
            }
        }

        // Register a push trigger.
        public void RegisterTrigger(string triggerId, string rootPath,
            TriggerInput<string, FileInfoWrapper> triggerInput)
        {
            // Use FileSystemWatcher to listen to file change event.
            var filter = string.IsNullOrEmpty(triggerInput.inputs) ? "*" : triggerInput.inputs;
            var watcher = new FileSystemWatcher(rootPath, filter);
            watcher.IncludeSubdirectories = true;
            watcher.EnableRaisingEvents = true;
            watcher.NotifyFilter = NotifyFilters.LastAccess;

            // When some file is changed, fire the push trigger.
            watcher.Changed +=
                (sender, e) => watcher_Changed(sender, e,
                    Runtime.FromAppSettings(),
                    triggerInput.GetCallback());

            // Assoicate the FileSystemWatcher object with the triggerId.
            _store[triggerId] = watcher;

        }

        // Fire the assoicated push trigger when some file is changed.
        void watcher_Changed(object sender, FileSystemEventArgs e,
            // AppService runtime object needed to invoke the callback.
            Runtime runtime,
            // The callback to invoke.
            ClientTriggerCallback<FileInfoWrapper> callback)
        {
            // Helper method provided by AppService service SDK to invoke a push trigger callback.
            callback.InvokeAsync(runtime, FileInfoWrapper.FromFileInfo(new FileInfo(e.FullPath)));
        }
    }

Para probar este desencadenador de sondeo, siga estos pasos:

1. Implemente la aplicación de API con una configuración de autenticación de **anónimo público**.
2. Vaya a [http://requestb.in/](http://requestb.in/) para crear un RequestBin que servirá como dirección URL de devolución de llamada.
3. Ejecute una llamada al desencadenador de inserción con un GUID como **triggerId** y la dirección URL de RequestBin como **callbackUrl**. ![Llamada al desencadenador de inserción mediante Postman](./media/app-service-api-dotnet-triggers/callpushtriggerfrompostman.PNG)
4. Ejecute la operación **touch** para tocar un archivo. La siguiente imagen muestra una solicitud de ejemplo a través de Postman. ![Llamada a la operación Touch mediante Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
5. Compruebe el RequestBin para confirmar que la devolución de llamada del desencadenador de inserción se realiza con una salida de propiedad. ![Llamada al desencadenador de sondeo mediante Postman](./media/app-service-api-dotnet-triggers/pushtriggercallbackinrequestbin.PNG)

### Descripción de los desencadenadores en la definición de la API

Después de implementar los desencadenadores e implementar la aplicación de API en Azure, navegue hasta la hoja de la **definición de la API** en el portal de vista previa de Azure. Verá que los desencadenadores se reconocen automáticamente en la interfaz de usuario, que se controla mediante la definición de la API Swagger 2.0 de la aplicación de API.

![Hoja de definición de la API](./media/app-service-api-dotnet-triggers/apidefinitionblade.PNG)

Si hace clic en el botón para **descargar Swagger** y abra el archivo JSONj. Verá resultados similares a los siguientes:

    "/api/files/poll/TouchedFiles": {
      "get": {
        "operationId": "Files_TouchedFilesPollTrigger",
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    },
    "/api/files/push/TouchedFiles/{triggerId}": {
      "put": {
        "operationId": "Files_TouchedFilesPushTrigger",
        ...
        "x-ms-scheduler-trigger": "push"
      }
    }

La propiedad de extensión **x-ms-schedular-trigger** representa la forma en que los desencadenadores se han descrito en la definición de la API. Esta propiedad la agrega automáticamente la puerta de enlace de la aplicación de API cuando se solicita la definición de la API a través de la puerta de enlace, si la solicitud se ajusta a uno de los siguientes criterios. (También puede agregar esta propiedad manualmente).

- Desencadenador de sondeo
    - Si el método HTTP es **GET**.
    - Si la propiedad **operationId** contiene la cadena **trigger**.
    - Si la propiedad **parameter** incluye un parámetro con una propiedad **name** establecida en **triggerState**.
- Desencadenador de inserción
    - Si el método HTTP es **PUT**.
    - Si la propiedad **operationId** contiene la cadena **trigger**.
    - Si la propiedad **parameter** incluye un parámetro con una propiedad **name** establecida en **triggerId**.

## Uso de desencadenadores de la aplicación de API en aplicaciones lógicas

### Muestre y configure desencadenadores de la aplicación de API en el Diseñador de aplicaciones lógicas

Si crea una aplicación lógica en el mismo grupo de recursos que la aplicación de API, podrá agregarla al lienzo del diseñador simplemente haciendo clic en ella. Las imágenes siguientes ilustran este proceso:

![Desencadenadores en el Diseñador de aplicaciones lógicas](./media/app-service-api-dotnet-triggers/triggersinlogicappdesigner.PNG)

![Configuración del desencadenador de sondeo en el Diseñador de aplicaciones lógicas](./media/app-service-api-dotnet-triggers/configurepolltriggerinlogicappdesigner.PNG)

![Configuración del desencadenador de inserción en el Diseñador de aplicaciones lógicas](./media/app-service-api-dotnet-triggers/configurepushtriggerinlogicappdesigner.PNG)

## Optimización de los desencadenadores de la aplicación de API para aplicaciones de lógicas

Después de agregar desencadenadores a una aplicación de API, hay algunas cosas que puede hacer para mejorar la experiencia al usar la aplicación de API en una aplicación lógica.

Por ejemplo, el parámetro **triggerState** para los desencadenadores de sondeo se debe configurar con la siguiente expresión en la aplicación lógica. Esta expresión debe evaluar la última invocación del desencadenador desde la aplicación lógica y devolver ese valor.

	@coalesce(triggers()?.outputs?.body?['triggerState'], '')

NOTA: para obtener una explicación de las funciones utilizadas en la expresión anterior, consulte la documentación sobre el [lenguaje de definición del flujo de trabajo de la aplicación lógica](https://msdn.microsoft.com/library/azure/dn948512.aspx).

Los usuarios de la aplicación lógica deben proporcionar la expresión anterior para el parámetro **triggerState** mientras usan el desencadenador. Este valor se puede preconfigurar en el diseñador de aplicaciones lógicas mediante la propiedad de extensión **x-ms-scheduler\_recommendation**. La propiedad de extensión **x-ms-visibility** se puede establecer en el valor *internal*, de modo que el propio parámetro no se muestre en el diseñador. El siguiente fragmento de código ilustra este hecho.

    "/api/Messages/poll": {
      "get": {
	    "operationId": "Messages_NewMessageTrigger",
        "parameters": [
          {
            "name": "triggerState",
            "in": "query",
            "required": true,
            "x-ms-visibility": "internal",
            "x-ms-scheduler-recommendation": "@coalesce(triggers()?.outputs?.body?['triggerState'], '')",
            "type": "string"
          }
        ]
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    }

Para los desencadenadores de inserción, el parámetro **triggerId** debe identificar de forma exclusiva la aplicación lógica. Una práctica recomendada consiste en establecer esta propiedad en el nombre del flujo de trabajo mediante la expresión siguiente:

    @workflow().name

Mediante las propiedades de extensión **x-ms-scheduler-recommendation** y **x-ms-visibility** de la definición de la API, la aplicación de API puede indicar al diseñador de aplicaciones lógicas que configure automáticamente esta expresión para el usuario.

        "parameters":[  
          {  
            "name":"triggerId",
            "in":"path",
            "required":true,
            "x-ms-visibility":"internal",
            "x-ms-scheduler-recommendation":"@workflow().name",
            "type":"string"
          },


### Adición de propiedades de extensión en la definición de la API

La información de metadatos adicionales (por ejemplo, las propiedades de extensión **x-ms-scheduler-recommendation** y **x-ms-visibility**) puede agregarse en la definición de la API de dos maneras: estática o dinámica.

En el caso de los metadatos estáticos, puede editar directamente el archivo */metadata/apiDefinition.swagger.json* en el proyecto y agregar manualmente las propiedades.

Para las aplicaciones de API con metadatos dinámicos, puede editar el archivo SwaggerConfig.cs para agregar un filtro de operación que pueda agregar estas extensiones.

    GlobalConfiguration.Configuration 
        .EnableSwagger(c =>
            {
                ...
                c.OperationFilter<TriggerStateFilter>();
                ...
            }


El siguiente es un ejemplo de cómo esta clase se puede implementar para facilitar el escenario de metadatos dinámicos.

    // Add extension properties on the triggerState parameter
    public class TriggerStateFilter : IOperationFilter
    {

        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
        {
            if (operation.operationId.IndexOf("Trigger", StringComparison.InvariantCultureIgnoreCase) >= 0)
            {
                // this is a possible trigger
                var triggerStateParam = operation.parameters.FirstOrDefault(x => x.name.Equals("triggerState"));
                if (triggerStateParam != null)
                {
                    if (triggerStateParam.vendorExtensions == null)
                    {
                        triggerStateParam.vendorExtensions = new Dictionary<string, object>();
                    }

                    // add 2 vendor extensions
                    // x-ms-visibility: set to 'internal' to signify this is an internal field
                    // x-ms-scheduler-recommendation: set to a value that logic app can use
                    triggerStateParam.vendorExtensions.Add("x-ms-visibility", "internal");
                    triggerStateParam.vendorExtensions.Add("x-ms-scheduler-recommendation",
                                                           "@coalesce(triggers()?.outputs?.body?['triggerState'], '')");
                }
            }
        }
    }
 

<!---HONumber=AcomDC_0107_2016-->