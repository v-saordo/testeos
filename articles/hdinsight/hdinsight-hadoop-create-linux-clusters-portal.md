<properties
   	pageTitle="Crear clústeres de Hadoop, HBase, Storm o Spark en Linux en HDInsight con el portal | Microsoft Azure"
   	description="Aprenda a crear clústeres Hadoop, HBase, Storm o Spark en Linux para HDInsight con un explorador web y el Portal de vista previa de Azure."
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
   	ms.date="12/08/2015"
   	ms.author="nitinme"/>


#Creación de clústeres basados en Linux en HDInsight con el Portal de vista previa de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

El Portal de vista previa de Azure es una herramienta de administración basada en web para servicios y recursos hospedados en la nube de Microsoft Azure. Use la información de este documento para crear un clúster de HDInsight basado en Linux con el Portal de vista previa y el explorador web.

##Requisitos previos

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Un explorador web moderno__. El Portal de vista previa de Azure usa HTML5 y Javascript, y puede que no funcione correctamente en exploradores más antiguos.

##Creación de clústeres

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com).

2. Haga clic en **NUEVO**, en **Análisis de datos** y, luego, en **HDInsight**.

    ![Crear un nuevo clúster en el Portal de vista previa de Azure](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.1.png "Crear un nuevo clúster en el Portal de vista previa de Azure")

3. Escriba un **Nombre de clúster**, seleccione **Hadoop** en **Tipo de clúster** y, en la lista desplegable **Sistema operativo de clústeres**, seleccione **Ubuntu**. Si está disponible, aparecerá una marca de verificación verde junto al Nombre de clúster.

    > [AZURE.NOTE] Para aprovisionar clústeres de HBase o Storm, seleccione el valor adecuado en la lista desplegable **Tipo de clúster**.

    ![Especifique el tipo y el nombre del clúster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.2.png "Especifique el tipo y el nombre del clúster")

4. Si tiene más de una suscripción, haga clic en la entrada **Suscripción** para seleccionar la suscripción de Azure que se usará para el clúster.

5. Haga clic en **Grupo de recursos** para ver una lista de grupos de recursos existentes y seleccione el grupo en el que quiere crear el clúster. También puede hacer clic en **Crear nuevo** y luego escribir el nombre del nuevo grupo de recursos. Aparecerá una marca de verificación verde para indicar si el nuevo nombre de grupo está disponible.

	> [AZURE.NOTE] Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.

6. Haga clic en **Credenciales** y escriba una contraseña para el usuario administrador. También debe especificar un **Nombre de usuario de SSH** y una **CONTRASEÑA** o una **CLAVE PÚBLICA**, que se usarán para autenticar al usuario de SSH. Es recomendable usar una clave pública. Haga clic en **Seleccionar** en la parte inferior para guardar la configuración de las credenciales.

	![Proporcione las credenciales del clúster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.3.png "Proporcione las credenciales del clúster")

	Para obtener más información sobre el uso de SSH con HDInsight, vea uno de los siguientes artículos:

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Haga clic en **Origen de datos** para elegir un origen de datos existente para el clúster o crear uno nuevo.

	![Hoja Origen de datos](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.4.png "Proporcione la configuración del origen de datos")

	Actualmente puede seleccionar una cuenta de almacenamiento de Azure como origen de datos para un clúster de HDInsight. Use lo siguiente para comprender las entradas de la hoja **Origen de datos**.

	- **Método de selección**: establézcalo en **De todas las suscripciones** para habilitar la exploración de cuentas de almacenamiento en todas sus suscripciones. Establézcalo en **Clave de acceso** si desea especificar el **Nombre de almacenamiento** y la **Clave de acceso** de una cuenta de almacenamiento existente.

	- **Seleccionar cuenta de almacenamiento o Crear nueva**: haga clic en **Seleccionar cuenta de almacenamiento** para buscar y seleccionar una cuenta de almacenamiento existente que desee asociar con el clúster. O bien, haga clic en **Crear nueva** para crear una nueva cuenta de almacenamiento. Use el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.

	- **Elegir contenedor predeterminado**: use esta opción para escribir el nombre del contenedor predeterminado que se usará para el clúster. Aunque se puede escribir cualquier nombre aquí, se recomienda usar el mismo nombre que el del clúster para que pueda reconocer fácilmente que el contenedor se usa para este clúster concreto.

	- **Ubicación**: región geográfica en la que se encuentra o donde se creará la cuenta de almacenamiento.

		> [AZURE.IMPORTANT] Seleccionar la ubicación del origen de datos predeterminado también establecerá la ubicación del clúster de HDInsight. El origen de datos del clúster y predeterminado deben encontrarse en la misma región.

	Haga clic en **Seleccionar** para guardar la configuración del origen de datos.

8. Haga clic en **Planes de tarifa de nodo** para mostrar información sobre los nodos que se creen para este clúster. Establezca el número de nodos de trabajo que necesita para el clúster. El costo estimado del clúster se mostrará en la hoja.

	![Hoja Niveles de precios de nodo](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.5.png "Especifique el número de nodos de clúster")
    
    > [AZURE.IMPORTANT] Si planea crear más de 32 nodos de trabajo, en la creación de clústeres o al cambiar el tamaño del clúster después de la creación, debe seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
    >
    > Para obtener más información acerca de los tamaños de nodo y los costos asociados, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

	Haga clic en **Seleccionar** para guardar la configuración de precios de nodo.

9. Haga clic en **Configuración opcional** para seleccionar la versión del clúster y configurar otros valores opcionales, como unirse a una **Red virtual**, configurar una **tienda de metadatos externos** para almacenar datos de Hive y Oozie, usar acciones de script para personalizar un clúster para instalar componentes personalizados o usar cuentas de almacenamiento adicionales con el clúster.

	* Haga clic en la lista desplegable **Versión de HDInsight** y seleccione la versión que quiera usar para el clúster. Para obtener más información, consulte [Versiones de clústeres de HDInsight](hdinsight-component-versioning.md).

	* **Red virtual**: seleccione una red virtual de Azure y la subred si quiere colocar el clúster en una red virtual.

		![Hoja de red virtual](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.6.png "Especifique detalles de red virtual")

    	Para obtener información sobre el uso de HDInsight con una red virtual, como por ejemplo, los requisitos de configuración específicos de la red virtual, consulte [Extend HDInsight capabilities by using an Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md) (Ampliar las capacidades de HDInsight con una red virtual de Azure).

	* Haga clic en **Tiendas de metadatos externas** para especificar la base de datos SQL que quiere usar para guardar los metadatos de Hive y Oozie asociados al clúster.
    
        > [AZURE.NOTE] La configuración de la tienda de metadatos no está disponible para los tipos de clúster de HBase.

		![Hoja de tiendas de metadatos personalizados](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.7.png "Especificar tiendas de metadatos externas")

		En **Usar una Base de datos SQL existente para metadatos de Hive**, haga clic en **Sí**, seleccione una base de datos SQL y luego escriba el nombre de usuario y la contraseña para la base de datos. Repita estos pasos si desea **Usar una Base de datos SQL existente para los metadatos de Oozie**. Haga clic en **Seleccionar** hasta volver a la hoja **Configuración opcional**.

		>[AZURE.NOTE] La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de la base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.

	* **Acciones de script** si quiere usar un script personalizado para personalizar un clúster, conforme se crea el clúster. Para obtener más información sobre acciones de script, vea [Personalización de clústeres de HDInsight con una acción de script](hdinsight-hadoop-customize-cluster-linux.md). En la hoja Acciones de script, proporcione los detalles como se muestra en la captura de pantalla.

		![Hoja de acción de script](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.8.png "Especifique una acción de script")

	* Haga clic en **Claves de almacenamiento de Azure** a fin de especificar cuentas de almacenamiento adicionales para asociar al clúster. En la hoja **Claves de almacenamiento de Azure**, haga clic en **Agregar una clave de almacenamiento** y luego seleccione una cuenta de almacenamiento existente o cree una nueva cuenta.

		![Hoja de almacenamiento adicional](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.CreateCluster.9.png "Especificar cuentas de almacenamiento adicionales")

		Haga clic en **Seleccionar** hasta volver a la hoja **Nuevo clúster de HDInsight**.

10. En la hoja **Nuevo clúster de HDInsight**, asegúrese de que **Anclar a Panel de inicio** está seleccionado y, a continuación, haga clic en **Crear**. Esto creará el clúster y agregará un icono para él en el panel de inicio de su Portal de Azure. El icono indicará que el clúster está aprovisionando y cambiará para mostrar el icono de HDInsight cuando se haya completado el proceso.

	| Durante el aprovisionamiento | Aprovisionamiento completado |
	| ------------------ | --------------------- |
	| ![Indicador de aprovisionamiento en el panel de inicio](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioning.png) | ![Icono de clúster aprovisionado](./media/hdinsight-hadoop-create-linux-cluster-portal/provisioned.png) |

	> [AZURE.NOTE] El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del Panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de aprovisionamiento.

11. Una vez que termine el aprovisionamiento, haga clic en el icono del clúster desde el panel de inicio para iniciar la hoja del clúster. La hoja de clúster proporciona información esencial sobre el clúster, como el nombre, el grupo de recursos al que pertenece, la ubicación, el sistema operativo, la dirección URL para el panel del clúster, etc.

	![Hoja de clúster](./media/hdinsight-hadoop-create-linux-cluster-portal/HDI.Cluster.Blade.png "Propiedades de clúster")

	Use las siguientes opciones para comprender los iconos de la parte superior de esta hoja y, en la sección **Conceptos básicos**:

	* **Configuración** y **Toda la configuración**: Muestra la hoja **Configuración** del clúster, que permite obtener acceso a información de configuración detallada para el clúster.

	* **Panel**, **Panel del clúster** y **URL**: se trata de formas de tener acceso al panel del clúster, que es un portal web que ejecuta trabajos en el clúster.

	* **Shell seguro**: información necesaria para acceder al clúster mediante SSH.

	* **Eliminar**: elimina el clúster de HDInsight.

	* **Inicio rápido** (![icono de nube y rayo = inicio rápido](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)): muestra información que le ayudará a empezar a usar HDInsight.

	* **Usuarios** (![icono de usuarios](./media/hdinsight-hadoop-create-linux-cluster-portal/users.png)): permite establecer permisos para la _administración del portal_ de este clúster para otros usuarios de la suscripción de Azure.

		> [AZURE.IMPORTANT] Esto _solo_ afecta al acceso y los permisos para este clúster en el Portal de vista previa de Azure, y no tiene ningún efecto sobre quién puede conectarse o enviar trabajos al clúster de HDInsight.

	* **Etiquetas** (![icono de etiqueta](./media/hdinsight-hadoop-create-linux-cluster-portal/tags.png)): las etiquetas permiten establecer pares clave-valor para definir una taxonomía personalizada de sus servicios en la nube. Por ejemplo, puede crear una clave denominada __proyecto__ y luego usar un valor común para todos los servicios asociados a un proyecto específico.

##Personalización de los clústeres

- Consulte [Customize HDInsight clusters using Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Windows)](hdinsight-hadoop-customize-cluster.md).

##Pasos siguientes

Ahora que ya creó un clúster de HDInsight correctamente, use lo siguiente para aprender a trabajar con el clúster:

###Clústeres Hadoop

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

###Clústeres HBase

* [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desarrollo de aplicaciones de Java para HBase en HDInsight](hdinsight-hbase-build-java-maven-linux)

###Clústeres Storm

* [Desarrollo de topologías de Java para Storm en HDInsight](hdinsight-storm-develop-java-topology.md)
* [Uso de componentes de Python en Storm en HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementación y supervisión de topologías con Storm en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

###Clústeres de Spark

* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

<!---HONumber=AcomDC_0224_2016-->