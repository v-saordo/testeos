<properties
   	pageTitle="Creación de clústeres de Hadoop, HBase, Storm o Spark en Linux en HDInsight | Microsoft Azure"
   	description="Aprenda a crear clústeres de Hadoop, HBase, Storm o Spark en Linux para HDInsight con un explorador web, la CLI de Azure, Azure PowerShell, REST o mediante un SDK."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="01/28/2016"
   	ms.author="nitinme"/>


#Creación de clústeres de Hadoop basados en Linux en HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

En este documento, obtendrá información sobre las diferentes maneras de crear un clúster de HDInsight basado en Linux en Azure, así como las configuraciones opcionales que se pueden usar con el clúster. HDInsight proporciona Apache Hadoop, Apache Storm y Apache HBase como servicios en la plataforma de nube de Azure.

> [AZURE.NOTE] En este documento se proporcionan instrucciones sobre diferentes formas de crear un clúster. Si busca un enfoque rápido para crear un clúster, consulte [Introducción a HDInsight de Azure en Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

## ¿Qué es un clúster de HDInsight?

Un clúster de Hadoop se compone de varias máquinas virtuales (nodos), que se usan para el procesamiento distribuido de tareas en el clúster. Azure abstrae de los detalles de implementación de la instalación y configuración de los nodos individuales, por lo que solo tiene que proporcionar información de configuración general.

###Tipos de clúster

Hay varios tipos de HDInsight disponibles:

| Tipo de clúster | Úselo si necesita... |
| ------------ | ----------------------------- |
| Hadoop | consultas y análisis (trabajos por lotes) |
| HBase | Almacenamiento de datos NoSQL |
| Storm | Procesamiento de eventos en tiempo real |
| Spark (vista preliminar) | Procesamiento en memoria, consultas interactivas, procesamiento de transmisión de microlotes |

Durante la configuración, seleccionará uno de estos tipos para el clúster. Puede agregar otras tecnologías como Hue o R a estos tipos básicos mediante el uso de [acciones de script](#scriptaction).

Cada tipo de clúster tiene su propia terminología de nodos dentro del clúster, así como el número de nodos y el tamaño de memoria virtual predeterminada para cada tipo de nodo:

> [AZURE.IMPORTANT] Si planea crear más de 32 nodos de trabajo, en la creación de clústeres o cambiando el tamaño del clúster después de la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.

![Nodos de clúster de Hadoop en HDInsight](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

Los clústeres de Hadoop para HDInsight tienen dos nodos de tipos:

- Nodo principal (2 nodos)
- Nodo de datos (al menos 1 nodo)

![Nodos de clúster de HBase en HDInsight](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

Los clústeres de HBase para HDInsight tienen tres tipos de nodos: - Servidores principales (2 nodos) - Servidores regionales (al menos 1 nodo) - Nodos principales/Zookeeper (3 nodos)

![Nodos de clúster de Storm en HDInsight](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

Los clústeres de Storm para HDInsight tienen tres tipos de nodos: - Nodos Nimbus (2 nodos) - Servidores de supervisor (al menos 1 nodo) - Nodos Zookeeper (3 nodos)


![Nodos de clúster de Spark en HDInsight](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

Los clústeres de Spark para HDInsight tienen tres tipos de nodos: - Nodo principal (2 nodos) - Nodo de trabajo (al menos 1 nodo) - Nodos de Zookeeper (3 nodos) (gratis para tamaño de máquina virtual Zookeepers A1)

###Almacenamiento de Azure para HDInsight

Cada tipo de clúster también tendrá una o más cuentas de Almacenamiento de Azure asociadas al clúster. HDInsight usa los blobs de Azure desde estas cuentas de almacenamiento como almacén de datos para el clúster. El mantenimiento de los datos separados del clúster permite eliminar clústeres cuando no están en uso y seguir conservando los datos. Después puede usar la misma cuenta de almacenamiento para un clúster nuevo si necesita realizar más análisis. Para obtener más información, consulte [Uso de almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a id="configuration"></a>Opciones de configuración básica

En las secciones siguientes se describen las opciones de configuración disponibles y necesarias al crear un clúster de HDInsight.

###Nombre del clúster

El nombre del clúster proporciona un identificador único para el clúster y se usa como parte del nombre de dominio para acceder al clúster por Internet. Por ejemplo, estará disponible un clúster denominado _miClúster_ en Internet como _miClúster_.azurehdinsight.net.

El nombre de clúster debe seguir las siguientes directrices:

- El campo debe contener una cadena entre 3 y 63 caracteres
- El campo solo puede contener letras, números y guiones.

###Tipo de clúster

El tipo de clúster le permite seleccionar configuraciones de propósito especial para el clúster. Estos son los tipos disponibles para HDInsight basados en Linux:

| Tipo de clúster | Úselo si necesita... |
| ------------ | ----------------------------- |
| Hadoop | consultas y análisis (trabajos por lotes) |
| HBase | Almacenamiento de datos NoSQL |
| Storm | Procesamiento de eventos en tiempo real |
| Spark (vista preliminar) | Procesamiento en memoria, consultas interactivas, procesamiento de transmisión de microlotes |

Puede agregar otras tecnologías como Hue o R a estos tipos básicos mediante el uso de [acciones de script](#scriptaction).

###Sistema operativo para clústeres

Puede aprovisionar clústeres de HDInsight en uno de los dos sistemas operativos siguientes:

- **HDInsight en Windows (Windows Server 2012 R2 Datacenter)**: seleccione esta opción para integrarlos con servicios de Windows y tecnologías que se ejecutan en el clúster con Hadoop, o si realiza una migración desde una distribución de Hadoop para Windows existente.

- **HDInsight en Linux (Ubuntu 12.04 LTS para Linux)**: seleccione esta opción si conoce Linux o Unix, si realiza una migración desde una solución de Hadoop para Linux existente o si desea una integración simple con los componentes del ecosistema de Hadoop creados para Linux. Para obtener más información, vea [Introducción a Hadoop en Linux en HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

> [AZURE.NOTE] En este documento se supone que usa un clúster de HDInsight basado en Linux. Para obtener información específica de los clústeres basados en Windows, consulte [Creación de clústeres de Hadoop basado en Windows en HDInsight](hdinsight-provision-clusters.md).

###Nombre de la suscripción

Si tiene varias suscripciones de Azure, úselo para seleccionar el que desea usar al crear el clúster de HDInsight.

###Grupos de recursos

Las aplicaciones normalmente se componen de muchos componentes,por ejemplo una aplicación web, base de datos, servidor de base de datos, almacenamiento y servicios de terceros. El Administrador de recursos de Azure (ARM) permite trabajar con los recursos de la aplicación como un grupo al que se hace referencia como Grupo de recursos de Azures Puede implementar, actualizar, supervisar o eliminar todos los recursos de la aplicación en una operación única y coordinada. Para la implementación se utiliza una plantilla, y esta plantilla puede trabajar en diferentes entornos, como pruebas, ensayo y producción. Puede aclarar la facturación de la organización consultando los costes acumulados de todo el grupo. Para obtener más información, consulte [Información general del Administrador de recursos de Azure](../resource-group-overview.md).

###Credenciales

Hay dos tipos de autenticación usados con los clústeres de HDInsight:

* Usuario __admin__ o __HTTPs__: la cuenta del administrador para un clúster se usa principalmente cuando se tiene acceso a servicios web o a servicios de REST expuestos por el clúster. No se puede usar para iniciar sesión directamente en el clúster.

* __Nombre de usuario de SSH__: se usa la cuenta de usuario de SSH para tener acceso remoto al clúster mediante un cliente de [Shell seguro](https://en.wikipedia.org/wiki/Secure_Shell). Se usa con frecuencia para proporcionar un acceso remoto de línea de comandos a los nodos principales del clúster.

La cuenta de administrador está protegida con contraseña y todas las comunicaciones web con el clúster mediante la cuenta de administrador deben realizarse a través de una conexión HTTPS segura.

Se puede autenticar el usuario de SSH con una contraseña o un certificado. La autenticación mediante un certificado es la opción más segura, aunque requiere la creación y el mantenimiento de un par de certificados, uno público y otro privado.

Para obtener más información sobre el uso de SSH con HDInsight, incluso cómo crear y usar las claves SSH, consulte uno de los artículos siguientes:

* [Para los clientes de Linux, Unix o Mac OS X: uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Para los clientes de Windows: uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md)

###Origen de datos

HDInsight usa el almacenamiento de blobs de Azure como almacenamiento subyacente para el clúster. Internamente, Hadoop y otro software en el clúster ve este almacenamiento como un sistema compatible con el sistema de archivos distribuido de Hadoop (HDFS).

Al crear un nuevo clúster, debe crear una nueva cuenta de almacenamiento de Azure o usar una existente.

> [AZURE.IMPORTANT] La ubicación geográfica que seleccione para la cuenta de almacenamiento determinará la ubicación del clúster de HDInsight, ya que el clúster debe estar en el mismo centro de datos que la cuenta de almacenamiento predeterminada.
>
> Si desea obtener una lista de las regiones admitidas, haga clic en la lista desplegable **Región** en [Precios de HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

####Contenedor de almacenamiento predeterminado

HDInsight también creará un _contenedor de almacenamiento predeterminado_ en la cuenta de almacenamiento. Se trata del almacenamiento predeterminado para el clúster de HDInsight.

De forma predeterminada, este contenedor tiene el mismo nombre que el del clúster. Para obtener más información sobre cómo funciona HDInsight con el almacenamiento de blobs de Azure, consulte [Uso del almacenamiento de blobs de Azure compatibles con HDFS con Hadoop en HDInsight](hdinsight-hadoop-use-blob-storage.md).

>[AZURE.WARNING] No comparta un contenedor para varios clústeres, ya que no es compatible.

###Tamaño del nodo

Puede seleccionar el tamaño de los recursos de proceso usados por el clúster. Por ejemplo, si sabe que va a realizar las operaciones que necesitan una gran cantidad de memoria, es posible que quiera seleccionar un recurso de proceso con más memoria.

>[AZURE.NOTE] Los nodos de clúster utilizados por el clúster no se consideran máquinas virtuales, ya que las imágenes de máquinas virtuales que se usan para los nodos son un detalle de implementación del servicio de HDInsight. Sin embargo, los núcleos de proceso utilizados por los nodos sí cuentan para el número total de núcleos de proceso disponibles para su suscripción. Puede ver el número de núcleos que se utilizarán en el clúster, así como el número de núcleos disponibles, en la sección de resumen de la hoja Plan de tarifa de nodos al crear un clúster de HDInsight.

Los distintos tipos de clúster tienen tipos de nodos, número de nodos y tamaños de nodo diferentes. Por ejemplo, un tipo de clúster de Hadoop tiene dos _nodos principales_ y su valor predeterminado es de cuatro _nodos de datos_, mientras que un tipo de clúster Storm tiene dos _nodos nimbus_, tres _nodos zookeeper_ y su valor predeterminado es de cuatro _nodos de supervisor_.

> [AZURE.IMPORTANT] Si planea crear más de 32 nodos de trabajo, en la creación de clústeres o al cambiar el tamaño del clúster después de la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.

Al usar el Portal de vista previa de Azure para configurar el clúster, el tamaño de nodo se expone a través de la hoja __Plan de tarifas de nodo__ y también debe mostrar el costo asociado con los distintos tamaños de nodo.

> [AZURE.IMPORTANT] La facturación se inicia una vez creado el clúster y solo se detiene cuando se elimina el clúster. Para obtener más información sobre los precios, consulte [Detalles de precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a id="optionalconfiguration"></a>Configuración opcional

Las secciones siguientes describen las opciones de configuración opcionales, así como los escenarios que requieren estas configuraciones.

### Versión de HDInsight

Se usa para determinar la versión de HDInsight que usará este clúster. Para obtener más información, consulte [Componentes y versiones de clústeres de Hadoop en HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

### Uso de redes virtuales de Azure

Una [Red virtual de Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permite crear una red segura y persistente que contenga los recursos que necesita para la solución. Una red virtual permite hacer lo siguiente:

* Conectar recursos en la nube en una red privada (solo en la nube)

	![Diagrama de la configuración solo en la nube](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* Conectar sus recursos en la nube a la red de su centro de datos local (sitio a sitio o punto a sitio) usando una red privada virtual (VPN).

    | Configuración de sitio a sitio | Configuración de punto a sitio |
    | -------------------------- | --------------------------- |
    | La configuración sitio a sitio permite conectar varios recursos de su centro de datos a la red virtual de Azure usando una VPN de hardware o el servicio de enrutamiento y acceso remoto.<br />![Diagrama de la configuración de sitio a sitio](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | La configuración punto a sitio permite conectar un recurso específico a la red virtual de Azure usando una VPN de software.<br />![Diagrama de la configuración de punto a sitio](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Para obtener información sobre el uso de HDInsight con una red virtual, incluidos los requisitos de configuración específicos de la red virtual, consulte [Extensión de las funcionalidades de HDInsight con Red virtual de Azure](hdinsight-extend-hadoop-virtual-network.md).

### Tienda de metadatos

La tienda de metadatos contiene metadatos de Hive y Oozie, como información sobre tablas de Hive, particiones, esquemas y columnas. El uso de la tienda de metadatos le ayuda a conservar sus metadatos de Hive y Oozie, por lo que no es necesario volver a crear tablas de Hive o trabajos de Oozie al crear un nuevo clúster.

Con la opción de configuración de la tienda de metadatos podrá especificar una tienda de metadatos externa con la Base de datos SQL. Esto permite conservar la información de metadatos cuando se elimina un clúster, porque se almacena externamente en la base de datos. Para obtener instrucciones sobre cómo crear una Base de datos SQL en Azure, consulte [Creación de la primera Base de datos SQL de Azure](../sql-database/sql-database-get-started.md).

> [AZURE.NOTE] La configuración de la tienda de metadatos no está disponible para los tipos de clúster de HBase.

###<a id="scriptaction"></a>Acción de script

Puede instalar componentes adicionales o personalizar la configuración del clúster mediante scripts durante el aprovisionamiento de clúster. Dichos scripts se invocan mediante la **acción de script**. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster-linux.md).

> [AZURE.IMPORTANT] No se admite agregar componentes adicionales después de que se haya creado un clúster, ya que dichos componentes no estarán disponibles después de que se restablezca la imagen inicial de un nodo de clúster. Los componentes instalados a través de acciones de script se reinstalan como parte del proceso de restablecimiento de la imagen inicial.

### Almacenamiento adicional

En algunos casos, es posible que desee agregar almacenamiento adicional al clúster. Por ejemplo, dispone de varias cuentas de almacenamiento de Azure para diferentes regiones geográficas, o para distintos servicios, pero desea analizarlas con HDInsight.

Para obtener más información sobre el uso de almacenes de blobs secundarios, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

##<a id="nextsteps"></a><a id="options"></a> Métodos de creación

En este artículo, aprendió información básica acerca de cómo crear un clúster de HDInsight basado en Linux. Use la siguiente tabla para buscar información específica acerca de cómo crear un clúster mediante un método que mejor se adapte a sus necesidades:

| Úselo para crear un clúster... | Mediante un explorador web... | Mediante una línea de comandos | Mediante la API de REST | Mediante un SDK | Desde Linux, Mac OS X o Unix | Desde Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Portal de vista previa de Azure](hdinsight-hadoop-create-linux-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [CLI de Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |


[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage/storage-create-storage-account.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[azure-preview-portal]: https://portal.azure.com


[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Enumeración y visualización de clústeres"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Uso de Sqoop con HDInsight"

<!---HONumber=AcomDC_0224_2016-->