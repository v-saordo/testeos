<properties
	pageTitle="Uso de Hue con Hadoop en clústeres de HDInsight Linux | Microsoft Azure"
	description="Aprenda a instalar y usar Hue con clústeres de Hadoop en HDInsight Linux."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/01/2016" 
	ms.author="nitinme"/>

# Instalación y uso de Hue en clústeres de Hadoop para HDInsight

Aprenda a instalar Hue en clústeres de HDInsight Linux y a usar tunelización para enrutar las solicitudes a Hue.

## ¿Qué es Hue?

Hue es un conjunto de aplicaciones web que se usan para interactuar con un clúster de Hadoop. Puede usar Hue para examinar el almacenamiento asociado a un clúster de Hadoop (WASB, en el caso de clústeres de HDInsight), ejecute trabajos de Hive y scripts de Pig, etc. Los siguientes componentes son compatibles con la instalación de Hue en un clúster de Hadoop para HDInsight.

* Editor Beeswax de Hive
* Pig
* Administrador de la tienda de metadatos
* Oozie
* FileBrowser (que se comunica con el contenedor predeterminado WASB)
* Explorador web


## Instalación de Hue mediante acciones de script

La acción de script [https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh) se usa para instalar Hue en un clúster de HDInsight. En esta sección se proporcionan instrucciones sobre cómo usar el script durante el aprovisionamiento del clúster mediante el Portal de Azure clásico.

> [AZURE.NOTE] También puede usar Azure PowerShell o el SDK de .NET para HDInsight para crear un clúster mediante este script. Para obtener más información sobre el uso de estos métodos, consulte [Personalización de clústeres de HDInsight mediante acciones de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie el aprovisionamiento de un clúster siguiendo los pasos que se describen en [Aprovisionamiento de clústeres de HDInsight en Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), pero no complete la operación.

	> [AZURE.NOTE] Para instalar Hue en clústeres de HDInsight, el tamaño recomendado de nodo principal es como mínimo A4 (8 núcleos, 14 gigabytes de memoria).

2. En la hoja **Configuración opcional**, seleccione **Acciones de script** y proporcione la información tal y como se muestra a continuación:

	![Proporcionar parámetros de acción de script para Hue](./media/hdinsight-hadoop-hue-linux/hue_script_action.png "Proporcionar parámetros de acción de script para Hue")

	* __NOMBRE__: escriba un nombre sencillo para la acción de script.
	* __URI DE SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
	* __PRINCIPAL__: active esta opción.
	* __TRABAJO__: déjelo en blanco.
	* __ZOOKEEPER__: déjelo en blanco.
	* __PARÁMETROS__: déjelo en blanco.

3. En la parte inferior de **Acciones de script**, use el botón **Seleccionar** para guardar la configuración. Por último, use el botón **Seleccionar** situado en la parte inferior de la hoja **Configuración opcional** para guardar la información de configuración opcional.

4. Continúe aprovisionando el clúster tal como se describe en [Aprovisionamiento de clústeres de HDInsight n Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

## Use Hue con clústeres de HDInsight

La tunelización de SSH es la única forma de obtener acceso a Hue en el clúster cuando se está ejecutando. La tunelización a través de SSH permite al tráfico ir directamente al nodo principal del clúster en el que se ejecuta Hue. Cuando termine de aprovisionar el clúster, siga estos pasos para usar Hue en un clúster de HDInsight Linux.

1. Consulte la información de [Uso de la tunelización SSH para acceder a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web](hdinsight-linux-ambari-ssh-tunnel.md) para crear un túnel SSH desde el sistema cliente al clúster de HDInsight y luego configurar el explorador web para usar el túnel SSH como proxy.

2. Después de crear un túnel SSH y configurar el explorador para redirigir el tráfico mediante proxy a través de él, debe encontrar el nombre de host del nodo principal. Use los pasos siguientes para obtener esta información de Ambari:

    1. En un explorador, vaya a https://CLUSTERNAME.azurehdinsight.net. Cuando se le solicite, use el nombre de usuario y la contraseña de administrador para autenticarse en el sitio.
    
    2. En el menú que aparece en la parte superior de la página, seleccione __Hosts__.
    
    3. Seleccione la entrada que comienza con __hn0__. Cuando se abre la página, se mostrará el nombre de host en la parte superior. El formato del nombre de host es __hn0 CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net__. Es el nombre de host que debe usar al conectarse a Hue.

2. Después de crear un túnel SSH y configurar el explorador para redirigir el tráfico mediante proxy a través de él, use el explorador para abrir el portal de Hue en http://HOSTNAME:8888. Reemplace HOSTNAME por el nombre obtenido en Ambari en el paso anterior.

    > [AZURE.NOTE] Al iniciar sesión por primera vez, se le pedirá que cree una cuenta para iniciar sesión en el portal de Hue. Las credenciales que especifique aquí se limitarán al portal y no están relacionadas con las credenciales de administrador o de usuario SSH que especificó al aprovisionar el clúster.

	![Inicio de sesión en el portal de Hue](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Login.png "Especifique las credenciales para el portal de Hue")

### Ejecución de una consulta de Hive

1. En el portal de Hue, haga clic en **Query Editors** (editores de consultas), y, a continuación, haga clic en **Hive** para abrir el editor de Hive.

	![Uso de Hive](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.png "Uso de Hive")

2. En la pestaña **Assist** (asistencia) , en **Database** (base de datos), debería de ver **hivesampletable**. Se trata de una tabla de ejemplo que se incluye con todos los clústeres de Hadoop en HDInsight. Escriba una consulta de ejemplo en el panel derecho y vea el resultado en la pestaña **Results** (resultados) en el panel debajo, como se muestra en la captura de pantalla.

	![Ejecución de consultas de Hive](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.Query.png "Ejecución de consultas de Hive")

	También puede usar la pestaña **Chart** para ver una representación visual del resultado.

### Examinar el almacenamiento del clúster

1. En el portal de Hue, haga clic en **File Browser** (explorador de archivos) en la esquina superior derecha de la barra de menús.

2. De forma predeterminada se abre el explorador de archivos en el directorio **/user/myuser**. Haga clic en la barra oblicua que se encuentra antes del directorio del usuario en la ruta de acceso para ir a la raíz del contenedor de Almacenamiento de Azure asociado con el clúster.

	![Uso del explorador de archivos](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.File.Browser.png "Uso del explorador de archivos")

3. Haga clic con el botón derecho en un archivo o carpeta para ver las operaciones disponibles. Use el botón **Cargar** situado en la esquina derecha para cargar archivos en el directorio actual. Use el botón **Nuevo** para crear nuevos archivos o directorios.

> [AZURE.NOTE] El explorador de archivos de Hue solo puede mostrar el contenido del contenedor predeterminado asociado con el clúster de HDInsight. Cualquier cuenta o contenedor de almacenamiento adicional asociados con el clúster no serán accesibles mediante el explorador de archivos. Sin embargo, los contenedores adicionales asociados con el clúster siempre serán accesibles para los trabajos de Hive. Por ejemplo, si escribe el comando `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` en el editor de Hive, también podrá ver el contenido de los contenedores adicionales. En este comando, **newcontainer** no es el contenedor predeterminado asociado a un clúster.

## Consideraciones importantes

1. El script que se usó para instalar Hue lo instala solo en el nodo principal 0 del clúster.

2. Durante la instalación, se reinician varios servicios de Hadoop (HDFS, YARN, MR2, Oozie) para actualizar la configuración. Cuando el script finaliza la instalación de Hue, puede tardar algún tiempo hasta que otros servicios de Hadoop se inicien. Esto podría afectar inicialmente al rendimiento de Hue. Una vez que todos los servicios se inician, la funcionalidad de Hue será total.

3.	Hue no entiende los trabajos Tez, que es el valor predeterminado actual de Hive. Si desea usar MapReduce como el motor de ejecución de Hive, actualice el script para usar el comando siguiente en el script:

		set hive.execution.engine=mr;

4.	Con los clústeres de Linux, puede tener un escenario en el que los servicios se ejecutan en el nodo principal 0 mientras el Administrador de recursos podría ejecutarse en el nodo principal 1. Este escenario podría producir errores (que se muestra a continuación) cuando se usa Hue para ver detalles de trabajos de ejecución en el clúster. De todas formas, puede ver los detalles del trabajo una vez que el trabajo se complete.

	![Error en el portal de Hue](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Error.png "Error en el portal de Hue")

	Este es un problema conocido. Como alternativa, modifique Ambari para que el Administrador de recursos que está activo también se ejecute en el nodo principal 0.

5.	Hue entiende WebHDFS, mientras que los clústeres de HDInsight usan Almacenamiento de Azure mediante `wasb://`. Por lo tanto, el script personalizado que se usa con la acción de script instala WebWasb, que es un servicio compatible con WebHDFS para hablar con WASB. Así que aunque el portal de Hue dice HDFS en lugares (como cuando se mueve el mouse sobre el **Explorador de archivos**), se debe interpretar como WASB.


## Pasos siguientes

- [Instalación y uso de Spark en clústeres de HDInsight](hdinsight-hadoop-spark-install-linux.md) para obtener instrucciones sobre cómo usar la personalización de clústeres para instalar y usar Spark en clústeres de Hadoop para HDInsight. Spark es un marco de procesamiento paralelo de código abierto que admite el procesamiento en memoria para mejorar el rendimiento de las aplicaciones analíticas de Big Data.

- [Instalación de Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use la personalización del clúster para instalar Giraph en clústeres de Hadoop para HDInsight. Giraph permite realizar un procesamiento gráfico con Hadoop y se puede usar con HDInsight de Azure.

- [Instalación de Solr en clústeres de HDInsight](hdinsight-hadoop-solr-install-linux.md). Use la personalización del clúster para instalar Solr en clústeres de Hadoop para HDInsight. Solr le permite realizar potentes operaciones de búsqueda en los datos almacenados.

- [Instalación de R en clústeres de HDInsight](hdinsight-hadoop-r-scripts-linux.md). Use la personalización del clúster para instalar R en clústeres de Hadoop para HDInsight. R es un entorno y lenguaje de código abierto para computación estadística. Proporciona cientos de de funciones estadísticas integradas y su propio lenguaje de programación que combina aspectos de la programación funcional y orientada a objetos. También proporciona amplias capacidades gráficas.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install-linux.md

<!---HONumber=AcomDC_0204_2016-->