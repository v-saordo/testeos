<properties
	pageTitle="Tutorial de NoSQL: SDK de DocumentDB para .NET | Microsoft Azure"
	description="Tutorial de NoSQL que crea una base de datos en línea y la aplicación de consola de C# mediante el SDK de DocumentDB para .NET. DocumentDB es una base de datos NoSQL para JSON."
	keywords="tutorial de nosql, base de datos en línea, aplicación de consola de c#"
	services="documentdb"
	documentationCenter=".net"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article" 
	ms.date="02/19/2016"
	ms.author="anhoh"/>

# Tutorial de NoSQL: Crear una aplicación de consola de C# DocumentDB 

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Bienvenido al tutorial de NoSQL para el SDK de DocumentDB para .NET. Después de seguir este tutorial, tendrá una aplicación de consola que crea recursos de DocumentDB y realiza consultas en ellos.

Describiremos:

- Creación y aprovisionamiento una cuenta de DocumentDB.
- Configuración de la solución de Visual Studio
- Creación de una base de datos en línea
- Creación de una colección
- Creación de documentos JSON
- Consulta de la colección
- Eliminación de la base de datos

¿No tiene tiempo? ¡No se preocupe! La solución completa está disponible en [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). Consulte [Obtención de la solución completa](#GetSolution) para obtener instrucciones rápidas.

Después, utilice los botones de votación situados en la parte superior o inferior de esta página para proporcionarnos sus comentarios. Si quiere que nos pongamos en contacto directamente con usted, puede incluir su dirección de correo electrónico en los comentarios.

Comencemos.

## Requisitos previos

Asegúrese de que dispone de lo siguiente:

- Una cuenta de Azure activa. Si no tiene una, puede registrarse para obtener una versión de [prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [Visual Studio 2013/Visual Studio 2015](http://www.visualstudio.com/).

## Paso 1: Creación de una cuenta de DocumentDB

Creemos una cuenta de DocumentDB. Si ya tiene una cuenta que desea usar, puede ir directamente a [Configuración de la solución de Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> Paso 2: Configuración de la solución de Visual Studio

1. Abra **Visual Studio** en el equipo.
2. En el menú **Archivo**, seleccione **Nuevo** y elija **Proyecto**.
3. En el cuadro de diálogo **Nuevo proyecto**, seleccione **Plantillas**/**Visual C#**/ **Aplicación de consola**, ponga un nombre al proyecto y haga clic en **Aceptar**.
4. En el **Explorador de soluciones**, haga clic con el botón secundario en la nueva aplicación de la consola, que se encuentra en la solución de Visual Studio.
5. A continuación, sin dejar el menú, haga clic en **Administrar paquetes de NuGet...**
6. En el panel de la izquierda de la ventana **Administrar paquetes de NuGet**, haga clic en **En línea** / **nuget.org**.
7. En el cuadro de entrada **Buscar en línea**, busque la **biblioteca de cliente de DocumentDB**.
8. En los resultados, busque la **biblioteca de cliente de Microsoft Azure DocumentDB** y haga clic en **Instalar**. El identificador de paquete de la biblioteca de cliente de DocumentDB es [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB).

Estupendo. Ahora que hemos terminado la configuración, comencemos a escribir algo de código.

##<a id="Connect"></a> Paso 3: Conexión a una cuenta de DocumentDB

En primer lugar, agregue estas referencias al principio de la aplicación de C#, en el archivo Program.cs:

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

> [AZURE.IMPORTANT] Para poder completar este tutorial de NoSQL, asegúrese de que agregar las dependencias anteriores.

A continuación, guarde el punto de conexión de la cuenta de DocumentDB y la clave de acceso principal o secundaria, que se encuentran en el [Portal de Azure](https://portal.azure.com).

![Captura de pantalla del Portal de Azure usada por el tutorial de NoSQL para crear una aplicación de consola de C#. Muestra una cuenta de DocumentDB, con el concentrador ACTIVO resaltado, el botón CLAVES resaltado en la hoja de cuenta de DocumentDB y los valores URI, CLAVE PRINCIPAL y CLAVE SECUNDARIA resaltados en la hoja Claves.][keys]

    private const string EndpointUrl = "<your endpoint URI>";
    private const string AuthorizationKey = "<your key>";

Comenzaremos la aplicación de demostración de introducción creando una nueva instancia de **DocumentClient**. Cree una nueva tarea asincrónica llamada **GetStartedDemo** y cree una instancia de nuestro nuevo **DocumentClient**.

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

Llame a la tarea asincrónica desde el método **Main** similar al código siguiente.

	public static void Main(string[] args)
    {
		try
    	{
        	GetStartedDemo().Wait();
		}
		catch (Exception e)
		{
			Exception baseException = e.GetBaseException();
			Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
		}
	}

> [AZURE.WARNING] Nunca almacene credenciales en código fuente. Para que este ejemplo resulte sencillo, las credenciales se muestran en el código fuente. Consulte [Sitios web de Azure: funcionamiento de las cadenas de aplicación y de las cadenas de conexión](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) para obtener información sobre cómo almacenar credenciales en un entorno de producción. Eche un vistazo a nuestra aplicación de ejemplo de [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs) para obtener un ejemplo sobre cómo almacenar las credenciales fuera del código fuente.

Ahora que sabe cómo conectarse a una cuenta de DocumentDB y crear una instancia de la clase **DocumentClient**, vamos a echar un vistazo a cómo trabajar con recursos de DocumentDB.

## Paso 4: Creación de una base de datos en línea
Puede crearse una [base de datos](documentdb-resources.md#databases) de DocumentDB mediante el método [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) de la clase **DocumentClient**. Una base de datos es un contenedor lógico de almacenamiento de documentos JSON particionado en recopilaciones. Cree la base de datos en su método **GetStartedDemo** después de su creación **DocumentClient**.

	// Check to verify a database with the id=FamilyRegistry does not exist
	Database database = client.CreateDatabaseQuery().Where(db => db.Id == "FamilyRegistry").AsEnumerable().FirstOrDefault();

	// If the database does not exist, create a new database
    if (database == null)
    {
    	database = await client.CreateDatabaseAsync(
    		new Database
            {
            	Id = "FamilyRegistry"
            });

		// Write the new database's id to the console
		Console.WriteLine(database.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateColl"></a>Paso 5: Creación de una colección  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** creará una nueva colección de S1, que tiene implicaciones de precios. Para obtener más información, visite nuestra [página de precios](https://azure.microsoft.com/pricing/details/documentdb/).

Una [colección](documentdb-resources.md#collections) puede crearse mediante el método [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) de la clase **DocumentClient**. Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript. La colección recién creada se asignará a un [nivel de rendimiento S1](documentdb-performance-levels.md). Cree una nueva colección llamada **FamilyCollection** después de crear la base de datos en el método **GetStartedDemo**.

    // Check to verify a document collection with the id=FamilyCollection does not exist
    DocumentCollection documentCollection = client.CreateDocumentCollectionQuery("dbs/" + database.Id).Where(c => c.Id == "FamilyCollection").AsEnumerable().FirstOrDefault();

	// If the document collection does not exist, create a new collection
    if (documentCollection == null)
    {
    	documentCollection = await client.CreateDocumentCollectionAsync("dbs/" + database.Id,
        	new DocumentCollection
            {
            	Id = "FamilyCollection"
            });

		// Write the new collection's id to the console
		Console.WriteLine(documentCollection.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateDoc"></a>Paso 6: Creación de documentos JSON
Un [documento](documentdb-resources.md#documents) puede crearse mediante el método [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) de la clase **DocumentClient**. Los documentos son contenido JSON definido por el usuario (arbitrario). Ahora podemos insertar uno o varios documentos. Si ya dispone de datos que desea almacenar en la base de datos, puede usar la [herramienta de migración de datos](documentdb-import-data.md) de DocumentDB.

En primer lugar, debemos crear una clase **principal**, **secundaria**, **mascota**, **dirección** y **familia**. Para agregar estas clases, agregue las siguientes subclases internas después del método **GetStartedDemo**.

    internal sealed class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    internal sealed class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    internal sealed class Pet
    {
        public string GivenName { get; set; }
    }

    internal sealed class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

    internal sealed class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
    }

A continuación, cree los documentos dentro de su método asincrónico **GetStartedDemo**.

    // Check to verify a document with the id=AndersenFamily does not exist
    Document document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "AndersenFamily").AsEnumerable().FirstOrDefault();

	// If the document does not exist, create a new document
	if (document == null)
	{
	    // Create the Andersen Family document
	    Family andersonFamily = new Family
	    {
	        Id = "AndersenFamily",
	        LastName = "Andersen",
	        Parents = new Parent[] {
	            new Parent { FirstName = "Thomas" },
	            new Parent { FirstName = "Mary Kay"}
	        },
	        Children = new Child[] {
	            new Child
	            { 
	                FirstName = "Henriette Thaulow", 
	                Gender = "female", 
	                Grade = 5, 
	                Pets = new Pet[] {
	                    new Pet { GivenName = "Fluffy" } 
	                }
	            } 
	        },
	        Address = new Address { State = "WA", County = "King", City = "Seattle" },
	        IsRegistered = true
	    };
	
	    // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
	    await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, andersonFamily);
	}

    // Check to verify a document with the id=AndersenFamily does not exist
    document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "WakefieldFamily").AsEnumerable().FirstOrDefault();

    if (document == null)
    {
        // Create the WakeField document
        Family wakefieldFamily = new Family
        {
            Id = "WakefieldFamily",
            Parents = new Parent[] {
                new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
                new Parent { FamilyName= "Miller", FirstName= "Ben" }
            },
            Children = new Child[] {
                new Child {
                    FamilyName= "Merriam", 
                    FirstName= "Jesse", 
                    Gender= "female", 
                    Grade= 8,
                    Pets= new Pet[] {
                        new Pet { GivenName= "Goofy" },
                        new Pet { GivenName= "Shadow" }
                    }
                },
                new Child {
                    FamilyName= "Miller", 
                    FirstName= "Lisa", 
                    Gender= "female", 
                    Grade= 1
                }
            },
            Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
            IsRegistered = false
        };

        // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
        await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, wakefieldFamily);
	}

Ahora ha creado la siguiente colección, documentos y bases de datos en la cuenta de DocumentDB.

![Diagrama que muestra la relación jerárquica entre la cuenta, la base de datos en línea, la colección y los documentos usados por el tutorial NoSQL para crear una aplicación de consola de C#.](./media/documentdb-get-started/nosql-tutorial-account-database.png)

##<a id="Query"></a>Paso 7: Consulta de recursos de DocumentDB

DocumentDB admite [consultas](documentdb-sql-query.md) enriquecidas contra los documentos JSON almacenados en cada colección. En el siguiente ejemplo se muestran diversas consultas (usando tanto la sintaxis SQL de Base de datos de documentos como LINQ) que podemos ejecutar con los documentos que hemos insertado en el paso anterior. Agregue estas consultas para su método asincrónico **GetStartedDemo**.

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = "AndersenFamily"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

El siguiente diagrama muestra la denominación de la sintaxis de la consulta de SQL DocumentDB en la recopilación creada y se aplica la misma lógica también a la consulta LINQ.

![Diagrama que ilustra el ámbito y el significado de la consulta usada por el tutorial de NoSQL para crear una aplicación de consola de C#.](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

La palabra clave [FROM](documentdb-sql-query.md#from-clause) es opcional en la consulta porque las consultas de DocumentDB ya tienen como ámbito una sola colección. Por lo tanto, «FROM Families f" se puede intercambiar por "FROM root r", o cualquier otra variable de nombre que elija. DocumentDB deducirá la familia, la raíz o el nombre de variable elegido y hará referencia a la colección actual de forma predeterminada.

##<a id="DeleteDatabase"></a>Paso 8: Eliminación de la base de datos en línea

La eliminación de la base de datos creada quitará la base de datos y todos los recursos secundarios (colecciones, documentos, etc.). Puede eliminar la base de datos y el cliente de documento agregando el siguiente fragmento de código al final del método asincrónico **GetStartedDemo**.

    // Clean up/delete the database
    await client.DeleteDatabaseAsync("dbs/" + database.Id);
	client.Dispose();

##<a id="Run"></a>Paso 9: Ejecución de la aplicación de consola de C#

Ahora ya puede ejecutar la aplicación. Al final de su método **principal**, agregue la siguiente línea de código, que le permitirá leer la salida de la consola antes de que la aplicación deje de ejecutarse.

	Console.ReadLine();

Presione F5 en Visual Studio para compilar la aplicación en modo de depuración.

Ahora debe ver el resultado de la aplicación iniciada get. La salida mostrará los resultados de las consultas que hemos agregado y debe coincidir con el texto de ejemplo siguiente.

	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
    	{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from SQL
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ query

¡Enhorabuena! Ha completado este tutorial de NoSQL y tiene una aplicación de consola de C# en funcionamiento.

##<a id="GetSolution"></a> Obtener la solución completa del tutorial de NoSQL
Para compilar la solución GetStarted que contiene todos los ejemplos de este artículo, necesitará lo siguiente:

-   [Cuenta de DocumentDB][documentdb-create-account].
-   La solución [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) está disponible en GitHub.

Para restaurar las referencias al SDK de DocumentDB para .NET en Visual Studio, haga clic con el botón derecho en la solución **GetStarted** en el Explorador de soluciones y, después, haga clic en **Habilitar la restauración del paquete NuGet**. A continuación, en el archivo App.config, actualice los valores EndpointUrl y AuthorizationKey como se describe en [Conexión a una cuenta de DocumentDB](#Connect).

## Pasos siguientes

-   ¿Desea un tutorial de ASP.NET MVC NoSQL más complejo? Consulte [Creación de una aplicación web con ASP.NET MVC mediante DocumentDB](documentdb-dotnet-application.md).
-	Aprenda a [supervisar una cuenta de DocumentDB](documentdb-monitor-accounts.md).
-	Ejecute las consultas en nuestro conjunto de datos de ejemplo en el [área de consultas](https://www.documentdb.com/sql/demo).
-	Obtenga más información sobre el modelo de programación en la sección sobre desarrollo de la [página de documentación de DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png
 

<!---HONumber=AcomDC_0224_2016-->