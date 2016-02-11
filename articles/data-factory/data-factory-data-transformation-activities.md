<properties 
	pageTitle="Actividades de transformación de datos | Microsoft Azure" 
	description="Obtenga información sobre cómo puede usar el servicio de la Factoría de datos de Azure para transformar y analizar los datos." 
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
	ms.date="01/19/2016" 
	ms.author="spelluru"/>

# Transformar y analizar mediante la Factoría de datos de Azure

## Información general
Las actividades de transformación en la Factoría de datos de Azure transforman y procesan sus datos sin procesar en predicciones y perspectivas. La actividad de transformación se ejecuta en un entorno informático, como clúster de HDInsight de Azure o un Lote de Azure. La Factoría de datos de Azure admite las siguientes actividades de transformación que se pueden agregar a[canalizaciones](data-factory-create-pipelines.md) ya sea individualmente o encadenadas a otra actividad.


Actividad de transformación | Entorno de procesos 
:----------------------- | :--------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Pig](data-factory-pig-activity.md) | HDInsight [Hadoop] 
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]
[Streaming de Hadoop](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop] 
[Actividades de Aprendizaje automático: ejecución de lotes y recurso de actualización](data-factory-azure-ml-batch-execution-activity.md) | MV de Azure 
[Procedimiento almacenado](data-factory-stored-proc-activity.md) | SQL Azure, Almacenamiento de datos SQL de Azure o SQL Server |
[U-SQL de análisis con Data Lake](data-factory-usql-activity.md) | Análisis con Azure Data Lake 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] o Lote de Azure
    

Deberá crear un servicio vinculado para el entorno de proceso y después usar el servicio vinculado al definir una actividad de transformación. La Factoría de datos admite dos tipos de entornos de proceso.

1. **A petición**: en este caso, el entorno informático es completamente administrado por la Factoría de datos. El servicio de la Factoría de datos lo crea automáticamente antes de que se envíe un trabajo para procesar los datos y de que se elimine cuando finalice el trabajo. Los usuarios pueden configurar y controlar la configuración granular del entorno de proceso a petición para la ejecución del trabajo, la administración del clúster y las acciones de arranque. 
2. **Traiga el suyo propio**: en este caso, puede registrar su propio entorno informático (por ejemplo, clúster de HDInsight) como servicio vinculado en la Factoría de datos. El usuario administra el entorno de procesos y el servicio Factoría de datos lo usa para ejecutar las actividades. 

Vea el artículo [Servicios vinculados de procesos](data-factory-compute-linked-services.md) para obtener información sobre los servicios vinculados de proceso compatibles con la Factoría de datos.

<!---HONumber=AcomDC_0121_2016-->