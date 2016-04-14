<properties
   	pageTitle="Tutorial de Hadoop: introducción a Hadoop y Hive | Microsoft Azure"
   	description="Siga este tutorial de Linux para empezar a usar Hadoop en HDInsight. Obtenga información sobre cómo aprovisionar clústeres de Linux y consultar datos con Hive."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="03/03/2016"
   	ms.author="jgao"/>

# Tutorial de Hadoop: Introducción al uso de Hadoop en HDInsight basado en Linux

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Aprenda a crear clústeres de Hadoop basados en Linux en HDInsight y a ejecutar trabajos de Hive mediante la vista Ambari Hive.

Si todavía no conoce Hadoop y macrodatos, puede obtener más información sobre los términos [Apache Hadoop](http://go.microsoft.com/fwlink/?LinkId=510084), [MapReduce](http://go.microsoft.com/fwlink/?LinkId=510086), [sistema de archivos distribuido de Hadoop (HDFS)](http://go.microsoft.com/fwlink/?LinkId=510087) y [Hive](http://go.microsoft.com/fwlink/?LinkId=510085). Para entender cómo HDInsight habilita Hadoop en Azure, consulte [Introducción a Hadoop en HDInsight](hdinsight-hadoop-introduction.md).

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

### Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Suscripción de Azure**: para crear una cuenta de evaluación gratuita, vaya a [azure.microsoft.com/free](https://azure.microsoft.com/free).

## Crear clúster

La mayoría de los trabajos de Hadoop son trabajos por lotes. Se crea un clúster, se ejecutan algunos trabajos y luego se elimina el clúster. En esta sección, creará un clúster de Hadoop basado en Linux en HDInsight con la [plantilla de ARM](../resource-group-template-deploy.md). La plantilla de ARM es totalmente personalizable y facilita la creación de recursos de Azure, como HDInsight. No es necesario tener experiencia en el uso de esta plantilla para seguir este tutorial. Para conocer otros métodos de creación de clústeres y comprender las propiedades utilizadas en este tutorial, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Para más información sobre cómo usar la plantilla de ARM para crear clústeres de Hadoop en HDInsight, consulte [Crear clústeres de Hadoop en HDInsight con plantillas del Administrador de recursos de Azure (Windows)](hdinsight-hadoop-create-windows-clusters-arm-templates.md). La plantilla de ARM utilizada en este tutorial se encuentra en un contenedor de blobs público, **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json*.

1. Haga clic en la imagen siguiente para abrir la plantilla de ARM en el Portal de Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/es-ES/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. En la hoja **Parámetros**, escriba lo siguiente:

    - **ClusterName**: escriba un nombre para el clúster de Hadoop que va a crear.
    - **Nombre de inicio de sesión y contraseña de clúster**: el nombre de inicio de sesión predeterminado es **admin**.
    - **Nombre de usuario y contraseña de SSH**: el nombre de usuario predeterminado es **sshuser**. Puede cambiarlo. 
    
    Otros parámetros son opcionales. Puede dejarlos como están.
    
    Cada clúster tiene una dependencia de cuenta de almacenamiento de blobs de Azure. Después de eliminar un clúster, los datos permanecen en la cuenta de almacenamiento. El nombre de cuenta de almacenamiento de clúster predeterminado es el nombre del clúster con "store" anexado. Está codificado en la sección de variables de plantilla.
    
3. Haga clic en **Aceptar** para guardar los parámetros.
4. En la hoja **Implementación personalizada**, haga clic en el cuadro desplegable **Grupo de recursos** y, después, en **Nuevo** para crear un nuevo grupo de recursos. El grupo de recursos es un contenedor que agrupa al clúster, a la cuenta de almacenamiento dependiente y a otros recursos vinculados. La ubicación del grupo de recursos puede ser diferente de la ubicación del clúster.
5. Haga clic en **Términos legales** y, a continuación, en **Crear**.
6. Compruebe que la casilla **Anclar al panel** está activada y, luego, haga clic en **Crear**. Verá un icono nuevo llamado **Envío de implementación para la implementación de plantilla**. Tarda aproximadamente 20 minutos en crear un clúster. Una vez creado el clúster, puede hacer clic en la hoja del clúster en el portal para abrirlo.

Después de completar el tutorial, quizá desee eliminar el clúster. Con HDInsight, los datos se almacenan en Almacenamiento de Azure, por lo que puede eliminar un clúster de forma segura cuando no está en uso. También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso. Para obtener instrucciones sobre cómo eliminar un clúster, consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

**Para explorar el clúster**

1. En el [portal](http://portal.azure.com), haga clic en **Microsoft Azure** en la esquina superior izquierda para abrir el panel. 
2. Haga doble clic en el icono con el nombre del clúster para abrir el clúster en una hoja. Este icono se crea porque seleccionó **Anclar al panel**.
3. El panel **Essentials** muestra alguna información básica. Haga clic en **Configuración** para ver más propiedades del clúster.

##Ejecución de consultas de Hive

Las herramientas de [Apache Hive](hdinsight-use-hive.md) son las más populares utilizadas en HDInsight. Hay muchas maneras de ejecutar trabajos de Hive en HDInsight. En este tutorial, utilizará la vista Ambari Hive desde el portal para ejecutar algunos trabajos de Hive. Para conocer otros métodos de enviar consultas de Hive, consulte [Usar Hive y HiveQL con Hadoop en HDInsight para analizar un archivo log4j de Apache de muestra](hdinsight-use-hive.md).

1. Vaya a **https://&lt;ClusterName>.azurehdinsight.net**, donde &lt;ClusterName> es el clúster que creó en la sección anterior para abrir Ambari.
2. Escriba el nombre de usuario de Hadoop y la contraseña que especificó en la sección anterior. El nombre de usuario predeterminado es **admin**.
3. Abra la **vista de Hive** como se muestra en la siguiente captura de pantalla:

    ![Selección de vistas de Ambari](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).
4. En la sección __Editor de consultas__ de la página, pegue las siguientes instrucciones de HiveQL en la hoja de cálculo:

		SHOW TABLES;
5. Haga clic en __Ejecutar__. Debe aparecer la sección __Resultados del proceso de consulta__ bajo el Editor de consultas y mostrar información sobre el trabajo. 

    Cuando haya finalizado la consulta, la sección __Resultados del proceso de consulta__ mostrará los resultados de la operación. Verá una tabla llamada **hivesampletable**. Esta es una tabla de Hive de ejemplo que viene integrada en todos los clústeres de HDInsight.

    ![Tablas de Hive de HDInsight](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png).

    
    > [AZURE.TIP] Tenga en cuenta el elemento desplegable __Guardar resultados__ en la esquina superior izquierda de la sección __Resultados del proceso de consulta__; úsela para descargar los resultados o guardarlos en el almacenamiento de HDInsight como un archivo CSV.
6. Repita los pasos 4 y 5 para ejecutar la consulta siguiente:

        SELECT * FROM hivesampletable;

Después de completar un trabajo de Hive, puede [exportar los resultados a la base de datos SQL de Azure o a la base de datos de SQL Server](hdinsight-use-sqoop-mac-linux.md), y también puede [visualizar los resultados con Excel](hdinsight-connect-excel-power-query.md). Para más información sobre el uso de Hive en HDInsight, consulte [Usar Hive y HiveQL con Hadoop en HDInsight para analizar un archivo log4j de Apache de muestra](hdinsight-use-hive.md).

##Eliminación del clúster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Pasos siguientes

En este tutorial, ha aprendido cómo crear un clúster de HDInsight basado en Linux mediante una plantilla del Administrador de recursos y cómo realizar consultas básicas de Hive.

Para más información sobre el análisis de datos con HDInsight, consulte lo siguiente:

- Para más información sobre el uso de Hive con HDInsight, incluida la forma de realizar consultas de Hive desde Visual Studio, consulte [Usar Hive y HiveQL con Hadoop en HDInsight para analizar un archivo log4j de Apache de muestra][hdinsight-use-hive].

- Para más información sobre Pig, un lenguaje usado para transformar datos, consulte [Uso de Pig con Hadoop en HDInsight][hdinsight-use-pig].

- Para más información sobre MapReduce, una manera de escribir programas que procesan datos en Hadoop, consulte [Uso de MapReduce en Hadoop en HDInsight][hdinsight-use-mapreduce].

- Para más información sobre el uso de las herramientas de HDInsight para Visual Studio para analizar datos en HDInsight, consulte [Introducción al uso de herramientas de Hadoop en Visual Studio para HDInsight para ejecutar una consulta de Hive](hdinsight-hadoop-visual-studio-tools-get-started.md).

Si está listo para empezar a trabajar con sus propios datos y necesita saber más sobre cómo HDInsight almacena los datos o sobre cómo obtener datos en HDInsight, consulte lo siguiente:

- Para más información sobre cómo HDInsight usa el almacenamiento de blobs de Azure, consulte [Uso del almacenamiento de blobs de Azure compatibles con HDFS con Hadoop en HDInsight](hdinsight-hadoop-use-blob-storage.md).

- Para más información sobre cómo cargar datos en HDInsight, consulte [Carga de datos para trabajos de Hadoop en HDInsight][hdinsight-upload-data].

Si desea información sobre cómo crear o administrar un clúster de HDInsight, consulte lo siguiente:

- Para más información sobre cómo administrar el clúster de HDInsight basado en Linux, consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md).

- Para más información sobre las opciones que puede seleccionar al crear un clúster de HDInsight, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

- Si está familiarizado con Linux y Hadoop, pero quiere conocer información específica sobre Hadoop en HDInsight, consulte [Información sobre el uso de HDInsight en Linux](hdinsight-hadoop-linux-information.md). Esto ofrece información como:

	* Direcciones URL para los servicios hospedados en el clúster, por ejemplo, Ambari y WebHCat
	* La ubicación de los archivos y ejemplos de Hadoop en el sistema de archivos local
	* El uso del Almacenamiento de Azure (WASB) en lugar de HDFS como el almacén de datos predeterminado


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png

<!---HONumber=AcomDC_0309_2016-->