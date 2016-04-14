<properties
	pageTitle="Introducción a Apache Storm en HDInsight | Microsoft Azure"
	description="Obtenga una introducción a Apache Storm y aprenda cómo puede usar Storm en HDInsight para crear soluciones de análisis de datos en tiempo real en la nube."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/08/2016"
   ms.author="larryfr"/>

#Introducción a Apache Storm en HDInsight: análisis en tiempo real de Hadoop

Apache Storm en HDInsight le permite crear soluciones de análisis en tiempo real distribuidas en el entorno de Azure mediante [Apache Hadoop](http://hadoop.apache.org).

##¿Qué es Apache Storm?

Apache Storm es un sistema de cálculo de código abierto, distribuido y con tolerancia a errores que le permite procesar datos en tiempo real con Hadoop. Las soluciones de Storm pueden proporcionar también procesamiento de datos garantizado, con la posibilidad de reproducir los datos que no se han procesado correctamente la primera vez.

##¿Por qué usar Apache Storm en HDInsight?

Apache Storm en HDInsight es un clúster administrado integrado en el entorno de Azure. Ofrece las siguientes ventajas principales:

* Actúa como un servicio administrado con un SLA de 99,9 % de tiempo de actividad.

* Use el lenguaje que prefiera, ya que ofrece compatibilidad con los componentes de Storm escritos en **Java**, **C#** y **Python**.

	* Admite una variedad de lenguajes de programación: lea datos con Java y procéselos con C#.
	
		> [AZURE.NOTE] Solo se admiten topologías de C# en clústeres de HDInsight basados en Windows.

	* Use la interfaz de Java **Trident** para crear topologías de Storm que admitan el procesamiento de mensajes "exactamente una vez", la persistencia del almacén de datos "transaccional" y un conjunto de operaciones comunes de análisis de flujos.

* Incluye características integradas de escalado vertical y reducción vertical (escale un clúster de HDInsight sin que afecte a las topologías de Storm en ejecución).

* Integración con otros servicios de Azure, incluidos Centro de eventos, Red virtual de Azure , Base de datos SQL, almacenamiento de blobs y DocumentDB.

	* Combine las capacidades de varios clústeres de HDInsight mediante la Red virtual de Azure (cree canalizaciones analíticas que usen clústeres de Hadoop, HDInsight o HBase).

Para obtener una lista de empresas que usan Apache Storm con sus soluciones de análisis en tiempo real, consulte [Compañías que usan Apache Storm](https://storm.apache.org/documentation/Powered-By.html).

Para ver una introducción al uso de Storm, consulte [Introducción a Storm en HDInsight][gettingstarted].

###Facilidad de aprovisionamiento

Puede aprovisionar un nuevo clúster de Storm en HDInsight en minutos. Especifique el nombre del clúster, su tamaño, la cuenta del administrador y la cuenta de almacenamiento. Azure creará el clúster, incluyendo las topologías de ejemplo y un panel de administración web.

> [AZURE.NOTE] También puede aprovisionar clústeres Storm mediante la [CLI de Azure](../xplat-cli-install.md) o [Azure PowerShell](../powershell-install-configure.md).

En menos de 15 minutos tras el envío de la solicitud, dispondrá de un nuevo clúster de Storm en ejecución y listo para su primera canalización de análisis en tiempo real.

###Facilidad de uso

__En clústeres Storm basados en Linux en HDInsight__, puede conectarse al clúster mediante SSH y usar el comando `storm` para iniciar y administrar topologías. Además, puede usar Ambari para supervisar el servicio Storm y la interfaz de usuario de Storm para supervisar y administrar topologías en ejecución.

Para obtener más información sobre cómo trabajar con clústeres Storm basados en Linux, vea [Introducción a Apache Storm en HDInsight basado en Linux](hdinsight-apache-storm-tutorial-get-started-linux.md).

__Para clústeres Storm basados en Windows en HDInsight__, las herramientas de HDInsight para Visual Studio permiten crear topologías en C# e híbridas en C# y Java, y luego enviarlas al clúster Storm en HDInsight.

![Creación de un proyecto de Storm](./media/hdinsight-storm-overview/createproject.png)

Las herramientas de HDInsight para Visual Studio también ofrecen una interfaz que permite supervisar y administrar topologías de Storm en un clúster.

![Administración de Storm](./media/hdinsight-storm-overview/stormview.png)

Para ver un ejemplo del uso de las herramientas de HDInsight para crear una aplicación de Storm, consulte [Desarrollo de topologías de Storm en C# con las herramientas de HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

Para obtener más información sobre las herramientas de HDInsight para Visual Studio, consulte [Introducción al uso de las herramientas de HDInsight para Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

Cada clúster de Storm en HDInsight también ofrece un panel de Storm basado en web que le permite enviar, supervisar y administrar topologías de Storm que se ejecuten en el clúster.

![Panel de Storm](./media/hdinsight-storm-overview/dashboard.png)

Para obtener más información sobre el uso del panel Storm, consulte [Implementación y administración de topologías de Apache Storm en HDInsight](hdinsight-storm-deploy-monitor-topology.md).

Storm en HDInsight también proporciona una integración sencilla con los Centros de eventos de Azure a través del **Spout de Centro de eventos**. Está disponible en cada clúster de storm en **%STORM\_HOME%\\examples\\eventhubspout\\eventhubs-storm-spout-0.9-jar-with-dependencies.jar**. Para obtener ejemplos del uso de este spout en una topología de Storm, vea los siguientes documentos:

* [Desarrollo de una topología de C# que usa Centros de eventos de Azure](hdinsight-storm-develop-csharp-event-hub-topology.md)

* [Desarrollo de una topología de Java que usa Centros de eventos de Azure](hdinsight-storm-develop-java-event-hub-topology.md)

###Confiabilidad

Apache Storm siempre garantiza que cada mensaje entrante se procesará por completo, incluso cuando el análisis de datos esté repartido en cientos de nodos.

El **nodo Nimbus** ofrece una funcionalidad similar a la del JobTracker de Hadoop y asigna tareas a otros nodos del clúster mediante **Zookeeper**. Los nodos de Zookeeper ofrecen coordinación para el clúster y facilitan la comunicación entre Nimbus y el proceso **Supervisor** en los nodos de trabajo. Si un nodo de procesamiento deja de funcionar, se informa al nodo Nimbus y se asigna la tarea y los datos asociados a otro nodo.

La configuración predeterminada de Apache Storm es que solo tenga un nodo Nimbus. Storm en HDInsight ejecuta dos nodos Nimbus. Si se produce un error en el nodo principal, el clúster de HDInsight cambiará al nodo secundario mientras se recupera el nodo principal.

![Diagrama de nimbus, zookeeper y supervisor](./media/hdinsight-storm-overview/nimbus.png)

###Escala

Aunque puede especificar el número de nodos del clúster durante la creación, puede que desee aumentar o reducir el clúster para que coincida con la carga de trabajo. Todos los clústeres de HDInsight le permiten cambiar el número de nodos del clúster, incluso durante el procesamiento de datos.

> [AZURE.NOTE] Para aprovechar de los nodos nuevos que se agregan mediante el escalado, deberá reequilibrar las topologías iniciadas antes de que se aumentara el tamaño del clúster.

###Soporte técnico

Storm en HDInsight incluye soporte técnico completo ininterrumpido de nivel de empresa. Storm en HDInsight también tiene un SLA del 99,9 %. Eso significa que está garantizado que el clúster dispondrá de conectividad externa como mínimo el 99,9 % del tiempo.

##Casos de uso comunes del análisis en tiempo real

A continuación se indican algunos de los escenarios comunes en los que se podría usar Apache Storm. Para obtener información sobre escenarios del mundo real, lea [Cómo usan Storm las empresas](https://storm.incubator.apache.org/documentation/Powered-By.html).

* Internet de las cosas (IoT)
* Detección de fraudes
* Análisis social
* Extraer, transformar y cargar (ETL)
* Supervisión de redes
* Search
* Mobile Engagement

##¿Cómo se procesan los datos de Storm en HDInsight?

Apache Storm ejecuta **topologías** en lugar de los trabajos de MapReduce con los que podría estar familiarizado en HDInsight o Hadoop. Un clúster de Storm en HDInsight contiene dos tipos de nodos: nodos principales que ejecutan **Nimbus** y nodos de trabajo que ejecutan **Supervisor**.

* **Nimbus**: parecido a JobTracker de Hadoop, es responsable de distribuir código a través del clúster, de asignar tareas a las máquinas virtuales y de supervisar los errores. HDInsight ofrece dos nodos Nimbus, así que no hay un solo punto de error de Storm en HDInsight.

* **Supervisor**: el supervisor de cada nodo de trabajo es responsable de iniciar y detener los **procesos de trabajo** en el nodo.

* **Proceso de trabajo**: ejecuta un subconjunto de una **topología**. Una topología en ejecución se distribuye entre muchos procesos de trabajo a través del clúster.

* **Topología**: define un gráfico de cálculo que procesa **flujos** de datos. A diferencia de los trabajos de MapReduce, las topologías se ejecutan hasta que las detenga.

* **Flujo**: una colección independiente de **tuplas**. Los **spouts** y **bolts** producen flujos y los **bolts** los consumen.

* **Tupla**: una lista con nombres de valores escritos de forma dinámica.

* **Spout**: consume datos de un origen de datos y emite uno o varios **flujos**.

	> [AZURE.NOTE] En muchos casos, los datos se leen de una cola, como Kafka, las colas del Bus de servicio de Azure o los Centros de eventos. La cola garantiza que los datos persistan en caso de una interrupción.

* **Bolt**: consume **flujos**, realiza el procesamiento en **tuplas** y puede emitir **flujos**. Los bolts son responsables también de escribir datos en el almacenamiento externo, como una cola, HBase de HDInsight, un blob u otro almacén de datos.

* **Apache Thrift**: un marco de software para el desarrollo de servicios escalables en distintos lenguajes Así, permite crear servicios que funcionan entre C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk y otros lenguajes.

	* **Nimbus** es un servicio de Thrift y una **topología** es una definición de Thrift, por lo que es posible desarrollar topologías mediante diversos lenguajes de programación.

Para obtener más información sobre los componentes de Storm, consulte el [Tutorial de Storm][apachetutorial] en apache.org.


##¿Qué lenguajes de programación se pueden usar?

El clúster de Storm en HDInsight ofrece compatibilidad con C#, Java y Python.

### C&#35;

Las herramientas de HDInsight para Visual Studio permiten a los desarrolladores de .NET diseñar e implementar una topología en C#. También puede crear topologías híbridas que usen los componentes de Java y C#.

Para obtener más información, consulte [Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

###Java

La mayoría de ejemplos de Java que encuentre serán de Java normal o Trident. Trident es una abstracción de alto nivel que facilita la creación de elementos como uniones, agregaciones, agrupaciones o filtrados. Sin embargo, Trident actúa sobre lotes de tuplas, mientras que una solución de Java sin procesar procesará un flujo una tupla cada vez.

Para obtener más información sobre Trident, consulte el [Tutorial de Trident](https://storm.incubator.apache.org/documentation/Trident-tutorial.html) en apache.org.

Para obtener ejemplos de topologías de Java y Trident, vea la [lista de topologías de ejemplo de Storm](hdinsight-storm-example-topology.md) o los ejemplos de storm-starter en el clúster de HDInsight.

Los ejemplos de storm-starter se encuentran en el directorio \_\_ /usr/hdp/current/storm-client/contrib/storm-starter\_\_ en clústeres basados en Linux, y en el directorio **%storm\_home%\\contrib\\storm-starter** en clústeres basados en Windows.

##¿Cuáles son algunos de los patrones de desarrollo comunes?

###Procesamiento de mensajes garantizado

Storm puede proporcionar diferentes niveles de procesamiento de mensajes garantizado. Por ejemplo, una aplicación básica de Storm puede garantizar un procesamiento una vez al menos y Trident puede garantizar exactamente el procesamiento exactamente una vez.

Para obtener más información, consulte las [Garantías en el procesamiento de los datos](https://storm.apache.org/about/guarantees-data-processing.html) en apache.org.

###IBasicBolt

El patrón consistente en leer una tupla de entrada, emitir cero o más tuplas y, después, confirmar la tupla de entrada inmediatamente al final del método de ejecución es muy común, y Storm ofrece la interfaz [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) para automatizarlo.

###Combinaciones

La unión de dos secuencias de datos variará según la aplicación. Por ejemplo, puede unir cada tupla de varios flujos en una nueva o unir únicamente solo los lotes de tuplas de una ventana específica. De cualquier modo, la unión se puede efectuar mediante [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), que es una forma de definir la forma en que las tuplas se enrutan a los bolts.

En el siguiente ejemplo de Java, se usa fieldsGrouping para enrutar las tuplas que se originan desde los componentes "1", "2" y "3" al bolt **MyJoiner**.

	builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

###Lotes

El procesamiento por lotes se puede efectuar de varias formas. Con una topología básica de Java de Storm, podría usar un simple contador para procesar por lotes un número X de tuplas antes de emitirlas, o bien usar un mecanismo de temporización interno conocido como "tupla de señalización" para emitir un lote cada X segundos.

Para ver un ejemplo de uso de tuplas de señalización, consulte [Análisis de los datos de los sensores con Storm y HBase en HDInsight](hdinsight-storm-sensor-data-analysis.md).

Si está usando Trident, se basa en el procesamiento por lotes de tuplas.

###Almacenamiento en caché

El almacenamiento en caché de memoria interna se usa a menudo como mecanismo para acelerar el procesamiento, porque mantiene en memoria los activos usados con mayor frecuencia. Dado que una topología se distribuye entre varios nodos y varios procesos dentro de cada nodo, debe considerar el uso de [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) para garantizar que las tuplas que contienen los campos que se usan para la búsqueda en caché se enruten siempre al mismo proceso. De esta forma se evita la duplicación de las entradas de la caché entre los procesos.

###Transmisión por secuencias del N principal

Cuando la topología depende del cálculo de un valor de "N principales", como las 5 principales tendencias en Twitter, debe calcular el valor N principales en paralelo y luego combinar el resultado de esos cálculos en un valor global. Esto se puede hacer mediante [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) para enrutar por campo a los bolts paralelos (lo cual particiona los datos según el valor de campo) y, después, enruta a un bolt que determina de forma global el valor N principales.

Para ver un ejemplo de este procedimiento, consulte el ejemplo [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java).

##Pasos siguientes

Obtenga más información sobre las soluciones de análisis en tiempo real con Apache Storm en HDInsight:

* [Introducción a Storm en HDInsight][gettingstarted]

* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.incubator.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.incubator.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

<!---HONumber=AcomDC_0302_2016-->