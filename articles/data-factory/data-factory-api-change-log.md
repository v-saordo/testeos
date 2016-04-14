<properties 
	pageTitle="Factoría de datos - Registro de cambios de la API de .NET | Microsoft Azure" 
	description="Describe los cambios importantes, las adiciones de características y las correcciones de errores, entre otros, en una versión específica de la API de .NET para Factoría de datos de Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/30/2016" 
	ms.author="spelluru"/>

# Factoría de datos de Azure: registro de cambios de la API de .NET 
En este artículo se proporciona información sobre los cambios realizados en el SDK de Factoría de datos de Azure en una versión específica. El paquete NuGet más reciente para la Factoría de datos de Azure se encuentra [aquí](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories).

## Versión 4.4.0
Fecha de lanzamiento: 28.01.2016

### Incorporación de características

- El siguiente tipo de servicio vinculado se ha agregado como orígenes de datos y receptores para actividades de copia:
	- [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Para obtener información conceptual y ejemplos, consulte [Servicio vinculado de SAS de Almacenamiento de Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service). 

## Versión 4.3.0
Fecha de lanzamiento: 25.11.2015

### Incorporación de características

- Los siguientes tipos de servicio vinculado se ha agregado como orígenes de datos para actividades de copia:
	- [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Para obtener información conceptual y ejemplos, consulte [Movimiento de datos desde HDFS local mediante Factoría de datos de Azure](data-factory-hdfs-connector.md). 
	- [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Para obtener información conceptual y ejemplos, consulte [Movimiento de datos desde almacenes de datos ODBC mediante Factoría de datos de Azure](data-factory-odbc-connector.md). 

## Versión 4.2.0
Fecha de lanzamiento: 10-11-2015

### Incorporación de características

- Se ha agregado el nuevo tipo de actividad siguiente: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Para obtener más información acerca de la actividad, consulte [Actualización de modelos de Aprendizaje automático de Azure mediante la actividad de recursos de actualización](data-factory-azure-ml-batch-execution-activity.md#updating-azure-ml-models-using-the-update-resource-activity).
- Se ha agregado una nueva propiedad opcional [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) a la [clase AzureMLLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx). 
- Se han agregado las propiedades [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) y [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) a la clase [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx). 
- Permita la configuración de los tiempos de espera para las llamadas de cliente al servicio de Factoría de datos. 


## Versión 4.1.0
Fecha de lanzamiento: 28-10-2015

### Incorporación de características
* Se han agregado los siguientes tipos de servicios vinculados: 
    * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
    * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Se han agregado los siguientes tipos de actividades: 
    * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Se han agregado los siguientes tipos de conjuntos de datos: 
    * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Se han agregado los siguientes tipos de origen y el receptor para la actividad de copia:
    * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
    * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)


## Versión 4.0.1
Fecha de lanzamiento: 13/10/2015

### Cambios drásticos
Las clases siguientes se han cambiado de nombre. Los nombres nuevos eran los nombres originales de las clases antes de la versión 4.0.0.
 
Nombre en 4.0.0 | Nombre en 4.0.1
:------------ | :------------ 
AzureSqlDataWarehouseDataset | [AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx)
AzureSqlDataset | [AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx)
AzureDataset | [AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx)
OracleDataset | [OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx)
RelationalDataset | [RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx)
SqlServerDataset | [SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx)


## Versión 4.0.0
Fecha de lanzamiento: 02/10/2015

### Cambios drásticos



- Las siguientes clases o interfaces se han cambiado de nombre.

| Nombre anterior | Nombre nuevo |
| :-------- | :-------- |
| ITableOperations | [IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |  
| Tabla | [Dataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) | 
| TableProperties | [DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) | 
| TableTypeProprerties | [DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters | [DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) | 
| TableCreateOrUpdateResponse | [DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) | 
| TableGetResponse | [DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) | 
| TableListResponse | [DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters | [DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) | 
    
- La **versión de API** para esta versión es: **2015-10-01**.

- Los métodos **List** devuelven ahora resultados paginados. Si la respuesta contiene una propiedad **NextLink** que no está vacía, la aplicación cliente debe seguir capturando la página siguiente hasta que se devuelvan todas las páginas. Este es un ejemplo:

		PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
	    var pipelines = new List<Pipeline>(response.Pipelines);
	
	    string nextLink = response.NextLink;
	    while (string.IsNullOrEmpty(response.NextLink))
	    {
	        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
	        pipelines.AddRange(nextResponse.Pipelines);
	
	        nextLink = nextResponse.NextLink;
	    }
	
- La API de canalización de **List** devuelve solamente el resumen de una canalización, en lugar de todos los detalles. Por ejemplo, en un resumen de canalización, las actividades solo contienen el nombre y el tipo.

### Incorporación de características
- La clase [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) admite dos nuevas propiedades, **SliceIdentifierColumnName** y **SqlWriterCleanupScript**, para permitir la copia idempotente en el Almacenamiento de datos SQL de Azure. Consulte el artículo de [Almacenamiento de datos SQL de Azure](data-factory-azure-sql-data-warehouse-connector.md), concretamente las secciones [Mecanismo 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) y [Mecanismo 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2), para obtener más detalles sobre estas propiedades.

- Actualmente, se admite la ejecución de procedimientos almacenados en orígenes de Base de datos SQL de Azure y Almacenamiento de datos SQL de Azure como parte de la actividad de copia. Para ello, las clases [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) y [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) tienen las siguientes propiedades: **SqlReaderStoredProcedureName** y **StoredProcedureParameters**. Consulte los artículos [Base de datos SQL de Azure](data-factory-azure-sql-connector.md#sqlsource) y [Almacenamiento de datos SQL de Azure](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) en Azure.com para obtener detalles sobre estas propiedades.

<!---HONumber=AcomDC_0204_2016-->