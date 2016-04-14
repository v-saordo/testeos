<properties 
   pageTitle="Tutorial de REST de mensajería asíncrona del Bus de servicio | Microsoft Azure"
   description="Tutorial de REST de mensajería asíncrona."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/15/2015"
   ms.author="sethm" />

# Tutorial de REST de mensajería asíncrona del Bus de servicio

En este tutorial se muestra cómo crear una cola básica del Bus de servicio de Azure basada en REST y un tema o servicio de suscripción.

## Paso 1: crear un espacio de nombres

El primer paso es crear un espacio de nombres de servicio y obtener una clave de [firma de acceso compartido](service-bus-sas-overview.md) (SAS) Un espacio de nombres de servicio proporciona un límite de aplicación para cada aplicación que se expone a través del Bus de servicio. El sistema genera una clave SAS automáticamente cuando se crea un espacio de nombres de servicio. La combinación del espacio de nombres de servicio y la clave SAS proporciona una credencial de Bus de servicio para autenticar el acceso a una aplicación.

### Creación de un espacio de nombres y obtención de una clave secreta compartida

1. Para crear un espacio de nombres de servicio, visite el [Portal de Azure clásico][]. Haga clic en **Bus de servicio** en el lado izquierdo y después en **Crear**. Escriba un nombre para el espacio de nombres y luego haga clic en la marca de verificación.

1. En la ventana principal del [Portal de Azure clásico][], haga clic en el nombre del espacio de nombres que creó en el paso anterior.

1. Haga clic en la pestaña **Configurar**.

1. En la sección **Generador de claves de acceso compartido**, anote el valor de **Clave principal ** asociado con la directiva **RootManageSharedAccessKey**, o cópielo en el Portapapeles. Este valor se usará más adelante en el tutorial.

## Creación de un cliente de consola

Las colas del Bus de servicio permiten almacenar mensajes en una cola en la que los primeros en entrar serán los últimos en salir. Los temas y suscripciones implementan un patrón de publicación/suscripción; se crea un tema y, a continuación, una o varias suscripciones asociadas a él. Cuando se envían mensajes al tema, se envían inmediatamente después a los suscriptores del tema.

El código de este tutorial:

- Usa el espacio de nombres de su servicio y la clave de [firma de acceso compartido](service-bus-sas-overview.md) (SAS) para obtener acceso a los recursos del espacio de nombres de Bus de servicio.

- Crea una cola, envía un mensaje a la cola y lee el mensaje de la cola.

- Crea un tema, una suscripción a dicho tema y envía y lee el mensaje de la suscripción.

- Recupera toda la información de la cola, el tema y la suscripción (incluidas las reglas de suscripción) del Bus de servicio.

- Elimina los recursos de la cola, el tema y la suscripción.

Dado que el servicio es un servicio web de estilo REST, no se incluyen tipos especiales, ya que todo el intercambio incluye cadenas. Esto significa que el proyecto Visual Studio no debe hacer referencia a bibliotecas de Bus de servicio.

Después de obtener el espacio de nombres del servicio y las credenciales en el primer paso, a continuación se crea una aplicación básica de consola de Visual Studio.

### Creación de una aplicación de consola

1. Abra Visual Studio como administrador; para ello, haga clic con el botón derecho en el programa en el menú **Inicio** y, después, haga clic en **Ejecutar como administrador**.

1. Cree un nuevo proyecto de aplicación de consola. En el menú **Archivo**, haga clic en **Nuevo** y luego en **Proyecto**. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Visual C#** (si **Visual C#** no aparece, busque en **Otros lenguajes**), seleccione la plantilla **Aplicación de consola** y asígnele el nombre **Microsoft.ServiceBus.Samples**. Use el valor predeterminado de Ubicación. Haga clic en **Aceptar** para crear el proyecto.

1. En Program.cs, asegúrese de que las instrucciones `using` aparecen de la siguiente manera:

	```
	using System;
	using System.Globalization;
	using System.IO;
	using System.Net;
	using System.Security.Cryptography;
	using System.Text;
	using System.Xml;
	```

1. Si fuera necesario, cambie el nombre del espacio de nombres del programa del valor predeterminado de Visual Studio a `Microsoft.ServiceBus.Samples`.

1. En la clase `Program`, agregue las variables globales siguientes:
	
	```
	static string serviceNamespace;
	static string baseAddress;
	static string token;
	const string sbHostName = "servicebus.windows.net";
	```

1. En `Main()`, pegue el código siguiente:

	```
	Console.Write("Enter your service namespace: ");
	serviceNamespace = Console.ReadLine();
	
	Console.Write("Enter your SAS key: ");
	string SASKey = Console.ReadLine();
	
	baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
	try
	{
	    token = GetSASToken("RootManageSharedAccessKey", SASKey);
	
	    string queueName = "Queue" + Guid.NewGuid().ToString();
	
	    // Create and put a message in the queue
	    CreateQueue(queueName, token);
	    SendMessage(queueName, "msg1");
	    string msg = ReceiveAndDeleteMessage(queueName);
	
	    string topicName = "Topic" + Guid.NewGuid().ToString();
	    string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
	    CreateTopic(topicName);
	    CreateSubscription(topicName, subscriptionName);
	    SendMessage(topicName, "msg2");
	
	    Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));
	
	    // Get an Atom feed with all the queues in the namespace
	    Console.WriteLine(GetResources("$Resources/Queues"));
	
	    // Get an Atom feed with all the topics in the namespace
	    Console.WriteLine(GetResources("$Resources/Topics"));
	
	    // Get an Atom feed with all the subscriptions for the topic we just created
	    Console.WriteLine(GetResources(topicName + "/Subscriptions"));
	
	    // Get an Atom feed with all the rules for the topic and subscription we just created
	    Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));
	
	    // Delete the queue we created
	    DeleteResource(queueName);
	
	    // Delete the topic we created
	    DeleteResource(topicName);
	
	    // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
	    Console.WriteLine(GetResources("$Resources/Topics"));
	
	    // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
	    Console.WriteLine(GetResources("$Resources/Queues"));
	}
	catch (WebException we)
	{
	    using (HttpWebResponse response = we.Response as HttpWebResponse)
	    {
	        if (response != null)
	        {
	            Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
	        }
	        else
	        {
	            Console.WriteLine(we.ToString());
	        }
	    }
	}
	
	Console.WriteLine("\nPress ENTER to exit.");
	Console.ReadLine();
	```

## Creación de credenciales de administración

El siguiente paso es escribir un método que procesa el espacio de nombres y la clave SAS que especificó en el paso anterior y devuelve un token SAS. En este ejemplo se crea un token SAS es válido durante una hora.

### Creación de un método GetSASToken()

Pegue el código siguiente después del método `Main()`, en la clase `Program`:

```
private static string GetSASToken(string SASKeyName, string SASKeyValue)
{
  TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
  string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
  string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
  HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

  string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
  string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
      WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
  return sasToken;
}
```
## Creación de la cola

El siguiente paso es escribir un método que use el comando PUT de HTTP de estilo REST para crear una cola.

Pegue el código siguiente directamente después del código `GetSASToken()` que agregó en el paso anterior:

```
// Uses HTTP PUT to create the queue
private static string CreateQueue(string queueName, string token)
{
    // Create the URI of the new queue, note that this uses the HTTPS schemestring queueAddress = baseAddress + queueName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating queue {0}", queueAddress);
    // Prepare the body of the create queue requestvar putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + queueName + @"</title>
                                  <content type=""application/xml"">
                                    <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## Envío de un mensaje a la cola

En este paso, agregue un método que use el comando POST de HTTP basado en REST para enviar un mensaje a la cola que creó en el paso anterior.

1. Pegue el código siguiente directamente después del código de `CreateQueue()` que agregó en el paso anterior:

	```
	// Sends a message to the "queueName" queue, given the name and the value to enqueue
	// Uses an HTTP POST request.
	private static void SendMessage(string queueName, string body)
	{
	    string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
	    Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
	    WebClient webClient = new WebClient();
	    webClient.Headers[HttpRequestHeader.Authorization] = token;
	
	    webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));
	}
	```

1. Las propiedades del mensaje asincrónico estándar se colocan en un encabezado HTTP `BrokerProperties`. Las propiedades del agente se deben serializar en formato JSON. Para especificar un valor de **TimeToLive** de 30 segundos y para agregar una etiqueta de mensaje "M1" al mensaje, agregue el código siguiente inmediatamente antes de la llamada a `webClient.UploadData()` que se muestra en el ejemplo anterior:

	```
	// Add brokered message properties "TimeToLive" and "Label"
	webClient.Headers.Add("BrokerProperties", "{ "TimeToLive":30, "Label":"M1"}");
	```

	Tenga en cuenta que las propiedades del mensaje de intermediación se han agregado y se agregarán. Por tanto, la solicitud de envío debe especificar una versión de API que admita todas las propiedades de mensaje de intermediación que formen parte de la solicitud. Si la versión de API especificada no admite alguna de las propiedades de mensaje de intermediación, dicha propiedad se omite.

1. Las propiedades personalizadas de mensajes se definen como conjunto de pares de clave-valor. Cada propiedad personalizada se almacena en su propio encabezado TPPT. Para agregar las propiedades personalizadas “Priority” y “Customer”, agregue el siguiente código inmediatamente antes de la llamada a `webClient.UploadData()` que se muestra en el ejemplo anterior:

	```
	// Add custom properties "Priority" and "Customer".
	webClient.Headers.Add("Priority", "High");
	webClient.Headers.Add("Customer", "12345");
	```

## Recepción y eliminación de un mensaje de la cola

El siguiente paso es agregar un método que use el comando DELETE de HTTP de estilo REST para recibir y eliminar un mensaje de la cola.

Pegue el código siguiente directamente después del código de `SendMessage()` que agregó en el paso anterior:

```
// Receives and deletes the next message from the given resource (queue, topic, or subscription)
// using the resourceName and an HTTP DELETE request
private static string ReceiveAndDeleteMessage(string resourceName)
{
    string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
    Console.WriteLine("\nRetrieving message from {0}", fullAddress);
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
    string responseStr = Encoding.UTF8.GetString(response);

    Console.WriteLine(responseStr);
    return responseStr;
}
```

## Creación de un tema y una suscripción

El siguiente paso es escribir un método que use el comando PUT de HTTP de estilo REST para crear un tema. A continuación, se escribe un método que cree una suscripción a dicho tema.

### de un tema

Pegue el código siguiente directamente después del código de `ReceiveAndDeleteMessage()` que agregó en el paso anterior:

```
// Using an HTTP PUT request.
private static string CreateTopic(string topicName)
{
    var topicAddress = baseAddress + topicName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating topic {0}", topicAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

### una suscripción

El código siguiente crea una suscripción al tema que creó en la sección anterior. Agregue el código siguiente directamente después de la definición de `CreateTopic()`:

```
private static string CreateSubscription(string topicName, string subscriptionName)
{
    var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## Recuperación de recursos de mensajes

En este paso, agregue el código que recupera las propiedades de los mensajes y, a continuación, elimina los recursos de mensajes que creó en los pasos anteriores.

### Recuperación de una fuente Atom con los recursos especificados

Agregue el código siguiente directamente después del método `CreateSubscription()` que agregó en el paso anterior:

```
private static string GetResources(string resourceAddress)
{
    string fullAddress = baseAddress + resourceAddress;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;
    Console.WriteLine("\nGetting resources from {0}", fullAddress);
    return FormatXml(webClient.DownloadString(fullAddress));
}
```

### Eliminación de entidades de mensajes

Agregue el código siguiente directamente después del código que agregó en el paso anterior:

```
private static string DeleteResource(string resourceName)
{
    string fullAddress = baseAddress + resourceName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nDeleting resource at {0}", fullAddress);
    byte[] response = webClient.UploadData(fullAddress, "DELETE", newbyte[0]);
    return Encoding.UTF8.GetString(response);
}
```

### Formato de la fuente Atom

El método `GetResources()` contiene una llamada a un método `FormatXml()` que vuelve a aplicar formato a la fuente Atom recuperada para que sea más legible. A continuación se muestra la definición de `FormatXml()`; agregue el código siguiente directamente después del código de `DeleteResource()` que agregó en el paso anterior:

```
// Formats the XML string to be more human-readable; intended for display purposes
private static string FormatXml(string inputXml)
{
    XmlDocument document = new XmlDocument();
    document.Load(new StringReader(inputXml));

    StringBuilder builder = new StringBuilder();
    using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
    {
        writer.Formatting = Formatting.Indented;
        document.Save(writer);
    }

    return builder.ToString();
}
```

## Compilación y ejecución de la aplicación

Ya puede compilar y ejecutar la aplicación. En el menú **Compilar** de Visual Studio, haga clic en **Compilar solución** o presione F6.

### Ejecución de la aplicación

Si no hay ningún error, presione F5 para ejecutar la aplicación. Cuando se le solicite, escriba el espacio de nombres del servicio, el nombre de la clave de SAS y el valor de la clave de SAS que obtuvo en el primer paso.

### Ejemplo

El siguiente ejemplo es el código completo, tal como debe aparecer después de seguir todos los pasos de este tutorial.

```
using System;
using System.Globalization;
using System.IO;
using System.Net;
using System.Security.Cryptography;
using System.Text;
using System.Xml;

namespace Microsoft.ServiceBus.Samples
{
    class Program
    {
        static string serviceNamespace;
        static string baseAddress;
        static string token;
        const string sbHostName = "servicebus.windows.net";

        static void Main(string[] args)
        {
            Console.Write("Enter your service namespace: ");
            serviceNamespace = Console.ReadLine();

            Console.Write("Enter your SAS key: ");
            string SASKey = Console.ReadLine();

            baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
            try
            {
                token = GetSASToken("RootManageSharedAccessKey", SASKey);

                string queueName = "Queue" + Guid.NewGuid().ToString();

                // Create and put a message in the queue
                CreateQueue(queueName, token);
                SendMessage(queueName, "msg1");
                string msg = ReceiveAndDeleteMessage(queueName);

                string topicName = "Topic" + Guid.NewGuid().ToString();
                string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
                CreateTopic(topicName);
                CreateSubscription(topicName, subscriptionName);
                SendMessage(topicName, "msg2");

                Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));

                // Get an Atom feed with all the queues in the namespace
                Console.WriteLine(GetResources("$Resources/Queues"));

                // Get an Atom feed with all the topics in the namespace
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the subscriptions for the topic we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions"));

                // Get an Atom feed with all the rules for the topic and subscription we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));

                // Delete the queue we created
                DeleteResource(queueName);

                // Delete the topic we created
                DeleteResource(topicName);

                // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Queues"));
            }
            catch (WebException we)
            {
                using (HttpWebResponse response = we.Response as HttpWebResponse)
                {
                    if (response != null)
                    {
                        Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
                    }
                    else
                    {
                        Console.WriteLine(we.ToString());
                    }
                }
            }

            Console.WriteLine("\nPress ENTER to exit.");
            Console.ReadLine();
        }

        private static string GetSASToken(string SASKeyName, string SASKeyValue)
        {
            TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
            string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
            string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
            HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

            string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
            string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
                WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
            return sasToken;
        }

        // Uses HTTP PUT to create the queue
        private static string CreateQueue(string queueName, string token)
        {
            // Create the URI of the new queue, note that this uses the HTTPS scheme
            string queueAddress = baseAddress + queueName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating queue {0}", queueAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + queueName + @"</title>
                                  <content type=""application/xml"">
                                    <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        // Sends a message to the "queueName" queue, given the name and the value to enqueue
        // Uses an HTTP POST request.
        private static void SendMessage(string queueName, string body)
        {
            string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
            Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            // Add brokered message properties “TimeToLive” and “Label”.
            webClient.Headers.Add("BrokerProperties", "{ "TimeToLive":30, "Label":"M1"}");
            // Add custom properties “Priority” and “Customer”.
            webClient.Headers.Add("Priority", "High");
            webClient.Headers.Add("Customer", "12345");
            webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));

        }

        // Receives and deletes the next message from the given resource (queue, topic, or subscription)
        // using the resourceName and an HTTP DELETE request.
        private static string ReceiveAndDeleteMessage(string resourceName)
        {
            string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
            Console.WriteLine("\nRetrieving message from {0}", fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            string responseStr = Encoding.UTF8.GetString(response);

            Console.WriteLine(responseStr);
            return responseStr;
        }

        // Using an HTTP PUT request.
        private static string CreateTopic(string topicName)
        {
            var topicAddress = baseAddress + topicName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating topic {0}", topicAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string CreateSubscription(string topicName, string subscriptionName)
        {
            var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string GetResources(string resourceAddress)
        {
            string fullAddress = baseAddress + resourceAddress;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            Console.WriteLine("\nGetting resources from {0}", fullAddress);
            return FormatXml(webClient.DownloadString(fullAddress));
        }

        private static string DeleteResource(string resourceName)
        {
            string fullAddress = baseAddress + resourceName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nDeleting resource at {0}", fullAddress);
            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            return Encoding.UTF8.GetString(response);
        }

        // Formats the XML string to be more human-readable; intended for display purposes
        private static string FormatXml(string inputXml)
        {
            XmlDocument document = new XmlDocument();
            document.Load(new StringReader(inputXml));

            StringBuilder builder = new StringBuilder();
            using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
            {
                writer.Formatting = Formatting.Indented;
                document.Save(writer);
            }

            return builder.ToString();
        }
    }
}
```

## Pasos siguientes

Para obtener más información, consulte los artículos siguientes:

- [Introducción a la mensajería del Bus de servicio](service-bus-messaging-overview.md)
- [Elementos fundamentales del Bus de servicio](service-bus-fundamentals-hybrid-solutions.md)
- [Tutorial de REST de Bus de servicio](service-bus-relay-rest-tutorial.md)

[Portal de Azure clásico]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0309_2016-->