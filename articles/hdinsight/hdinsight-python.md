<properties
	pageTitle="Uso de Python con Hive y Pig en HDInsight | Microsoft Azure"
	description="Vea cómo usar funciones definidas por el usuario (UDF) de Python desde Hive y Pig en HDInsight, la pila de tecnología de Hadoop en Azure."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="02/10/2016" 
	ms.author="larryfr"/>

#Uso de Python con Hive y Pig en HDInsight

Hive y Pig resultan excelentes para trabajar con datos en HDInsight, pero en ocasiones se necesita un lenguaje con una finalidad más general. Tanto Hive como Pig le permiten crear funciones definidas por el usuario (UDF) mediante diversos lenguajes de programación. En este artículo, aprenderá a usar una UDF de Python desde Hive y Pig.

> [AZURE.NOTE] Los pasos de este artículo se aplican a las versiones de clúster de HDInsight 2.1, 3.0, 3.1 y 3.2.

##Requisitos

* Un clúster de HDInsight (Windows o Linux)

* Un editor de texto

    > [AZURE.IMPORTANT] Si está utilizando un servidor HDInsight basado en Linux, pero creará los archivos de Python en un cliente Windows, debe utilizar un editor que utilice LF como final de línea. Si no está seguro de si el editor utiliza LF o CRLF, consulte la sección [Solución de problemas](#troubleshooting) para conocer los pasos que debe realizar para quitar el carácter CR con las utilidades del clúster de HDInsight.
    
##<a name="python"></a>Python en HDInsight

Python2.7 se instala de forma predeterminada en los clústeres de HDInsight 3.0 y posteriores. Hive se puede usar con esta versión de Python para el procesamiento por secuencias (los datos se pasan entre Hive y Python mediante STDOUT/STDIN).

HDInsight incluye también Jython, que es una implementación de Python escrita en Java. Pig comprende cómo hablar con Jython sin tener que recurrir a la transmisión por secuencias, de modo que es preferible cuando se usa Pig.

###<a name="hivepython"></a>Hive y Python

Python se puede usar como UDF desde Hive a través de la instrucción **TRANSFORM** de HiveQL. Por ejemplo, el siguiente HiveQL invoca un script de Python almacenado en el archivo **streaming.py**.

**HDInsight basado en Linux**

	add file wasb:///streaming.py;

	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

**HDInsight basado en Windows**

	add file wasb:///streaming.py;

	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'D:\Python27\python.exe streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

> [AZURE.NOTE] En los clústeres de HDInsight basados en Windows, la cláusula **USING** debe especificar la ruta completa a python.exe. Esta es siempre `D:\Python27\python.exe`.

A continuación se muestra lo que hace este ejemplo:

1. La instrucción **add file** al comienzo del archivo agrega el archivo **streaming.py** a la caché distribuida, de modo que todos los nodos del clúster pueden acceder a él.

2. La instrucción **SELECT TRANSFORM ... USING** selecciona datos de **hivesampletable** y pasa clientid, devicemake y devicemodel al script **streaming.py**.

3. La cláusula **AS** describe los campos devueltos por **streaming.py**.

<a name="streamingpy"></a> Este es el archivo **streaming.py** usado en el ejemplo de HiveQL.

	#!/usr/bin/env python

	import sys
	import string
	import hashlib

	while True:
	  line = sys.stdin.readline()
	  if not line:
	    break

	  line = string.strip(line, "\n ")
	  clientid, devicemake, devicemodel = string.split(line, "\t")
	  phone_label = devicemake + ' ' + devicemodel
	  print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])

Dado que usamos la transmisión por secuencias, este script debe hacer lo siguiente:

1. Leer datos de STDIN. Para ello se usa `sys.stdin.readline()` en este ejemplo.

2. El carácter de nueva línea final se elimina con `string.strip(line, "\n ")`, dado que solo queremos los datos de texto y no el final del indicador de línea.

2. Al realizar el procesamiento por secuencias, una sola línea contiene todos los valores con un carácter de tabulación entre cada uno. Por tanto se puede usar `string.split(line, "\t")` para dividir la entrada en cada tabulación y que solo se devuelvan los campos.

3. Cuando el procesamiento haya finalizado, la salida se debe escribir en STDOUT como una sola línea, con una tabulación entre cada campo. Para ello se usa `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.

4. Todo esto tiene lugar dentro de un bucle `while`, que se repetirá hasta que no se lea ninguna `line`, momento en el cual `break` sale del bucle y termina el script.

Aparte de eso, el script simplemente concatena los valores de entrada de `devicemake`y `devicemodel`, y calcula un hash del valor concatenado. Es bastante sencillo, pero describe los aspectos básicos del funcionamiento de cualquier script de Python que se invoca desde la función should de Hive: bucle, entrada de lectura hasta que no hay nada más, introducción de salto de cada línea de entrada aparte en las tabulaciones, proceso, escritura de una línea única de salida delimitada por tabulaciones.

Consulte [Ejecución de los ejemplos](#running) para obtener información sobre cómo ejecutar este ejemplo en su clúster de HDInsight.

###<a name="pigpython"></a>Pig y Python

Un script de Python se puede usar como UDF desde Pig a través de la instrucción **GENERATE**. Por ejemplo, el siguiente ejemplo usa un script de Python almacenado en el archivo **jython.py**.

	Register 'wasb:///jython.py' using jython as myfuncs;
    LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

A continuación se muestra cómo funciona este ejemplo:

1. Registra el archivo que contiene el script de Python (**jython.py**), mediante **Jython**, y lo expone a Pig como **myfuncs**. Jython es una implementación de Python en Java, y se ejecuta en la misma máquina virtual Java que Pig. Esto nos permite tratar el script de Python como una llamada a función tradicional frente al enfoque de transmisión por secuencias usado con Hive.

2. La siguiente línea carga el archivo de datos de ejemplo **sample.log** en **LOGS**. Como este archivo de registro no tiene un esquema coherente, también define cada registro (en este caso **LINE**) como una **chararray**. Chararray es básicamente una cadena.

3. La tercera línea filtra los valores nulos y almacena el resultado de la operación en **LOG**.

4. A continuación, recorre en iteración los registros de **LOG** y usa **GENERATE** para invocar el método **create\_structure** contenido en el script **jython.py** cargado como **myfuncs**. **LINE** se usa para pasar el registro actual a la función.

5. Por último, los resultados se vuelcan en STDOUT con el comando **DUMP**. Esta acción se realiza para mostrar inmediatamente los resultados una vez que la operación ha finalizado; en un script real los datos se almacenarían (**STORE**) normalmente en un nuevo archivo.

<a name="jythonpy"></a> Este es el archivo **jython.py** usado en el ejemplo de Pig:

	@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
	def create_structure(input):
	  if (input.startswith('java.lang.Exception')):
	    input = input[21:len(input)] + ' - java.lang.Exception'
	  date, time, classname, level, detail = input.split(' ', 4)
	  return date, time, classname, level, detail

Recuerde que anteriormente hemos definido la entrada **LINE** como chararray porque no hay un esquema coherente para la entrada. Lo que **jython.py** hace es transformar los datos en un esquema coherente para la salida. Funciona de la siguiente manera:

1. La instrucción **@outputSchema** define el formato de los datos que se devolverán a Pig. En este caso es un **contenedor de datos**, que es un tipo de datos de Pig. El contenedor contiene los siguientes campos, los cuales son todos chararray (cadenas):

	* date: la fecha en la que se creó la entrada del registro
	* time: la hora a la que se creó la entrada del registro
	* classname: el nombre de la clase para la que se creó la entrada
	* level: el nivel de registro
	* detail: los detalles de la entrada del registro

2. A continuación, **def create\_structure(input)** define la función a la que Pig pasará elementos de línea.

3. Los datos de ejemplo, **sample.log**, se ajustan principalmente al esquema de date, time, classname, level y detail que queremos devolver. Pero también contiene unas cuantas líneas que comienzan por la cadena '*java.lang.Exception*' y que han de modificarse para que coincidan con el esquema. La instrucción **if** las busca, y luego modifica los datos de entrada para mover la cadena '*java.lang.Exception*' al final, de forma que pone los datos en línea con nuestro esquema de salida esperado.

4. A continuación, se usa el comando **split** para dividir los datos en los primeros cuatro caracteres de espacio. El resultado son cinco valores, que se asignan a **date**, **time**, **classname**, **level** y **detail**.

5. Finalmente, los valores se devuelven a Pig.

Es entonces cuando tendremos un esquema coherente tal y como se define en la instrucción **@outputSchema**.

##<a name="running"></a>Ejecución de los ejemplos

Si está usando un clúster de HDInsight basado en Linux, siga los pasos de **SSH** siguientes. Si usa un clúster de HDInsight basado en Windows y un cliente de Windows, siga los pasos de **PowerShell**.

###SSH

Para obtener más información sobre el uso de SSH, vea <a href="../hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">Usar SSH con Hadoop basado en Linux desde Linux, Unix u OS X</a> o <a href="../hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">Usar SSH con Hadoop basado en Linux desde Windows</a>.

1. Mediante los ejemplos de Python [streaming.py](#streamingpy) y [jython.py](#jythonpy), cree copias locales de los archivos en su máquina de desarrollo.

2. Use `scp` para copiar los archivos en el clúster de HDInsight. Por ejemplo, lo siguiente copiaría los archivos a un clúster denominado **mycluster**.

		scp streaming.py jython.py myuser@mycluster-ssh.azurehdinsight.net:

3. Use SSH para conectarse al clúster. Por ejemplo, lo siguiente debería conectarse a un clúster denominado **mycluster** como usuario **myuser**.

		ssh myuser@mycluster-ssh.azurehdinsight.net

4. En la sesión de SSH, agregue los archivos de python cargados anteriormente en el almacenamiento de WASB para el clúster.

		hadoop fs -copyFromLocal streaming.py /streaming.py
		hadoop fs -copyFromLocal jython.py /jython.py

Después de cargar los archivos, siga los pasos siguientes para ejecutar los trabajos de Hive y Pig.

####Hive

1. Use el comando `hive` para iniciar el shell de Hive. Debería ver un símbolo del sistema `hive>` cuando se haya cargado el shell.

2. En el símbolo del sistema `hive>`, escriba lo siguiente:

		add file wasb:///streaming.py;
		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		  USING 'streaming.py' AS
		  (clientid string, phoneLabel string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

3. Después de escribir la última línea, debe iniciarse el trabajo. Finalmente, se devolverán unos resultados similares a los siguientes:

		100041	RIM 9650	d476f3687700442549a83fac4560c51c
		100041	RIM 9650	d476f3687700442549a83fac4560c51c
		100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
		100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
		100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

####Pig

1. Use el comando `pig` para iniciar el shell. Debería ver un símbolo del sistema `grunt>` cuando se haya cargado el shell.

2. En el símbolo del sistema `grunt>`, escriba las siguientes instrucciones:

		Register wasb:///jython.py using jython as myfuncs;
	    LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
	    LOG = FILTER LOGS by LINE is not null;
	    DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
	    DUMP DETAILS;

3. Después de escribir la siguiente línea, debe iniciarse el trabajo. Finalmente, se devolverán unos resultados similares a los siguientes:

		((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
		((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
		((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
		((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
		((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

###PowerShell

En estos pasos que se usa Azure PowerShell. Si aún no está instalado y configurado en su máquina de desarrollo, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) antes de realizar los siguientes pasos.

1. Mediante los ejemplos de Python [streaming.py](#streamingpy) y [jython.py](#jythonpy), cree copias locales de los archivos en su máquina de desarrollo.

2. Utilice el siguiente script de PowerShell para cargar los archivos **streaming.py** y **jython.py** en el servidor. Sustituya el nombre del clúster de HDInsight de Azure y la ruta de acceso a los archivos **streaming.py** y **jython.py** en las tres primeras líneas del script.

		$clusterName = YourHDIClusterName
		$pathToStreamingFile = "C:\path\to\streaming.py"
		$pathToJythonFile = "C:\path\to\jython.py"

		$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }

		#Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
        
        Set-AzureStorageBlobContent `
            -File $pathToStreamingFile `
            -Blob "streaming.py" `
            -Container $container `
            -Context $context
		
        Set-AzureStorageBlobContent `
            -File $pathToJythonFile `
            -Blob "jython.py" `
            -Container $container `
            -Context $context

	Este script recupera información del clúster de HDInsight, luego extrae la cuenta y la clave de la cuenta de almacenamiento predeterminada y seguidamente carga los archivos en la raíz del contenedor.

	> [AZURE.NOTE] En el documento [Carga de datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md)se pueden encontrar otros métodos para cargar los scripts.

Después de cargar los archivos, use los siguientes scripts de PowerShell para iniciar los trabajos. Cuando finalice el trabajo, la salida se debe escribir en la consola de PowerShell.

####Hive

El siguiente script ejecutará el script __streaming.py__. Antes de ejecutarse, solicitará la información de la cuenta de administrador/HTTPs para el clúster de HDInsight.

    # Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName
    $creds=Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        | %{ $_.Key1 }
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
	$HiveQuery = "add file wasb:///streaming.py;" +
	             "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
	               "USING 'D:\Python27\python.exe streaming.py' AS " +
	               "(clientid string, phoneLabel string, phoneHash string) " +
	             "FROM hivesampletable " +
	             "ORDER BY clientid LIMIT 50;"

	$jobDefinition = New-AzureRmHDInsightHiveJobDefinition `
        -Query $HiveQuery

	$job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
	Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
	Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

La salida del trabajo de **Hive** debe parecerse a la siguiente:

	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

####Pig

Lo siguiente usará el script __jython.py__. Antes de ejecutarse, solicitará la información de administración/HTTPs para el clúster de HDInsight.

	# Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        | %{ $_.Key1 }
    
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
	$PigQuery = "Register wasb:///jython.py using jython as myfuncs;" +
	            "LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);" +
	            "LOG = FILTER LOGS by LINE is not null;" +
	            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
	            "DUMP DETAILS;"

	$jobDefinition = New-AzureRmHDInsightPigJobDefinition -Query $PigQuery

	$job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
        
	Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
	Wait-AzureRmHDInsightJob `
        -Job $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

La salida del trabajo de **Pig** debe parecerse a la siguiente:

	((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
	((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
	((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
	((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
	((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

##<a name="troubleshooting"></a>Solución de problemas

###Errores en la ejecución de trabajos

Al ejecutar el trabajo de Hive, es posible que se produzca un error similar al siguiente:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
    
Este problema puede deberse a los finales de línea del archivo streaming.py. De forma predeterminada, muchos editores de Windows usan CRLF como final de línea, pero las aplicaciones Linux normalmente esperan caracteres LF.

Si tiene un editor que no puede crear finales de línea LF, o bien no está seguro qué finales de línea usa, siga las siguientes instrucciones de PowerShell para quitar los caracteres CR antes de cargar el archivo en HDInsight:

    $original_file ='c:\path\to\streaming.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)

###Scripts de PowerShell

Ambos scripts de ejemplo de PowerShell usados para ejecutar los ejemplos contienen una línea comentada que mostrará una salida con error para el trabajo. Si no ve la salida esperada del trabajo, quite los comentarios de la siguiente línea y observe si la información de error indica un problema.

	# Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

La información de error (STDERR) y el resultado del trabajo (STDOUT) también se registran en el contenedor de blobs predeterminado de los clústeres en las siguientes ubicaciones.

Para este trabajo:|Examine estos archivos en el contenedor de blobs.
---|---
Hive|/HivePython/stderr<p>/HivePython/stdout
Pig|/PigPython/stderr<p>/PigPython/stdout

##<a name="next"></a>Pasos siguientes

Si necesita cargar módulos de Python que no se proporcionan de forma predeterminada, consulte [Implementación de un módulo en HDInsight de Azure](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) para ver un ejemplo de cómo hacerlo.

Para conocer otras formas de usar Pig y Hive, y para obtener información sobre el uso de MapReduce, consulte lo siguiente:

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)

* [Uso de Pig con HDInsight](hdinsight-use-pig.md)

* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0211_2016-->