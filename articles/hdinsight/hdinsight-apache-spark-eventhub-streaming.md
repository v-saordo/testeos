<properties 
	pageTitle="Use los centros de eventos de Azure con Apache Spark en HDInsight para procesar los datos de transmisión por secuencias | Microsoft Azure" 
	description="Instrucciones detalladas sobre cómo enviar una transmisión de datos al Centro de eventos de Azure y, luego, recibir dichos eventos en Spark mediante una aplicación de Scala" 
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


# Streaming de Spark: proceso de eventos desde Centros de eventos de Azure con Apache Spark en HDInsight (Linux)

La transmisión de Spark amplía la API de Spark de núcleo para crear aplicaciones escalables de procesamiento de transmisión de alto rendimiento y tolerantes a errores. Los datos pueden ser introducidos desde varios orígenes. En este artículo se usan Centros de eventos de Azure para introducir datos. Los Centros de eventos son un sistema de introducción altamente escalable que introduce millones de eventos por segundo.

En este tutorial aprenderá a crear un Centro de eventos de Azure, a introducir mensajes en un Centro de eventos mediante una aplicación de consola en Java y a recuperarlos en paralelo mediante una aplicación Spark escrita en Scala. Esta aplicación consume los datos que se transmiten a través de Centros de eventos y los enruta a diferentes salidas (blob de Almacenamiento de Azure, tabla de Hive y tabla SQL).

> [AZURE.NOTE] Para seguir las instrucciones de este artículo, tendrá que usar las dos versiones del portal de Azure. Para crear un Centro de eventos, deberá usar el [Portal de Azure](https://manage.windowsazure.com). Para trabajar con el clúster de HDInsight Spark, deberá usar el [Portal de vista previa de Azure](https://ms.portal.azure.com/).

**Requisitos previos:**

Debe tener lo siguiente:

- Una suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un clúster Apache Spark. Para obtener instrucciones, consulte [Aprovisionamiento de clústeres Apache Spark en HDInsight mediante opciones personalizadas](hdinsight-apache-spark-jupyter-spark-sql.md).
- Kit de desarrollo de Oracle Java. Se puede instalar desde [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Un IDE de Java. En este artículo se usa IntelliJ IDEA 15.0.1. Se puede instalar desde [aquí](https://www.jetbrains.com/idea/download/).
- Microsoft JDBC Driver para SQL Server, v4.1 o posterior. Se requiere para escribir los datos de eventos en una base de datos de SQL Server. Se puede instalar desde [aquí](https://msdn.microsoft.com/sqlserver/aa937724.aspx).
- Una base de datos SQL de Azure. Para obtener instrucciones, consulte [Tutorial de Base de datos SQL: creación de una Base de datos SQL en cuestión de minutos con datos de ejemplo y el Portal de Azure](../sql-database/sql-database-get-started.md)

## ¿Cómo funciona la solución?

Así es como fluye la solución de streaming:

1. Cree un Centro de eventos de Azure, que será el que reciba una transmisión de eventos.

2. Ejecute una aplicación local autónoma que genere eventos y los inserte en el Centro de eventos de Azure. La aplicación de ejemplo que lo hace se publica en [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples).

2. Ejecute una aplicación de streaming de forma remota en un clúster de Spark que lea los eventos de streaming del Centro de eventos de Azure y lo inserte en diferentes ubicaciones (Blob de Azure, tabla de Hive y tabla de Base de datos SQL).

## Creación de un Centro de eventos de Azure

1. En el [Portal de Azure](https://manage.windowsazure.com), seleccione **NUEVO** > **Bus de servicio** > **Centro de eventos** > **Creación personalizada**.

2. En la pantalla **Agregar un nuevo centro de eventos**, escriba un **Nombre del centro de eventos**, seleccione la **Región** donde se va a crear el centro y cree un nuevo espacio de nombres o seleccione uno existente. Haga clic en la **Flecha** para continuar.

	![página 1 del asistente](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.create.event.hub.png "Crear un centro de eventos de Azure")

	> [AZURE.NOTE] Para reducir la latencia y los costos, debe seleccionar la misma **ubicación** que la del clúster Apache Spark en HDInsight.

3. En la pantalla **Configuración del centro de eventos**, escriba los valores **Recuento de particiones** y **Retención de mensajes** y, a continuación, haga clic en la marca de verificación. En este ejemplo, utilice un recuento de particiones de 10 y una retención de mensajes de 1. Anote el recuento de particiones, porque necesitará más adelante este valor.

	![página 2 del asistente](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.create.event.hub2.png "Especificar los días de retención y el tamaño de la partición para el centro de eventos")

4. Haga clic en el Centro de eventos que ha creado, en **Configurar** y, después, cree dos directivas de acceso para el Centro de eventos.

	<table>
<tr><th>Nombre</th><th>Permisos</th></tr>
<tr><td>mysendpolicy</td><td>Los métodos Send</td></tr>
<tr><td>myreceivepolicy</td><td>Escuchar</td></tr>
</table>Después de crear los permisos, seleccione el icono **Guardar** en la parte inferior de la página. Así se crean las directivas de acceso compartido que se usarán para enviar (**mysendpolicy**) y escuchar (**myreceivepolicy**) a este Centro de eventos.

	![directivas](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policies.png "Crear directivas de centro de eventos")

	
5. En la misma página, tome nota de las claves de directiva que se generan para las dos directivas. Guarde estas claves, ya que se usarán después.

	![claves de directiva](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policy.keys.png "Guardar claves de directiva")

6. En la página **Panel**, haga clic en **Información de conexión** desde la parte inferior para recuperar y guardar las cadenas de conexión para el centro de eventos mediante las dos directivas.

	![claves de directiva](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policy.connection.strings.png "Guardar cadenas de conexión de directivas")

## Uso de una aplicación de Scala para enviar mensajes al Centro de eventos

En esta sección se usa una aplicación de Scala autónoma local para enviar una transmisión de eventos al Centro de eventos de Azure que se creó en el paso anterior. Esta aplicación está disponible en GitHub en [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer). En los siguientes pasos se asume que ya ha bifurcado este repositorio de GitHub.

1. Abra la aplicación, **EventhubsSampleEventProducer**, en IntelliJ IDEA.
	
2. Compile el proyecto. En el menú **Compilar**, haga clic en **Crear proyecto**. El archivo JAR de salida se crea en **\\out\\artifacts**.

>[AZURE.TIP] También puede usar una opción disponible en IntelliJ IDEA para crear el proyecto directamente desde un repositorio de GitHub. Para entender cómo usar dicho enfoque, siga las instrucciones de la siguiente sección. Tenga en cuenta que muchos de los pasos que se describen en la siguiente sección no se pueden aplicar a la aplicación de Scala que cree en este paso. Por ejemplo:

> * No tendrá que actualizar el archivo POM para incluir la versión de Spark, ya que no se depende de Spark para crear esta aplicación.
> * No tendrá que agregar archivos JAR de dependencia a la biblioteca de proyectos, ya que el proyecto no requiere dichos archivos.

## Actualización de la aplicación de streaming de Scala para recibir eventos

En [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples) está disponible una aplicación de Scala de ejemplo para recibir el evento y enrutarlo a diferentes destinos. Siga los pasos que se indican a continuación para actualizar la aplicación y crear el archivo JAR de salida.

1. Inicie IntelliJ IDEA y en la pantalla de inicio, seleccione **Check out from Version Control** (Extraer del repositorio de control de versiones) y, después, haga clic en **Git**.
		
	![Obtener orígenes de Git](./media/hdinsight-apache-spark-eventhub-streaming/get-source-from-git.png)

2. En el cuadro de diálogo **Clone Repository** (Clonar repositorio), indique la dirección URL del repositorio de Git desde el que se va a realizar la clonación, especifique el directorio en el que se va a realizar la clonación y, después, haga clic en **Clone** (Clonar).

	![Clonar de Git](./media/hdinsight-apache-spark-eventhub-streaming/clone-from-git.png)

	
3. Siga las indicaciones hasta que el proyecto se haya clonado completamente. Presione **Alt + 1** para abrir la **Project View** (Vista de proyecto). Debería ser similar a la siguiente.

	![Vista de proyecto](./media/hdinsight-apache-spark-eventhub-streaming/project-view.png)
	
4. Abra pom.xml y asegúrese de que la versión de Spark es correcta. En el <properties> nodo, busque el siguiente fragmento y compruebe la versión de Spark.

		<scala.version>2.10.4</scala.version>
    	<scala.compat.version>2.10.4</scala.compat.version>
    	<scala.binary.version>2.10</scala.binary.version>
    	<spark.version>1.5.1</spark.version>

	Asegúrese de que el valor **spark.version** está establecido en **1.5.1**.

5. La aplicación requiere dos archivos JAR de dependencia:

	* **Archivo JAR de receptor de EventHub**. Requerido para que Spark reciba los mensajes del Centro de eventos. Este archivo JAR está disponible en el clúster Spark Linux en `/usr/hdp/current/spark-client/lib/spark-streaming-eventhubs-example-1.5.2.2.3.3.1-7-jar-with-dependencies.jar`. Para copiar el archivo JAR en el equipo local se puede usar pscp.

			pscp sshuser@mysparkcluster-ssh.azurehdinsight.net:/usr/hdp/current/spark-client/lib/spark-streaming-eventhubs-example-1.5.2.2.3.3.1-7-jar-with-dependencies.jar C:/eventhubjar

		Así se copiará el archivo JAR desde el clúster Spark en el equipo local.

	* **Archivo JAR de JDBC Driver** Se requiere para escribir los mensajes recibidos desde el Centro de eventos en una Base de datos SQL de Azure. La versión 4.1, o las versiones posteriores de este archivo JAR, se pueden descargar desde [aquí](https://msdn.microsoft.com/es-ES/sqlserver/aa937724.aspx).
	

		Agregue referencia a estos archivos JAR a la biblioteca de proyectos. Lleve a cabo los siguiente pasos:

		1. En la ventana de IntelliJ IDEA en la que la aplicación está abierta, haga clic en **File** (Archivo), **Project Structure** (Estructura de proyecto), y luego en **Libraries** (Bibliotecas). 

			![agregar dependencias que faltan](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "Agregar archivos JAR de dependencia que faltan")

			Haga clic en el icono de agregar (![icono Agregar](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png)), haga clic en **Java**, y luego navegue hasta la ubicación en que descargó el archivo JAR de receptor de EventHub. Siga las indicaciones para agregar el archivo JAR a la biblioteca de proyectos.

		1. Repita el paso anterior para agregar también el archivo JAR de JDBC a la biblioteca de proyectos.
	
			![agregar dependencias que faltan](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "Agregar archivos JAR de dependencia que faltan")

		1. Haga clic en **Apply**.

6. Cree el archivo JAR de salida. Lleve a cabo los siguiente pasos.
	1. En el cuadro de diálogo **Project Structure** (Estructura de proyecto), haga clic en **Artifacts** (Artefactos) y, después, haga clic en el signo más. En el cuadro de diálogo emergente, haga clic en **JAR** y luego haga clic en **From modules with dependencies** (Desde módulos con dependencias).

		![Crear archivo JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-1.png)

	1. En el cuadro de diálogo **Create JAR from Modules** (Crear archivo JAR desde módulos), haga clic en el botón de puntos suspensivos (![puntos suspensivos](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png)) en el **clase principal**.

	1. En el cuadro de diálogo **Select Main Class** (Seleccionar clase principal), seleccione cualquiera de las clases disponibles y, después, haga clic en **OK** (Aceptar).

		![Crear archivo JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-2.png)

	1. En el cuadro de diálogo **Create JAR from Modules** (Crear archivo JAR desde módulos), asegúrese de que está seleccionada la opción para **extraer al archivo JAR de destino** y, después, haga clic en **OK** (Aceptar). Así se crea un archivo JAR único con todas las dependencias.

		![Crear archivo JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-3.png)

	1. La pestaña **Output Layout** (Diseño de salida) enumera todos los archivos JAR que forman parte del proyecto Maven. Puede seleccionar y eliminar aquellos de los que la aplicación de Scala no tenga ninguna dependencia directa. En el caso de la aplicación que creamos aquí, puede quitar todas las instancias de (**salida de compilación de microsoft-spark-streaming-examples**) menos la última. Seleccione los archivos JAR que va a eliminar y, después, haga clic en el icono **Delete** (Eliminar) (![eliminar icono](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png)).

		![Crear archivo JAR](./media/hdinsight-apache-spark-eventhub-streaming/delete-output-jars.png)

		Asegúrese de que la casilla **Build on make** (Compilar al crear) está activada, lo que garantiza que el archivo jar se crea cada vez que el proyecto se compila o actualiza. Haga clic en **Apply** (Aplicar) y, a continuación, en **Aceptar**.

	1. En la pestaña **Output Layout** (Diseño de salida), en la parte inferior del cuadro de Available Elements (Elementos disponibles), tendrá los dos archivos JAR de dependencia que agregó anteriormente a la biblioteca de proyectos. Debe agregarlos a la pestaña Output Layout (Diseño de salida). Haga clic con el botón derecho en cada archivo JAR y, después, haga clic en **Extract Into Output Root** (Extraer en raíz de salida).

		![Extraer archivo JAR de dependencia](./media/hdinsight-apache-spark-eventhub-streaming/extract-dependency-jar.png)

		Repita este paso en el otro archivo JAR de dependencia. La pestaña **Output Layout** (Diseño de salida) debe ser como la siguiente.

		![Pestaña de salida final](./media/hdinsight-apache-spark-eventhub-streaming/final-output-tab.png)

		En el cuadro de diálogo **Project Structure** (Estructura de proyecto), haga clic en **Apply** (Aplicar) y, después, haga clic en **OK** (Aceptar).

	1. En la barra de menús, haga clic en **Build** (Compilar) y, después, haga clic en **Make Project** (Crear proyecto). También puede hacer clic en **Build Artifacts** (Compilar artefactos) para crear el archivo JAR. El archivo JAR de salida se crea en **\\out\\artifacts**.

		![Crear archivo JAR](./media/hdinsight-apache-spark-create-standalone-application/output.png)

## Ejecución remota de aplicaciones en un clúster Spark mediante Livy

Usaremos Livy para ejecutar la aplicación de streaming de forma remota en un clúster de Spark. Para obtener una explicación detallada sobre cómo usar Livy con un clúster de HDInsight Spark, consulte [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md). Antes de empezar a ejecutar los trabajos remotos en los eventos de transmisión mediante Spark debe realizar dos operaciones:

1. Inicie la aplicación autónoma local para generar eventos y enviarlos al Centro de eventos. Utilice el siguiente comando para hacerlo:

		java -cp EventhubsSampleEventProducer.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. Copie el archivo JAR de streaming (**microsoft-spark-streaming-examples.jar**) en el Almacenamiento de blobs de Azure asociado con el clúster. Esto permite que Livy pueda acceder al archivo JAR. Puede usar [**AzCopy**](../storage/storage-use-azcopy.md), una utilidad de línea de comandos, para hacerlo. Hay muchos otros clientes que se pueden usar para cargar datos. Puede encontrar más información al respecto en [Carga de datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md).

3. Instale CURL en el equipo desde el que se ejecutan estas aplicaciones. CURL se usa para invocar los puntos de conexión de Livy para ejecutar los trabajos de forma remota.

### Ejecución de las aplicaciones para recibir los eventos en un blob de Almacenamiento de Azure como texto

Abra un símbolo del sistema, navegue al directorio en que instaló CURL y ejecute el siguiente comando (reemplace el nombre de usuario y la contraseña, y el nombre del clúster):

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

A continuación se definen los parámetros del archivo **inputBlob.txt**:

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Estos son los parámetros del archivo de entrada:

* **file** es la ruta de acceso al archivo JAR de la aplicación en la cuenta de Almacenamiento de Azure asociada con el clúster.
* **className** es el nombre de la clase en el archivo JAR.
* **args** es la lista de argumentos requeridos por la clase
* **numExecutors** es el número de núcleos que usa Spark para ejecutar la aplicación de streaming. Siempre debe ser al menos dos veces el número de particiones del Centro de eventos.
* **executorMemory**, **executorCores** y **driverMemory** son los parámetros que se usan para asignar los recursos requeridos a la aplicación de streaming.

>[AZURE.NOTE] No es preciso crear las carpetas de salida (EventCheckpoint, EventCount/EventCount10) que se usan como parámetros. La aplicación de streaming las crea automáticamente.
	
Al ejecutar el comando, debería ver una salida similar a la siguiente:

	< HTTP/1.1 201 Created
	< Content-Type: application/json; charset=UTF-8
	< Location: /18
	< Server: Microsoft-IIS/8.5
	< X-Powered-By: ARR/2.5
	< X-Powered-By: ASP.NET
	< Date: Tue, 01 Dec 2015 05:39:10 GMT
	< Content-Length: 37
	<
	{"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

Anote el identificador de lote de la última línea de la salida (en este ejemplo es '1'). Para comprobar que la aplicación se ejecuta correctamente, puede examinar la cuenta de Almacenamiento de Azure asociada con el clúster, donde debería ver que se ha creado la carpeta **/EventCount/EventCount10**. Dicha carpeta debe contener blobs que capturen el número de eventos procesados en el período especificado para el parámetro **batch-interval-in-seconds**.

La aplicación seguirá ejecutándose hasta que la elimine. Para ello, utilice el siguiente comando:

	curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### Ejecución de las aplicaciones para recibir los eventos en un blob de Almacenamiento de Azure como JSON

Abra un símbolo del sistema, navegue al directorio en que instaló CURL y ejecute el siguiente comando (reemplace el nombre de usuario y la contraseña, y el nombre del clúster):

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

A continuación se definen los parámetros del archivo **inputJSON.txt**:

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Los parámetros son similares a los que especificó para la salida de texto en el paso anterior. Una vez más, no es preciso crear las carpetas de salida (EventCheckpoint, EventCount/EventCount10) que se usan como parámetros. La aplicación de streaming las crea automáticamente.

 Tras ejecutar el comando puede examinar la cuenta de Almacenamiento de Azure asociada con el clúster, donde debería ver que se ha creado la carpeta **/EventStore10**. Si abre cualquier archivo con el prefijo **part-**, debería ver los eventos procesados en un formato JSON.

### Ejecución de las aplicaciones para recibir los eventos en una tabla de Hive

Para ejecutar la aplicación que transmite los eventos a una tabla de Hive se necesitan varios componentes adicionales. Dichos componentes son:

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* datanucleus-core-3.2.10.jar
* hive-site.xml

Los archivos **.jar** están disponibles en el clúster de HDInsight Spark en `/usr/hdp/current/spark-client/lib`. El archivo **hive-site.xml** está disponible en `/usr/hdp/current/spark-client/conf`.

Puede usar [WinScp](http://winscp.net/eng/download.php) para copiar estos archivos desde el clúster al equipo local. A continuación, puede usan herramientas para copiar estos archivos en su cuenta de almacenamiento asociada con el clúster. Para más información acerca de cómo cargar archivos en la cuenta de almacenamiento, consulte [Carga de datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md).

Una vez que haya copiado los archivos a la cuenta de Almacenamiento de Azure, abra un símbolo del sistema, navegue al directorio en que instaló CURL y ejecute el siguiente comando (reemplace el nombre de usuario y la contraseña, y el nombre del clúster):

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

A continuación se definen los parámetros del archivo **inputHive.txt**:

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasb:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasb:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasb:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasb:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Los parámetros son similares a los que especificó para la salida de texto en los pasos anteriores. Una vez más, no es preciso crear las carpetas de salida (EventCheckpoint, EventCount/EventCount10) ni la tabla de Hive de salida (EventHiveTable10) que se usan como parámetros. La aplicación de streaming las crea automáticamente. Tenga en cuenta que la opción **jars** y **files** incluye rutas de acceso a los archivos .jar y hive-site.xml que copió a la cuenta de almacenamiento.

Para comprobar que la tabla de Hive se ha creado correctamente, puede usar SSH en el clúster y ejecutar consultas de Hive. Para obtener instrucciones, consulte [Uso de Hive con Hadoop en HDInsight con SSH](hdinsight-hadoop-use-hive-ssh.md). Una vez que se haya conectado mediante SSH, puede ejecutar el comando siguiente para comprobar que se ha creado la tabla de Hive, **EventHiveTable10**.

	show tables;

Debería ver una salida similar a la siguiente:

	OK
	eventhivetable10
	hivesampletable

También puede ejecutar una consulta SELECT para ver el contenido de la tabla.

	SELECT * FROM eventhivetable10 LIMIT 10;

Debe ver algo parecido a lo siguiente:

	ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
	sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
	o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
	TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
	HKCpPlWFWAJILwR69MAq863nCWYzDEw6
	Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
	85dRppSBSbZgThLr1s0GMgKqynDUqudr
	5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
	ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
	vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
	Time taken: 4.434 seconds, Fetched: 10 row(s)


### Ejecución de las aplicaciones para recibir los eventos en una tabla de Base de datos SQL de Azure

Antes de realizar este paso, asegúrese de que ha creado una Base de datos SQL de Azure. Necesitará los valores de nombre de la base de datos, nombre del servidor de la base de datos y las credenciales del administrador de la base de datos como parámetros. Sin embargo, no es preciso crear la tabla de base de datos. La aplicación de streaming la crea automáticamente.

Abra un símbolo del sistema, navegue al directorio en que instaló CURL y ejecute el siguiente comando:

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

A continuación se definen los parámetros del archivo **inputSQL.txt**:

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Para comprobar que la aplicación se ejecuta correctamente, puede conectarse a la Base de datos SQL de Azure mediante SQL Server Management Studio. Para obtener instrucciones sobre cómo hacerlo, consulte [Conexión a la Base de datos SQL con SQL Server Management Studio](sql-database/sql-database-connect-query-ssms). Una vez que se haya conectado a la base de datos, puede navegar a la tabla **EventContent** que creó la aplicación de streaming. Para obtener los datos de la tabla, puede ejecutar una consulta rápida. Ejecute la siguiente consulta:

	SELECT * FROM EventCount

Debería ver una salida similar a la siguiente:

	00046b0f-2552-4980-9c3f-8bba5647c8ee
	000b7530-12f9-4081-8e19-90acd26f9c0c
	000bc521-9c1b-4a42-ab08-dc1893b83f3b
	00123a2a-e00d-496a-9104-108920955718
	0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
	001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
	001vIZgOStka4DXtud0e3tX7XbfMnZrN
	00220586-3e1a-4d2d-a89b-05c5892e541a
	0029e309-9e54-4e1b-84be-cd04e6fce5ec
	003333cf-874f-4045-9da3-9f98c2b4ea49
	0043c07e-8d73-420a-9af7-1fcb94575356
	004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9

	
## <a name="seealso"></a>Otras referencias


* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### Escenarios

* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark con Aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Análisis del registro de sitios web con Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Creación y ejecución de aplicaciones

* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Envío de trabajos de Spark de manera remota mediante Livy con clústeres de Spark en HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md)

### Herramientas y extensiones

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applications (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones Scala Spark)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponibles para cuadernos de Jupyter Notebook en un clúster de Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Administración de recursos

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0218_2016-->