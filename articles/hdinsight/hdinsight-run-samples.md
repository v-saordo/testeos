<properties
	pageTitle="Ejecución de ejemplos de Hadoop en HDInsight | Microsoft Azure"
	description="Introducción al uso del servicio HDInsight de Azure con los ejemplos que se proporcionan. Use scripts de PowerShell que ejecutan programas MapReduce en clústeres de datos."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="jgao"/>

#Ejecución de ejemplos de Hadoop MapReduce en HDInsight basado en Windows

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Se proporciona un conjunto de muestras para ayudarle a empezar a ejecutar trabajos de MapReduce en clústeres de Hadoop con HDInsight de Azure. Estas muestras se habilitan en cada uno de los clústeres administrados de HDInsight que se crean. La ejecución de estas muestras le permitirá familiarizarse con el uso de cmdlets de Azure PowerShell para ejecutar trabajos en clústeres de Hadoop.

- [**Recuento de palabras**][hdinsight-sample-wordcount]\: cuenta las apariciones de una palabra en un archivo de texto.
- [**Recuento de palabras de streaming de C#**][hdinsight-sample-csharp-streaming]\: cuenta las apariciones de una palabra en un archivo de texto mediante la interfaz de streaming de Hadoop.
- [**Estimador de pi**][hdinsight-sample-pi-estimator]\: usa un método estadístico (quasi-Monte Carlo) para calcular el valor de pi.
- [**Graysort de 10 GB**][hdinsight-sample-10gb-graysort]\: se ejecuta una muestra de GraySort de uso general en un archivo de 10 GB mediante el uso de HDInsight. Hay tres trabajos que se deben ejecutar: Teragen para generar los datos, Terasort para ordenarlos y Teravalidate para confirmar que los datos se han ordenado correctamente.

>[AZURE.NOTE] El código de origen puede encontrarse en el anexo.

En la web se encuentra disponible mucha documentación adicional sobre las tecnologías relacionadas con Hadoop, como la transmisión y programación de MapReduce basada en Java y documentación sobre los cmdlets que se usan en el scripting de Windows PowerShell. Para obtener más información acerca de estos recursos, consulte:

- [Desarrollo de programas MapReduce de Java para Hadoop en HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
- [Desarrollo de programas de streaming de Hadoop C# para HDInsight](hdinsight-hadoop-develop-deploy-streaming-jobs.md)
- [Envío de trabajos de Hadoop en HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Introducción a Azure HDInsight][hdinsight-introduction]

En la actualidad, muchas personas prefieren Hive y Pig a MapReduce. Para más información, consulte:

- [Uso de Hive en HDInsight](hdinsight-use-hive.md)
- [Uso de Pig en HDInsight](hdinsight-use-pig.md)
 
**Requisitos previos**:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **un clúster de HDInsight**. Para obtener instrucciones acerca de varias formas de creación de dichos clústeres, consulte [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md).
- **Una estación de trabajo con Azure PowerShell**. Consulte [Instalar Azure PowerShell 1.0 y versiones posteriores](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater).

## <a name="hdinsight-sample-wordcount"></a>Recuento de palabras: Java 

Para enviar un proyecto de MapReduce, primero hay que crear una definición de trabajo de MapReduce. En la definición del trabajo, se especifica el archivo jar de programa de MapReduce y la ubicación del archivo jar, que es ****wasb:///example/jars/hadoop-mapreduce-examples.jar**, el nombre de clase y los argumentos. El programa de MapReduce de recuento de palabras toma dos argumentos: el archivo de origen que se usará para contar las palabras y la ubicación del resultado.

El código fuente puede encontrarse en el [Anexo A](#apendix-a---the-word-count-MapReduce-program-in-java).

Para conocer el procedimiento de desarrollo del programa MapReduce de Java, consulte [Desarrollo de programas MapReduce de Java para Hadoop en HDInsight](hdinsight-develop-deploy-java-mapreduce.md).
 
**Para enviar un trabajo de MapReduce de recuento de palabras**

1. Abra **Windows PowerShell ISE**. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].
2. Pegue el siguiente script de PowerShell:

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
		
		Select-AzureRmSubscription -SubscriptionName $subscriptionName
		
		# Define the MapReduce job
		$mrJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
									-JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
									-ClassName "wordcount" `
									-Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput1"
		
		# Submit the job and wait for job completion
		$cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
		$mrJob = Start-AzureRmHDInsightJob `
							-ResourceGroupName $resourceGroupName `
							-ClusterName $clusterName `
							-HttpCredential $cred `
							-JobDefinition $mrJobDefinition 
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-JobId $mrJob.JobId 
		
		# Get the job output
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-DefaultStorageAccountName $defaultStorageAccount `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-DefaultContainer $defaultStorageContainer  `
			-JobId $mrJob.JobId `
			-DisplayOutputType StandardError

		# Download the job output to the workstation
		$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
		Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
		
		# Display the output file
		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	El trabajo de MapReduce genera un archivo denominado *part-r-00000* con las palabras y los recuentos. El script usa el comando **findstr** para enumerar todas las palabras que contienen *"there*".

3. Establezca las 3 variables y ejecute el script.

## <a name="hdinsight-sample-csharp-streaming"></a>Recuento de palabras: streaming en C#

Hadoop ofrece una API de streaming para MapReduce que le permite escribir mapas y reducir funciones en lenguajes distintos de Java.

> [AZURE.NOTE] Los pasos de este tutorial solo se aplican a los clústeres de HDInsight basados en Windows. Para obtener un ejemplo de streaming para clústeres de HDInsight basados en Linux, consulte [Desarrollo de programas de streaming en Python para HDInsight](hdinsight-hadoop-streaming-python.md).

En el ejemplo, el asignador y el reductor son ejecutables que leen la entrada desde [stdin][stdin-stdout-stderr] (línea a línea) y emiten la salida en [stdout][stdin-stdout-stderr]. El programa cuenta todas las palabras del texto.

Cuando se especifica un archivo ejecutable para los **asignadores**, cada tarea del asignador inicia el ejecutable como un proceso aparte al inicializar el asignador. Mientras se ejecuta la tarea del asignador, convierte sus entradas en líneas y alimenta dichas líneas a [stdin][stdin-stdout-stderr] del proceso.

Mientras tanto, el asignador recopila la salida orientada a líneas desde el elemento stdout del proceso. Convierte cada línea en un par de clave y valor, que se recopila como la salida del asignador. De manera predeterminada, el prefijo de una línea hasta el primer carácter de tabulación es la clave y el resto de la línea (sin incluir el carácter de tabulación) es el valor. Si no hay un carácter de tabulación en la línea, se considera que toda la línea es la clave y el valor es nulo.

Cuando se especifica un archivo ejecutable para los **reductores**, cada tarea del reductor inicia el ejecutable como un proceso aparte al inicializar el reductor. Mientras se ejecuta la tarea del reductor, convierte sus pares clave-valor de entrada en líneas y alimenta las líneas a [stdin][stdin-stdout-stderr] del proceso.

Mientras tanto, el reductor recopila la salida orientada a líneas desde el elemento [stdout][stdin-stdout-stderr] del proceso. Convierte cada línea en un par de clave y valor, que se recopila como la salida del reductor. De manera predeterminada, el prefijo de una línea hasta el primer carácter de tabulación es la clave y el resto de la línea (sin incluir el carácter de tabulación) es el valor.

Para obtener más información acerca de la interfaz de streaming de Hadoop, consulte [Hadoop Streaming][hadoop-streaming].

**Para enviar un trabajo de recuento de palabras de streaming de C#**

- Siga el procedimiento de [Recuento de palabras: Java](#word-count-java) y reemplace la definición de trabajo por lo siguiente:

		$mrJobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
									-File "/example/apps/" `
									-Mapper "cat.exe" `
									-Reducer "wc.exe" `
									-InputPath "/example/data/gutenberg/davinci.txt" `
									-OutputPath "/example/data/StreamingOutput/wc.txt"


	El archivo de salida será:
	
		example/data/StreamingOutput/wc.txt/part-00000		
								
## <a name="hdinsight-sample-pi-estimator"></a>Estimador de pi

El estimador de pi usa un método estadístico (cuasi Monte Carlo) para calcular el valor de pi. Los puntos colocados en el interior aleatorio de un cuadrado unitario también entran dentro de un círculo inscrito dentro de ese cuadrado con una probabilidad igual al área del círculo, pi/4. El valor de pi se puede estimar a partir del valor de 4R, donde R es la proporción de la cantidad de puntos contados dentro del círculo con respecto al número total de puntos que se encuentran dentro del cuadrado. Mientras más grande sea la muestra de puntos usada, mejor resulta el valor calculado.

El script que se proporciona para esta muestra envía un trabajo jar de Hadoop y se configura para ejecutarse con un valor de 16 asignaciones, cada una de las cuales debe procesar 10 millones de puntos de muestra por los valores de parámetro. Estos valores de parámetro se pueden cambiar para mejorar el valor calculado de pi. Como referencia, las primeras 10 posiciones decimales de pi son 3,1415926535.

**Para enviar un trabajo del estimador de pi**

- Siga el procedimiento de [Recuento de palabras: Java](#word-count-java) y reemplace la definición de trabajo por lo siguiente:

		$mrJobJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
									-JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
									-ClassName "pi" `
									-Arguments "16", "10000000"

## <a name="hdinsight-sample-10gb-graysort"></a>GraySort de 10 GB

Este ejemplo utiliza solo 10 GB de datos, para así poder ejecutarlo relativamente rápido. En él se emplean las aplicaciones de MapReduce, desarrolladas por Owen O'Malley y Arun Murthy, que ganaron el estándar de comparación anual de ordenación de terabytes de fin general ("daytona") en 2009 con una velocidad de 0,578 TB/min (100 TB en 173 minutos). Para obtener más información sobre este y otros estándares de comparación de ordenación, consulte el sitio [Sortbenchmark](http://sortbenchmark.org/).

Este ejemplo utiliza tres conjuntos de programas de MapReduce:

1. **TeraGen** es un programa de MapReduce que puede utilizarse para generar las filas de datos que se van a ordenar.
2. **TeraSort** toma una muestra de los datos de entrada y usa MapReduce para ordenar los datos de manera absoluta. TeraSort es un ordenamiento estándar de funciones de MapReduce, con la excepción de un particionador personalizado que utiliza una lista ordenada de N-1 claves de muestra que definen el rango de claves para cada reducción. En concreto, todas las claves, como esa muestra[i-1] <= clave < muestra[i] se envían a la reducción i. Esto garantiza que las salidas de la reducción i sean todas menores que la salida de la reducción i+1.
3. **TeraValidate** es un programa de MapReduce que valida que la salida se ordene de manera global. Crea una asignación por archivo en el directorio de salida y cada asignación asegura que cada clave es menor o igual que la anterior. La función de asignación también genera registros de la primera y la última clave de cada archivo, y la función de reducción garantiza que la primera clave del archivo i es mayor que la última clave de archivo i-1. Los problemas se notifican como una salida de la reducción con las claves que no están en orden.

El formato de entrada y salida, que utilizan las tres aplicaciones, lee y escribe los archivos de texto en el formato correcto. La salida de la reducción tiene la replicación definida en 1, en lugar del valor predeterminado 3, porque el concurso de estándar de comparación no requiere que los datos de salida se repliquen en varios nodos.

El ejemplo requiere tres tareas, cada una de las cuales corresponde a uno de los programas de MapReduce que se describen en la introducción:

1. Ejecute el trabajo de MapReduce **TeraGen** para generar los datos que se van a ordenar.
2. Ejecute el trabajo de MapReduce **TeraSort** para ordenar los datos.
3. Ejecute el trabajo de MapReduce **TeraValidate** para confirmar que los datos se han ordenado correctamente.

**Para enviar los trabajos**

- Siga el procedimiento de [Recuento de palabras: Java](#word-count-java) y use las siguientes definiciones de trabajo:

	$teragen = New-$teragen = New-AzureRmHDInsightMapReduceJobDefinition `
								-JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` -ClassName "teragen" ` -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input"
	
	$terasort = New-AzureRmHDInsightMapReduceJobDefinition `
								-JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` -ClassName "terasort" ` -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output"
	
	$teravalidate = New-AzureRmHDInsightMapReduceJobDefinition `
								-JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` -ClassName "teravalidate" ` -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate"


##Pasos siguientes 

En este artículo y en los artículos en cada una de las muestras, ha obtenido información sobre cómo ejecutar las muestras incluidas en los clústeres de HDInsight mediante Azure PowerShell. Para obtener acceso a tutoriales sobre cómo usar Pig, Hive y MapReduce con HDInsight, consulte los siguientes temas:

* [Introducción al uso de Hadoop con Hive en HDInsight para analizar el uso de datos móviles][hdinsight-get-started]
* [Uso de Pig con Hadoop en HDInsight][hdinsight-use-pig]
* [Uso de Hive con Hadoop en HDInsight][hdinsight-use-hive]
* [Envío de trabajos de Hadoop en HDInsight][hdinsight-submit-jobs]
* [Documentación de SDK de HDInsight de Azure][hdinsight-sdk-documentation]
* [Depuración de Hadoop en HDInsight: mensajes de error][hdinsight-errors]


## Anexo A: código fuente del recuento de palabras

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


## Anexo B: código fuente del streaming de recuento de palabras

El programa de MapReduce utiliza la aplicación cat.exe como una interfaz de asignación para transmitir el texto en la consola y la aplicación wc.exe como la interfaz de reducción para contar el número de palabras que se transmiten desde un documento. Tanto el asignador como el reductor leen caracteres, línea por línea, a partir de la secuencia de entrada estándar (stdin) y escriben en la secuencia de salida estándar (stdout).

	// The source code for the cat.exe (Mapper).

	using System;
	using System.IO;

	namespace cat
	{
	    class cat
	    {
	        static void Main(string[] args)
	        {
	            if (args.Length > 0)
	            {
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            string line;
	            while ((line = Console.ReadLine()) != null)
	            {
	                Console.WriteLine(line);
	            }
	        }
	    }
	}



El código del asignador en el archivo cat.cs usa un objeto [StreamReader][streamreader] para leer los caracteres de la transmisión entrante en la consola, la cual escribe a continuación la transmisión en la secuencia de salida estándar con el método [Console.Writeline][console-writeline] estático.


	// The source code for wc.exe (Reducer) is:

	using System;
	using System.IO;
	using System.Linq;

	namespace wc
	{
	    class wc
	    {
	        static void Main(string[] args)
	        {
	            string line;
	            var count = 0;

	            if (args.Length > 0){
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            while ((line = Console.ReadLine()) != null) {
	                count += line.Count(cr => (cr == ' ' || cr == '\n'));
	            }
	            Console.WriteLine(count);
	        }
	    }
	}


El código reductor en el archivo wc.cs usa un objeto [StreamReader][streamreader] para leer caracteres de la secuencia de entrada estándar que han sido de salida por el asignador cat.exe. A medida que lee los caracteres con el método [Console.Writeline][console-writeline], cuenta las palabras contando espacios y caracteres de fin de línea al final de cada palabra. A continuación, escribe el total en la secuencia de salida estándar con el método [Console.Writeline][console-writeline].





## Anexo C: código fuente del estimador de pi

El código Java del estimador de pi que contiene las funciones de asignador y reductor está disponible para inspección a continuación. El programa de asignador genera un número especificado de puntos ubicados en el interior aleatorio de un cuadrado unitario y, a continuación, cuenta la cantidad de esos puntos que se encuentran dentro del círculo. El programa de reductor acumula los puntos que cuentan los asignadores y, a continuación, calcula el valor de pi a partir de la fórmula 4R, donde R es la proporción de la cantidad de puntos contados dentro del círculo con respecto al número total de puntos que se encuentran dentro del cuadrado.

 	/**
 	* Licensed to the Apache Software Foundation (ASF) under one
 	* or more contributor license agreements. See the NOTICE file
 	* distributed with this work for additional information
 	* regarding copyright ownership. The ASF licenses this file
 	* to you under the Apache License, Version 2.0 (the
 	* "License"); you may not use this file except in compliance
 	* with the License. You may obtain a copy of the License at
 	*
	* http://www.apache.org/licenses/LICENSE-2.0
 	*
 	* Unless required by applicable law or agreed to in writing, software
 	* distributed under the License is distributed on an "AS IS" BASIS,
 	* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or 	implied.
 	* See the License for the specific language governing permissions and
 	* limitations under the License.
 	*/

 	package org.apache.hadoop.examples;

 	import java.io.IOException;
 	import java.math.BigDecimal;
 	import java.util.Iterator;

 	import org.apache.hadoop.conf.Configured;
 	import org.apache.hadoop.fs.FileSystem;
 	import org.apache.hadoop.fs.Path;
 	import org.apache.hadoop.io.BooleanWritable;
 	import org.apache.hadoop.io.LongWritable;
 	import org.apache.hadoop.io.SequenceFile;
 	import org.apache.hadoop.io.Writable;
 	import org.apache.hadoop.io.WritableComparable;
 	import org.apache.hadoop.io.SequenceFile.CompressionType;
 	import org.apache.hadoop.mapred.FileInputFormat;
 	import org.apache.hadoop.mapred.FileOutputFormat;
 	import org.apache.hadoop.mapred.JobClient;
 	import org.apache.hadoop.mapred.JobConf;
 	import org.apache.hadoop.mapred.MapReduceBase;
 	import org.apache.hadoop.mapred.Mapper;
 	import org.apache.hadoop.mapred.OutputCollector;
 	import org.apache.hadoop.mapred.Reducer;
 	import org.apache.hadoop.mapred.Reporter;
 	import org.apache.hadoop.mapred.SequenceFileInputFormat;
 	import org.apache.hadoop.mapred.SequenceFileOutputFormat;
 	import org.apache.hadoop.util.Tool;
 	import org.apache.hadoop.util.ToolRunner;


	//A Map-reduce program to estimate the value of Pi
	//using quasi-Monte Carlo method.
	//
	//Mapper:
	//Generate points in a unit square
	//and then count points inside/outside of the inscribed circle of the square.
	//
	//Reducer:
	//Accumulate points inside/outside results from the mappers.
	//Let numTotal = numInside + numOutside.
	//The fraction numInside/numTotal is a rational approximation of
	//the value (Area of the circle)/(Area of the square),
	//where the area of the inscribed circle is Pi/4
	//and the area of unit square is 1.
	//Then, Pi is estimated value to be 4(numInside/numTotal).
	//

 	public class PiEstimator extends Configured implements Tool {
	//tmp directory for input/output
 	static private final Path TMP_DIR = new Path(
 	PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

	//2-dimensional Halton sequence {H(i)},
	//where H(i) is a 2-dimensional point and i >= 1 is the index.
	//Halton sequence is used to generate sample points for Pi estimation.
 	private static class HaltonSequence {
	// Bases
 	static final int[] P = {2, 3};
	//Maximum number of digits allowed
 	static final int[] K = {63, 40};

 	private long index;
 	private double[] x;
 	private double[][] q;
 	private int[][] d;

	//Initialize to H(startindex),
	//so the sequence begins with H(startindex+1).
 	HaltonSequence(long startindex) {
 	index = startindex;
 	x = new double[K.length];
 	q = new double[K.length][];
 	d = new int[K.length][];
 	for(int i = 0; i < K.length; i++) {
 	q[i] = new double[K[i]];
 	d[i] = new int[K[i]];
 	}

 	for(int i = 0; i < K.length; i++) {
 	long k = index;
 	x[i] = 0;

 	for(int j = 0; j < K[i]; j++) {
 	q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
 	d[i][j] = (int)(k % P[i]);
 	k = (k - d[i][j])/P[i];
 	x[i] += d[i][j] * q[i][j];
 	}
 	}
 	}

	//Compute next point.
	//Assume the current point is H(index).
	//Compute H(index+1).
	//@return a 2-dimensional point with coordinates in [0,1)^2
 	double[] nextPoint() {
 	index++;
 	for(int i = 0; i < K.length; i++) {
 	for(int j = 0; j < K[i]; j++) {
 	d[i][j]++;
 	x[i] += q[i][j];
 	if (d[i][j] < P[i]) {
 	break;
 	}
 	d[i][j] = 0;
 	x[i] -= (j == 0? 1.0: q[i][j-1]);
 	}
 	}
 	return x;
 	}
 	}

	//Mapper class for Pi estimation.
	//Generate points in a unit square and then
	//count points inside/outside of the inscribed circle of the square.
 	public static class PiMapper extends MapReduceBase
 	implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

	//Map method.
	//@param offset samples starting from the (offset+1)th sample.
	//@param size the number of samples for this map
	//@param out output {ture->numInside, false->numOutside}
	//@param reporter
 	public void map(LongWritable offset,
 	LongWritable size,
 	OutputCollector<BooleanWritable, LongWritable> out,
 	Reporter reporter) throws IOException {

 	final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
 	long numInside = 0L;
 	long numOutside = 0L;

 	for(long i = 0; i < size.get(); ) {
 	//generate points in a unit square
 	final double[] point = haltonsequence.nextPoint();

 	//count points inside/outside of the inscribed circle of the square
 	final double x = point[0] - 0.5;
 	final double y = point[1] - 0.5;
 	if (x*x + y*y > 0.25) {
 	numOutside++;
 	} else {
 	numInside++;
 	}

 	//report status
 	i++;
 	if (i % 1000 == 0) {
 	reporter.setStatus("Generated " + i + " samples.");
 	}
 	}

 	//output map results
 	out.collect(new BooleanWritable(true), new LongWritable(numInside));
 	out.collect(new BooleanWritable(false), new LongWritable(numOutside));
 	}
 	}


	//Reducer class for Pi estimation.
	//Accumulate points inside/outside results from the mappers.
 	public static class PiReducer extends MapReduceBase
 	implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

 	private long numInside = 0;
 	private long numOutside = 0;
 	private JobConf conf; //configuration for accessing the file system

	//Store job configuration.
 	@Override
 	public void configure(JobConf job) {
 	conf = job;
 	}


	// Accumulate number of points inside/outside results from the mappers.
	// @param isInside Is the points inside?
	// @param values An iterator to a list of point counts
	// @param output dummy, not used here.
	// @param reporter

 	public void reduce(BooleanWritable isInside,
 	Iterator<LongWritable> values,
 	OutputCollector<WritableComparable<?>, Writable> output,
 	Reporter reporter) throws IOException {
 	if (isInside.get()) {
 	for(; values.hasNext(); numInside += values.next().get());
 	} else {
 	for(; values.hasNext(); numOutside += values.next().get());
 	}
 	}

 	//Reduce task done, write output to a file.
 	@Override
 	public void close() throws IOException {
 	//write output to a file
 	Path outDir = new Path(TMP_DIR, "out");
 	Path outFile = new Path(outDir, "reduce-out");
 	FileSystem fileSys = FileSystem.get(conf);
 	SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
 	outFile, LongWritable.class, LongWritable.class,
 	CompressionType.NONE);
 	writer.append(new LongWritable(numInside), new LongWritable(numOutside));
 	writer.close();
 	}
 	}

	//Run a map/reduce job for estimating Pi.
	//@return the estimated value of Pi.
 	public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
 	)
 	throws IOException {
 	//setup job conf
 	jobConf.setJobName(PiEstimator.class.getSimpleName());

 	jobConf.setInputFormat(SequenceFileInputFormat.class);

 	jobConf.setOutputKeyClass(BooleanWritable.class);
 	jobConf.setOutputValueClass(LongWritable.class);
 	jobConf.setOutputFormat(SequenceFileOutputFormat.class);

 	jobConf.setMapperClass(PiMapper.class);
 	jobConf.setNumMapTasks(numMaps);

 	jobConf.setReducerClass(PiReducer.class);
 	jobConf.setNumReduceTasks(1);

 	// turn off speculative execution, because DFS doesn't handle
 	// multiple writers to the same file.
 	jobConf.setSpeculativeExecution(false);

 	//setup input/output directories
 	final Path inDir = new Path(TMP_DIR, "in");
 	final Path outDir = new Path(TMP_DIR, "out");
 	FileInputFormat.setInputPaths(jobConf, inDir);
 	FileOutputFormat.setOutputPath(jobConf, outDir);

 	final FileSystem fs = FileSystem.get(jobConf);
 	if (fs.exists(TMP_DIR)) {
	 throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
	 + " already exists. Please remove it first.");
	 }
	 if (!fs.mkdirs(inDir)) {
	 throw new IOException("Cannot create input directory " + inDir);
	 }

	 //generate an input file for each map task
	 try {
	 for(int i=0; i < numMaps; ++i) {
	 final Path file = new Path(inDir, "part"+i);
	 final LongWritable offset = new LongWritable(i * numPoints);
	 final LongWritable size = new LongWritable(numPoints);
	 final SequenceFile.Writer writer = SequenceFile.createWriter(
	 fs, jobConf, file,
	 LongWritable.class, LongWritable.class, CompressionType.NONE);
	 try {
	 writer.append(offset, size);
	 } finally {
	 writer.close();
	 }
	 System.out.println("Wrote input for Map #"+i);
	 }

	 //start a map/reduce job
	 System.out.println("Starting Job");
	 final long startTime = System.currentTimeMillis();
	 JobClient.runJob(jobConf);
	 final double duration = (System.currentTimeMillis() - startTime)/1000.0;
	 System.out.println("Job Finished in " + duration + " seconds");

	 //read outputs
	 Path inFile = new Path(outDir, "reduce-out");
	 LongWritable numInside = new LongWritable();
	 LongWritable numOutside = new LongWritable();
	 SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
	 try {
	 reader.next(numInside, numOutside);
	 } finally {
	 reader.close();
	 }

	 //compute estimated value
	 return BigDecimal.valueOf(4).setScale(20)
	 .multiply(BigDecimal.valueOf(numInside.get()))
	 .divide(BigDecimal.valueOf(numMaps))
	 .divide(BigDecimal.valueOf(numPoints));
	 } finally {
	 fs.delete(TMP_DIR, true);
	 }
	 }

	//Parse arguments and then runs a map/reduce job.
	//Print output in standard out.
	//@return a non-zero if there is an error. Otherwise, return 0.
	 public int run(String[] args) throws Exception {
	 if (args.length != 2) {
	 System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
	 ToolRunner.printGenericCommandUsage(System.err);
	 return -1;
	 }

	 final int nMaps = Integer.parseInt(args[0]);
	 final long nSamples = Long.parseLong(args[1]);

	 System.out.println("Number of Maps = " + nMaps);
	 System.out.println("Samples per Map = " + nSamples);

	 final JobConf jobConf = new JobConf(getConf(), getClass());
	 System.out.println("Estimated value of Pi is "
	 + estimate(nMaps, nSamples, jobConf));
	 return 0;
	 }

	 //main method for running it as a stand alone command.
	 public static void main(String[] argv) throws Exception {
	 System.exit(ToolRunner.run(null, new PiEstimator(), argv));
	 }
	 }
	 
## Anexo D: código fuente de graysort de 10 GB

En esta sección se analiza el código del programa de MapReduce de TeraSort.


	/**
	 * Licensed to the Apache Software Foundation (ASF) under one
	 * or more contributor license agreements.  See the NOTICE file
	 * distributed with this work for additional information
	 * regarding copyright ownership.  The ASF licenses this file
	 * to you under the Apache License, Version 2.0 (the
	 * "License"); you may not use this file except in compliance
	 * with the License.  You may obtain a copy of the License at
	 *
	 *     http://www.apache.org/licenses/LICENSE-2.0
	 *
	 * Unless required by applicable law or agreed to in writing, software
	 * distributed under the License is distributed on an "AS IS" BASIS,
	 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
	 * See the License for the specific language governing permissions and
	 * limitations under the License.
	 */

	package org.apache.hadoop.examples.terasort;

	import java.io.IOException;
	import java.io.PrintStream;
	import java.net.URI;
	import java.util.ArrayList;
	import java.util.List;

	import org.apache.commons.logging.Log;
	import org.apache.commons.logging.LogFactory;
	import org.apache.hadoop.conf.Configured;
	import org.apache.hadoop.filecache.DistributedCache;
	import org.apache.hadoop.fs.FileSystem;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.NullWritable;
	import org.apache.hadoop.io.SequenceFile;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapred.FileOutputFormat;
	import org.apache.hadoop.mapred.JobClient;
	import org.apache.hadoop.mapred.JobConf;
	import org.apache.hadoop.mapred.Partitioner;
	import org.apache.hadoop.util.Tool;
	import org.apache.hadoop.util.ToolRunner;

	/**
	 * Generates the sampled split points, launches the job,
	 * and waits for it to finish.
	 * <p>
	 * To run the program:
	 * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
	 */

	public class TeraSort extends Configured implements Tool {
	  private static final Log LOG = LogFactory.getLog(TeraSort.class);

	  /**
	   * A partitioner that splits text keys into roughly equal
	   * partitions in a global sorted order.
	   */

	  static class TotalOrderPartitioner implements Partitioner<Text,Text>{
	    private TrieNode trie;
	    private Text[] splitPoints;

	    /**
	     * A generic trie node
	     */
	    static abstract class TrieNode {
	      private int level;
	      TrieNode(int level) {
	        this.level = level;
	      }
	      abstract int findPartition(Text key);
	      abstract void print(PrintStream strm) throws IOException;
	      int getLevel() {
	        return level;
	      }
	    }

	    /**
	     * An inner trie node that contains 256 children based on the next
	     * character.
	     */
	    static class InnerTrieNode extends TrieNode {
	      private TrieNode[] child = new TrieNode[256];

	      InnerTrieNode(int level) {
	        super(level);
	      }
	      int findPartition(Text key) {
	        int level = getLevel();
	        if (key.getLength() <= level) {
	          return child[0].findPartition(key);
	        }
	        return child[key.getBytes()[level]].findPartition(key);
	      }
	      void setChild(int idx, TrieNode child) {
	        this.child[idx] = child;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int ch=0; ch < 255; ++ch) {
	          for(int i = 0; i < 2*getLevel(); ++i) {
	            strm.print(' ');
	          }
	          strm.print(ch);
	          strm.println(" ->");
	          if (child[ch] != null) {
	            child[ch].print(strm);
	          }
	        }
	      }
	    }

	    /**
	     * A leaf trie node that does string compares to figure out where the given
	     * key belongs between lower..upper.
	     */
	    static class LeafTrieNode extends TrieNode {
	      int lower;
	      int upper;
	      Text[] splitPoints;
	      LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
	        super(level);
	        this.splitPoints = splitPoints;
	        this.lower = lower;
	        this.upper = upper;
	      }
	      int findPartition(Text key) {
	        for(int i=lower; i<upper; ++i) {
	          if (splitPoints[i].compareTo(key) >= 0) {
	            return i;
	          }
	        }
	        return upper;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int i = 0; i < 2*getLevel(); ++i) {
	          strm.print(' ');
	        }
	        strm.print(lower);
	        strm.print(", ");
	        strm.println(upper);
	      }
	    }


	    /**
	     * Read the cut points from the given sequence file.
	     * @param fs the file system
	     * @param p the path to read
	     * @param job the job config
	     * @return the strings to split the partitions on
	     * @throws IOException
	     */
	    private static Text[] readPartitions(FileSystem fs, Path p,
	                                         JobConf job) throws IOException {
	      SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
	      List<Text> parts = new ArrayList<Text>();
	      Text key = new Text();
	      NullWritable value = NullWritable.get();
	      while (reader.next(key, value)) {
	        parts.add(key);
	        key = new Text();
	      }
	      reader.close();
	      return parts.toArray(new Text[parts.size()]);  
	    }

	    /**
	     * Given a sorted set of cut points, build a trie that will find the correct
	     * partition quickly.
	     * @param splits the list of cut points
	     * @param lower the lower bound of partitions 0..numPartitions-1
	     * @param upper the upper bound of partitions 0..numPartitions-1
	     * @param prefix the prefix that we have already checked against
	     * @param maxDepth the maximum depth we will build a trie for
	     * @return the trie node that will divide the splits correctly
	     */
	    private static TrieNode buildTrie(Text[] splits, int lower, int upper,
	                                      Text prefix, int maxDepth) {
	      int depth = prefix.getLength();
	      if (depth >= maxDepth || lower == upper) {
	        return new LeafTrieNode(depth, splits, lower, upper);
	      }
	      InnerTrieNode result = new InnerTrieNode(depth);
	      Text trial = new Text(prefix);
	      // append an extra byte on to the prefix
	      trial.append(new byte[1], 0, 1);
	      int currentBound = lower;
	      for(int ch = 0; ch < 255; ++ch) {
	        trial.getBytes()[depth] = (byte) (ch + 1);
	        lower = currentBound;
	        while (currentBound < upper) {
	          if (splits[currentBound].compareTo(trial) >= 0) {
	            break;
	          }
	          currentBound += 1;
	        }
	        trial.getBytes()[depth] = (byte) ch;
	        result.child[ch] = buildTrie(splits, lower, currentBound, trial,
	                                     maxDepth);
	      }
	      // pick up the rest
	      trial.getBytes()[depth] = 127;
	      result.child[255] = buildTrie(splits, currentBound, upper, trial,
	                                    maxDepth);
	      return result;
	    }

	    public void configure(JobConf job) {
	      try {
	        FileSystem fs = FileSystem.getLocal(job);
	        Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
	        splitPoints = readPartitions(fs, partFile, job);
	        trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
	      } catch (IOException ie) {
	        throw new IllegalArgumentException("can't read paritions file", ie);
	      }
	    }

	    public TotalOrderPartitioner() {
	    }

	    public int getPartition(Text key, Text value, int numPartitions) {
	      return trie.findPartition(key);
	    }

	  }

	  public int run(String[] args) throws Exception {
	    LOG.info("starting");
	    JobConf job = (JobConf) getConf();
	    Path inputDir = new Path(args[0]);
	    inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
	    Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
	    URI partitionUri = new URI(partitionFile.toString() +
	                               "#" + TeraInputFormat.PARTITION_FILENAME);
	    TeraInputFormat.setInputPaths(job, new Path(args[0]));
	    FileOutputFormat.setOutputPath(job, new Path(args[1]));
	    job.setJobName("TeraSort");
	    job.setJarByClass(TeraSort.class);
	    job.setOutputKeyClass(Text.class);
	    job.setOutputValueClass(Text.class);
	    job.setInputFormat(TeraInputFormat.class);
	    job.setOutputFormat(TeraOutputFormat.class);
	    job.setPartitionerClass(TotalOrderPartitioner.class);
	    TeraInputFormat.writePartitionFile(job, partitionFile);
	    DistributedCache.addCacheFile(partitionUri, job);
	    DistributedCache.createSymlink(job);
	    job.setInt("dfs.replication", 1);
	    TeraOutputFormat.setFinalSync(job, true);
	    JobClient.runJob(job);
	    LOG.info("done");
	    return 0;
	  }

	  /**
	   * @param args
	   */

	  public static void main(String[] args) throws Exception {
	    int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
	    System.exit(res);
	  }
	}














 




[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: powershell-install-configure.md

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: #hdinsight-sample-10gb-graysort
[hdinsight-sample-csharp-streaming]: #hdinsight-sample-csharp-streaming
[hdinsight-sample-pi-estimator]: #hdinsight-sample-pi-estimator
[hdinsight-sample-wordcount]: #hdinsight-sample-wordcount

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline

<!---HONumber=AcomDC_0224_2016-->