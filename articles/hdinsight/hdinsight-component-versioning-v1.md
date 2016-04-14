<properties
	pageTitle="Novedades en las versiones de clúster de Hadoop de HDInsight | Microsoft Azure"
	description="HDInsight es compatible con varias versiones de clúster de Hadoop que se pueden implementar. Vea las versiones de distribución de Hadoop y HortonWorks Data Platform (HDP) que se admiten."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="jgao"/>


#Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight

##Versiones de HDInsight y componentes de Hadoop
HDInsight de Azure es compatible con varias versiones de clústeres de Hadoop que se pueden implementar en cualquier momento. Cada versión aprovisiona una versión específica de la distribución HortonWorks Data Platform (HDP) y un conjunto de componentes que están incluidos en esa distribución. En la tabla siguiente se detallan las versiones de componente asociadas a las versiones de clúster de HDInsight. Tenga en cuenta que la versión de clúster predeterminada que usa HDInsight de Azure actualmente es la 3.1 y, desde 07/11/2014, basada en HDP 2.1.8.


<table border="1">
<tr><th>Componente</th><th>HDInsight versión 3.2</th><th>HDInsight versión 3.1 (predeterminada)</th><th>HDInsight versión 3.0</th><th>HDInsight versión 2.1</th></tr>
<tr><td>Hortonworks Data Platform</td><td>2.2</td><td>2.1.7</td><td>2.0</td><td>1.3</td></tr>
<tr><td>Apache Hadoop y YARN</td><td>2.6.0</td><td>2.4.0</td><td>2.2.0</td><td>1.2.0</td></tr>
<tr><td>Apache Tez</td><td>0.5.2</td><td>0.4.0</td><td></td><td></td></tr>
<tr><td>Apache Pig</td><td>0.14.0</td><td>0.12.1</td><td>0.12.0</td><td>0.11.0</td></tr>
<tr><td>Apache Hive y HCatalog</td><td>0.14.0</td><td>0.13.1</td><td>0.12.0</td><td>0.11.0</td></tr>
<tr><td>Apache HBase </td><td>0.98.4</td><td>0.98.0</td><td></td><td></td></tr>
<tr><td>Apache Sqoop</td><td>1.4.5</td><td>1.4.4</td><td>1.4.4</td><td>1.4.3</td></tr>
<tr><td>Apache Oozie</td><td>4.1.0</td><td>4.0.0</td><td>4.0.0</td><td>3.3.2</td></tr>
<tr><td>Apache Zookeeper</td><td>3.4.6</td><td>3.4.5</td><td>3.4.5</td><td></td></tr>
<tr><td>Apache Storm</td><td>0.9.3</td><td>0.9.1</td><td></td><td></td></tr>
<tr><td>Apache Mahout</td><td>0.9.0</td><td>0.9.0</td><td></td><td></td></tr>
<tr><td>Apache Phoenix</td><td>4.2.0</td><td>4.0.0.2.1.7.0-2162</td><td></td><td></td></tr>
<tr><td>Spark de Apache</td><td>1.3.1</td><td></td><td></td><td></td></tr>
</table>


**Obtención de información sobre las versiones actuales de componentes**

Las versiones de los componentes asociados a las versiones de clúster de HDInsight pueden cambiar en futuras actualizaciones de HDInsight. Una forma de determinar los componentes disponibles y comprobar las versiones que se están usando para un clúster es usar la API REST de Ambari. El comando **GetComponentInformation** se puede usar para recuperar información acerca de un componente del servicio. Para obtener más información, consulte la [documentación de Ambari][ambari-docs]. Otra forma de obtener esta información es iniciar sesión en un clúster mediante el Escritorio remoto y examinar directamente el contenido del directorio "C:\\apps\\dist".


**Notas de la versión**

Consulte [Notas de la versión de HDInsight](hdinsight-release-notes.md) para conocer otras notas de las últimas versiones de HDInsight.

### Selección de una versión al aprovisionar un clúster de HDInsight

Al crear un clúster a través de los cmdlets de Windows PowerShell para HDInsight o de .NET SDK de HDInsight, puede elegir la versión del clúster de Hadoop para HDInsight mediante el parámetro "Version".

Si usa la opción **Creación rápida**, obtendrá la versión 3.1 de HDInsight que crea el clúster de Hadoop de forma predeterminada. Si usa la opción **Creación personalizada** del Portal de Azure clásico, puede elegir la versión del clúster que implementará desde el menú desplegable **Versión de HDInsight** en la página **Detalles del clúster**.

##Características destacadas
Algunas de las características más destacadas de la plataforma de HDInsight incluyen:

- **Spark**: Apache Spark es un marco de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones de análisis de Big Data. Las capacidades de cálculo en memoria de Spark lo convierten en una buena opción para algoritmos iterativos en los cálculos de gráficos y aprendizaje automático.

	Spark también se puede usar para llevar a cabo el procesamiento de datos convencional basado en disco. Spark mejora el marco de MapReduce tradicional evitando escrituras en disco en las etapas intermedias. Además, Spark es compatible con el Sistema de archivos distribuido de Hadoop (HDFS) y el almacenamiento de blobs de Azure, por lo que se pueden procesar los datos existentes fácilmente a través de Spark.

	Spark también se puede agregar mediante una acción de script. La acción de script agrega Spark 1.2.0 a un clúster de HDInsight 3.2 o Spark 1.0.2 a un clúster de HDInsight 3.1. Para obtener más información, consulte [Instalación y uso de Spark en clústeres Hadoop de HDInsight](hdinsight-hadoop-spark-install.md).


- **Storm** : Storm en HDInsight de Azure ya está disponible con carácter general y ofrece una manera rápida y fácil de implementar análisis en tiempo real con solo unos cuantos clics y en pocos minutos. Apache Storm en HDInsight de Azure es un proyecto de código abierto en el ecosistema de Apache Hadoop que proporciona acceso a una plataforma de análisis capaz de procesar millones de eventos de forma confiable. Ahora los usuarios de Hadoop pueden obtener información a medida que ocurren los eventos, además de información de eventos anteriores. Microsoft también proporciona integración con Visual Studio, lo que facilita la interacción de los desarrolladores con Storm. Ahora puede desarrollar, implementar y depurar topologías de Storm desde Visual Studio.

- **HDInsight en Linux**: HDInsight de Azure ofrece la opción de aprovisionar clústeres Hadoop que se ejecutan en máquinas virtuales (VM) de Linux (Ubuntu). Puede usar esta opción si conoce Linux o Unix, si realiza una migración desde una solución de Hadoop basada en Linux existente o si desea una integración simple con los componentes del ecosistema de Hadoop creados para Linux. Puede aprovisionar un clúster de HDInsight en Linux desde un equipo cliente que ejecuta Windows o Linux mediante el Portal de Azure clásico, la CLI de Azure o SDK para .NET de HDInsight (solo Windows).

- **Tamaños de máquinas virtuales adicionales**: los clústeres de HDInsight ahora están disponibles en varios tamaños y tipos de máquinas virtuales. Los clústeres de HDInsight ahora pueden usar los tamaños A2 a A7 creados para fines generales; nodos de la serie D que cuentan con unidades de estado sólido (SSD) y procesadores un 60 por ciento más rápidos; y tamaños A8 y A9 con compatibilidad InfiniBand para una red rápida. Los clientes de Apache HBase en HDInsight de Azure se pueden beneficiar de las configuraciones de memoria más grandes de la serie D para aumentar el rendimiento. Los clientes de Apache Storm en HDInsight de Azure también pueden beneficiarse de la memoria adicional para cargar grandes conjuntos de datos de referencia, así como de CPU más rápidas, para obtener un mayor rendimiento.

- **Escalado de clústeres**: el escalado de clústeres permite cambiar el número de nodos de un clúster de HDInsight en ejecución sin tener que eliminarlo o volverlo a crear. En la actualidad, solo la consulta Hadoop y Apache Storm disponen de esta capacidad, pero pronto se les unirá Apache HBase.

- **Generar script de acción**: esta característica de personalización de clústeres permite la modificación de clústeres de Hadoop de formas arbitrarias mediante scripts personalizados. Con esta nueva característica, los usuarios pueden experimentar con los proyectos disponibles en el ecosistema Hadoop de Apache e implementarlos en clústeres de HDInsight de Azure. Esta característica de personalización está disponible en todos los tipos de clústeres de HDInsight, como Hadoop, HBase y Storm.

- **HBase**: HBase es una base de datos NoSQL de baja latencia que permite el procesamiento de transacciones en línea de Big Data. HBase se ofrece como un clúster administrado integrado en el entorno de Azure. Los clústeres están configurados para almacenar datos directamente en el almacenamiento de blobs de Azure, que proporciona una latencia baja y una mayor elasticidad de las opciones de rendimiento o coste. Esto permite que los clientes creen sitios web interactivos que trabajen con grandes conjuntos de datos, creen servicios que almacenen datos del sensor y telemetría desde millones de extremos y analicen estos datos con trabajos de Hadoop.

- **Apache Phoenix**: Apache Phoenix es una capa de consulta de Lenguaje de consulta estructurado (SQL) a través de HBase. Admite un subconjunto limitado de especificación de lenguaje de consulta SQL que incluye compatibilidad con índices secundarios. Se entrega como un controlador de Conectividad de base de datos Java (JDBC) incrustado en el cliente que dirige a consultas de latencia baja a sobre de datos de HBase. Apache Phoenix toma su consulta SQL, la compila en una serie de análisis de HBase y llamadas de coprocesadores y produce conjuntos de resultados JDBC regulares. Apache Phoenix es una capa de base de datos relacional sobre HBase. Se entrega como un controlador JDBC incrustado en el cliente que dirige a consultas de latencia baja sobre datos de HBase. Apache Phoenix toma su consulta SQL, la compila en una serie de análisis de HBase y orquesta la ejecución de esos análisis para producir conjuntos de resultados JDBC regulares.

- **Panel de clúster**: una nueva aplicación web que se implementa en el clúster de HDInsight. Úsela para ejecutar consultas de Hive, comprobar registros de trabajos y examinar el almacenamiento de blobs de Azure. La dirección URL utilizada para obtener acceso a la aplicación web es <*NombreDeClúster*>.azurehdinsight.net.

- **Microsoft Avro Library**: esta biblioteca implementa el sistema de serialización de datos de Apache Avro para el entorno de Microsoft.NET. Apache Avro proporciona un formato compacto de intercambio de datos binarios para la serialización. Usa la notación de objetos JavaScript (JSON) para definir un esquema independiente del lenguaje que garantiza la interoperabilidad de lenguajes. Los datos serializados en un lenguaje se pueden leer en otro. Actualmente, se admiten C, C++, C#, Java, PHP, Python y Ruby. El formato de serialización de Apache Avro se utiliza ampliamente en HDInsight de Azure para representar estructuras de datos complejas en un trabajo MapReduce de Hadoop.

- **YARN**: marco de administración de aplicaciones nuevo, de uso general y distribuido que ha reemplazado al marco Apache Hadoop MapReduce para el procesamiento de datos en los clústeres de Hadoop. De hecho, sirve como sistema operativo de Hadoop y consigue que Hadoop pase de ser una plataforma de datos para el procesamiento por lotes y para un único usuario a una plataforma multiuso que permite el procesamiento por lotes, interactivo, en línea y por secuencia. Este nuevo marco de administración mejora la escalabilidad y el uso de clústeres de acuerdo con criterios como las garantías de capacidad, la legitimidad y los contratos de nivel de servicio (SLA).

- **Tez (solo HDInsight 3.1 y versiones posteriores)**: marco con fines generales y personalizable que crea tareas de procesamiento de datos simplificadas en cargas de trabajo a pequeña y gran escala en Hadoop. Proporciona la capacidad de ejecutar un gráfico acíclico dirigido (DAG) de tareas para un solo trabajo, de forma que los proyectos del ecosistema Apache Hadoop, como Apache Hive y Apache Pig, puedan cumplir los requisitos de tiempos de respuesta para la interacción con humanos y de rendimiento extremo a escala de petabytes. Tenga en cuenta que Hive 0.13 permite ejecutar consultas de Hive en Tez, en lugar de MapReduce.

- **Alta disponibilidad (HA)**: se ha agregado un segundo nodo principal a los clústeres de Hadoop desarrollados por HDInsight para aumentar la disponibilidad del servicio. Las implementaciones estándar de clústeres de Hadoop normalmente tienen un solo nodo principal. HDInsight elimina este único punto de error al agregar un segundo nodo principal. El cambio a una nueva configuración de clúster de alta disponibilidad no modifica el precio del clúster, a menos que los clientes aprovisionen los clústeres con un nodo principal extragrande en lugar del nodo de tamaño grande predeterminado.

- **Rendimiento de Hive**: mejoras exponenciales de los tiempos de respuesta de las consultas de Hive (hasta 40 veces más rápidas) y de la compresión de los datos (hasta el 80 %) mediante el formato **Optimized Row Columnar** (ORC).

- **Pig, Sqoop, Oozie, Ambari**: actualizaciones de versión de componentes para el clúster de HDInsight versión 3.0 (HDP 2.0/Hadoop 2.2) que proporciona paridad con el clúster de HDInsight versión 2.1 (HDP 1.3/Hadoop 1.2). Consulte la tabla de versiones a continuación para obtener información específica.

- **Mahout**: esta biblioteca de algoritmos de aprendizaje automático escalable está preinstalada en los clústeres de Hadoop de HDInsight 3.1 (y versiones posteriores). Por tanto, puede ejecutar trabajos de Mahout sin necesidad de configuración adicional del clúster.

- **Compatibilidad con Red virtual**: los clústeres de HDInsight se pueden usar con Red virtual de Azure para permitir el aislamiento de recursos en la nube o escenarios híbridos que vinculen recursos en la nube con los de su centro de datos.


## Versiones compatibles
En la siguiente tabla se enumeran las versiones de HDInsight que está disponibles actualmente, las versiones correspondientes de la distribución Hortonworks Data Platform que usan y sus fechas de lanzamiento. Si se conocen, también se proporcionan también las fechas de expiración y desuso. Tenga en cuenta lo siguiente:

* Los clústeres de alta disponibilidad con dos nodos principales se implementan de forma predeterminada para los clústeres de HDInsight 2.1 y versiones posteriores. No están disponibles para los clústeres de HDInsight 1.6.
* Cuando expira el soporte técnico de una versión concreta, es posible que no esté disponible en el Portal de Azure clásico. En la tabla siguiente se indica qué versiones están disponibles en el Portal de Azure clásico. Las versiones de clúster seguirán estando disponibles mediante el parámetro `Version` en el comando [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) y en el SDK para .NET hasta la fecha de desuso.

<table border="1"> <tr><th>Versión de HDInsight</th><th>Versión de HDP</a><th>Alta disponibilidad</th></th><th>Fecha de lanzamiento</th><th>Disponible en el Portal de Azure clásico</th><th>Fecha de expiración de soporte técnico</th><th>Fecha de desuso</th></tr> <tr><td>HDI 3.2</td><td>HDP 2.2</td><td>Sí</td><td>18/02/2015</td><td>Sí</td><td></td><td></td></tr> <tr><td>HDI 3.1</td><td>HDP 2.1</td><td>Sí</td><td>24/06/2014</td><td>Sí</td><td></td><td></td></tr> <tr><td>HDI 3.0</td><td>HDP 2.0</td><td>Sí</td><td>11/02/2014</td><td>Sí</td><td>17/09/2014</td><td>30/06/2015</td></tr> <tr><td>HDI 2.1</td><td>HDP 1.3</td><td>Sí</td><td>28/10/2013</td><td>No</td><td>12/05/2014</td><td>31/05/2015</td></tr> <tr><td>HDI 1.6</td><td>HDP 1.1</td><td>No</td><td>28/10/2013</td><td>No</td><td>26/04/2014</td><td>31/05/2015</td></tr> </table><br>

**Implementación de clústeres no predeterminados**

Los clústeres de HDInsight 3.1 se crean de forma predeterminada en Hadoop 2.4, por lo que los usuarios deben usar la opción **Creación personalizada** en el portal para especificar otras versiones de clúster de HDInsight si es necesario crearlas.

### El contrato de nivel de servicio para las versiones de clúster de HDInsight

El SLA se define en términos de "plazo de soporte técnico". Un plazo de soporte técnico se refiere al período durante el cual la versión del clúster de HDInsight puede recibir soporte técnico por parte del Soporte técnico y el servicio al cliente de Microsoft. Un clúster de HDInsight está fuera del plazo de soporte técnico si su versión cuenta con una **fecha de expiración de soporte técnico** anterior a la fecha actual. En la tabla anterior se puede consultar una lista de las versiones de clúster de HDInsight con soporte técnico. La fecha de expiración de una versión determinada (X) de HDInsight (una vez que hay disponible una versión más reciente X+1) se calcula como la fecha más tardía de las dos siguientes:

- Fórmula 1: agregue 180 días a la fecha en la que se lanzó la versión X del clúster de HDInsight.
- Fórmula 2: agregue 90 días a la fecha en la que la versión X+1 (versión subsiguiente después de X) del clúster de HDInsight se puso a su disposición en el Portal de Azure clásico.

La **fecha de desuso** es la fecha tras la cual no se puede crear la versión del clúster en HDInsight.

> [AZURE.NOTE] Los clústeres de HDInsight 2.1 y 3.0 se ejecutan en el SO invitado de Azure [Familia 4](../cloud-services-guestos-update-matrix.md) que usa la versión de 64 bits de Windows Server 2012 R2 y admite .NET Framework 4.0, 4.5 y 4.5.1.

## Notas de la versión de HortonWorks asociadas con las versiones de HDInsight##

* La versión 3.2 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.2][hdp-2-2].

	* Notas de la versión de componentes específicos de Apache: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).


* La versión 3.1 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Este es el clúster de Hadoop **predeterminado** que se crea al usar el portal de HDInsight de Azure después del 7/11/2014. Los clústeres de HDInsight 3.1 creados antes del 7/11/2014 se basaban en [Hortonworks Data Platform 2.1.1][hdp-2-1-1].

* La versión 3.0 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.0][hdp-2-0-8].

* La versión 2.1 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 1.3][hdp-1-3-0].

* La versión 1.6 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 1.1][hdp-1-1-0].


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

<!---HONumber=AcomDC_0211_2016-->