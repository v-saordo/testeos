<properties
   pageTitle="Uso de Pig con Hadoop con Curl en HDInsight | Microsoft Azure"
   description="En este documento aprenderá a utilizar Curl para ejecutar trabajos de Pig Latin en un clúster de Hadoop en HDInsight de Azure."
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

#Ejecución de trabajos de Pig con Hadoop en HDInsight con Curl

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

En este documento, aprenderá a utilizar Curl para ejecutar trabajos de Pig Latin en un clúster de HDInsight de Azure. El lenguaje de programación de Pig Latin le permite describir las transformaciones que se aplican a los datos de entrada para generar el resultado deseado.

CURL sirve para demostrar cómo se puede interactuar con HDInsight mediante solicitudes HTTP sin procesar para ejecutar, supervisar y recuperar los resultados de los trabajos de Pig. Esto funciona mediante la API de REST de WebHCat (antes conocida como Templeton) que proporciona el clúster de HDInsight.

> [AZURE.NOTE]Si ya está familiarizado con el uso de servidores de Hadoop basados en Linux, pero no conoce HDInsight, consulte [Información sobre el uso de HDInsight en Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesitará lo siguiente:

* Un clúster de HDInsight de Azure (Hadoop en HDInsight) (basado en Windows o en Linux)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Ejecución de trabajos de Pig con Curl

> [AZURE.NOTE]Al usar Curl o cualquier otra comunicación REST con WebHCat, debe proporcionar el nombre de usuario y la contraseña del administrador del clúster de HDInsight para autenticar las solicitudes. También debe usar el nombre del clúster como parte del identificador uniforme de recursos (URI) que se usa para enviar las solicitudes al servidor.
>
> En el caso de los comandos que aparecen en esta sección, reemplace **USERNAME** por el usuario para autenticación en el clúster y **PASSWORD** por la contraseña de la cuenta de usuario. Reemplace **CLUSTERNAME** por el nombre del clúster.
>
> La API de REST se protege con la [autenticación de acceso básico](http://en.wikipedia.org/wiki/Basic_access_authentication). Siempre debe crear solicitudes usando HTTP segura (HTTPS) para así garantizar que las credenciales se envían de manera segura al servidor.

1. Desde una línea de comandos, utilice el siguiente comando para comprobar que puede conectarse al clúster de HDInsight.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Debería recibir una respuesta similar a la siguiente:

        {"status":"ok","version":"v1"}

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-u**: el nombre de usuario y la contraseña que se utilizan para autenticar la solicitud.
    * **-G**: indica que esta es una solicitud GET.

    El comienzo de la dirección URL, ****https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será el mismo para todas las solicitudes. La ruta de acceso, **/status**, indica que la solicitud debe devolver el estado de WebHCat (también conocido como Templeton) al servidor.

2. Utilice el siguiente código para enviar un trabajo de Pig Latin al clúster:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasb:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasb:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-d**: dado que `-G` no se usa, la solicitud tiene como valor predeterminado el método POST. `-d` especifica los valores de datos que se envían con la solicitud.

        * **user.name**: el usuario que ejecuta el comando.
        * **execute**: las instrucciones de Pig Latin que se ejecutarán.
        * **statusdir**: el directorio donde se escribirá el estado de este trabajo.

    > [AZURE.NOTE]Observe que los espacios en las instrucciones de Pig Latin se reemplazan por el carácter `+` cuando se utilizan con Curl.

    Este comando debe devolver un identificador de trabajo que se pueda usar para comprobar el estado del trabajo, por ejemplo:

        {"id":"job_1415651640909_0026"}

3. Para revisar el estado del trabajo, use el siguiente comando. Reemplace **JOBID** por el valor devuelto en el paso anterior. Por ejemplo, si el valor devuelto fue `{"id":"job_1415651640909_0026"}`, entonces **JOBID** sería `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Si se completó el trabajo, el estado será **SUCCEEDED**.

    > [AZURE.NOTE]Esta solicitud de Curl devuelve un documento de notación de objetos JavaScript (JSON) con información acerca del trabajo; jq se usa para recuperar solo el valor de estado.

##<a id="results"></a>Visualización de resultados

Una vez que el estado del trabajo cambió a **SUCCEEDED**, puede recuperar los resultados del trabajo desde el almacenamiento de blobs de Azure. El parámetro `statusdir` transmitido con la consulta contiene la ubicación del archivo de salida; en este caso, ****wasb:///example/pigcurl**. Esta dirección almacena el resultado del trabajo en el directorio **example/pigcurl** en el contenedor de almacenamiento predeterminado utilizado por el clúster de HDInsight.

Puede enumerar y descargar estos archivos mediante el [CLI de Azure para Mac, Linux y Windows](../xplat-cli-install.md). Por ejemplo, para enumerar los archivos en **example/pigcurl**, use el siguiente comando:

	azure storage blob list <container-name> example/pigcurl

Para descargar un archivo, use lo siguiente:

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]Debe especificar el nombre de la cuenta de almacenamiento que contiene el blob usando los parámetros `-a` y `-k` o bien, definir las variables de entorno **AZURE\_STORAGE\_ACCOUNT** y **AZURE\_STORAGE\_ACCESS\_KEY**.

##<a id="summary"></a>Resumen

Como se muestra en este documento, puede utilizar la solicitud HTTP sin procesar para ejecutar, supervisar y ver los resultados de los trabajos de Hive en el clúster de HDInsight.

Para obtener más información sobre la interfaz REST utilizada en este artículo, consulte la [referencia de WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre Pig en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

Para obtener información sobre otras maneras de trabajar con Hadoop en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0114_2016-->