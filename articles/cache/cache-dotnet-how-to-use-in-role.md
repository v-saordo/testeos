<properties 
	pageTitle="Uso de Caché en rol (.NET) | Microsoft Azure" 
	description="Obtenga información acerca de cómo usar la caché en rol de Azure. Los ejemplos se escriben en código C# y usan la API .NET." 
	services="cache" 
	documentationCenter=".net" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/03/2015" 
	ms.author="sdanie"/>






# Uso de la Caché en rol para Caché de Azure

En esta guía se explica cómo comenzar a usar **Caché en rol para Caché de Azure**. Los ejemplos se escriben en código C# y usan la API .NET. Entre los escenarios tratados se incluyen la **configuración de un clúster de caché**, la **configuración de clientes de caché**, la **incorporación y eliminación de objetos en la caché, el almacenamiento del estado de sesión ASP.NET en la caché** y la **activación de la caché de resultados de la página ASP.NET con el uso de la caché**. Para obtener más información acerca del uso de la caché en rol, consulte la sección [Pasos siguientes][].

>[AZURE.IMPORTANT]El 30 de noviembre de 2016 se retirará el Servicio de caché administrado de Azure y la Caché en rol de Azure. Se recomienda que migre a la Caché en Redis de Azure con vistas a prepararse para la mencionada retirada. Para obtener más información sobre las fechas y la guía de migración, consulte [¿Qué oferta de caché de Azure es adecuada para mí?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)

<a name="what-is"></a>
## ¿Qué es Caché en rol?

La caché en rol proporciona una capa de almacenamiento en caché para las aplicaciones de Azure. El almacenamiento en caché aumenta el rendimiento gracias a que almacena temporalmente la información en memoria desde otras fuentes back-end y, además, puede reducir los costes asociados con las transacciones de base de datos en la nube. La caché en rol incluye las siguientes características:

-   Proveedores de ASP.NET previamente generados para el almacenamiento en caché del estado de la sesión y los resultados de la página, lo que permite acelerar las aplicaciones web sin necesidad de modificar su código.
-   Almacenamiento en caché de cualquier objeto administrado serializable; por ejemplo: Objetos CLR, filas, XML y datos binarios.
-   Modelo de desarrollo coherente en Azure y Windows Server AppFabric.

La caché en rol proporciona una nueva forma de ejecutar el almacenamiento en caché con la utilización de una parte de la memoria de las máquinas virtuales que hospedan las instancias de rol en los servicios en la nube de Azure (también conocidos como servicios hospedados). Si dispone de mayor flexibilidad en términos de opciones de implementación, las cachés pueden tener un gran tamaño y no presentar restricciones de cuota específicas de caché.

>[AZURE.IMPORTANT]A partir de Azure SDK 2.6, caché en rol utiliza Almacenamiento de Microsoft Azure SDK versión 4.3. En versiones anteriores del SDK de Azure, caché en rol utilizaba Almacenamiento de Azure SDK 1.7. Las aplicaciones que usan la caché en rol con las versiones del SDK de Azure anteriores a 2.6 deberían migrar a Azure SDK 2.6 antes de que la versión de Almacenamiento de Azure 2011-08-18 se retire el 1 de agosto de 2016. Para obtener más información, consulte [Notas de la versión del SDK 2.6 de Azure: caché en rol](../azure-sdk-dotnet-release-notes-2_6.md#in-role-cache-updates) y [Actualización de la eliminación de la versión del servicio Almacenamiento de Microsoft Azure: extensión hasta 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

El almacenamiento en caché de instancias de rol presenta las siguientes ventajas:

-	No se pagan cuotas premium por el almacenamiento en caché. Se paga únicamente por los recursos informáticos que hospedan la caché.
-	Se eliminan las cuotas de caché y la limitación de peticiones.
-	Se ofrece mayor control y aislamiento. 
-	Se mejora el rendimiento.
-	Las cachés cambian de tamaño automáticamente cuando los roles se escalan vertical u horizontalmente. La memoria disponible para el almacenamiento en caché se aumenta o reduce vertical u horizontalmente de forma eficaz cuando se incorporan o eliminan instancias de rol.
-	Se ofrece una depuración del tiempo de desarrollo con total fidelidad. 
-	Se admite el protocolo memcache.

Además, el almacenamiento en caché de instancias de rol ofrece estas opciones de configuración:

-	Configuración de un rol dedicado para el almacenamiento en caché o almacenamiento en caché colocalizada en los roles existentes. 
-	Disponibilidad de la caché para varios clientes en la misma implementación del servicio en la nube.
-	Creación de varias cachés con nombre con diferentes propiedades.
-	Configuración opcional de alta disponibilidad en cachés individuales.
-	Uso de capacidades expandidas de almacenamiento en caché, como regiones, etiquetado y notificaciones.

En esta guía se ofrece una introducción sobre cómo comenzar a usar la caché en rol. Para obtener información más detallada acerca de estas características, que van más allá del ámbito de esta guía de introducción, consulte [Información general acerca de la caché en rol.][]

<a name="getting-started-cache-role-instance"></a>
## Introducción a Caché en rol

La caché en rol ofrece una forma de habilitar el almacenamiento en caché con el uso de la memoria que se encuentra en las máquinas virtuales que hospedan las instancias de rol. Las instancias de rol que hospedan las cachés constituyen lo que se conocen como un **clúster de caché**. Hay dos topologías de implementación para el almacenamiento en caché en instancias de rol:

-	Almacenamiento en caché de **rol dedicado**: las instancias de rol se usan exclusivamente para el almacenamiento en caché.
-	Almacenamiento en caché de **rol colocalizado**: la memoria caché comparte los recursos de VM (ancho de banda, CPU y memoria) con la aplicación.

Para usar el almacenamiento en caché en instancias de rol, necesita configurar un clúster de caché y, a continuación, configurar los clientes de caché para que puedan tener acceso al clúster de caché.

-	[Configuración del clúster de caché][]
-	[Configuración de los clientes de caché][]

<a name="enable-caching"></a>
## Configuración del clúster de caché

Para configurar un clúster de caché de **rol colocalizado**, seleccione el rol en que desea hospedar el clúster de caché. Haga clic con el botón derecho en **Explorador de soluciones** y seleccione **Propiedades**.

![RoleCache1][RoleCache1]

Cambie a la pestaña **Almacenamiento en caché**, marque la casilla **Habilitar caché** y especifique las opciones deseadas para el almacenamiento en caché. Si el almacenamiento en caché está habilitado en un **Rol de trabajo** o un **Rol web de ASP.NET**, la configuración predeterminada es el almacenamiento en caché de un **Rol colocalizado** con el 30% de la memoria de las instancias de rol asignado para el almacenamiento en caché. Se configura automáticamente una caché predeterminada y, si lo desea, puede crear cachés con nombre adicionales y estas cachés compartirán la memoria asignada.

![RoleCache2][RoleCache2]

Para configurar un clúster de caché de **Rol dedicado**, agregue al proyecto un **Rol de trabajo de caché**.

![RoleCache7][RoleCache7]

Si se agrega a un proyecto un **Rol de trabajo de caché**, la configuración predeterminada es el almacenamiento en caché de **Rol dedicado**.

![RoleCache8][RoleCache8]

Cuando el almacenamiento en caché está habilitado, se puede configurar la cuenta de almacenamiento del clúster de caché. La caché en rol precisa de una cuenta de almacenamiento de Azure. Esta cuenta de almacenamiento se usa para contener los datos de configuración del clúster de caché al que se tiene acceso desde todas las máquinas virtuales que conforman el clúster de caché. Esta cuenta de almacenamiento se especifica en la pestaña **Almacenamiento en caché** de la página de propiedades del rol de clúster de caché, justo encima de **Configuración de caché con nombre**.

![RoleCache10][RoleCache10]

>Si esta cuenta de almacenamiento no está configurada, los roles presentarán errores de inicio.

El tamaño de la caché lo determina una combinación del tamaño de VM del rol, el recuento de instancias del rol y si el clúster de caché está configurado como un clúster de caché de rol dedicado o un clúster de caché de rol colocalizado.

>En esta sección se ofrece información general simplificada acerca de la configuración del tamaño de la caché. Para obtener más información acerca del tamaño de la caché y otras consideraciones sobre el planeamiento de capacidad, consulte [Consideraciones sobre el planeamiento de la capacidad para la caché en rol][].

Para configurar el tamaño de la máquina virtual y el número de instancias de rol, haga clic con el botón derecho en las propiedades del rol en el **Explorador de soluciones** y seleccione **Propiedades**.

![RoleCache1][RoleCache1]

Cambie a la pestaña **Configuración**. El **Recuento de instancias** predeterminado es 1 y el **Tamaño de VM** predeterminado es **Pequeño**.

![RoleCache3][RoleCache3]

La memoria total para los tamaños de VM es la siguiente:

-	**Pequeño**: 1,75 GB
-	**Mediano**: 3,5 GB
-	**Grande**: 7 GB
-	**Extragrande**: 14 GB


> Estos tamaños de memoria representan la cantidad total de memoria disponible para la VM compartida entre el SO, el proceso de caché, los datos de caché y la aplicación. Para obtener más información acerca de cómo configurar los tamaños de la máquina virtual, consulte [Configuración de tamaños de la máquina virtual][]. Tenga en cuenta que la caché no es compatible con el tamaño **Extrapequeño** de la VM.

Si se especifica el almacenamiento en caché de **Rol colocalizado**, el tamaño de la caché se determina mediante el porcentaje especificado de memoria de la máquina virtual. En cambio, si se especifica el almacenamiento en caché de **Rol dedicado**, se utiliza toda la memoria disponible de la máquina virtual para el almacenamiento en caché. Si se configuran dos instancias de rol, se usa la memoria combinada de las máquinas virtuales. Esto forma un clúster de caché donde la memoria de caché disponible se distribuye entre varias instancias de rol, pero se presenta a los clientes de la caché como un único recurso. La configuración de instancias de rol adicionales aumenta el tamaño de la caché de la misma manera. Para determinar la configuración necesaria para aprovisionar una caché con el tamaño deseado, puede usar la hoja de cálculo de planeamiento de la capacidad que se trata en [Consideraciones sobre el planeamiento de la capacidad para la caché en rol][].

Cuando el clúster de caché está configurado, puede configurar los clientes de caché a fin de que puedan tener acceso a la caché.

<a name="NuGet"></a>
## Configuración de los clientes de caché

Para obtener acceso a la caché en rol, los clientes deben estar en la misma implementación. Si el clúster de caché es de rol dedicado, entonces los clientes son otros roles de la implementación. Si el clúster de caché es un rol colocalizado, entonces los clientes pueden ser los otros roles de la implementación o los propios roles que hospedan el clúster de caché. Se ofrece un paquete de NuGet que se puede usar para configurar cada rol de cliente que tiene acceso a la caché. Para configurar un rol a fin de que tenga acceso a un clúster de caché con la utilización del paquete de NuGet para el almacenamiento en caché, haga clic con el botón derecho en el proyecto de rol en el **Explorador de soluciones** y seleccione **Administración de paquetes de NuGet**.

![RoleCache4][RoleCache4]

Seleccione **Caché en rol**, haga clic en **Instalar** y, a continuación, en **Acepto**.

>Si **Caché en rol **no aparece en la lista, escriba **WindowsAzure.Caching** en el cuadro de texto **Buscar en línea** y seleccione la opción en los resultados.

![RoleCache5][RoleCache5]

El paquete NuGet realiza varias acciones: incorpora la configuración necesaria al archivo config del rol, incorpora una configuración de nivel de diagnóstico del cliente de caché al archivo ServiceConfiguration.cscfg de la aplicación de Azure e incorpora las referencias de ensamblado necesarias.

>Para los roles de web ASP.NET, el paquete NuGet de almacenamiento en caché también agrega dos secciones comentadas al archivo web.config. La primera sección permite almacenar el estado de sesión en la memoria caché y la segunda sección permite almacenar en la caché los resultados de la página ASP.NET. Para obtener más información, consulte [Almacenamiento del estado de la sesión de ASP.NET en la memoria caché] y [Almacenamiento de la caché de resultados de la página ASP.NET en la caché][].

El paquete NuGet agrega los siguientes elementos de configuración a los archivos web.config o app.config de su rol. Las secciones **dataCacheClients** y **cacheDiagnostics** se agregan bajo el elemento **configSections**. Si no existe el elemento **configSections**, se crea uno como elemento secundario del elemento **configuration**.

    <configSections>
      <!-- Existing sections omitted for clarity. -->

      <section name="dataCacheClients" 
               type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    
      <section name="cacheDiagnostics" 
               type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection, Microsoft.ApplicationServer.Caching.AzureCommon" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    </configSections>

Estas nuevas secciones incluyen referencias a un elemento **dataCacheClients** y a un elemento **cacheDiagnostics**. Estos elementos también se incorporan al elemento **configuration**.

    <dataCacheClients>
      <dataCacheClient name="default">
        <autoDiscover isEnabled="true" identifier="[cache cluster role name]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
      </dataCacheClient>
    </dataCacheClients>
    <cacheDiagnostics>
      <crashDump dumpLevel="Off" dumpStorageQuotaInMB="100" />
    </cacheDiagnostics>

Después de agregar la configuración, reemplace **[cache cluster role name]** por el nombre del rol que hospeda el clúster de caché.

>Si **[cache cluster role name]** no se reemplaza por el nombre del rol que hospeda el clúster de caché, se producirá la excepción **TargetInvocationException** cuando se obtenga acceso a una excepción **DatacacheException** interna con el mensaje "Este rol no existe".

El paquete de NuGet también agrega el parámetro **ClientDiagnosticLevel** al valor **ConfigurationSettings** del rol de cliente de caché en ServiceConfiguration.cscfg. El ejemplo siguiente es la sección **WebRole1** del archivo ServiceConfiguration.cscfg con el parámetro **ClientDiagnosticLevel de 1**, que es el valor predeterminado de **ClientDiagnosticLevel**.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

>La caché en rol ofrece un servidor de caché y un nivel de diagnóstico de cliente de caché. El nivel de diagnóstico es una configuración única que configura el nivel de información de diagnóstico recopilada para el almacenamiento en caché. Para obtener más información, consulte [Diagnóstico y solución de problemas de Caché en rol][]

El paquete de NuGet también agrega referencias a los siguientes ensamblados:

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

Si se trata de un rol web de ASP.NET, también se incorpora la siguiente referencia de ensamblado:

-	Microsoft.Web.DistributedCache.dll.

Cuando el proyecto de cliente ya está configurado para el almacenamiento en caché, puede usar las técnicas descritas en las siguientes secciones para trabajar con la caché.

<a name="working-with-caches"></a>
## Trabajo con cachés

En los pasos de esta sección se describe cómo ejecutar tareas comunes con el almacenamiento en caché.

-	[Creación de un objeto DataCache][]
-   [Agregación y recuperación de un objeto de la caché][]
-   [Especificación de la expiración de un objeto en la memoria caché][]
-   [Almacenamiento del estado de la sesión de ASP.NET en la memoria caché][]
-   [Almacenamiento de la caché de resultados de la página ASP.NET en la caché][]

<a name="create-cache-object"></a>
## Creación de un objeto DataCache

Para trabajar con una caché mediante programación, necesita una referencia a la misma. Incorpore lo siguiente en la parte superior de cualquier archivo desde el que desea usar la caché en rol:

    using Microsoft.ApplicationServer.Caching;

>Si Visual Studio no reconoce los tipos de la instrucción de uso incluso tras la instalación del paquete de NuGet para el servicio de caché, que agrega las referencias necesarias, asegúrese de que el perfil de destino del proyecto sea .NET Framework 4.0 o superior y seleccione un perfil que no especifique **Client Profile**. Para obtener instrucciones acerca de la configuración de los clientes de caché, consulte [Configuración de los clientes de caché][].

Un objeto **DataCache** se puede crear de dos formas. La primera consiste sencillamente en crear un objeto **DataCache**, para lo que es necesario reemplazar el nombre de la caché deseada.

    DataCache cache = new DataCache("default");

Tras crear una instancia del objeto **DataCache**, puede usarlo para interactuar con la caché, tal y como se describe en las siguientes secciones.

La segunda forma se basa en crear un nuevo objeto **DataCacheFactory** en la aplicación con la utilización del constructor predeterminado. Esto da lugar a que el cliente de caché use los ajustes del archivo de configuración. Recurra al método **GetDefaultCache** de la nueva instancia **DataCacheFactory** que devuelve un objeto **DataCache**, o bien recurra al método **GetCache** y reemplace el nombre de la caché. Estos métodos devuelven un objeto **DataCache** que se puede usar para obtener acceso a la caché de manera programada.

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items.	

<a name="add-object"></a>
## Agregación y recuperación de un objeto de la caché

Para incorporar un elemento a la caché, se puede usar el método **Add** o **Put**. El método **Add** incorpora el objeto especificado a la caché, cuya clave se corresponde con el valor del parámetro clave.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

Si un objeto con la misma clave ya se encuentra en la caché, se producirá una excepción **DataCacheException** con el mensaje siguiente:

> ErrorCode:SubStatus: Se está realizando un intento de crear un objeto con una clave que ya existe en la memoria caché. Caching will only accept unique Key values for objects.

Para recuperar un objeto con una clave específica, se puede usar el método **Get**. Si el objeto existe, este se devuelve, y si no existe, se devuelve null.

    // Add the string "value" to the cache, keyed by "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" not in cache. Obtain it from specified data source
        // and add it.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" is in cache, cast result to correct type.
    }

El método **Put** agrega el objeto con la clave especificada a la caché si no existe, o bien reemplaza el objeto en caso de que exista.

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>
## Especificación de la expiración de un objeto en la memoria caché

De forma predeterminada, los elementos de la caché expiran 10 minutos después de colocarlos en la caché. Este valor se puede configurar en **Time to Live (min)** en las propiedades del rol que hospeda el clúster de caché.

![RoleCache6][RoleCache6]

Hay tres tipos de **Tipo de expiración**: **Ninguno**, **Absoluta** y **Ventana deslizante**. Estas opciones configuran cómo se usa **Período de vida (min)** para determinar la expiración. El valor de **Tipo de expiración** predeterminado es **Absoluta**, que significa que el temporizador de cuenta regresiva de la expiración de un elemento comienza cuando el elemento se coloca en la caché. Cuando ha transcurrido la cantidad de tiempo especificada para un elemento, este expira. Si se especifica **Ventana deslizante**, la cuenta regresiva para la expiración de un elemento se restablece cada vez que se obtiene acceso al elemento en la caché, y el elemento no expirará hasta que haya transcurrido la cantidad de tiempo especificada desde el último acceso. Si se especifica **Ninguno**, la opción **Período de vida (min)** debe establecerse en **0**, y los elementos no expirarán y serán válidos mientras estén en la caché.

Si se desea un intervalo de tiempo de expiración más largo o más corto que el que se ha configurado en las propiedades del rol, se puede definir una duración específica cuando se incorpora o actualiza un elemento en la caché mediante la utilización de la sobrecarga de **Add** y **Put** que consideran un parámetro **TimeSpan**. En el siguiente ejemplo, la cadena **value** se incorpora a la caché, con la clave de **item** y con un tiempo de expiración de 30 minutos.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

Para ver el intervalo de tiempo de expiración restante de un elemento de la caché, se puede usar el método **GetCacheItem** para recuperar un objeto **DataCacheItem** que contiene información acerca del elemento de la caché, incluido el intervalo de tiempo de expiración restante.

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>
## Almacenamiento del estado de la sesión de ASP.NET en la memoria caché

El proveedor del estado de sesión de la caché en rol es un mecanismo de almacenamiento fuera de proceso para las aplicaciones ASP.NET. Este proveedor le permite almacenar el estado de sesión en una caché de Azure en lugar de en la memoria o en una base de datos de SQL Server. Para usar el proveedor de estado de sesión del almacenamiento en caché, primero configure el clúster de caché y, a continuación, configure la aplicación ASP.NET para el almacenamiento en caché con la utilización del paquete de NuGet para el almacenamiento en caché, según se describe en [Introducción a Caché en rol][]. Cuando el paquete de NuGet para el almacenamiento en caché está instalado, incorpora una sección comentada en web.config que contiene la configuración necesaria para que la aplicación ASP.NET use el proveedor del estado de sesión para la caché en rol.

    <!--Uncomment this section to use In-Role Cache for session state caching
    <system.web>
      <sessionState mode="Custom" customProvider="AFCacheSessionStateProvider">
        <providers>
          <add name="AFCacheSessionStateProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </sessionState>
    </system.web>-->

>Si el archivo web.config no contiene esta sección comentada después de instalar el paquete de NuGet para la caché, asegúrese de que el último administrador del paquete de NuGet se ha instalado desde la página [NuGet Package Manager Installation][] (Instalación de NuGet Package Manager) y, a continuación, desinstale el paquete y vuelva a instalarlo.

Para habilitar el proveedor del estado de sesión para la caché en rol, quite el comentario de la sección especificada. La caché predeterminada se especifica en el fragmento proporcionado. Para usar una caché diferente, especifique la que desee en el atributo **cacheName**.

Para obtener más información acerca del uso del proveedor de estado de sesión del servicio de almacenamiento en caché, consulte [Proveedor de estado de sesión para la caché en rol][].

<a name="store-page"></a>
## Almacenamiento de la caché de resultados de la página ASP.NET en la caché

El proveedor de la caché de resultados para la caché en rol es un mecanismo de almacenamiento fuera de proceso para los datos de la caché de resultados. Estos datos resultan necesarios específicamente para respuestas HTTP completas (caché de resultados de la página). El proveedor se conecta al nuevo punto de extensibilidad del proveedor de caché de salida que se introdujo en ASP.NET 4. Para utilizar el proveedor de caché de resultados, primero configure el clúster de caché y, a continuación, configure la aplicación de ASP.NET para el almacenamiento en caché con el paquete NuGet de almacenamiento en caché, tal como se describe en [Introducción a Caché en rol][]. Cuando el paquete de NuGet para el almacenamiento en caché está instalado, incorpora la siguiente sección comentada en web.config que contiene la configuración necesaria para que la aplicación ASP.NET use el proveedor de la caché de resultados para la caché en rol.

    <!--Uncomment this section to use In-Role Cache for output caching
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default" />
        </providers>
      </outputCache>
    </caching>-->

>Si el archivo web.config no contiene esta sección comentada después de instalar el paquete de NuGet para la caché, asegúrese de que el último administrador del paquete de NuGet se ha instalado desde la página [NuGet Package Manager Installation][] (Instalación de NuGet Package Manager) y, a continuación, desinstale el paquete y vuelva a instalarlo.

Para habilitar el proveedor de la caché de resultados para la caché en rol, quite el comentario de la sección especificada. La caché predeterminada se especifica en el fragmento proporcionado. Para usar una caché diferente, especifique la que desee en el atributo **cacheName**.

Incorpore una directiva **OutputCache** a cada página cuyos resultados desea almacenar en caché.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

En este ejemplo, los datos de la página almacenados en la caché permanecerán ahí durante 60 segundos y se almacenará en la caché una versión diferente de la página para cada combinación de parámetros. Para obtener más información acerca de las opciones disponibles, consulte [Directiva OutputCache][].

Para obtener más información acerca del uso del proveedor de la caché de resultados para la caché en rol, consulte [Proveedor de caché de resultados para la caché en rol][].

<a name="next-steps"></a>
## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos de la caché en rol, utilice estos enlaces para obtener más información acerca de cómo realizar tareas de almacenamiento en caché más complejas.

-   Consulte la referencia de MSDN: [Caché en rol][]
-   Obtenga información acerca de cómo migrar a la caché en rol: [Migración a Caché en rol][]
-   Consulte los ejemplos: [Ejemplos de Caché en rol][]
-	Vea la sesión [Maximum Performance: Accelerate Your Cloud Services Applications with Azure Caching][] (Rendimiento máximo: agilización de las aplicaciones de servicios en la nube con el almacenamiento en caché de Azure) de TechEd 2013 sobre la caché en rol

<!-- INTRA-TOPIC LINKS -->
[Pasos siguientes]: #next-steps
[What is In-Role Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Getting Started with the In-Role Cache Service]: #getting-started-cache-service
[Prepare Your Visual Studio Project to Use In-Role Cache]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Introducción a Caché en rol]: #getting-started-cache-role-instance
[Configuración del clúster de caché]: #enable-caching
[Configure the desired cache size]: #cache-size
[Configuración de los clientes de caché]: #NuGet
[Working with Caches]: #working-with-caches
[Creación de un objeto DataCache]: #create-cache-object
[Agregación y recuperación de un objeto de la caché]: #add-object
[Especificación de la expiración de un objeto en la memoria caché]: #specify-expiration
[Almacenamiento del estado de la sesión de ASP.NET en la memoria caché]: #store-session
[Almacenamiento de la caché de resultados de la página ASP.NET en la caché]: #store-page
[Target a Supported .NET Framework Profile]: #prepare-vs-target-net
 
<!-- IMAGES -->
[RoleCache1]: ./media/cache-dotnet-how-to-use-in-role/cache8.png
[RoleCache2]: ./media/cache-dotnet-how-to-use-in-role/cache9.png
[RoleCache3]: ./media/cache-dotnet-how-to-use-in-role/cache10.png
[RoleCache4]: ./media/cache-dotnet-how-to-use-in-role/cache11.png
[RoleCache5]: ./media/cache-dotnet-how-to-use-in-role/cache12.png
[RoleCache6]: ./media/cache-dotnet-how-to-use-in-role/cache13.png
[RoleCache7]: ./media/cache-dotnet-how-to-use-in-role/cache14.png
[RoleCache8]: ./media/cache-dotnet-how-to-use-in-role/cache15.png
[RoleCache10]: ./media/cache-dotnet-how-to-use-in-role/cache17.png
  
<!-- LINKS -->
[Configuración de tamaños de la máquina virtual]: http://go.microsoft.com/fwlink/?LinkId=164387
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[Consideraciones sobre el planeamiento de la capacidad para la caché en rol]: http://go.microsoft.com/fwlink/?LinkId=252651
[Ejemplos de Caché en rol]: http://msdn.microsoft.com/library/jj189876.aspx
[In-Role Cache]: http://go.microsoft.com/fwlink/?LinkId=252658
[Caché en rol]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[Maximum Performance: Accelerate Your Cloud Services Applications with Azure Caching]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/WAD-B326#fbid=kmrzkRxQ6gU
[Migración a Caché en rol]: http://msdn.microsoft.com/library/hh914163.aspx
[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Proveedor de caché de resultados para la caché en rol]: http://msdn.microsoft.com/library/windowsazure/gg185662.aspx
[Directiva OutputCache]: http://go.microsoft.com/fwlink/?LinkId=251979
[Información general acerca de la caché en rol.]: http://go.microsoft.com/fwlink/?LinkId=254172
[Proveedor de estado de sesión para la caché en rol]: http://msdn.microsoft.com/library/windowsazure/gg185668.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Diagnóstico y solución de problemas de Caché en rol]: http://msdn.microsoft.com/library/windowsazure/hh914135.aspx
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx

[Which Azure Cache offering is right for me?]: cache-faq.md#which-azure-cache-offering-is-right-for-me
 

<!---HONumber=AcomDC_1210_2015-->