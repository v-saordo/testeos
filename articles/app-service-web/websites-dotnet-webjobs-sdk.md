<properties 
	pageTitle="Qué es el SDK de WebJobs de Azure" 
	description="Una introducción al SDK de WebJobs de Azure. Se explica qué es el SDK, se describen escenarios habituales en los que resulta útil y se proporcionan códigos de ejemplo." 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/14/2015" 
	ms.author="tdykstra"/>

# Qué es el SDK de WebJobs de Azure

## <a id="overview"></a>Información general

Este artículo explica qué es el SDK de Trabajos web, revisa algunos escenarios habituales en los que resulta de utilidad y ofrece información general acerca de cómo utilizarlo en su código.

[WebJobs](websites-webjobs-resources.md) es una característica del Servicio de aplicaciones de Azure que le permite ejecutar un programa o script en el mismo contexto que una aplicación web, una aplicación de API o una aplicación móvil. El propósito del [SDK de WebJobs](websites-webjobs-resources.md) es simplificar el código que escriba para las tareas comunes que puede realizar un trabajo web, como procesamiento de imágenes, procesamiento de colas, agregación de RSS, mantenimiento de archivos y envío de correos electrónicos. El SDK de WebJobs tiene características integradas para trabajar con Almacenamiento de Azure y Bus de servicio, para programar tareas y controlar errores, y para muchos otros escenarios comunes. Además, se ha diseñado para ser extensible y hay un [repositorio de código abierto para las extensiones](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).

El SDK de WebJobs incluye los siguientes componentes:

* **Paquetes NuGet**. Los paquetes NuGet que se agregan a un proyecto de la aplicación de consola en Visual Studio proporcionan un marco de trabajo que usa el código mediante la decoración de los métodos con atributos del SDK de WebJobs.
  
* **Panel**. Parte del SDK de WebJobs se incluye en Servicio de aplicaciones de Azure y proporciona una supervisión y un diagnóstico completos para los programas que utilizan paquetes NuGet. No es necesario escribir código para usar estas características de supervisión y diagnóstico.

## <a id="scenarios"></a>Escenarios

Estos son algunos de los escenarios típicos que puede controlar más fácilmente con el SDK de Azure WebJobs:

* Procesamiento de imágenes u otros trabajos de uso intensivo de CPU. Una característica común de los sitios web es la capacidad para cargar imágenes y vídeos. A menudo, necesitamos manipular el contenido después de cargarlo, pero no queremos que el usuario esté esperando mientras realizamos este proceso.

* Procesamiento de colas. Una de las formas más comunes de establecer comunicación entre un front-end web y un servicio back-end consiste en usar colas. Cuando el sitio web necesita que se realicen tareas en él, inserta un mensaje en una cola. Un servicio back-end extrae los mensajes de la cola y realiza el trabajo. Puede usar colas para procesamiento de imágenes: por ejemplo, después de que el usuario cargue una serie de archivos, coloque los nombres de archivo en un mensaje de cola para que el backend los recoja para su procesamiento. También puede usar las colas para mejorar la capacidad de respuesta del sitio. Por ejemplo, en lugar de escribir directamente en una base de datos SQL, escriba en una cola, indique al usuario que ha finalizado y deje que el servicio back-end controle el trabajo de bases de datos relacionales de alta latencia. Para ver un ejemplo del procesamiento de cola con proceso de imagen, consulte el tutorial [Introducción al SDK de WebJobs](websites-dotnet-webjobs-sdk-get-started.md).

* Agregación de fuentes RSS. Si tiene un sitio que mantiene una lista de fuentes RSS, puede extraer todos los artículos de las fuentes en un proceso en segundo plano.

* Mantenimiento de archivos, como el agregado o limpieza de archivos de registro. Puede tener archivos de registro creados a través de varios sitios o para intervalos de tiempo diferentes que desee combinar con el fin de ejecutar trabajos de análisis en ellos. O puede que desee programar una tarea para limpiar archivos de registro antiguos que se ejecute semanalmente.

* Entradas en tablas de Azure. Es posible que tenga archivos almacenados y blobs y que desee analizarlos y almacenar los datos en tablas. Si la función de entrada fuera escribir muchas filas (millones, en algunos casos), el SDK de Trabajos web haría posible la implementación de esta funcionalidad fácilmente. El SDK también ofrece supervisión en tiempo real de los indicadores de progreso, como el número de filas escritas en la tabla.

* Otras tareas con una ejecución prolongada que desee ejecutar en un subproceso en segundo plano, como el [envío de correos electrónicos](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure).

* Las tareas que desea ejecutar en una programación, como realizar una operación de copia de seguridad cada noche.

En muchos de estos escenarios, es posible que desee escalar una aplicación web para que ejecute varias máquinas virtuales, las que ejecutarán varios WebJobs de manera simultánea. En algunos escenarios, esto podría hacer que los mismos datos se procesen varias veces, pero esto no supone un problema cuando se usan los desencadenadores de colas, blobs y bus de servicio integrados del SDK de WebJobs. El SDK garantiza que las funciones se procesarán una sola vez para cada mensaje o blob.

El SDK de WebJobs también facilita el control de los escenarios de control de errores comunes. Puede configurar alertas para enviar notificaciones cuando se produce un error en una función y puede establecer tiempos de espera para que una función se cancele automáticamente si no se completa dentro de un límite de tiempo especificado.

## <a id="code"></a>Ejemplos de código

El código para controlar las tareas comunes que se realizan con Almacenamiento de Azure es sencillo. En el método `Main` de la aplicación de consola crea un objeto `JobHost` que coordina las llamadas a los métodos que escriba. El marco de trabajo del SDK de WebJobs sabe cuándo llamar a los métodos y qué valores de parámetros utilizar en función de los atributos del SDK de WebJobs que use en ellos. El SDK proporciona *desencadenadores* que especifican qué condiciones causan la llamada a la función y *enlazadores* que especifican cómo obtener información dentro y fuera de los parámetros del método.

Por ejemplo, el atributo [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md) genera la llamada a una función cuando se recibe un mensaje en una cola, y si el formato del mensaje es JSON para una matriz de bytes o un tipo personalizado, el mensaje se deserializa automáticamente. El atributo [BlobTrigger](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) desencadena un proceso cada vez que se crea un blob nuevo en una cuenta de Almacenamiento de Azure.

Este es un simple programa que sondea una cola y crea un blob para cada mensaje de la cola recibido:

		public static void Main()
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}

		public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
		{
		    writer.WriteLine(inputText);
		}

El objeto `JobHost` es un contenedor para un conjunto de funciones en segundo plano. El objeto `JobHost` supervisa las funciones, inspecciona los eventos que las desencadenan y ejecuta las funciones cuando se producen los efectos desencadenantes. La llamada a un método `JobHost` permite indicar si desea ejecutar el proceso del contenedor en el subproceso actual o en un subproceso de segundo plano. En el ejemplo, el método `RunAndBlock` ejecuta el proceso de forma continua en el subproceso actual.

Dado que el método `ProcessQueueMessage` de este ejemplo contiene un atributo `QueueTrigger`, el desencadenador de esa función es la recepción de un nuevo mensaje de cola. El objeto `JobHost` inspecciona los mensajes de cola nuevos de la cola especificada ("webjobsqueue" en este ejemplo) y cuando encuentra uno, llama a `ProcessQueueMessage`.

El atributo `QueueTrigger` enlaza el parámetro `inputText` con el valor del mensaje en la cola. Además, el atributo `Blob` enlaza un objeto `TextWriter` con un blob llamado "blobname" en un contenedor llamado "containername".

		public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
		    [Blob("containername/blobname")]TextWriter writer)

A continuación, la función usa estos parámetros para escribir el valor del mensaje de cola en el blob:

		writer.WriteLine(inputText);

Las características de desencadenamiento y enlace del SDK de WebJobs simplifican enormemente el código que se debe escribir. El código de bajo nivel necesario para procesar las colas, los blobs o los archivos, o para iniciar tareas programadas, lo hace automáticamente el marco de trabajo del SDK de WebJobs. Por ejemplo, el marco de trabajo crea colas que no existen aún, abre la cola, lee sus mensajes, los elimina cuando se ha completado el procesamiento, crea contenedores de blobs que no existen aún, escribe en los blobs, etc.

En el ejemplo de código siguiente se muestra una variedad de desencadenadores en un trabajo web: `QueueTrigger`, `FileTrigger`, `WebHookTrigger` y `ErrorTrigger`.

```
    public class Functions
    {
        public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        TextWriter log)
        {
            log.WriteLine(message);
        }

        public static void ProcessFileAndUploadToBlob(
            [FileTrigger(@"import\{name}", "*.*", autoDelete: true)] Stream file,
            [Blob(@"processed/{name}", FileAccess.Write)] Stream output,
            string name,
            TextWriter log)
        {
            output = file;
            file.Close();
            log.WriteLine(string.Format("Processed input file '{0}'!", name));
        }

        [Singleton]
        public static void ProcessWebHookA([WebHookTrigger] string body, TextWriter log)
        {
            log.WriteLine(string.Format("WebHookA invoked! Body: {0}", body));
        }

        public static void ProcessGitHubWebHook([WebHookTrigger] string body, TextWriter log)
        {
            dynamic issueEvent = JObject.Parse(body);
            log.WriteLine(string.Format("GitHub WebHook invoked! ('{0}', '{1}')",
                issueEvent.issue.title, issueEvent.action));
        }

        public static void ErrorMonitor(
        [ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
        [SendGrid(
            To = "admin@emailaddress.com",
            Subject = "Error!")]
         SendGridMessage message)
        {
            // log last 5 detailed errors to the Dashboard
            log.WriteLine(filter.GetDetailedMessage(5));
            message.Text = filter.GetDetailedMessage(1);
        }
    }
```

## <a id="schedule"></a> Programación

El atributo `TimerTrigger` permite desencadenar funciones para ejecutarlas en una programación. Puede programar un trabajo web como un conjunto a través de Azure o de funciones individuales programadas de un trabajo web mediante la utilización del SDK de WebJobs `TimerTrigger`. A continuación se presenta un ejemplo de código.

```
public class Functions
{
    public static void ProcessTimer([TimerTrigger("*/15 * * * * *", RunOnStartup = true)]
    TimerInfo info, [Queue("queue")] out string message)
    {
        message = info.FormatNextOccurrences(1);
    }
}
```

Para obtener más código de ejemplo, consulte [TimerSamples.cs](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/ExtensionsSample/Samples/TimerSamples.cs) en el repositorio azure-webjobs-sdk-extensions de GitHub.com.

## Extensibilidad

No está limitado a la funcionalidad integrada; el SDK de WebJobs permite escribir desencadenadores y enlazadores personalizados. Por ejemplo, puede escribir desencadenadores de eventos de caché y programaciones periódicas. Un [repositorio de código abierto](https://github.com/Azure/azure-webjobs-sdk-extensions) contiene un [guía detallada de la extensibilidad del SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) y código de ejemplo que le ayudarán a escribir sus propios desencadenadores y enlazadores.

## <a id="workerrole"></a>Uso del SDK de WebJobs fuera de WebJobs

Un programa que utiliza el SDK de WebJobs es una aplicación de consola estándar y puede ejecutarse en cualquier lugar, no necesariamente como un trabajo web. Puede probar el programa localmente en el equipo de desarrollo y, en producción, puede ejecutarlo en un rol de trabajo de Servicio en la nube o en un servicio de Windows si prefiere uno de estos entornos.

Sin embargo, el panel solo está disponible como una extensión para una aplicación web del Servicio de aplicaciones de Azure. Si desea ejecutarlo fuera de un trabajo web y seguir utilizando el Panel, puede configurar una aplicación web para que utilice la misma cuenta de almacenamiento a la que hace referencia la cadena de conexión del Panel del SDK de WebJobs, y el Panel de WebJobs de esa aplicación web mostrará entonces información acerca de la ejecución de la función de su programa que se está ejecutando fuera. Puede obtener acceso al Panel utilizando la dirección URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions. Para obtener más información, consulte [Cómo obtener un panel para desarrollo local con el SDK de WebJobs](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx), pero tenga en cuenta que la entrada del blog muestra un nombre de cadena de conexión antiguo.

## <a id="nostorage"></a>Características del panel

El SDK de WebJobs proporciona varias ventajas, incluso aunque no utilice los desencadenadores o enlazadores del SDK de WebJobs:

* Puede invocar funciones desde el Panel.
* Puede reproducir funciones desde el Panel.
* Puede ver los registros en el Panel, vinculados al trabajo web específico (registros de aplicación, escritos usando Console.Out, Console.Error, Trace, etc.) o vinculados a la invocación de función determinada que los generó (registros escritos usando un objeto `TextWriter` que el SDK pasa a la función como parámetro). 

Para obtener más información, consulte [Invocación de una función manualmente](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) y [Escritura de registros](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)

## <a id="nextsteps"></a>Pasos siguientes

Para obtener información acerca del SDK de WebJobs, consulte [Recursos recomendados de WebJobs de Azure](http://go.microsoft.com/fwlink/?linkid=390226).

Para obtener información sobre las últimas mejoras del SDK de WebJobs, consulte las [notas de la versión](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes).
 

<!---HONumber=AcomDC_1217_2015-->