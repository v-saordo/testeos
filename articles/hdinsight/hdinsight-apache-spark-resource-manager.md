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
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# Administración de recursos para el clúster Apache Spark en HDInsight de Azure (Linux)

Spark en HDInsight de Azure (Linux) proporciona la interfaz de usuario web de Ambari para administrar los recursos del clúster y supervisar el estado del clúster. También puede utilizar el servidor de historial de Spark para realizar el seguimiento de las aplicaciones que han terminado de ejecutarse en el clúster. Puede utilizar la interfaz de usuario de YARN para supervisar lo que se encuentra en ejecución en el clúster. En este artículo se ofrecen instrucciones sobre cómo acceder a estas interfaces de usuario y cómo realizar algunas tareas básicas de administración de recursos con estas interfaces.

**Requisitos previos:**

Debe tener lo siguiente:

- Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster Apache Spark en HDInsight Linux. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

## ¿Cómo se puede iniciar la interfaz de usuario web de Ambari?

1. Desde el [Portal de vista previa de Azure](https://ms.portal.azure.com/), en el panel de inicio, haga clic en el icono del clúster Spark (si lo ha anclado al panel de inicio). También puede navegar hasta el clúster en **Examinar todo** > **Clústeres de HDInsight**. 
 
2. En la hoja del clúster Spark, haga clic en **Panel**. Cuando se le pida, escriba las credenciales de administrador del clúster Spark.

	![Iniciar Ambari](./media/hdinsight-apache-spark-resource-manager/hdispark.cluster.launch.dashboard.png "Inicio del Administrador de recursos")

3. Esto debería iniciar la interfaz de usuario web de Ambari, como se muestra a continuación.

	![Interfaz de usuario web de Ambari](./media/hdinsight-apache-spark-resource-manager/ambari-web-ui.png "Interfaz de usuario web de Ambari")

## ¿Cómo se puede iniciar el servidor de historial de Spark?

1. Desde el [Portal de vista previa de Azure](https://ms.portal.azure.com/), en el panel de inicio, haga clic en el icono del clúster Spark (si lo ha fijado en el panel de inicio).

2. En la hoja de clúster, en **Vínculos rápidos**, haga clic en **Panel de clúster**. En la hoja **Panel de clúster**, haga clic en **Servidor de historial de Spark**.

	![Servidor de historial de Spark](./media/hdinsight-apache-spark-resource-manager/launch-history-server.png "Servidor de historial de Spark")

	Cuando se le pida, escriba las credenciales de administrador del clúster Spark.


## ¿Cómo se puede iniciar la interfaz de usuario de Yarn?

Puede utilizar la interfaz de usuario de YARN para supervisar las aplicaciones que se encuentran en ejecución en el clúster Spark. Antes de acceder a la interfaz de usuario de YARN, debe habilitar la tunelización SSH para el clúster. Para obtener instrucciones, vea [Uso de la tunelización SSH para tener acceso a la interfaz de usuario web de Ambari](hdinsight-linux-ambari-ssh-tunnel.md)

1. Inicie la interfaz de usuario web de Ambari como se ha indicado en la sección anterior.

2. En la interfaz de usuario web de Ambari, seleccione YARN en la lista de la izquierda de la página.

3. Cuando se muestra la información del servicio YARN, seleccione **Vínculos rápidos**. Aparecerá una lista de los nodos del clúster principal. Seleccione uno de los nodos principales y luego seleccione **IU de ResourceManager**.

	![Iniciar interfaz de usuario de YARN](./media/hdinsight-apache-spark-resource-manager/launch-yarn-ui.png "Iniciar interfaz de usuario de YARN")

4. Esto debería iniciar la interfaz de usuario de YARN y debería ver una página similar a la siguiente:

	![IU de YARN](./media/hdinsight-apache-spark-resource-manager/yarn-ui.png "IU de YARN")

##<a name="scenariosrm"></a>¿Cómo se administran los recursos mediante la interfaz de usuario web de Ambari?

Presentamos algunos escenarios comunes que pueden aparecer con el clúster Spark y las instrucciones para abordarlos mediante la interfaz de usuario web de Ambari.

### No uso BI con el clúster Spark. ¿Cómo elimino los recursos de nuevo?

1. Inicie la interfaz de usuario web de Ambari como se ha indicado anteriormente. En el panel de navegación izquierdo, haga clic en **Spark** y luego en **Configs** (Configuraciones).

2. En la lista de configuraciones disponibles, busque **Custom spark-thrift-sparkconf** (Personalizar spark-thrift-sparkconf) y cambie los valores de **spark.executor.memory** y **spark.drivers.core** a **0**.

	![Recursos para BI](./media/hdinsight-apache-spark-resource-manager/spark-bi-resources.png "Recursos para BI")

3. Haga clic en **Guardar**. Escriba una descripción de los cambios realizados y después vuelva a hacer clic en **Save** (Guardar).

4. En la parte superior de la página, verá un símbolo del sistema para reiniciar el servicio Spark. Haga clic en **Reiniciar** para que los cambios surtan efecto.


### Mis cuadernos de Jupyter no se ejecutan según lo previsto. ¿Cómo se puede reiniciar el servicio?

1. Inicie la interfaz de usuario web de Ambari como se ha indicado anteriormente. En el panel de navegación izquierdo, haga clic en **Jupyter**, haga clic en **Service Actions** (Acciones de servicio) y luego haga clic en **Restart All** (Reiniciar todo). Esto iniciará el servicio de Jupyter en todos los nodos principales.

	![Reiniciar Jupyter](./media/hdinsight-apache-spark-resource-manager/restart-jupyter.png "Reiniciar Jupyter")

	


## <a name="seealso"></a>Otras referencias


* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### Escenarios

* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análisis del registro del sitio web con Spark en HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Creación y ejecución de aplicaciones

* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md)

### Herramientas y extensiones

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applications (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones Scala Spark)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)



[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->