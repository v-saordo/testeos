<properties
 pageTitle="Procesamiento de datos del sensor de vehículo con Apache Storm en HDInsight | Microsoft Azure"
 description="Aprenda a procesar datos del sensor de vehículo de Centros de eventos con Apache Storm en HDInsight. Agregue datos de modelo de DocumentDB y almacene el resultado en el almacenamiento."
 services="hdinsight,documentdb,notification-hubs"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="01/08/2016"
ms.author="larryfr"/>

#Procese datos del sensor de vehículo de Centros de eventos de Azure con Apache Storm en HDInsight

Aprenda a procesar datos del sensor de vehículo de Centros de eventos de Azure con Apache Storm en HDInsight. Este ejemplo lee datos de sensor de los Centros de eventos de Azure, enriquece los datos haciendo referencia a datos almacenados en Azure DocumentDB y, finalmente, almacena los datos en Almacenamiento de Azure mediante el sistema de archivos de Hadoop (HDFS).

![Diagrama de arquitectura de Internet de las cosas (IoT) y HDInsight](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

##Información general

Agregar sensores a vehículos permite predecir problemas de equipos basándose en tendencias de datos históricos, así como realizar mejoras en versiones futuras según el análisis de patrones de uso. Aunque el procesamiento por lotes de MapReduce tradicional se puede usar para este análisis, debe ser capaz de cargar los datos de todos los vehículos en Hadoop de forma rápida y eficaz antes de que se lleve a cabo dicho procesamiento. Además, puede que desee realizar análisis para rutas de error crítico (temperatura del motor, frenos, etc.) en tiempo real.

Los Centros de eventos de Azure están diseñados para manejar el gran volumen de datos generado por los sensores y Apache Storm en HDInsight se puede usar para cargar y procesar los datos antes de almacenarlos en HDFS (respaldado por Almacenamiento de Azure) para procesamiento adicional de MapReduce.

##Solución

Los datos de telemetría de temperatura del motor, temperatura ambiente y velocidad del vehículo se graban mediante sensores, a continuación se envían a los Centros de eventos junto con el número de identificación del vehículo (VIN) y una marca de tiempo. Desde allí, una topología de Storm ejecutándose en un clúster de Apache Storm en HDInsight lee los datos, los procesa y los almacena en HDFS.

Durante el procesamiento, el VIN se usa para recuperar información sobre el modelo desde Azure DocumentDB. Esto se agrega a la secuencia de datos antes de almacenarse.

Los componentes usados en la topología de Storm son:

* **EventHubSpout**: lee los datos de Centros de eventos de Azure.

* **TypeConversionBolt**: convierte la cadena JSON de los Centros de eventos en una tupla que contiene los valores de datos individuales de temperatura del motor, temperatura ambiente, velocidad, VIN y marca de tiempo.

* **DataReferencBolt**: busca el modelo del vehículo desde DocumentDB mediante el VIN.

* **WasbStoreBolt**: almacena los datos en HDFS (Almacenamiento de Azure).

A continuación se muestra un diagrama de esta solución:

![topología de storm](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [AZURE.NOTE]Se trata de un diagrama simplificado y cada componente de la solución puede tener varias instancias. Por ejemplo, las distintas instancias de cada componente de la topología se distribuyen por los nodos del clúster de Storm en HDInsight.

##Implementación

Una solución completa y automatizada para este escenario está disponible como parte del repositorio <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">HDInsight-Storm-Examples</a> en GitHub. Para utilizar este ejemplo, siga los pasos de [IoTExample README.MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md).

## Pasos siguientes

Para más topologías de ejemplo de Storm, vea [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=AcomDC_0114_2016-->