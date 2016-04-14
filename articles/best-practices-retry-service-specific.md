<properties
   pageTitle="Vuelva a intentar la orientación específica del servicio | Microsoft Azure"
   description="Instrucciones específicas de servicios para establecer el mecanismo de reintento."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/28/2015"
   ms.author="masashin"/>

# Vuelva a intentar la orientación específica del servicio

![](media/best-practices-retry-service-specific/pnp-logo.png)

## Información general

La mayoría de SDK de cliente y de servicios de Azure incluye un mecanismo de reintento. Sin embargo, estos son diferentes porque cada servicio tiene requisitos y características diferentes, por lo que cada mecanismo de reintento se ajusta a un servicio específico. Esta guía resume las características de mecanismo de reintento de la mayoría de los servicios de Azure e incluye información para ayudarle a usar, adaptar o ampliar el mecanismo de reintento para ese servicio.

Para obtener instrucciones generales sobre el control de errores transitorios y reintentar conexiones y operaciones en servicios y recursos, consulte [Guía de reintentos](best-practices-retry-general.md).

En la tabla siguiente se resumen las características de reintento de los servicios de Azure descritos en esta guía.

| **Servicio** | **Capacidades de reintento** | **Configuración de directivas** | **Ámbito** | **Características de telemetría** |
|---------------------------------------|-----------------------------------------|------------------------------|--------------------------------------------------|------------------------
| **[AzureStorage](#azure-storage-retry-guidelines)** | Nativo en el cliente | Programático | Operaciones de cliente e individuales | TraceSource |
| **[Base de datos SQL con Entity Framework](#sql-database-using-entity-framework-6-retry-guidelines)** | Nativo en el cliente | Programático | Global por AppDomain | None |
| **[Base de datos SQL con ADO.NET](#sql-database-using-ado-net-retry-guidelines)** | Topaz* | Declarativo y programático | Instrucciones únicas o bloques de código | Personalizado |
| **[Bus de servicio](#service-bus-retry-guidelines)** | Nativo en el cliente | Programático | Administrador de espacio de nombres, fábrica de mensajería y cliente | ETW |
| **[Memoria caché](#cache-redis-retry-guidelines)** | Nativo en el cliente | Programático | Cliente | TextWriter |
| **[DocumentDB](#documentdb-pre-release-retry-guidelines)** | Nativo en servicio | No configurable | Global | TraceSource |
| **[Búsqueda](#search-retry-guidelines)** | Topaz* (con estrategia de detección personalizada) | Declarativo y programático | Bloques de código | Personalizado |
| **[Active Directory](#azure-active-directory-retry-guidelines)** | Topaz* (con estrategia de detección personalizada) | Declarativo y programático | Bloques de código | Personalizado |
**Topaz en el nombre descriptivo para el bloque de aplicaciones de manejo de errores transitorios que se incluye en <a href="http://msdn.microsoft.com/library/dn440719.aspx">Enterprise Library 6.0</a>. Puede usar una estrategia de detección personalizada con Topaz para la mayoría de los tipos de servicios, como se describe en esta guía. En la sección [Estrategias de bloques de aplicaciones de manejo de errores transitorios (Topaz)](#transient-fault-handling-application-block-topaz-strategies) del final de esta guía se muestran estrategias predeterminadas para Topaz. Tenga en cuenta que el bloque es ahora un marco de código abierto y no es compatible directamente con Microsoft.

> [AZURE.NOTE] Para la mayoría de los mecanismos de reintento integrados de Azure, actualmente no hay ninguna manera de aplicar una directiva de reintento diferente para distintos tipos de error o excepción más allá de la funcionalidad que se incluye en la directiva de reintentos. Por lo tanto, las instrucciones recomendadas disponibles en el momento de la escritura consisten en configurar una directiva que proporcione el rendimiento y la disponibilidad medios óptimos. Una forma de ajustar la directiva consiste en analizar archivos de registro para determinar el tipo de errores transitorios que se están produciendo. Por ejemplo, si la mayoría de los errores está relacionada con problemas de conectividad de red, podría intentar efectuar un reintento inmediato en lugar de esperar mucho tiempo para el primer reintento.

## Directrices de reintento de almacenamiento Azure

Los servicios de almacenamiento de Azure incluyen almacenamiento de tablas y blobs, archivos y colas de almacenamiento.

### Mecanismo de reintento

Los reintentos se producen en el nivel de operación REST individual y son parte integral de la implementación de la API de cliente. El SDK de almacenamiento de cliente usa clases que implementan la [Interfaz IExtendedRetryPolicy](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.aspx).

Hay diferentes implementaciones de la interfaz. Los clientes de almacenamiento pueden elegir entre directivas diseñadas específicamente para el acceso a tablas, blobs y colas. Cada implementación utiliza una estrategia de reintento diferente que define el intervalo de reintento y otros detalles.

Las clases integradas proporcionan compatibilidad para intervalos de reintento lineales (retraso constante) y exponenciales de selección aleatoria. También hay una directiva de no realización de reintentos a usar cuando otro proceso está controlando los reintentos a un nivel superior. Sin embargo, puede implementar sus propias clases de reintentos si tiene requisitos específicos no proporcionados por las clases integradas.

Los reintentos alternativos cambian entre ubicación del servicio de almacenamiento principal y secundario si usa almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) y el resultado de la solicitud es un error que se puede reproducir. Para obtener más información, consulte [Opciones de redundancia de Almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dn727290.aspx).

### Configuración de directivas (almacenamiento de Azure)

Las directivas de reintento se configuran mediante programación. Un procedimiento típico consiste en crear y rellenar una instancia **TableRequestOptions**, **BlobRequestOptions**, **FileRequestOptions** o **QueueRequestOptions** instancia.

```csharp
TableRequestOptions interactiveRequestOption = new TableRequestOptions()
{
  RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
  // For Read-access geo-redundant storage, use PrimaryThenSecondary.
  // Otherwise set this to PrimaryOnly.
  LocationMode = LocationMode.PrimaryThenSecondary,
  // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
  MaximumExecutionTime = TimeSpan.FromSeconds(2)
};
```

A continuación, la instancia de opciones de solicitud se puede establecer en el cliente y todas las operaciones con el cliente usarán las opciones de solicitud especificadas.

```csharp
client.DefaultRequestOptions = interactiveRequestOption;
var stats = await client.GetServiceStatsAsync();
```

Puede anular las opciones de solicitud de cliente pasando una instancia completada de la clase de opciones de solicitud como parámetro a los métodos de operación.

```csharp
var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
```

Puede usar una instancia **OperationContext** para especificar el código para ejecutar cuando se produce un reintento y cuando se ha completado una operación. Este código puede recopilar información acerca de la operación para su uso en registros y telemetría.

	// Set up notifications for an operation
	var context = new OperationContext();
	context.ClientRequestID = "some request id";
	context.Retrying += (sender, args) =>
	{
	  /* Collect retry information */
	};
	context.RequestCompleted += (sender, args) =>
	{
	  /* Collect operation completion information */
	};
	var stats = await client.GetServiceStatsAsync(null, context);

Además de indicar si se puede volver a intentar un error, las directivas de reintento ampliadas devuelven un objeto **RetryContext** que indica el número de reintentos, los resultados de la última solicitud y si se realizará el siguiente reintento en la ubicación principal o secundaria (consulte la tabla siguiente para obtener más información). Las propiedades del objeto **RetryContext** pueden usarse para decidir cuándo y si realizar un reintento. Para obtener más información, consulte [Método IExtendedRetryPolicy.Evaluate](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.retrypolicies.iextendedretrypolicy.evaluate.aspx).

La siguiente tabla muestra la configuración predeterminada de las directivas de reintento integradas.

| **Contexto** | **Configuración** | **Valor predeterminado** | **Significado** |
|--------------------------|-------------------------------------------------------------|------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tabla / Blob / Archivo<br />QueueRequestOptions | MaximumExecutionTime<br /><br />ServerTimeout<br /><br /><br /><br /><br />LocationMode<br /><br /><br /><br /><br /><br /><br />RetryPolicy | 120 segundos<br /><br />Ninguno<br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br />ExponentialPolicy | Tiempo de ejecución máximo para la solicitud, incluidos todos los posibles reintentos.<br />Intervalo de tiempo de espera del servidor para la solicitud (el valor se redondea en segundos). Si no se especifica, usará el valor predeterminado para todas las solicitudes al servidor. Normalmente, la mejor opción es omitir este valor para que se utilice la configuración predeterminada del servidor.<br />Si la cuenta de almacenamiento se crea con la opción de replicación del almacenamiento con redundancia geográfica de acceso de lectura (RA-GRS), puede usar el modo de ubicación para indicar qué ubicación debe recibir la solicitud. Por ejemplo, si se especifica **PrimaryThenSecondary**, las solicitudes siempre se envían a la ubicación principal en primer lugar. Si se produce un error en una solicitud, se envía a la ubicación secundaria.<br />Consulte a continuación los detalles de cada opción. |
| Directiva exponencial | maxAttempt<br />deltaBackoff<br /><br /><br />MinBackoff<br /><br />MaxBackoff | 3<br />4 segundos<br /><br /><br />3 segundos<br /><br />30 segundos | Número de reintentos.<br />Intervalo de espera entre reintentos. Se usarán múltiplos de este período de tiempo, incluyendo un elemento aleatorio, para los reintentos posteriores.<br />Agregado a todos los intervalos de reintento calculados a partir de deltaBackoff. No se puede cambiar este valor.<br />MaxBackoff se usa si el intervalo de reintento calculado es mayor que MaxBackoff. No se puede cambiar este valor. |
| Directiva lineal | maxAttempt<br />deltaBackoff | 3<br />30 segundos | Número de reintentos.<br />Intervalo de espera entre reintentos. |

### Instrucciones de uso del reintento
Al obtener acceso a los servicios de almacenamiento de Azure mediante la API de cliente de almacenamiento, tenga en cuenta las siguientes directrices:

* Use las directivas de reintento integradas del espacio de nombres Microsoft.WindowsAzure.Storage.RetryPolicies donde son adecuadas para sus requisitos. En la mayoría de los casos, estas directivas serán suficientes.
* Use la directiva **ExponentialRetry** en operaciones por lotes, tareas en segundo plano o escenarios no interactivos. En estos escenarios, normalmente puede permitir más tiempo para recuperar el servicio (por consiguiente, con unas mayores posibilidades de que la operación se efectúe correctamente).
* Considere la posibilidad de especificar la propiedad **MaximumExecutionTime** del parámetro **RequestOptions** para limitar el tiempo de ejecución total, pero tenga en cuenta el tipo y tamaño de la operación al elegir un valor de tiempo de espera.
* Si necesita implementar un reintento personalizado, evite crear contenedores en torno a las clases de cliente de almacenamiento. En su lugar, use las capacidades para ampliar las directivas existentes a través de la interfaz **IExtendedRetryPolicy**.
* Si utiliza almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS) puede usar el **LocationMode** para especificar que los reintentos tengan acceso a la copia de solo lectura secundaria de la tienda en caso de error en el acceso principal. Sin embargo, al utilizar esta opción debe asegurarse de que la aplicación pueda trabajar correctamente con datos que pueden ser obsoletos si todavía no ha completado la replicación desde el almacén principal.

Considere la posibilidad de comenzar con la configuración siguiente para volver a intentar las operaciones. Esta es la configuración de propósito general, y debe supervisar las operaciones y ajustar los valores para adaptarlos a su propio escenario.

| **Contexto** | **Destino de ejemplo E2E<br />máximo de latencia** | **Directiva de reintentos** | **Configuración** | **Valores** | **Cómo funciona** |
|----------------------|-----------------------------------|------------------|-------------------------|-------------|-----------------------------------------------------------------------------|
| Interactivo, interfaz de usuario<br />o primer plano | 2 segundos | Lineal | maxAttempt<br />deltaBackoff | 3<br />500 ms | Intento 1 - retraso de 500 segundos<br />Intento 2 - retraso de 500 ms<br />Intento 3 – retraso de 500 ms |
| Fondo<br />o proceso por lotes | 30 segundos | Exponencial | maxAttempt<br />deltaBackoff | 5<br />4 segundos | Intento 1 - retraso de ~3 segundos<br />Intento 2 - retraso de ~7 segundos<br />Intento 3 - retraso de ~15 segundos |

## Telemetría

Los reintentos se registran en un **TraceSource**. Debe configurar un **TraceListener** para capturar los eventos y escribirlos en un registro de destino adecuado. Puede usar el **TextWriterTraceListener** o **XmlWriterTraceListener** para escribir los datos en un archivo de registro, el **EventLogTraceListener** para escribir en el registro de eventos de Windows o el **EventProviderTraceListener** para escribir los datos de seguimiento en el subsistema ETW. También puede configurar automáticamente el vaciado del búfer y el nivel de detalle de los eventos que se registrarán (por ejemplo, Error, Advertencia, Informativo y Detallado). Para obtener más información, consulte [Registro de cliente con biblioteca de cliente de almacenamiento .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx).

Las operaciones pueden recibir una instancia **OperationContext**, que expone un evento de **Reintento** que puede usarse para adjuntar la lógica personalizada de telemetría. Para obtener más información, consulte [Evento OperationContext.Retrying](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.operationcontext.retrying.aspx).

## Ejemplos (almacenamiento de Azure)

En el ejemplo de código siguiente se muestra cómo crear dos instancias **TableRequestOptions** con diferentes configuraciones de reintento; una para solicitudes interactivas y otra para solicitudes en segundo plano. A continuación, el ejemplo establece estas dos directivas de reintento en el cliente para que puedan aplicarse a todas las solicitudes y también establece la estrategia interactiva en una solicitud concreta para que reemplace la configuración predeterminada que se aplica al cliente.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.RetryPolicies;
using Microsoft.WindowsAzure.Storage.Table;

namespace RetryCodeSamples
{
    class AzureStorageCodeSamples
    {
        private const string connectionString = "UseDevelopmentStorage=true";

        public async static Task Samples()
        {
            var storageAccount = CloudStorageAccount.Parse(connectionString);

            TableRequestOptions interactiveRequestOption = new TableRequestOptions()
            {
                RetryPolicy = new LinearRetry(TimeSpan.FromMilliseconds(500), 3),
                // For Read-access geo-redundant storage, use PrimaryThenSecondary.
                // Otherwise set this to PrimaryOnly.
                LocationMode = LocationMode.PrimaryThenSecondary,
                // Maximum execution time based on the business use case. Maximum value up to 10 seconds.
                MaximumExecutionTime = TimeSpan.FromSeconds(2)
            };

            TableRequestOptions backgroundRequestOption = new TableRequestOptions()
            {
                // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
                // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
                MaximumExecutionTime = TimeSpan.FromSeconds(30),
                // PrimaryThenSecondary in case of Read-access geo-redundant storage, else set this to PrimaryOnly
                LocationMode = LocationMode.PrimaryThenSecondary
            };

            var client = storageAccount.CreateCloudTableClient();
            // Client has a default exponential retry policy with 4 sec delay and 3 retry attempts
            // Retry delays will be approximately 3 sec, 7 sec, and 15 sec
            // ServerTimeout and MaximumExecutionTime are not set

            {
                // Set properties for the client (used on all requests unless overridden)
                // Different exponential policy parameters for background scenarios
                client.DefaultRequestOptions = backgroundRequestOption;
                // Linear policy for interactive scenarios
                client.DefaultRequestOptions = interactiveRequestOption;
            }

            {
                // set properties for a specific request
                var stats = await client.GetServiceStatsAsync(interactiveRequestOption, operationContext: null);
            }

            {
                // Set up notifications for an operation
                var context = new OperationContext();
                context.ClientRequestID = "some request id";
                context.Retrying += (sender, args) =>
                {
                    /* Collect retry information */
                };
                context.RequestCompleted += (sender, args) =>
                {
                    /* Collect operation completion information */
                };
                var stats = await client.GetServiceStatsAsync(null, context);
            }
        }
    }
}
```

## Más información

- [Recomendaciones de la directiva de reintento de biblioteca de cliente de almacenamiento de Azure](https://azure.microsoft.com/blog/2014/05/22/azure-storage-client-library-retry-policy-recommendations/)
- [Biblioteca de cliente de almacenamiento 2.0: implementación de directivas de reintento](http://gauravmantri.com/2012/12/30/storage-client-library-2-0-implementing-retry-policies/)

## Base de datos SQL mediante el uso de las directrices de reintento de Entity Framework 6

Base de datos SQL es una base de datos SQL hospedada que está disponible en una amplia variedad de tamaños y como un servicio estándar (compartido) y premium (no compartido). Entity Framework es un mapeador relacional de objetos que permite a los desarrolladores de .NET trabajar con datos relacionales usando objetos específicos del dominio. Elimina la necesidad de usar la mayoría del código de acceso a datos que los programadores suelen tener que escribir.

## Mecanismo de reintento

Se proporciona compatibilidad con los reintentos al obtener acceso a la Base de datos SQL mediante Entity Framework 6.0 y versiones posteriores mediante un mecanismo denominado [Resistencia de la conexión y lógica de reintento](http://msdn.microsoft.com/data/dn456835.aspx). Hay disponible una especificación completa en la [wiki .NET Entity Framework](https://entityframework.codeplex.com/wikipage?title=Connection%20Resiliency%20Spec) en Codeplex. Las características principales del mecanismo de reintento son:

* La abstracción principal es la interfaz **IDbExecutionStrategy**. Esta interfaz:
  * Define métodos **Execute*** sincrónicos y asincrónicos.
  * Define las clases que pueden usarse o configurarse directamente en un contexto de base de datos como una estrategia predeterminada, asignada al nombre de un proveedor o asignada a un nombre de proveedor y de servidor. Cuando se configura en un contexto, los reintentos se producen en el nivel de operaciones de base de datos individual, de las que podría haber varias para una operación de contexto especificada.
  * Define cuándo se debe reintentar una conexión fallida y cómo.
* Incluye varias implementaciones integradas de la interfaz **IDbExecutionStrategy**:
  * Predeterminado: ningún reintento.
  * Valor predeterminado para la Base de datos SQL (automático): sin reintento, pero inspecciona las excepciones y las ajusta con la recomendación de usar la estrategia de Base de datos SQL.
  * Predeterminado para la Base de datos SQL: exponencial (heredado de la clase base) más la lógica de detección de la Base de datos SQL.
* Implementa una estrategia de retroceso exponencial que incluye la selección aleatoria.
* Las clases de reintento integradas tienen estado y no están protegidas para subprocesos. Sin embargo, se pueden volver a usar una vez completada la operación actual.
* Si se supera el número de reintentos especificado, los resultados se encapsulan en una nueva excepción. No se propaga la excepción actual.

## Configuración de directiva (Base de datos SQL mediante Entity Framework 6)

Al obtener acceso a la base de datos de SQL mediante Entity Framework 6.0 y versiones posteriors, se proporciona compatibilidad con los reintentos. Las directivas de reintento se configuran mediante programación. No se puede cambiar la configuración en cada operación.

Al configurar una estrategia en el contexto como valor predeterminado, especifique una función que cree una nueva estrategia a petición. El código siguiente muestra cómo puede crear una clase de configuración de reintento que amplíe la clase base **DbConfiguration**.

```csharp
public class BloggingContextConfiguration : DbConfiguration
{
  public BlogConfiguration()
  {
    // Set up the execution strategy for SQL Database (exponential) with 5 retries and 4 sec delay
    this.SetExecutionStrategy(
         "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4)));
  }
}
```

A continuación, puede especificar esto como estrategia de reintento predeterminada para todas las operaciones mediante el método **SetConfiguration** de la instancia **DbConfiguration** cuando se inicia la aplicación. De forma predeterminada, EF detectará y usará automáticamente la clase de configuración.

	DbConfiguration.SetConfiguration(new BloggingContextConfiguration());

Puede especificar la clase de configuración de reintento para un contexto anotando la clase de contexto con un atributo **DbConfigurationType**. Sin embargo, si solo tiene una clase de configuración, EF la usará sin necesidad de anotar el contexto.

	[DbConfigurationType(typeof(BloggingContextConfiguration))]
	public class BloggingContext : DbContext
	{ ...

Si necesita usar estrategias de reintento diferentes para operaciones específicas o deshabilitar reintentos para operaciones específicas, puede crear una clase de configuración que permita suspender o intercambiar estrategias estableciendo un indicador en **CallContext**. La clase de configuración puede usar este indicador para cambiar las estrategias o deshabilitar la estrategia proporcionada por usted y usar una estrategia predeterminada. Para obtener más información, consulte [Suspender la estrategia de ejecución](http://msdn.microsoft.com/dn307226#transactions_workarounds) en la página Limitaciones con estrategias de ejecución reintentos (EF6 y versiones posteriores).

Otra técnica para el uso de estrategias de reintento específica para las operaciones individuales es crear una instancia de la clase de estrategia necesaria y proporcionar la configuración deseada a través de parámetros. A continuación, invoque su método **ExecuteAsync**.

	var executionStrategy = new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(4));
	var blogs = await executionStrategy.ExecuteAsync(
	    async () =>
	    {
	        using (var db = new BloggingContext("Blogs"))
	        {
	            // Acquire some values asynchronously and return them
	        }
	    },
	    new CancellationToken()
	);

La manera más sencilla de usar una clase **DbConfiguration** es buscarla en el mismo ensamblado que la clase **DbContext**. Sin embargo, esto no es adecuado cuando se requiere el mismo contexto en diferentes escenarios, como diferentes estrategias de reintento interactivo y en segundo plano. Si los contextos diferentes se ejecutan en dominios de aplicación independientes, puede usar la compatibilidad integrada para especificar clases de configuración en el archivo de configuración o establecerla explícitamente mediante código. Si se deben ejecutar los contextos diferentes en el mismo dominio de aplicación, se requerirá una solución personalizada.

Para obtener más información, consulte [Configuración basada en código (EF6 y versiones posteriores)](http://msdn.microsoft.com/data/jj680699.aspx).

La siguiente tabla muestra la configuración predeterminada de las directivas de reintento integradas cuando se usa EF6.

![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable4.png)
## Instrucciones de uso del reintento

Al obtener acceso a la Base de datos SQL mediante EF6, tenga en cuenta las siguientes directrices:

* Elija la opción de servicio adecuada (compartida o premium). Una instancia compartida puede sufrir retrasos en la conexión más largos de lo habitual y limitaciones debido al uso de otros inquilinos del servidor compartido. Si se requieren un rendimiento predecible y operaciones de latencia baja confiable, considere la posibilidad de elegir la opción premium.
* No se recomienda una estrategia de intervalo fijo para su uso con la Base de datos SQL de Azure. En su lugar, use una estrategia de retroceso exponencial debido a que el servicio esté sobrecargado, y retrasos más prolongados permiten un mayor tiempo para la recuperación.
* Elija un valor adecuado para los tiempos de espera de conexión y comando a la hora de definir las conexiones. Base el tiempo de espera en el diseño de la lógica de negocios y a través de pruebas. Puede que necesite modificar este valor con el tiempo a medida que los volúmenes de datos o los procesos empresariales cambien. Un tiempo de espera demasiado corto puede provocar errores prematuros en las conexiones cuando la base de datos está ocupada. Un tiempo de espera demasiado largo puede impedir que la lógica de reintento funcione correctamente esperando demasiado tiempo antes de detectar un error en la conexión. El valor de tiempo de espera es un componente de la latencia de extremo a extremo, aunque no podrá determinar fácilmente cuántos comandos se ejecutarán cuando se guarde el contexto. Puede cambiar el tiempo de espera predeterminado estableciendo la propiedad **CommandTimeout** de la instancia **DbContext**.
* Entity Framework admite configuraciones de reintento definidas en archivos de configuración. Sin embargo, para obtener la máxima flexibilidad en Azure puede crear la configuración mediante programación dentro de la aplicación. Los parámetros específicos para las directivas de reintento, como el número de reintentos y los intervalos de reintento, se pueden almacenar en el archivo de configuración del servicio y usar en tiempo de ejecución para crear las directivas apropiadas. Esto permite cambiar la configuración, requiriendo que la aplicación se reinicie.

Considere la posibilidad de comenzar con la configuración siguiente para volver a intentar las operaciones. No se puede especificar el retraso entre reintentos (es fijo y se genera como una secuencia exponencial). Puede especificar solo los valores máximos, como se muestra aquí, a menos que cree una estrategia de reintento personalizada. Esta es la configuración de propósito general, y debe supervisar las operaciones y ajustar los valores para adaptarlos a su propio escenario.

| **Contexto** | **Destino de ejemplo E2E<br />máximo de latencia** | **Directiva de reintentos** | **Configuración** | **Valores** | **Cómo funciona** |
|----------------------|-----------------------------------|--------------------|------------------------|--------------|-----------------------------------------------------------------------------------------------------------------------------|
| Interactivo, interfaz de usuario<br />o primer plano | 2 segundos | Exponencial | MaxRetryCount<br />MaxDelay | 3<br />750 ms | Intento 1 - retraso de 0 segundos<br />Intento 2 - retraso de 750 ms<br />Intento 3 – retraso de 750 ms |
| Segundo plano<br /> o proceso por lotes | 30 segundos | Exponencial | MaxRetryCount<br />MaxDelay | 5<br />12 segundos | Intento 1 - retraso de 0 segundos<br />Intento 2 - retraso de ~1 segundos<br />Intento 3 - retraso de ~3 segundos<br />Intento 4 - retraso de ~7 segundos<br />Intento 5 - retraso de 12 segundos |

> [AZURE.NOTE] Los destinos de latencia de extremo a extremo suponen el tiempo de espera predeterminado para las conexiones con el servicio. Si especifica tiempos de espera de conexión más largos, la latencia de extremo a extremo se extenderá este tiempo adicional en cada reintento.

## Ejemplos (Base de datos SQL mediante Entity Framework 6)

En el ejemplo de código siguiente se define una solución de acceso de datos simple que utiliza Entity Framework. Establece una estrategia de reintento específica mediante la definición de una instancia de una clase denominada **BlogConfiguration** que extiende **DbConfiguration**.

```csharp
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.SqlServer;
using System.Threading.Tasks;

namespace RetryCodeSamples
{
	public class BlogConfiguration : DbConfiguration
	{
	    public BlogConfiguration()
	    {
	        // Set up the execution strategy for SQL Database (exponential) with 5 retries and 12 sec delay.
	        // These values could be loaded from configuration rather than being hard-coded.
	        this.SetExecutionStrategy(
	                "System.Data.SqlClient", () => new SqlAzureExecutionStrategy(5, TimeSpan.FromSeconds(12)));
	    }
	}

	// Specify the configuration type if more than one has been defined.
	// [DbConfigurationType(typeof(BlogConfiguration))]
	public class BloggingContext : DbContext
	{
	    // Definition of content goes here.
	}

	class EF6CodeSamples
	{
	    public async static Task Samples()
	    {
	        // Execution strategy configured by DbConfiguration subclass, discovered automatically or
	        // or explicitly indicated through configuration or with an attribute. Default is no retries.
	        using (var db = new BloggingContext("Blogs"))
	        {
	            // Add, edit, delete blog items here, then:
	            await db.SaveChangesAsync();
	        }
	    }
	}
}
```

Más ejemplos de uso del mecanismo de reintento de Entity Framework se pueden encontrar en [Resistencia de la conexión/lógica de reintento](http://msdn.microsoft.com/data/dn456835.aspx).

## Más información

* [Guía sobre rendimiento y elasticidad de la Base de datos SQL de Azure](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)

## Base de datos SQL mediante directrices de reintento ADO.NET

Base de datos SQL es una base de datos SQL hospedada que está disponible en una amplia variedad de tamaños y como un servicio estándar (compartido) y premium (no compartido).

### Mecanismo de reintento

Base de datos SQL no tiene soporte integrado para reintentos cuando se tiene acceso mediante ADO.NET. Sin embargo, los códigos de retorno de solicitudes pueden usarse para determinar el motivo del error de una solicitud. La página [Limitación de base de datos de SQL Azure](http://msdn.microsoft.com/library/dn338079.aspx) explica cómo puede la limitación impedir las conexiones, los códigos de retorno para situaciones específicas y cómo puede controlar estos y las operaciones de reintento.

Puede usar el bloque de aplicaciones de manejo de errores transitorios (Topaz) con el paquete de Nuget EnterpriseLibrary.TransientFaultHandling.Data (clase **SqlAzureTransientErrorDetectionStrategy**) para implementar un mecanismo de reintento para Base de datos SQL.

El bloque también proporciona la clase **ReliableSqlConnection**, que implementa la API 1.0 de ADO.NET anterior (**IDbConnection** en lugar de **DbConnection**) y realiza internamente reintentos y administración de conexiones. Aunque resulta útil, esto requiere usar un conjunto diferente de métodos para invocar operaciones con reintentos y no es un simple reemplazo directo. No admite la ejecución asincrónica, que se recomienda al implementar y usar los servicios de Azure. Además, dado que esta clase usa ADO.NET 1.0, no se beneficia de las recientes mejoras y actualizaciones de ADO.NET.

### Configuración de directivas (Base de datos SQL mediante ADO.NET)

El bloque de aplicaciones de control de errores transitorios admite la configuración basada en archivos y mediante programación. En general, debe usar la configuración mediante programación para obtener la máxima flexibilidad (vea las notas de la siguiente sección para obtener más información). El código siguiente, que se ejecutaría una vez al iniciarse la aplicación, crea y rellena un **RetryManager** con una lista de cuatro estrategias de reintento adecuadas para su uso con la Base de datos SQL Azure. También establece las estrategias predeterminadas para el **RetryManager**. Estas son las estrategias que se usarán para las conexiones y comandos si no se especifica una alternativa al crear una conexión o un comando.

```csharp
RetryManager.SetDefault(new RetryManager(
	new List<RetryStrategy> { new ExponentialBackoff(name: "default", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "default sql connection", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "default sql command", retryCount: 3,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true),
	                        new ExponentialBackoff(name: "alt sql", retryCount: 5,
	                                                minBackoff: 	TimeSpan.FromMilliseconds(100),
	                                                maxBackoff: 	TimeSpan.FromSeconds(30),
	                                                deltaBackoff: 	TimeSpan.FromSeconds(1),
	                                                firstFastRetry: true), },
	"default",
	new Dictionary<string, string> {
	    {
	    RetryManagerSqlExtensions.DefaultStrategyConnectionTechnologyName, "default sql connection"
	    },
	    {
	    RetryManagerSqlExtensions.DefaultStrategyCommandTechnologyName, "default sql command"}
	    }));
```

Para obtener información acerca de cómo puede usar las directivas de reintento que ha configurado al acceder a la Base de datos SQL de Azure, consulte la sección [ejemplos](#examples-sql-database-using-ado-net-) siguiente.

En la sección [Estrategias de bloques de aplicaciones de manejo de errores transitorios (Topaz)](#transient-fault-handling-application-block-topaz-strategies) del final de esta guía se muestran estrategias predeterminadas para bloques de aplicaciones de manejo de errores transitorios.

### Instrucciones de uso del reintento

Al obtener acceso a la Base de datos SQL mediante ADO.NET, tenga en cuenta las siguientes directrices:

* Elija la opción de servicio adecuada (compartida o premium). Una instancia compartida puede sufrir retrasos en la conexión más largos de lo habitual y limitaciones debido al uso de otros inquilinos del servidor compartido. Si se requieren más operaciones de rendimiento predecible y de latencia baja confiable, considere la posibilidad de elegir la opción premium.
* Asegúrese de realizar reintentos al nivel o ámbito adecuado para evitar las operaciones no idempotentes que provocan incoherencias en los datos. Lo ideal es que todas las operaciones sean idempotentes para que puedan repetirse sin causar incoherencias. Si no es el caso, el reintento deberá realizarse en un nivel o ámbito que permita que todos los cambios relacionados se deshagan si se produce un error en una operación; por ejemplo, desde dentro de un ámbito transaccional. Para obtener más información, consulte [Capa de acceso a datos de fundamentos del servicio de nube: tratamiento de errores transitorios](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Idempotent_Guarantee).
* No se recomienda una estrategia de intervalo fijo para su uso con la Base de datos SQL de Azure excepto para los escenarios interactivos donde hay solo unos cuantos reintentos a intervalos muy cortos. En su lugar, considere el uso de una estrategia de retroceso exponencial para la mayoría de escenarios.
* Elija un valor adecuado para los tiempos de espera de conexión y comando a la hora de definir las conexiones. Un tiempo de espera demasiado corto puede provocar errores prematuros en las conexiones cuando la base de datos está ocupada. Un tiempo de espera demasiado largo puede impedir que la lógica de reintento funcione correctamente esperando demasiado tiempo antes de detectar un error en la conexión. El valor de tiempo de espera es un componente de la latencia de extremo a extremo; se agrega eficazmente al intervalo entre reintentos especificado en la directiva de reintento para cada reintento.
* Cierre la conexión después de un cierto número de reintentos, incluso cuando se usa una lógica de reintentos de interrupción exponencial y vuelva a intentar la operación en una conexión nueva. Reintentar la misma operación varias veces en la misma conexión puede ser un factor que contribuya a ocasionar problemas de conexión. Para obtener un ejemplo de esta técnica, consulte [Capa de acceso a datos de fundamentos del servicio de nube: tratamiento de errores transitorios](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx).
* Cuando la agrupación de conexiones está en uso (valor predeterminado) es probable que se elija la misma conexión de la agrupación, incluso después de cerrar y volver a abrir una conexión. Si este es el caso, una técnica para resolverlo es llamar al método **ClearPool** de la clase **SqlConnection** para marcar la conexión como no reutilizable. Sin embargo, debería hacerlo solo después de que fallen varios intentos de conexión y solo al encontrar la clase específica de errores transitorios como tiempos de espera SQL (código de error -2) relacionados con conexiones erróneas.
* Si el código de acceso de datos usa las transacciones iniciadas como instancias **TransactionScope**, la lógica de reintento debe volver a abrir la conexión e iniciar un nuevo ámbito de transacción. Por este motivo, el bloque de código que se puede reintentar debe abarcar todo el ámbito de la transacción.
* El bloque de aplicaciones de manejo de errores transitorios admite configuraciones de reintento definidas completamente en archivos de configuración. Sin embargo, para obtener la máxima flexibilidad en Azure puede crear la configuración mediante programación dentro de la aplicación. Los parámetros específicos para las directivas de reintento, como el número de reintentos y los intervalos de reintento, se pueden almacenar en el archivo de configuración del servicio y usar en tiempo de ejecución para crear las directivas apropiadas. Esto permite cambiar la configuración, requiriendo que la aplicación se reinicie.

Considere la posibilidad de comenzar con la configuración siguiente para volver a intentar las operaciones. Esta es la configuración de propósito general, y debe supervisar las operaciones y ajustar los valores para adaptarlos a su propio escenario.

| **Contexto** | **Destino de ejemplo E2E<br />máximo de latencia** | **Estrategia de reintento** | **Configuración** | **Valores** | **Cómo funciona** |
|----------------------|-----------------------------------|--------------------|-----------------------------------------------------------------------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Interactivo, interfaz de usuario<br />o primer plano | 2 segundos | FixedInterval | Número de reintentos<br />Intervalo de reintento<br />Primer reintento rápido | 3<br />500 ms<br />true | Intento 1 - retraso de 0 segundos<br />Intento 2 - retraso de 500 ms<br />Intento 3 – retraso de 500 ms |
| Fondo<br />o proceso por lotes | 30 segundos | ExponentialBackoff | Número de reintentos<br />Interrupción mínima<br />Interrupción máxima<br />Interrupción delta<br />primer reintento rápido | 5<br />0 segundos<br />60 segundos<br />2 segundos<br />false | Intento 1 - retraso de 0 segundos<br />Intento 2 - retraso de ~2 segundos<br />Intento 3 - retraso de ~6 segundos<br />Intento 4 - retraso de ~14 segundos<br />Intento 5 - retraso de ~30 segundos |

> [AZURE.NOTE] Los destinos de latencia de extremo a extremo suponen el tiempo de espera predeterminado para las conexiones con el servicio. Si especifica tiempos de espera de conexión más largos, la latencia de extremo a extremo se extenderá este tiempo adicional en cada reintento.

### Ejemplos (Base de datos SQL mediante ADO.NET)

Esta sección describe cómo puede usar el bloque de aplicaciones de manejo de errores transitorios para tener acceso a la Base de datos SQL de Azure mediante un conjunto de directivas de reintento configurado en el **RetryManager** (como se muestra en la sección anterior [Configuración de directivas](#policy-configuration-sql-database-using-ado-net-). La manera más sencilla de usar el bloque es a través de la clase **ReliableSqlConnection** o al llamar a los métodos de extensión como **OpenWithRetry** en una conexión (consulte [Bloque de aplicaciones de control de errores transitorios](http://msdn.microsoft.com/library/hh680934.aspx) para obtener más información).

Sin embargo, en la versión actual del bloque de aplicaciones de control de errores transitorios estos enfoques no admiten las operaciones asincrónicas en la Base de datos SQL de manera nativa. Una buena práctica requiere usar solamente técnicas asincrónicas para tener acceso a servicios de Azure como Base de datos SQL, y por lo tanto debe tener en cuenta las siguientes técnicas para usar el bloque de aplicaciones de control de errores transitorios con la Base de datos SQL.

Puede usar la compatibilidad asincrónica simplificada en la versión 5 del lenguaje C# para crear versiones asincrónicas de los métodos proporcionados por el bloque. Por ejemplo, el código siguiente muestra cómo es posible crear una versión asincrónica del método de extensión **ExecuteReaderWithRetry**. Se resaltan los cambios e incorporaciones al código original. El código fuente de Topaz está disponible en Codeplex en [Transient Fault Handling Application Block ("Topaz")](http://topaz.codeplex.com/SourceControl/latest) (Bloque de aplicación de control de errores transitorios ("Topaz")).

```csharp
public async static Task<SqlDataReader> ExecuteReaderWithRetryAsync(this SqlCommand command, RetryPolicy cmdRetryPolicy,
RetryPolicy conRetryPolicy)
{
	GuardConnectionIsNotNull(command);

	// Check if retry policy was specified, if not, use the default retry policy.
	return await (cmdRetryPolicy ?? RetryPolicy.NoRetry).ExecuteAsync(async () =>
	{
	    var hasOpenConnection = await EnsureValidConnectionAsync(command, conRetryPolicy).ConfigureAwait(false);

	    try
	    {
	        return await command.ExecuteReaderAsync().ConfigureAwait(false);
	    }
	    catch (Exception)
	    {
	        if (hasOpenConnection && command.Connection != null && command.Connection.State == ConnectionState.Open)
	        {
	            command.Connection.Close();
	        }

	        throw;
	    }
	}).ConfigureAwait(false);
}
```

Este nuevo método de extensión asincrónica puede usarse en la misma forma que las versiones sincrónicas incluidas en el bloque.

```csharp
var sqlCommand = sqlConnection.CreateCommand();
sqlCommand.CommandText = "[some query]";

var retryPolicy =
	RetryManager.Instance.GetRetryPolicy<SqlDatabaseTransientErrorDetectionStrategy>("alt sql");
using (var reader = await sqlCommand.ExecuteReaderWithRetryAsync(retryPolicy))
{
	// Do something with the values
}
```

Sin embargo, este enfoque se ocupa solo de operaciones o comandos individuales y no bloques de instrucciones en los que puede haber límites transaccionales correctamente definidos. Además, no trata la situación de quitar las conexiones erróneas de la agrupación de conexiones para que no se seleccionen para los intentos posteriores. Puede encontrar un ejemplo sincrónico para resolver estos problemas en [Capa de acceso a datos de fundamentos del servicio de nube: tratamiento de errores transitorios](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx#Timeouts_amp_Connection_Management). Además de volver a intentar secuencias arbitrarias de instrucciones de la base de datos, borra la agrupación de conexiones para quitar las conexiones no válidas e instrumenta todo el proceso. Mientras que el código mostrado en este ejemplo es sincrónico, es relativamente fácil convertirlo en código asincrónico.

### Más información

Para obtener información detallada acerca de cómo usar el bloque de aplicaciones de control de errores transitorios, consulte:

* [Uso del bloque de aplicaciones de control de errores transitorios con SQL Azure](http://msdn.microsoft.com/library/hh680899.aspx)
* [La perseverancia, el secreto de todos los triunfos: uso del bloque de aplicaciones de control de errores transitorios](http://msdn.microsoft.com/library/dn440719.aspx)
* [Capa de acceso a datos de fundamentos del servicio de nube: tratamiento de errores transitorios](http://social.technet.microsoft.com/wiki/contents/articles/18665.cloud-service-fundamentals-data-access-layer-transient-fault-handling.aspx)

Para obtener instrucciones generales sobre cómo obtener el máximo partido de la Base de datos SQL, consulte:

* [Guía sobre rendimiento y elasticidad de la Base de datos SQL de Azure](http://social.technet.microsoft.com/wiki/contents/articles/3507.windows-azure-sql-database-performance-and-elasticity-guide.aspx)
* [Minimización de los errores de la agrupación de conexiones en SQL Azure](http://blogs.msdn.com/b/adonet/archive/2011/11/05/minimizing-connection-pool-errors-in-sql-azure.aspx)

## Directrices de reintento de Bus de servicio

Bus de servicio es una plataforma de mensajería de nube que proporciona el intercambio de mensajes de acoplamiento flexible con mejor escala y resistencia para los componentes de una aplicación, ya esté hospedada en la nube o localmente.

### Mecanismo de reintento

Bus de servicio implementa reintentos mediante implementaciones de la clase base [RetryPolicy](http://msdn.microsoft.com/library/microsoft.servicebus.retrypolicy.aspx). Todos los clientes de Bus de servicio exponen una propiedad **RetryPolicy** que puede establecerse en una de las implementaciones de la clase base **RetryPolicy**. Las implementaciones integradas son:

* La [clase RetryExponential](http://msdn.microsoft.com/library/microsoft.servicebus.retryexponential.aspx). Esto expone las propiedades que controlan el intervalo de interrupción, el número de reintentos y la propiedad **TerminationTimeBuffer** que se utiliza para limitar el tiempo total para que se complete la operación.
* La [clase NoRetry](http://msdn.microsoft.com/library/microsoft.servicebus.noretry.aspx). Se utiliza cuando los reintentos en el nivel de la API de Bus de servicio no son necesarios, como cuando otro proceso administra los reintentos como parte de una operación en lotes o de múltiples pasos.

Las acciones del Bus de servicio pueden devolver una amplia gama de excepciones, como se muestra en [Apéndice: excepciones de mensajería](http://msdn.microsoft.com/library/hh418082.aspx). La lista proporciona información sobre si estas indican que la operación de reintento es adecuada. Por ejemplo, un [ServerBusyException](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.serverbusyexception.aspx) indica que el cliente debe esperar durante un período de tiempo y, a continuación, volver a intentar la operación. La aparición de una **ServerBusyException** también hace que el Bus de servicio cambie a un modo diferente, en el que se agrega un retraso adicional de 10 segundos a los retrasos de reintento calculados. Este modo se restablece tras un breve período.

Las excepciones devueltas del Bus de servicio exponen la propiedad **IsTransient** propiedad que indica si el cliente debería reintentar la operación. La directiva **RetryExponential** se basa en la propiedad **IsTransient** de la clase **MessagingException**, que es la clase base para todas las excepciones de Bus de servicio. Si crea implementaciones personalizadas de la clase de base **RetryPolicy**, podría usar una combinación del tipo de excepción y la propiedad **IsTransient** para proporcionar un control más fino sobre las acciones de reintento. Por ejemplo, se podría detectar una **QuotaExceededException** y tomar medidas para vaciar la cola antes de volver a intentar enviar un mensaje.

### Configuración de directivas (Bus de servicio)

Las directivas de reintento se establecen mediante programación y se pueden establecer como una directiva predeterminada para un **NamespaceManager** y una **MessagingFactory**, o por separado para cada cliente de mensajería. Para establecer la directiva de reintentos predeterminada para una sesión de mensajería, establezca la **RetryPolicy** del **NamespaceManager**.

	namespaceManager.Settings.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                                             maxBackoff: TimeSpan.FromSeconds(30),
	                                                             maxRetryCount: 3);

Tenga en cuenta que este código usa parámetros con nombre para obtener una mayor claridad. También puede omitir los nombres porque ninguno de los parámetros es opcional.

	namespaceManager.Settings.RetryPolicy = new RetryExponential(TimeSpan.FromSeconds(0.1),
	                 TimeSpan.FromSeconds(30), TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(5), 3);

Para establecer la directiva de reintentos predeterminada para todos los clientes creada a partir de una fábrica de mensajería, establezca la **RetryPolicy** de la **MessagingFactory**.

	messagingFactory.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                                    maxBackoff: TimeSpan.FromSeconds(30),
	                                                    maxRetryCount: 3);

Para establecer la directiva de reintentos para un cliente de mensajería o para invalidar su directiva predeterminada, establezca su propiedad **RetryPolicy** mediante una instancia de la clase de directiva requerida:

```csharp
client.RetryPolicy = new RetryExponential(minBackoff: TimeSpan.FromSeconds(0.1),
	                                        maxBackoff: TimeSpan.FromSeconds(30),
	                                        maxRetryCount: 3);
```

No se puede establecer la directiva de reintentos en el nivel de operación individual. Se aplica a todas las operaciones para el cliente de mensajería. La siguiente tabla muestra la configuración predeterminada de la directiva de reintento integrada.

![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable7.png)

### Instrucciones de uso del reintento

Cuando se usa el Bus de servicio, tenga en cuenta las siguientes directrices:

* Al utilizar la implementación **RetryExponential** integrada, no implemente una operación de reserva, ya que la directiva reacciona a las excepciones de servidor ocupado y automáticamente cambia a un modo de reintentos adecuado.
* Bus de servicio admite una característica denominada espacios de nombres emparejados, que implementa la conmutación automática por error a una cola de copia de seguridad en un espacio de nombres independiente si se produce un error en la cola del espacio de nombres principal. Los mensajes de la cola secundaria pueden enviarse a la cola principal cuando se recupera. Esta característica ayuda a controlar los errores transitorios. Para obtener más información, consulte [Patrones de mensajería asincrónica y alta disponibilidad](http://msdn.microsoft.com/library/azure/dn292562.aspx).

Considere la posibilidad de comenzar con la configuración siguiente para volver a intentar las operaciones. Esta es la configuración de propósito general, y debe supervisar las operaciones y ajustar los valores para adaptarlos a su propio escenario.


![](media/best-practices-retry-service-specific/RetryServiceSpecificGuidanceTable8.png)

### Telemetría

Bus de servicio registra reintentos como eventos ETW mediante un **EventSource**. Debe asociar un **EventListener** al origen de eventos para capturar los eventos y verlos en el Visor de rendimiento o escribirlos en un registro de destino adecuado. Puede usar el [Bloque de aplicación de registro semántico](http://msdn.microsoft.com/library/dn775006.aspx) para ello. Los eventos de reintento tienen la forma siguiente:

```text
Microsoft-ServiceBus-Client/RetryPolicyIteration
ThreadID="14,500"
FormattedMessage="[TrackingId:] RetryExponential: Operation Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05 at iteration 0 is retrying after 00:00:00.1000000 sleep because of Microsoft.ServiceBus.Messaging.MessagingCommunicationException: The remote name could not be resolved: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3, TimeStamp:9/5/2014 10:00:13 PM."
trackingId=""
policyType="RetryExponential"
operation="Get:https://retry-guidance-tests.servicebus.windows.net/TestQueue/?api-version=2014-05"
iteration="0"
iterationSleep="00:00:00.1000000"
lastExceptionType="Microsoft.ServiceBus.Messaging.MessagingCommunicationException"
exceptionMessage="The remote name could not be resolved: 'retry-guidance-tests.servicebus.windows.net'.TrackingId:6a26f99c-dc6d-422e-8565-f89fdd0d4fe3,TimeStamp:9/5/2014 10:00:13 PM"
```

### Ejemplos (Bus de servicio)

En el siguiente ejemplo de código se muestra cómo establecer la directiva de reintentos para:

* Un administrador de espacio de nombres. La directiva se aplica a todas las operaciones de ese administrador y no se puede reemplazar para las operaciones individuales.
* Una fábrica de mensajería. La directiva se aplica a todos los clientes que se crean a partir de ese generador y no se puede invalidar al crear clientes individuales.
* Un cliente de mensajería individual. Después de crear un cliente, puede establecer la directiva de reintentos para el cliente. La directiva se aplica a todas las operaciones de ese cliente.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Messaging;

namespace RetryCodeSamples
{
	class ServiceBusCodeSamples
	{
		private const string connectionString =
		    @"Endpoint=sb://[my-namespace].servicebus.windows.net/;
		        SharedAccessKeyName=RootManageSharedAccessKey;
		        SharedAccessKey=C99..........Mk=";

		public async static Task Samples()
		{
		    const string QueueName = "TestQueue";

		    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.Http;

		    var namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);

		    // The namespace manager will have a default exponential policy with 10 retry attempts
		    // and a 3 second delay delta.
		    // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
		    // with an extra 10 sec added when receiving a ServiceBusyException.

		    {
		        // Set different values for the retry policy, used for all operations on the namespace manager.
		        namespaceManager.Settings.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(0),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                maxRetryCount: 3);

		        // Policies cannot be specified on a per-operation basis.
		        if (!await namespaceManager.QueueExistsAsync(QueueName))
		        {
		            await namespaceManager.CreateQueueAsync(QueueName);
		        }
		    }


		    var messagingFactory = MessagingFactory.Create(
		        namespaceManager.Address, namespaceManager.Settings.TokenProvider);
		    // The messaging factory will have a default exponential policy with 10 retry attempts
		    // and a 3 second delay delta.
		    // Retry delays will be approximately 0 sec, 3 sec, 9 sec, 25 sec and the fixed 30 sec,
		    // with an extra 10 sec added when receiving a ServiceBusyException.

		    {
		        // Set different values for the retry policy, used for clients created from it.
		        messagingFactory.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(1),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                maxRetryCount: 3);


		        // Policies cannot be specified on a per-operation basis.
		        var session = await messagingFactory.AcceptMessageSessionAsync();
		    }


		    {
		        var client = messagingFactory.CreateQueueClient(QueueName);
		        // The client inherits the policy from the factory that created it.


		        // Set different values for the retry policy on the client.
		        client.RetryPolicy =
		            new RetryExponential(
		                minBackoff: TimeSpan.FromSeconds(0.1),
		                maxBackoff: TimeSpan.FromSeconds(30),
		                maxRetryCount: 3);


		        // Policies cannot be specified on a per-operation basis.
		        var session = await client.AcceptMessageSessionAsync();
		    }
		}
	}
}
```

## Más información

* [Patrones de mensajería asincrónica y alta disponibilidad.](http://msdn.microsoft.com/library/azure/dn292562.aspx)

## Directrices de reintento de caché (Redis)

Caché en Redis de Azure es un servicio de caché de acceso rápido a datos y de baja latencia basado en la caché en Redis de código abierto popular. Es segura, está administrada por Microsoft y es accesible desde cualquier aplicación en Azure.

Las instrucciones de esta sección se basan en el uso del cliente StackExchange.Redis para tener acceso a la memoria caché. Puede encontrar una lista de otros clientes adecuados en el [sitio web de Redis](http://redis.io/clients), y estos pueden tener mecanismos de reintento diferentes.

Tenga en cuenta que el cliente StackExchange.Redis usa multiplexación a través de una sola conexión. El uso recomendado es crear una instancia del cliente al iniciar la aplicación y usar esta instancia para todas las operaciones en la memoria caché. Por este motivo, la conexión a la memoria caché se realiza solo una vez y todas las instrucciones de esta sección están relacionadas con la directiva de reintentos de esta conexión inicial y no para cada operación que tiene acceso a la memoria caché.

### Mecanismo de reintento

El cliente StackExchange.Redis usa una clase de administrador de conexiones que se configura a través de un conjunto de opciones. Estas opciones incluyen una propiedad **ConnectRetry** que especifica el número de veces que se reintentará un error en la conexión a la memoria caché. Sin embargo, la directiva de reintentos solo se usa para la acción de conexión inicial, y no para esperar entre reintentos.

### Configuración de directivas (Caché en Redis de Azure)

Las directivas de reintento se configuran mediante programación, estableciendo las opciones para el cliente antes de conectarse a la memoria caché. Esto puede hacerse mediante la creación de una instancia de la clase **ConfigurationOptions**, rellenando sus propiedades y pasándola al método **Conectar**.

```csharp
var options = new ConfigurationOptions { EndPoints = { "localhost" },
	                                        ConnectRetry = 3,
	                                        ConnectTimeout = 2000 };
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

Tenga en cuenta que la propiedad **ConnectTimeout** especifica el tiempo de espera máximo en milisegundos), no el intervalo entre reintentos.

Como alternativa, puede especificar las opciones como una cadena y pasar esto al método **Conectar**.

```csharp
	var options = "localhost,connectRetry=3,connectTimeout=2000";
	ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

También es posible especificar opciones directamente al conectarse a la memoria caché.

```csharp
var conn = ConnectionMultiplexer.Connect("redis0:6380,redis1:6380,connectRetry=3");
```

La siguiente tabla muestra la configuración predeterminada de la directiva de reintento integrada.

| **Contexto** | **Configuración** | **Valor predeterminado**<br />(v 1.0.331) | **Significado** |
|----------------------|-----------------------------------------|-----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConfigurationOptions | ConnectRetry<br /><br />ConnectTimeout<br /><br />SyncTimeout | 3<br /><br />Máximo de 5000 ms más SyncTimeout<br />1000 | El número de veces que se repiten los intentos de conexión durante la operación de conexión inicial.<br />Tiempo de espera (ms) para conectar las operaciones. No un retraso entre reintentos.<br />Tiempo (ms) para permitir las operaciones sincrónicas. |

> [AZURE.NOTE] SyncTimeout contribuye a la latencia de extremo a extremo de una operación. Sin embargo, en general, no es recomendable usar operaciones sincrónicas. Para obtener más información, consulte [Canalizaciones y multiplexores](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md).

## Instrucciones de uso del reintento

Tenga en cuenta las siguientes directrices cuando use la Caché en Redis de Azure:

* El cliente Redis StackExchange administra sus propios reintentos, pero solo al establecer una conexión a la memoria caché cuando la aplicación se inicia por primera vez. Puede configurar el tiempo de espera de conexión y el número de reintentos para establecer esta conexión, pero la directiva de reintentos no se aplica a las operaciones en la memoria caché.
* El mecanismo de reintento no tiene ningún retraso entre reintentos. Simplemente reintenta una conexión incorrecta una vez transcurrido el tiempo de espera de conexión especificado y el número de veces especificado.
* En lugar de usar un gran número de reintentos, considere la posibilidad de recurrir a obtener acceso a un origen de datos original en su lugar.

## Telemetría

Puede recopilar información acerca de las conexiones (pero no otras operaciones) con un **TextWriter**.

```csharp
var writer = new StringWriter();
...
ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);
```

A continuación, se muestra un ejemplo del resultado que este genera.

```text
localhost:6379,connectTimeout=2000,connectRetry=3
1 unique nodes specified
Requesting tie-break from localhost:6379 > __Booksleeve_TieBreak...
Allowing endpoints 00:00:02 to respond...
localhost:6379 faulted: SocketFailure on PING
localhost:6379 failed to nominate (Faulted)
> UnableToResolvePhysicalConnection on GET
No masters detected
localhost:6379: Standalone v2.0.0, master; keep-alive: 00:01:00; int: Connecting; sub: Connecting; not in use: DidNotRespond
localhost:6379: int ops=0, qu=0, qs=0, qc=1, wr=0, sync=1, socks=2; sub ops=0, qu=0, qs=0, qc=0, wr=0, socks=2
Circular op-count snapshot; int: 0 (0.00 ops/s; spans 10s); sub: 0 (0.00 ops/s; spans 10s)
Sync timeouts: 0; fire and forget: 0; last heartbeat: -1s ago
resetting failing connections to retry...
retrying; attempts left: 2...
...
```

## Ejemplos (Caché en Redis de Azure)

En el ejemplo de código siguiente se muestra cómo configurar el valor de tiempo de espera de conexión y el número de reintentos al inicializar el cliente StackExchange.Redis para tener acceso a la Caché en Redis de Azure al iniciarse la aplicación. Tenga en cuenta que el tiempo de espera de conexión es el período de tiempo que está dispuesto a esperar para la conexión a la memoria caché; no es el retardo entre los reintentos.

Este ejemplo muestra cómo establecer la configuración mediante una instancia de las **ConfigurationOptions**.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
	class CacheRedisCodeSamples
	{
	    public async static Task Samples()
	    {
	        var writer = new StringWriter();

	        {
	            try
	            {
	                // Using object-based configuration.
	                var options = new ConfigurationOptions
	                                    {
	                                        EndPoints = { "localhost" },
	                                        ConnectRetry = 3,
	                                        ConnectTimeout = 2000  // The maximum waiting time (ms), not the delay for retries.
	                                    };
	                ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

	                // Store a reference to the multiplexer for use in the application.
	            }
	            catch
	            {
	                Console.WriteLine(writer.ToString());
	                throw;
	            }
	        }
	    }
	}
}
```

En este ejemplo se muestra cómo establecer la configuración mediante la especificación de las opciones como una cadena.

```csharp
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using StackExchange.Redis;

namespace RetryCodeSamples
{
	class CacheRedisCodeSamples
	{
	    public async static Task Samples()
	    {
	        var writer = new StringWriter();

	        {
	            try
	            {
	                // Using string-based configuration.
	                var options = "localhost,connectRetry=3,connectTimeout=2000";
	                ConnectionMultiplexer redis = ConnectionMultiplexer.Connect(options, writer);

	                // Store a reference to the multiplexer for use in the application.
	            }
	            catch
	            {
	                Console.WriteLine(writer.ToString());
	                throw;
	            }
	        }
	    }
	}
}
```

Para obtener más ejemplos, consulte [Configuración](http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md#configuration) en el sitio web del proyecto.

## Más información

* [Sitio web de Redis](http://redis.io/)

## Directrices de reintento de DocumentDB (versión preliminar)

DocumentDB es una base de datos como servicio de documentos totalmente administrada con capacidades de indización y consulta completa sobre un modelo de datos JSON sin esquema. Ofrece un rendimiento confiable y configurable, procesamiento transaccional de JavaScript nativo y se ha creado para la nube con la escala elástica.

## Mecanismo de reintento

La versión preliminar del cliente DocumentDB incluye un mecanismo de reintento interno y no configurable (esto puede cambiar en versiones posteriores). La configuración predeterminada para esto varía dependiendo del contexto de su uso. Algunas operaciones usan una estrategia de retroceso exponencial con parámetros codificados de forma rígida. Otros usuarios especifican solo los reintentos que deben efectuarse y usan el intervalo entre reintentos de la instancia [DocumentClientException](http://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx) que se devuelve desde el servicio. Si no se especifica ningún retraso, se usa un retraso de cinco segundos.

## Configuración de directivas (DocumentDB)

Ninguno. Todas las clases que se usan para implementar los reintentos son internas. Los parámetros de reintento son constantes o se establecen usando parámetros de los constructores de clase.

La siguiente tabla muestra la configuración predeterminada de la directiva de reintento integrada.

| **Contexto** | **Configuración** | **Valores** | **Cómo funciona** |
|------------------------|---------------------------------------------------|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RetryPolicy (interno) | MaxRetryAttemptsOnQuery<br /><br />MaxRetryAttemptsOnRequest | 3<br /><br />0 | Número de reintentos para las consultas de documento. No se puede cambiar este valor.<br />Número de reintentos para otras solicitudes. No se puede cambiar este valor. |

## Instrucciones de uso del reintento

Cuando se usa DocumentDB, tenga en cuenta las siguientes directrices:

* No se puede cambiar la directiva de reintentos predeterminada.
* Para obtener más información sobre la configuración predeterminada, consulte [TBD].

## Telemetría

Los reintentos se registran como mensajes de seguimiento no estructurados a través de .NET **TraceSource**. Debe configurar un **TraceListener** para capturar los eventos y escribirlos en un registro de destino adecuado.

## Directrices de reintento de búsqueda

Búsqueda de Azure puede usarse para agregar capacidades de búsqueda eficaces y sofisticadas a un sitio web o aplicación, ajustar de manera rápida y fácil los resultados de la búsqueda y construir modelos de clasificación enriquecidos y optimizados.

### Mecanismo de reintento

No hay ningún mecanismo de reintento integrado para la búsqueda, ya que el uso normal se efectúa a través de solicitudes HTTP. Para implementar los reintentos puede usar una implementación genérica de un cliente REST y tomar decisiones sobre cuándo y si reintentar la operación en función de la respuesta del servicio. Para obtener más información, consulte la sección [Directrices generales de REST y de reintento](#general-rest-and-retry-guidelines) más adelante en esta guía.

### Instrucciones de uso del reintento

Tenga en cuenta las siguientes directrices cuando use la Búsqueda de Azure:

* Use el código de estado devuelto por el servicio para determinar el tipo de error. Los códigos de estado se definen en [Códigos de estado HTTP (Búsqueda de Azure)](http://msdn.microsoft.com/library/dn798925.aspx). El código de estado 503 (servicio no disponible) indica que el servicio está sobrecargado y no se puede procesar la solicitud de inmediato. La acción apropiada es volver a intentar la operación únicamente tras dejar tiempo para que se recupere el servicio. Es probable que efectuar el reintento tras un intervalo demasiado corto prolongue la no disponibilidad.
* Consulte la sección [Directrices generales de REST y de reintento](#general-rest-and-retry-guidelines) más adelante en esta guía para obtener información general acerca del reintento de las operaciones REST.

## Más información

* [API de REST de Búsqueda de Azure](http://msdn.microsoft.com/library/dn798935.aspx)

## Directrices de reintento de Azure Active Directory

Azure Active Directory (AD) es una solución de nube de administración de identidades y accesos integral que combina servicios de directorio de núcleo, gobierno de identidades avanzado, seguridad y administración de acceso a aplicaciones. Azure AD también ofrece a los desarrolladores una plataforma de administración de identidades para proporcionar control de acceso a sus aplicaciones, según las reglas y directivas centralizadas.

### Mecanismo de reintento

No hay ningún mecanismo de reintento integrado para Azure Active Directory en la biblioteca de autenticación de Active Directory (ADAL). Puede usar el bloque de aplicaciones de control de errores transitorios para implementar una estrategia de reintento que contiene un mecanismo de detección personalizado para las excepciones devueltas por Active Directory.

### Configuración de directivas (Azure Active Directory)

Cuando se usa el bloque de aplicaciones de control de errores transitorios con Azure Active Directory crea una instancia de **RetryPolicy** basada en una clase que define la estrategia de detección que desea usar.

```csharp
var policy = new RetryPolicy<AdalDetectionStrategy>(new ExponentialBackoff(retryCount: 5,
	                                                                 minBackoff: TimeSpan.FromSeconds(0),
	                                                                 maxBackoff: TimeSpan.FromSeconds(60),
	                                                                 deltaBackoff: TimeSpan.FromSeconds(2)));
```

A continuación, llame al método **ExecuteAction** o **ExecuteAsync** de la directiva de reintento, pasando la operación que desea ejecutar.

```csharp
var result = await policy.ExecuteAsync(() => authContext.AcquireTokenAsync(resourceId, clientId, uc));
```

La clase de estrategia de detección recibe excepciones cuando se produce un error y debe detectar si es probable que sea un error transitorio o un error más permanente. Normalmente lo hará examinando el código de estado y el tipo de excepción. Por ejemplo, una respuesta de servicio no disponible indica que debe realizarse un reintento. El bloque de aplicaciones de control de errores transitorios no incluye una clase de estrategia de detección que sea adecuada para su uso con el cliente AAL, pero se proporciona un ejemplo de una estrategia de detección personalizada en la sección [Ejemplos](#examples-azure-active-directory-) disponible a continuación. Usar una estrategia de detección personalizada no es diferente de usar una suministrada con el bloque.

En la sección [Estrategias de bloques de aplicaciones de manejo de errores transitorios (Topaz)](#transient-fault-handling-application-block-topaz-strategies) del final de esta guía se muestran estrategias predeterminadas para bloques de aplicaciones de manejo de errores transitorios.

## Instrucciones de uso del reintento

Tenga en cuenta las siguientes directrices cuando use Azure Active Directory:

* Si está usando la API de REST para Azure Active Directory, debe reintentar la operación solo si el resultado es un error en el intervalo de 5xx (por ejemplo, Error de servidor interno 500, Puerta de enlace incorrecta 502, Servicio no disponible 503 y Tiempo de espera de puerta de enlace 504). No lo intente de nuevo para cualquier otro error.
* Si está usando la biblioteca de autenticación de Active Directory (ADAL), los códigos HTTP no serán de fácil acceso. Necesitará crear una estrategia de detección personalizada que incluya la lógica para comprobar las propiedades de las excepciones específicas de ADAL. Consulte la sección de [ejemplos](#examples-azure-active-directory-) disponible a continuación.
* Se recomienda una directiva de retroceso exponencial para su uso en escenarios de lotes con Azure Active Directory.

Considere la posibilidad de comenzar con la configuración siguiente para volver a intentar las operaciones. Esta es la configuración de propósito general, y debe supervisar las operaciones y ajustar los valores para adaptarlos a su propio escenario.


| **Contexto** | **Destino de ejemplo E2E<br />máximo de latencia** | **Estrategia de reintento** | **Configuración** | **Valores** | **Cómo funciona** |
|----------------------|----------------------------------------------|--------------------|-----------------------------------------------------------------------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Interactivo, interfaz de usuario<br />o primer plano | 2 segundos | FixedInterval | Número de reintentos<br />Intervalo de reintento<br />Primer reintento rápido | 3<br />500 ms<br />true | Intento 1 - retraso de 0 segundos<br />Intento 2 - retraso de 500 ms<br />Intento 3 – retraso de 500 ms |
| Segundo plano o<br />proceso por lotes | 60 segundos | ExponentialBackoff | Número de reintentos<br />Interrupción mínima<br />Interrupción máxima<br />Interrupción delta<br />primer reintento rápido | 5<br />0 segundos<br />60 segundos<br />2 segundos<br />false | Intento 1 - retraso de 0 segundos<br />Intento 2 - retraso de ~2 segundos<br />Intento 3 - retraso de ~6 segundos<br />Intento 4 - retraso de ~14 segundos<br />Intento 5 - retraso de ~30 segundos |

## Ejemplos (Azure Active Directory)

En el ejemplo de código siguiente se muestra cómo puede usar el bloque de aplicaciones de manejo de errores transitorios (Topaz) para definir una estrategia de detección de errores transitorios personalizada apropiada para su uso con el cliente ADAL. El código crea una nueva instancia **RetryPolicy** basada en una estrategia de detección personalizada de tipo **AdalDetectionStrategy**, tal como se define en la lista de código siguiente. Las estrategias de detección personalizadas para Topaz implementan la interfaz **ITransientErrorDetectionStrategy** y devuelven el valor true si debe realizarse un reintento, o **false** si el error parece no transitorio y no se debe intentar un reintento.

	using System;
	using System.Linq;
	using System.Net;
	using System.Threading.Tasks;
	using Microsoft.Practices.TransientFaultHandling;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;

	namespace RetryCodeSamples
	{
	    class ActiveDirectoryCodeSamples
	    {
	        public async static Task Samples()
	        {
	            var authority = "[some authority]";
	            var resourceId = “[some resource id]”;
	            var clientId = “[some client id]”;

	            var authContext = new AuthenticationContext(authority);

	            var uc = new UserCredential(“[user]", "[password]");

	            // Use Topaz with a custom detection strategy to manage retries.
	            var policy =
	                new RetryPolicy<AdalDetectionStrategy>(
	                    new ExponentialBackoff(
	                        retryCount: 5,
	                        minBackoff: TimeSpan.FromSeconds(0),
	                        maxBackoff: TimeSpan.FromSeconds(60),
	                        deltaBackoff: TimeSpan.FromSeconds(2)));

	            var result = await policy.ExecuteAsync(() => authContext.AcquireTokenAsync(resourceId, clientId, uc));

	            // Get the access token
	            var accessToken = result.AccessToken;

	            // Use the result, probably to authorize an API call.
	        }
	    }

	    // TODO: This is sample code that needs validation from the WAAD team!
	    // based on existing detection strategies
	    public class AdalDetectionStrategy : ITransientErrorDetectionStrategy
	    {
	        private static readonly WebExceptionStatus[] webExceptionStatus =
	            new[]
	            {
	                WebExceptionStatus.ConnectionClosed,
	                WebExceptionStatus.Timeout,
	                WebExceptionStatus.RequestCanceled
	            };

	        private static readonly HttpStatusCode[] httpStatusCodes =
	            new[]
	            {
	                HttpStatusCode.InternalServerError,
	                HttpStatusCode.GatewayTimeout,
	                HttpStatusCode.ServiceUnavailable,
	                HttpStatusCode.RequestTimeout
	            };

	        public bool IsTransient(Exception ex)
	        {
	            var adalException = ex as AdalException;
	            if (adalException == null)
	            {
	                return false;
	            }

	            if (adalException.ErrorCode == AdalError.ServiceUnavailable)
	            {
	                return true;
	            }

	            var innerWebException = adalException.InnerException as WebException;
	            if (innerWebException != null)
	            {
	                if (webExceptionStatus.Contains(innerWebException.Status))
	                {
	                    return true;
	                }

	                if (innerWebException.Status == WebExceptionStatus.ProtocolError)
	                {
	                    var response = innerWebException.Response as HttpWebResponse;
	                    return response != null && httpStatusCodes.Contains(response.StatusCode);
	                }
	            }

	            return false;
	        }
	    }
	}

Para obtener información acerca de cómo reintentar las operaciones de la API Graph de Active Directory y los códigos de error devueltos, consulte:

* [Ejemplo de código: Lógica de reintento](http://msdn.microsoft.com/library/azure/dn448547.aspx)
* [Códigos de error de Graph de Azure AD](http://msdn.microsoft.com/library/azure/hh974480.aspx)

## Más información

* [Implementación de una estrategia de detección personalizada](http://msdn.microsoft.com/library/hh680940.aspx) (Topaz)
* [Implementación de una estrategia de reintento personalizada](http://msdn.microsoft.com/library/hh680943.aspx) (Topaz)
* [Emisión de tokens y directrices de reintento](http://msdn.microsoft.com/library/azure/dn168916.aspx)

## Directrices generales de REST y de reintento

Al obtener acceso a los servicios de Azure o de terceros, tenga en cuenta lo siguiente:

* Use un enfoque sistemático para administrar reintentos, quizás como código reutilizable, para que pueda aplicar una metodología coherente en todos los clientes y todas las soluciones.
* Considere el uso de un marco de reintento como el bloque de aplicaciones de control de errores transitorios para administrar reintentos si el servicio de destino o el cliente no tiene ningún mecanismo de reintento integrado. Esto le ayudará a implementar un comportamiento de reintento coherente y puede proporcionar una estrategia de reintento predeterminada adecuados para el servicio de destino. Sin embargo, puede que necesite crear código de reintento personalizado para los servicios que tienen un comportamiento no estándar, que no dependen de excepciones para indicar errores transitorios o si desea usar una respuesta de **respuesta de reintento** para administrar el comportamiento de reintento.
* La lógica de detección transitoria dependerá de la API de cliente real que use para invocar las llamadas REST. Algunos clientes, como los de la clase **HttpClient** más reciente no producirán excepciones para las solicitudes completadas con un código de estado HTTP no correcto. Esto mejora el rendimiento, pero impide el uso del bloque de aplicaciones de control de errores transitorios. En este caso podría encapsular la llamada a la API de REST con código que produce excepciones para códigos de estado HTTP no correctos que, a continuación, pueden ser procesados por el bloque. Como alternativa, puede usar un mecanismo diferente para controlar los reintentos.
* El código de estado HTTP devuelto desde el servicio puede ayudar a indicar si el error es transitorio. Puede que necesite examinar las excepciones generadas por un cliente o el marco de trabajo de reintento para obtener acceso al código de estado o para determinar el tipo de excepción equivalente. Los siguientes códigos HTTP normalmente indican que un reintento es adecuado:
  * Tiempo de espera de solicitud 408
  * Error de servidor interno 500
  * Puerta de enlace incorrecta 502
  * Servicio no disponible 503
  * Tiempo de espera de puerta de enlace 504
* Si la lógica de reintento se basa en excepciones, las siguientes suelen indican un error transitorio donde no se pudo establecer ninguna conexión:
  * WebExceptionStatus.ConnectionClosed
  * WebExceptionStatus.ConnectFailure
  * WebExceptionStatus.Timeout
  * WebExceptionStatus.RequestCanceled
* En el caso de un estado no disponible del servicio, el servicio podría indicar el retraso adecuado antes de reintentar en el encabezado de respuesta **Reintentar después de** o un encabezado personalizado diferente (como en el servicio DocumentDB). Los servicios también pueden enviar información adicional como encabezados personalizados o incrustados en el contenido de la respuesta. El bloque de aplicaciones de control de errores transitorios no puede usar los encabezados ni ningún encabezado "reintentar después de" personalizado.
* No intente de nuevo los códigos de estado que representan errores de cliente (errores en el intervalo 4xx) excepto un Tiempo de espera de solicitud 408.
* Pruebe las estrategias y mecanismos de reintento a fondo en una amplia variedad de condiciones, como diferentes estados de red diferente y diferentes cargas de sistema.

## Estrategia de reintento

Estos son los tipos típicos de intervalos de estrategia de reintento:

* **Exponencial**: directiva de reintentos que realiza un número especificado de reintentos, usando un enfoque de retroceso exponencial aleatorio para determinar el intervalo entre reintentos. Por ejemplo:

		var random = new Random();

		var delta = (int)((Math.Pow(2.0, currentRetryCount) - 1.0) *
		            random.Next((int)(this.deltaBackoff.TotalMilliseconds * 0.8),
		            (int)(this.deltaBackoff.TotalMilliseconds * 1.2)));
		var interval = (int)Math.Min(checked(this.minBackoff.TotalMilliseconds + delta),
		               this.maxBackoff.TotalMilliseconds);
		retryInterval = TimeSpan.FromMilliseconds(interval);

* **Incremental**: estrategia de reintentos con un número especificado de reintentos y un intervalo de tiempo incremental entre los reintentos. Por ejemplo:

		retryInterval = TimeSpan.FromMilliseconds(this.initialInterval.TotalMilliseconds +
		               (this.increment.TotalMilliseconds * currentRetryCount));

* **LinearRetry**: directiva de reintentos que realiza un número especificado de reintentos, usando un intervalo de tiempo fijo especificado entre los reintentos. Por ejemplo:

		retryInterval = this.deltaBackoff;

## Más información

* [Estrategias de disyuntor](http://msdn.microsoft.com/library/dn589784.aspx)

## Estrategias del bloque de aplicación de control de errores transitorios (Topaz).

El bloque de aplicaciones de control de errores transitorios tiene las siguientes estrategias predeterminadas.

| **Estrategia** | **Configuración** | **Valor predeterminado** | **Significado** |
|-------------------------|-----------------------------------------------------|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Exponencial** | retryCount<br />minBackoff<br /><br />maxBackoff<br /><br />deltaBackoff<br /><br />fastFirstRetry | 10<br />1 segundo<br /><br />30 segundos<br /><br />10 segundos<br /><br />true | Número de reintentos.<br />Tiempo de interrupción mínimo. Se usará como intervalo entre reintentos el máximo de este valor o la interrupción procesada.<br />El tiempo de interrupción mínimo. Se usará el mínimo de este valor o la interrupción calculada como intervalo entre reintentos.<br />Valor usado para calcular un delta aleatorio para el intervalo exponencial entre reintentos.<br />Si el primer reintento se realizará inmediatamente. |
| **Incremental** | retryCount<br />initialInterval<br />increment<br /><br />fastFirstRetry<br />| 10<br />1 segundo<br />1 segundo<br /><br />true | Número de reintentos.<br />Intervalo inicial que se aplicará al primer reintento.<br />Valor de tiempo incremental que se usará para calcular el intervalo entre reintentos progresivo.<br />Si el primer reintento se realizará inmediatamente. |
| **Lineal (intervalo fijo)** | retryCount<br />retryInterval<br />fastFirstRetry<br /> | 10<br />1 segundo<br />true | Número de reintentos.<br />Intervalo entre reintentos.<br />Si el primer reintento se realizará inmediatamente. |
Para obtener ejemplos del uso del bloque de aplicaciones de control de errores transitorios, consulte las secciones de ejemplos mostradas anteriormente en esta guía para la Base de datos SQL de Azure mediante ADO.NET y Azure Active Directory.

<!---HONumber=AcomDC_0128_2016-->