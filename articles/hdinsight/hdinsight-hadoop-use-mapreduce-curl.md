<properties
   pageTitle="Uso de MapReduce y Curl con Hadoop en HDInsight | Microsoft Azure"
   description="Obtenga información acerca de cómo ejecutar de manera remota trabajos de MapReduce con Hadoop en HDInsight con Curl."
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
   ms.date="02/05/2016"
   ms.author="larryfr"/>

#Ejecución de trabajos de MapReduce con Hadoop en HDInsight con Curl

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

En este documento, obtendrá información sobre cómo utilizar Curl para ejecutar trabajos de MapReduce en un clúster de Hadoop en HDInsight.

Curl se usa para demostrar cómo puede interactuar con HDInsight mediante solicitudes HTTP sin formato para ejecutar trabajos de MapReduce. Esto funciona mediante la API de REST de WebHCat (conocida anteriormente como Templeton) proporcionada por el clúster de HDInsight.

> [AZURE.NOTE] Si ya está familiarizado con el uso de servidores de Hadoop basado en Linux, pero no conoce HDInsight, consulte [Lo que necesita saber acerca de Hadoop en HDInsight basado en Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesitará lo siguiente:

* Un clúster de Hadoop en HDInsight (basado en Linux o Windows)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Ejecución de trabajos de MapReduce mediante Curl

> [AZURE.NOTE] Al usar Curl o cualquier otra comunicación REST con WebHCat, debe autenticar las solicitudes proporcionando el nombre de usuario y la contraseña de administrador del clúster de HDInsight. También debe utilizar el nombre de clúster como parte del URI utilizado para enviar las solicitudes al servidor.
>
> Para los comandos de esta sección, reemplace **USERNAME** por el usuario que se autenticará en el clúster y **PASSWORD** por la contraseña de la cuenta de usuario. Reemplace **CLUSTERNAME** por el nombre del clúster.
>
> La API de REST se protege con la [autenticación de acceso básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Siempre debe crear solicitudes usando HTTPS para así garantizar que las credenciales se envían de manera segura al servidor.

1. Desde una línea de comandos, use el siguiente comando para comprobar que puede conectarse al clúster de HDInsight:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Debería recibir una respuesta similar a la siguiente:

        {"status":"ok","version":"v1"}

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-u**: el nombre de usuario y la contraseña que se utilizan para autenticar la solicitud.
    * **-G**: indica que esta es una solicitud GET.

    El comienzo del URI, ****https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será el mismo para todas las solicitudes.

2. Para enviar un trabajo de MapReduce, utilice lo siguiente:

		curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasb:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasb:///example/data/gutenberg/davinci.txt -d arg=wasb:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    El final del URI (/mapreduce/jar) indica a WebHCat que esta solicitud iniciará un trabajo de MapReduce desde una clase en un archivo jar. Los parámetros que se utilizan en este comando son los siguientes:

	* **-d** : `-G` no se usa, la solicitud tiene como valor predeterminado el método POST. `-d` especifica los valores de datos que se envían con la solicitud.

        * **user.name**: el usuario que ejecuta el comando.
        * ** jar**: la ubicación del archivo jar que contiene la clase que se va a ejecutar.
        * **class**: la clase que contiene la lógica de MapReduce.
        * **arg**: los argumentos que se pasarán al trabajo de MapReduce; en este caso, el archivo de texto de entrada y el directorio que se utilizan para la salida.

    Este comando debe devolver un identificador de trabajo que se pueda usar para comprobar el estado del trabajo:

        {"id":"job_1415651640909_0026"}

3. Para revisar el estado del trabajo, use el siguiente comando. Reemplace el valor de **JOBID** por el valor devuelto en el paso anterior. Por ejemplo, si el valor devuelto fue `{"id":"job_1415651640909_0026"}`, entonces el JOBID debería ser `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Si se completó el trabajo, el estado será "SUCCEEDED".

    > [AZURE.NOTE] Esta solicitud de Curl devuelve un documento JSON con información acerca del trabajo; jq se usa para recuperar solo el valor de estado.

4. Una vez que el estado del trabajo cambió a **SUCCEEDED**, puede recuperar los resultados del trabajo desde el almacenamiento de blobs de Azure. El parámetro `statusdir` transmitido con la consulta contiene la ubicación del archivo de salida; en este caso,****wasb:///example/curl**. Esta dirección almacena la salida del trabajo en el directorio **example/curl** en el contenedor de almacenamiento predeterminado que su clúster de HDInsight usa.

Puede enumerar y descargar estos archivos mediante el [CLI de Azure para Mac, Linux y Windows](../xplat-cli-install.md). Por ejemplo, para enumerar los archivos existentes en **example/curl**, use el siguiente comando:

	azure storage blob list <container-name> example/curl

Para descargar un archivo, use lo siguiente:

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Debe especificar el nombre de la cuenta de almacenamiento que contiene el blob usando los parámetros `-a` y `-k` o bien, definir las variables de entorno **AZURE\_STORAGE\_ACCOUNT** y **AZURE\_STORAGE\_ACCESS\_KEY**. Consulte la sección acerca de [cómo cargar datos en HDInsight](hdinsight-upload-data.md) para obtener más información.

##<a id="summary"></a>Resumen

Tal como se demostró en este documento, puede usar una solicitud HTTP sin procesar para ejecutar, supervisar y ver los resultados de los trabajos de Hive en su clúster de HDInsight.

Para obtener más información sobre la interfaz REST utilizada en este artículo, consulte la [referencia de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre los trabajos de MapReduce en HDInsight:

* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Para obtener información sobre otras maneras de trabajar con Hadoop en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0211_2016-->