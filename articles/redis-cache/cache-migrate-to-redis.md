<properties 
    pageTitle="Migración de la caché a Redis"
    description="Obtenga información sobre cómo migrar las aplicaciones del Servicio de caché administrado a Caché en Redis de Azure"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="dwrede"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="12/03/2015"
    ms.author="sdanie" />

# Migración desde el Servicio de caché administrado a Caché en Redis de Azure

La migración de aplicaciones que usan el Servicio de caché administrado de Azure a Caché en Redis de Azure se puede realizar con unos cambios mínimos en la aplicación, según las características del Servicio de caché administrado usadas por la aplicación de almacenamiento en caché. Si bien las API no son exactamente iguales, son parecidas, y gran parte del código existente que usa el Servicio de caché administrado para tener acceso a una caché se puede reutilizar con cambios mínimos. En este tema se muestra como realizar los cambios necesarios de configuración y en las aplicaciones para migrar las aplicaciones del Servicio de caché administrado para usar Caché de Redis en Azure. También se muestra como algunas de las características de la Caché en Redis de Azure se pueden usar para implementar la funcionalidad de una caché del Servicio de caché administrado.

## Pasos de migración

A continuación se describen los pasos necesarios para migrar una aplicación del Servicio de caché administrado para usar Caché en Redis de Azure.

-	Asignación de características del Servicio de caché administrado a Caché en Redis de Azure
-	Elegir una oferta de caché
-	Creación de una caché
-	Configuración de los clientes de caché
	-	Eliminación de la configuración del Servicio de caché administrado
	-	Configuración de un cliente de caché mediante el paquete NuGet StackExchange.Redis
-	Migración del código del Servicio de caché administrado
	-	Conexión a la caché mediante la clase ConnectionMultiplexer
	-	Acceder a tipos de datos primitivos en la caché
	-	Trabajar con objetos .NET en la memoria caché
-	Migrar el estado de sesión de ASP.NET y el almacenamiento en caché de resultados a Caché en Redis de Azure 

## Asignación de características del Servicio de caché administrado a Caché en Redis de Azure

El Servicio de caché administrado de Azure y Caché en Redis de Azure guardan ciertas semejanzas pero implementan algunas de sus características de formas diferentes. En esta sección se describen algunas de las diferencias y se proporcionan instrucciones para la implementación de las características del Servicio de caché administrado en Caché en Redis de Azure.

|Características del Servicio de caché administrado|Compatibilidad con el Servicio de caché administrado|Compatibilidad con Caché en Redis de Azure|
|---|---|---|
|Cachés con nombre|Se configura una caché predeterminada y, en las ofertas de caché Estándar y Premium, se pueden configurar, si se desea, hasta nueve cachés con nombre adicionales.|Las cachés en Redis de Azure tienen 16 bases de datos que se pueden usar para implementar una funcionalidad parecida en las cachés con nombre. Para obtener más información, consulte [Configuración de servidor predeterminada en Redis](cache-configure.md#default-redis-server-configuration).|
|Alta disponibilidad|Proporciona alta disponibilidad para los elementos de la caché en las ofertas de caché Estándar y Premium. Si los elementos se pierden debido a un error, aún se puede acceder a las copias de seguridad de los elementos en la caché. Las escrituras en la caché secundaria se realizan de manera sincrónica.|Existe alta disponibilidad en las ofertas de caché Estándar y Premio, que tienen una configuración de réplica-principal de dos nodos (cada partición de una caché Premium tiene un par de réplica-principal). Las escrituras en la réplica se realizan de forma asincrónica. Para obtener más información, consulte [Precios de Caché en Redis de Azure](https://azure.microsoft.com/pricing/details/cache/).|
|Notificaciones|Permite a los clientes recibir notificaciones asincrónicas cuando se producen diversas operaciones de caché en una caché con nombre.|Las aplicaciones cliente pueden usar pub/sub de Redis o [notificaciones de Keyspace](cache-configure.md#keyspace-notifications-advanced-settings) para lograr una funcionalidad similar a las notificaciones.|
|Caché local|Almacena una copia de los objetos en caché de forma local en el cliente para un acceso a un más rápido.|Las aplicaciones cliente deberán implementar esta funcionalidad mediante un diccionario o una estructura de datos parecida.|
|Directiva de expulsión|Ninguna o LRU. La directiva predeterminada es LRU.|Caché en Redis de Azure admite las siguientes directivas de expulsión: volatile-lru, allkeys-lru, volatile-random, allkeys-random, volatile-ttl, noeviction. La directiva predeterminada es volatile-lru. Para obtener más información, consulte [Configuración de servidor predeterminada en Redis](cache-configure.md#default-redis-server-configuration).|
|Directiva de caducidad|La directiva de caducidad predeterminada es Absoluta y el intervalo de caducidad predeterminado es de diez minutos. También hay directivas variable y Nunca.|De forma predeterminada, los elementos de la caché no caducan, pero se puede configurar una caducidad por escritura mediante el uso de sobrecargas de conjunto de caché. Para obtener más información, consulte [Incorporación y recuperación de objetos en la caché](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache).|
|Regiones y etiquetado|Las regiones son subgrupos de elementos en caché. Las regiones también admiten la anotación de elementos en caché con cadenas descriptivas adicionales llamadas etiquetas. Además, ofrecen la posibilidad de realizar operaciones de búsqueda en elementos etiquetados en dicha región. Todos los elementos de una región se encuentran en un único nodo del clúster de caché.|Una caché en Redis consta de un único nodo (a menos que el clúster Redis esté habilitado), por lo que no se aplica el concepto de regiones del Servicio de caché administrado. Redis admite búsqueda y operaciones con caracteres comodín al recuperar claves, de modo que se pueden insertar etiquetas descriptivas en los nombres de clave y usarlas más adelante para recuperar los elementos. Para ver un ejemplo de la implementación de una solución de etiquetado con Redis, consulte [Implementing cache tagging with Redis](http://stackify.com/implementing-cache-tagging-redis/) (Implementación del etiquetado de caché con Redis).|
|Serialización|La caché administrada admite NetDataContractSerializer, BinaryFormatter y el uso de serializadores personalizados. El valor predeterminado es NetDataContractSerializer.|Es responsabilidad de la aplicación cliente serializar los objetos .NET antes de colocarlos en la caché y dejar en manos del desarrollador de la aplicación cliente la elección del serializador. Para obtener más información y ejemplos de código, consulte [Objetos .NET en la caché](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).|

## Elegir una oferta de caché

Caché en Redis de Microsoft Azure está disponible en los siguientes niveles:

-	**Básico** – Nodo único. Varios tamaños de hasta 53 GB.
-	**Estándar** – Principal/Réplica de dos nodos. Varios tamaños de hasta 53 GB. Contrato de nivel de servicio del 99,9 %.
-	**Premium**: principal/réplica de dos nodos con hasta 10 particiones. Varios tamaños, desde 6 GB a 530 GB (póngase en contacto con nosotros para obtener más información). Todas las características del nivel Estándar y otras más, incluida la compatibilidad con los [clústeres de Redis](cache-how-to-premium-clustering.md), la [persistencia de Redis](cache-how-to-premium-persistence.md) y la [red virtual de Azure](cache-how-to-premium-vnet.md). Contrato de nivel de servicio del 99,9 %.

Estos niveles difieren en las características y el precio. Las características se tratan más adelante en esta guía; por otro lado, para obtener más información acerca de los precios consulte [Detalles de precios de caché](https://azure.microsoft.com/pricing/details/cache/).

Un punto de partida para la migración es seleccionar el tamaño que coincida con el tamaño anterior de la caché del Servicio de caché administrado, y luego escalar o reducir verticalmente en función de los requisitos de la aplicación. Para obtener instrucciones sobre cómo elegir la oferta correcta de Caché en Redis de Azure, consulte [¿Qué oferta y tamaño de Caché en Redis debo usar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Creación de una caché

Se pueden crear cachés en Caché en Redis de Azure en el [Portal de Azure](https://portal.azure.com), o bien mediante el uso de plantillas ARM, PowerShell o la CLI de Azure.

-	Para crear una caché en el Portal de Azure, consulte [Creación de una caché](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).
-	Para crear una caché mediante plantillas ARM, consulte [Creación de una caché en Redis mediante una plantilla](cache-redis-cache-arm-provision.md).
-	Para crear una caché con Azure PowerShell, consulte [Administración de Caché en Redis de Azure con Azure PowerShell](cache-howto-manage-redis-cache-powershell.md).
-	Para crear una caché mediante la CLI de Azure, consulte [Creación y administración de Caché en Redis de Azure mediante la interfaz de línea de comandos de Azure (CLI de Azure)](cache-manage-cli.md).

>[AZURE.NOTE] Para usar Caché en Redis de Azure, necesita una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero).

## Configuración de los clientes de caché

Después de crear y configurar la caché, el siguiente paso consiste en eliminar la configuración del Servicio de caché administrado y agregar la configuración de Caché en Redis de Azure, junto con referencias para que los clientes de caché puedan acceder a ella.

-	Eliminación de la configuración del Servicio de caché administrado
-	Configuración de un cliente de caché mediante el paquete NuGet StackExchange.Redis

### Eliminación de la configuración del Servicio de caché administrado

Para que las aplicaciones cliente se puedan configurar para Caché en Redis de Azure, se debe eliminar la configuración y las referencias de ensamblado existentes del Servicio de caché administrado desinstalando el paquete NuGet del Servicio de caché administrado.

Para desinstalar el paquete NuGet del Servicio de caché administrado, haga clic con el botón derecho en el proyecto de cliente en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**. Seleccione el nodo **Paquetes instalados** y escriba W**indowsAzure.Caching** en el cuadro de búsqueda de paquetes instalados. Seleccione **Windows** **Caché de Azure** (o **Windows** **Servicio de almacenamiento en caché de Azure** según la versión del paquete NuGet), haga clic en **Desinstalar** y luego haga clic en **Cerrar**.

![Desinstalación del paquete NuGet del Servicio de caché administrado de Azure](./media/cache-migrate-to-redis/IC757666.jpg)

Al desinstalar el paquete NuGet del Servicio de caché administrado se eliminan los ensamblados del Servicio de caché administrado y sus entradas en app.config o web.config de la aplicación cliente. Como no es posible eliminar algunos valores de configuración personalizados al desinstalar el paquete NuGet, abra web.config o app.config y asegúrese de que los siguientes elementos se han eliminado completamente.

Asegúrese de que la entrada `dataCacheClients` se ha eliminado del elemento `configSections`. No quite el elemento `configSections` entero; solo la entrada `dataCacheClients`, si existe.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
    </configSections>

Asegúrese de que la sección `dataCacheClients` esté eliminada. La sección `dataCacheClients` se parecerá al ejemplo siguiente.

    <dataCacheClients>
      <dataCacheClientname="default">
        <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
        <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
        <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="true">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

Después de quitar la configuración del Servicio de caché administrado, puede configurar el cliente de caché como se describe en la siguiente sección.

### Configuración de un cliente de caché mediante el paquete NuGet StackExchange.Redis

Las aplicaciones .NET desarrolladas en Visual Studio pueden usar el cliente de caché StackExchange.Redis para tener acceso a sus cachés. Para configurar una aplicación cliente en Visual Studio usando el paquete NuGet StackExchange.Redis, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**.

![Paquetes NuGet de administración de la caché en Redis de Azure](./media/cache-migrate-to-redis/IC729541.png)

Escriba **StackExchange.Redis** en el cuadro de texto **Buscar en línea**, selecciónelo en los resultados y haga clic en **Instalar**.

![Paquete NuGet StackExchange.Redis de Caché en Redis de Azure](./media/cache-migrate-to-redis/IC746253.png)

El paquete de NuGet se descarga y agrega las referencias de ensamblado requeridas para que la aplicación cliente acceda a Caché en Redis de Azure con el cliente de caché StackExchange.Redis.

Para obtener más información, consulte [Configuración de los clientes de caché](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## Migración del código del Servicio de caché administrado

La API del cliente de caché StackExchange.Redis es parecida al Servicio de caché administrado. En esta sección se proporciona un resumen de las diferencias.

### Conexión a la caché mediante la clase ConnectionMultiplexer

En el Servicio de caché administrado, las conexiones a la caché se controlan mediante las clases `DataCacheFactory` y `DataCache`. En Caché en Redis de Azure, estas conexiones se administran con la clase `ConnectionMultiplexer`.

Agregue la siguiente instrucción using en la parte superior de cualquier archivo desde el que desea obtener acceso a la caché.

	using StackExchange.Redis
								
Si no se resuelve este espacio de nombres, asegúrese de que ha agregado el paquete NuGet StackExchange.Redis, como se describe en [Configuración de los clientes de caché](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

>[AZURE.NOTE] Tenga en cuenta que el cliente StackExchange.Redis requiere .NET Framework 4 o superior.

Para conectarse a una instancia de Caché en Redis de Azure, llame a método estático `ConnectionMultiplexer.Connect` y pase el punto de conexión y la clave. Un enfoque para compartir una instancia de `ConnectionMultiplexer` en su aplicación es tener una propiedad estática que devuelva una instancia conectada, como en el ejemplo siguiente. Con ello se proporciona una manera segura para subprocesos de inicializar solo una instancia de `ConnectionMultiplexer` conectada. En este ejemplo, `abortConnect` se establece en false, lo que significa que la llamada se realizará correctamente incluso si no se establece una conexión a la caché. Una de las características principales de `ConnectionMultiplexer` es que restaura automáticamente la conectividad a la caché una vez que el problema de red u otras causas se resuelven.

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

El punto de conexión, las claves y los puertos de la caché se pueden obtener de la hoja **Caché en Redis** de su instancia de caché. Para obtener más información, consulte [Propiedades de Caché en Redis](cache-configure.md#properties).

Después de establecer la conexión, devuelva una referencia a la base de datos de Caché en Redis llamando al método `ConnectionMultiplexer.GetDatabase`. El objeto devuelto desde el método `GetDatabase` es un objeto de paso a través ligero y no necesita almacenarse.

	IDatabase cache = Connection.GetDatabase();
	
	// Perform cache operations using the cache object...
	// Simple put of integral data types into the cache
	cache.StringSet("key1", "value");
	cache.StringSet("key2", 25);
	
	// Simple get of data types from the cache
	string key1 = cache.StringGet("key1");
	int key2 = (int)cache.StringGet("key2");

El cliente StackExchange.Redis usa los tipos `RedisKey` y `RedisValue` para obtener acceso y almacenar elementos en la caché. Estos tipos se asignan a tipos de lenguajes más primitivos, como la cadena, y no suelen usarse directamente. Las cadenas de Redis son el tipo más básico de valor de Redis y pueden contener muchos tipos de datos, como secuencias binarias serializadas. Aunque es posible que no use el tipo directamente, usará los métodos que contienen `String` en el nombre. Para los tipos de datos más primitivos, los elementos se almacenan y recuperan de la caché mediante los métodos `StringSet` y `StringGet`, a menos que vaya a almacenar colecciones u otros tipos de datos de Redis en la caché.

`StringSet` y `StringGet` se parecen mucho a los métodos `Put` y `Get` del Servicio de caché administrado, siendo una de las diferencias principales que antes de establecer y obtener un objeto .NET en la caché se debe serializar primero.

Cuando llame a `StringGet`, si el objeto existe, se devuelve y, si no existe, se devuelve null. En este caso, puede recuperar el valor desde el origen de datos que desee y almacenarlo en la memoria caché para su uso posterior. Esto se conoce como patrón cache-aside.

Para especificar la expiración de un elemento en la memoria caché, use el parámetro `TimeSpan` de `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

Caché en Redis de Azure puede trabajar con objetos .NET, así como con tipos de datos primitivos; sin embargo, antes de poder almacenar en caché un objeto .NET, se debe serializar. Esta tarea es responsabilidad del desarrollador de la aplicación, lo que le permite tener la flexibilidad de elegir el serializador. Para obtener más información y ejemplos de código, consulte [Objetos .NET en la caché](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## Migrar el estado de sesión de ASP.NET y el almacenamiento en caché de resultados a Caché en Redis de Azure

Caché en Redis de Azure cuenta con proveedores de estado de sesión ASP.NET y almacenamiento en caché de los resultados de las páginas. Para migrar la aplicación que usa versiones de estos proveedores del Servicio de caché administrado, elimine primero las secciones existentes de web.config, y luego configure las versiones de los proveedores de Caché en Redis de Azure. Para obtener instrucciones sobre cómo usar los proveedores de ASP.NET de Caché en Redis de Azure, consulte [Proveedor de estado de sesión de ASP.NET para Caché en Redis de Azure](cache-asp.net-session-state-provider.md) y [Proveedor de caché de resultados de ASP.NET para Caché en Redis de Azure](cache-asp.net-output-cache-provider.md).

## Pasos siguientes

Explore la [documentación de Caché en Redis de Azure](https://azure.microsoft.com/documentation/services/cache/), encontrará tutoriales, ejemplos, vídeos y mucho más.

<!---HONumber=AcomDC_0128_2016-->