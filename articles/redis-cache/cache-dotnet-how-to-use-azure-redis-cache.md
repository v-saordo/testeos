<properties 
	pageTitle="Uso de Caché en Redis de Azure" 
	description="Obtener más información acerca de cómo mejorar el rendimiento de sus aplicaciones de Azure con Caché en Redis de Azure" 
	services="redis-cache,app-service" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="01/21/2016" 
	ms.author="sdanie"/>

# Uso de Caché en Redis de Azure

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

En esta guía se muestra cómo empezar a usar **Caché en Redis de Azure**. Caché en Redis de Microsoft Azure se basa en la conocida Caché en Redis de código fuente abierto. Le proporciona acceso a una caché en Redis segura y dedicada, administrada por Microsoft. Una caché creada usando Caché en Redis de Azure es accesible desde cualquier aplicación dentro de Microsoft Azure.

Caché en Redis de Microsoft Azure está disponible en los siguientes niveles:

-	**Básico** – Nodo único. Varios tamaños de hasta 53 GB.
-	**Estándar** – Principal/Réplica de dos nodos. Varios tamaños de hasta 53 GB. Contrato de nivel de servicio del 99,9 %.
-	**Premium**: principal/réplica de dos nodos con hasta 10 particiones. Varios tamaños, desde 6 GB a 530 GB (póngase en contacto con nosotros para obtener más información). Todas las características del nivel Estándar y otras más, incluida la compatibilidad con los [clústeres de Redis](cache-how-to-premium-clustering.md), la [persistencia de Redis](cache-how-to-premium-persistence.md) y la [red virtual de Azure](cache-how-to-premium-vnet.md). Contrato de nivel de servicio del 99,9 %.

Estos niveles difieren en las características y el precio. Para obtener información sobre los precios, consulte los [Detalles de precios de caché][].

Esta guía muestra cómo usar el cliente [StackExchange.Redis][] con el código C#. Entre los escenarios tratados se incluyen la **creación y configuración de una caché**, la **configuración de clientes de caché** y la **adición y eliminación de objetos de la memoria caché**. Para obtener más información acerca del uso de Caché en Redis de Azure, consulte la sección [Pasos siguientes][].

<a name="getting-started-cache-service"></a>
## Introducción a Caché en Redis de Azure

Ponerse en marcha con Caché en Redis de Azure es fácil. En primer lugar, tiene que aprovisionar y configurar una caché. A continuación, debe configurar los clientes de caché para que puedan obtener acceso a la caché. Una vez que los clientes de caché estén configurados, ya puede empezar a trabajar con ellos.

-	[Creación de la memoria caché][]
-	[Configuración de los clientes de caché][]

<a name="create-cache"></a>
## Creación de una caché

Para crear una memoria caché, primero inicie sesión en el [Portal de Azure][] y haga clic en **Nuevo**, **Datos y almacenamiento** y **Caché en Redis**.

>[AZURE.NOTE] Además de crear memorias caché en el Portal de Azure, también puede crearlas mediante las plantillas de ARM, PowerShell o la CLI de Azure.
>
>-	Para crear una caché mediante plantillas ARM, consulte [Creación de una caché en Redis mediante una plantilla](cache-redis-cache-arm-provision.md).
>-	Para crear una caché con Azure PowerShell, consulte [Administración de Caché en Redis de Azure con Azure PowerShell](cache-howto-manage-redis-cache-powershell.md).
>-	Para crear una caché mediante la CLI de Azure, consulte [Creación y administración de Caché en Redis de Azure mediante la interfaz de línea de comandos de Azure (CLI de Azure)](cache-manage-cli.md).

![New cache][NewCacheMenu]

>[AZURE.NOTE] En caso de no tener ninguna cuenta de Azure, puede crear una gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure][].

En la hoja **Nueva caché en Redis**, especifique la configuración que desee para la memoria caché.

![Create cache][CacheCreate]

-	En **Nombre DNS**, escriba el nombre de la memoria caché que se va a usar para el punto de conexión de la caché. El nombre de la memoria caché debe ser una cadena de entre 1 y 63 caracteres, y que solo contenga números, letras y el carácter `-`. El nombre de la memoria caché no puede comenzar ni terminar por el carácter `-` y no son válidos varios caracteres `-` consecutivos.
-	En **Suscripción**, seleccione la suscripción de Azure que desee utilizar para la memoria caché. Si su cuenta solo dispone de una suscripción, esta se seleccionará automáticamente y no aparecerá la lista desplegable **Suscripción**.
-	En **Grupo de recursos**, seleccione o cree un grupo de recursos para su caché. Para obtener más información, consulte [Uso de grupos de recursos para administrar los recursos de Azure][]. 
-	Use **Ubicación** para especificar la ubicación geográfica en la que se hospeda su caché. Para optimizar el rendimiento, Microsoft recomienda encarecidamente que cree la memoria caché en la misma región que la aplicación cliente de caché.
-	Use **Nivel de precios** para seleccionar el tamaño y las características de caché que desee.
-	**Redis clúster** le permite crear cachés más grandes de 53 GB y los datos de partición entre varios nodos de Redis. Para obtener más información, consulte [Cómo configurar la agrupación en clústeres para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-clustering.md).
-	**Persistencia de Redis** ofrece la posibilidad de conservar la memoria caché para una cuenta de Almacenamiento de Azure. Para obtener instrucciones sobre cómo configurar la persistencia, consulte [Configuración de la persistencia para una Caché en Redis de Azure de nivel Premium](cache-how-to-premium-persistence.md).
-	**Red virtual** ofrece seguridad y aislamiento mejorados al restringir el acceso a la memoria caché solo a los clientes dentro de la red virtual de Azure especificada. Además, puede usar todas las características de la red virtual, como las subredes y las directivas de control de acceso, entre otras, para restringir aún más el acceso a Redis. Para obtener más información, consulte [Cómo configurar la compatibilidad de red virtual para una memoria Caché en Redis de Azure Premium](cache-how-to-premium-vnet.md).
-	Use **Diagnósticos** para especificar una cuenta de almacenamiento para las métricas de la memoria caché. Para obtener más información sobre cómo configurar y ver las métricas de la memoria caché, consulte [Supervisión de Caché en Redis de Azure](cache-how-to-monitor.md).

Una vez que las nuevas opciones de caché estén configuradas, haga clic en **Crear**. La creación de la caché puede tardar unos minutos. Para comprobar el estado, puede supervisar el progreso en el panel de inicio. Una vez que se cree la memoria caché, esta presentará el estado **En ejecución** y estará lista para usarse con la configuración predeterminada.

![Cache created][CacheCreated]

Una vez creada su caché, podrá acceder a ella desde la hoja **Examinar**.

![Browse blade][BrowseCaches]

Haga clic en **Cachés en Redis** para ver sus cachés.

![Caches][Caches]

<a name="NuGet"></a>
## Configuración de los clientes de caché

Una caché creada usando Caché en Redis de Azure es accesible desde cualquier aplicación de Azure. Las aplicaciones .NET desarrolladas en Visual Studio pueden usar el cliente de caché **StackExchange.Redis**, que se puede configurar usando un paquete NuGet que simplifica la configuración de aplicaciones cliente de caché.

>[AZURE.NOTE] Para obtener más información, consulte la página Github [StackExchange.Redis][] y la [documentación del cliente de caché StackExchange.Redis][].

Para configurar una aplicación cliente en Visual Studio usando el paquete NuGet StackExchange.Redis, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**.

![Manage NuGet packages][NuGetMenu]

Escriba **StackExchange.Redis** o **StackExchange.Redis.StrongName** en el cuadro de texto **Buscar en línea**, seleccione la versión que desee en los resultados y haga clic en **Instalar**.

>[AZURE.NOTE] Si prefiere usar una versión con nombre seguro de la biblioteca de cliente **StackExchange.Redis**, elija **StackExchange.Redis.StrongName**; de lo contrario, elija **StackExchange.Redis**.

![StackExchange.Redis NuGet package][StackExchangeNuget]

El paquete de NuGet se descarga y agrega las referencias de ensamblado requeridas para que la aplicación cliente acceda a Caché en Redis de Azure con el cliente de caché StackExchange.Redis.

Cuando el proyecto de cliente ya está configurado para el almacenamiento en caché, puede usar las técnicas descritas en las siguientes secciones para trabajar con la caché.

<a name="working-with-caches"></a>
## Trabajo con cachés

En esta sección se describe cómo realizar tareas comunes con el servicio de caché.

-	[Conexión a la memoria caché][]
-   [Agregación y recuperación de objetos de la memoria caché][]
-   [Trabajar con objetos .NET en la memoria caché](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## Conexión a la memoria caché

Para trabajar con una caché mediante programación, necesita una referencia a la misma. Agregue lo siguiente a la parte superior de cualquier archivo del que desea usar el cliente StackExchange.Redis para acceder a una Caché en Redis de Azure.

    using StackExchange.Redis;

>[AZURE.NOTE] El cliente StackExchange.Redis requiere .NET Framework 4 o superior.

La clase `ConnectionMultiplexer` administra la conexión con Caché en Redis de Azure. Esta clase está diseñada para compartirse y reusarse a través de su aplicación cliente y no necesita crearse basándose en operación.

Para conectarse a una Caché en Redis de Azure y que se devuelva una instancia de `ConnectionMultiplexer`, llame al método estático `Connect` y pase el extremo y la clave de caché como en el siguiente ejemplo. Use la clave generada desde el Portal de Azure como parámetro de contraseña.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] Advertencia: nunca almacene credenciales en el código fuente. Para que este ejemplo resulte sencillo, se muestra en código fuente. Consulte [Cómo funcionan las cadenas de aplicación y las cadenas de conexión][] para obtener información sobre cómo almacenar credenciales.

Si no desea usar SSL, establezca `ssl=false` u omita el parámetro `ssl`.

>[AZURE.NOTE] El puerto no SSL está deshabilitado de forma predeterminada para las cachés nuevas. Para obtener instrucciones acerca de cómo habilitar el puerto no SSL, consulte [Puertos de acceso](cache-configure.md#access-ports).

Un enfoque para compartir una instancia `ConnectionMultiplexer` en su aplicación es tener una propiedad estática que devuelva una instancia conectada, similar al ejemplo siguiente. Esto proporciona una manera segura para subprocesos para inicializar una sola instancia`ConnectionMultiplexer` conectada. En estos ejemplos `abortConnect` está establecida en falso, lo que significa que la llamada se realizará correctamente incluso si no se establece ninguna conexión a la Caché en Redis de Azure. Una característica clave de `ConnectionMultiplexer` es que restaurará automáticamente la conectividad a la memoria caché una vez que el problema de red u otras causas se hayan resuelto.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

Para obtener más información sobre las opciones de configuración de conexión avanzadas, consulte [Modelo de configuración de StackExchange.Redis][].

El extremo y las claves de caché se pueden obtener del cuadro de la hoja **Caché en Redis** para su instancia de caché.

![Cache properties][CacheProperties]

![Manage keys][ManageKeys]

Una vez establecida la conexión, devuelva una referencia a la base de datos de caché en Redis llamando al método `ConnectionMultiplexer.GetDatabase`. El objeto devuelto desde el método `GetDatabase` es un objeto de paso a través ligero y no necesita almacenarse.

	// Connection refers to a property that returns a ConnectionMultiplexer
	// as shown in the previous example.
	IDatabase cache = Connection.GetDatabase();

	// Perform cache operations using the cache object...
	// Simple put of integral data types into the cache
	cache.StringSet("key1", "value");
	cache.StringSet("key2", 25);

	// Simple get of data types from the cache
	string key1 = cache.StringGet("key1");
	int key2 = (int)cache.StringGet("key2");

Ahora que sabe cómo conectarse a una instancia de Caché en Redis de Azure y devolver una referencia a la base de datos de caché, echemos un vistazo a cómo se trabaja con la memoria caché.

<a name="add-object"></a>
## Agregación y recuperación de objetos de la memoria caché

Los elementos se pueden almacenar en una memoria caché y recuperarse de esta usando los métodos `StringSet` y `StringGet`.

	// If key1 exists, it is overwritten.
	cache.StringSet("key1", "value1");

	string value = cache.StringGet("key1");

Redis almacena la mayoría de los datos como cadenas Redis, pero estas cadenas pueden contener muchos tipos de datos, como por ejemplo datos binarios serializados, que se pueden usar cuando se almacenan objetos .NET en caché.

Cuando llame a `StringGet`, si el objeto existe, se devuelve y, si no existe, se devuelve `null`. En este caso, puede recuperar el valor desde el origen de datos que desee y almacenarlo en la memoria caché para su uso posterior. Esto se conoce como patrón cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Para especificar la expiración de un elemento en la memoria caché, use el parámetro `TimeSpan` de `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## Trabajar con objetos .NET en la memoria caché

Caché en Redis de Azure puede almacenar en caché objetos .NET así como tipos de datos primitivos, pero antes de poder almacenar un objeto .NET en caché, se debe serializar. Esta es la responsabilidad del desarrollador de la aplicación, que tiene total flexibilidad a la hora de elegir el serializador.

Una manera sencilla para serializar objetos es usar los métodos de serialización `JsonConvert` en [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) y serializar a y desde JSON. En el ejemplo siguiente se muestra un get y set con una instancia de objeto `Employee`.


	class Employee
	{
	    public int Id { get; set; }
	    public string Name { get; set; }
	
	    public Employee(int EmployeeId, string Name)
	    {
	        this.Id = EmployeeId;
	        this.Name = Name;
	    }
	}

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos, siga estos vínculos para obtener más información sobre Caché en Redis de Azure.

-	Consulte los proveedores de ASP.NET para Caché en Redis de Azure.
	-	[Proveedor de estado de sesión de Redis de Azure](cache-asp.net-session-state-provider.md)
	-	[Proveedor de caché de resultados de ASP.NET de caché en Redis de Azure](cache-asp.net-output-cache-provider.md)
-	[Habilite los diagnósticos de caché](cache-how-to-monitor.md#enable-cache-diagnostics) para que pueda [supervisar](cache-how-to-monitor.md) el estado de la memoria caché. Puede ver las métricas en el Portal de Azure y también [descargarlas y revisarlas](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) mediante el uso de las herramientas que prefiera.
-	Compruebe la [documentación del cliente de caché StackExchange.Redis][].
	-	Se puede obtener acceso a Caché en Redis de Azure desde numerosos clientes Redis e idiomas de desarrollo. Para obtener más información, vea [http://redis.io/clients][] y [Desarrollar en otros idiomas para Caché en Redis de Azure][].
	-	Caché en Redis de Azure también se puede usar con servicios como Redsmin. Para obtener más información, vea [Recuperación de una cadena de conexión de Redis de Azure y su uso con Redsmin][].
-	Vea la documentación de [Redis][] y lea la información sobre los [tipos de datos Redis][] y una [introducción de quince minutos para tipos de datos Redis][].



<!-- INTRA-TOPIC LINKS -->
[Pasos siguientes]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Creación de la memoria caché]: #create-cache
[Configure the cache]: #enable-caching
[Configuración de los clientes de caché]: #NuGet
[Working with Caches]: #working-with-caches
[Conexión a la memoria caché]: #connect-to-cache
[Agregación y recuperación de objetos de la memoria caché]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session

  
<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png

[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png

[CacheCreated]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png




   
<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Desarrollar en otros idiomas para Caché en Redis de Azure]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Recuperación de una cadena de conexión de Redis de Azure y su uso con Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modelo de configuración de StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Detalles de precios de caché]: http://www.windowsazure.com/pricing/details/cache/
[Portal de Azure]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Uso de grupos de recursos para administrar los recursos de Azure]: http://azure.microsoft.com/documentation/articles/resource-group-overview/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[documentación del cliente de caché StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[tipos de datos Redis]: http://redis.io/topics/data-types
[introducción de quince minutos para tipos de datos Redis]: http://redis.io/topics/data-types-intro

[Cómo funcionan las cadenas de aplicación y las cadenas de conexión]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/

[Evaluación gratuita de Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero

<!---HONumber=AcomDC_0309_2016-->