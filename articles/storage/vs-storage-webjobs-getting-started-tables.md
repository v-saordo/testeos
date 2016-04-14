<properties
	pageTitle="Introducción al Almacenamiento de Azure y servicios conectados de Visual Studio (proyectos de WebJobs)"
	description="Cómo empezar a usar el almacenamiento de tablas de Azure en un proyecto de WebJobs de Azure en Visual Studio después de conectarse a una cuenta de almacenamiento mediante los servicios conectados de Visual Studio"
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/21/2016"
	ms.author="tarcher"/>

# Introducción a Almacenamiento de Azure (proyectos de WebJobs de Azure)

## Información general

En este artículo se proporcionan ejemplos de código C# que muestran cómo usar la versión 1.x del SDK de WebJobs de Azure con el servicio Almacenamiento de tablas de Azure. Los ejemplos de código usan el [SDK de WebJobs](../app-service-web/websites-dotnet-webjobs-sdk.md) versión 1.x.

El servicio de almacenamiento de tabla de Azure permite almacenar una gran cantidad de datos estructurados. El servicio es un almacén de datos NoSQL que acepta llamadas autenticadas desde dentro y fuera de la nube de Azure. Las tablas de Azure son ideales para el almacenamiento de datos estructurados no relacionales. Vea [Introducción al Almacenamiento de tablas de Azure mediante .NET](storage-dotnet-how-to-use-tables.md#create-a-table) para obtener más información.

Algunos de los fragmentos de código muestran el atributo **Table** usado en funciones que se llaman manualmente, es decir, sin usar ninguno de los atributos de desencadenador.

## Cómo agregar entidades a una tabla

Para agregar entidades a una tabla, use el atributo **Table** con un parámetro **ICollector<T>** o **IAsyncCollector<T>** donde **T** especifica el esquema de las entidades que quiere agregar. El constructor de atributo toma un parámetro de cadena que especifica el nombre de la tabla.

El ejemplo de código siguiente agrega entidades **Person** a una tabla denominada *Ingress*.

		[NoAutomaticTrigger]
		public static void IngressDemo(
		    [Table("Ingress")] ICollector<Person> tableBinding)
		{
		    for (int i = 0; i < 100000; i++)
		    {
		        tableBinding.Add(
		            new Person() {
		                PartitionKey = "Test",
		                RowKey = i.ToString(),
		                Name = "Name" }
		            );
		    }
		}

Normalmente el tipo que se usa con **ICollector** se deriva de **TableEntity** o implementa **ITableEntity**, pero no es necesario. Cualquiera de las siguientes clases **Person** funciona con el código que se muestra en el método **Ingress** anterior.

		public class Person : TableEntity
		{
		    public string Name { get; set; }
		}

		public class Person
		{
		    public string PartitionKey { get; set; }
		    public string RowKey { get; set; }
		    public string Name { get; set; }
		}

Si quiere trabajar directamente con la API de almacenamiento de Azure, puede agregar un parámetro **CloudStorageAccount** a la firma del método.

## Supervisión en tiempo real

Dado que las funciones de entrada de datos a menudo procesan grandes volúmenes de datos, el panel del SDK de WebJobs proporciona datos de supervisión en tiempo real. La sección **Registro de invocación** indica si la función sigue en ejecución.

![Función de entrada en ejecución](./media/vs-storage-webjobs-getting-started-tables/ingressrunning.png)

La página **Detalles de invocación** informa el progreso de la función (la cantidad de entidades escritas) mientras se ejecuta y ofrece la oportunidad de anularla.

![Función de entrada en ejecución](./media/vs-storage-webjobs-getting-started-tables/ingressprogress.png)

Cuando finaliza la función, la página **Detalles de invocación** informa la cantidad de filas escritas.

![Función de entrada finalizada](./media/vs-storage-webjobs-getting-started-tables/ingresssuccess.png)

## Cómo leer varias entidades desde una tabla

Para leer una tabla, use el atributo **Table** con un parámetro **IQueryable<T>** donde el tipo **T** se deriva de **TableEntity** o implementa **ITableEntity**.

El ejemplo de código siguiente lee y registra todas las filas de la tabla **Ingress**:

		public static void ReadTable(
		    [Table("Ingress")] IQueryable<Person> tableBinding,
		    TextWriter logger)
		{
		    var query = from p in tableBinding select p;
		    foreach (Person person in query)
		    {
		        logger.WriteLine("PK:{0}, RK:{1}, Name:{2}",
		            person.PartitionKey, person.RowKey, person.Name);
		    }
		}

### Cómo leer una entidad única de una tabla

Existe un constructor de atributo **Table** con dos parámetros adicionales que le permiten especificar la clave de partición y la clave de fila cuando quiera enlazar a una entidad de tabla única.

El siguiente ejemplo de código lee una fila de una tabla de una entidad **Person** según los valores de clave de partición y clave de fila recibidos en un mensaje en cola:

		public static void ReadTableEntity(
		    [QueueTrigger("inputqueue")] Person personInQueue,
		    [Table("persontable","{PartitionKey}", "{RowKey}")] Person personInTable,
		    TextWriter logger)
		{
		    if (personInTable == null)
		    {
		        logger.WriteLine("Person not found: PK:{0}, RK:{1}",
		                personInQueue.PartitionKey, personInQueue.RowKey);
		    }
		    else
		    {
		        logger.WriteLine("Person found: PK:{0}, RK:{1}, Name:{2}",
		                personInTable.PartitionKey, personInTable.RowKey, personInTable.Name);
		    }
		}


La clase **Person** de este ejemplo no debe implementar **ITableEntity**.

## Cómo usar la API de almacenamiento .NET directamente para trabajar con una tabla

También puede usar el atributo **Table** con un objeto **CloudTable** para obtener más flexibilidad en el trabajo con una tabla.

El siguiente ejemplo de código usa un objeto **CloudTable** para agregar una entidad única a la tabla *Ingress*.

		public static void UseStorageAPI(
		    [Table("Ingress")] CloudTable tableBinding,
		    TextWriter logger)
		{
		    var person = new Person()
		        {
		            PartitionKey = "Test",
		            RowKey = "100",
		            Name = "Name"
		        };
		    TableOperation insertOperation = TableOperation.Insert(person);
		    tableBinding.Execute(insertOperation);
		}

Para más información sobre cómo usar el objeto **CloudTable**, vea [Introducción al Almacenamiento de tablas de Azure mediante .NET](storage-dotnet-how-to-use-tables.md).

## Temas relacionados tratados en el artículo de procedimientos de las colas

Para obtener información sobre cómo controlar el procesamiento de tablas desencadenado por un mensaje de cola o para ver los escenarios de SDK de WebJobs no específicos para el procesamiento de tablas, consulte [Introducción al Almacenamiento de colas de Azure y servicios conectados de Visual Studio (proyectos de WebJobs)](vs-storage-webjobs-getting-started-queues.md).



## Pasos siguientes

En este artículo se han proporcionado ejemplos de código que muestran cómo tratar escenarios comunes para trabajar con tablas de Azure. Para obtener más información acerca de cómo usar el SDK de WebJobs y WebJobs de Azure, consulte [Recursos de documentación de WebJobs de Azure](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=AcomDC_0224_2016-->