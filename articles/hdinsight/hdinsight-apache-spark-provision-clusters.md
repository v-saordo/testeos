<properties
   pageTitle="Aprovisionamiento de clústeres de Apache Spark en HDInsight | Microsoft Azure"
   description="Obtenga información sobre cómo aprovisionar clústeres Spark para HDInsight de Azure mediante el Portal de Azure, Azure PowerShell, una línea de comandos o el SDK de .NET de HDInsight"
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

# Creación de clústeres Apache Spark en HDInsight mediante opciones personalizadas (Windows)

> [AZURE.NOTE] HDInsight ofrece ahora clústeres Spark en Linux. Para obtener información sobre cómo crear un clúster Spark en HDInsight Linux de manera personalizada, vea [Crear clústeres basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Para la mayoría de los escenarios, puede crear un clúster Spark mediante el método de creación rápida como se describe en [Introducción a Apache Spark en HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). En determinados escenarios, debería crear un clúster personalizado. Por ejemplo, si desea conectar un almacén de metadatos externo para mantener la persistencia de los metadatos de Hive más allá de la duración de un clúster o si desea usar más almacenamiento con el clúster.

Para estos y otros escenarios, en este artículo se proporcionan instrucciones sobre cómo usar el Portal de Azure, Azure PowerShell o el SDK de .NET de HDInsight para crear un clúster Spark personalizado en HDInsight.


**Requisitos previos:**

Antes de comenzar con las instrucciones de este artículo, debe tener una suscripción a Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>¿Cuáles son las diferentes opciones de configuración?

### Almacenamiento adicional

Durante la configuración, debe especificar una cuenta de almacenamiento de blobs de Azure y un contenedor predeterminado. El clúster usa este contenedor como ubicación de almacenamiento predeterminada. Si lo desea, puede especificar una cuenta de almacenamiento de Azure adicional que también se asociará con el clúster.

>[AZURE.NOTE] No comparta un contenedor de almacenamiento de blobs para varios clústeres, ya que no es compatible.

Para obtener más información sobre el uso de almacenes de blobs secundarios, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

### Tienda de metadatos

Spark permite definir esquemas y tablas de Hive con datos sin procesar. Puede guardar estos esquemas y metadatos de las tablas en las tiendas de metadatos externas. El uso de la tienda de metadatos lo ayuda a conservar sus metadatos de Hive, por lo que no es necesario volver a crear tablas de Hive al crear un nuevo clúster. De forma predeterminada, Hive utiliza una base de datos integrada para almacenar esta información. La base de datos incrustada no puede conservar los metadatos cuando se elimina el clúster.

Para obtener instrucciones sobre cómo crear una Base de datos SQL en Azure, vea [Creación de la primera Base de datos SQL de Azure](../sql-database/sql-database-get-started.md).

### Personalización del clúster

Puede instalar componentes adicionales o personalizar la configuración del clúster mediante el uso de scripts durante la creación. Tales scripts se invocan mediante la opción de **Acción de script**, una opción de configuración que se puede usar a partir de los cmdlets de Windows PowerShell de HDInsight, en el Portal de Azure o el SDK de .NET para HDInsight. Para obtener más información, consulte [Personalizar el clúster de HDInsight mediante la acción de script][hdinsight-customize-cluster].


### Redes virtuales

[Red virtual de Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permite crear una red segura y persistente que contenga los recursos que necesita para la solución. Una red virtual permite hacer lo siguiente:

* Conectar recursos en la nube en una red privada (solo en la nube)

	![Diagrama de la configuración solo en la nube](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* Conectar sus recursos en la nube a la red de su centro de datos local (sitio a sitio o punto a sitio) usando una red privada virtual (VPN).

	La configuración sitio a sitio permite conectar varios recursos de su centro de datos a la red virtual de Azure usando una VPN de hardware o el servicio de enrutamiento y acceso remoto.

	![Diagrama de la configuración de sitio a sitio](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	La configuración punto a sitio permite conectar un recurso específico a la red virtual de Azure usando una VPN de software

	![Diagrama de la configuración de punto a sitio](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

Para obtener información sobre el uso de HDInsight con una red virtual, como por ejemplo, los requisitos de configuración específicos de la red virtual, consulte [Extend HDInsight capabilities by using an Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md) (Ampliar las capacidades de HDInsight con una red virtual de Azure).

##<a id="portal"></a> Uso del Portal de vista previa de Azure

Los clústeres Spark en HDInsight usan un contenedor de almacenamiento de blobs de Azure como sistema de archivos predeterminado. Es preciso tener una cuenta de almacenamiento de Azure ubicada en el mismo centro de datos antes de crear un clúster de HDInsight. Para obtener más información, consulte [Uso de Almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure, consulte [Creación de una cuenta de almacenamiento][azure-create-storageaccount].

**Para crear un clúster de HDInsight con la opción de creación personalizada**

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com).
2. Haga clic en **NUEVO**, en **Análisis de datos** y luego en **HDInsight**.

    ![Crear un nuevo clúster en el Portal de vista previa de Azure](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.1.png "Crear un nuevo clúster en el Portal de vista previa de Azure")

3. Escriba un **Nombre de clúster**, seleccione **Spark** para el **Tipo de clúster** y, en el menú desplegable **Sistema operativo de clúster**, seleccione **Windows Server 2012 R2 Datacenter**. Si está disponible, aparecerá una marca de verificación verde junto al Nombre de clúster.

	![Especifique el tipo y el nombre del clúster](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.2.png "Especifique el tipo y el nombre del clúster")

4. Si tiene más de una suscripción, haga clic en la entrada **Suscripción** para seleccionar la suscripción de Azure que se usará para el clúster.

5. Haga clic en **Grupo de recursos** para ver una lista de grupos de recursos existentes y seleccione el grupo en el que quiere crear el clúster. También puede hacer clic en **Crear nuevo** y luego escribir el nombre del nuevo grupo de recursos. Aparecerá una marca de verificación verde para indicar si el nuevo nombre de grupo está disponible.

	> [AZURE.NOTE] Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.

6. Haga clic en **Credenciales** y luego especifique un **Nombre de usuario de inicio de sesión de clúster** y una **Contraseña de inicio de sesión de clúster**. Si quiere habilitar Escritorio remoto en el nodo de clúster, para **Habilitar Escritorio remoto**, haga clic en **Sí**. Seleccione una fecha de caducidad de acceso mediante escritorio remoto al clúster y proporcione el nombre de usuario y la contraseña para el usuario del escritorio remoto. Haga clic en **Seleccionar** en la parte inferior para guardar la configuración de las credenciales.

	![Proporcione las credenciales del clúster](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.3.png "Proporcione las credenciales del clúster")

7. Haga clic en **Origen de datos** para elegir un origen de datos existente para el clúster o crear uno nuevo.

	![Hoja Origen de datos](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.4.png "Proporcione la configuración del origen de datos")

	Actualmente puede seleccionar una cuenta de almacenamiento de Azure como origen de datos para un clúster de HDInsight. Use lo siguiente para comprender las entradas de la hoja **Origen de datos**.

	- **Método de selección**: establézcalo en **De todas las suscripciones** para habilitar la exploración de cuentas de almacenamiento en todas sus suscripciones. Establézcalo en **Clave de acceso** si desea especificar el **Nombre de almacenamiento** y la **Clave de acceso** de una cuenta de almacenamiento existente.

	- **Seleccionar cuenta de almacenamiento o Crear nueva**: haga clic en **Seleccionar cuenta de almacenamiento** para buscar y seleccionar una cuenta de almacenamiento existente que desee asociar con el clúster. O bien, haga clic en **Crear nueva** para crear una nueva cuenta de almacenamiento. Use el campo que aparece para especificar el nombre de la cuenta de almacenamiento. Si el nombre está disponible, aparecerá una marca de verificación verde.

	- **Elegir contenedor predeterminado**: use esta opción para escribir el nombre del contenedor predeterminado que se usará para el clúster. Aunque se puede escribir cualquier nombre aquí, se recomienda usar el mismo nombre que el del clúster para que pueda reconocer fácilmente que el contenedor se usa para este clúster concreto.

	- **Ubicación**: región geográfica en la que se encuentra o donde se creará la cuenta de almacenamiento.

		> [AZURE.IMPORTANT] Seleccionar la ubicación del origen de datos predeterminado también establecerá la ubicación del clúster de HDInsight. El origen de datos del clúster y predeterminado deben encontrarse en la misma región.

	Haga clic en **Seleccionar** para guardar la configuración del origen de datos.

8. Haga clic en **Planes de tarifa de nodo** para mostrar información sobre los nodos que se creen para este clúster. Establezca el número de nodos de trabajo que necesita para el clúster. El costo estimado del clúster se mostrará en la hoja.

	![Hoja Niveles de precios de nodo](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.5.png "Especifique el número de nodos de clúster")

	Haga clic en **Seleccionar** para guardar la configuración de precios de nodo.

9. Haga clic en **Configuración opcional** para seleccionar la versión del clúster y configurar otros valores opcionales, como unirse a una **Red virtual**, configurar una **tienda de metadatos externos** para almacenar datos de Hive y Oozie, usar acciones de script para personalizar un clúster para instalar componentes personalizados o usar cuentas de almacenamiento adicionales con el clúster.

	* Haga clic en la lista desplegable **Versión de HDInsight** y seleccione la versión que quiera usar para el clúster. Para obtener más información, consulte [Versiones del clúster de HDInsight](hdinsight-component-versioning.md).

	* Haga clic en **Red virtual** para proporcionar detalles de configuración para configurar el clúster como parte de una red virtual. En la hoja **Red virtual**, haga clic en **Red virtual** y luego haga clic en una red que quiera usar. De forma similar, seleccione una **subred** para la red y, a continuación, haga clic en **Seleccionar** para guardar la configuración de red virtual.

		![Hoja de red virtual](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.6.png "Especifique detalles de red virtual")

	* Haga clic en **Tiendas de metadatos externas** para especificar la base de datos SQL que quiere usar para guardar los metadatos de Hive y Oozie asociados al clúster.

		![Hoja de tiendas de metadatos personalizados](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.7.png "Especificar tiendas de metadatos externas")

		En **Usar una Base de datos SQL existente para metadatos de Hive**, haga clic en **Sí**, seleccione una base de datos SQL y luego escriba el nombre de usuario y la contraseña para la base de datos. Repita estos pasos si desea **Usar una Base de datos SQL existente para los metadatos de Oozie**. Haga clic en **Seleccionar** hasta volver a la hoja **Configuración opcional**.

		>[AZURE.NOTE] La base de datos SQL de Azure usada para la tienda de metadatos debe permitir la conectividad con otros servicios de Azure, incluido HDInsight de Azure. En el panel de la base de datos SQL de Azure, en el lado derecho, haga clic en el nombre de servidor. Este es el servidor en el que se ejecuta la instancia de base de datos SQL. Cuando se encuentre en la vista de servidor, haga clic en **Configurar** y luego, en **Servicios de Azure**, haga clic en **Sí** y en **Guardar**.

	* Haga clic en **Acciones de script** si quiere usar un script personalizado para personalizar un clúster, conforme se crea el clúster. Para obtener más información sobre acciones de script, vea [Personalización de clústeres de HDInsight con una acción de script](hdinsight-hadoop-customize-cluster.md). En la hoja Acciones de script, proporcione los detalles como se muestra en la captura de pantalla.

		![Hoja de acción de script](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.8.png "Especifique una acción de script")

		Haga clic en **Seleccionar** para guardar los cambios en la configuración de la acción de script.

	* Haga clic en **Claves de almacenamiento de Azure** para especificar cuentas de almacenamiento adicionales para asociar con el clúster. En la hoja **Claves de almacenamiento de Azure**, haga clic en **Agregar una clave de almacenamiento** y luego seleccione una cuenta de almacenamiento existente o cree una nueva cuenta.

		![Hoja de almacenamiento adicional](./media/hdinsight-apache-spark-provision-clusters/hdispark.createcluster.9.png "Especificar cuentas de almacenamiento adicionales")

		Haga clic en **Seleccionar** hasta volver a la hoja **Nuevo clúster de HDInsight**.

10. En la hoja **Nuevo clúster de HDInsight**, asegúrese de que **Anclar a Panel de inicio** está seleccionado y, a continuación, haga clic en **Crear**. Esto creará el clúster y agregará un icono para él en el panel de inicio de su Portal de Azure. El icono indicará que el clúster se está creando y, cuando se haya completado el proceso, cambiará para mostrar el icono de HDInsight.

	| Al crear | Creación finalizada |
	| ------------------ | --------------------- |
	| ![Indicador de creación en el Panel de inicio](./media/hdinsight-apache-spark-provision-clusters/provisioning.png) | ![Icono de clúster creado](./media/hdinsight-apache-spark-provision-clusters/provisioned.png) |

	> [AZURE.NOTE] El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del Panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de creación.

11. Cuando termine la creación, haga clic en el icono del clúster desde el panel de inicio para iniciar la hoja del clúster. La hoja de clúster proporciona información esencial sobre el clúster, como el nombre, el grupo de recursos al que pertenece, la ubicación, el sistema operativo, la dirección URL para el panel del clúster, etc.

	![Hoja de clúster](./media/hdinsight-apache-spark-provision-clusters/hdispark.cluster.blade.png "Propiedades de clúster")

	Use la siguiente información para comprender los iconos de la parte superior de esta hoja y de las secciones **Conceptos básicos** y **Vínculos rápidos**:

	* **Configuración** y **Toda la configuración**: muestra la hoja **Configuración** del clúster, que permite obtener acceso a información de configuración detallada para el clúster.

	* **Panel** y **URL**: se trata de formas de acceder al panel del clúster, que es un portal web que ejecuta trabajos en el clúster.

	* **Escritorio remoto**: permite habilitar o deshabilitar Escritorio remoto en los nodos de clúster.

	* **Escalar clúster**: Permite cambiar el número de nodos de trabajo para este clúster.

	* **Eliminar**: elimina el clúster de HDInsight.

	* **Inicio rápido** (![icono de nube y rayo = inicio rápido](./media/hdinsight-apache-spark-provision-clusters/quickstart.png)): muestra información que le ayudará a empezar a usar HDInsight.

	* **Usuarios** (![icono de usuarios](./media/hdinsight-apache-spark-provision-clusters/users.png)): permite establecer permisos para la _administración del portal_ de este clúster para otros usuarios de la suscripción de Azure.

		> [AZURE.IMPORTANT] Esto _solo_ afecta al acceso y los permisos para este clúster en el Portal de vista previa de Azure, y no tiene ningún efecto sobre quién puede conectarse o enviar trabajos al clúster de HDInsight.

	* **Etiquetas** (![icono de etiqueta](./media/hdinsight-apache-spark-provision-clusters/tags.png)): las etiquetas permiten establecer pares clave-valor para definir una taxonomía personalizada de sus servicios en la nube. Por ejemplo, puede crear una clave denominada __proyecto__ y luego usar un valor común para todos los servicios asociados a un proyecto específico.

	* **Panel de clúster**: inicia la hoja Panel de clúster desde la que puede iniciar el panel de clúster propio o iniciar cuadernos de Zeppelin y Jupyter.


##<a id="powershell"></a>Uso de Azure PowerShell

Vea [Creación de clústeres de HDInsight](hdinsight-provision-clusters.md#create-using-azure-powershell).

Especifique el modificador de tipo de clúster Spark en el cmdlet New-AzureRmHDInsightCluster:

	-ClusterType Spark


## Uso del SDK de .NET de HDInsight basado en ARM

Vea [Creación de clústeres de HDInsight](hdinsight-provision-clusters.md#create-using-the-hdinsight-net-sdk).

Especifique el tipo de clúster Spark:

	private const HDInsightClusterType NewClusterType = HDInsightClusterType.Spark;


##<a id="nextsteps"></a> Pasos siguientes

* Vea [Uso de herramientas de BI con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-use-bi-tools-v1.md) para aprender a usar Apache Spark en HDInsight con herramientas de BI como Power BI y Tableau.
* Vea [Uso de Spark en HDInsight para crear aplicaciones de aprendizaje automático](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md) para aprender a crear aplicaciones de aprendizaje automático con Apache Spark en HDInsight.
* Vea [Streaming con Spark: procesamiento de eventos desde el Centro de eventos de Azure con Apache Spark en HDInsight](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md) para aprender a crear aplicaciones de streaming con Apache Spark en HDInsight.
* Vea [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager-v1.md) para aprender a usar el Administrador de recursos para administrar los recursos asignados al clúster Spark.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/


[azure-preview-portal]: https://portal.azure.com

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Uso de Sqoop con HDInsight"

<!---HONumber=AcomDC_0218_2016-->