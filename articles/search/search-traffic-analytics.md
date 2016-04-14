<properties 
	pageTitle="Análisis de tráfico de búsqueda para Búsqueda de Azure | Microsoft Azure" 
	description="Habilite el análisis de tráfico de búsqueda para Búsqueda de Azure, un servicio de búsqueda hospedado en la nube en Microsoft Azure, para descubrir información acerca de los usuarios y los datos." 
	services="search" 
	documentationCenter="" 
	authors="bernitorres" 
	manager="pablocas" 
	editor=""
/>

<tags 
	ms.service="search" 
	ms.devlang="multiple" 
	ms.workload="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/26/2016" 
	ms.author="betorres"
/>


# Habilitación y uso de Análisis de tráfico de búsqueda

Análisis de tráfico de búsqueda es una característica de Búsqueda de Azure que permite tener visibilidad en el servicio de búsqueda y descubrir información acerca de los usuarios y su comportamiento. Cuando se habilita esta característica, los datos del servicio de búsqueda se copian en una cuenta de almacenamiento de su elección. Estos datos incluyen los registros del servicio de búsqueda y métricas operacionales agregadas. Una vez allí, puede procesar y manipular los datos de uso de cualquier manera.


## Habilitación de Análisis de tráfico de búsqueda

### 1\. Uso del portal
Abra el servicio de Búsqueda de Azure en el [Portal de Azure](http://portal.azure.com). En Configuración, encontrará la opción Análisis de tráfico de búsqueda.

![][1]

Seleccione esta opción y se abrirá una nueva hoja. Cambie el estado a **Encendido**, seleccione la cuenta de almacenamiento de Azure en la que se vayan a copiar los datos y elija los datos que desee copiar: registros, métricas o ambos. Se recomienda copiar los registros y métricas.

![][2]


> [AZURE.IMPORTANT] La cuenta de almacenamiento debe estar en la misma región y suscripción que el servicio de búsqueda.
> 
> Se aplican gastos estándares para esta cuenta de almacenamiento

### 2\. Uso de PowerShell

También puede habilitar esta característica ejecutando los siguientes cmdlets de PowerShell.

```PowerShell
Login-AzureRmAccount
Set-AzureRmDiagnosticSetting -ResourceId <SearchService ResourceId> StorageAccountId <StorageAccount ResourceId> -Enabled $true
```

-   **SearchService ResourceId**: ```
/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Search/searchServices/<searchServiceName>
```

 
-  **StorageAccount ResourceId**: puede encontrarlo en el portal en Configuración -> Propiedades -> ResourceId ```
New: /subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>
OR
Classic: /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ClassicStorage/storageAccounts/<storageAccountName>
```

----------

Una vez habilitada, los datos comenzarán a estar disponibles en la cuenta de almacenamiento en un período de 5 a 10 minutos. Encontrará 2 nuevos contenedores en Almacenamiento de blobs:

    insights-logs-operationlogs: search traffic logs
    insights-metrics-pt1m: aggregated metrics


## Descripción de los datos

Los datos se almacenan en blobs de Almacenamiento de Azure con formato JSON.

Habrá un blob, por hora, por contenedor.
  
Ruta de acceso de ejemplo: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

### Registros

Los blobs de registros contienen los registros de tráfico del servicio de búsqueda.

Cada blob tiene un objeto raíz llamado **registros** que contiene una matriz de objetos de registro

####Esquema de registro

Nombre |Tipo |Ejemplo |Notas 
------|-----|----|-----
Twitter en tiempo |datetime |"2015-12-07T00:00:43.6872559Z" |Marca de tiempo de la operación
resourceId |cadena |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Su ResourceId
operationName |cadena |"Query.Search" |El nombre de la operación
operationVersion |cadena |"2015-02-28"|La versión de la API usada
categoría |cadena |"OperationLogs" |constant 
resultType |cadena |"Success" |Valores posibles: Success o Failure 
resultSignature |int |200 |Código de resultado HTTP 
durationMS |int |50 |Duración de la operación en milisegundos 
propiedades |objeto |consulte a continuación |Objeto que contiene datos específicos de la operación

####Esquema de propiedades

|Nombre |Tipo |Ejemplo |Notas|
|------|-----|----|-----|
|Descripción|cadena |"GET /indexes('content')/docs" |Punto de conexión de la operación |
|Consultar |cadena |"?search=AzureSearch&$count=true&api-version=2015-02-28" |Los parámetros de consulta |
|Documentos |int |42 |Número de documentos procesados|
|IndexName |cadena |"testindex"|Nombre del índice asociado a la operación |

### Métricas

Los blobs de métricas contienen valores agregados para el servicio de búsqueda. Cada archivo tiene un objeto raíz llamado **registros** que contiene una matriz de objetos de métricas

Métricas disponibles:

- Latency

####Esquema de métricas

|Nombre |Tipo |Ejemplo |Notas|
|------|-----|----|-----|
|resourceId |cadena |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |el identificador de recurso |
|metricName |cadena |"Latency" |el nombre de la métrica |
|Twitter en tiempo|datetime |"2015-12-07T00:00:43.6872559Z" |la marca de tiempo de la operación |
|average |int |64|El valor de media de las muestras sin procesar en el intervalo de tiempo de la métrica |
|minimum |int |37 |El valor mínimo de las muestras sin procesar en el intervalo de tiempo de la métrica |
|maximum |int |78 |El valor máximo de las muestras sin procesar en el intervalo de tiempo de la métrica |
|total |int |258 |El valor total de las muestras sin procesar en el intervalo de tiempo de la métrica |
|count |int |4 |El número de muestras sin procesar usadas para generar la métrica |
|timegrain |cadena |"PT1M" |El intervalo de agregación de la métrica en ISO 8601|

## Análisis de datos

Los datos están en su propia cuenta de almacenamiento. Le animamos a explorar estos datos de la manera más adecuada para su caso.

Como punto de partida, se recomienda usar [Power BI](https://powerbi.microsoft.com) para explorar y visualizar los datos. Puede conectarse fácilmente a su cuenta de Almacenamiento de Azure y comenzar rápidamente a analizar los datos.

#### Power BI en línea

[Paquete de contenido de Power BI](https://app.powerbi.com/getdata/services/azure-search): cree un panel de Power BI y un conjunto de informes de Power BI que muestren los datos automáticamente y proporcionen información visual sobre el servicio de búsqueda. Vea la [página de ayuda sobre el paquete de contenido](https://powerbi.microsoft.com/es-ES/documentation/powerbi-content-pack-azure-search/).

![][3]

#### Power BI Desktop

[Power BI Desktop](https://powerbi.microsoft.com/es-ES/desktop): explore los datos y cree sus propias visualizaciones de los datos. Se proporciona una consulta de inicio a continuación para ayudarle.

1. Abrir un nuevo informe de Power BI Desktop
2. Seleccione Obtener datos -> Más...

	![][4]

3. Seleccione Almacenamiento de blobs de Microsoft Azure y Connect

	![][5]

4. Especifique el nombre y la clave de cuenta de la cuenta de almacenamiento
5. Seleccione "insight-logs-operationlogs" y "insights-metrics-pt1m" y luego haga clic en Editar.
6. Se abrirá el Editor de consultas , asegúrese de que "insight-logs-operationlogs" está seleccionado a la izquierda. Ahora abra el Editor avanzado seleccionando Ver -> Editor avanzado

	![][6]

7. Mantenga las 2 primeros líneas y reemplace el resto por la consulta siguiente:

	>     #"insights-logs-operationlogs" = Source{[Name="insights-logs-operationlogs"]}[Data],
	>     #"Sorted Rows" = Table.Sort(#"insights-logs-operationlogs",{{"Date modified", Order.Descending}}),
	>     #"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
	>     #"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
	>     #"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
	>     #"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
	>     #"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
	>     #"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}, {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}),
	>     #"Expanded properties" = Table.ExpandRecordColumn(#"Expanded records1", "properties", {"Description", "Query", "IndexName", "Documents"}, {"Description", "Query", "IndexName", "Documents"}),
	>     #"Renamed Columns" = Table.RenameColumns(#"Expanded properties",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"operationName", "OperationName"}, {"operationVersion", "OperationVersion"}, {"category", "Category"}, {"resultType", "ResultType"}, {"resultSignature", "ResultSignature"}, {"durationMS", "Duration"}}),
	>     #"Added Custom2" = Table.AddColumn(#"Renamed Columns", "QueryParameters", each Uri.Parts("http://tmp" & [Query])),
	>     #"Expanded QueryParameters" = Table.ExpandRecordColumn(#"Added Custom2", "QueryParameters", {"Query"}, {"Query.1"}),
	>     #"Expanded Query.1" = Table.ExpandRecordColumn(#"Expanded QueryParameters", "Query.1", {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}, {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}),
	>     #"Removed Columns1" = Table.RemoveColumns(#"Expanded Query.1",{"OperationVersion"}),
	>     #"Changed Type" = Table.TransformColumnTypes(#"Removed Columns1",{{"Datetime", type datetimezone}, {"ResourceId", type text}, {"OperationName", type text}, {"Category", type text}, {"ResultType", type text}, {"ResultSignature", type text}, {"Duration", Int64.Type}, {"Description", type text}, {"Query", type text}, {"IndexName", type text}, {"Documents", Int64.Type}, {"search", type text}, {"$skip", Int64.Type}, {"$top", Int64.Type}, {"$count", type logical}, {"api-version", type text}, {"searchMode", type text}, {"$filter", type text}}),
	>     #"Inserted Date" = Table.AddColumn(#"Changed Type", "Date", each DateTime.Date([Datetime]), type date),
	>     #"Duplicated Column" = Table.DuplicateColumn(#"Inserted Date", "ResourceId", "Copy of ResourceId"),
	>     #"Split Column by Delimiter" = Table.SplitColumn(#"Duplicated Column","Copy of ResourceId",Splitter.SplitTextByEachDelimiter({"/"}, null, true),{"Copy of ResourceId.1", "Copy of ResourceId.2"}),
	>     #"Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Copy of ResourceId.1", type text}, {"Copy of ResourceId.2", type text}}),
	>     #"Removed Columns2" = Table.RemoveColumns(#"Changed Type1",{"Copy of ResourceId.1"}),
	>     #"Renamed Columns1" = Table.RenameColumns(#"Removed Columns2",{{"Copy of ResourceId.2", "ServiceName"}}),
	>     #"Lowercased Text" = Table.TransformColumns(#"Renamed Columns1",{{"ServiceName", Text.Lower}}),
	>     #"Added Custom" = Table.AddColumn(#"Lowercased Text", "DaysFromToday", each Duration.Days(DateTimeZone.UtcNow() - [Datetime])),
	>     #"Changed Type2" = Table.TransformColumnTypes(#"Added Custom",{{"DaysFromToday", Int64.Type}})
	>     in
	>     #"Changed Type2"

8. Haga clic en Listo.

9. Seleccione ahora "insights-metrics-pt1m" en la consulta que se encuentra menos a la izquierda y abra el editor avanzado de nuevo. Mantenga las 2 primeros líneas y reemplace el resto por la consulta siguiente:

	>     #"insights-metrics-pt1m1" = Source{[Name="insights-metrics-pt1m"]}[Data],
	>     #"Sorted Rows" = Table.Sort(#"insights-metrics-pt1m1",{{"Date modified", Order.Descending}}),
	>     #"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
    	#"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
	>     #"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
	>     #"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
	>     #"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
	>     #"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}, {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}),
	>     #"Filtered Rows" = Table.SelectRows(#"Expanded records1", each ([metricName] = "Latency")),
	>     #"Removed Columns1" = Table.RemoveColumns(#"Filtered Rows",{"timeGrain"}),
	>     #"Renamed Columns" = Table.RenameColumns(#"Removed Columns1",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"metricName", "MetricName"}, {"average", "Average"}, {"minimum", "Minimum"}, {"maximum", "Maximum"}, {"total", "Total"}, {"count", "Count"}}),
	>     #"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns",{{"ResourceId", type text}, {"MetricName", type text}, {"Datetime", type datetimezone}, {"Average", type number}, {"Minimum", Int64.Type}, {"Maximum", Int64.Type}, {"Total", Int64.Type}, {"Count", Int64.Type}}),
	>         Rounding = Table.TransformColumns(#"Changed Type",{{"Average", each Number.Round(_, 2)}}),
	>     #"Changed Type1" = Table.TransformColumnTypes(Rounding,{{"Average", type number}}),
	>     #"Inserted Date" = Table.AddColumn(#"Changed Type1", "Date", each DateTime.Date([Datetime]), type date)
	>     in
    	#"Inserted Date"

10. Haga clic en Listo y seleccione Cerrar y aplicar en la pestaña Inicio.

11. Los datos de los últimos 30 días están ahora listos para su consumo. Continué y cree algunas [visualizaciones](https://powerbi.microsoft.com/es-ES/documentation/powerbi-desktop-report-view/).

## Pasos siguientes

Obtenga más información sobre la sintaxis de búsqueda y los parámetros de consulta. Vea [Búsqueda de documentos (API de REST de Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para información detallada.

Obtenga más información sobre cómo crear informes increíbles. Consulte [Introducción a Power BI Desktop](https://powerbi.microsoft.com/es-ES/documentation/powerbi-desktop-getting-started/) para obtener más información

<!--Image references-->

[1]: ./media/search-traffic-analytics/SettingsBlade.png
[2]: ./media/search-traffic-analytics/DiagnosticsBlade.png
[3]: ./media/search-traffic-analytics/Dashboard.png
[4]: ./media/search-traffic-analytics/GetData.png
[5]: ./media/search-traffic-analytics/BlobStorage.png
[6]: ./media/search-traffic-analytics/QueryEditor.png

<!---HONumber=AcomDC_0204_2016-->