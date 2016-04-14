<properties
	pageTitle="Administración de clústeres de Hadoop en HDInsight con el Portal de Azure | Microsoft Azure"
	description="Vea cómo administrar el servicio HDInsight. Cree un clúster de HDInsight, abra la consola interactiva de JavaScript y la consola de comandos de Hadoop."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="jgao"/>

# Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Con el [Portal de Azure][azure-portal] puede crear clústeres de Hadoop en HDInsight de Azure, cambiar la contraseña de usuario de Hadoop y habilitar el Protocolo de escritorio remoto (RDP) para que pueda tener acceso a la consola de comandos de Hadoop en el clúster.

La información de este artículo solo se aplica a los clústeres de HDInsight para Windows. Para obtener información acerca de cómo administrar clústeres para Linux, haga clic en el selector de pestañas anterior.

Haga clic en el selector de pestañas para obtener información sobre cómo crear clústeres de Hadoop en HDInsight con otras herramientas.

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Administración de clústeres de Hadoop en HDInsight mediante el Portal de Azure](hdinsight-administer-use-management-portal-v1.md)

**Requisitos previos**

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Cuenta de Almacenamiento de Azure**: un clúster de HDInsight usa contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Para obtener más información acerca de cómo el almacenamiento de blobs de Azure ofrece una experiencia perfecta con los clústeres de HDInsight, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obtener información acerca de la creación de una cuenta de Almacenamiento de Azure, consulte [Creación de una cuenta de Almacenamiento](../storage/storage-create-storage-account.md).

##Abrir el portal

1. Inicie sesión en [https://portal.azure.com/](https://portal.azure.com).
2. Después de abrir el portal, puede:

	- Hacer clic en **Nuevo** en el menú de la izquierda para crear un nuevo clúster:
	
		![botón nuevo clúster de HDInsight](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
	- Haga clic en **Clústeres de HDInsight** en el menú de la izquierda.
	
		![Portal de Azure botón clúster de HDInsight](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

	Si **HDInsight** no aparece en el menú de la izquierda, haga clic en **Examinar**.

	![Portal de Azure botón Examinar clúster](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

##Creación de clústeres

Para obtener instrucciones sobre la creación mediante el Portal, consulte [Creación de clústeres de HDInsight](hdinsight-provision-clusters.md#create-using-the-preview-portal).

HDInsight trabaja con una amplia gama de componentes de Hadoop. Para ver la lista de los componentes que han sido comprobados y admitidos, consulte [¿Qué versión de Hadoop tiene HDInsight de Azure?](hdinsight-component-versioning.md). Puede personalizar HDInsight mediante una de las opciones siguientes:

- Use Generar script de acción para ejecutar scripts que pueden personalizar un clúster para cambiar la configuración del clúster o para instalar los componentes personalizados, como Giraph o Solr. Para obtener más información, consulte [Personalización de un clúster de HDInsight mediante Generar acción de script](hdinsight-hadoop-customize-cluster.md).
- Use los parámetros de personalización de clústeres de .NET SDK de HDInsight o de Azure PowerShell durante la creación de los clústeres. Estos cambios de configuración se conservan durante toda la vida del clúster y no se ven afectados por el restablecimiento de imágenes de nodos del clúster que la plataforma Azure realiza periódicamente para su mantenimiento. Para obtener más información acerca del uso de los parámetros de personalización de clústeres, consulte [Creación de clústeres de HDInsight](hdinsight-provision-clusters.md).
- Algunos de los componentes nativos de Java, como Mahout y Cascading, se pueden ejecutar en el clúster como archivos JAR. Estos archivos JAR se pueden distribuir al almacenamiento de blobs de Azure y enviarse a los clústeres de HDInsight usando los mecanismos de envío de trabajo de Hadoop. Para obtener más información, consulte [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md).

	>[AZURE.NOTE] Si tiene problemas con la implementación de los archivos JAR en los clústeres de HDInsight o con la llamada de los archivos JAR en los clústeres de HDInsight, póngase en contacto con [Soporte técnico de Microsoft](https://azure.microsoft.com/support/options/).

	> La presentación en cascada no se admite en HDInsight, y no puede optar a recibir soporte técnico de Microsoft. Para ver las listas de los componentes compatibles, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight](hdinsight-component-versioning.md).

La instalación de software personalizado en el clúster mediante la Conexión a Escritorio remoto no es compatible. Debe evitar el almacenamiento de cualquier archivo en las unidades del nodo principal, debido a que se perderán si necesita volver a crear los clústeres. Recomendamos almacenar los archivos en el almacenamiento de blobs de Azure. El almacenamiento de blobs es persistente.

##Enumeración y visualización de clústeres

1. Inicie sesión en [https://portal.azure.com/](https://portal.azure.com).
2. Haga clic en **Clústeres de HDInsight** en el menú de la izquierda:
	
	![Portal de Azure botón clúster de HDInsight](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)
		
	Si **HDInsight** no aparece en el menú de la izquierda, haga clic en **Examinar**:

	![Portal de Azure botón Examinar clúster](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

	Si hay alguno, verá una lista de clústeres:
	
	![Portal de Azure lista de clústeres](./media/hdinsight-administer-use-management-portal/hdinsight-list-clusters.png)

3. Use **Filtrar elementos** y "Suscripción" para limitar la lista.
4. Haga doble clic en un clúster de la lista para mostrar los detalles.

	**Menú y operaciones básicas**:

	![Portal de Azure clúster de hdinsight operaciones básicas](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)
	
	- Para personalizar el menú, haga doble clic en cualquier lugar del menú y, después, haga clic en **Personalizar**.
	- **Configuración** y **Toda la configuración**: muestra la hoja **Configuración** del clúster, que permite obtener acceso a información de configuración detallada para el clúster.
	- **Panel**, **Panel de clúster** y **Dirección URL: todas son formas de acceder al panel del clúster, que es la web de Ambari para los clústeres basados en Linux.
	- **Escritorio remoto**: conéctese al clúster mediante RDP.
	- **Escalar clúster**: permite cambiar el número de nodos de trabajo de este clúster.
	- **Eliminar**: elimina el clúster.
	- **Inicio rápido** (![icono de nube y rayo = inicio rápido](./media/hdinsight-administer-use-portal-linux/quickstart.png)): muestra información que le ayudará a empezar a usar HDInsight.
	- **Usuarios** (![icono de usuarios](./media/hdinsight-administer-use-portal-linux/users.png)): permite establecer permisos para la _administración del portal_ de este clúster para otros usuarios de su suscripción de Azure.
	
		> [AZURE.IMPORTANT] Esto _solo_ afecta al acceso y a los permisos para este clúster en el Portal de vista previa de Azure y no tiene ningún efecto sobre quién puede conectarse o enviar trabajos al clúster de HDInsight.
	- **Etiquetas** (![icono de etiqueta](./media/hdinsight-administer-use-portal-linux/tags.png)): las etiquetas te permiten establecer pares de clave-valor para definir una taxonomía personalizada de tus servicios en la nube. Por ejemplo, puede crear una clave denominada __proyecto__ y luego usar un valor común para todos los servicios asociados a un proyecto específico.
	- **Documentación**: vínculos a la documentación de HDInsight de Azure.
	
	> [AZURE.IMPORTANT] Para administrar los servicios que proporciona el clúster de HDInsight, debe usar la web de Ambari o la API de REST de Ambari. Para obtener más información sobre el uso de Ambari, consulte [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md).

	**Uso**
	
	![Botón de clúster HDInsight en el Portal de Azure](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
	
5. Haga clic en **Configuración**.

	![Botón de clúster HDInsight en el Portal de Azure](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

	- **Propiedades**: muestra las propiedades del clúster.
	- **Identidad AAD de clúster**: 
	- **Claves de almacenamiento de Azure**: consulte la cuenta de almacenamiento predeterminada y su clave. La cuenta de almacenamiento se configura durante el proceso de creación del clúster.
	- **Inicio de sesión de clúster**: cambie el nombre de usuario y la contraseña HTTP del clúster.
	- **Tiendas de metadatos externas**: consulte las tiendas de metadatos de Hive y Oozie. Las tiendas de metadatos solo pueden configurarse durante el proceso de creación del clúster.
	- **Escalar clúster**: aumente o disminuya el número de nodos de trabajo del clúster.
	- **Escritorio remoto**: habilite y deshabilite el acceso de Escritorio remoto (RDP) y configure el nombre de usuario de RDP. El nombre de usuario de RDP debe ser diferente del nombre de usuario de HTTP.
	- **Socio de registro**:
    
    > [AZURE.NOTE] Se trata de una lista genérica de opciones de configuración disponibles; no todas ellas estarán presentes en todos los tipos de clúster.

6. Haga clic en **Propiedades**.

	Esta es la lista de propiedades:
	
	- **Nombre del clúster**: nombre del clúster.
	- **Dirección URL del clúster**.
	- **Estado**: puede ser Aborted, Accepted, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Operational, Running, Error, Deleting, Deleted, Timedout, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, ClusterCustomization
	- **Región**: ubicación de Azure. Para una lista de ubicaciones de Azure admitidas, vea el cuadro de lista desplegable **Región** en [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
	- **Datos creados**.
	- **Sistema operativo**: **Windows** o **Linux**.
	- **Tipo**: Hadoop, HBase, Storm, Spark. 
	- **Versión**. Consulte [Versiones de HDInsight](hdinsight-component-versioning.md).
	- **Subscripción**: nombre de la suscripción.
	- **Id. de suscripción**.
	- **Origen de datos principal**. Cuenta de almacenamiento de blobs de Azure usada como sistema de archivos predeterminado de Hadoop.
	- **Plan de tarifa de los nodos de trabajo**.
	- **Plan de tarifa de los nodos de Head**.

##Eliminación de clústeres

Eliminar un clúster no elimina la cuenta de almacenamiento predeterminada u otras cuentas de almacenamiento vinculadas. Puede volver a crear el clúster con las mismas cuentas de almacenamiento y las mismas tiendas de metadatos.

1. Inicie sesión en el [Portal][azure-portal].
2. En el menú de la izquierda, haga clic en **Examinar todo**, **Clústeres de HDInsight** y en el nombre del clúster.
3. Haga clic en **Eliminar** en el menú superior y luego siga las instrucciones.

Vea también [Pausa o apagado de clústeres](#pauseshut-down-clusters).

##Escalado de clústeres
La característica de escalado de clústeres permite cambiar la cantidad de nodos de trabajo que usa un clúster que se ejecuta en HDInsight de Azure sin necesidad de volver a crear el clúster.

>[AZURE.NOTE] Solo son compatibles los clústeres con la versión 3.1.3 de HDInsight, o superior. Si no está seguro de la versión del clúster, puede comprobar la página de propiedades. Vea [Enumeración y visualización de clústeres](hdinsight-adminster-use-management-portal/#list-and-show-clusters).

A continuación se muestra el efecto que tiene cambiar la cantidad de nodos de datos de cada tipo de clúster compatible con HDInsight:

- Hadoop

	Puede aumentar sin ningún problema la cantidad de nodos de trabajo en un clúster de Hadoop que se encuentre en ejecución, sin que afecte a ningún trabajo pendiente o en ejecución. También se pueden enviar trabajos nuevos mientras la operación está en curso. Los errores que puedan surgir en una operación de escalado se enfrentan oportunamente, por lo que el clúster siempre queda en estado funcional.

	Cuando se realiza la reducción vertical de un clúster de Hadoop al disminuir la cantidad de nodos de datos, se reinician algunos de los servicios del clúster. Esto provoca que todos los trabajos pendientes y en ejecución fallen al completarse la operación de escalado. Sin embargo, puede volver a enviar los trabajos una vez finalizada la operación.

- HBase

	Puede agregar nodos sin problemas al clúster de HBase mientras se encuentra en ejecución, así como eliminarlos. Los servidores regionales se equilibran automáticamente en unos pocos minutos tras completar la operación de escalado. Sin embargo, puede equilibrar manualmente los servidores regionales iniciando sesión en el nodo principal del clúster y ejecutando los comandos siguientes desde una ventana del símbolo del sistema:

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	Para obtener más información sobre el uso del shell HBase, consulte
- Storm

	Puede agregar o quitar sin problemas nodos de datos de su clúster de Storm mientras se encuentra en ejecución. Sin embargo, después de finalizar correctamente la operación de escalado, deberá volver a equilibrar la topología.

	Esto se puede realizar de dos formas:

	* La interfaz de usuario web de Storm
	* La herramienta de la interfaz de línea de comandos (CLI)

	Consulte la [documentación de Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obtener más detalles.

	La interfaz de usuario web de Storm se encuentra disponible en el clúster de HDInsight:

	![reequilibrio de escalado de storm de hdinsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	El siguiente es un ejemplo de cómo usar el comando CLI para volver a equilibrar la topología de Storm:

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10


**Para escalar clústeres**

1. Inicie sesión en el [Portal][azure-portal].
2. En el menú de la izquierda, haga clic en **Examinar todo**, **Clústeres de HDInsight** y en el nombre del clúster.
3. En el menú superior, haga clic en **Configuración** y en **Escalar clúster**.
4. Escriba el **Número de nodos de trabajo**. El límite del número de nodos del clúster varía según las suscripciones de Azure. Puede ponerse en contacto con el servicio de soporte relacionado con la facturación para aumentar el límite. La información de costo reflejará los cambios realizados en el número de nodos.

	![hdinsight hadoop hbase storm spark escalar](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

##Pausa o apagado de clústeres

La mayoría de los trabajos de Hadoop son trabajos por lotes que se ejecutan sol ocasionalmente. En la mayoría de los clústeres de Hadoop, hay grandes períodos de tiempo en los que el clúster no se usa para el procesamiento. Con HDInsight, los datos se almacenan en Almacenamiento de Azure, por lo que puede eliminar un clúster de forma segura cuando no está en uso.
También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso.

Hay muchas maneras de programar el proceso:

- Usar Factoría de datos de Azure. Vea [Servicio vinculado de Azure HDInsight](../data-factory/data-factory-compute-linked-services.md/#azure-hdinsight-linked-service) y [Transformación y análisis mediante la Factoría de datos de Azure](../data-factory/data-factory-data-transformation-activities.md) para servicios vinculados de HDInsight a petición y definidos por el usuario.
- Usar Azure PowerShell. Vea [Análisis de datos de retrasos de vuelos](hdinsight-analyze-flight-delay-data.md).
- Uso de CLI de Azure. Consulte [Administrar clústeres de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md).
- Usar .NET SDK de HDInsight. Vea [Envío de trabajos de Hadoop](hdinsight-submit-hadoop-jobs-programmatically.md).

Para información sobre precios, vea [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/). Para eliminar un clúster desde el portal, vea [Eliminar clústeres](#delete-clusters).

##Cambio del nombre de usuario del clúster

Un clúster de HDInsight puede tener dos cuentas de usuario. La cuenta de usuario del clúster de HDInsight se crea durante el proceso de creación. También puede crear una cuenta de usuario de RDP para tener acceso al clúster mediante RDP. Consulte [Habilitación de escritorio remoto](#connect-to-hdinsight-clusters-by-using-rdp).

**Para cambiar el nombre de usuario y contraseña del clúster de HDInsight**

1. Inicie sesión en el [Portal][azure-portal].
2. En el menú de la izquierda, haga clic en **Examinar todo**, **Clústeres de HDInsight** y en el nombre del clúster.
3. En el menú superior, haga clic en **Configuración** y luego en **Inicio de sesión de clúster**.
4. Si la opción **Inicio de sesión de clúster** está habilitada, debe hacer clic en **Deshabilitar** y luego en **Habilitar** para poder cambiar el nombre de usuario y la contraseña.
4. Cambie el **Nombre de inicio de sesión del clúster** o la **Contraseña de inicio de sesión del clúster** y haga clic en **Guardar**.

	![hdinsight cambiar nombre de usuario contraseña de clúster http](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

##Concesión o revocación del acceso

Los clústeres de HDInsight tienen los siguientes servicios web HTTP (todos estos servicios tienen extremos RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

De manera predeterminada, estos servicios se conceden para el acceso. Puede revocar/conceder el acceso desde el Portal de Azure.

>[AZURE.NOTE] Al conceder/revocar el acceso, restablecerá el nombre de usuario y la contraseña del clúster.

**Para conceder/revocar el acceso a los servicios web de HTTP**

1. Inicie sesión en el [Portal][azure-portal].
2. En el menú de la izquierda, haga clic en **Examinar todo**, **Clústeres de HDInsight** y en el nombre del clúster.
3. En el menú superior, haga clic en **Configuración** y luego en **Inicio de sesión de clúster**.
4. Si la opción **Inicio de sesión de clúster** está habilitada, debe hacer clic en **Deshabilitar** y luego en **Habilitar** para poder cambiar el nombre de usuario y la contraseña.
6. Como **Nombre de usuario del clúster** y **Contraseña del clúster**, escriba el nombre de usuario y la contraseña nuevos (respectivamente) del clúster.
7. Haga clic en **GUARDAR**.

	![hdinsight grand eliminar acceso al servicio web http](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)




##Búsqueda de la cuenta de almacenamiento predeterminada

Cada clúster de HDInsight tiene una cuenta de almacenamiento predeterminada. La cuenta de almacenamiento predeterminada y sus claves para un clúster están en **Configuración**/**Propiedades**/**Claves de almacenamiento de Azure**. Consulte [Enumeración y visualización de clústeres](#list-and-show-clusters).

	
##Búsqueda del grupo de recursos 

En el modo ARM, cada clúster de HDInsight se crea con un grupo de recursos de Azure. El grupo de recursos de Azure al que pertenece un clúster aparece en:

- La lista de clústeres tiene una columna **Grupo de recursos**.
- Icono **Esencial** del clúster.  

Vea [Enumeración y visualización de clústeres](#list-and-show-clusters).
   
##Apertura de la consola de consulta de HDInsight

La consola de consulta de HDInsight incluye las siguientes características:

- **Galería de introducción**: para usar la galería, consulte [Información sobre Hadoop con la Galería de introducción de HDInsight de Azure](hdinsight-learn-hadoop-use-sample-gallery.md).
- **Editor Hive**: interfaz de web de GUI para el envío de trabajos de Hive. Vea [Ejecución de consultas de Hive mediante la consola de consulta](hdinsight-hadoop-use-hive-query-console.md).

	![portal de hdinsight editor hive](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)

- **Historial de trabajos**: supervise los trabajos de Hadoop.

	![portal de hdinsight historial de trabajos](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

	Haga clic en **Nombre de la consulta** para mostrar los detalles, incluidas las propiedades del trabajo, la **consulta del trabajo** y **salida del trabajo**. También puede descargar la consulta y la salida a la estación de trabajo.

- **Explorador de archivos**: explore la cuenta de almacenamiento predeterminada y las cuentas de almacenamiento vinculadas.

	![portal de hdinsight explorador de archivos explorar](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

	En la captura de pantalla, el tipo **<Account>** indica que el elemento es una cuenta de almacenamiento de Azure. Haga clic en el nombre de la cuenta para examinar los archivos.
	
- **IU de Hadoop**.

	![portal de hdinsight UI de Hadoop](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)
	
	En *IU de Hadoop* puede examinar los archivos y comprobar los registros.

- **UI de Yarn**.

	![portal de hdinsight UI de Yarn](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

##Ejecución de consultas de Hive

Para ejecutar trabajos de Hive desde el Portal, haga clic en **Editor Hive** en la consola de HDInsight Query. Vea [Apertura de la consola de consulta de HDInsight](#open-hdinsight-query-console).

##Supervisión de trabajos

Para supervisar trabajos desde el Portal, haga clic en **Historial de trabajos** en la consola de HDInsight Query. Vea [Apertura de la consola de consulta de HDInsight](#open-hdinsight-query-console).

##Examinar archivos

Para examinar los archivos almacenados en la cuenta de almacenamiento predeterminada y las cuentas de almacenamiento vinculadas, haga clic en **Explorador de archivos** en la consola de consulta de HDInsight. Vea [Apertura de la consola de consulta de HDInsight](#open-hdinsight-query-console).

También puede usar la utilidad **Examinar el sistema de archivos** de la **UI de Hadoop** en la consola de HDInsight. Vea [Apertura de la consola de consulta de HDInsight](#open-hdinsight-query-console).



##Supervisión del uso de los clústeres

En la sección __Uso__ de la hoja del clúster de HDInsight se muestra información sobre el número de núcleos disponibles con la suscripción para su uso con HDInsight, así como el número de núcleos asignados al clúster y cómo se asignan a los nodos de este clúster. Vea [Enumeración y visualización de clústeres](#list-and-show-clusters).

> [AZURE.IMPORTANT] Para supervisar los servicios que proporciona el clúster de HDInsight, debe usar la web de Ambari o la API de REST de Ambari. Para más información sobre el uso de Ambari, vea [Administración de clústeres de HDInsight con Ambari](hdinsight-hadoop-manage-ambari.md).


##Apertura de la IU de Hadoop

Para supervisar el clúster, examinar el sistema de archivos y comprobar los registros, haga clic en **UI de Hadoop** en la consola de consulta de HDInsight. Vea [Apertura de la consola de consulta de HDInsight](#open-hdinsight-query-console).

##Apertura de la UI de Yarn

Para usar la interfaz de usuario de Yarn, haga clic en **UI de Yarn** en la consola de consulta de HDInsight. Vea [Apertura de la consola de consulta de HDInsight](#open-hdinsight-query-console).

##Conexión a los clústeres mediante RDP

Las credenciales para el clúster que proporcionó en su creación dan acceso a los servicios en el clúster, pero no al clúster mismo a través del Escritorio remoto. Puede activar el acceso de Escritorio remoto cuando aprovisiona un clúster o después de su aprovisionamiento. Para instrucciones sobre cómo habilitar Escritorio remoto durante la creación, vea [Creación de clústeres de HDInsight](hdinsight-provision-clusters.md).

**Para habilitar el Escritorio remoto**

1. Inicie sesión en el [Portal][azure-portal].
2. En el menú de la izquierda, haga clic en **Examinar todo**, **Clústeres de HDInsight** y en el nombre del clúster.
3. En el menú superior, haga clic en **Configuración** y en **Escritorio remoto**.
4. Especifique los valores de **Expira en**, **Nombre de usuario de Escritorio remoto** y **Contraseña de Escritorio remoto** y haga clic en **Habilitar**.

	![hdinsight habilitar deshabilitar configurar escritorio remoto](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

	El valor predeterminado de Expira en es de una semana.
> [AZURE.NOTE] También puede usar .NET SDK de HDInsight para habilitar el Escritorio remoto en un clúster. Use el método **EnableRdp** en el objeto de cliente de HDInsight de la siguiente manera: **client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**. De forma similar, para deshabilitar el Escritorio remoto en el clúster, puede usar **client.DisableRdp(clustername, location)**. Para obtener más información sobre estos métodos, consulte [Referencia de .NET SDK de HDInsight](http://go.microsoft.com/fwlink/?LinkId=529017). Esto solo se aplica a los clústeres de HDInsight que se ejecutan en Windows.

**Para conectarse a un clúster con RDP**

1. Inicie sesión en el [Portal][azure-portal].
2. En el menú de la izquierda, haga clic en **Examinar todo**, **Clústeres de HDInsight** y en el nombre del clúster.
3. En el menú superior, haga clic en **Configuración** y en **Escritorio remoto**.
4. Haga clic en **Conectar** y siga las instrucciones. Si Conectar está deshabilitado, debe habilitarlo primero. Asegúrese de usar el nombre de usuario y la contraseña de Escritorio remoto. No puede usar las credenciales de usuario del clúster.


##Apertura de la línea de comandos de Hadoop

Para conectarse al clúster mediante el Escritorio remoto y usar la línea de comandos de Hadoop, primero debe tener habilitado el acceso del Escritorio remoto al clúster como se describe en la sección anterior.

**Para abrir una línea de comandos de Hadoop**

1. Conéctese al clúster a través del Escritorio remoto.
8. En el escritorio, haga doble clic en **Línea de comandos de Hadoop**.

	![HDI.HadoopCommandLine][image-hadoopcommandline]

	Para obtener más información acerca de los comandos de Hadoop, consulte [Referencia de comandos de Hadoop](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

En la captura de pantalla anterior, el nombre de la carpeta tiene el número de versión de Hadoop incrustado. El número de versión se puede cambiar según la versión de los componentes de Hadoop instalados en el clúster. Puede usar las variables de entorno de Hadoop para referirse a esas carpetas. Por ejemplo:

	cd %hadoop_home%
	cd %hive_home%
	cd %hbase_home%
	cd %pig_home%
	cd %sqoop_home%
	cd %hcatalog_home%
	
##Pasos siguientes
En este artículo, ha aprendido a crear un clúster de HDInsight mediante el Portal y a abrir la herramienta de línea de comandos de Hadoop. Para obtener más información, consulte los artículos siguientes:

* [Administración de HDInsight con PowerShell de Azure](hdinsight-administer-use-powershell.md)
* [Administración de HDInsight con la CLI de Azure](hdinsight-administer-use-command-line.md)
* [Creación de clústeres de HDInsight](hdinsight-provision-clusters.md)
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md)
* [¿Qué versión de Hadoop tiene HDInsight de Azure?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Línea de comandos de Hadoop"

<!---HONumber=AcomDC_0218_2016-->