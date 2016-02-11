<properties
    pageTitle="Supervisión de API con Administración de API de Azure, Centros de eventos y Runscope"
    description="Aplicación de ejemplo que muestra la directiva log-to-eventhub que conecta Administración de API de Azure, Centros de eventos de Azure y Runscope para el registro y supervisión de HTTP"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager=""
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/10/2015"
    ms.author="v-darmi"/>

# Supervisión de API con Administración de API de Azure, Centros de eventos y Runscope

El [servicio Administración de API](api-management-key-concepts.md) proporciona muchas capacidades para mejorar el procesamiento de solicitudes de HTTP enviadas a la API HTTP. Sin embargo, la existencia de las solicitudes y respuestas son transitorias. Se realiza la solicitud y fluye a través del servicio Administración de API a la API de back-end. La API procesa la solicitud y se pasa una respuesta al consumidor de API. El servicio Administración de API mantiene algunas estadísticas importantes acerca de las API que se muestran en el panel del portal de Publisher, pero aparte de eso, los detalles desaparecen.

Mediante la [directiva](api-management-howto-policies.md) [log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) en el servicio Administración de API, puede enviar los detalles de la solicitud y la respuesta a un [Centro de eventos de Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Existen diversos motivos por los que puede desear generar eventos de mensajes HTTP que se envían a las API. Algunos ejemplos incluyen la traza de auditoría de las actualizaciones, análisis de uso, las alertas de excepción e integraciones de terceros.

Este artículo muestra cómo capturar el mensaje de solicitud y respuesta de HTTP completo, enviarlo a un Centro de eventos y, a continuación, retransmitir ese mensaje a un servicio que proporciona servicios de registro y supervisión de HTTP.

## ¿Por qué se envían desde el servicio Administración de API?
Es posible escribir middleware HTTP que se puede conectar a los marcos API HTTP para capturar solicitudes y respuestas de HTTP e introducirlas en sistemas de registro y supervisión. El inconveniente de este enfoque es que el middleware HTTP debe integrarse en la API de back-end y debe coincidir con la plataforma de la API. Si hay varias API, todas deben implementar el middleware. A menudo existen motivos por los que no se pueden actualizar las API de back-end.

El usp del servicio Administración de API de Azure para la integración con la infraestructura de registro proporciona una solución centralizada e independiente de la plataforma. También es escalable, en parte debido a las capacidades de [replicación geográfica](api-management-howto-deploy-multi-region.md) de Administración de API de Azure.

## ¿Por qué se envía a un Centro de eventos de Azure?
Es razonable preguntar por qué crear una directiva que es específica de Centros de eventos de Azure Hay muchos lugares diferentes donde puede ser conveniente registrar mis solicitudes. ¿Por qué no simplemente enviar las solicitudes directamente al destino final? Es una opción. Sin embargo, al realizar solicitudes de registro de un servicio Administración de API, es necesario tener en cuenta cómo afectarán los mensajes de registro al rendimiento de la API. Para manejar los aumentos graduales de la carga puede aumentar las instancias disponibles de los componentes del sistema o aprovechar las ventajas de la replicación geográfica. Sin embargo, picos de tráfico cortos pueden hacer que las solicitudes se retrasen significativamente si las solicitudes a la infraestructura de registro empiezan a ralentizarse debido a la carga.

Centros de eventos de Azure está diseñado para la entrada de grandes volúmenes de datos, con capacidad para tratar con un número mucho mayor de eventos que el número de solicitudes de HTTP que procesan la mayoría de las API. El Centro de eventos actúa como un tipo de búfer sofisticada entre el servicio Administración de API y la infraestructura que almacena y procesa los mensajes. Esto garantiza que no se verá afectado el rendimiento de la API debido a la infraestructura de registro.

Una vez que los datos se han pasado a un Centro de eventos, se conservan y esperan a que los consumidores del centro de eventos los procese. Al Centro de eventos no le importa cómo se procesará, simplemente se ocupa de asegurarse de que el mensaje se entregará correctamente.

Centros de eventos tiene la capacidad de transmitir eventos a varios grupos de consumidores. Esto permite que sistemas completamente diferentes procesen los eventos. Esto permite muchos escenarios de integración sin agregar retrasos en el procesamiento de la solicitud de API dentro del servicio Administración de API, ya que solo es necesario generar un evento.

## Una directiva para enviar mensajes de aplicación/http
Un Centro de eventos acepta datos de eventos como una cadena simple. El contenido de esa cadena depende completamente de usted. Para poder empaquetar una solicitud de HTTP y enviarla a Centros de eventos, debemos formatear la cadena con la información de solicitud o respuesta. En situaciones como esta, si hay un formato existente que podamos reusar, es posible que no necesitemos escribir nuestro propio código de análisis. Inicialmente consideré el uso de [HAR](http://www.softwareishard.com/blog/har-12-spec/) para enviar solicitudes y respuestas de HTTP. Sin embargo, este formato está optimizado para almacenar una secuencia de solicitudes de HTTP en un formato basado en JSON. Contenía un número de elementos obligatorios que agregaba una complejidad innecesaria para el escenario de pasar el mensaje HTTP a través del cable.

Una opción alternativa era usar el tipo de soporte físico `application/http`, como se describe en la especificación HTTP [RFC 7230](http://tools.ietf.org/html/rfc7230). Este tipo de soporte físico usa el mismo formato que se usa para enviar realmente mensajes de HTTP a través del cable, pero el mensaje completo se puede colocar en el cuerpo de otra solicitud de HTTP. En nuestro caso simplemente vamos a usar el cuerpo como nuestro mensaje para enviarlo a Centros de eventos. Convenientemente, hay un analizador en las bibliotecas de [Microsoft ASP.NET Web API 2.2 Cliente](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) que puede analizar este formato y convertirlo a `HttpRequestMessage` nativo y objetos `HttpResponseMessage`.

Para poder crear este mensaje debemos aprovechar las [expresiones de directiva](https://msdn.microsoft.com/library/azure/dn910913.aspx) basadas en C# en Administración de API de Azure. A continuación se proporciona la directiva que envía un mensaje de solicitud de HTTP a Centros de eventos de Azure.

       <log-to-eventhub logger-id="conferencelogger" partition-id="0">
       @{
           var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                       context.Request.Method,
                                                       context.Request.Url.Path + context.Request.Url.QueryString);

           var body = context.Request.Body?.As<string>(true);
           if (body != null && body.Length > 1024)
           {
               body = body.Substring(0, 1024);
           }

           var headers = context.Request.Headers
                                  .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                  .ToArray<string>();

           var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

           return "request:"   + context.Variables["message-id"] + "\n"
                               + requestLine + headerString + "\r\n" + body;
       }
       </log-to-eventhub>

### Declaración de directiva
Hay algunas cosas específicas que merece la pena mencionar acerca de esta expresión de directiva. La directiva log-to-eventhub tiene un atributo denominado logger-id que hace referencia al nombre del registrador que se ha creado en el servicio Administración de API. Los detalles de cómo configurar un registrador del Centro de eventos en el servicio Administración de API pueden encontrarse en el documento [Cómo registrar eventos en los centros de eventos de Azure en Administración de API de Azure](api-management-howto-log-event-hubs.md). El segundo atributo es un parámetro opcional que indica a Centros de eventos en qué partición almacenar el mensaje. Centros de eventos usa particiones para habilitar la escalabilidad y requiere un mínimo de dos. La entrega ordenada de mensajes solo se garantiza dentro de una partición. Si no se indica al Centro de eventos en qué partición desea colocar el mensaje, usará un algoritmo round-robin para distribuir la carga. Sin embargo, eso puede hacer que algunos de nuestros mensajes no se procesen en el orden correcto.

### Particiones
Para garantizar que nuestros mensajes se entregan a los consumidores en orden y aprovechar la capacidad de distribución de carga de las particiones, elegí enviar mensajes de solicitud de HTTP a una partición y los mensajes de respuesta de HTTP a una segunda partición. Esto garantiza una distribución equilibrada de la carga y podemos garantizar que se consumirán todas las solicitudes en orden y todas las respuestas se consumirán en orden. Es posible que una respuesta se consuma antes de la solicitud correspondiente, pero como esto no es un problema porque tenemos un mecanismo diferente para correlacionar las solicitudes a las respuestas y sabemos que las solicitudes van siempre antes las respuestas.

### Cargas de HTTP
Después de crear el `requestLine` comprobamos si se debe truncar el cuerpo de la solicitud. El cuerpo de la solicitud se trunca a solo 1024. Esto podría aumentarse; sin embargo, los mensajes individuales del Centro de eventos están limitados a 256 KB, por lo que es probable que algunos cuerpos de los mensajes HTTP no quepan en un único mensaje. Al realizar el registro y análisis, una cantidad significativa de información puede derivarse simplemente de la línea de solicitud de HTTP y los encabezados. Además, muchas solicitudes de API devuelven solo cuerpos pequeños, por lo que la pérdida de valor de la información debido al truncamiento de cuerpos grandes es bastante mínima en comparación con la reducción de los costos de almacenamiento, transferencia y transformación para mantener todo el contenido del cuerpo. Una nota final acerca del procesamiento del cuerpo es que necesitamos pasar `true` al método As<string>() porque leemos el contenido del cuerpo, pero también queríamos que la API de back-end pudiera leer el cuerpo. Al pasar true a este método, hacemos que el cuerpo se almacene en la búfer para que se pueda leer una segunda vez. Es importante tenerlo en cuenta si tiene una API que carga archivos muy grandes o usa el sondeo largo. En estos casos, es mejor evitar totalmente la lectura del cuerpo.

### Encabezados HTTP
Los encabezados HTTP pueden transferirse simplemente al formato del mensaje con un formato simple de par clave/valor. Hemos decidido eliminar ciertos campos de seguridad confidenciales para evitar la pérdida innecesaria de información de credenciales. No es probable que se usen claves de API y otras credenciales para los análisis. Si deseamos realizar el análisis en el usuario y el producto específico que usan, podíamos obtenerlo desde el objeto `context` y agregarlo al mensaje.
### Metadatos del mensaje
Cuando se crea el mensaje completo para enviarlo al Centro de eventos, la primera línea no es realmente parte del mensaje `application/http`. La primera línea son metadatos adicionales que incluyen si el mensaje es un mensaje de solicitud o de respuesta y un identificador de mensaje que se usa para correlacionar las solicitudes y las respuestas. El identificador del mensaje se crea mediante otra directiva que tiene este aspecto:

    <set-variable name="message-id" value="@(Guid.NewGuid())" />

Podríamos haber creado el mensaje de solicitud, almacenarlo en una variable hasta que se devuelva la respuesta y, después, simplemente enviar la solicitud y la respuesta como un solo mensaje. Sin embargo, al enviar la solicitud y la respuesta de forma independiente y usar un identificador de mensaje para correlacionar las dos, obtenemos un poco más flexibilidad en el tamaño de mensaje, la capacidad de aprovechar varias particiones al tiempo que se mantiene el orden de los mensajes y la solicitud aparecerá antes en nuestro panel de registro. También puede haber algunos escenarios donde nunca se envíe una respuesta válida al Centro de eventos, posiblemente debido a un error de solicitud irrecuperable en el servicio Administración de API, pero aún tendremos un registro de la solicitud.

La directiva para enviar el mensaje de respuesta de HTTP es muy parecida a la solicitud; por tanto, la configuración de directiva completa tiene el siguiente aspecto:

      <policies>
      	<inbound>
      		<set-variable name="message-id" value="@(Guid.NewGuid())" />
      		<log-to-eventhub logger-id="conferencelogger" partition-id="0">
              @{
                  var requestLine = string.Format("{0} {1} HTTP/1.1\r\n",
                                                              context.Request.Method,
                                                              context.Request.Url.Path + context.Request.Url.QueryString);

                  var body = context.Request.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Request.Headers
                                       .Where(h => h.Key != "Authorization" && h.Key != "Ocp-Apim-Subscription-Key")
                                       .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                       .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "request:"   + context.Variables["message-id"] + "\n"
                                      + requestLine + headerString + "\r\n" + body;
              }
          </log-to-eventhub>
      	</inbound>
      	<backend>
      		<forward-request follow-redirects="true" />
      	</backend>
      	<outbound>
      		<log-to-eventhub logger-id="conferencelogger" partition-id="1">
              @{
                  var statusLine = string.Format("HTTP/1.1 {0} {1}\r\n",
                                                      context.Response.StatusCode,
                                                      context.Response.StatusReason);

                  var body = context.Response.Body?.As<string>(true);
                  if (body != null && body.Length > 1024)
                  {
                      body = body.Substring(0, 1024);
                  }

                  var headers = context.Response.Headers
                                                  .Select(h => string.Format("{0}: {1}", h.Key, String.Join(", ", h.Value)))
                                                  .ToArray<string>();

                  var headerString = (headers.Any()) ? string.Join("\r\n", headers) + "\r\n" : string.Empty;

                  return "response:"  + context.Variables["message-id"] + "\n"
                                      + statusLine + headerString + "\r\n" + body;
             }
          </log-to-eventhub>
      	</outbound>
      </policies>

La directiva `set-variable` crea un valor que sea accesible por la directiva `log-to-eventhub` en la sección `<inbound>` y la sección `<outbound>`.

## Recepción de eventos de Centros de eventos
Se reciben eventos del Centro de eventos de Azure mediante el [protocolo AMQP](http://www.amqp.org/). El equipo del Bus de servicio de Microsoft hizo que las bibliotecas cliente disponibles para facilitar los eventos de consumo. Existen dos enfoques diferentes admitidos, uno se está un *Consumidor directo* y la otra es emplear la clase `EventProcessorHost`. Ejemplos de estos dos enfoques pueden encontrarse en la [Guía de programación de Centros de eventos](../event-hubs/event-hubs-programming-guide.md). La versión abreviada de las diferencias es que `Direct Consumer` proporciona un control total y `EventProcessorHost` realiza parte del trabajo de fontanería por usted, pero hace determinadas suposiciones sobre cómo procesará los eventos.

### EventProcessorHost
En este ejemplo, usaremos `EventProcessorHost` por motivos de simplicidad; sin embargo es posible que no sea la mejor opción para este escenario concreto. `EventProcessorHost` realiza el trabajo duro de asegurarse de que no tiene que preocuparse acerca de problemas de subprocesamiento dentro de una clase de procesador de eventos concreto. Sin embargo, en nuestro escenario, simplemente estamos convirtiendo el mensaje a otro formato y lo pasamos a otro servicio con un método asincrónico. No es necesario actualizar el estado compartido y, por tanto, no hay riesgo de problemas de subprocesamiento. Para la mayoría de los escenarios, `EventProcessorHost` es probablemente la mejor opción y es ciertamente la opción más fácil.

### IEventProcessor
El concepto central al usar `EventProcessorHost` es crear una implementación de la interfaz `IEventProcessor` que contiene el método `ProcessEventAsync`. La esencia de ese método se muestra aquí:

  async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages) {

           foreach (EventData eventData in messages)
           {
               _Logger.LogInfo(string.Format("Event received from partition: {0} - {1}", context.Lease.PartitionId,eventData.PartitionKey));

               try
               {
                   var httpMessage = HttpMessage.Parse(eventData.GetBodyStream());
                   await _MessageContentProcessor.ProcessHttpMessage(httpMessage);
               }
               catch (Exception ex)
               {
                   _Logger.LogError(ex.Message);
               }
           }
            ... checkpointing code snipped ...
        }

Se pasa una lista de objetos EventData al método y realizamos la iteración en esa lista. Se analizan los bytes de cada método en un objeto HttpMessage y ese objeto se pasa a una instancia de IHttpMessageProcessor.

### HttpMessage
La instancia `HttpMessage` contiene tres elementos de datos:

      public class HttpMessage
       {
           public Guid MessageId { get; set; }
           public bool IsRequest { get; set; }
           public HttpRequestMessage HttpRequestMessage { get; set; }
           public HttpResponseMessage HttpResponseMessage { get; set; }

        ... parsing code snipped ...

      }

La instancia `HttpMessage` contiene un GUID `MessageId` que nos permite conectar la solicitud de HTTP a la respuesta de HTTP correspondiente y un valor booleano que identifica si el objeto contiene una instancia de HttpRequestMessage y HttpResponseMessage. Mediante las clases HTTP integradas de `System.Net.Http`, pude aprovechar el código de análisis `application/http` que se incluye en `System.Net.Http.Formatting`.

### IHttpMessageProcessor
La instancia `HttpMessage` se reenvía a la implementación de `IHttpMessageProcessor` que es una interfaz que creé para desacoplar la recepción y la interpretación del evento del Centro de eventos de Azure y el procesamiento real de la misma.


## Reenvío del mensaje HTTP
En este ejemplo, decidí que sería interesante insertar la solicitud de HTTP en [Runscope](http://www.runscope.com). Runscope es un servicio basado en la nube que se especializa en depuración, registro y supervisión de HTTP. Tiene un nivel gratuito, por lo que resulta fácil probarlo y nos permite ver en tiempo real cómo fluyen las solicitudes de HTTP a través de nuestro servicio Administración de API.

Las implementación `IHttpMessageProcessor` tiene este aspecto:

      public class RunscopeHttpMessageProcessor : IHttpMessageProcessor
       {
           private HttpClient _HttpClient;
           private ILogger _Logger;
           private string _BucketKey;
           public RunscopeHttpMessageProcessor(HttpClient httpClient, ILogger logger)
           {
               _HttpClient = httpClient;
               var key = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-KEY", EnvironmentVariableTarget.User);
               _HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", key);
               _HttpClient.BaseAddress = new Uri("https://api.runscope.com");
               _BucketKey = Environment.GetEnvironmentVariable("APIMEVENTS-RUNSCOPE-BUCKET", EnvironmentVariableTarget.User);
               _Logger = logger;
           }

           public async Task ProcessHttpMessage(HttpMessage message)
           {
               var runscopeMessage = new RunscopeMessage()
               {
                   UniqueIdentifier = message.MessageId
               };

               if (message.IsRequest)
               {
                   _Logger.LogInfo("Sending HTTP request " + message.MessageId.ToString());
                   runscopeMessage.Request = await RunscopeRequest.CreateFromAsync(message.HttpRequestMessage);
               }
               else
               {
                   _Logger.LogInfo("Sending HTTP response " + message.MessageId.ToString());
                   runscopeMessage.Response = await RunscopeResponse.CreateFromAsync(message.HttpResponseMessage);
               }

               var messagesLink = new MessagesLink() { Method = HttpMethod.Post };
               messagesLink.BucketKey = _BucketKey;
               messagesLink.RunscopeMessage = runscopeMessage;
               var runscopeResponse = await _HttpClient.SendAsync(messagesLink.CreateRequest());
               _Logger.LogDebug("Request sent to Runscope");
           }
       }

Pude aprovechar una [biblioteca de cliente existente para Runscope](http://www.nuget.org/packages/Runscope.net.hapikit/0.9.0-alpha) que facilita inserción de las instancias `HttpRequestMessage` y `HttpResponseMessage` a su servicio. Para tener acceso a la API Runscope, necesitará una cuenta y una clave de API. Pueden encontrarse instrucciones para obtener una clave de API en la presentación en pantalla [Creación de aplicaciones para acceder a la API Runscope](http://blog.runscope.com/posts/creating-applications-to-access-the-runscope-api).

## Ejemplo completo
El [código fuente](https://github.com/darrelmiller/ApimEventProcessor) y las pruebas del ejemplo se encuentran en Github. Necesitará un [servicio Administración de API](api-management-get-started.md), [un Centro de eventos conectado](api-management-howto-log-event-hubs.md) y una [cuenta de almacenamiento](../storage/storage-create-storage-account.md) para ejecutar el ejemplo usted mismo.

El ejemplo es simplemente una aplicación de consola simple que realiza escuchas de eventos procedentes del Centro de eventos, los convierte a objetos `HttpRequestMessage` y `HttpResponseMessage` y, a continuación, los reenvía a la API Runscope.

En la siguiente imagen animada, puede ver una solicitud realizada a una API en el portal para desarrolladores, la aplicación de consola que muestra el mensaje que se recibe, procesa y reenvía y, a continuación, la solicitud y respuesta se muestra en el inspector de tráfico Runscope.

![Demostración de solicitud se reenvía a Runscope](./media/api-management-log-to-eventhub-sample/apim-eventhub-runscope.gif)

## Resumen
El servicio de Administración de API de Azure proporciona un lugar ideal para capturar el tráfico de HTTP hacia y desde la API. Centros de eventos de Azure es una solución altamente escalable y de bajo costo para capturar ese tráfico y colocarlo en sistemas de procesamiento secundario para el registro, supervisión y otros análisis sofisticados. La conexión a sistemas de supervisión del tráfico de terceros como Runscope es tan simple como las pocas docenas de líneas de código.

## Pasos siguientes
-	Obtenga más información acerca de los centros de eventos de Azure
	-	[Introducción a los centros de eventos de Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
	-	[Recepción de mensajes con EventProcessorHost](../event-hubs/event-hubs-csharp-ephcs-getstarted.md#receive-messages-with-eventprocessorhost)
	-	[Guía de programación de Centros de eventos](../event-hubs/event-hubs-programming-guide.md)
-	Obtenga más información acerca de la integración de Administración de API y Centros de eventos
	-	[Cómo registrar eventos en los centros de eventos de Azure en la administración de API de Azure](api-management-howto-log-event-hubs.md)
	-	[Referencia de entidad del registrador](https://msdn.microsoft.com/library/azure/mt592020.aspx)
	-	[referencia de la directiva log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
	

<!---HONumber=Nov15_HO3-->