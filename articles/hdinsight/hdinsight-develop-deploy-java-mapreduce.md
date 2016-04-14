<properties
	pageTitle="Desarrollo de programas MapReduce de Java para Hadoop | Microsoft Azure"
	description="Aprenda a desarrollar programas de MapReduce de Java en el emulador de HDInsight y a implementarlos en HDInsight."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="nitinme"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="07/11/2015"
	ms.author="nitinme"/>

# Desarrollo de programas MapReduce de Java para Hadoop en HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-maven-mapreduce-selector.md)]

Este tutorial le guiará por un escenario integral para desarrollar un trabajo de recuento de palabras de MapReduce de Hadoop en Java con Apache Maven. Asimismo, le mostrará cómo probar la aplicación en el emulador de HDInsight de Azure e implementarla y ejecutarla en un clúster de HDInsight basado en Windows.

##<a name="prerequisites"></a>Requisitos previos

Antes de empezar este tutorial, debe haber completado lo siguiente:

- Instalación del emulador de HDInsight Para obtener más información, consulte [Introducción al emulador de HDInsight][hdinsight-emulator]. Asegúrese de que todos los servicios necesarios estén en ejecución. En el equipo que tiene instalado el emulador de HDInsight, inicie la línea de comandos de Hadoop desde el acceso directo del escritorio, vaya a **C:\\hdp**, y ejecute el comando **start\_local\_hdp\_services.cmd**.
- Instale Azure PowerShell en el equipo emulador. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].
- Instale Java platform JDK 7 o superior en el equipo emulador. Este está ya disponible en el equipo emulador.
- Instale y configure [Apache Maven](http://maven.apache.org/).
- Obtenga una suscripción de Azure. Para obtener instrucciones, consulte [Opciones de compra][azure-purchase-options], [Ofertas para miembros][azure-member-offers] o [Prueba gratuita][azure-free-trial].


##<a name="develop"></a>Uso de Apache Maven para crear un programa de MapReduce en Java

Cree una aplicación de recuento de palabras de MapReduce. Se trata de una aplicación sencilla que cuenta las repeticiones de cada palabra en un conjunto de entrada determinado. En esta sección realizaremos las siguientes tareas:

1. Creación de un proyecto con Apache Maven
2. Actualización del modelo de objetos de proyectos (POM)
3. Creación de una aplicación de recuento de palabras de MapReduce
4. Compilación y empaquetado de la aplicación

**Para crear un proyecto con Maven**

1. Cree un directorio **C:\\Tutorials\\WordCountJava**.
2. Desde la línea de comandos de su entorno de desarrollo, cambie los directorios a la ubicación que ha creado.
3. Use el comando __mvn__, que se instala con Maven, para generar el scaffolding del proyecto.

		mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	Se creará un nuevo directorio en el directorio actual, con el nombre especificado por el parámetro __artifactID__ (**wordcountjava** en este ejemplo). Este directorio contendrá los siguientes elementos:

	* __pom.xml__: el [modelo de objetos de proyectos (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) contiene la información y los detalles de configuración para compilar el proyecto.

	* __src__: directorio que contiene el directorio __main\\java\\org\\apache\\hadoop\\examples__, donde creará la aplicación.
3. Elimine el archivo __src\\test\\java\\org\\apache\\hadoop\\examples\\apptest.java__, puesto que no se usará en este ejemplo.

**Para actualizar POM**

1. Edite el archivo __pom.xml__ y agregue lo siguiente dentro de la sección `<dependencies>`:

		<dependency>
		  <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
        </dependency>
    	<dependency>
      	  <groupId>org.apache.hadoop</groupId>
      	  <artifactId>hadoop-mapreduce-client-common</artifactId>
      	  <version>2.5.1</version>
    	</dependency>
    	<dependency>
      	  <groupId>org.apache.hadoop</groupId>
      	  <artifactId>hadoop-common</artifactId>
      	  <version>2.5.1</version>
    	</dependency>

	Esto indica a Maven que el proyecto requiere bibliotecas (enumeradas en <artifactId>) de una versión específica (enumerada en <version>). En el momento de compilación, esto se descargará desde el repositorio de Maven predeterminado. Puede usar la [búsqueda del repositorio de Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) para ver más información.

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
  		  </plugins>
	    </build>

	Esta acción configura el [complemento Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/), que se usa para impedir la duplicación de licencias en el archivo JAR que compila Maven. La razón de usar este complemento es que los archivos de licencia duplicados pueden provocar un error en tiempo de ejecución en el clúster de HDInsight. El uso del complemento Maven Shade con la implementación de `ApacheLicenseResourceTransformer` evita este error.

	El complemento Maven Shade también producirá un uberjar (en ocasiones denominado fatjar), que contiene todas las dependencias que necesita la aplicación.

3. Guarde el archivo __pom.xml__.

**Para crear la aplicación de recuento de palabras**

1. Vaya al directorio __wordcountjava\\src\\main\\java\\org\\apache\\hadoop\\examples__ y cambie el nombre del archivo __app.java__ por __WordCount.java__.
2. Abra el Bloc de notas.
2. Copie y pegue el siguiente programa en el Bloc de notas:

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

**Para compilar y empaquetar la aplicación**

1. Abra un símbolo del sistema y cambie los directorios por el directorio __wordcountjava__.

2. Use el siguiente comando para compilar un archivo JAR que contenga la aplicación:

		mvn clean package

	Esta acción eliminará los artefactos de compilación anteriores, descargará las dependencias que no se hayan instalado aún y, a continuación, compilará y empaquetará la aplicación.

3. Cuando el comando termine de ejecutarse, el directorio __wordcountjava\\target__ contendrá un archivo llamado __wordcountjava-1.0-SNAPSHOT.jar__.

	> [AZURE.NOTE] El archivo __wordcountjava-1.0-SNAPSHOT.jar__ es un uberjar.


##<a name="test"></a>Prueba del programa en el emulador

La prueba del trabajo de MapReduce en el emulador de HDInsight incluye los procedimientos siguientes:

1. Carga de los archivos de datos en el sistema de archivos distribuido de Hadoop (HDFS) en el emulador
2. Creación de un grupo de usuarios local
3. Ejecución de un trabajo de recuento de palabras de MapReduce
4. Recuperación de los resultados del trabajo

De forma predeterminada, el emulador de HDInsight utiliza HDFS como sistema de archivos. Opcionalmente, puede configurar el emulador de HDInsight para utilizar el almacenamiento de blobs de Azure. Para obtener más información, consulte [Introducción al emulador de HDInsight][hdinsight-emulator-wasb].

En este tutorial, utilizará el comando **copyFromLocal** de HDFS para cargar los archivos de datos en HDFS. La siguiente sección muestra cómo cargar archivos en el almacenamiento de blobs de Azure con Azure PowerShell. Para obtener información acerca de otros métodos para cargar archivos en el almacenamiento de blobs de Azure, consulte [Carga de datos en HDInsight][hdinsight-upload-data].

Este tutorial utiliza la siguiente estructura de carpetas de HDFS:

Carpeta|Nota:
---|---
/WordCount|La carpeta raíz para el proyecto de recuento de palabras. 
/WordCount/Apps|La carpeta para los ejecutables de los programas asignador y reductor.
/WordCount/Input|La carpeta de los archivos de origen de MapReduce.
/WordCount/Output|La carpeta de los archivos de resultados de MapReduce.
/WordCount/MRStatusOutput|La carpeta de resultados del trabajo.


Este tutorial utiliza los archivos .txt ubicados en el directorio %hadoop\_home% como archivos de datos.

> [AZURE.NOTE] Los comandos HDFS de Hadoop distinguen mayúsculas de minúsculas.

**Para copiar los archivos de datos al HDFS del emulador**

1. Abra la línea de comandos de Hadoop desde el escritorio. La línea de comandos de Hadoop la instala el instalador del emulador.
2. Desde la ventana de la línea de comandos de Hadoop, ejecute el comando siguiente para crear un directorio para los archivos de entrada:

		hadoop fs -mkdir /WordCount
		hadoop fs -mkdir /WordCount/Input

	La ruta de acceso utilizada aquí es la ruta de acceso relativa. Es equivalente a la siguiente:

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3. Ejecute el comando siguiente para copiar algunos archivos de texto en la carpeta de entrada en HDFS:

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common*.txt /WordCount/Input

	El trabajo de MapReduce contará las palabras de estos archivos.

4. Ejecute el comando siguiente para enumerar y comprobar los archivos cargados:

		hadoop fs -ls /WordCount/Input

**Para crear un grupo de usuarios local**

Para ejecutar correctamente el trabajo de MapReduce en el clúster, debe crear un grupo de usuarios denominado hdfs. A este grupo, debe agregar también un usuario denominado hadoop y el usuario local con el que inicia sesión en el emulador. Use los siguientes comandos desde un símbolo del sistema con privilegios elevados:

		# Add a user group called hdfs
		net localgroup hdfs /add

		# Adds a user called hadoop to the group
		net localgroup hdfs hadoop /add

		# Adds the local user to the group
		net localgroup hdfs <username> /add

**Para ejecutar el trabajo de MapReduce mediante la línea de comandos de Hadoop**

1. Abra la línea de comandos de Hadoop desde el escritorio.
2. Ejecute el siguiente comando para eliminar la estructura de carpetas /WordCount/Output de HDFS. /WordCount/Output es la carpeta de salida del trabajo de MapReduce de recuento de palabras. El trabajo de MapReduce producirá un error si la carpeta ya existe. Este paso es necesario si es la segunda vez que ejecuta el trabajo.

		hadoop fs -rm - r /WordCount/Output

2. Ejecute el siguiente comando:

		hadoop jar C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

	Si el trabajo finaliza satisfactoriamente, debería obtener un resultado similar al de la captura de pantalla siguiente:

	![HDI.EMulator.WordCount.Run][image-emulator-wordcount-run]

	En la captura de pantalla, puede ver tanto la asignación como la reducción finalizadas al 100 %. También muestra el identificador del trabajo. El mismo informe se puede recuperar abriendo el acceso directo **Hadoop MapReduce status** en el escritorio y buscando dicho identificador del trabajo.

La otra opción para ejecutar un trabajo de MapReduce es utilizar Azure PowerShell. Para obtener instrucciones, consulte [Introducción al emulador de HDInsight][hdinsight-emulator].

**Para mostrar el resultado de HDFS**

1. Abra la línea de comandos de Hadoop.
2. Ejecute los comandos siguientes para ver el resultado:

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-r-00000

	Puede anexar "|more" al final del comando para obtener la vista de la página. O bien, utilice el comando **findstr** para encontrar un patrón de cadena:

		hadoop fs -cat /WordCount/Output/part-r-00000 | findstr "there"

Ha desarrollado un trabajo de MapReduce para el recuento de palabras y lo ha probado correctamente en el emulador. El paso siguiente es implementarlo y ejecutarlo en HDInsight de Azure.



##<a id="upload"></a>Carga de los datos y la aplicación en el almacenamiento de blobs de Azure
HDInsight de Azure utiliza el almacenamiento de blobs de Azure para almacenar datos. Cuando se aprovisiona un clúster de HDInsight, se utiliza un contenedor de almacenamiento de blobs de Azure para almacenar archivos del sistema. Puede usar este contenedor predeterminado u otro diferente (ya sea en la misma cuenta de almacenamiento de Azure o en otra cuenta de almacenamiento ubicada en la misma región que el clúster) para almacenar los archivos de datos.

En este tutorial, creará un contenedor en una cuenta de almacenamiento independiente para los archivos de datos y la aplicación de MapReduce. Los archivos de datos son los archivos de texto del directorio **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common** en su estación de trabajo del emulador.

**Para crear una cuenta de almacenamiento de blobs y un contenedor**

1. Abra Azure PowerShell.
2. Configure las variables y, a continuación, ejecute los siguientes comandos:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"
		$location = "<Region>"  # For example, "East US"

	La variable **$subscripionName** está asociada a la suscripción de Azure. Debe nombrar los valores **$storageAccountName\\_Data** y **$containerName\\_Data**. Para las restricciones de nomenclatura, consulte [Asignación de nombres y referencias a contenedores, blobs y metadatos](http://msdn.microsoft.com/library/windowsazure/dd135715.aspx).

3. Ejecute los comandos siguientes para crear una cuenta de almacenamiento y un contenedor de almacenamiento de blobs en la cuenta:

		# Select an Azure subscription
		Select-AzureSubscription $subscriptionName

		# Create a Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location

		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4. Ejecute los comandos siguientes para comprobar la cuenta de almacenamiento y el contenedor:

		Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
		Get-AzureStorageContainer -Context $destContext

**Para cargar los archivos de datos**

1. Abra Azure PowerShell.
2. Configure las tres primeras variables y, a continuación, ejecute los siguientes comandos:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	Las variables **$storageAccountName\\_Data** y **$containerName\\_Data** son iguales a las definidas en el último procedimiento.

	Tenga en cuenta que la carpeta de los archivos de origen es **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT** y la carpeta de destino es **WordCount/Input**.

3. Ejecute los siguientes comandos para obtener una lista de los archivos .txt de la carpeta de archivos de origen:

		# Get a list of the .txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4. Ejecute los comandos siguientes para crear un objeto de contexto de almacenamiento:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. Ejecute los comandos siguientes para copiar los archivos:

		# Copy the files from the local workstation to the Blob container
		foreach ($file in $filesTxt){

		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"

		    write-host "Copying $fileName to $blobName"

		    Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
		}

6. Ejecute los comandos siguientes para incluir los archivos cargados:

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded data files:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

	Debería ver los archivos de datos de texto cargados.

**Para cargar la aplicación de recuento de palabras**

1. Abra Azure PowerShell.
2. Configure las tres primeras variables y, a continuación, ejecute los siguientes comandos:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$jarFile = "C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar"
		$blobFolder = "WordCount/jars"

	Las variables **$storageAccountName\\_Data** y **$containerName\\_Data** son las mismas que ha definido en el último procedimiento, lo que significa que cargará tanto el archivo de datos como la aplicación en el mismo contenedor y en la misma cuenta de almacenamiento.

	Tenga en cuenta que la carpeta de destino es **WordCount/jars**.

4. Ejecute los comandos siguientes para crear un objeto de contexto de almacenamiento:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. Ejecute el comando siguiente para copiar las aplicaciones:

		Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

6. Ejecute los comandos siguientes para incluir los archivos cargados:

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded application file:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

	Debería ver aquí el archivo JAR.

##<a name="run"></a>Ejecución del trabajo de MapReduce en HDInsight de Azure

En esta sección, creará un script de Azure PowerShell que realiza las siguientes tareas:

1. Aprovisionamientos de un clúster de HDInsight

	1. Creación de una cuenta de almacenamiento que se utilizará como sistema de archivos predeterminado del clúster de HDInsight
	2. Creación de un contenedor de almacenamiento de blobs
	3. Creación de un clúster de HDInsight

2. Envío del trabajo de MapReduce

	1. Creación de una definición de trabajo de MapReduce
	2. Envío de un trabajo de MapReduce
	3. Espera para que finalice el trabajo
	4. Visualización del error estándar
	5. Visualización del resultado estándar

3. Eliminación del clúster

	1. Eliminación del clúster de HDInsight
	2. Eliminación de la cuenta de almacenamiento usada como sistema de archivos predeterminado del clúster de HDInsight


**Para ejecutar el script de Azure PowerShell**

1. Abra el Bloc de notas.
2. Copie y pegue el código siguiente:

		# The Storage account and the HDInsight cluster variables
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"
		$location = "<Region>"     ### Must match the data Storage account location
		$clusterNodes = <NumberOFNodesInTheCluster>

		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"

		$clusterName = $stringPrefix + "hdicluster"

		$storageAccountName_Default = $stringPrefix + "hdistore"
		$containerName_Default =  $stringPrefix + "hdicluster"

		# The MapReduce job variables
		$jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
		$className = "org.apache.hadoop.examples.WordCount"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

		# Create a PSCredential object. The user name and password are hardcoded here. You can change them if you want.
		$password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ("Admin", $password)

		Select-AzureSubscription $subscriptionName

		#=============================
		# Create a Storage account used as the default file system
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

		#=============================
		# Create a Blob storage container used as the default file system
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default

		New-AzureStorageContainer -Name $containerName_Default -Context $destContext

		#=============================
		# Create an HDInsight cluster
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }

		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data

		New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $creds -Config $config

		#=============================
		# Create a MapReduce job definition
		Write-Host "Create a MapReduce job definition" -ForegroundColor Green
		$mrJobDef = New-AzureHDInsightMapReduceJobDefinition -JobName mrWordCountJob  -JarFile $jarFile -ClassName $className -Arguments $mrInput, $mrOutput -StatusFolder /WordCountStatus

		#=============================
		# Run the MapReduce job
		Write-Host "Run the MapReduce job" -ForegroundColor Green
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600

		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput

		#=============================
		# Delete the HDInsight cluster
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Remove-AzureHDInsightCluster -Name $clusterName  

		# Delete the default file system Storage account
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. Establezca las seis primeras variables en el script. La variable **$stringPrefix** se usa para agregar la cadena especificada como prefijo al nombre del clúster de HDInsight, el nombre de la cuenta de almacenamiento y el nombre del contenedor de almacenamiento de blobs. Puesto que estos nombres deben tener entre 3 y 24 caracteres, asegúrese de que la cadena que especifique y los nombres que use el script no superen, en conjunto, el límite de caracteres del nombre. Solo debe utilizar minúsculas en **$stringPrefix**.

	Las variables **$storageAccountName\\_Data** y **$containerName\\_Data** constituyen la cuenta de almacenamiento y el contenedor que se utilizan para almacenar los archivos de datos y la aplicación. La variable **$location** debe coincidir con la ubicación de la cuenta de almacenamiento de datos.

4. Revise el resto de las variables.
5. Guarde el archivo de script.
6. Abra Azure PowerShell.
7. Ejecute el comando siguiente para establecer la directiva de ejecución en RemoteSigned:

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. Cuando se le solicite, escriba el nombre de usuario y la contraseña para el clúster de HDInsight. Como eliminará el clúster al final del script y no necesitará más el nombre de usuario y la contraseña, estos pueden ser cualquier cadena. Si no desea que se le soliciten las credenciales, consulte [Trabajo con contraseñas, cadenas seguras y credenciales en Windows PowerShell][powershell-PSCredential].


##<a name="retrieve"></a>Recuperación del resultado del trabajo de MapReduce
En esta sección se muestra cómo descargar y mostrar los resultados. Para obtener más información sobre cómo mostrar los resultados en Excel, consulte [Conexión de Excel a HDInsight con Microsoft Hive ODBC Driver][hdinsight-ODBC] y [Conexión de Excel a HDInsight con Power Query][hdinsight-power-query].


**Para recuperar los resultados**

1. Abra la ventana de Azure PowerShell.
2. Cambie el directorio a **C:\\Tutorials\\WordCountJava**. La carpeta predeterminada de Azure PowerShell es **C:\\Windows\\System32\\WindowsPowerShell\\v1.0**. Los cmdlets que ejecutará descargarán el archivo de resultados a la carpeta actual. No tiene permiso para descargar los archivos en las carpetas del sistema.
2. Ejecute los siguientes comandos para establecer los valores:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-r-00000"

3. Ejecute los siguientes comandos para crear un objeto de contexto de almacenamiento de Azure:

		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

4. Ejecute los comandos siguientes para descargar y mostrar el resultado:

		Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

Después de finalizar el trabajo, puede exportar los datos a una base de datos SQL Server o SQL de Azure usando [Sqoop][hdinsight-use-sqoop], así como exportar los datos a Excel.

##<a id="nextsteps"></a>Pasos siguientes
En este tutorial, ha aprendido a desarrollar un trabajo de MapReduce de Java, a probar la aplicación en el emulador de HDInsight y a escribir un script de Azure PowerShell para aprovisionar un clúster de HDInsight y ejecutar un trabajo de MapReduce en el clúster. Para obtener más información, consulte los artículos siguientes:

- [Desarrollo de programas de MapReduce de streaming de Hadoop C# para HDInsight][hdinsight-develop-streaming]
- [Introducción a HDInsight de Azure][hdinsight-get-started]
- [Introducción al emulador de HDInsight][hdinsight-emulator]
- [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage]
- [Administración de HDInsight con Azure PowerShell][hdinsight-admin-powershell]
- [Carga de datos en HDInsight][hdinsight-upload-data]
- [Uso de Hive con HDInsight][hdinsight-use-hive]
- [Uso de Pig con HDInsight][hdinsight-use-pig]
- [Conexión de Excel a HDInsight con Power Query][hdinsight-power-query]
- [Conexión de Excel a HDInsight con Microsoft Hive ODBC Driver][hdinsight-ODBC]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-emulator]: ../hdinsight-get-started-emulator.md
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator.md#blobstorage
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[powershell-install-configure]: powershell-install-configure.md



[image-emulator-wordcount-compile]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png
[image-emulator-wordcount-run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png

<!---HONumber=AcomDC_0218_2016-->