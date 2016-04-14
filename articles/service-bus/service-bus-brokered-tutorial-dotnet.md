<properties 
   pageTitle="Tutorial de .NET de mensajería asíncrona del Bus de servicio | Microsoft Azure"
   description="Tutorial de .NET de mensajería asíncrona."
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
   ms.date="09/14/2015"
   ms.author="sethm" />

# Tutorial de .NET de mensajería asíncrona del Bus de servicio

El Bus de servicio de Azure ofrece dos soluciones integrales de mensajes: una, mediante un servicio de "retransmisión" centralizado que se ejecuta en la nube y que admite diferentes protocolos de transporte y servicios web estándar, incluidos SOAP, WS-* y REST. El cliente no necesita una conexión directa al servicio local ni necesita saber dónde reside el servicio, y el servicio local no necesita ningún puerto de entrada abierto en el firewall.

La segunda solución de mensajería habilita capacidades de mensajería "asíncrona". Estos pueden considerarse como características de mensajería asíncrona o desacoplada que admiten la publicación y suscripción, el desacoplamiento temporal y escenarios de equilibrio de carga que usan la infraestructura de mensajería del Bus de servicio. La comunicación desacoplada ofrece muchas ventajas; por ejemplo, los clientes y servidores pueden conectarse según sea necesario y realizar sus operaciones de forma asincrónica.

Este tutorial está diseñado para ofrecerle una visión general y experiencia práctica con las colas, uno de los componentes principales de la mensajería asíncrona del Bus de servicio. Una vez completada la secuencia de temas de este tutorial, tendrá una aplicación que rellena una lista de mensajes, crea una cola y envía mensajes a dicha cola. Por último, la aplicación recibe y muestra los mensajes de la cola, limpia sus recursos y se cierra. Para obtener un tutorial correspondiente que describe cómo crear una aplicación que usa las capacidades de mensajería "retransmitida" del Bus de servicio, consulte el [tutorial de mensajería retransmitida del Bus de servicio](service-bus-relay-tutorial.md).

## Introducción y requisitos previos

Las colas ofrecen una entrega de mensajes FIFO (PEPS, primero en entrar, primero en salir) a uno o más destinatarios de la competencia. FIFO significa que normalmente se espera que los mensajes sean recibidos y procesados por los receptores en el orden temporal en el que se pusieron en cola, y cada mensaje solo será recibido y procesado solo por el consumidor de un mensaje. La principal ventaja del uso de colas es conseguir el *desacoplamiento temporal* de componentes de la aplicación: en otras palabras, los productores y consumidores no necesitan enviar y recibir mensajes al mismo tiempo, ya que los mensajes se almacenan de forma duradera en la cola. Una ventaja relacionada es la *nivelación de la carga*, lo que permite a los productores y consumidores enviar y recibir mensajes con distintas velocidades.

Los siguientes son algunos pasos administrativos y requisitos previos que deben seguirse antes de comenzar el tutorial. El primero es crear un espacio de nombres de servicio y obtener una clave de firma de acceso compartido (SAS). Un espacio de nombres de servicio proporciona un límite de aplicación para cada aplicación que se expone a través del Bus de servicio. El sistema genera una clave SAS automáticamente cuando se crea un espacio de nombres de servicio. La combinación del espacio de nombres de servicio y la clave SAS proporciona una credencial con la que el Bus de servicio autentica el acceso a una aplicación.

### Creación de un espacio de nombres de servicio y obtención de una clave de SAS

1. Para crear un espacio de nombres de servicio, visite el [Portal de Azure clásico][]. Haga clic en **Bus de servicio** en el lado izquierdo y después en **Crear**. Escriba un nombre para el espacio de nombres y luego haga clic en la marca de verificación.

1. En la ventana principal del [Portal de Azure clásico][], haga clic en el nombre del espacio de nombres que creó en el paso anterior.

1. Haga clic en **Configurar**.

1. En la sección **Generador de firmas de acceso compartido**, anote la clave principal asociada a la directiva **RootManagerSharedAccessKey** o copíelo en el Portapapeles. Este valor se usará más adelante en este tutorial.

El siguiente paso es crear un proyecto de Visual Studio y escribir dos funciones auxiliares que cargan una lista delimitada por comas de mensajes en un objeto [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) .NET [List](https://msdn.microsoft.com/library/6sh2ey19.aspx) fuertemente tipado.

### Creación de un proyecto de Visual Studio

1. Abra Visual Studio como administrador haciendo clic en el programa en el menú Inicio y haga clic en **Ejecutar como administrador**.

1. Cree un nuevo proyecto de aplicación de consola. Haga clic en el menú **Archivo** y seleccione **Nuevo**; a continuación, haga clic en **Proyecto**. En el cuadro de diálogo **Nuevo proyecto**, haga clic en **Visual C#** (si **Visual C#** no aparece, mire en **Otros idiomas**), haga clic en la plantilla **Aplicación de consola** y asígnele el nombre **QueueSample**. Use la **Ubicación** predeterminada. Haga clic en **Aceptar** para crear el proyecto.

1. Use el Administrador de paquetes de NuGet para agregar las bibliotecas del Bus de servicio al proyecto:
	1. En el Explorador de soluciones, haga clic con el botón derecho en la carpeta del proyecto y haga clic en **Administrar paquetes de NuGet**.
	2. En el cuadro de diálogo **Administrar paquetes de Nuget**, busque en línea **Bus de servicio** y haga clic en **Instalar**. <br />
1. En el Explorador de soluciones, haga doble clic en el archivo Program.cs para abrirlo en el editor de Visual Studio. Cambie el nombre del espacio de nombres de su nombre predeterminado de `QueueSample` a `Microsoft.ServiceBus.Samples`.

	```
	Microsoft.ServiceBus.Samples
	{
	    …
	```

1. Modifique las instrucciones `using` como se muestra en el código siguiente.

	```
	using System;
	using System.Collections.Generic;
	using System.Data;
	using System.IO;
	using System.Threading;
	using Microsoft.ServiceBus.Messaging;
	```

1. Cree un archivo de texto denominado Data.csv y copie el siguiente texto delimitado por comas.

	```
	IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
	1,Package lost,1,1,Basic,5,1,FALSE
	2,Package damaged,1,1,Basic,5,1,FALSE
	3,Product defective,1,2,Premium,5,2,FALSE
	4,Product damaged,2,2,Premium,5,2,FALSE
	5,Package lost,2,2,Basic,5,2,TRUE
	6,Package lost,3,2,Basic,5,2,FALSE
	7,Package damaged,3,7,Premium,5,3,FALSE
	8,Product defective,3,2,Premium,5,3,FALSE
	9,Product damaged,4,6,Premium,5,3,TRUE
	10,Package lost,4,8,Basic,5,3,FALSE
	11,Package damaged,5,4,Basic,5,4,FALSE
	12,Product defective,5,4,Basic,5,4,FALSE
	13,Package lost,6,8,Basic,5,4,FALSE
	14,Package damaged,6,7,Premium,5,5,FALSE
	15,Product defective,6,2,Premium,5,5,FALSE
	```

	Guarde y cierre el archivo Data.csv y recuerde la ubicación en la que se ha guardado.

1. En el Explorador de soluciones, haga clic en el nombre del proyecto (en este ejemplo, **QueueSample**), haga clic en **Agregar**, a continuación, haga clic en **Elemento existente**.

1. Busque el archivo Data.csv que ha creado en el paso 6. Haga clic en el archivo y; a continuación, en **Agregar**. Asegúrese de que **Todos los archivos (*.*)** está seleccionado en la lista de tipos de archivo.

### Creación de una función que analiza una lista de mensajes

1. Antes del método`Main()`, declare dos variables: una de tipo **DataTable**, que contendrá la lista de mensajes en Data.csv. La otra debe ser de tipo objeto List, fuertemente tipado en [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Esta última es la lista de mensajes asíncronos que se usará en los pasos siguientes del tutorial.

	```
	namespace Microsoft.ServiceBus.Samples
	{
	    public class Program
	    {
	
	        private static DataTable issues;
	        private static List<BrokeredMessage> MessageList;
	```

1. Fuera de `Main()`, defina un método `ParseCSV()` que analice la lista de mensajes de Data.csv y cargue los mensajes en una tabla [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx), como se muestra aquí. El método devuelve un objeto **DataTable**.

	```
	static DataTable ParseCSVFile()
	{
	    DataTable tableIssues = new DataTable("Issues");
	    string path = @"..\..\data.csv";
	    try
	    {
	        using (StreamReader readFile = new StreamReader(path))
	        {
	            string line;
	            string[] row;
	
	            // create the columns
	            line = readFile.ReadLine();
	            foreach (string columnTitle in line.Split(','))
	            {
	                tableIssues.Columns.Add(columnTitle);
	            }
	
	            while ((line = readFile.ReadLine()) != null)
	            {
	                row = line.Split(',');
	                tableIssues.Rows.Add(row);
	            }
	        }
	    }
	    catch (Exception e)
	    {
	        Console.WriteLine("Error:" + e.ToString());
	    }
	
	    return tableIssues;
	}
	```

1. En el método `Main()`, agregue una instrucción que llama al método `ParseCSVFile()`:

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	
	}
	```

### Creación de una función que carga la lista de mensajes

1. Fuera de `Main()`, defina un método `GenerateMessages()` que toma el objeto **DataTable** devuelto por `ParseCSVFile()` y carga la tabla en una lista de mensajes asíncronos fuertemente tipados. El método devuelve el objeto **List**, como en el ejemplo siguiente. 

	```
	static List<BrokeredMessage> GenerateMessages(DataTable issues)
	{
	    // Instantiate the brokered list object
	    List<BrokeredMessage> result = new List<BrokeredMessage>();
	
	    // Iterate through the table and create a brokered message for each row
	    foreach (DataRow item in issues.Rows)
	    {
	        BrokeredMessage message = new BrokeredMessage();
	        foreach (DataColumn property in issues.Columns)
	        {
	            message.Properties.Add(property.ColumnName, item[property]);
	        }
	        result.Add(message);
	    }
	    return result;
	}
	```

1. En `Main()`, directamente debajo de la llamada a `ParseCSVFile()`, agregue una instrucción que llame al método `GenerateMessages()` con el valor devuelto de `ParseCSVFile()` como argumento:

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	}
	```

### Obtención de las credenciales de usuario

1. En primer lugar, cree tres variables de cadena globales para alojar estos valores. Declare estas variables directamente después de las declaraciones de variables anteriores; por ejemplo:

	```
	namespace Microsoft.ServiceBus.Samples
	{
	    public class Program
	    {
	
	        private static DataTable issues;
	        private static List<BrokeredMessage> MessageList; 

	        // Add these variables
			private static string ServiceNamespace;
	        private static string sasKeyName = "RootManageSharedAccessKey";
	        private static string sasKeyValue;
	        …
	```

1. A continuación, cree una función que acepta y almacena el espacio de nombres de servicio y la clave SAS. Agregue este método fuera de `Main()`. Por ejemplo:

	```
	static void CollectUserInput()
	{
	    // User service namespace
	    Console.Write("Please enter the namespace to use: ");
	    ServiceNamespace = Console.ReadLine();
	
	    // Issuer key
	    Console.Write("Enter the SAS key to use: ");
	    sasKeyValue = Console.ReadLine();
	}
	```

1. En `Main()`, directamente debajo de la llamada a `GenerateMessages()`, agregue una sentencia que llame al método `CollectUserInput()`:

	```
	public static void Main(string[] args)
	{
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	    
	    // Collect user input
	    CollectUserInput();
	}
	```

### Compile la solución

Desde el menú **Crear** de Visual Studio, puede hacer clic en **Generar solución** o presionar F6 para confirmar la exactitud de su trabajo hasta ahora.

## Creación de credenciales de administración

En este paso, definirá las operaciones de administración que va a usar para crear credenciales de firma de acceso compartido (SAS) con las que se va a autorizar su aplicación.

1. Para mayor claridad, este tutorial coloca todas las operaciones de cola en un método independiente. Cree un método `Queue()` en la clase `Program`, bajo el método `Main()`. Por ejemplo:
 
	```
	public static void Main(string[] args)
	{
	…
	}
	static void Queue()
	{
	}
	```

1. El paso siguiente consiste en crear una credencial SAS con un objeto [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx). El método de creación toma el nombre de clave de SAS y el valor obtenido en el método `CollectUserInput()`. Agregue el siguiente código al método `Queue()`:

	```
	static void Queue()
	{
	    // Create management credentials
	    TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
	}
	```
### Creación del administrador del espacio de nombres

1. Cree un nuevo objeto de administración del espacio de nombres con un URI que contenga el nombre del espacio de nombres y las credenciales de administración que obtuvo en el último paso como argumentos. Agregue este código directamente bajo el código que agregó en el paso anterior:
	
	```
	NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", <namespaceName>, string.Empty), credentials);
	```

### Ejemplo

En este punto, el código debe ser similar al siguiente:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Queue();
    }

    static void Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

En el paso siguiente, cree la cola a la que enviará mensajes.

## Envío de mensajes a la cola

En este paso, cree una cola y envíe los mensajes contenidos en la lista de mensajes asíncronos a dicha cola.

### Creación de la cola y envío de mensajes a la cola

1. En primer lugar, cree la cola. Por ejemplo, llámelo `myQueue`, y declárelo directamente después de las operaciones de administración que agregó en el último paso:

	```
	QueueDescription myQueue;
	myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
	```

1. En el método `Queue()`, cree un objeto de fábrica de mensajería con un identificador URI de Bus de servicio recién creado como argumento. Agregue el siguiente código directamente después de las operaciones de administración que agregó en el último paso:

	```
	MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
	```

1. A continuación, cree el objeto de cola mediante la clase [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx). Agregue el siguiente código directamente después del código que agregó en el último paso:

	```
	QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
	```

1. A continuación, agregue código que recorra la lista de mensajes asíncronos que creó anteriormente, y envíe cada uno a la cola. Agregue el código siguiente directamente después de la instrucción `CreateQueueClient()` en el paso anterior:
	
	```
	// Send messages
	Console.WriteLine("Now sending messages to the queue.");
	for (int count = 0; count < 6; count++)
	{
	    var issue = MessageList[count];
	    issue.Label = issue.Properties["IssueTitle"].ToString();
	    myQueueClient.Send(issue);
	    Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
	}
	```

## Recepción de mensajes de la cola

En este paso, obtendrá la lista de mensajes de la cola que creó en el paso anterior.

### Creación de un receptor y recepción de mensajes de la cola

En el método `Queue()`, itere a través de la cola y reciba los mensajes con el método [Microsoft.ServiceBus.Messaging.QueueClient.Receive](https://msdn.microsoft.com/library/azure/hh322678.aspx), e imprima cada mensaje en la consola. Agregue el siguiente código directamente bajo el código que agregó en el paso anterior:

	```
	Console.WriteLine("Now receiving messages from Queue.");
	BrokeredMessage message;
	while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
	    {
	        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
	        message.Complete();
	
	        Console.WriteLine("Processing message (sleeping...)");
	        Thread.Sleep(1000);
	    }
	```

### Termine el método `Queue()` y limpie los recursos

Directamente después del código anterior, agregue el siguiente código para limpiar la fábrica de mensajes y los recursos de cola:

	```
	factory.Close();
	myQueueClient.Close();
	namespaceClient.DeleteQueue("IssueTrackingQueue");
	```

### Llame al método `Queue()`

El último paso es agregar una instrucción que llama al método `Queue()` desde `Main()`. Agregue la siguiente línea de código resaltada al final de Main():
	
	```
	public static void Main(string[] args)
	{
	    // Collect user input
	    CollectUserInput();
	
	    // Populate test data
	    issues = ParseCSVFile();
	    MessageList = GenerateMessages(issues);
	
	    // Add this call
	    Queue();
	}
	```

### Ejemplo

El código siguiente contiene la aplicación **QueueSample**

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Queue();
    }

    static void Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);

      QueueDescription myQueue;
      myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");

      MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
      QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

      // Send messages
      Console.WriteLine("Now sending messages to the Queue.");
      for (int count = 0; count < 6; count++)
      {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        myQueueClient.Send(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
      }

      Console.WriteLine("Now receiving messages from Queue.");
      BrokeredMessage message;
      while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
      {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
      }

      factory.Close();
      myQueueClient.Close();
      namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## Compilación y ejecución de la aplicación QueueSample

Ahora que ha completado los pasos anteriores, puede generar y ejecutar la aplicación **QueueSample**.

### Compilación de la aplicación QueueSample

En Visual Studio, desde el menú **Compilación**, haga clic en **Generar solución** o presione F6. Si se producen errores, compruebe que el código es correcto de acuerdo con el ejemplo completo al final del paso anterior.

### Ejecución de la aplicación QueueSample

1. Antes de ejecutar la aplicación, debe asegurarse de que ha creado un espacio de nombres de servicio y obtenido una clave SAS, tal como se describe en [Introducción y requisitos previos](#introduction-and-prerequisites).

1. Abra un explorador y vaya al [Portal de Azure clásico][].

1. Haga clic en **Bus de servicio** en el árbol de la izquierda.

1. Haga clic en el nombre del espacio de nombres que desee usar. En la parte inferior de la página, haga clic en **Información de conexión**. Anote la cadena de conexión con la clave SAS o copíela en el Portapapeles.

1. En Visual Studio, en el menú **Depurar**, haga clic en **Iniciar depuración** o presione F5. Cuando se le solicite, escriba el nombre del espacio de nombres de servicio y la clave que obtuvo en el paso anterior.

## Pasos siguientes

Este tutorial ha mostrado cómo crear una aplicación cliente del Bus de servicio y un servicio mediante las capacidades de mensajería asíncrona del Bus de servicio. Para obtener un tutorial similar que use la [mensajería retransmitida](service-bus-messaging-overview.md#Relayed-messaging) del Bus de servicio, consulte el [tutorial de mensajería retransmitida del Bus de servicio](service-bus-relay-tutorial.md).

Para obtener más información sobre el [Bus de servicio](https://azure.microsoft.com/services/service-bus/), vea los temas siguientes:

- [Introducción a la mensajería del Bus de servicio](service-bus-messaging-overview.md)
- [Elementos fundamentales del Bus de servicio](service-bus-fundamentals-hybrid-solutions.md)
- [Arquitectura del Bus de servicio](service-bus-architecture.md)

[Portal de Azure clásico]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0218_2016-->