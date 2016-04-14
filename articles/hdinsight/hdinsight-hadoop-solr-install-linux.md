<properties
	pageTitle="Uso de la acción de script para instalar Solr en HDInsight basado en Linux | Microsoft Azure"
	description="Aprenda a instalar Solr en clústeres de Hadoop para HDInsight basados en Linux mediante acciones de script."
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
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="larryfr"/>

# Instalación y uso de Solr en clústeres de Hadoop de HDInsight

En este tema aprenderá a instalar Solr Azure HDInsight con la acción de script. Solr es una eficaz plataforma de búsqueda eficaz y proporciona capacidades de búsqueda de nivel empresarial en datos administrados por Hadoop. Una vez que haya instalado Solr en un clúster de HDInsight, también aprenderá a buscar datos mediante Solr.

> [AZURE.NOTE] Para realizar los pasos que se describen en este documento se requiere un clúster de HDInsight basado en Linux. Para obtener información sobre el uso de Solr con un clúster basado en Windows, vea [Instalación y uso de Solr en clústeres Hadoop de HDinsight (Windows)](hdinsight-hadoop-solr-install.md)

El script de ejemplo que se utiliza en este tema crea un clúster de Solr con una configuración concreta. Si desea configurar el clúster de Solr con distintas colecciones, particiones, esquemas o réplicas, entre otras, debe modificar el script y los archivos binarios de Solr en consecuencia.

## <a name="whatis"></a>¿Qué es Solr?

[Apache Solr](http://lucene.apache.org/solr/features.html) es una plataforma de búsqueda empresarial que permite una eficaz búsqueda de texto completo en los datos. Si bien Hadoop permite almacenar y administrar grandes cantidades de datos, Apache Solr proporciona las capacidades de búsqueda para recuperar rápidamente los datos. Este tema proporciona instrucciones sobre cómo personalizar un clúster de HDInsight para instalar Solr.

## Funcionamiento del script

Este script realiza los siguientes cambios en el clúster de HDInsight:

* Instala Solr en `/usr/hdp/current/solr`
* Crea un nuevo usuario, __solrusr__, que se usa para ejecutar el servicio Solr
* Establece __solruser__ como propietario de `/usr/hdp/current/solr`
* Agrega una configuración de [Upstart](http://upstart.ubuntu.com/) que iniciará Solr si se reinicia un nodo de clúster. Solr se inicia automáticamente en los nodos del clúster después de la instalación

## <a name="install"></a>Instalación de Solr mediante acciones de script

Hay un script de ejemplo para instalar Solr en un clúster de HDInsight disponible desde un blob de Almacenamiento de Azure de solo lectura en [https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh). Esta sección proporciona instrucciones sobre cómo utilizar el script de ejemplo durante el aprovisionamiento del clúster mediante el Portal de Azure.

> [AZURE.NOTE] También puede usar Azure PowerShell o el SDK de .NET para HDInsight para crear un clúster mediante este script. Para obtener más información sobre el uso de estos métodos, consulte [Personalización de clústeres de HDInsight mediante acciones de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie el aprovisionamiento de un clúster siguiendo los pasos que se describen en [Aprovisionamiento de clústeres de HDInsight basado en Linux](hdinsight-provision-linux-clusters.md#portal), pero no complete la operación.

2. En la hoja **Configuración opcional**, seleccione **Acciones de script** y proporcione la información siguiente:

	* __NOMBRE__: escriba un nombre descriptivo para la acción de script.
	* __URI DE SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
	* __PRINCIPAL__: active esta opción.
	* __TRABAJO__: active esta opción.
	* __ZOOKEEPER__: active esta opción para instalar en el nodo Zookeeper.
	* __PARÁMETROS__: deje este campo en blanco.

3. En la parte inferior de **Acciones de scripts**, use el botón **Seleccionar** para guardar la configuración. Por último, use el botón **Seleccionar** situado en la parte inferior de la hoja **Configuración opcional** para guardar la información de configuración opcional.

4. Siga aprovisionando el clúster como se describe en [Aprovisionamiento de clústeres de HDInsight basados en Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usesolr"></a>¿Cómo uso Solr en HDInsight?

### Datos de indexación

Debe comenzar con la indización de Solr con algunos archivos de datos. A continuación, puede utilizar Solr para ejecutar consultas de búsqueda en los datos indizados. Use los pasos siguientes para agregar algunos datos de ejemplo a Solr y luego, consúltelo :

1. Conéctese al clúster de HDInsight con SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Para obtener más información sobre el uso de SSH con HDInsight, vea lo siguiente:

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

	> [AZURE.IMPORTANT] En unos pasos más adelante en este documento, usará un túnel SSL para conectarse a la interfaz de usuario web Solr. Para poder seguir estos pasos, tiene que establecer un túnel SSL y, a continuación, configurar el explorador para que lo utilice.
	>
	> Para obtener más información, consulte [Uso de la tunelación SSH para tener acceso a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web](hdinsight-linux-ambari-ssh-tunnel.md)

2. Use los siguientes comandos para tener los datos de ejemplo del índice Solr:

		cd /usr/hdp/current/solr/example/exampledocs
		java -jar post.jar solr.xml monitor.xml

	Debería ver estos resultados en la consola:

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	La utilidad post.jar indexa Solr con dos documentos de muestra, **solr.xml** y **monitor.xml**. Estos se almacenarán en __collection1__ en Solr.

3. Para consultar la API de REST expuestos por Solr, utilice lo siguiente:

		curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"

	Esto emite una consulta a __collection1__ sobre cualquier documento coincidente __*: *__ (codificado como * %3A * en la cadena de consulta) y la respuesta se debe devolver como JSON. Los resultados deberían parecerse a los siguientes:

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }

### Mediante el panel de Solr

El panel de Solr es una interfaz de usuario web que le permite trabajar con Solr mediante el explorador web. El panel de Solr no se expone directamente a Internet desde su clúster de HDInsight, pero debe tener acceso usando un túnel SSH. Para obtener más información sobre el uso de un tune SSH, consulte [Uso de la tunelación SSH para tener acceso a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web](hdinsight-linux-ambari-ssh-tunnel.md)

Una vez establecido un túnel SSH, siga estos pasos para usar el panel de Solr:

1. Determine el nombre del host del nodo principal:

    1. En un explorador, vaya a https://CLUSTERNAME.azurehdinsight.net. Cuando se le solicite, use el nombre de usuario y la contraseña de administrador para autenticarse en el sitio.
    
    2. En el menú que aparece en la parte superior de la página, seleccione __Hosts__.
    
    3. Seleccione la entrada que comienza con __hn0__. Cuando se abre la página, se mostrará el nombre de host en la parte superior. El formato del nombre de host es __hn0-PARTEDELNOMBREDELCLÚSTER.caracteres aleatorios.cx.internal.cloudapp.net__. Este es el nombre del host que debe usar cuando se conecte con el panel de Solr.
    
1. En el explorador, conéctese a \_\___http://HOSTNAME:8983/solr/#/__, donde __HOSTNAME__ es el nombre que determinó en los pasos anteriores.

    La solicitud se debe enrutar a través del túnel SSH al nodo principal para el clúster de HDInsight. Debería ver una página similar a la siguiente:

	![Imagen del panel de Solr](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

2. En el panel izquierdo, use la lista desplegable **Selector principal** para seleccionar **collection1**. Varias entradas aparecerán debajo de __collection1__.

3. En las entradas que aparecen en __collection1__, seleccione __Consulta__. Use los siguientes valores para rellenar la página de búsqueda:

	* En el cuadro de texto **q**, escriba ***:***. Se devolverán todos los documentos indizados en Solr. Si desea buscar una cadena específica dentro de los documentos, puede especificar esa cadena aquí.

	* En el cuadro de texto **wt**, seleccione el formato de salida. El valor predeterminado es **json**.

	Por último, seleccione el botón **Ejecutar consulta** que aparece en la parte inferior de la página de búsqueda.

	![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

	La salida devuelve los dos documentos utilizados para indizar el Solr. La salida debe ser similar a la siguiente:

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }

### Inicio y detención de Solr

Si necesita detener o iniciar Solar manualmente, use los siguientes comandos:

	sudo stop solr

	sudo start solr

## Copia de seguridad de los datos indizados

Se recomienda que cree una copia de seguridad de los datos indexados desde los nodos del clúster de Solr en el almacenamiento de blobs de Azure. Realice los pasos siguientes para ello:

1. Conéctese al clúster con SSH y, luego, use el comando siguiente para obtener el nombre del host para el nodo principal:

        hostname -f
        
2. Use lo siguiente para crear una instantánea de los datos indexados. Reemplace __HOSTNAME__ por el nombre que devolvió el comando anterior:

		curl http://HOSTNAME:8983/solr/replication?command=backup

	Debería obtener una respuesta similar a la siguiente:

		<?xml version="1.0" encoding="UTF-8"?>
		<response>
		  <lst name="responseHeader">
		    <int name="status">0</int>
		    <int name="QTime">9</int>
		  </lst>
		  <str name="status">OK</str>
		</response>

2. A continuación, cambie al directorio __/usr/hdp/current/solr/example/solr__. Habrá un subdirectorio aquí para cada colección. Cada directorio de la colección contiene un directorio de __datos__, que es donde se encuentra la instantánea de la colección.

	Por ejemplo, si usa los pasos anteriores para indexar los documentos de ejemplo, el directorio __/usr/hdp/current/solr/example/solr/collection1/data__ debería contener ahora un directorio denominado __snapshot.###########__ donde los símbolos # se refieren a la fecha y la hora de la instantánea.

3. Cree un archivo comprimido de la carpeta de instantáneas mediante un comando similar al siguiente:

		tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855

	Esto creará un nuevo archivo denominado __snapshot.20150806185338855.tgz__, que contiene el contenido del directorio __snapshot.20150806185338855__.

3. A continuación, puede almacenar el archivo en el almacenamiento principal del clúster con el comando siguiente:

	hadoop fs -copyFromLocal snapshot.20150806185338855.tgz /example/data

	> [AZURE.NOTE] Puede que desee crear un directorio dedicado a almacenar las instantáneas Solr. Por ejemplo: `hadoop fs -mkdir /solrbackup`.

Para obtener más información sobre cómo trabajar con copia de seguridad y restauraciones de Solr, consulte [Making and restoring backups of SolrCores](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups+of+SolrCores) (Realización y restauración de copias de seguridad de SolrCores).


## Consulte también

- [Instalación y uso de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md). Hue es una interfaz de usuario web que simplifica la creación, la ejecución y el guardado de trabajos de Pig y Hive, así como el examen del almacenamiento predeterminado de su clúster de HDInsight.

- [Instalación y uso de Spark en clústeres de HDInsight][hdinsight-install-spark]. Use la personalización del clúster para instalar Spark en clústeres de Hadoop para HDInsight. Spark es un marco de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones analíticas de Big Data.

- [Instalación de R en clústeres de HDInsight][hdinsight-install-r]. Use la personalización del clúster para instalar R en clústeres de Hadoop para HDInsight. R es un entorno y lenguaje de código abierto para computación estadística. Proporciona cientos de de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientada a objetos. También proporciona amplias capacidades gráficas.

- [Instalación de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use la personalización del clúster para instalar Giraph en clústeres de Hadoop para HDInsight. Giraph permite realizar un procesamiento gráfico mediante Hadoop y se puede usar con HDInsight de Azure.

- [Instalación de Hue en clústeres de HDInsight](hdinsight-hadoop-hue-linux.md). Use la personalización del clúster para instalar Hue en clústeres de Hadoop para HDInsight. Hue es un conjunto de aplicaciones web que usan para interactuar con un clúster de Hadoop.



[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

<!---HONumber=AcomDC_0211_2016-->