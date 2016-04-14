<properties
   pageTitle="Creación de clústeres de Hadoop basados en Windows en HDInsight | Microsoft Azure"
   	description="Aprenda a crear clústeres para HDInsight de Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/12/2016"
   ms.author="jgao"/>

# Creación de clústeres de Hadoop basados en Windows en HDInsight

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Obtenga información acerca de cómo planear la creación de clústeres de HDInsight.

###Requisitos previos:

Antes de empezar las instrucciones de este artículo, debe tener lo siguiente:

- Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Opciones de configuración básica

A continuación se presentan las opciones de configuración básicas para crear un clúster de HDInsight.

- **Nombre del clúster**

	El nombre del clúster se utiliza para identificar un clúster. El nombre de clúster debe seguir las siguientes directrices:

	- El campo debe ser una cadena que contenga entre 3 y 63 caracteres
	- El campo solo puede contener letras, números y guiones.

- **Nombre de la suscripción**

	Un clúster de HDInsight está asociado a una suscripción a Azure.

- **Definición de un nombre de grupo de recursos**

	El Administrador de recursos de Azure (ARM) permite trabajar con los recursos de la aplicación como un grupo al que se hace referencia como Grupo de recursos de Azures Puede implementar, actualizar, supervisar o eliminar todos los recursos de la aplicación en una operación única y coordinada. Para obtener más información, consulte [Información general del Administrador de recursos de Azure](resource-group-overview.md).
	
- **Sistema operativos**

	Puede crear clústeres de HDInsight en uno de los dos sistemas operativos siguientes:
	- **HDInsight en Windows (Windows Server 2012 R2 Datacenter)**:
	- **HDInsight en Linux (Ubuntu 12.04 LTS para Linux)**: HDInsight ofrece la opción de configurar clústeres de Linux en Azure. Configure un clúster de Linux si conoce Linux o Unix, migrando desde una solución existente de Hadoop basado en Linux, o si desea una integración fácil con componentes del ecosistema de Hadoop creados para Linux. Para obtener más información, vea [Introducción a Hadoop en Linux en HDInsight](hdinsight-hadoop-linux-get-started.md).

- **Tipo de clúster** y **tamaño del clúster (también conocidos como nodos de datos)**

	HDInsight permite a los clientes implementar varios tipos de clúster para diferentes cargas de trabajo de análisis de datos. Los tipos de clústeres que se ofrecen hoy son:

	- Clústeres de Hadoop: para cargas de trabajo de consulta y análisis
	- Clústeres de HBase: para cargas de trabajo NoSQL
	- Clústeres de Storm: para cargas de trabajo de procesamiento de eventos en tiempo real
	- Clústeres de Spark: para procesamiento en memoria, consultas interactivas, transmisiones y cargas de trabajo de aprendizaje automático

	![Clústeres de HDInsight](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE] El *clúster de HDInsight de Azure* se llama también *clúster de Hadoop en HDInsight* o *clúster de HDInsight*. En algunas ocasiones, se usa indistintamente con el *clúster de Hadoop*. Todos estos nombres se refieren a los clústeres de Hadoop hospedados en el entorno de Microsoft Azure.

	Dentro de un tipo de clúster determinado, existen distintos roles para los diversos nodos, los que permiten que un cliente dimensione esos nodos en un rol determinado según los detalles de su carga de trabajo. Por ejemplo, un clúster de Hadoop puede crear sus nodos de trabajo con una gran cantidad de memoria si el tipo de análisis que se realiza requiere mucha memoria.

	![Roles de clúster de Hadoop en HDInsight](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	Los clústeres de Hadoop para HDInsight se implementan con dos roles:

	- Nodo principal (2 nodos)
	- Nodo de datos (al menos 1 nodo)

	![Roles de clúster de Hadoop en HDInsight](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	Los clústeres de HBase para HDInsight se implementan con tres roles: 
	- Servidores principales (2 nodos) 
	- Servidores regionales (al menos 1 nodo) 
	- Nodos principales/Zookeeper (3 nodos)

	![Roles de clúster de Hadoop en HDInsight](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	Los clústeres de Storm para HDInsight se implementan con tres roles: 
	- Nodos Nimbus (2 nodos) 
	- Servidores de supervisor (al menos 1 nodo) 
	- Nodos Zookeeper (3 nodos)


	![Roles de clúster de Hadoop en HDInsight](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	Los clústeres de Spark para HDInsight se implementan con tres roles: 
	- Nodo principal (2 nodos) 
	- Nodo de trabajo (al menos 1 nodo) 
	- Nodos de Zookeeper (3 nodos) (gratis para Zookeepers A1)

	El uso de esos nodos se factura a los clientes por la duración del clúster. La facturación comienza una vez que se crea un clúster y se detiene cuando se elimina el clúster (no es posible eliminar la asignación de los clústeres ni tampoco se pueden poner en espera) El tamaño del clúster afecta el precio del mismo. Con fines de aprendizaje, se recomienda utilizar 1 nodo de datos. Para más información sobre los precios de HDInsight, vea [HDInsight Precios](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE] El límite de tamaño del clúster varía entre las suscripciones a Azure. Póngase en contacto con el servicio de soporte relacionado con la facturación para aumentar el límite.

- **Versión de HDInsight**

	Se utiliza para determinar la versión de HDInsight que se utilizará para este clúster. Para obtener más información, consulte [Componentes y versiones de clústeres de Hadoop en HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)


- **Ubicación (región)**

	El clúster de HDInsight y su cuenta de almacenamiento predeterminada deben colocarse en la misma ubicación de Azure.
	
	![Regiones de Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

	Si desea obtener una lista de las regiones admitidas, haga clic en la lista desplegable **Región** en los [precios de HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Tamaño del nodo**

	![tamaños de nodos de vm de hdinsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	Seleccione el tamaño de VM para los nodos. Para obtener más información, consulte [Tamaños de los servicios en la nube](cloud-services-sizes-specs.md). Puede seleccionar el tamaño de los recursos de proceso usados por el clúster. Por ejemplo, si sabe que va a realizar las operaciones que necesitan una gran cantidad de memoria, es posible que quiera seleccionar un recurso de proceso con más memoria.

	>[AZURE.NOTE] Los nodos de clúster utilizados por el clúster no se consideran máquinas virtuales, ya que las imágenes de máquinas virtuales que se usan para los nodos son un detalle de implementación del servicio de HDInsight. Sin embargo, los núcleos de proceso utilizados por los nodos sí cuentan para el número total de núcleos de proceso disponibles para su suscripción. Puede ver el número de núcleos que se utilizarán en el clúster, así como el número de núcleos disponibles, en la sección de resumen de la hoja Plan de tarifa de nodos al crear un clúster de HDInsight.

	Los distintos tipos de clúster tienen tipos de nodos, número de nodos y tamaños de nodo diferentes. Por ejemplo, un tipo de clúster de Hadoop tiene dos _nodos principales_ y su valor predeterminado es de cuatro _nodos de datos_, mientras que un tipo de clúster Storm tiene dos _nodos nimbus_, tres _nodos zookeeper_ y su valor predeterminado es de cuatro _nodos de supervisor_.

	> [AZURE.IMPORTANT] Si planea crear más de 32 nodos de trabajo, en la creación de clústeres o al cambiar el tamaño del clúster después de la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.

	Si usa el Portal de Azure para configurar el clúster, el tamaño del nodo estará disponible a través de la hoja __Plan de tarifas de nodo__, y aparecerá también el costo asociado con los distintos tamaños de nodo.

	> [AZURE.IMPORTANT] La facturación se inicia una vez creado el clúster y solo se detiene cuando se elimina el clúster. Para obtener más información sobre los precios, consulte [Detalles de precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


- **Usuarios de HDInsight**

	Los clústeres de HDInsight le permiten configurar dos cuentas de usuario durante el aprovisionamiento:

	- Usuario de HTTP. El nombre de usuario predeterminado es admin durante la configuración básica en el Portal de Azure.
	- Usuario de RDP (clústeres de Windows): se usa para conectarse al clúster mediante RDP. Cuando crea la cuenta, debe establecer una fecha de caducidad que se encuentre dentro de 90 días a contar del día de hoy.
	- Usuario SSH (clústeres de Linux): se usa para conectarse al clúster mediante SSH. Puede crear cuentas de usuario SSH adicional una vez que se cree el clúster siguiendo los pasos en [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md).



- **Cuenta de Almacenamiento de Azure**

	El HDFS original usa muchos discos locales en el clúster. En su lugar, HDInsight utiliza el almacenamiento de blobs de Azure para el almacenamiento de datos. El almacenamiento de blobs de Azure es una solución de almacenamiento sólida y de uso general, que se integra sin problemas con HDInsight. A través de una interfaz del sistema de archivos distribuidos de Hadoop (HDFS), el conjunto completo de componentes de HDInsight puede operar directamente en datos estructurados o no estructurados en el almacenamiento de blobs. Almacenar los datos en un almacenamiento de blobs hace que elimine de forma segura los clústeres de HDInsight que se usan para los cálculos sin perder los datos del usuario.

	Durante la configuración, debe especificar una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs de Azure en la cuenta de almacenamiento de Azure. Algunos de los procesos de creación requieren que la cuenta de almacenamiento de Azure y el contenedor de almacenamiento de blobs se creen con antelación. El clúster utiliza el contenedor de almacenamiento de blobs como la ubicación de almacenamiento predeterminada. De manera opcional, puede especificar cuentas de almacenamiento de Azure adicionales (almacenamiento vinculado) al que podrá tener acceso el clúster. Además, el clúster también podrá tener acceso a cualquier contenedor de blobs que esté configurado con acceso de lectura público completo o con acceso de lectura público solo para blobs. Para obtener más información sobre el acceso restringido, consulte [Administración del acceso a los recursos de almacenamiento de Azure](storage-manage-access-to-resources.md).

	![Almacenamiento de HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE] Un contenedor de almacenamiento de blobs proporciona una agrupación de un conjunto de blobs, tal como se muestra en la imagen:

	![Almacenamiento de blobs de Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING] No comparta un contenedor de almacenamiento de blobs para varios clústeres, ya que no es compatible.

	Para obtener más información sobre el uso de almacenes de blobs secundarios, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

- **Tienda de metadatos Hive/Oozie**

	La tienda de metadatos contiene metadatos de Hive y Oozie, como columnas, esquemas, particiones y tablas de Hive. El uso de la tienda de metadatos le ayuda a conservar sus metadatos de Hive y Oozie, por lo que no es necesario volver a crear tablas de Hive o trabajos de Oozie al crear un nuevo clúster. De forma predeterminada, Hive utiliza una base de datos SQL de Azure incrustada para almacenar esta información. La base de datos incrustada no puede conservar los metadatos cuando se elimina el clúster. Por ejemplo, tiene un clúster creado con una tienda de metadatos de Hive. Creó algunas tablas de Hive. Después de eliminar el clúster y de volverlo a crear con la misma tienda de metadatos de Hive, podrá ver las tablas de Hive que creó en el clúster original.
    
    > [AZURE.NOTE] La configuración de la tienda de metadatos no está disponible para los tipos de clúster de HBase.

## Personalización de clústeres mediante la personalización de clústeres de HDInsight (bootstrap)

A veces quiere configurar los archivos de configuración.

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Los clústeres no pueden conservar los cambios debido a la recreación de imágenes. Para obtener más información, consulte [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Para mantener los cambios durante la vida útil de los clústeres, puede usar la personalización de clústeres de HDInsight durante el proceso de creación. Este es el método recomendado para cambiar las configuraciones de un clúster y persistir entre estos eventos de reinicio para el restablecimiento de imagen inicial de Azure. Estos cambios de configuración se aplican antes del inicio del servicio, por lo que no es necesario reiniciar los servicios.

Para ver un ejemplo, consulte [Personalización de los clústeres de HDInsight con Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## Personalización de clústeres mediante la acción de script

Puede instalar componentes adicionales o personalizar la configuración del clúster mediante el uso de scripts durante la creación. Tales scripts se invocan mediante la opción de **Acción de script**, una opción de configuración que se puede usar a partir de los cmdlet de Windows PowerShell de HDInsight, el Portal o el SDK de .NET de HDInsight. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster.md).


## Uso de redes virtuales de Azure

[Red virtual de Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permite crear una red segura y persistente que contenga los recursos necesarios para una solución. Una red virtual permite hacer lo siguiente:

* Conectar recursos en la nube en una red privada (solo en la nube)

	![Diagrama de la configuración solo en la nube](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* Conectar sus recursos en la nube a la red de su centro de datos local (sitio a sitio o punto a sitio) usando una red privada virtual (VPN).

	La configuración sitio a sitio permite conectar varios recursos de su centro de datos a la red virtual de Azure usando una VPN de hardware o el servicio de enrutamiento y acceso remoto.

	![Diagrama de la configuración de sitio a sitio](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	La configuración punto a sitio permite conectar un recurso específico a la red virtual de Azure usando una VPN de software

	![Diagrama de la configuración de punto a sitio](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Para obtener información sobre el uso de HDInsight con una red virtual, como los requisitos de configuración específicos de la red virtual, consulte [Extensión de las funcionalidades de HDInsight con Red virtual de Azure](hdinsight-extend-hadoop-virtual-network.md).

## Métodos de creación de clústeres

En este artículo, ha obtenido información básica acerca de cómo crear un clúster de HDInsight basado en Windows. Use la siguiente tabla para buscar información específica acerca de cómo crear un clúster mediante un método que mejor se adapte a sus necesidades:

| Úselo para crear un clúster... | Mediante un explorador web... | Mediante una línea de comandos | Mediante la API de REST | Mediante un SDK | Desde Linux, Mac OS X o Unix | Desde Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Portal de Azure](hdinsight-hadoop-create-windows-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [CLI de Azure](hdinsight-hadoop-create-windows-clusters-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-windows-clusters-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-windows-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [Plantillas de ARM](hdinsight-hadoop-create-windows-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!---HONumber=AcomDC_0224_2016-->