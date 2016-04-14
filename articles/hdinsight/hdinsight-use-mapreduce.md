<properties
   pageTitle="MapReduce con Hadoop en HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo ejecutar trabajos de MapReduce en Hadoop en clústeres de HDInsight. Ejecutará una operación de recuento de palabras básica implementada como un trabajo de MapReduce de Java."
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
   ms.date="01/08/2016"
   ms.author="larryfr"/>

# Uso de MapReduce en Hadoop en HDInsight

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

En este artículo, obtendrá información sobre cómo ejecutar trabajos de MapReduce en clústeres de HDInsight. Ejecutamos una operación de recuento de palabras básica implementada como un trabajo de MapReduce de Java.

##<a id="whatis"></a>¿Qué es MapReduce?

MapReduce de Hadoop es un marco de software para escribir trabajos que procesan enormes cantidades de datos. La entrada de datos se divide en fragmentos independientes que, a continuación, se procesan en paralelo a través de los nodos del clúster. Un trabajo de MapReduce consta de dos funciones:

* **Asignador**: consume datos de entrada, los analiza (normalmente con un filtro y operaciones de ordenación) y emite tuplas (pares de clave-valor)
* **Reductor**: consume tuplas emitidas por el asignador y realiza una operación de resumen que crea un resultado combinado más pequeño de los datos del asignador

En el siguiente diagrama se muestra un ejemplo de trabajo de MapReduce de recuento de palabras básico:

![HDI.ProgramaRecuentoPalabras][image-hdi-wordcountdiagram]

La salida de este trabajo es un recuento de las veces que aparece cada palabra en el texto que se ha analizado.

* El asignador utiliza cada línea del texto de entrada como una entrada y la desglosa en palabras. Emite un par clave-valor cada vez que se detecta una palabra, seguida por un 1. La salida se ordena antes de enviarla al reductor.

* El reductor suma estos recuentos individuales de cada palabra y emite un solo par clave-valor que contiene la palabra seguido de la suma de sus apariciones.

MapReduce se puede implementar en varios lenguajes. Java es la implementación más común y se utiliza para fines de demostración en este documento.

### Streaming de Hadoop

Lenguajes o marcos basados en Java y la máquina virtual de Java (por ejemplo, Scalding o Cascading) se pueda ejecutar directamente como un trabajo de MapReduce, similar a una aplicación de Java. Otros, como C# o Python, o ejecutables independientes, deben usar streaming de Hadoop.

Streaming de Hadoop se comunica con el asignador y el reductor por STDIN y STDOUT; el asignador y el reductor leen datos una línea cada vez desde STDIN y escriben la salida a STDOUT. Cada línea leída o emitida por el asignador y el reductor debe estar en el formato de un par de clave-valor delimitado por un carácter de tabulación:

    [key]/t[value]

Para obtener más información, consulte [Streaming de Hadoop](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Para obtener ejemplos del uso del streaming de Hadoop con HDInsight, vea lo siguiente:

* [Desarrollo de programas de streaming de Hadoop C#](hdinsight-hadoop-develop-deploy-streaming-jobs.md)

* [Desarrollar trabajos de MapReduce de Python](hdinsight-hadoop-streaming-python.md)

##<a id="data"></a>Acerca de los datos de ejemplo

En este ejemplo, para datos de ejemplo, usará los cuadernos de Leonardo Da Vinci, que se proporcionan como documento de texto en el clúster de HDInsight.

Los datos de ejemplo se almacenan en el almacenamiento de blobs de Azure, que HDInsight usa como el sistema de archivos predeterminado para clústeres de Hadoop. HDInsight puede acceder a archivos almacenados en el almacenamiento de blobs mediante el prefijo **wasb**. Por ejemplo, para tener acceso al archivo sample.log, use la siguiente sintaxis:

	wasb:///example/data/gutenberg/davinci.txt

Dado que el almacenamiento de blobs de Azure es el almacenamiento predeterminado para HDInsight, también puede acceder al archivo mediante **/example/data/gutenberg/davinci.txt**.

> [AZURE.NOTE] En la sintaxis anterior, ****wasb:///** se usa para acceder a archivos almacenados en el contenedor de almacenamiento predeterminado para el clúster de HDInsight. Si especificó cuentas de almacenamiento adicionales cuando aprovisionó el clúster y desea obtener acceso a los archivos almacenados en esas cuentas, puede acceder a los datos especificando el nombre de contenedor y la dirección de las cuentas de almacenamiento. Por ejemplo: ****wasb://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**.

##<a id="job"></a>Acerca del MapReduce de ejemplo

El trabajo de MapReduce que se usa en este ejemplo se encuentra en ****wasb://example/jars/hadoop-mapreduce-examples.jar** y se proporciona con el clúster de HDInsight. Contiene un ejemplo de recuento de palabras que se ejecutará con **davinci.txt**.

> [AZURE.NOTE] En los clústeres de HDInsight 2.1, la ubicación del archivo es ****wasb:///example/jars/hadoop-examples.jar**.

Como referencia, lo siguiente es el código Java para el trabajo de MapReduce de recuento de palabras:

	package org.apache.hadoop.examples;

	import java.io.IOException;
	import java.util.StringTokenizer;

	import org.apache.hadoop.conf.Configuration;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.IntWritable;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapreduce.Job;
	import org.apache.hadoop.mapreduce.Mapper;
	import org.apache.hadoop.mapreduce.Reducer;
	import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
	import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
	import org.apache.hadoop.util.GenericOptionsParser;

	public class WordCount {

	  public static class TokenizerMapper
	       extends Mapper<Object, Text, Text, IntWritable>{

	    private final static IntWritable one = new IntWritable(1);
	    private Text word = new Text();

	    public void map(Object key, Text value, Context context
	                    ) throws IOException, InterruptedException {
	      StringTokenizer itr = new StringTokenizer(value.toString());
	      while (itr.hasMoreTokens()) {
	        word.set(itr.nextToken());
	        context.write(word, one);
	      }
	    }
	  }

	  public static class IntSumReducer
	       extends Reducer<Text,IntWritable,Text,IntWritable> {
	    private IntWritable result = new IntWritable();

	    public void reduce(Text key, Iterable<IntWritable> values,
	                       Context context
	                       ) throws IOException, InterruptedException {
	      int sum = 0;
	      for (IntWritable val : values) {
	        sum += val.get();
	      }
	      result.set(sum);
	      context.write(key, result);
	    }
	  }

	  public static void main(String[] args) throws Exception {
	    Configuration conf = new Configuration();
	    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
	    if (otherArgs.length != 2) {
	      System.err.println("Usage: wordcount <in> <out>");
	      System.exit(2);
	    }
	    Job job = new Job(conf, "word count");
	    job.setJarByClass(WordCount.class);
	    job.setMapperClass(TokenizerMapper.class);
	    job.setCombinerClass(IntSumReducer.class);
	    job.setReducerClass(IntSumReducer.class);
	    job.setOutputKeyClass(Text.class);
	    job.setOutputValueClass(IntWritable.class);
	    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
	    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
	    System.exit(job.waitForCompletion(true) ? 0 : 1);
	  }
	}

Para obtener instrucciones sobre cómo escribir su propio trabajo de MapReduce, consulte [Desarrollo de programas MapReduce de Java para HDInsight](hdinsight-develop-deploy-java-mapreduce.md).

##<a id="run"></a>Ejecución de MapReduce

HDInsight puede ejecutar trabajos de HiveQL mediante una variedad de métodos. Use la tabla siguiente para decidir cuál es el método adecuado para usted luego siga el vínculo para ver un tutorial.

| **Use esto**... | **...para hacer esto** | ...con este **sistema operativo de clúster** | ...desde este **sistema operativo de cliente** |
|:-------------------------------------------------------------------|:--------------------------------------------------------|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md) | Uso del comando Hadoop mediante **SSH** | Linux | Linux, Unix, Mac OS X o Windows |
| [Curl](hdinsight-hadoop-use-mapreduce-curl.md) | Enviar el trabajo de forma remota mediante **REST** | Linux o Windows | Linux, Unix, Mac OS X o Windows |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) | Enviar el trabajo de forma remota mediante **Windows PowerShell** | Linux o Windows | Windows |
| [Escritorio remoto](hdinsight-hadoop-use-mapreduce-remote-desktop) | Uso del comando Hadoop mediante **Escritorio remoto** | Windows | Windows |

##<a id="nextsteps"></a>Pasos siguientes

Aunque MapReduce ofrece potentes capacidades de diagnóstico, puede ser un poco difícil de dominar. Hay varios marcos basados en Java que facilitan la definición de aplicaciones de MapReduce, además de tecnologías como Pig y Hive, que proporcionan una manera más sencilla de trabajar con datos en HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Desarrollo de programas MapReduce de Java para HDInsight](hdinsight-develop-deploy-java-mapreduce.md)

* [Desarrollo de programas de MapReduce de streaming de Python para HDInsight](hdinsight-hadoop-streaming-python.md)

* [Desarrollo de programas de MapReduce de streaming de Hadoop C# para HDInsight][hdinsight-develop-streaming]

* [Desarrollo de trabajos de MapReduce de Scalding con Hadoop Apache en HDInsight](hdinsight-hadoop-mapreduce-scalding.md)

* [Uso de Hive con HDInsight][hdinsight-use-hive]

* [Uso de Pig con HDInsight][hdinsight-use-pig]

* [Ejecución de muestras de HDInsight][hdinsight-samples]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif

<!---HONumber=AcomDC_0218_2016-->