<properties
   pageTitle="Uso de Hive con Hadoop con Curl en HDInsight | Microsoft Azure"
   description="Sepa cómo enviar remotamente trabajos de Pig a HDInsight mediante el uso de Curl."
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
   ms.date="02/16/2016"
   ms.author="larryfr"/>

#Ejecución de consultas de Hive con Hadoop en HDInsight con Curl

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

En este documento, aprenderá a usar Curl para ejecutar consultas de Hive en un clúster de Hadoop en HDInsight de Azure.

Curl se usa para mostrar cómo se puede interactuar con HDInsight usando solicitudes HTTP sin procesar para ejecutar, supervisar y recuperar los resultados de las consultas de Hive. Esto funciona mediante la API de REST de WebHCat (conocida anteriormente como Templeton) proporcionada por el clúster de HDInsight.

> [AZURE.NOTE] Si ya está familiarizado con el uso de servidores de Hadoop basado en Linux, pero no conoce HDInsight, consulte [Lo que necesita saber acerca de Hadoop en HDInsight basado en Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Requisitos previos

Para completar los pasos de este artículo, necesitará lo siguiente:

* Un clúster de Hadoop en HDInsight (basado en Linux o Windows)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Ejecución de consultas de Hive con Curl

> [AZURE.NOTE] Al usar Curl o cualquier otra comunicación REST con WebHCat, debe proporcionar el nombre de usuario y la contraseña del administrador del clúster de HDInsight para autenticar las solicitudes. También debe usar el nombre del clúster como parte del identificador uniforme de recursos (URI) que se usa para enviar las solicitudes al servidor.
>
> En el caso de los comandos que aparecen en esta sección, reemplace **USERNAME** por el usuario para autenticación en el clúster y **PASSWORD** por la contraseña de la cuenta de usuario. Reemplace **CLUSTERNAME** por el nombre del clúster.
>
> La API de REST se protege con la [autenticación básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Siempre debe crear solicitudes usando HTTP segura (HTTPS) para así garantizar que las credenciales se envían de manera segura al servidor.

1. Desde una línea de comandos, utilice el siguiente comando para comprobar que puede conectarse al clúster de HDInsight.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Debería recibir una respuesta similar a la siguiente:

        {"status":"ok","version":"v1"}

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-u**: el nombre de usuario y la contraseña que se utilizan para autenticar la solicitud.
    * **-G**: indica que esta es una solicitud GET.

    El comienzo de la dirección URL, ****https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, será el mismo para todas las solicitudes. La ruta de acceso, **/status**, indica que la solicitud debe devolver un estado de WebHCat (también conocido como Templeton) al servidor. También puede solicitar la versión de Hive con el siguiente comando:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    Debiera devolver una respuesta similar a la siguiente:

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Use lo siguiente para crear una tabla nueva llamada **log4jLogs**:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasb:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Los parámetros que se utilizan en este comando son los siguientes:

    * **-d** : desde dado que `-G` no se usa, la solicitud tiene como valor predeterminado el método POST. `-d` especifica los valores de datos que se envían con la solicitud.

        * **user.name**: el usuario que ejecuta el comando.

        * **execute**: las instrucciones HiveQL para ejecutar.

        * **statusdir**: el directorio donde se escribirá el estado de este trabajo.

    Estas instrucciones realizan las acciones siguientes:

    * **DROP TABLE**: elimina la tabla y el archivo de datos si la tabla ya existe.

    * **CREATE EXTERNAL TABLE**: crea una tabla "externa" nueva en Hive. Las tablas externas solo almacenan la definición de tabla en Hive. Los datos permanecen en la ubicación original.

		> [AZURE.NOTE] Las tablas externas se deben usar cuando espera que un origen externo, como por ejemplo un proceso de carga de datos automático, u otra operación MapReduce, actualice los datos subyacentes, pero siempre desea que las consultas de Hive usen los datos más recientes.
		>
		> La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.

    * **ROW FORMAT**: indica cómo se da formato a los datos de Hive. En este caso, los campos de cada registro se separan mediante un espacio.

    * **STORED AS TEXTFILE LOCATION**: indica a Hive dónde se almacenan los datos (el directorio example/data) y que se almacenan como texto.

    * **SELECT**: selecciona un número de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Esto debe devolver un valor de **3** ya que hay tres filas que contienen este valor.

    > [AZURE.NOTE] Observe que los espacios entre las instrucciones HiveQL se reemplazan por el carácter `+` cuando se usa con Curl. Los valores entre comillas que contengan un espacio, como el delimitador, no se reemplazarán por `+`.

    * **INPUT\_\_FILE\_\_NAME LIKE '%25.log'**: esto limita la búsqueda a solo usar archivos que terminan en .log. Si esto no está presente, Hive intentará buscar todos los archivos de este directorio y sus subdirectorios, incluidos los archivos que no coinciden con el esquema de columna definido para esta tabla.

    > [AZURE.NOTE] Tenga en cuenta que %25 es el formato codificado de URL de %, por lo que la condición real es `like '%.log'`. El % tiene que ser una dirección URL codificada, ya que se trata como carácter especial en las direcciones URL.

    Este comando debe devolver un identificador de trabajo que se pueda usar para comprobar el estado del trabajo.

        {"id":"job_1415651640909_0026"}

3. Para revisar el estado del trabajo, use el siguiente comando. Reemplace **JOBID** por el valor devuelto en el paso anterior. Por ejemplo, si el valor devuelto fue `{"id":"job_1415651640909_0026"}`, entonces **JOBID** sería `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Si se completó el trabajo, el estado será **SUCCEEDED**.

    > [AZURE.NOTE] Esta solicitud de Curl devuelve un documento de notación de objetos JavaScript (JSON) con información acerca del trabajo; jq se usa para recuperar solo el valor de estado.

4. Una vez que el estado del trabajo cambió a **SUCCEEDED**, puede recuperar los resultados del trabajo desde el almacenamiento de blobs de Azure. El parámetro `statusdir` transmitido con la consulta contiene la ubicación del archivo de salida; en este caso, ****wasb:///example/curl**. Esta dirección almacena la salida del trabajo en el directorio **example/curl** en el contenedor de almacenamiento predeterminado que su clúster de HDInsight usa.

    Puede enumerar y descargar estos archivos mediante el [CLI de Azure para Mac, Linux y Windows](../xplat-cli-install.md). Por ejemplo, para enumerar los archivos existentes en **example/curl**, use el siguiente comando.

		azure storage blob list <container-name> example/curl

	Para descargar un archivo, use lo siguiente:

		azure storage blob download <container-name> <blob-name> <destination-file>

	> [AZURE.NOTE] Debe especificar el nombre de la cuenta de almacenamiento que contiene el blob usando los parámetros `-a` y `-k` o bien, definir las variables de entorno **AZURE\_STORAGE\_ACCOUNT** y **AZURE\_STORAGE\_ACCESS\_KEY**. Vea <a href="hdinsight-upload-data.md" target="\_blank" para obtener más información.

6. Use las siguientes instrucciones para crear una nueva tabla "interna" llamada **errorLogs**:

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log';SELECT+*+from+errorLogs;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Estas instrucciones realizan las acciones siguientes:

    * **CREATE TABLE IF NOT EXISTS**: crea una tabla, si todavía no existe. Dado que la palabra clave **EXTERNAL** no es usa, se trata de una tabla interna, la que se almacena en el almacenamiento de datos de Hive y es administrada completamente por Hive.

		> [AZURE.NOTE] A diferencia de las tablas externas, la eliminación de una tabla interna también eliminará los datos subyacentes.

    * **STORED AS ORC**: almacena los datos en el formato Optimized Row Columnar (ORC). Se trata de un formato altamente optimizado y eficiente para almacenar datos de Hive.
    * **INSERT OVERWRITE ... SELECT**: selecciona filas de la tabla **log4jLogs** que contienen **[ERROR]** y, a continuación, inserta los datos en la tabla **errorLogs**.
    * **SELECT**: selecciona todas las filas de la nueva tabla **errorLogs**.

7. Use el identificador de trabajo devuelto para revisar el estado del trabajo. Una vez realizado correctamente, use la CLI de Azure para Mac, Linux y Windows tal como se describió anteriormente para descargar y ver los resultados. La salida debe contener tres líneas, todas las cuales contienen **[ERROR]**.


##<a id="summary"></a>Resumen

Tal como se demostró en este documento, puede usar una solicitud HTTP sin procesar para ejecutar, supervisar y ver los resultados de los trabajos de Hive en su clúster de HDInsight.

Para obtener más información sobre la interfaz REST utilizada en este artículo, consulte la referencia de <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">WebHCat</a>.

##<a id="nextsteps"></a>Pasos siguientes

Si desea obtener información general sobre Hive con HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras formas en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Si usa Tez con Hive, consulte los siguientes documentos para la información de depuración:

* [Use the Tez UI on Windows-based HDInsight](hdinsight-debug-tez-ui.md) (Uso de la IU de Tez en HDInsight basado en Windows)

* [Use the Ambari Tez view on Linux-based HDInsight](hdinsight-debug-ambari-tez-view.md) (Uso de la vista Tez de Ambari en HDInsight basado en Linux)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md




[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

<!---HONumber=AcomDC_0218_2016-->