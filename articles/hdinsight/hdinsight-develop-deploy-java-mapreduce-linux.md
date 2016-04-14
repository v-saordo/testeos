<properties
	pageTitle="Desarrollo de programas MapReduce de Java para HDInsight basado en Linux | Microsoft Azure"
	description="Aprenda a desarrollar programas de MapReduce de Java y a implementarlos en HDInsight basado en Linux."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="larryfr"/>

# Desarrollo de programas MapReduce de Java para Hadoop en HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-maven-mapreduce-selector.md)]

Estos documentos le ayudarán a usar Apache Maven para crear una aplicación de MapReduce e implementarla y ejecutarla en el marco de trabajo basado en Linux Hadoop, en el clúster de HDInsight. Para obtener información sobre el uso de un clúster Hadoop basado en Windows en HDInsight, vea [Desarrollo de programas MapReduce de Java para Hadoop en HDInsight (Windows)](hdinsight-develop-deploy-java-mapreduce.md)

##<a name="prerequisites"></a>Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 o posterior (o un equivalente como OpenJDK)

- [Apache Maven](http://maven.apache.org/)

- **Una suscripción de Azure**

- **Azure CLI**: para obtener más información, consulte [Instalación y configuración de Azure CLI](../xplat-cli-install.md)

##Configuración de las variables de entorno

Pueden establecer las siguientes variables de entorno al instalar Java y el JDK. Sin embargo, debe comprobar que existen y que contienen los valores correctos para su sistema.

* **JAVA\_HOME**: debe apuntar al directorio donde está instalado Java Runtime Environment (JRE). Por ejemplo, en un sistema OS X, Unix o Linux, debe tener un valor similar a `/usr/lib/jvm/java-7-oracle`. En Windows, tendría un valor similar a `c:\Program Files (x86)\Java\jre1.7`.

* **PATH**: debe contener las rutas de acceso siguientes:

	* **JAVA\_HOME** (o la ruta de acceso equivalente).

	* **JAVA\_HOME\\bin** (o la ruta de acceso equivalente).

	* El directorio donde está instalado Maven

##Creación de un nuevo proyecto de Maven

1. Desde una sesión de terminal o la línea de comandos de su entorno de desarrollo, cambie los directorios por la ubicación en la cual desea guardar el proyecto.

3. Use el comando __mvn__, que se instala con Maven, para generar el scaffolding del proyecto.

		mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	Se creará un nuevo directorio en el directorio actual, con el nombre especificado por el parámetro __artifactID__ (**wordcountjava** en este ejemplo). Este directorio contendrá los siguientes elementos:

	* __pom.xml__: el [modelo de objetos de proyectos (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) contiene la información y los detalles de configuración para compilar el proyecto.

	* __src__: directorio que contiene el directorio __main/java/org/apache/hadoop/examples__, donde creará la aplicación.

3. Elimine el archivo __src/test/java/org/apache/hadoop/examples/apptest.java__ puesto que no lo usará en este ejemplo.

##Adición de dependencias

1. Edite el archivo __pom.xml__ y agregue lo siguiente dentro de la sección `<dependencies>`:

		<dependency>
		  <groupId>org.apache.hadoop</groupId>
	      <artifactId>hadoop-mapreduce-examples</artifactId>
	      <version>2.5.1</version>
		  <scope>provided</scope>
	    </dependency>
		<dependency>
	  	  <groupId>org.apache.hadoop</groupId>
	  	  <artifactId>hadoop-mapreduce-client-common</artifactId>
	  	  <version>2.5.1</version>
		  <scope>provided</scope>
		</dependency>
		<dependency>
	  	  <groupId>org.apache.hadoop</groupId>
	  	  <artifactId>hadoop-common</artifactId>
	  	  <version>2.5.1</version>
		  <scope>provided</scope>
		</dependency>

	Esto indica a Maven que el proyecto requiere bibliotecas (enumeradas en <artifactId>) de una versión específica (enumerada en <version>). En el momento de compilación, esto se descargará desde el repositorio de Maven predeterminado. Puede usar la [búsqueda del repositorio de Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) para ver más información.

	`<scope>provided</scope>` indica a Maven que no hace falta que empaquete esas dependencias con la aplicación, puesto que las proporcionará el clúster de HDInsight en tiempo de ejecución.

2. Agregue lo siguiente al archivo __pom.xml__. Esto debe estar dentro de las etiquetas `<project>...</project>` en el archivo; por ejemplo, entre `</dependencies>` y `</project>`.

		<build>
  		  <plugins>
    		<plugin>
      		  <groupId>org.apache.maven.plugins</groupId>
      		  <artifactId>maven-shade-plugin</artifactId>
      		  <version>2.3</version>
      		  <configuration>
        		<transformers>
          		  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		          </transformer>
        		</transformers>
      		  </configuration>
      		  <executions>
        		<execution>
          		  <phase>package</phase>
          			<goals>
            		  <goal>shade</goal>
          			</goals>
        	    </execution>
      		  </executions>
      	    </plugin>
			<plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
  		  </plugins>
	    </build>

	El primer complemento configura el [complemento Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/) que se usará para compilar un uberjar (en ocasiones denominado fatjar), que contiene todas las dependencias que necesita la aplicación. Asimismo, se evita que se dupliquen licencias en el paquete JAR, lo que suele causar problemas en algunos sistemas.

	El segundo complemento configura el compilador Maven, que se usa para establecer la versión de Java requerida por esta aplicación para la versión utilizada en el clúster de HDInsight.

3. Guarde el archivo __pom.xml__.

##Creación de la aplicación MapReduce

1. Vaya al directorio __wordcountjava\\src\\main\\java\\org\\apache\\hadoop\\examples__ y cambie el nombre del archivo __App.java__ por __WordCount.java__.

2. Abra el archivo __WordCount.java__ en un editor de texto y reemplace el contenido por lo siguiente:

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

	Tenga en cuenta que el nombre del paquete es **org.apache.hadoop.examples** y el nombre de clase es **WordCount**. Utilizará estos nombres cuando envíe el trabajo de MapReduce.

3. Guarde el archivo .

##Compilar la aplicación

1. Vaya al directorio __wordcountjava__ si no está en él.

2. Use el siguiente comando para compilar un archivo JAR que contenga la aplicación:

		mvn clean package

	Esta acción eliminará los artefactos de compilación anteriores, descargará las dependencias que no se hayan instalado aún y, a continuación, compilará y empaquetará la aplicación.

3. Cuando el comando termine de ejecutarse, el directorio __wordcountjava\\target__ contendrá un archivo llamado __wordcountjava-1.0-SNAPSHOT.jar__.

	> [AZURE.NOTE] El archivo __wordcountjava-1.0-SNAPSHOT.jar__ es un uberjar que contiene no sólo el trabajo WordCount, sino también las dependencias que el trabajo necesita en tiempo de ejecución.


##<a id="upload"></a>Carga del archivo .jar

Use el siguiente comando para cargar el archivo .jar al nodo principal de HDInsight:

	scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

	Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

De esta manera, se copiarán los archivos del sistema local al nodo principal.

> [AZURE.NOTE] Si usó una contraseña para proteger la cuenta SSH, se le preguntará la contraseña. Si usó una clave SSH, es posible que deba usar el parámetro `-i` y la ruta de acceso a la clave privada. Por ejemplo: `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

##<a name="run"></a>Ejecute el trabajo MapReduce

1. Conéctese a HDInsight con SSH, tal y como se describe en los siguientes artículos:

    - [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. En la sesión SSH, use el siguiente comando para ejecutar la aplicación MapReduce:

		yarn jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/wordcountout

	De este modo, se usará la aplicación WordCount MapReduce para contar las palabras del archivo davinci.txt y almacenar los resultados en \_\___wasb:///example/data/wordcountout__. Tanto el archivo de entrada como el de salida se almacenan en el almacenamiento predeterminado del clúster.

3. Una vez completado el trabajo, use lo siguiente para ver los resultados:

		hdfs dfs -cat wasb:///example/data/wordcountout/*

	Verá una lista de palabras y números con valores similares a los siguientes:

		zeal    1
		zelus   1
		zenith  2

##<a id="nextsteps"></a>Pasos siguientes

Gracias a este documento, ha aprendido a desarrollar un trabajo MapReduce de Java. Consulte los siguientes documentos para obtener información acerca de otras formas de trabajar con HDInsight.

- [Uso de Hive con HDInsight][hdinsight-use-hive]
- [Uso de Pig con HDInsight][hdinsight-use-pig]
- [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

Para más información, consulte también el [Centro de desarrolladores de Java](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md



[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

<!---HONumber=AcomDC_0211_2016-->