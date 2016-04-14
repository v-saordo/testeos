<properties 
	pageTitle="Habilitar las métricas de almacenamiento en el portal de Azure | Microsoft Azure" 
	description="Cómo habilitar las métricas de almacenamiento para los servicios BLOB, Cola, Tabla y Archivo" 
	services="storage" 
	documentationCenter="" 
	authors="robinsh" 
	manager="carmonm" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/14/2016" 
	ms.author="robinsh"/>

# Habilitar las Métricas de almacenamiento y las Métricas de visualización

[AZURE.INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## Información general

Las Métricas de almacenamiento no están habilitadas de forma predeterminada para los servicios de almacenamiento. Puede habilitar la supervisión mediante el [Portal de Azure clásico](https://manage.windowsazure.com), Windows PowerShell o mediante programación a través de una API de almacenamiento.

Al habilitar las Métricas de almacenamiento, debe elegir un período de retención de datos: este período determina el tiempo que el servicio de almacenamiento mantiene las métricas y cobra por el espacio necesario para almacenarlas. Generalmente, usará un período de retención más corto para la métrica por minuto que para la métrica por hora debido al considerable espacio adicional requerido para la métrica por minuto. Debe elegir un período de retención durante el cual tenga suficiente tiempo para analizar los datos y descargar todas las métricas que quiera mantener para los informes o el análisis sin conexión. Recuerde que también se le facturará por la descarga de los datos de métricas desde su cuenta de almacenamiento.

## Cómo habilitar las Métricas de almacenamiento mediante el Portal de Azure clásico

En el [Portal de Azure clásico](https://manage.windowsazure.com), puede usar la página Configurar de una cuenta de almacenamiento para así controlar las Métricas de almacenamiento. Para supervisarlas, puede establecer un nivel y un período de retención cuantificado en días para cada blob, tabla y cola. En cada caso, el nivel es uno de los siguientes:

- Desactivada: significa que no se recopilan métricas.

- Mínima: las Métricas de almacenamiento recopilan un conjunto de métricas básicas como entrada/salida, disponibilidad, latencia y porcentajes de éxito, que se agregan para los servicios BLOB, tabla y cola.

- Detallada: las Métricas de almacenamiento recopilan un conjunto completo de métricas que incluye las mismas métricas para cada operación de API de almacenamiento, además de las métricas de nivel de servicio. Las métricas detalladas facilitan el análisis preciso de los problemas que se producen durante las operaciones de las aplicaciones.

Tenga en cuenta que el Portal de Azure clásico no permite actualmente configurar métricas por minuto en su cuenta de almacenamiento; debe habilitar las métricas por minuto con PowerShell o mediante programación.


## Cómo habilitar las Métricas de almacenamiento con PowerShell

Puede usar PowerShell en el equipo local para configurar las Métricas de almacenamiento en su cuenta de almacenamiento con el cmdlet Get-AzureStorageServiceMetricsProperty de Azure PowerShell para recuperar la configuración actual y el cmdlet Set-AzureStorageServiceMetricsProperty para cambiar la configuración actual.

Los cmdlets que controlan las Métricas de almacenamiento usan los siguientes parámetros:

- Los valores posibles de MetricsType son Hour y Minute.

- Los valores posibles de ServiceType son Blob, Queue y Table.

- Los valores posibles de MetricsLevel son None (equivalente a Desactivado en el Portal de Azure clásico), Service (equivalente a Mínimo en el Portal de Azure clásico) y ServiceAndApi (equivalente a Detallado en el Portal de Azure clásico).

Por ejemplo, el siguiente comando activa las métricas por minuto para el servicio BLOB en su cuenta de almacenamiento predeterminada con el período de retención establecido en cinco días:

`Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`

El comando siguiente recupera el nivel de métricas por hora actual y los días de retención para el servicio BLOB en su cuenta de almacenamiento predeterminada:

`Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob`

Para obtener información sobre cómo configurar los cmdlets de Azure PowerShell para que funcionen con su suscripción de Azure y cómo seleccionar la cuenta de almacenamiento predeterminada que quiere usar, vea: [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

## Cómo habilitar las Métricas de almacenamiento mediante programación

El siguiente fragmento de C# muestra cómo habilitar el registro y las métricas para el servicio BLOB mediante la biblioteca del cliente de almacenamiento de .NET:

	// Parse connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Enable Storage Analytics logging and set retention policy to 10 days. 
    ServiceProperties properties = new ServiceProperties();
    properties.Logging.LoggingOperations = LoggingOperations.All;
    properties.Logging.RetentionDays = 10;
    properties.Logging.Version = "1.0";

    // Configure service properties for metrics. Both metrics and logging must be set at the same time.
    properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.HourMetrics.RetentionDays = 10;
    properties.HourMetrics.Version = "1.0";

    properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
    properties.MinuteMetrics.RetentionDays = 10;
    properties.MinuteMetrics.Version = "1.0";

    // Set the default service version to be used for anonymous requests.
    properties.DefaultServiceVersion = "2015-04-05";

    // Set the service properties.
    blobClient.SetServiceProperties(properties);
    
## Mostrar Métricas de almacenamiento

Cuando haya configurado las Métricas de almacenamiento para supervisar su cuenta de almacenamiento, registra las métricas en un conjunto de tablas conocidas en su cuenta de almacenamiento. Puede usar la página Supervisar de su cuenta de almacenamiento en el Portal de Azure clásico para ver las métricas por hora a medida que están disponibles en un gráfico. En esta página del Portal de Azure clásico, puede:

- Seleccionar qué métricas quiere trazar en el gráfico (la variedad de métricas disponibles dependerá de si ha elegido la supervisión detallada o mínima para el servicio en la página Configurar).


- Seleccionar el intervalo de tiempo para las métricas mostradas en el gráfico.


- Elegir el uso de una escala absoluta o relativa para trazar las métricas.


- Configurar alertas por correo electrónico que le notifiquen cuándo una métrica específica alcanza un valor determinado.


Si quiere descargar las métricas para su almacenamiento de larga duración o para analizarlas localmente, deberá usar una herramienta o escribir código para leer las tablas. Debe descargar las métricas por minuto para el análisis. Las tablas no aparecen si lista todas las tablas en su cuenta de almacenamiento, pero puede acceder a ellas directamente por su nombre. Muchas herramientas de exploración de almacenamiento de terceros tienen en cuenta estas tablas y permiten que las vea directamente (vea la publicación en el blog [Información general sobre los exploradores del almacenamiento de Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) para ver una lista de herramientas disponibles).

### Métricas por hora
- $MetricsHourPrimaryTransactionsBlob
- $MetricsHourPrimaryTransactionsTable
- $MetricsHourPrimaryTransactionsQueue

### Métricas por minuto
- $MetricsMinutePrimaryTransactionsBlob
- $MetricsMinutePrimaryTransactionsTable
- $MetricsMinutePrimaryTransactionsQueue

### Capacity
- $MetricsCapacityBlob

Puede encontrar detalles completos de los esquemas para estas tablas en [Esquema de las tablas de métricas del análisis de almacenamiento](https://msdn.microsoft.com/library/azure/hh343264.aspx). Las filas de ejemplo que tiene a continuación muestran solo un subconjunto de columnas disponibles, pero ilustran algunas características importantes acerca de la manera en que las Métricas de almacenamiento guardan estas métricas:

| PartitionKey | RowKey | Timestamp | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Disponibilidad | AverageE2ELatency | AverageServerLatency | PercentSuccess |
|---------------|:------------------:|-----------------------------:|---------------|-----------------------|--------------|-------------|--------------|-------------------|----------------------|----------------|
| 20140522T1100 | user;All | 2014-05-22T11:01:16.7650250Z | 7 | 7 | 4003 | 46801 | 100 | 104\.4286 | 6\.857143 | 100 |
| 20140522T1100 | user;QueryEntities | 2014-05-22T11:01:16.7640250Z | 5 | 5 | 2694 | 45951 | 100 | 143\.8 | 7\.8 | 100 |
| 20140522T1100 | user;QueryEntity | 2014-05-22T11:01:16.7650250Z | 1 | 1 | 538 | 633 | 100 | 3 | 3 | 100 |
| 20140522T1100 | user;UpdateEntity | 2014-05-22T11:01:16.7650250Z | 1 | 1 | 771 | 217 | 100 | 9 | 6 | 100 |

En este ejemplo de datos de métricas por minuto, la clave de partición usa el tiempo en la resolución de minutos. La clave de fila identifica el tipo de información que se almacena en la fila y se compone de dos fragmentos de información, el tipo de acceso y el tipo de solicitud:

- El tipo de acceso es user o system, donde user hace referencia a todas las solicitudes de usuario al servicio de almacenamiento y system hace referencia a las solicitudes realizadas por el Análisis de almacenamiento.

- El tipo de solicitud es all en cuyo caso es una línea de resumen, o identifica una API específica como QueryEntity o UpdateEntity.


Los datos de ejemplo anteriores muestran todos los registros para un único minuto (que empieza el a las 11:00 AM), por lo que el número de solicitudes QueryEntities más el número de solicitudes QueryEntity más el número de solicitudes UpdateEntity suman siete, que es el total que se muestra en la fila user:All. De forma similar, puede derivar la latencia promedio de un extremo a otro 104,4286 en la fila user:All con el cálculo ((143,8 * 5) + 3 + 9)/7.

Debe considerar la posibilidad de configurar alertas en la página “Supervisar” del Portal de Azure clásico, para que las Métricas de almacenamiento puedan notificarle automáticamente cualquier cambio importante en el comportamiento de sus servicios de almacenamiento. Si usa una herramienta de exploración de almacenamiento para descargar estos datos de métricas en un formato delimitado, puede usar Microsoft Excel para analizar los datos. Consulte la publicación sobre los[Exploradores de almacenamiento de Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) que encontrará en el blog para conseguir una lista de herramientas de exploradores de almacenamiento disponibles.



## Acceso a los datos de métricas mediante programación

El listado siguiente muestra código C# de ejemplo que accede a las métricas por minuto para un intervalo de minutos y muestra los resultados en una ventana de la consola. Usa Azure Storage Library versión 4 que incluye la clase CloudAnalyticsClient que simplifica el acceso a las tablas de métricas en almacenamiento.

    private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
    {
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    
    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
    Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
    var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
    var t = analyticsClient.GetMinuteMetricsTable(service);
    var opContext = new OperationContext();
    var query =
    from entity in metricsQuery
    // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
    // because they are calculated fields in the MetricsEntity class.
    // The PartitionKey identifies the DataTime of the metrics.
    where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0 
    select entity;
    
    // Filter on "user" transactions after fetching the metrics from Table Storage.
    // (StartsWith is not supported using LINQ with Azure table storage)
    var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
    var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
    Console.WriteLine(resultString);
    }
    }
    
    private static string MetricsString(MetricsEntity entity, OperationContext opContext)
    {
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
    string.Format("Time: {0}, ", entity.Time) +
    string.Format("AccessType: {0}, ", entity.AccessType) +
    string.Format("TransactionType: {0}, ", entity.TransactionType) +
    string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
    
    }




## ¿Qué se le facturará cuando habilite las métricas de almacenamiento?

Las solicitudes por escrito para crear entidades de tabla para métricas se cobran con las tarifas estándar aplicables a todas las operaciones de Almacenamiento de Azure.

La lectura y eliminación de solicitudes por un cliente para datos de métricas también se facturan con las tarifas estándar. Si ha configurado una directiva de retención de datos, no se le cobrará cuando Almacenamiento de Azure elimine datos de métricas antiguos. Sin embargo, si elimina datos de análisis, se cobrará a su cuenta las operaciones de eliminación.

La capacidad de las tablas de métricas también es facturable: puede usar las opciones siguientes para calcular la cantidad de capacidad usada para almacenar datos de métricas:

- Si cada hora un servicio utiliza todas las API en todos los servicios, se almacenan aproximadamente 148 KB de datos cada hora en las tablas de transacciones de métricas si se ha habilitado tanto el resumen de nivel servicio como de nivel de API.

- Si cada hora un servicio utiliza todas las API en todos los servicios, se almacenan aproximadamente 12 KB de datos cada hora en las tablas de transacciones de métricas si ha habilitado solo el resumen de nivel de servicio.

- Se agregan dos filas cada día a la tabla de capacidad para blobs (siempre que el usuario haya optado por los registros): esto implica que todos los días el tamaño de la tabla aumenta en unos 300 bytes, aproximadamente.

## Pasos siguientes:
[Habilitar el registro del Análisis de almacenamiento y acceder a los datos del mismo](https://msdn.microsoft.com/library/dn782840.aspx)
 

<!---HONumber=AcomDC_0218_2016-->