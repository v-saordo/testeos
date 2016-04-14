<properties
 pageTitle="Ejemplo de topologías de Apache Storm en HDInsight | Microsoft Azure"
 description="Una lista de topologías de ejemplo de Storm creada y probada con Apache Storm en HDInsight, incluidas las topologías básicas de C# y Java, y trabajando con los Centros de eventos."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="01/15/2016"
 ms.author="larryfr"/>

# Topologías y componentes de ejemplo de Storm para Apache Storm en HDInsight

La siguiente es una lista de ejemplos creada y mantenida por Microsoft para su uso con Apache Storm en HDInsight. Estos ejemplos tratan diversos temas, desde la creación de topologías básicas en C# y Java para trabajar con servicios de Azure como Centros de eventos, DocumentDB, Power BI, Base de datos SQL, HBase en HDInsight y Almacenamiento de Azure. Algunos ejemplos también muestran cómo trabajar con tecnologías que no son de Azure, o incluso que no son de Microsoft, como SignalR y Socket.IO

| Descripción | Muestra | Lenguaje/Marco de trabajo |
|:--------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|:---------------------------|
| [Escritura en el Almacén de Azure Data Lake desde Apache Storm](hdinsight-storm-write-data-lake-store.md) | Escritura en el Almacén de Azure Data Lake | Java |
| [Origen de spout y bolt de Centro de eventos](https://github.com/apache/storm/tree/master/external/storm-eventhubs) | Origen de spout y bolt de Centro de eventos | Java |
| [Desarrollo de topologías basadas en Java para Apache Storm en HDInsight][5797064f] | Maven | Java |
| [Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio][16fce2d1] | Herramientas de HDInsight para Visual Studio | C#, Java |
| [Creación de varios flujos de datos en una topología de Storm en C#][ec5a4064] | Varios flujos | C# |
| [Determinación de los temas más destacados de Twitter con Storm en HDInsight][3c86c7c8] | Trident | Java, Trident |
| [Procesamiento de eventos desde Centros de eventos de Azure con Storm en HDInsight (C#)][844d1d81] | Centros de eventos | C# y Java |
| [Procesamiento de eventos desde Centros de eventos de Azure con Storm en HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md) | Centros de eventos | Java |
| [Uso de Power BI para visualizar datos desde la topología de Storm][94d15238] | Power BI | C# |
| [Análisis de los datos de sensor con Storm y HBase en HDInsight][ab894747] | Centros de eventos, HBase, Socket.IO, panel Web | C#, Java, JavaScript, HTML |
| [Procesamiento de los datos de sensor del vehículo desde Centros de eventos usando Storm en HDInsight][246ee964] | Centros de eventos, DocumentDb, blob de almacenamiento de Azure (WASB) | C#, Java |
| [Extracción, transformación y carga (ETL) desde Centros de eventos de Azure en HBase usando Storm en HDInsight][b4b68194] | Centros de eventos, HBase | C# |
| [Proyecto de topología de Storm en C# para plantillas para trabajar con servicios de Azure desde Storm en HDInsight][ce0c02a2] | Centros de eventos, DocumentDb, Base de datos SQL, HBase, SignalR | C#, Java |
| [Pruebas comparativas de escalabilidad para leer desde Centros de eventos de Azure usando Storm en HDInsight][d6c540e3] | Rendimiento de mensajes, Centros de eventos, Base de datos SQL | C#, Java |
| [Poner en correlación eventos con Storm y HBase en HDInsight](hdinsight-storm-correlation-topology.md) | HBase | C# |
| [Usar Python con Storm en HDInsight](hdinsight-storm-develop-python-topology.md) | Componentes de Python con topologías de Storm basadas en Java y Clojure | Python |

## Pasos siguientes

* [Introducción a Apache Storm en HDInsight][2b8c3488]

* [Implementación y administración de topologías de Storm con Storm en HDInsight][6eb0d3b8]

  [2b8c3488]: hdinsight-apache-storm-tutorial-get-started-linux.md "Aprenda a crear un clúster de Storm en HDInsight y a utilizar el panel de Storm para implementar topologías de ejemplo."
  [6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Aprenda a implementar y administrar topologías mediante el panel de Storm basado en web y la interfaz de usuario de Storm o las Herramientas de HDInsight para Visual Studio."
  [16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "Aprenda a crear topologías de Storm en C# mediante las Herramientas de HDInsight para Visual Studio."
  [5797064f]: hdinsight-storm-develop-java-topology.md "Aprenda a crear topologías de Storm en Java mediante Maven creando una topología de recuento de palabras básica."
  [94d15238]: hdinsight-storm-power-bi-topology.md "Muestra cómo escribir datos en Power BI desde una topología de C# y, a continuación, crear un gráfico y un panel a partir de los datos."
  [ec5a4064]: https://github.com/Blackmist/csharp-storm-example "Muestra una topología básica de Storm que realiza un recuento de palabras, implementada en C#. También muestra cómo crear varios flujos de datos dentro de una topología en C#."
  [844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "Aprenda a leer y escribir datos de los Centros de eventos de Azure con Storm en HDInsight."
  [ab894747]: hdinsight-storm-sensor-data-analysis.md "Aprenda a usar Apache Storm en HDInsight para procesar datos de sensor de los Centros de eventos de Azure, a visualizarlos mediante D3.js y (opcionalmente) a almacenarlos en HBase."
  [3c86c7c8]: hdinsight-storm-twitter-trending.md "Aprenda a usar Trident para crear una topología de Storm que determine los temas más destacados (basándose en hashtags) en Twitter."
  [246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "Aprenda a usar una topología Storm para leer los mensajes de los Centros de eventos de Azure, leer documentos de Azure DocumentDB para hacer referencia a datos y guardar datos en Almacenamiento de Azure."
  [d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Varias topologías para mostrar el rendimiento cuando se leen los Centros de eventos de Azure y se realiza el almacenamiento en Base de datos SQL mediante Apache Storm en HDInsight."
  [b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Aprenda a leer datos de los Centros de eventos de Azure, a agregar y transformar los datos y, a continuación, a almacenarlos en HBase en HDInsight."
  [ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Este proyecto contiene plantillas para spouts, bolts y topologías para interactuar con distintos servicios de Azure como Centros de eventos, DocumentDB y Base de datos SQL."
 

<!---HONumber=AcomDC_0218_2016-->