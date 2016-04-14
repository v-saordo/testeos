<properties 
	pageTitle="Uso del Administrador de recursos para asignar recursos al clúster de Apache Spark en HDInsight | Microsoft Azure" 
	description="Aprenda a usar el Administrador de recursos para clústeres Spark en HDInsight con el fin de mejorar el rendimiento." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/22/2015" 
	ms.author="nitinme"/>


# Administración de recursos para el clúster Apache Spark en HDInsight de Azure (Windows)

> [AZURE.NOTE] HDInsight ofrece ahora clústeres de Spark en Linux. Para información sobre cómo administrar los recursos para un clúster Spark en HDInsight Linux, vea [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md).

El Administrador de recursos es un componente del panel de clúster Spark que le permite administrar los recursos como núcleos y RAM usados por cada aplicación que se ejecuta en el clúster.

## <a name="launchrm"></a>¿Cómo se inicia el Administrador de recursos?

1. En el [Portal de vista previa de Azure](https://ms.portal.azure.com/), en el panel de inicio, haga clic en el icono del clúster Spark (si lo ancló al panel de inicio). También puede navegar hasta el clúster en **Examinar todo** > **Clústeres de HDInsight**. 
 
2. En la hoja del clúster Spark, haga clic en **Panel**. Cuando se le pida, escriba las credenciales de administrador del clúster Spark.

	![Iniciar el Administrador de recursos](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.cluster.launch.dashboard.png "Inicio del Administrador de recursos")

##<a name="scenariosrm"></a>¿Cómo se pueden solucionar estos problemas mediante el Administrador de recursos?

Presentamos algunos escenarios comunes que pueden aparecer con el clúster Spark y las instrucciones para solucionarlos con el Administrador de recursos.

### El clúster Spark en HDInsight es lento

El clúster Apache Spark en HDInsight está diseñado para una arquitectura multiempresa, por lo que los recursos se dividen entre varios componentes (cuadernos, servidor de trabajo, etc.). Esto le permite usar todos los componentes de Spark simultáneamente sin preocuparse de que alguno de ellos no pueda obtener los recursos para ejecutarse, aunque cada componente será más lento, ya que los recursos están fragmentados. Esto se puede ajustar según sus necesidades.


### Solo uso el cuaderno de Jupyter con el clúster Spark. ¿Cómo puedo asignarle todos los recursos?

1. En el **Panel de Spark**, haga clic en la pestaña **IU de Spark** para averiguar el número máximo de núcleos y la cantidad máxima de RAM que puede asignar a las aplicaciones.

	![Asignación de recursos](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.resource.png "Encontrar los recursos asignados a un clúster Spark")

	Según la captura de pantalla anterior, el número máximo de núcleos que puede asignar es 7 (un total de 8 núcleos, de los cuales 1 está en uso) y la cantidad máxima de RAM que se puede asignar es 9 GB (un total de 12 GB de RAM, de los cuales 2 GB se deben reservar para uso del sistema y 1 GB para el uso de las demás aplicaciones).

	También se deben tener en cuenta todas las aplicaciones que se estén ejecutando. Puede consultarlas en la pestaña **IU de Spark**.

	![Aplicaciones en ejecución](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.running.apps.png "Aplicaciones que se ejecutan en el clúster")

	
2. En el panel de Spark de HDInsight, haga clic en la pestaña **Administrador de recursos** y especifique los valores para **Recuento de núcleos de la aplicación predeterminados** y **Memoria de ejecución predeterminada por nodo de trabajo**. Establezca las demás propiedades en 0.

	![Asignación de recursos](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.allocate.resources.png "Asignar recursos a las aplicaciones")

### No uso herramientas de BI con el clúster Spark. ¿Cómo puedo recuperar recursos? 

Especifique el recuento de núcleos de servidor Thrift y la memoria de ejecución por nodo de trabajo de servidor Thrift como 0. Sin núcleos ni memoria asignados, el servidor Thrift pasará al estado **EN ESPERA**.

![Asignación de recursos](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.no.thrift.png "No hay recursos en el servidor Thrift")

##<a name="seealso"></a>Otras referencias

* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview-v1.md)
* [Creación de un clúster Spark de HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Uso de herramientas de BI con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Uso de Spark en HDInsight para crear aplicaciones de aprendizaje automático](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [Streaming con Spark: Procesamiento de eventos desde el Centro de eventos de Azure con Apache Spark en HDInsight](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->