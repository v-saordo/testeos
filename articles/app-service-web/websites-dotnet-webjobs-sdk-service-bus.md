<properties 
	pageTitle="Uso del Bus de servicio de Azure con el SDK de WebJobs" 
	description="Aprenda a usar los temas y las colas del Bus de servicio de Azure con el SDK de WebJobs." 
	services="app-service\web, service-bus" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/14/2015" 
	ms.author="tdykstra"/>

# Uso del Bus de servicio de Azure con el SDK de WebJobs

## Información general

Esta guía proporciona muestras de código de C# que muestran la manera de desencadenar un proceso cuando se crea o actualiza un blob de Azure. Las muestras de código usan el [SDK de WebJobs](websites-dotnet-webjobs-sdk.md) versión 1.x.

En la guía se supone que sabe [cómo crear un proyecto de trabajos web en Visual Studio con cadenas de conexión que señalan a su cuenta de almacenamiento](websites-dotnet-webjobs-sdk-get-started.md).

Los fragmentos de código solo muestran funciones, no el código que crea el objeto `JobHost` como en este ejemplo:

```
public class Program
{
   public static void Main()
   {
      JobHostConfiguration config = new JobHostConfiguration();
      config.UseServiceBus();
      JobHost host = new JobHost(config);
      host.RunAndBlock();
   }
}
```

Puede encontrar un [ejemplo de código de Bus de servicio completo](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Program.cs) en el repositorio azure-webjobs-sdk-samples en GitHub.com.

## <a id="prerequisites"></a> Requisitos previos

Para trabajar con el Bus de servicio debe instalar el paquete NuGet [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) además de los otros paquetes del SDK de trabajos web.

También debe establecer la cadena de conexión AzureWebJobsServiceBus además de las cadenas de conexión de almacenamiento. Esto puede hacerlo en la sección `connectionStrings` del archivo App.config, como se muestra en el ejemplo siguiente:

		<connectionStrings>
		    <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
		    <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
		    <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
		</connectionStrings>

Para ver un proyecto de muestra que incluye la configuración de la cadena de conexión del Bus de servicio App.config, consulte [Ejemplo de Bus de servicio](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus).

Las cadenas de conexión también se puede definir en el entorno de tiempo de ejecución de Azure, con lo que se reemplaza la configuración de App.config cuando WebJob se ejecuta en Azure. Si desea obtener más información, consulte [Introducción al SDK de WebJobs](websites-dotnet-webjobs-sdk-get-started.md#configure-the-web-app-to-use-your-azure-sql-database-and-storage-account).

## <a id="trigger"></a> Cómo desencadenar una función cuando se recibe un mensaje de la cola de Bus de servicio

Para escribir una función que el SDK de WebJobs llama cuando se recibe un mensaje en cola, use el atributo`ServiceBusTrigger`. El constructor de atributo toma un parámetro de cadena que especifica el nombre de la cola que se va a sondear.

### Cómo funciona ServicebusTrigger

El SDK de recibe un mensaje en el modo `PeekLock` y llama a `Complete` en el mensaje si la función finaliza correctamente, o llama a `Abandon` si se produce un error en la función. Si la ejecución de la función dura más que el tiempo de espera de `PeekLock`, el bloqueo se renovará automáticamente.

El Bus de servicio hace su propio control de la cola de daños, para que no lo controle el SDK de WebJobs ni sea configurable en él.

### Mensajes en cola de cadena

La siguiente muestra de código lee un mensaje en cola que contiene una cadena y escribe la cadena en el panel del SDK de WebJobs.

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**Nota:** si va a crear los mensajes en cola en una aplicación que no usa el SDK de WebJobs, asegúrese de establecer [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) en "text/plain".

### Mensaje en cola de POCO

El SDK deserializará automáticamente un mensaje en cola que contenga JSON para un tipo POCO [(objeto CLR antiguo sin formato)](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object). El siguiente ejemplo de código lee un mensaje en cola que contiene un objeto `BlobInformation` que tiene una propiedad `BlobName`:

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Para los ejemplos de código que muestran cómo utilizar las propiedades de POCO para que funcionen con blobs y tablas en la misma función, vea la [versión para colas de almacenamiento de este artículo](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs).

Si el código que crea el mensaje en cola no usa el SDK de WebJobs, use código similar al del ejemplo siguiente:

		var client = QueueClient.CreateFromConnectionString(ConfigurationManager.ConnectionStrings["AzureWebJobsServiceBus"].ConnectionString, "blobadded");
		BlobInformation blobInformation = new BlobInformation () ;
		var message = new BrokeredMessage(blobInformation);
		client.Send(message);

### Los tipos ServiceBusTrigger funcionan con

Además de los tipos `string` y POCO, puede usar el atributo `ServiceBusTrigger` con una matriz de bytes o un objeto `BrokeredMessage`.

## <a id="create"></a> Creación de mensajes en cola del Bus de servicio

Para escribir una función que cree un nuevo mensaje en cola, use el atributo `ServiceBus` y pase el nombre de la cola al constructor de atributos.


### Crear un mensaje en cola único en una función no asincrónica

La siguiente muestra de código utiliza un parámetro de salida para crear un nuevo mensaje en la cola llamado "outputqueue" con el mismo contenido que el mensaje recibido en la cola llamada "inputqueue".

		public static void CreateQueueMessage(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] out string outputQueueMessage)
		{
		    outputQueueMessage = queueMessage;
		}

El parámetro de salida para crear un mensaje en cola único puede ser cualquiera de los siguientes tipos:

* `string`
* `byte[]`
* `BrokeredMessage`
* Un tipo POCO serializable que define Seriado automáticamente como JSON.

Para los parámetros de tipo POCO, siempre se crea un mensaje en cola cuando finaliza la función; si el parámetro es null, el SDK crea un mensaje en cola que devolverá null cuando se reciba y se deserialice el mensaje. Para los demás tipos, si el parámetro es null, no se creará ningún mensaje en cola.

### Crear varios mensajes en cola o en funciones asincrónicas

Para crear varios mensajes, utilice el atributo `ServiceBus` con `ICollector<T>` o `IAsyncCollector<T>`, como se muestra en el ejemplo de código siguiente:

		public static void CreateQueueMessages(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Cada mensaje en cola se crea inmediatamente cuando se llama al método `Add`.

## <a id="topics"></a>Trabajo con temas del Bus de servicio

Para escribir una función a la que llama el SDK cuando se recibe un mensaje en un tema del Bus de servicio, use el atributo `ServiceBusTrigger` con el constructor que toma el nombre del tema y el nombre de la suscripción, como se muestra en el siguiente código de ejemplo:

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

Para crear un mensaje sobre un tema, use el atributo `ServiceBus` con un nombre de un tema de la misma manera que lo usa con un nombre de cola.

## Características agregadas en la versión 1.1

Las siguientes características se agregaron en la versión 1.1:

* Se permite la personalización profunda del procesamiento de mensajes a través de `ServiceBusConfiguration.MessagingProvider`.
* `MessagingProvider` admite la personalización de `MessagingFactory` y `NamespaceManager` del Bus de servicio.
* Un patrón de estrategia `MessageProcessor` le permite especificar un procesador por cola/tema.
* La simultaneidad del procesamiento de mensajes se admite de manera predeterminada. 
* La personalización sencilla de `OnMessageOptions` a través de `ServiceBusConfiguration.MessageOptions`.
* Se permite especificar [AccessRights](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Functions.cs#L71) en `ServiceBusTriggerAttribute`/`ServiceBusAttribute` (en los escenarios donde es probable que no tenga derechos de administración). 

## <a id="queues"></a>Temas relacionados tratados en el artículo de procedimientos de las colas de almacenamiento

Para obtener información acerca de los escenarios del SDK de trabajos web no específicos del Bus de servicio, vea [Uso del almacenamiento de colas de Azure con el SDK de WebJobs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

Entre los temas tratados en este artículo se incluyen los siguientes:

* Funciones asincrónicas
* Varias instancias
* Apagado correcto
* Utilizar atributos del SDK de WebJobs en el cuerpo de una función
* Establecer las cadenas de conexión del SDK en el código.
* Establecer valores para los parámetros del constructor del SDK de WebJobs en el código
* Desencadenar una función manualmente
* Escribir registros

## <a id="nextsteps"></a> Pasos siguientes

En esta guía se han proporcionado ejemplos de código que muestran cómo controlar los escenarios comunes para trabajar con el Bus de servicio de Azure. Para obtener más información acerca de cómo usar el SDK de WebJobs y WebJobs de Azure, consulte [Recursos de WebJobs de Azure recomendados](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=AcomDC_0107_2016-->