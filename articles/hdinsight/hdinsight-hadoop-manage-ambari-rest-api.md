<properties
   pageTitle="Supervisión y administración de clústeres de HDInsight con la API de REST de Apache Ambari | Microsoft Azure"
   description="Aprenda a usar Ambari para supervisar y administrar clústeres de HDInsight basado en Linux. En este documento, aprenderá a usar la API de REST de Ambari incluida con clústeres de HDInsight."
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

#Administración de clústeres de HDInsight con la API de REST de Ambari

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifica la administración y la supervisión de un clúster de Hadoop al brindar una API de REST y una interfaz de usuario web fácil de usar. Ambari se incluye en los clústeres de HDInsight basado en Linux y, además, se usa para supervisar el clúster y realizar cambios en la configuración. En este documento, aprenderá los conceptos básicos de trabajar con la API de REST de Ambari al realizar tareas comunes, como encontrar el nombre de dominio completo de los nodos de clúster o encontrar la cuenta de almacenamiento predeterminada que utiliza el clúster.

> [AZURE.NOTE]La información que aparece en este artículo solo se aplica a clústeres de HDInsight basado en Linux. En el caso de los clústeres de HDInsight basado en Windows, solo hay disponible un subconjunto de funcionalidad de supervisión a través de la API de REST de Ambari. Consulte [Supervisión de Hadoop en HDInsight basado en Windows con la API de Ambari](hdinsight-monitor-use-ambari-api.md).

##Requisitos previos

* [cURL](http://curl.haxx.se/): cURL es una utilidad multiplataforma que se puede usar para trabajar con las API de REST desde la línea de comandos. En este documento, se usa para comunicarse con la API de REST de Ambari.
* [jq](https://stedolan.github.io/jq/): jq es una utilidad de línea de comandos multiplataforma para trabajar con documentos JSON. En este documento, se usa para analizar los documentos JSON devueltos desde la API de REST de Ambari.

##<a id="whatis"></a> ¿Qué es Ambari?

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> simplifica la administración de Hadoop al proporcionar una interfaz de usuario web fácil de usar que se puede utilizar para aprovisionar, administrar y supervisar clústeres de Hadoop. Los desarrolladores pueden integrar estas funcionalidades en sus aplicaciones mediante el uso de las <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">API de REST de Ambari</a>.

De manera predeterminada, Ambari viene con los clústeres de HDInsight basado en Linux.

##API de REST

El URI base de la API de REST de Ambari en HDInsight es https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, donde __CLUSTERNAME__ es el nombre del clúster.

> [AZURE.IMPORTANT]La conexión a Ambari en HDInsight requiere HTTPS. También debe autenticarse en Ambari mediante el nombre de la cuenta de administrador (el valor predeterminado es __admin__) y la contraseña que proporcionó cuando se creó el clúster.

A continuación, aparece un ejemplo de cómo usar cURL para realizar una solicitud GET en la API de REST:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
Si la ejecuta, reemplazando __PASSWORD__ por la contraseña de administrador del clúster y __CLUSTERNAME__ por el nombre del clúster, recibirá un documento JSON que comienza con información similar a la siguiente:

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Como es un documento JSON, normalmente resulta más fácil usar un analizador JSON para recuperar datos. Por ejemplo, si desea recuperar un recuento de alertas (contenidas en el elemento __"Host/host\_status/ALERT"__), puede usar lo siguiente para tener acceso directamente al valor:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report."Host/host_status/ALERT"'
    
Con esto se recupera el documento JSON y, luego, se canaliza el resultado a jq. `'.Clusters.health_report."Host/host_status/ALERT"'` indica el elemento dentro del documento JSON que desea recuperar.

> [AZURE.NOTE]El elemento __Host/host\_status/ALERT__ aparece entre comillas para indicar que "/" es parte del nombre del elemento. Para obtener más información sobre cómo usar jq, consulte el [sitio web de jq](https://stedolan.github.io/jq/).

##Ejemplo: Obtener el FQDN de los nodos del clúster

Cuando trabaja con HDInsight, es posible que deba conocer el nombre de dominio completo (FQDN) del nodo de un clúster. Puede recuperar fácilmente el FQDN de varios nodos del clúster con lo siguiente:

* __Nodos principales__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __Nodos de trabajo__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Nodos Zookeeper__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Observe que todos estos nodos siguen el mismo patrón de consultar a un componente que sabemos que se ejecuta en esos nodos y, luego, recuperar los elementos `host_name` que contienen el FQDN de estos nodos.

El elemento `host_components` del documento devuelto contiene varios elementos. Con `.host_components[]` y, luego, al especificar una ruta de acceso dentro del elemento se aplicará un bucle a través de cada elemento y se extraerá el valor de la ruta de acceso específica. Si solo desea un valor, como la primera entrada de FQDN, puede devolver los elementos como una colección y, luego, seleccionar una entrada específica:

    jq '[.host_components[].HostRoles.host_name][0]'

Esto devolverá el primer FQDN de la colección.

##Ejemplo: Obtener el contenedor y la cuenta de almacenamiento predeterminados

Cuando crea un clúster de HDInsight, debe usar una cuenta de almacenamiento de Azure y un contenedor de blobs como el almacenamiento predeterminado para el clúster. Puede usar Ambari para recuperar esta información una vez creado el clúster. Por ejemplo, si desea escribir datos de manera programática directamente en el contenedor.

Lo siguiente recuperará el URI de WASB del almacenamiento predeterminado de los clústeres:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE]Esto devolverá la primera configuración aplicada al servidor (`service_config_version=1`), la que contendrá esta información. Si recupera un valor que se modificó después de la creación del clúster, es posible que deba enumerar las versiones de configuración y recuperar la más reciente.

Esto devolverá un valor similar al siguiente, donde __CONTAINER__ es el contenedor predeterminado y __ACCOUNTNAME__ es el nombre de la cuenta de almacenamiento de Azure:

    wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net

Luego, puede usar esta información con la [CLI de Azure](../xplat-cli-install.md) para cargar y descargar datos del contenedor. Por ejemplo:

1. Obtenga el grupo de recursos para la cuenta de almacenamiento. Reemplace __ACCOUNTNAME__ por el nombre de la cuenta de almacenamiento que se recuperó de Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Esto devolverá el nombre del grupo de recursos de la cuenta.
    
    > [AZURE.NOTE]Si este comando no devuelve nada, debe cambiar la CLI de Azure al modo Administrador de recursos de Azure y ejecutar el comando de nuevo. Para cambiar al modo Administrador de recursos de Azure, use el siguiente comando:
    >
    > `azure config mode arm`
    
2. Obtenga la clave de la cuenta de almacenamiento. Reemplace __GROUPNAME__ por el grupo de recursos del paso anterior. Reemplace __ACCOUNTNAME__ por el nombre de la cuenta de almacenamiento:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Esto devolverá la clave principal de la cuenta.
    
3. Use el comando upload para almacenar un archivo en el contenedor:

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Reemplace __ACCOUNTNAME__ por el nombre de la cuenta de almacenamiento. Reemplace __ACCOUNTKEY__ por la clave que se recuperó anteriormente. __FILEPATH__ es la ruta de acceso al archivo que desea cargar, mientras que __BLOBPATH__ es la ruta de acceso en el contenedor.

    Por ejemplo, si desea que el archivo aparezca en HDInsight en wasb://example/data/filename.txt, __BLOBPATH__ sería `example/data/filename.txt`.

##Pasos siguientes

Para obtener una referencia completa de la API de REST, consulte [Referencia de API de Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE]Cierta funcionalidad de Ambari está deshabilitada, puesto que está administrada por el servicio en la nube de HDInsight; por ejemplo, agregar o quitar hosts del clúster o agregar nuevos servicios.

<!---HONumber=AcomDC_0114_2016-->