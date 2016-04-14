<properties
    pageTitle="Uso de colas de Bus de servicio (.NET) | Microsoft Azure"
    description="Obtenga información acerca de cómo usar las colas del Bus de servicio en Azure. Ejemplos de código escritos en C# con la API de .NET."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="01/26/2016"
    ms.author="sethm"/>

# Utilización de las colas del Bus de servicio

[AZURE.INCLUDE [servicio de bus de selector de colas](../../includes/service-bus-selector-queues.md)]

Este artículo describe cómo usar las colas del Bus de servicio. Los ejemplos están escritos en C# y utilizan la API .NET. Entre los escenarios que abarca se incluyen la creación de colas y el envío y recepción de mensajes. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes](#next-steps).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## Agregar el paquete NuGet del bus de servicio

El [paquete**NuGet** del bus de servicio](https://www.nuget.org/packages/WindowsAzure.ServiceBus) es la forma más sencilla de obtener la API del bus de servicio y configurar una aplicación con todas las dependencias del bus de servicio. La extensión NuGet Visual Studio facilita la instalación y la actualización de las bibliotecas y las herramientas en Visual Studio y Visual Studio Express. El paquete NuGet del bus de servicio es la forma más sencilla de obtener la API del bus de servicio y configurar su aplicación con todas las dependencias del bus de servicio.

Realice los pasos siguientes para instalar el paquete NuGet en su aplicación:

1.  En el Explorador de soluciones, haga clic con el botón secundario en **References** y, a continuación, en **Manage NuGet Packages**.
2.  Busque "Bus de servicio" y seleccione el elemento **Bus de servicio de Microsoft Azure**. Haga clic en **Instalar** para completar la instalación y, a continuación, cierre este cuadro de diálogo.

    ![][7]

De este modo ya estará listo para escribir código para el bus de servicio.

## Configuración de una cadena de conexión del Bus de servicio

El bus de servicio usa una cadena de conexión para almacenar extremos y credenciales. Puede poner la cadena de conexión en un archivo de configuración en vez de codificarla de forma rígida:

- Si usa Servicios en la nube de Azure, es aconsejable que almacene la cadena de conexión con el sistema de configuración de servicios de Azure (archivos *.csdef y *.cscfg).
- Al usar Sitios web Azure o Máquinas virtuales de Azure, es recomendable que almacene su cadena de conexión con el sistema de configuración .NET (por ejemplo, el archivo Web.config).

En cualquiera de los dos casos, puede recuperar la cadena de conexión usando el método [CloudConfigurationManager.GetSetting][GetSetting], como se indica más adelante en este artículo.

### Configuración de la cadena de conexión si usa Servicios en la nube

El mecanismo de configuración de servicios es exclusivo para los proyectos de los servicios en la nube de Azure y le permite cambiar dinámicamente la configuración desde el [Portal de Azure clásico][] sin volver a implementar la aplicación. Por ejemplo, agregue una etiqueta `Setting` al archivo de definición de servicio (.csdef), como se indica en el siguiente ejemplo:

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```
A continuación, especifique los valores del archivo de configuración de servicio (.cscfg), tal y como se muestra en el siguiente ejemplo.

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Use el nombre y los valores de clave de la firma de acceso compartido (SAS) recuperados del Portal de Azure clásico, como se indica en la sección anterior.

### Configuración de la cadena de conexión al usar Sitios web o Máquinas virtuales de Azure

Al usar Sitios web o Máquinas virtuales, se recomienda usar el sistema de configuración de .NET (por ejemplo, **Web.config**). Almacene la cadena de conexión mediante el elemento `<appSettings>`.

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Use el nombre y los valores de clave de SAS recuperados del Portal de Azure clásico, como se indica en la sección anterior.

## Creación de una cola

Puede realizar operaciones de administración para las colas del Bus de servicio mediante la clase [NamespaceManager][]. Esta clase proporciona métodos para crear, enumerar y eliminar colas.

En este ejemplo, se construye un objeto [NamespaceManager][] usando la clase [CloudConfigurationManager][] de Azure con una cadena de conexión que consta de una dirección base de un espacio de nombres del Bus de servicio y las credenciales SAS adecuadas con los permisos para administrarla. Esta cadena de conexión tiene la forma que se muestra en el ejemplo siguiente.

````
Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey
````

Use el siguiente ejemplo con las opciones de configuración de la sección anterior.

```
// Create the queue if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue("TestQueue");
}
```

Hay sobrecargas del método [CreateQueue](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx) que le permiten ajustar las propiedades de la cola (por ejemplo, para establecer el valor del período de vida predeterminado para que se aplique a los mensajes enviados a la cola). Esta configuración se aplica mediante la clase [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx). En el siguiente ejemplo se muestra cómo crear una cola con el nombre `TestQueue` con un tamaño máximo de 5 GB y un período de vida de mensaje predeterminado de un minuto.

```
// Configure queue settings.
QueueDescription qd = new QueueDescription("TestQueue");
qd.MaxSizeInMegabytes = 5120;
qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new queue with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue(qd);
}
```

> [AZURE.NOTE] Puede usar el método [QueueExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx) en los objetos [NamespaceManager][] para comprobar si ya existe una cola con un nombre especificado en un espacio de nombres de servicio.

## mensajes a una cola

Para enviar un mensaje a una cola del bus de servicio, la aplicación crea un objeto [QueueClient][] mediante la cadena de conexión.

El código siguiente muestra cómo crear un objeto [QueueClient][] para la cola `TestQueue` que se acaba de crear con la llamada de la API [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx).

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

Client.Send(new BrokeredMessage());
```

Los mensajes enviados a las colas del bus de servicio y recibidos en ellas son instancias de la clase [BrokeredMessage][]. Los objetos [BrokeredMessage][] cuentan con un conjunto de propiedades estándar (como [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) y [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un diccionario que se usa para mantener las propiedades personalizadas específicas de la aplicación y un conjunto de datos arbitrarios de aplicaciones. Una aplicación puede configurar el cuerpo del mensaje pasando todos los objetos serializables al constructor del objeto [BrokeredMessage][] y, a continuación, se usará el **DataContractSerializer** adecuado para serializar el objeto. También puede especificar un objeto **System.IO.Stream**.

En el ejemplo siguiente se muestra cómo enviar cinco mensajes de prueba al objeto `TestQueue` de [QueueClient][] obtenido en el ejemplo de código anterior.

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set some addtional custom app-specific properties.
  message.Properties["TestProperty"] = "TestValue";
  message.Properties["Message number"] = i;

  // Send message to the queue.
  Client.Send(message);
}
```

Las colas del Bus de servicio admiten mensajes con un [tamaño máximo de 256 KB](service-bus-azure-and-service-bus-queues-compared-contrasted.md#capacity-and-quotas) (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 KB). No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un límite de 5 GB. Si está habilitada la división en particiones, el límite superior es más elevado. Para más información, consulte [Entidades de mensajería con particiones](service-bus-partitioning.md).

## Recepción de mensajes de una cola

La forma recomendada de recibir mensajes desde una cola es usar un objeto [QueueClient][]. Los objetos [QueueClient][] pueden funcionar en dos modos distintos: [ReceiveAndDelete y PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Cuando se usa el modo **ReceiveAndDelete**, la operación de recepción consta de una sola fase; es decir, cuando el Bus de servicio recibe una solicitud de lectura para un mensaje de una cola, lo marca como consumido y lo devuelve a la aplicación. **ReceiveAndDelete** es el modelo más sencillo y funciona mejor en aquellos escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el modo **PeekLock** (que es el predeterminado), el proceso de recepción es una operación en dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción creando la llamada [Complete][] en el mensaje recibido. Cuando el Bus de servicio ve la llamada [Complete][], marca el mensaje como consumido y lo quita de la cola.

En el ejemplo siguiente se muestra cómo se pueden recibir y procesar mensajes con el modo **PeekLock** predeterminado. Para especificar un valor [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) diferente, puede usar otra sobrecarga para [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx). Este ejemplo usa la devolución de llamada [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) para procesar mensajes a medida que llegan a `TestQueue`.

```
string connectionString =
  CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
QueueClient Client =
  QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

// Callback to handle received messages.
Client.OnMessage((message) =>
{
    try
    {
        // Process message from queue.
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Test Property: " +
        message.Properties["TestProperty"]);

        // Remove message from queue.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in queue.
        message.Abandon();
    }
}, options);
```

Este ejemplo configura la devolución de llamada [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) usando un objeto [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). [Autocompletar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) se establece en **false** para habilitar el control manual sobre cuándo llamar a [Complete][] en el mensaje recibido. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) se establece en un minuto, lo que hace que el cliente espere durante un minuto por un mensaje antes de que se agote el tiempo de espera de la llamada y el cliente realice una nueva llamada para comprobar los mensajes. Este valor de propiedad reduce el número de veces que el cliente hace llamadas facturables que no recuperan mensajes.

## Actuación ante errores de la aplicación y mensajes que no se pueden leer

El Bus de servicio proporciona una funcionalidad que ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, entonces realice la llamada al método [Abandon](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) del mensaje recibido (en lugar del método [Complete][]). Esto hará que el Bus de servicio desbloquee el mensaje de la cola y esté disponible para volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay otro tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación se bloquea), entonces el Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

Si la aplicación se bloquea después de procesar el mensaje y antes de emitir la solicitud [Complete][], el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Esta posibilidad habitualmente se denomina **Al menos un procesamiento**, es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue usando la propiedad [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) del mensaje, que permanece constante en todos los intentos de entrega.

## Pasos siguientes

Ahora que conoce los fundamentos de las colas del Bus de servicio, siga estos vínculos para obtener más información.

-   Obtenga información sobre las entidades de la mensajería del Bus de servicio en [Colas, temas y suscripciones][].
-   Compile una aplicación que envíe y reciba mensajes desde una cola del Bus de servicio y hacia ella con el [Tutorial de .NET de mensajería asincrónica del Bus de servicio][].
-   Descargue ejemplos de Bus de servicio en [Ejemplos de Azure][] o consulte la [información general de ejemplos de Bus de servicio][].

  [Portal de Azure clásico]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Colas, temas y suscripciones]: service-bus-queues-topics-subscriptions.md
  [Tutorial de .NET de mensajería asincrónica del Bus de servicio]: service-bus-brokered-tutorial-dotnet.md
  [Ejemplos de Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [información general de ejemplos de Bus de servicio]: service-bus-samples.md
  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [Complete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx

<!---HONumber=AcomDC_0128_2016-->