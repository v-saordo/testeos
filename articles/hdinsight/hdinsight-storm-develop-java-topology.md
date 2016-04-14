<properties
   pageTitle="Desarrollo de topologías basadas en Java para Apache Storm | Microsoft Azure"
   description="Aprenda a crear topologías de Storm en Java mediante la creación de una topología de recuento de palabras sencilla."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/29/2016"
   ms.author="larryfr"/>

#Desarrolle topologías basadas en Java para una aplicación básica de recuento de palabras con Apache Storm y Maven en HDInsight

Aprenda un proceso básico de creación de una topología basada en Java para Apache Storm en HDInsight mediante Maven. Le guiará a través del proceso de creación de una aplicación básica de recuento de palabras mediante el uso de Maven y Java. Aunque en las instrucciones se indica el uso de Eclipse, también puede usar el editor de texto de que prefiera.

Después de completar los pasos descritos en este documento, tendrá una topología básica que puede implementar en Apache Storm en HDInsight.

> [AZURE.NOTE]\: Una versión completa de esta topología está disponible en [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).

##Requisitos previos

* <a href="https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html" target="_blank">Kit de desarrolladores de Java (JDK) versión 7</a>

* <a href="https://maven.apache.org/download.cgi" target="_blank">Maven</a>: Maven es un sistema de compilación de proyectos para proyectos de Java.

* Un editor de texto como Bloc de notas, <a href="http://www.gnu.org/software/emacs/" target="_blank">Emacs<a>, <a href="http://www.sublimetext.com/" target="_blank">Sublime Text</a>, <a href="https://atom.io/" target="_blank">Atom.io</a>, <a href="http://brackets.io/" target="_blank">Brackets.io</a>. O puede usar un entorno de desarrollo integrado (IDE), como <a href="https://eclipse.org/" target="_blank">Eclipse</a> (versión Luna o posterior).

	> [AZURE.NOTE] El editor o IDE puede tener una funcionalidad específica para trabajar con Maven que no se trata en este documento. Para obtener información sobre las capacidades de su entorno de edición, consulte la documentación del producto que esté utilizando.

##Configuración de las variables de entorno

Pueden establecer las siguientes variables de entorno al instalar Java y el JDK. Sin embargo, debe comprobar que existen y que contienen los valores correctos para su sistema.

* **JAVA\_HOME**: debe apuntar al directorio donde está instalado Java Runtime Environment (JRE). Por ejemplo, en una distribución de Unix o Linux, debe tener un valor similar a `/usr/lib/jvm/java-7-oracle`. En Windows, tendría un valor similar a `c:\Program Files (x86)\Java\jre1.7`.

* **PATH**: debe contener las rutas de acceso siguientes:

	* **JAVA\_HOME** (o la ruta de acceso equivalente).

	* **JAVA\_HOME\\bin** (o la ruta de acceso equivalente).

	* El directorio donde está instalado Maven

##Creación de un nuevo proyecto de Maven

Desde la línea de comandos, use el siguiente código para crear un nuevo proyecto de Maven denominado **WordCount**:

	mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

Esto creará un nuevo directorio denominado **WordCount** en la ubicación actual, que contiene un proyecto de Maven básico.

El directorio **WordCount** contendrá los siguientes elementos:

* **pom.xml**: contiene la configuración del proyecto de Maven

* **src\\main\\java\\com\\microsoft\\example**: contiene el código de la aplicación

* **src\\test\\java\\com\\microsoft\\example**: contiene pruebas para la aplicación. En este ejemplo, no se crearán pruebas.

###Supresión del código de ejemplo

Dado que se creará una aplicación propia, elimine los archivos de la aplicación y las pruebas generadas:

*  **src\\test\\java\\com\\microsoft\\example\\AppTest.java**

*  **src\\main\\java\\com\\microsoft\\example\\App.java**

##Adición de dependencias

Dado que se trata de una topología de Storm, debe agregar una dependencia para los componentes de Storm. Abra el archivo **pom.xml** y agregue el código siguiente en la sección **&lt;dependencias>**.

	<dependency>
	  <groupId>org.apache.storm</groupId>
	  <artifactId>storm-core</artifactId>
	  <version>0.9.2-incubating</version>
	  <!-- keep storm out of the jar-with-dependencies -->
	  <scope>provided</scope>
	</dependency>

En tiempo de compilación, Maven usa esta información para buscar **storm-core** en el repositorio de Maven. Busca primero en el repositorio del equipo local. Si los archivos no están allí, se descargan desde el repositorio de Maven público y se almacenan en el repositorio local.

> [AZURE.NOTE] Observe la línea `<scope>provided</scope>` en la sección que se agregó. Indica a Maven que excluya **storm-core** de los archivos JAR que creemos, ya que el sistema lo proporcionará. Esto permite que los paquetes creados sean un poco más pequeños y se garantiza que usarán los bits de **storm-core** incluidos en el clúster de Storm en HDInsight.

##Configuración de compilación

Los complementos de Maven le permiten personalizar las fases de la compilación del proyecto, por ejemplo, cómo se compila el proyecto y cómo se empaqueta en un archivo JAR. Abra el archivo **pom.xml** y agregue el código siguiente directamente encima de la línea `</project>`.

	<build>
	  <plugins>
	  </plugins>
	</build>

Esta sección se utilizará para agregar complementos y otras opciones de configuración de compilación.

###Agregar complementos

En el caso de las topologías de Storm, el <a href="http://mojo.codehaus.org/exec-maven-plugin/" target="_blank">Complemento Exec Maven</a> es útil porque le permite ejecutar fácilmente la topología de manera local en su entorno de desarrollo. Agregue lo siguiente a la sección `<plugins>` del archivo **pom.xml** para incluir el complemento Exec Maven:

	<plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

Otro complemento útil es el <a href="http://maven.apache.org/plugins/maven-compiler-plugin/" target="_blank">Complemento Apache Maven Compiler</a>, que se usa para cambiar las opciones de compilación. Es necesario principalmente para cambiar la versión de Java que usa Maven para el origen y destino de la aplicación. Queremos la versión 1.7.

Agregue lo siguiente en la sección `<plugins>` del archivo **pom.xml** para incluir el complemento Apache Maven Compiler y establezca las versiones de origen y de destino en 1.7.

	<plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

##Creación de la topología

Una topología de Storm basada en Java consta de tres componentes que debe crear (o hacer referencia) como una dependencia.

* **Spouts**: lee datos de orígenes externos y emite flujos de datos a la topología.

* **Bolts**: realiza el procesamiento en flujos que emite spouts u otros bolts, y emite uno o varios flujos.

* **Topology**: define cómo se organizan los spouts y bolts, y proporciona el punto de entrada de la topología.

###Creación del spout

Para reducir los requisitos de la configuración de los orígenes de datos externos, el spout siguiente simplemente emite frases aleatorias. Se trata de una versión modificada de spout que se ofrece con los (<a href="https://github.com/apache/storm/blob/master/examples/storm-starter/" target="_blank">ejemplos de Storm-Starter</a>).

> [AZURE.NOTE] Para ver un ejemplo de un spout que lee desde un origen de datos externos, consulte uno de los siguientes ejemplos:
>
> * <a href="https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java" target="_blank">TwitterSampleSpout</a>: un spout de ejemplo que lee desde Twitter.
>
> * <a href="https://github.com/apache/storm/tree/master/external/storm-kafka" target="_blank">Storm Kafka</a>: un spout que lee desde Kafka.

Para el spout, cree un nuevo archivo denominado **RandomSentenceSpout.java** en el directorio **src\\main\\java\\com\\microsoft\\example** y use lo siguiente como contenido:

    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     * http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

     /**
      * Original is available at https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/RandomSentenceSpout.java
      */

    package com.microsoft.example;

    import backtype.storm.spout.SpoutOutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichSpout;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Values;
    import backtype.storm.utils.Utils;

    import java.util.Map;
    import java.util.Random;

    //This spout randomly emits sentences
    public class RandomSentenceSpout extends BaseRichSpout {
      //Collector used to emit output
      SpoutOutputCollector _collector;
      //Used to generate a random number
      Random _rand;

      //Open is called when an instance of the class is created
      @Override
      public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
      //Set the instance collector to the one passed in
        _collector = collector;
        //For randomness
        _rand = new Random();
      }

      //Emit data to the stream
      @Override
      public void nextTuple() {
      //Sleep for a bit
        Utils.sleep(100);
        //The sentences that will be randomly emitted
        String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
            "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
        //Randomly pick a sentence
        String sentence = sentences[_rand.nextInt(sentences.length)];
        //Emit the sentence
        _collector.emit(new Values(sentence));
      }

      //Ack is not implemented since this is a basic example
      @Override
      public void ack(Object id) {
      }

      //Fail is not implemented since this is a basic example
      @Override
      public void fail(Object id) {
      }

      //Declare the output fields. In this case, an sentence
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("sentence"));
      }
    }

Dedique un momento para leer los comentarios del código y entender cómo funciona este spout.

> [AZURE.NOTE] Aunque esta topología solo usa un spout, otras pueden tener varios que alimenten datos desde orígenes distintos en la topología.

###Creación de los bolts

Los bolts controlan el procesamiento de datos. Para esta topología, hay dos bolts:

* **SplitSentence**: divide las frases que emite **RandomSentenceSpout** en palabras individuales.

* **WordCount**: cuenta cuántas veces se ha repetido cada palabra.

> [AZURE.NOTE] Los bolts pueden hacer prácticamente todo; por ejemplo, cálculo, persistencia o hablar con componentes externos.

Cree dos archivos nuevos, **SplitSentence.java** y **WordCount.Java** en el directorio **src\\main\\java\\com\\microsoft\\example**. Use lo siguiente como contenido para los archivos:

**SplitSentence**

    package com.microsoft.example;

    import java.text.BreakIterator;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class SplitSentence extends BaseBasicBolt {

      //Execute is called to process tuples
      @Override
      public void execute(Tuple tuple, BasicOutputCollector collector) {
        //Get the sentence content from the tuple
        String sentence = tuple.getString(0);
        //An iterator to get each word
        BreakIterator boundary=BreakIterator.getWordInstance();
        //Give the iterator the sentence
        boundary.setText(sentence);
        //Find the beginning first word
        int start=boundary.first();
        //Iterate over each word and emit it to the output stream
        for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
          //get the word
          String word=sentence.substring(start,end);
          //If a word is whitespace characters, replace it with empty
          word=word.replaceAll("\\s+","");
          //if it's an actual word, emit it
          if (!word.equals("")) {
            collector.emit(new Values(word));
          }
        }
      }

      //Declare that emitted tuples will contain a word field
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("word"));
      }
    }

**WordCount**

    package com.microsoft.example;

    import java.util.HashMap;
    import java.util.Map;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class WordCount extends BaseBasicBolt {
      //For holding words and counts
        Map<String, Integer> counts = new HashMap<String, Integer>();

        //execute is called to process tuples
        @Override
        public void execute(Tuple tuple, BasicOutputCollector collector) {
          //Get the word contents from the tuple
          String word = tuple.getString(0);
          //Have we counted any already?
          Integer count = counts.get(word);
          if (count == null)
            count = 0;
          //Increment the count and store it
          count++;
          counts.put(word, count);
          //Emit the word and the current count
          collector.emit(new Values(word, count));
        }

        //Declare that we will emit a tuple containing two fields; word and count
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
          declarer.declare(new Fields("word", "count"));
        }
      }

Dedique un momento a leer los comentarios del código para comprender cómo funciona cada bolt.

###Creación de la topología

La topología vincula los spouts y los bolts juntos en un gráfico, que define cómo fluyen los datos entre los componentes. También proporciona sugerencias de paralelismos que utiliza Storm al crear instancias de los componentes dentro del clúster.

Lo siguiente es un diagrama básico del gráfico de componentes para esta topología.

![diagrama que muestra la disposición de los spouts y bolts](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

Para implementar la topología, cree un nuevo archivo denominado **WordCountTopology.java** en el directorio **src\\main\\java\\com\\microsoft\\example**. Use lo siguiente como contenido del archivo:

	package com.microsoft.example;

    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.topology.TopologyBuilder;
    import backtype.storm.tuple.Fields;

    import com.microsoft.example.RandomSentenceSpout;

    public class WordCountTopology {

      //Entry point for the topology
      public static void main(String[] args) throws Exception {
      //Used to build the topology
        TopologyBuilder builder = new TopologyBuilder();
        //Add the spout, with a name of 'spout'
        //and parallelism hint of 5 executors
        builder.setSpout("spout", new RandomSentenceSpout(), 5);
        //Add the SplitSentence bolt, with a name of 'split'
        //and parallelism hint of 8 executors
        //shufflegrouping subscribes to the spout, and equally distributes
        //tuples (sentences) across instances of the SplitSentence bolt
        builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
        //Add the counter, with a name of 'count'
        //and parallelism hint of 12 executors
        //fieldsgrouping subscribes to the split bolt, and
        //ensures that the same word is sent to the same instance (group by field 'word')
        builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

        //new configuration
        Config conf = new Config();
        conf.setDebug(true);

        //If there are arguments, we are running on a cluster
        if (args != null && args.length > 0) {
          //parallelism hint to set the number of workers
          conf.setNumWorkers(3);
          //submit the topology
          StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
        }
        //Otherwise, we are running locally
        else {
          //Cap the maximum number of executors that can be spawned
          //for a component to 3
          conf.setMaxTaskParallelism(3);
          //LocalCluster is used to run locally
          LocalCluster cluster = new LocalCluster();
          //submit the topology
          cluster.submitTopology("word-count", conf, builder.createTopology());
          //sleep
          Thread.sleep(10000);
          //shut down the cluster
          cluster.shutdown();
        }
      }
    }

Dedique un momento a leer los comentarios del código para saber cómo se define la topología y cómo se envía después al clúster.

##Prueba de la topología de manera local

Después de guardar los archivos, use el comando siguiente para probar localmente la topología.

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

Mientras se ejecuta, la topología mostrará información de inicio. A continuación, comenzará a mostrar líneas similares a las siguientes, a medida que se emiten frases desde el spout y los bolts las procesan.

    15398 [Thread-16-split] INFO  backtype.storm.daemon.executor - Processing received message source: spout:10, stream: default, id: {}, [an apple a day keeps thedoctor away]]
    15398 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [an]
    15399 [Thread-10-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [an]
    15399 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [apple]
    15400 [Thread-8-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [apple]
    15400 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [a]
    15399 [Thread-10-count] INFO  backtype.storm.daemon.task - Emitting: count default [an, 53]
    15400 [Thread-12-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [a]
    15400 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [day]
    15400 [Thread-8-count] INFO  backtype.storm.daemon.task - Emitting: count default [apple, 53]
    15401 [Thread-10-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [day]
    15401 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [keeps]
    15401 [Thread-12-count] INFO  backtype.storm.daemon.task - Emitting: count default [a, 53]

Como puede ver en esta salida, se produjo lo siguiente:

1. Spout emite "an apple a day keeps the doctor away".

2. El bolt de división comienza a emitir palabras individuales de la frase.

3. El bolt de recuento comienza a emitir cada palabra y cuántas veces se ha emitido.

Si se consultan los datos emitidos por el bolt de recuento, podemos comprobar que "apple" se emitió 53 veces. El recuento seguirá subiendo mientras se ejecute la topología, ya que se emiten las mismas frases de forma aleatoria una y otra vez, y el recuento nunca se restablece.

##Trident

Trident es una abstracción de alto nivel que ofrece Storm. Admite el procesamiento con estado. La principal ventaja de Trident es que puede garantizar que todos los mensajes que entran en la topología se procesan una sola vez. Esto es difícil de lograr en una topología de Java, cuya garantía es que los mensajes se procesarán al menos una vez. También hay otras diferencias, como los componentes integrados que se pueden usar en lugar de crear bolts. De hecho, los bolts se reemplazan completamente por componentes menos genéricos, como filtros, proyecciones y funciones.

Las aplicaciones de Trident se pueden crear mediante proyectos de Maven. Siga los mismos pasos básicos que se mostraron anteriormente en este artículo; lo único diferente es el código.

Para obtener más información sobre Trident, consulte <a href="http://storm.apache.org/documentation/Trident-API-Overview.html" target="_blank">Información general sobre la API de Trident</a>.

Para ver un ejemplo de una aplicación de Trident, consulte [Tendencias de Twitter con Apache Storm en HDInsight](hdinsight-storm-twitter-trending.md).

##Pasos siguientes

Ha aprendido a crear una topología de Storm con Java. Ahora obtenga información sobre:

* [Implementación y administración de topologías de Apache Storm en HDInsight](hdinsight-storm-deploy-monitor-topology.md)

* [Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Puede encontrar más topologías de ejemplo de Storm en [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=AcomDC_0204_2016-->