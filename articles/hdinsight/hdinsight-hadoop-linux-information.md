<properties
   pageTitle="Sugerencias para usar Hadoop en HDInsight basado en Linux | Microsoft Azure"
   description="Obtenga sugerencias de implementación para usar clústeres de HDInsight basado en Linux (Hadoop) en un entorno de Linux conocido que se ejecuta en la nube de Azure."
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
   ms.date="01/12/2016"
   ms.author="larryfr"/>

# Información sobre el uso de HDInsight en Linux

Los clústeres de HDInsight de Azure basado en Linux proporcionan Hadoop en un entorno conocido de Linux, que se ejecuta en la nube de Azure. En la mayoría de los casos, debiera funcionar exactamente como cualquier otra instalación de Hadoop en Linux. Este documento detalla las diferencias específicas que debe tener en cuenta.

## Nombres de dominio

El nombre de dominio completo (FQDN) que se usa al conectarse con el clúster es **&lt;nombre del clúster>.azurehdinsight.net** o **&lt;nombre del clúster-ssh>.azurehdinsight.net** (solo para SSH).

De forma interna, cada nodo del clúster tiene un nombre que se asigna durante la configuración del clúster. Para encontrar los nombres de los clústeres, puede visitar la página __Hosts__ de la interfaz de usuario web de Ambari, o bien use lo siguiente para devolver una lista de hosts desde la API de REST de Ambari con [cURL](http://curl.haxx.se/) y [jq](https://stedolan.github.io/jq/):

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Reemplace __PASSWORD__ por la contraseña de la cuenta de administrador y __CLUSTERNAME__ por el nombre del clúster. Esto devolverá un documento JSON que contiene una lista de los hosts incluidos en el clúster y, luego, jq extrae el valor del elemento `host_name` para cada host del clúster.

Si necesita encontrar el nombre del nodo de un servicio específico, puede consultar a Ambari por ese componente. Por ejemplo, para encontrar los hosts del nodo de nombres HDFS, use lo siguiente.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Esto devuelve un documento JSON que describe el servicio y, luego, jq extrae solo el valor `host_name` para los hosts.

## Acceso remoto a los servicios

* **Ambari (web)** - https://&lt;clustername>.azurehdinsight.net

	Realice la autenticación con el usuario y la contraseña del administrador de clúster y, a continuación, inicie sesión en Ambari. Aquí también se usa el usuario y la contraseña del administrador de clúster.

	La autenticación es texto no cifrado: use siempre HTTPS para asegurarse de que la conexión sea segura.

	> [AZURE.IMPORTANT]A pesar de que es posible tener acceso directamente a través de Internet a Ambari para su clúster, cierta funcionalidad se basa en tener acceso a nodos a través del nombre de dominio interno que usa el clúster. Debido a que se trata de un nombre de dominio interno y no público, recibirá errores de "servidor no encontrado" al intentar tener acceso a algunas características a través de Internet.
	>
	> Para usar la funcionalidad completa de la interfaz de usuario de la web Ambari, usa un túnel SSH para delegar el tráfico web al nodo principal del clúster. Consulte [Uso de la tunelización SSH para tener acceso a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE]Realice la autenticación con el usuario y la contraseña del administrador de clúster.
	>
	> La autenticación es texto no cifrado: use siempre HTTPS para asegurarse de que la conexión sea segura.

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE]Realice la autenticación con el usuario y la contraseña del administrador de clúster.
	>
	> La autenticación es texto no cifrado: use siempre HTTPS para asegurarse de que la conexión sea segura.

* **SSH** - &lt;nombre del clúster>-ssh.azurehdinsight.net en el puerto 22 o 23. El puerto 22 se usa para conectarse al nodo principal 0, mientras que el 23 se usa para conectarse al nodo principal 1. Para obtener más información sobre los nodos principales, consulte [Disponibilidad y confiabilidad de clústeres de Hadoop en HDInsight](hdinsight-high-availability-linux.md).

	> [AZURE.NOTE]Solo puede tener acceso a los nodos principales del clúster a través de SSH desde un equipo cliente. Una vez conectado, puede tener acceso a los nodos de trabajo mediante el uso de SSH desde el nodo principal.

## Ubicaciones de archivo

Puede encontrar los archivos relacionados con Hadoop en los nodos de clúster en `/usr/hdp`. Este directorio raíz contiene los siguientes subdirectorios:

* __2.2.4.9-1__: este directorio se denomina con el nombre de la versión de Hortonworks Data Platform usada por HDInsight, por lo que el número de su clúster puede ser diferente del que aparece aquí.
* __current__: este directorio contiene vínculos a los directorios que se encuentran en el directorio __2.2.4.9-1__ y existe para que no tengas que escribir un número de versión (que puede cambiar) cada vez que quieras acceder a un archivo.

Es posible encontrar los archivos JAR y datos de ejemplo en el Sistema de archivos distribuido de Hadoop (HDFS) o en el almacenamiento de blobs de Azure en "/example" o "wasb:///example".

## Procedimientos recomendados de almacenamiento, almacenamiento de blobs de Azure y HDFS

En la mayoría de las distribuciones de Hadoop, se crean copias de seguridad de HDFS en el almacenamiento local de las máquinas del clúster. A pesar de que esto es eficaz, puede ser costoso para una solución basada en la nube en la que se le cobra por hora por los recursos de proceso.

HDInsight usa el almacenamiento de blobs de Azure como el almacén predeterminado, lo que brinda los siguientes beneficios:

* Almacenamiento económico a largo plazo

* Accesible desde servicios externos, como sitios web, utilidades para carga/descarga de archivos, SDK en varios idiomas y exploradores web.

Debido a que es el almacén predeterminado para HDInsight, normalmente no tiene que hacer nada para utilizarlo. Por ejemplo, el comando siguiente enumerará los archivos existentes en la carpeta **/example/data**, que está almacenada en el almacenamiento de blobs de Azure:

	hadoop fs -ls /example/data

Es posible que algunos comandos requieran que especifique que usa almacenamiento de blobs. En estos casos, puede usar el prefijo ****WASB://** en el comando.

HDInsight también le permite asociar varias cuentas de almacenamiento de blobs a un clúster. Para tener acceso a los datos ubicados en una cuenta de almacenamiento de blobs no predeterminada, puede usar el formato **WASB://&lt;container-name>@&lt;nombre de la cuenta>.blob.core.windows.net/**. Por ejemplo, lo siguiente enumerará los contenidos del directorio **/example/data** para la cuenta de almacenamiento de blobs y el contenedor especificados:

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### ¿Qué almacenamiento de blobs utiliza el clúster?

Durante la creación del clúster, seleccionó usar un contenedor y una cuenta de almacenamiento de Azure existentes, o bien, crear una nueva. Por lo tanto, probablemente se le olvidó. Puede encontrar la cuenta de almacenamiento y el contenedor predeterminados con la API de REST de Ambari.

1. Utilice el siguiente comando para recuperar información de configuración de HDFS con curl y filtrarla mediante [jq](https://stedolan.github.io/jq/):

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
    > [AZURE.NOTE]Esto devolverá la primera configuración aplicada al servidor (`service_config_version=1`), la que contendrá esta información. Si recupera un valor que se modificó después de la creación del clúster, es posible que deba enumerar las versiones de configuración y recuperar la más reciente.

    Esto devolverá un valor similar al siguiente, donde __CONTAINER__ es el contenedor predeterminado y __ACCOUNTNAME__ es el nombre de la cuenta de almacenamiento de Azure:

        wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net

1. Para obtener el grupo de recursos para la cuenta de Almacenamiento, use la [CLI de Azure](../xplat-cli-install.md). En el comando siguiente, reemplace __ACCOUNTNAME__ por el nombre de la cuenta de almacenamiento que se recuperó de Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Esto devolverá el nombre del grupo de recursos de la cuenta.
    
    > [AZURE.NOTE]Si este comando no devuelve nada, debe cambiar la CLI de Azure al modo Administrador de recursos de Azure y ejecutar el comando de nuevo. Para cambiar al modo Administrador de recursos de Azure, use el siguiente comando:
    >
    > `azure config mode arm`
    
2. Obtenga la clave de la cuenta de almacenamiento. Reemplace __GROUPNAME__ por el grupo de recursos del paso anterior. Reemplace __ACCOUNTNAME__ por el nombre de la cuenta de almacenamiento:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Esto devolverá la clave principal de la cuenta.

También puede encontrar la información de almacenamiento mediante el portal de Azure:

1. En el [Portal de Azure](https://portal.azure.com/), seleccione el clúster de HDInsight.

2. En la sección __Essentials__, seleccione __Todas las configuraciones__.

3. En __Configuración__, selecciona __Claves de almacenamiento de Azure__.

4. En __Claves de almacenamiento de Azure__, seleccione una de las cuentas de almacenamiento que aparecen en la lista. Entonces, se mostrará información sobre la cuenta de almacenamiento.

5. Selecciona el icono de la llave. Esto mostrará las claves para esta cuenta de almacenamiento.

### ¿Cómo obtengo acceso al almacenamiento de blobs?

Además de mediante el comando Hadoop desde el clúster, existe una variedad de formas para tener acceso a los blobs:

* [CLI de Azure para Mac, Linux y Windows](../xplat-cli-install.md): comandos de interfaz de línea de comandos para trabajar con Azure. Después de la instalación, use el comando `azure storage` para obtener ayuda sobre el uso del almacenamiento o `azure blob` para comandos específicos para los blobs.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): un script de Python para trabajar con blobs en almacenamiento de Azure.

* Una variedad de SDK:

	* [Java](https://github.com/Azure/azure-sdk-for-java)

	* [Node.js](https://github.com/Azure/azure-sdk-for-node)

	* [PHP](https://github.com/Azure/azure-sdk-for-php)

	* [Python](https://github.com/Azure/azure-sdk-for-python)

	* [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

	* [.NET](https://github.com/Azure/azure-sdk-for-net)

* [API de REST de almacenamiento](https://msdn.microsoft.com/library/azure/dd135733.aspx)

##<a name="scaling"></a>Escalar el clúster

La característica de escalado de clúster permite cambiar la cantidad de nodos de datos que usa un clúster en ejecución en HDInsight de Azure sin necesidad de eliminar el clúster y volver a crearlo.

Puedes realizar operaciones de escala mientras se están ejecutando otros trabajos o procesos en un clúster.

Los diferentes tipos de clúster se ven afectados por la escala de esta manera:

* __Hadoop__: al reducir verticalmente el número de nodos en un clúster, se reinician algunos de los servicios del clúster. Esto puede provocar que los trabajos pendientes y en ejecución fallen al completarse la operación de escalado. Sin embargo, puedes volver a enviar los trabajos una vez finalizada la operación.

* __HBase__: los servidores regionales se equilibran automáticamente en unos pocos minutos tras completar la operación de escalado. Para equilibrar manualmente servidores regionales, sigue estos pasos:

	1. Conéctate al clúster de HDInsight con SSH: Para obtener más información sobre el uso de SSH con HDInsight, consulta uno de los siguientes documentos:

		* [Uso de SSH con HDInsight de Linux, Unix y Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

		* [Uso de SSH con HDInsight de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

	1. Usa lo siguiente para iniciar el shell de HBase:

			hbase shell

	2. Una vez que se haya cargado el shell de HBase, usa lo siguiente para equilibrar manualmente los servidores regionales:

			balancer

* __Storm__: debe reequilibrar todas las topologías Storm en ejecución después de haber realizado una operación de escalado. Esto permite la topología volver a ajustar la configuración de paralelismo en función del nuevo número de nodos del clúster. Para volver a equilibrar las topologías en ejecución, usa una de las siguientes opciones:

	* __SSH__: conéctese al servidor y use el siguiente comando para volver a equilibrar una topología:

			storm rebalance TOPOLOGYNAME

		También puedes especificar parámetros para reemplazar las sugerencias de paralelismo que proporcionó originalmente la topología. Por ejemplo, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` volverá a configurar la topología en 5 procesos de trabajo, 3 ejecutores para el componente blue-spout y 10 ejecutores para el componente yellow-bolt.

	* __Interfaz de usuario de Storm__: siga estos pasos para reequilibrar una topología mediante la interfaz de usuario de Storm.

		1. Abra \_\___https://CLUSTERNAME.azurehdinsight.net/stormui__ en el explorador web, donde CLUSTERNAME es el nombre del clúster de Storm. Si se le solicite, escriba el nombre de administrador (admin) del clúster de HDInsight y la contraseña que especificó al crear el clúster.

		3. Selecciona la topología que quieres equilibrar y después selecciona el botón __Reequilibrar__. Especifica el retraso antes de realizar la operación de reequilibrio.

Para obtener información específica sobre cómo ampliar tu clúster de HDInsight, consulta:

* [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure](hdinsight-administer-use-portal-linux.md#scaling)

* [Administración de clústeres de Hadoop en HDInsight con PowerShell de Azure](hdinsight-administer-use-command-line.md#scaling)

## ¿Cómo puedo instalar Hue (u otro componente de Hadoop)?

HDInsight es un servicio administrado, lo que significa que Azure puede destruir y volver a aprovisionar automáticamente los nodos de un clúster si se detecta un problema. Por este motivo, no se recomienda instalar nada de forma manual en los nodos del clúster directamente. En su lugar, use las [acciones de script de HDInsight](hdinsight-hadoop-customize-cluster.md) cuando necesite instalar lo siguiente:

* Un servicio o sitio web como Spark o Hue.
* Un componente que requiera cambios de configuración en varios nodos del clúster. Por ejemplo, una variable de entorno requerida o la creación de un directorio de registro o de un archivo de configuración.

Las acciones de script son scripts de Bash que se ejecutan durante el aprovisionamiento del clúster. Se pueden usar para instalar y configurar componentes adicionales en el clúster. Se proporcionan scripts de ejemplo para instalar los componentes siguientes:

* [Hue](hdinsight-hadoop-hue-linux.md)
* [Giraph.](hdinsight-hadoop-giraph-install-linux.md)
* [R](hdinsight-hadoop-r-scripts-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)
* [Spark](hdinsight-hadoop-spark-install-linux.md)

Para obtener información sobre el desarrollo de sus propias acciones de script, consulte [Desarrollo de la acción de script con HDInsight](hdinsight-hadoop-script-actions-linux.md).

###Archivos JAR

Algunas tecnologías de Hadoop se proporcionan en archivos jar independientes con funciones que se usan como parte de un trabajo de MapReduce o desde dentro de Pig o Hive. Aunque se pueden instalar mediante acciones de script, a menudo, no requieren ninguna configuración y se pueden cargar simplemente en el clúster después de haberlos aprovisionado y usado directamente. Si desea asegurarse de que el componente sobreviva a la creación de una nueva imagen del clúster, puede almacenar el archivo jar en WASB.

Por ejemplo, si desea usar la versión más reciente de [DataFu](http://datafu.incubator.apache.org/), puede descargar un archivo jar que contiene el proyecto y cargarlo en el clúster de HDInsight. A continuación, siga las instrucciones que aparecen en la documentación de DataFu sobre el uso con Pig o Hive.

> [AZURE.IMPORTANT]Algunos componentes que son archivos jar independientes se proporcionan con HDInsight, pero no están en la ruta de acceso. Si está buscando un componente específico, puede utilizar lo siguiente para buscarlo en el clúster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Esto devolverá la ruta de los archivos jar coincidentes.

Si el clúster le proporciona una versión de un componente como un archivo jar independiente, pero desea utilizar una versión diferente, puede cargar una nueva versión del componente en el clúster y probar a usarla en sus trabajos.

> [AZURE.WARNING]Los componentes proporcionados con HDInsight son totalmente compatibles. Además, el soporte técnico de Microsoft le ayudará a aislar y resolver problemas relacionados con estos componentes.
>
> Los componentes personalizados reciben soporte técnico comercialmente razonable para ayudarle a solucionar el problema. Esto podría resolver el problema o pedirle que forme parte de los canales disponibles para las tecnologías de código abierto donde se encuentra la más amplia experiencia para esa tecnología. Por ejemplo, hay diversos sitios de la comunidad que se pueden usar, como el [foro de MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/es-ES/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Además, los proyectos de Apache tienen sitios del proyecto en [http://apache.org](http://apache.org), por ejemplo, [Hadoop](http://hadoop.apache.org/) y [Spark](http://spark.apache.org/).

## Pasos siguientes

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de trabajos de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0114_2016-->