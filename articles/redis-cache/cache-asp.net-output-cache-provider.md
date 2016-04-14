<properties 
   pageTitle="Proveedor de la caché de salida de ASP.NET"
   description="Obtenga información sobre cómo almacenar en caché los resultados de página de ASP.NET con Caché en Redis de Azure"
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
   ms.date="01/13/2016"
   ms.author="sdanie" />

# Proveedor de caché de salida de ASP.NET para Caché en Redis de Azure

El proveedor de la caché de salida de Redis es un mecanismo de almacenamiento fuera de proceso para los datos de la caché de salida. Estos datos resultan necesarios específicamente para respuestas HTTP completas (caché de resultados de la página). El proveedor se conecta al nuevo punto de extensibilidad del proveedor de caché de salida que se introdujo en ASP.NET 4.

Para usar el proveedor de la caché de salida de Redis, configure primero la caché y luego configure la aplicación de ASP.NET mediante el paquete NuGet del proveedor de la caché de salida de Redis. En este tema se proporcionan instrucciones sobre cómo configurar la aplicación para usar el proveedor de la caché de salida de Redis. Para obtener más información sobre cómo crear y configurar una instancia de Caché en Redis de Azure, consulte [Creación de una caché](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## Almacenamiento de los resultados de página de ASP.NET en la caché

Para configurar una aplicación cliente en Visual Studio usando el paquete NuGet del proveedor de la caché de salida de Redis, haga clic con el botón derecho en el proyecto en el **Explorador de soluciones** y elija **Administrar paquetes de NuGet**.

![Paquetes NuGet de administración de Caché en Redis de Azure](./media/cache-asp.net-output-cache-provider/IC729541.png)

Escriba **RedisOutputCacheProvider** en el cuadro de texto **Buscar en línea**, selecciónelo en los resultados y haga clic en **Instalar**.

![Proveedor de la caché de salida de Caché en Redis de Azure](./media/cache-asp.net-output-cache-provider/IC751727.jpg)

El paquete NuGet del proveedor de la caché de salida de Redis tiene una dependencia del paquete StackExchange.Redis.StrongName. Si el paquete StackExchange.Redis.StrongName no existe en el proyecto, se instalará. Tenga en cuenta que, además del paquete StackExchange.Redis.StrongName con nombre seguro, también está la versión con nombre no seguro de StackExchange.Redis. Si su proyecto usa la versión de StackExchange.Redis con nombre no seguro, debe desinstalara antes o después de instalar el paquete NuGet del proveedor de la caché de salida de Redis, de lo contrario se producirán conflictos con los nombres en el proyecto. Para obtener más información sobre estos paquetes, consulte [Configuración de los clientes de la caché de .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

El paquete NuGet se descarga, agrega las referencias de ensamblado requeridas y agrega la siguiente sección en el archivo web.config que contiene la configuración necesaria para que su aplicación ASP.NET use el proveedor de la caché de salida de Redis.

    <caching>
      <outputCachedefaultProvider="MyRedisOutputCache">
        <providers>
          <!--
          <add name="MyRedisOutputCache" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
          -->
          <add name="MyRedisOutputCache"type="Microsoft.Web.Redis.RedisOutputCacheProvider"host="127.0.0.1"accessKey="" ssl="false"/>
        </providers>
      </outputCache>
    </caching>

En la sección comentada se proporciona un ejemplo de los atributos y la configuración de ejemplo de cada uno.

Configure los atributos con los valores de la hoja de la caché en el Portal de Microsoft Azure y configure los demás valores según prefiera. Para obtener instrucciones sobre cómo acceder a las propiedades de caché, consulte [Configuración de la caché en Redis](cache-configure.md#configure-redis-cache-settings).

-	**host**: especifique el punto de conexión de la caché.
-	**puerto**: use el puerto no SSL o SSL, según la configuración de SSL.
-	**accessKey**: use la clave primaria o secundaria para la caché.
-	**ssl**: true si desea proteger las comunicaciones de la caché o el cliente con SLS; de lo contrario, false. Asegúrese de especificar el puerto correcto.
	-	El puerto no SSL está deshabilitado de forma predeterminada para las cachés nuevas. Especifique true en este valor para usar el puerto SSL. Para obtener más información sobre cómo habilitar el puerto no SSL, consulte la sección [Puertos de acceso](cache-configure.md#access-ports) del tema de [Configuración de caché](cache-configure.md).
-	**databaseId**: especifique qué base de datos se usará para los datos de salida de la caché. Si no se especifica, se usa el valor predeterminado de 0.
-	**applicationName**: las claves están almacenadas en Redis como <AppName>\_<SessionId>\_Data. Esto permite que varias aplicaciones compartan la misma clave. Este parámetro es opcional y, si no se especifica, se usa un valor predeterminado.
-	**connectionTimeoutInMilliseconds**: esta opción le permite invalidar la configuración de connectTimeout en el cliente de StackExchange.Redis. Si no se especifica, se usa el valor predeterminado de connectTimeout, que es 5000. Para obtener más información, consulte el [modelo de configuración de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
-	**operationTimeoutInMilliseconds**: esta opción le permite invalidar la configuración de syncTimeout en el cliente de StackExchange.Redis. Si no se especifica, se usa el valor predeterminado de syncTimeout, que es 1000. Para obtener más información, consulte el [modelo de configuración de StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Incorpore una directiva OutputCache a cada página cuyos resultados desea almacenar en caché.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

En este ejemplo, los datos de la página almacenados en la caché permanecerán ahí durante 60 segundos y se almacenará en la caché una versión diferente de la página para cada combinación de parámetros. Para obtener más información sobre la directiva OutputCache, consulte [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837).

Después de realizar estos pasos, la aplicación está configurada para usar el proveedor de la caché de salida de Redis.

## Pasos siguientes

Consulte el [proveedor de estado de sesión de ASP.NET para Caché en Redis de Azure](cache-asp.net-session-state-provider.md).

<!---HONumber=AcomDC_0128_2016-->