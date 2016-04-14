<properties
	pageTitle="¿Qué es Hadoop en la nube? Introducción a HDInsight | Microsoft Azure"
	description="¿Qué es Hadoop en la nube y cómo se administra en HDInsight? Una introducción a los componentes de Hadoop y análisis de macrodatos"
	keywords="análisis de macrodatos,introducción a hadoop,qué es hadoop, hadoop en la nube"
	services="hdinsight"
	documentationCenter=""
	authors="cjgronlund"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/27/2016"
   ms.author="cgronlun"/>


# ¿Qué es Hadoop en la nube? Una introducción a los componentes de Hadoop en HDInsight para análisis de macrodatos

Obtenga una introducción a Hadoop, su ecosistema y Big Data en HDInsight de Azure: Hadoop en HDInsight y conozca cuáles son los componentes de Hadoop, su terminología común y los escenarios para el análisis de Big Data. Además, obtenga información acerca de los tutoriales, la documentación y los recursos para usar la nube de Hadoop en HDInsight.

## ¿Qué es Hadoop en la nube en HDInsight?

Azure HDInsight implementa y aprovisiona clústeres de Apache Hadoop administrados en la nube con el fin de proporcionar un marco de software diseñado para procesar, analizar y generar informes en relación con macrodatos con alta confiabilidad y disponibilidad. HDInsight utiliza la distribución **Hortonworks Data Platform (HDP)** de Hadoop. Hadoop a menudo hace referencia a todo el ecosistema de Hadoop de componentes, que incluye Apache HBase, Apache Spark y Apache Storm, así como otras tecnologías bajo el paraguas de Hadoop. Para obtener información detallada, consulte [Información general sobre el ecosistema de Hadoop en HDInsight](#overview).


## ¿Qué son grandes volúmenes de datos?
Los macrodatos se refieren a datos que se recopilan en volúmenes de escala continua, a velocidades cada vez mayores, y para una variedad cada vez más amplia de formatos no estructurados y contextos semánticos variables.

Big Data describe a cualquier conjunto de gran tamaño de información digital, desde el texto de una fuente de Twitter hasta información del sensor de un equipamiento industrial e información sobre la actividad de exploración y compras en un catálogo en línea de un cliente. Los grandes volúmenes de datos pueden ser históricos (es decir, datos almacenados) o en tiempo real (es decir, transferidos directamente desde el origen).

Para que los macrodatos ofrezcan conocimientos o información de inteligencia procesables, no solo debe recopilar datos relevantes y formular las preguntas correctas, sino que los datos también deben ser accesibles y poderse limpiar y analizar para presentarlos de forma útil. Y ahí es donde el análisis de Big Data de Hadoop en HDInsight puede resultar útil.


## <a name="overview"></a>Información general sobre el ecosistema de Hadoop en HDInsight

HDInsight es una implementación en la nube en Microsoft Azure del componente tecnológico de Apache Hadoop en rápida expansión que constituye la solución imprescindible para el análisis de macrodatos. Incluye implementaciones de Apache Spark, HBase, Storm, Pig, Hive, Sqoop, Oozie, Ambari, etc. HDInsight se integra también con herramientas de Business Intelligence (BI) como, por ejemplo, Power BI, Excel, SQL Server Analysis Services y SQL Server Reporting Services.

### Clústeres en Linux

HDInsight de Azure implementa y aprovisiona clústeres de Hadoop en la nube en **Linux**. Vea la tabla siguiente para detalles.

Categoría | Hadoop en Linux
---------| -------------------
**SO del clúster** | Ubuntu 12.04 Long Term Support (LTS)
**Tipo de clúster** | Hadoop, Spark, HBase, Storm
**Implementación** | Portal de Azure, CLI de Azure, Azure PowerShell
**Interfaz de usuario del clúster** | Ambari
**Acceso remoto** | Shell seguro (SSH), API de REST, ODBC, JDBC



### Hadoop, HBase, Spark, Storm y clústeres personalizados

HDInsight ofrece configuraciones de clúster para Apache Hadoop, Spark, HBase o Storm. O, puede [personalizar clústeres con acciones de script](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop** (la carga de trabajo "Query"): proporciona almacenamiento de datos confiable con [HDFS](#HDFS) y un modelo de programación de [MapReduce](#mapreduce) simple para procesar y analizar datos en paralelo.

* **<a target="_blank" href="http://spark.apache.org/">Apache Spark</a>**: marco de procesamiento paralelo que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones de análisis de microdatos, trabajos de Spark para SQL, datos de streaming y aprendizaje automático. Vea [Información general: ¿Qué es Apache Spark en HDInsight?](hdinsight-apache-spark-overview.md)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>** (la carga de trabajo "NoSQL"): una base de datos NoSQL compilada en Hadoop que proporciona acceso aleatorio y una sólida coherencia para grandes cantidades de datos no estructurados y semiestructurados (potencialmente, miles de millones de filas multiplicadas por millones de columnas). Consulte [Información general de HBase en HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>** (la carga de trabajo "Stream"): un sistema distribuido de cálculo en tiempo real para el procesamiento rápido de grandes secuencias de datos. Storm se ofrece como clúster administrado en HDInsight. Consulte [Análisis de datos de sensor en tiempo real con Storm y Hadoop](hdinsight-storm-sensor-data-analysis.md).

#### Scripts de personalización de ejemplo

Las acciones de script son scripts que se ejecutan durante el aprovisionamiento del clúster y que se pueden usar para instalar componentes adicionales en el clúster. En el caso de los clústeres basados en Linux, se trata de scripts de Bash.

Los siguientes scripts de ejemplo los ofrece el equipo de HDInsight:

* [Hue](hdinsight-hadoop-hue-linux.md): conjunto de aplicaciones web que se usan para interactuar con un clúster. Solo clústeres Linux.

* [Giraph](hdinsight-hadoop-giraph-install-linux.md): procesamiento de gráficos para modelar las relaciones entre las cosas o las personas.

* [R](hdinsight-hadoop-r-scripts-linux.md): es un entorno y lenguaje de código abierto para computación estadística que se usa en el aprendizaje automático.

* [Solr](hdinsight-hadoop-solr-install-linux.md): plataforma de búsqueda de escala empresarial que permite la búsqueda de texto completo en los datos.

Para obtener información sobre el desarrollo de sus propias acciones de script, consulte [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

## ¿Cuáles son las utilidades y los componentes de Hadoop?

Se incluyen los siguientes componentes y utilidades en clústeres de HDInsight.

* **[Ambari](#ambari)**: aprovisionamiento, administración, supervisión y utilidades de clústeres.

* **[Avro](#avro)** (biblioteca de Microsoft .NET para Avro): serialización de datos para el entorno de Microsoft .NET.

* **[Hive y HCatalog](#hive)**: consultas tipo SQL (lenguaje de consulta estructurado) y una capa de administración de almacenamiento y de tablas.

* **[Mahout](#mahout)**: aprendizaje automático.

* **[MapReduce](#mapreduce)**: marco heredado para el procesamiento distribuido y la administración de recursos de Hadoop. Consulte [YARN](#yarn), el marco de recursos de última generación.

* **[Oozie](#oozie)**: administración de flujo de trabajo.

* **[Phoenix](#phoenix)**: capa de base de datos relacional sobre HBase.

* **[Pig](#pig)**: scripts más sencillos para transformaciones de MapReduce.

* **[Sqoop](#sqoop)**: importación y exportación de datos.

* **[Tez](#tez)**: permite a los procesos con muchos datos ejecutarse de forma eficaz a escala.

* **[YARN](#yarn)**: parte de la biblioteca principal de Hadoop y el marco de software de MapReduce de última generación.

* **[ZooKeeper](#zookeeper)**: coordinación de procesos en sistemas distribuidos.

> [AZURE.NOTE] Para obtener información sobre los componentes específicos y sus versiones, consulte [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight][component-versioning].

### <a name="ambari"></a>Ambari

Apache Ambari sirve para el aprovisionamiento, la administración y la supervisión de clústeres de Hadoop de Apache. Incluye una recopilación intuitiva de herramientas de operador y un conjunto sólido de API que ocultan la complejidad de Hadoop y simplifican la operación de clústeres. Los clústeres de HDInsight basados en Linux proporcionan la interfaz de usuario web de Ambari y la API de REST de Ambari, mientras que los clústeres basados en Windows proporcionan un subconjunto de la API de REST. Las Vistas de Ambari en clústeres de HDInsight admiten funcionalidades de IU de complementos.

Consulte [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md) (solo Linux), [Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari](hdinsight-monitor-use-ambari-api.md) y la <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Referencia de API de Apache Ambari</a>.

### <a name="avro"></a>Avro (biblioteca de Microsoft .NET para Avro)

La biblioteca de Microsoft .NET para Avro implementa el formato compacto de intercambio de datos binarios de Apache Avro para la serialización del entorno de Microsoft .NET. Utiliza <a target="_blank" href="http://www.json.org/">notación de objetos JavaScript (JSON)</a> para definir un esquema independiente del lenguaje que garantiza la interoperabilidad de lenguajes, lo que se traduce en que los serializados en un lenguaje se pueden leer en otro. Encontrará información detallada sobre el formato en las <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Especificaciones de Apache Avro</a>. El formato de los archivos de Avro admite el modelo de programación distribuida de MapReduce. Los archivos son "divisibles" en el sentido de que se puede buscar cualquier punto en un archivo y comenzar a leer desde un bloque concreto. Para descubrir cómo, consulte [Serialización de datos con la biblioteca de Microsoft .NET para Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HDFS

El sistema de archivos distribuido de Hadoop (HDFS) es un sistema de archivos distribuido que, junto con MapReduce y YARN, conforma el núcleo del ecosistema Hadoop. HDFS es el sistema de archivos estándar para los clústeres de Hadoop en HDInsight.

### <a name="hive"></a>Hive y HCatalog

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> es el software de almacenamiento de datos basado en Hadoop que le permite consultar y administrar grandes conjuntos de datos en almacenamiento distribuido con un lenguaje de tipo SQL denominado HiveQL. Hive, al igual que Pig, es una abstracción situada en un nivel superior a MapReduce y, al ejecutarse, traduce las consultas a una serie de trabajos de MapReduce. Hive se encuentra conceptualmente más cerca de un sistema de administración de bases de datos relacionales que Pig y, por lo tanto, es más adecuado para su uso con datos más estructurados. Para los datos no estructurados, Pig es una mejor opción. Consulte [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> es una capa de administración de almacenamiento y tablas para Hadoop que presenta a los usuarios una vista relacional de los datos. En HCatalog, se pueden leer y escribir archivos en cualquier formato para el que se pueda escribir un SerDe de Hive (serializador-deserializador).

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> es una biblioteca escalable de algoritmos de aprendizaje automático que se ejecuta en Hadoop. Mediante principios de estadísticas, las aplicaciones de aprendizaje automático enseñan a los sistemas a aprender de los datos y a usar los resultados obtenidos en el pasado para determinar el comportamiento en el futuro. Consulte [Generación de recomendaciones de películas mediante Mahout en Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
MapReduce es el marco de software heredado para permitir a Hadoop la escritura de aplicaciones con el fin de procesar en lote conjuntos de macrodatos en paralelo. Un trabajo de MapReduce divide conjuntos de datos de gran tamaño y organiza los datos en pares clave-valor para su procesamiento.

[YARN](#yarn) es el marco de aplicación y administrador de recursos de última generación, al que se conoce como MapReduce 2.0. Los trabajos de MapReduce se ejecutarán en YARN.

Para obtener más información sobre MapReduce, consulte <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> en el sitio Wiki de Hadoop.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> es un sistema de coordinación de flujos de trabajo que administra trabajos de Hadoop. Se integra con la pila de Hadoop y es compatible con los trabajos de Hadoop para MapReduce, Pig, Hive y Sqoop. También puede usarse para programar trabajos específicos de un sistema, como scripts de shell o programas Java. Consulte [Uso del coordinador de Oozie de tiempo con Hadoop en HDInsight](hdinsight-use-oozie-coordinator-time.md).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> es una capa de base de datos relacional sobre HBase. Phoenix incluye un controlador JDBC que permite a los usuarios consultar y administrar tablas SQL directamente. Phoenix traduce consultas y otras instrucciones en llamadas nativas de la API NoSQL nativas en lugar de usar MapReduce, lo que permite aplicaciones más rápidas además de los almacenes NoSQL. Consulte [Usar Apache Phoenix y SQuirreL con clústeres de HBase](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> es una plataforma de alto nivel que permite realizar transformaciones complejas de MapReduce en conjunto de datos de gran tamaño con un sencillo lenguaje de scripting denominado Pig Latin. Pig traduce los scripts de Pig Latin para que se ejecuten en Hadoop. Se pueden crear funciones definidas por el usuario (UDF) para ampliar Pig Latin. Consulte [Uso de Pig con Hadoop para analizar un archivo de registro de Apache](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> es una herramienta que transfiere grandes cantidades de datos entre Hadoop y bases de datos relacionales, como SQL, u otros almacenes de datos estructurados, de la manera más eficiente posible. Consulte [Uso de Sqoop con Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> es un marco de aplicación basado en Hadoop YARN que ejecuta gráficos complejos y acíclicos general de procesamiento de datos generales. Es un sucesor más flexible y eficaz al marco de MapReduce que permite procesos intensivos de datos, como Hive, para ejecutar de manera más eficaz a escala. Consulte ["Usar Apache Tez para un rendimiento mejorado" en Usar Hive y HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>YARN
Apache YARN es la siguiente generación de MapReduce (MapReduce 2.0, o MRv2) y es compatible con escenarios de procesamiento de datos más allá del procesamiento por lotes de MapReduce con mayor escalabilidad y procesamiento en tiempo real. YARN proporciona un marco de aplicación distribuida y administración de recursos. Los trabajos de MapReduce se ejecutarán en YARN.

Para obtener información sobre YARN, consulte <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">MapReduce de última generación de Apache Hadoop (YARN)</a>.


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordina procesos en grandes sistemas distribuidos por medio de un espacio de nombres jerárquico compartido de registros de datos (znodes). Znodes contiene pequeñas cantidades de metadatos necesarios para la coordinación de procesos: estado, ubicación, configuración, entre otros.

## Lenguajes de programación en HDInsight

Los clústeres de HDInsight clusters—los clústeres de Hadoop, HBase, Storm y Spark— admiten varios lenguajes de programación, pero algunos de ellos no están instalados de manera predeterminada. En el caso de las bibliotecas, los módulos o los paquetes no instalados de manera predeterminada, instale el componente con una acción de script. Consulte [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

### Compatibilidad con lenguajes de programación predeterminados

De forma predeterminada, los clústeres de HDInsight admiten lo siguiente:

* Java

* Python

Se pueden instalar lenguajes adicionales mediante acciones de script: [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

### Lenguajes de la máquina virtual de Java (JVM)

En una máquina virtual de Java se pueden utilizar muchos lenguajes distintos de Java; sin embargo, la ejecución de algunos de estos lenguajes puede requerir la instalación de componentes adicionales en el clúster.

Estos lenguajes basados en JVM son compatibles con clústeres de HDInsight:

* Clojure

* Jython (Python para Java)

* Scala

### Lenguajes específicos de Hadoop

Los clústeres de HDInsight proporcionan compatibilidad para los siguientes lenguajes específicos del ecosistema de Hadoop:

* Pig Latin para trabajos de Pig

* HiveQL para trabajos de Hive y SparkSQL


## <a name="advantage"></a>Ventajas de Hadoop en la nube

Como parte del ecosistema de la nube de Azure, Hadoop en HDInsight ofrece varias ventajas, entre otras:

* El aprovisionamiento automático de clústeres de Hadoop. La creación de los clústeres de HDInsight es mucho más simple que la configuración manual de los clústeres de Hadoop. Para obtener información detallada, consulte [Aprovisionamiento de clústeres de Hadoop en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

* Componentes de Hadoop de última generación. Para obtener información más detallada, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight][component-versioning].

* Alta disponibilidad y confiabilidad de los clústeres. Si desea obtener información detallada, consulte [Disponibilidad y confiabilidad de clústeres de Hadoop en HDInsight](hdinsight-high-availability-linux.md).

* Almacenamiento de datos eficiente y económico con el almacenamiento de blobs de Azure, opción compatible con Hadoop. Si desea obtener información detallada, consulte [Uso del almacenamiento de blobs de Azure con Hadoop en HDInsight](hdinsight-hadoop-use-blob-storage.md).

* Integración con otros servicios de Azure, como [aplicaciones web](../documentation/services/app-service/web/) y [Base de datos SQL](../documentation/services/sql-database/).

* Coste inicial bajo. Inicie una [prueba gratuita](/pricing/free-trial/) o consulte [Detalles de precios de HDInsight](/pricing/details/hdinsight/).


Para obtener más información sobre las ventajas de Hadoop en HDInsight, consulte la [página de características de Azure para HDInsight][marketing-page].



## <a id="resources"></a>Recursos para obtener más información sobre el análisis de Big Data, Hadoop y HDInsight

Básese en esta introducción a Hadoop en la nube y en el análisis de macrodatos con los recursos que aparecen a continuación.


### Documentación de Hadoop para HDInsight

* [Documentación de HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): la página de documentación de HDInsight de Azure con vínculos a artículos, vídeos y otros recursos.

* [Introducción a HDInsight en Linux](hdinsight-hadoop-linux-tutorial-get-started.md): un tutorial de inicio rápido sobre el aprovisionamiento de clústeres de Hadoop para HDInsight en Linux y la ejecución de consultas de ejemplo de Hive.

* [Introducción a Storm basado en Linux en HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md): tutorial de inicio rápido para aprovisionar un clúster Storm en HDInsight y ejecutar topologías Storm de ejemplo.

* [Aprovisionamiento de HDInsight en Linux](hdinsight-hadoop-provision-linux-clusters.md): aprenda a aprovisionar un clúster Hadoop de HDInsight en Linux a través del Portal de Azure, la CLI de Azure o Azure PowerShell.

* [Trabajo con HDInsight en Linux](hdinsight-hadoop-linux-information.md): obtenga algunas sugerencias rápidas sobre cómo trabajar con clústeres de Hadoop en Linux aprovisionados en Azure.

* [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md): aprenda a supervisar y administrar su clúster de Hadoop en HDInsight basado en Linux con Ambari Web o la API de REST de Ambari.


### Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: obtenga más información acerca de la biblioteca de software Apache Hadoop, un marco que permite efectuar el procesamiento distribuido de grandes conjuntos de datos en clústeres de equipos.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: obtenga más información acerca de la arquitectura y el diseño del Sistema de archivos distribuidos Hadoop, el sistema de almacenamiento principal de las aplicaciones de Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">Tutorial de MapReduce</a>: obtenga más información acerca del marco de programación para escribir aplicaciones de Hadoop con el fin de procesar rápidamente enormes cantidades de datos, en paralelo, en grandes clústeres de nodos de proceso.

### Base de datos SQL en Azure

* [Base de datos SQL de Azure](/documentation/services/sql-database/): documentación, tutoriales y vídeos para la Base de datos SQL.

* [Base de datos SQL en el Portal de Azure](sql-database-manage-portal.md): una herramienta de administración ligera y fácil de usar para administrar el modelo de ejecución Base de datos SQL en la nube.

* [Adventure Works para Base de datos SQL](http://msftdbprodsamples.codeplex.com/releases/view/37304): página de descarga de una base de datos de ejemplo de Base de datos SQL.

### Microsoft Business Intelligence (para HDInsight en Windows)

Las herramientas de Business Intelligence (BI) habituales, como Excel, PowerPivot, SQL Server Analysis Services y SQL Server Reporting Services, recuperan, analizan y generan informes de datos integrados en HDInsight con el complemento Power Query o Microsoft Hive ODBC Driver.

Estas herramientas de BI pueden ayudarle en el análisis de Big Data:

* [Conexión de Excel a Hadoop con Power Query](hdinsight-connect-excel-power-query.md): aprenda a conectar Excel a una cuenta de almacenamiento de Azure que almacene los datos asociados a su clúster de HDInsight mediante Microsoft Power Query para Excel.

* [Conexión de Excel a Hadoop con Microsoft Hive ODBC Driver](hdinsight-connect-excel-hive-ODBC-driver.md): aprenda a importar datos desde HDInsight con Microsoft Hive ODBC Driver.

* [Plataforma en la nube de Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): obtenga información sobre Power BI para Office 365, descargue la evaluación de SQL Server y configure SharePoint Server 2013 y SQL Server BI.

* <a target="_blank" href="http://msdn.microsoft.com/library/hh231701.aspx">Más información sobre SQL Server Analysis Services</a>.

* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">Más información acerca de SQL Server Reporting Services</a>.


### Pruebe soluciones de Hadoop para el análisis de Big Data (para HDInsight en Windows)

Utilice el análisis de Big Data sobre los datos de su organización para obtener información sobre su negocio. A continuación se muestran algunos ejemplos:

* [Análisis de datos de sensor de HVAC](hdinsight-hive-analyze-sensor-data.md): aprenda a analizar los datos del sensor mediante Hive con HDInsight (Hadoop) y, a continuación, visualice los datos en Microsoft Excel. En este ejemplo, usará Hive para procesar datos históricos producidos por sistemas HVAC para ver los sistemas que pueden mantener de forma confiable una temperatura establecida.

* [Uso de Hive con HDInsight para analizar registros de sitios web](hdinsight-hive-analyze-website-log.md): aprenda a usar HiveQL en HDInsight para analizar registros de sitios web con el objeto de obtener información sobre la frecuencia de las visitas diarias desde sitios web externos, además de un resumen de los errores que experimentan los usuarios de los sitios web.

* [Análisis de los datos de sensor en tiempo real con Storm y HBase en HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md): aprenda a compilar una solución que utilice un clúster de Storm en HDInsight con el fin de procesar datos de sensor desde Centros de eventos de Azure y, a continuación, visualice los datos de sensor procesados como información prácticamente en tiempo real en un panel basado en web.


[marketing-page]: ../services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/

<!---HONumber=AcomDC_0302_2016-->