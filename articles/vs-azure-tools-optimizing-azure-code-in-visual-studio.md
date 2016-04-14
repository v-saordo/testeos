<properties
   pageTitle="Optimización del código de Azure en Visual Studio | Microsoft Azure"
   description="Obtenga información sobre cómo las herramientas de optimización del código de Azure en Visual Studio ayudan a que el código sea más sólido y tenga un mejor rendimiento."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="01/30/2016"
   ms.author="tarcher" />

# Optimización del código de Azure

Al programar aplicaciones que usan Microsoft Azure, debe seguir algunas prácticas de codificación para evitar problemas en la escalabilidad, el comportamiento y el rendimiento de la aplicación en un entorno en la nube. Microsoft proporciona una herramienta de análisis de código de Azure que reconoce e identifica varios de los problemas que se suelen encontrar y ayuda a resolverlos. Puede descargar la herramienta en Visual Studio a través de NuGet.

## Reglas de análisis de código de Azure

La herramienta de análisis de código de Azure usa la siguientes reglas para marcar automáticamente su código de Azure cuando encuentra problemas conocidos que influyen en el rendimiento. Los problemas detectados aparecen como advertencias o errores del compilador. Con frecuencia, las correcciones de código o sugerencias para resolver la advertencia o el error se proporcionan con un icono de bombilla.

## Evite usar el modo de estado de sesión predeterminado (en proceso)

### ID

AP0000

### Descripción

Si usa el modo de estado de sesión (en proceso) de forma predeterminada para las aplicaciones de nube, puede perder el estado de sesión.

Comparta sus ideas y comentarios en [Comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

De forma predeterminada, el modo de estado de sesión especificado en el archivo web.config es en proceso. Además, si no hay ninguna entrada especificada en el archivo de configuración, el modo de estado de sesión pasa de forma predeterminada a en proceso. El modo en proceso almacena el estado de sesión en memoria en el servidor web. Cuando se reinicia una instancia o se usa una nueva para el equilibrio de carga o para el soporte contra conmutaciones por error, no se guarda el estado de sesión almacenado en memoria en el servidor web. Esta situación impide que la aplicación sea escalable en la nube.

El estado de sesión ASP.NET es compatible con distintas opciones de almacenamiento para los datos de estado de sesión: InProc, StateServer, SQLServer, personalizado y desactivado. Se recomienda usar el modo personalizado para hospedar datos en un almacén de estado de sesión externo, como el [Proveedor de estado de sesión de Azure para Redis](http://go.microsoft.com/fwlink/?LinkId=401521).

### Solución

Una solución recomendada es almacenar el estado de sesión en un servicio de caché administrado. Aprenda a usar el [Proveedor de estado de sesión de Azure para Redis](http://go.microsoft.com/fwlink/?LinkId=401521) para almacenar el estado de sesión. También puede almacenar el estado de sesión en otros lugares para garantizar que su aplicación sea escalable en la nube. Para obtener más información acerca de soluciones alternativas, lea [Modos de estado de sesión](https://msdn.microsoft.com/library/ms178586).

## El método de ejecución no debe ser asincrónico

### ID

AP1000

### Descripción

Cree métodos asincrónicos (como [await](https://msdn.microsoft.com/library/hh156528.aspx)) fuera del método [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) y luego llame a los métodos asincrónicos desde [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Si se declara el método [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) como asincrónico, el rol de trabajo entra en un bucle de reinicio.

Comparta sus ideas y comentarios en [Comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

Si se llama a métodos asincrónicos desde el método [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx), el tiempo de ejecución del servicio en la nube recicla el rol de trabajo. Cuando se inicia un rol de trabajo, toda la ejecución del programa tiene lugar dentro del método [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Cuando se sale del método [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx), el rol de trabajo se reinicia. Cuando el tiempo de ejecución del rol de trabajo alcanza el método asincrónico, distribuye todas las operaciones posteriores al método asincrónico y luego regresa. Esto hace que el rol de trabajo salga del método [[[[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) y se reinicie. En la siguiente iteración de la ejecución, el rol de trabajo vuelve a alcanzar el método asincrónico y se reinicia, por lo que el rol de trabajo también se vuelve a reciclar.

### Solución

Coloque todas las operaciones asincrónicas fuera del método [Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx). Luego, llame al método asincrónico refactorizado desde el método [[Run()](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx)](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx), como RunAsync().wait. La herramienta de análisis de código de Azure puede ayudarle a arreglar este problema.

El siguiente fragmento de código muestra la corrección de código para este problema:

```
public override void Run()
{
    RunAsync().Wait();
}

public async Task RunAsync()
{
    //Asynchronous operations code logic
    // This is a sample worker implementation. Replace with your logic.

    Trace.TraceInformation("WorkerRole1 entry point called");

    HttpClient client = new HttpClient();

    Task<string> urlString = client.GetStringAsync("http://msdn.microsoft.com");

    while (true)
    {
        Thread.Sleep(10000);
        Trace.TraceInformation("Working");

        string stream = await urlString;
    }

}
```

## Use la autenticación con firma de acceso compartido de Bus de servicio

### ID

AP2000

### Descripción

Use SAS (firma de acceso compartido) para la autenticación. El servicio de control de acceso (ACS) se está desusando para la autenticación de Bus de servicio.

Comparta sus ideas y comentarios en [Comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

Para mejorar la seguridad, Azure Active Directory está sustituyendo la autenticación ACS por la autenticación SAS. Consulte el blog [Azure Active Directory is the future of ACS](http://blogs.technet.com/b/ad/archive/2013/06/22/azure-active-directory-is-the-future-of-acs.aspx) (Azure Active Directory es el futuro de ACS) para obtener información sobre el plan de transición.

### Solución

Use la autenticación SAS en sus aplicaciones. El ejemplo siguiente muestra cómo usar un token SAS para acceder a un espacio de nombres o entidad de Bus de servicio.

```
MessagingFactory listenMF = MessagingFactory.Create(endpoints, new StaticSASTokenProvider(subscriptionToken));
SubscriptionClient sc = listenMF.CreateSubscriptionClient(topicPath, subscriptionName);
BrokeredMessage receivedMessage = sc.Receive();
```

Para obtener más información, consulte los temas siguientes.

- Para obtener información general, consulte [Autenticación con firma de acceso compartido en Bus de servicio](https://msdn.microsoft.com/library/dn170477.aspx).

- [Usar la autenticación con firma de acceso compartido con el Bus de servicio](https://msdn.microsoft.com/library/dn205161.aspx)

- Para ver un proyecto de ejemplo, consulte [Uso de la autenticación con firma de acceso compartido (SAS) con suscripciones de Bus de servicio](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c).

## Considere usar el método OnMessage para evitar un "bucle de recepción"

### ID

AP2002

### Descripción

Para evitar entrar en un "bucle de recepción", la mejor solución para recibir mensajes es llamar al método **OnMessage** en lugar de llamar al método **Receive**. Sin embargo, si debe usar el método **Receive** y especifica un tiempo de espera del servidor no predeterminado, asegúrese de que el tiempo de espera del servidor es superior a un minuto.

Comparta sus ideas y comentarios en [Comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

Al llamar a **OnMessage**, el cliente inicia un suministro de mensajes interno que sondea constantemente la cola o la suscripción. Este suministro de mensajes contiene un bucle infinito que emite una llamada para recibir mensajes. Si la llamada agota el tiempo de espera, emite una llamada nueva. El intervalo de tiempo de espera está determinado por el valor de la propiedad [OperationTimeout](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx) de [MessagingFactory](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagingfactory.aspx) que se usa.

La ventaja de usar **OnMessage** en comparación con **Receive** es que los usuarios no tienen que sondear en busca de mensajes, controlar excepciones, procesar varios mensajes en paralelo ni completar los mensajes manualmente.

Si llama a **Receive** sin usar su valor predeterminado, asegúrese de que el valor *ServerWaitTime* es superior a un minuto. La configuración de *ServerWaitTime* en un valor superior a un minuto evita que el servidor agote el tiempo de espera antes de que el mensaje se reciba completamente.

### Solución

Consulte los ejemplos de código siguientes para usos recomendados. Para obtener más detalles, consulte [Método QueueClient.OnMessage (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.onmessage.aspx) y [Método QueueClient.Receive (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.receive.aspx).

Para mejorar el rendimiento de la infraestructura de mensajería de Azure, consulte el modelo de diseño [Manual de mensajería asincrónica](https://msdn.microsoft.com/library/dn589781.aspx).

El siguiente es un ejemplo del uso de **OnMessage** para recibir mensajes.

```
void ReceiveMessages()
{
    // Initialize message pump options.
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = true; // Indicates if the message-pump should call complete on messages after the callback has completed processing.
    options.MaxConcurrentCalls = 1; // Indicates the maximum number of concurrent calls to the callback the pump should initiate.
    options.ExceptionReceived += LogErrors; // Enables you to get notified of any errors encountered by the message pump.

    // Start receiving messages.
    QueueClient client = QueueClient.Create("myQueue");
    client.OnMessage((receivedMessage) => // Initiates the message pump and callback is invoked for each message that is recieved, calling close on the client will stop the pump.
    {
        // Process the message.
    }, options);
    Console.WriteLine("Press any key to exit.");
    Console.ReadKey();
```

El siguiente es un ejemplo del uso de **Receive** con el tiempo de espera del servidor predeterminado.

```
string connectionString =  
CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =  
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

while (true)  
{   
   BrokeredMessage message = Client.Receive();
   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
```

El siguiente es un ejemplo del uso de **Receive** con un tiempo de espera del servidor no predeterminado.

```
while (true)  
{   
   BrokeredMessage message = Client.Receive(new TimeSpan(0,1,0));

   if (message != null)
   {
      try  
      {
         Console.WriteLine("Body: " + message.GetBody<string>());
         Console.WriteLine("MessageID: " + message.MessageId);
         Console.WriteLine("Test Property: " +  
            message.Properties["TestProperty"]);

         // Remove message from queue
         message.Complete();
      }

      catch (Exception)
      {
         // Indicate a problem, unlock message in queue
         message.Abandon();
      }
   }
}
```
## Considere usar los métodos asincrónicos de Bus de servicio

### ID

AP2003

### Descripción

Usar métodos asincrónicos de Bus de servicio para mejorar el rendimiento de la mensajería asíncrona.

Comparta sus ideas y comentarios en [Comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

Usar métodos asincrónicos permite la simultaneidad de programas de aplicación porque ejecutar cada llamada no bloquea el subproceso principal. Cuando se usan los métodos de mensajería de Bus de servicio, realizar una operación (enviar, recibir, eliminar, etc.) lleva tiempo. Este tiempo incluye el procesamiento de la operación por el servicio Bus de servicio, además de la latencia de la solicitud y la respuesta. Para aumentar el número de operaciones por tiempo, las operaciones deberán ejecutarse simultáneamente. Para obtener más información, consulte [Prácticas recomendadas para mejorar el rendimiento mediante la mensajería asíncrona de Bus de servicio](https://msdn.microsoft.com/library/azure/hh528527.aspx).

### Solución

Consulte [Clase QueueClient (Microsoft.ServiceBus.Messaging)](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.aspx) para obtener información sobre cómo usar el método asincrónico recomendado.

Para mejorar el rendimiento de la infraestructura de mensajería de Azure, consulte el modelo de diseño [Manual de mensajería asincrónica](https://msdn.microsoft.com/library/dn589781.aspx).

## Considere crear particiones de temas y colas de Bus de servicio

### ID

AP2004

### Descripción

Partición de temas y colas de Bus de servicio para mejorar el rendimiento de la mensajería de Bus de servicio.

Comparta sus ideas y comentarios en [Comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

La partición de temas y colas de Bus de servicio aumenta el rendimiento y la disponibilidad de servicios porque el rendimiento general de una cola o tema particionado ya no está limitado por el rendimiento de un solo agente de mensajes o almacén de mensajería. Además, una interrupción temporal de un almacén de mensajería no hace que una cola o tema particionado deje de estar disponible. Para obtener más información, consulte [Particionamiento de entidades de mensajería](https://msdn.microsoft.com/library/azure/dn520246.aspx).

### Solución

El fragmento de código siguiente muestra cómo crear particiones de las entidades de mensajería.

```
// Create partitioned topic.
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Para obtener más información, consulte [Temas y colas de Bus de servicio con particiones | Blog de Microsoft Azure](https://azure.microsoft.com/blog/2013/10/29/partitioned-service-bus-queues-and-topics/) y consulte el ejemplo de [Cola con particiones de Bus de servicio de Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Service-Bus-Partitioned-7dfd3f1f).

## No establezca SharedAccessStartTime

### ID

AP3001

### Descripción

Debe evitar el uso de SharedAccessStartTime establecido en la hora actual para iniciar inmediatamente la directiva de acceso compartido. Solo es necesario establecer esta propiedad si quiere iniciar la directiva de acceso compartido en un momento posterior.

Comparta sus ideas y comentarios en [Comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

La sincronización de relojes provoca una ligera diferencia de tiempo entre los centros de datos. Por ejemplo, puede pensar que establecer el tiempo de inicio de una directiva SAS de almacenamiento en el tiempo actual mediante DateTime.Now o un método similar hará que la directiva SAS surta efecto inmediatamente. Sin embargo, la ligera diferencia de tiempo entre los distintos centros de datos puede causar problemas, ya que las horas de algunos centros de datos pueden presentar un retraso con respecto al tiempo de inicio, mientras que otros pueden ir adelantados. Como resultado, la directiva SAS puede expirar rápidamente (o incluso inmediatamente) si se establece un tiempo de vida de la directiva muy corto.

Para obtener instrucciones sobre cómo usar la firma de acceso compartido en el Almacenamiento de Azure, consulte [Introducción a SAS (firma de acceso compartido) de tabla, SAS de fila y actualización a SAS de blob - Blog del equipo de Almacenamiento de Azure - Página principal del sitio - Blogs de MSDN](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### Solución

Quite la instrucción que establece el tiempo de inicio de la directiva de acceso compartido. La herramienta de análisis de código de Azure proporciona una corrección para este problema. Para obtener más información sobre la administración de seguridad, consulte el modelo de diseño [Patrón de clave valet](https://msdn.microsoft.com/library/dn568102.aspx).

El siguiente fragmento de código muestra la corrección de código para este problema.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

## El tiempo de expiración de la directiva de acceso compartido debe ser superior a cinco minutos.

### ID

AP3002

### Descripción

Puede haber hasta cinco minutos de diferencia entre los relojes de los centros de datos en diferentes ubicaciones debido a una condición conocida como "desplazamiento del reloj". Para evitar que el token de la directiva de SAS expire antes de lo planeado, establezca el tiempo de expiración en un valor superior a cinco minutos.

Comparta sus ideas y comentarios en [Comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

Los centros de datos en diferentes ubicaciones de todo el mundo se sincronizan por una señal de reloj. Dado que la señal de reloj tarda en viajar a ubicaciones diferentes, puede haber una variación de tiempo entre los centros de datos en distintas ubicaciones geográficas aunque supuestamente todo está sincronizado. Esta diferencia de tiempo puede afectar el intervalo expiración y a la hora de inicio de la directiva de acceso compartido. Por lo tanto, para asegurarse de que la directiva de acceso compartido surte efecto inmediatamente, no especifique ninguna hora de inicio. Además, asegúrese de que el tiempo de expiración es superior a 5 minutos para evitar que expire muy pronto.

Para obtener más información acerca de cómo usar la firma de acceso compartido en el almacenamiento de Azure, consulte [Introducción a SAS (firma de acceso compartido) de tabla, SAS de fila y actualización a SAS de blob - Blog del equipo de Almacenamiento de Azure - Página principal del sitio - Blogs de MSDN](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx).

### Solución

Para obtener más información sobre la administración de seguridad, consulte el modelo de diseño [Patrón de clave valet](https://msdn.microsoft.com/library/dn568102.aspx).

El siguiente es un ejemplo de no especificar una hora de inicio a la directiva de acceso compartido.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
   SharedAccessExpiryTime = DateTime.UtcNow.AddHours(10),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

El siguiente es un ejemplo de especificar una hora de inicio a la directiva de acceso compartido con una duración de expiración de la directiva superior a los cinco minutos.

```
// The shared access policy provides  
// read/write access to the container for 10 hours.
blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
{
   // To ensure SAS is valid immediately, don’t set start time.
   // This way, you can avoid failures caused by small clock differences.
  SharedAccessStartTime = new DateTime(2014,1,20),   
 SharedAccessExpiryTime = new DateTime(2014, 1, 21),
   Permissions = SharedAccessBlobPermissions.Write |
      SharedAccessBlobPermissions.Read
});
```

Para obtener más información, consulte [Crear y usar una firma de acceso compartido](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## Use CloudConfigurationManager

### ID

AP4000

### Descripción

El uso de la clase [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) para proyectos como Sitio web de Azure y Servicios móviles de Azure no presentará problemas de tiempo de ejecución. Sin embargo, se recomienda usar Cloud[ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager(v=vs.110).aspx) como una manera unificada de administración de configuraciones para todas las aplicaciones de nube de Azure.

Comparta sus ideas y comentarios en [Comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

CloudConfigurationManager lee el archivo de configuración adecuado para el entorno de aplicación.

[CloudConfigurationManager](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx)

### Solución

Refactorice el código para que use la [Clase CloudConfigurationManager+](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx). La herramienta de análisis de código de Azure proporciona una corrección para este problema.

El siguiente fragmento de código muestra la corrección de código para este problema. Sustituya

`var settings = ConfigurationManager.AppSettings["mySettings"];`

por

`var settings = CloudConfigurationManager.GetSetting("mySettings");`

Este es un ejemplo de cómo almacenar la configuración en un archivo App.config o Web.config. Agregue la configuración a la sección appSettings del archivo de configuración. El siguiente es el archivo Web.config para el ejemplo de código anterior.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="mySettings" value="[put_your_setting_here]"/>
  </appSettings>  
```

## Evite usar cadenas de conexión codificadas de forma rígida

### ID

AP4001

### Descripción

Si usa las cadenas de conexión codificadas de forma rígida y necesita actualizarlas más adelante, tendrá que realizar cambios en el código fuente y volver a compilar la aplicación. Sin embargo, si almacena las cadenas de conexión en un archivo de configuración, puede cambiarlas más adelante simplemente actualizando el archivo de configuración.

Comparta sus ideas y comentarios en [Comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

Codificar las cadenas de conexión de forma rígida es una práctica incorrecta porque presenta problemas cuando las cadenas de conexión deben cambiarse rápidamente. Además, si el proyecto debe protegerse en el control de código fuente, las cadenas de conexión codificadas de forma rígida incorporan vulnerabilidades de seguridad, ya que las cadenas se pueden ver en el código fuente.

### Solución

Almacene las cadenas de conexión en los archivos de configuración o entornos de Azure.

- Para aplicaciones independientes, use app.config para almacenar la configuración de las cadenas de conexión.

- Para aplicaciones web hospedadas en IIS, use web.config para almacenar cadenas de conexión.

- Para aplicaciones ASP.NET vNext, use configuration.json para almacenar cadenas de conexión.

Para obtener información sobre el uso de archivos de configuración como web.config o app.config, consulte [ASP.NET Web Configuration Guidelines](https://msdn.microsoft.com/library/vstudio/ff400235(v=vs.100).aspx). Para obtener información sobre cómo funcionan las variables de entorno de Azure, consulte [Sitios web Microsoft Azure: cómo funcionan las cadenas de aplicaciones y las cadenas de conexión](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Para obtener información sobre cómo almacenar la cadena de conexión en el control de código fuente, consulte [Evitar colocar información confidencial, como cadenas de conexión, en archivos que se almacenan en el repositorio de código fuente.](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control)

## Uso del archivo de configuración de diagnóstico

### ID

AP5000

### Descripción

En lugar de configurar opciones de diagnóstico en el código como mediante la API de programación de Microsoft.WindowsAzure.Diagnostics, debe configurar las opciones de diagnóstico en el archivo diagnostics.wadcfg. (O bien, diagnostics.wadcfgx si usa Azure SDK 2.5). Al hacer eso, puede cambiar la configuración de diagnóstico sin tener que volver a compilar el código.

Comparta sus ideas y comentarios en [Comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

Antes de Azure SDK 2.5 (que usa el diagnóstico de Azure 1.3), Diagnósticos de Azure (WAD) puede configurarse mediante varios métodos diferentes: puede agregarlos al blob de configuración en el almacenamiento, o usar código imperativo, configuración declarativa o la configuración predeterminada. Sin embargo, la mejor manera de configurar los diagnósticos es usar un archivo de configuración XML (diagnostics.wadcfg o diagnositcs.wadcfgx para 2.5 SDK y versiones posteriores) en el proyecto de aplicación. En este enfoque, el archivo diagnostics.wadcfg define completamente la configuración y se puede actualizar y volver a implementar según se quiera. Si combina el uso del archivo de configuración diagnostics.wadcfg con los métodos de establecimiento de configuraciones mediante programación con las clases [DiagnosticMonitor](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.diagnosticmonitor.aspx) o [RoleInstanceDiagnosticManager](https://msdn.microsoft.com/library/microsoft.windowsazure.diagnostics.management.roleinstancediagnosticmanager.aspx) puede crearle confusión. Consulte [Inicializar o cambiar la configuración de Diagnósticos de Azure](https://msdn.microsoft.com/library/azure/hh411537.aspx) para obtener más información.

A partir de WAD 1.3 (incluido con Azure SDK 2.5), ya no es posible usar código para configurar los diagnósticos. Como resultado, solo puede proporcionar la configuración al aplicar o actualizar la extensión de diagnósticos.

### Solución

Use el diseñador de configuración de diagnósticos para mover la configuración de diagnóstico al archivo de configuración de diagnósticos (diagnositcs.wadcfg o diagnositcs.wadcfgx para SDK 2.5 y versiones posteriores). También se recomienda que instale [Azure SDK 2.5](http://go.microsoft.com/fwlink/?LinkId=513188) y use la característica de diagnóstico más reciente.

1. En el menú contextual del rol que quiere configurar, elija Propiedades, y luego elija la pestaña Configuración.

1. En la sección **Diagnósticos**, asegúrese de que la casilla **Habilitar diagnósticos** está seleccionada.

1. Elija el botón **Configurar**.

  ![Acceso a la opción de habilitar diagnósticos](./media/vs-azure-tools-optimizing-azure-code-in-visual-studio/IC796660.png)

  Consulte [Configuración de los diagnósticos para los servicios en la nube y las máquinas virtuales de Azure](vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md) para obtener más información.


## Evite declarar objetos DbContext como estáticos

### ID

AP6000

### Descripción

Para ahorrar memoria, evite declarar objetos DBContext como estáticos.

Comparta sus ideas y comentarios en [Comentarios de análisis de código de Azure](http://go.microsoft.com/fwlink/?LinkId=403771).

### Motivo

Los objetos DBContext contienen los resultados de consulta de cada llamada. Los objetos DBContext estáticos no se eliminan hasta que se descarga el dominio de aplicación. Por lo tanto, un objeto DBContext estático puede consumir grandes cantidades de memoria.

### Solución

Declare DBContext como un campo de instancia variable o no estático, úselo para una tarea y, luego, deje que se elimine después de usarlo.

El ejemplo siguiente de clase de controlador MVC muestra cómo usar el objeto DBContext.

```
public class BlogsController : Controller
    {
        //BloggingContext is a subclass to DbContext        
        private BloggingContext db = new BloggingContext();
        // GET: Blogs
        public ActionResult Index()
        {
            //business logics…
            return View();
        }
        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                db.Dispose();
            }
            base.Dispose(disposing);
        }
    }
```

## Pasos siguientes

Para obtener más información sobre cómo optimizar y solucionar problemas de Aplicaciones de Azure, consulte [Solución de problemas de una aplicación web en el Servicio de aplicaciones de Azure con Visual Studio](/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

<!---HONumber=AcomDC_0204_2016-->