<properties 
	pageTitle="Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight Linux | Azure" 
	description="Instrucciones paso a paso sobre cómo usar cuadernos de Zeppelin con clústeres Spark en HDInsight Linux." 
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
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight (Linux)

Obtenga información sobre cómo instalar los cuadernos de Zeppelin en clústeres Spark y cómo usar los cuadernos de Zeppelin.

> [AZURE.IMPORTANT] Cuaderno de Zeppelin Notebook para un clúster de HDInsight Spark es una oferta solo para mostrar cómo usar Zeppelin en un entorno de Azure HDInsight Spark. Si desea utilizar cuadernos para trabajar con HDInsight Spark, se recomienda utilizar cuadernos de Jupyter Notebook. Los cuadernos de Jupyter Notebook también proporcionan diferentes opciones de kernel, como Scala, y se seguirán mejorando sus características. Para obtener instrucciones acerca de cómo usar cuadernos de Jupyter Notebook con HDInsight Spark, consulte [Ejecución de consultas Spark SQL mediante un cuaderno de Jupyter](hdinsight-apache-spark-jupyter-spark-sql.md#jupyter).

**Requisitos previos:**

* Antes de comenzar este tutorial, debe tener una suscripción a Azure. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un clúster Apache Spark. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-provision-clusters.md).
* Un cliente SSH. Para las distribuciones de Linux y Unix o Macintosh OS X, el comando `ssh` se proporciona con el sistema operativo. Para Windows, se recomienda [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

	> [AZURE.NOTE] Si desea usar un cliente SSH distinto de `ssh` o PuTTY, vea la documentación de su cliente sobre cómo establecer un túnel SSH.

* Un explorador web que se puede configurar para usar un proxy SOCKS

* __(opcional)__: un complemento como [FoxyProxy](http://getfoxyproxy.org/,) que puede aplicar reglas que se limitan a enrutar solicitudes específicas a través del túnel.

	> [AZURE.WARNING] Sin un complemento como FoxyProxy, todas las solicitudes realizadas a través del explorador se pueden enrutar a través del túnel. Esto puede producir una carga más lenta de las páginas web en el explorador.

## Instalación de Zeppelin como parte de la creación del clúster

Puede instalar Zeppelin en un clúster Spark mediante la acción de scripts. La acción de scripts usa scripts personalizados para instalar los componentes en el clúster que no están disponibles de forma predeterminada. El script personalizado para instalar Zeppelin en un clúster Spark está disponible en **https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh**.

### Uso del portal de Azure

Para obtener instrucciones sobre cómo usar SDK .NET de HDInsight para ejecutar acciones de scripts para instalar Zeppelin, vea [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-azure-portal). Debe realizar un par de cambios en las instrucciones de ese artículo.

* Debe utilizar el script para instalar Zeppelin. El script que hay que usar es **https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh**.

* Debe ejecutar la acción de scripts solo en el nodo principal.

* El script no necesita ningún parámetro.

### Uso del SDK .NET de HDInsight

Para obtener instrucciones sobre cómo usar SDK .NET de HDInsight para ejecutar acciones de scripts para instalar Zeppelin, vea [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-from-the-hdinsight-net-sdk). Debe realizar un par de cambios en las instrucciones de ese artículo.

* Debe utilizar el script para instalar Zeppelin. El script que hay que usar es **https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh**.

* El script no necesita ningún parámetro.

* Establece el tipo de clúster que se va a crear en Spark.

### Uso de Azure PowerShell

Utilice el siguiente fragmento de código de PowerShell para crear un clúster Spark en HDInsight Linux con Zeppelin instalado. Asegúrese de tener PowerShell instalado antes de continuar. Vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md) para obtener instrucciones.

	Login-AzureRMAccount
	
	# PROVIDE VALUES FOR THE VARIABLES
	$clusterAdminUsername="admin"
	$clusterAdminPassword="<<password>>"
	$clusterSshUsername="adminssh"
	$clusterSshPassword="<<password>>"
	$clusterName="<<clustername>>"
	$clusterContainerName=$clusterName
	$resourceGroupName="<<resourceGroupName>>"
	$location="<<region>>"
	$storage1Name="<<storagename>>"
	$storage1Key="<<storagekey>>"
	$subscriptionId="<<subscriptionId>>"
	
	Select-AzureRmSubscription -SubscriptionId $subscriptionId
	
	$passwordAsSecureString=ConvertTo-SecureString $clusterAdminPassword -AsPlainText -Force
	$clusterCredential=New-Object System.Management.Automation.PSCredential ($clusterAdminUsername, $passwordAsSecureString)
	$passwordAsSecureString=ConvertTo-SecureString $clusterSshPassword -AsPlainText -Force
	$clusterSshCredential=New-Object System.Management.Automation.PSCredential ($clusterSshUsername, $passwordAsSecureString)
	
	$azureHDInsightConfigs= New-AzureRmHDInsightClusterConfig -ClusterType Spark
	$azureHDInsightConfigs.DefaultStorageAccountKey = $storage1Key
	$azureHDInsightConfigs.DefaultStorageAccountName = "$storage1Name.blob.core.windows.net"
	
	Add-AzureRMHDInsightScriptAction -Config $azureHDInsightConfigs -Name "Install Zeppelin" -NodeType HeadNode -Parameters "void" -Uri "https://hdiconfigactions.blob.core.windows.net/linuxincubatorzeppelinv01/install-zeppelin-spark151-v01.sh"
	
	New-AzureRMHDInsightCluster -Config $azureHDInsightConfigs -OSType Linux -HeadNodeSize "Standard_D12" -WorkerNodeSize "Standard_D12" -ClusterSizeInNodes 2 -Location $location -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $clusterCredential -DefaultStorageContainer $clusterContainerName -SshCredential $clusterSshCredential -Version "3.3"
 
## Configuración de la tunelización SSH para acceder a un cuaderno de Zeppelin

Usará los túneles SSH para acceder a los cuadernos de Zeppelin que se ejecutan en clústeres Spark en HDInsight Linux. Los pasos siguientes muestran cómo crear un túnel SSH con la línea de comandos ssh (Linux) y PuTTY (Windows).

### Creación de un túnel con el comando SSH (Linux)

Use el siguiente comando para crear un túnel SSH con el comando `ssh`. Reemplace __USERNAME__ por un usuario SSH para su clúster de HDInsight y reemplace __CLUSTERNAME__ por el nombre de su clúster de HDInsight.

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

Con esto se crea una conexión que enruta el tráfico al puerto local 9876 al clúster sobre SSH. Las opciones son:

* **D 9876**: el puerto local que enrutará el tráfico a través del túnel.

* **C**: comprimir todos los datos, debido a que el tráfico web es principalmente texto.

* **2**: forzar a SSH a que intente solo la versión 2 del protocolo.

* **q**: modo silencioso.

* **T**: deshabilitar la asignación seudotty, debido a que solo estamos desviando un puerto.

* **n**: evitar la lectura de STDIN, debido a que solo estamos desviando un puerto.

* **N**: no ejecutar un comando remoto, debido a que solo estamos desviando un puerto.

* **f**: ejecutar en segundo plano.

Si ha configurado el clúster con una clave SSH, es posible que tenga que usar el parámetro `-i` y especificar la ruta de acceso a la clave SSH privada.

Una vez que se completa el comando, el tráfico enviado al puerto 9876 del equipo local se enrutará sobre Capa de sockets seguros (SSL) al nodo principal del clúster y aparecerá como originado ahí.

### Creación de un túnel mediante PuTTY (Windows)

Use los siguientes pasos para crear un túnel SSH con PuTTY.

1. Abra PuTTY y escriba la información de conexión. Si no está familiarizado con PuTTY, vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obtener información sobre cómo usarlo con HDInsight.

2. En la sección **Category** (Categoría) a la izquierda del cuadro de diálogo, expanda **Connection** (Conexión), **SSH** y, a continuación, seleccione **Tunnels** (Túneles).

3. Proporcione la siguiente información en el formulario **Options controlling SSH port forwarding** (Opciones que controlan el desvío de puertos SSH):

	* **Source port**: el puerto en el cliente que desea desviar. Por ejemplo, **9876**.

	* **Destination**: la dirección SSH del clúster de HDInsight basado en Linux. Por ejemplo, **mycluster-ssh.azurehdinsight.net**.

	* **Dynamic**: habilita el enrutamiento dinámico del proxy SOCKS.

	![imagen de las opciones de tunelización](./media/hdinsight-apache-spark-use-zeppelin-notebook/puttytunnel.png)

4. Haga clic en **Add** (Agregar) para agregar la configuración y, a continuación, en **Open** (Abrir) para abrir una conexión SSH.

5. Cuando se le solicite, inicie sesión en el servidor. Esto establecerá una sesión SSH y habilitará el túnel.

### Uso del túnel desde el explorador

> [AZURE.NOTE] Los pasos de esta sección usan el explorador FireFox, ya que es gratuito para sistemas Linux, Unix, Macintosh OS X y Windows. Otros exploradores modernos como Google Chrome, Microsoft Edge o Apple Safari deberían funcionar; sin embargo, el complemento FoxyProxy usado en algunos pasos no está disponible para todos los exploradores.

1. Configure el explorador para usar **localhost:9876** como un proxy **SOCKS v5**. La configuración de Firefox se verá de la siguiente manera. Si usa un puerto que no es 9876, cambie el puerto al que usa:

	![imagen de la configuración de Firefox](./media/hdinsight-apache-spark-use-zeppelin-notebook/socks.png)

	> [AZURE.NOTE] Seleccionar **DNS remoto** se resuelven las solicitudes del sistema de nombres de dominio (DNS) mediante el uso de un clúster de HDInsight. Si no se selecciona, el DNS se resolverá de manera local.

2. Compruebe que el tráfico se enruta a través del túnel en un sitio como [http://www.whatismyip.com/](http://www.whatismyip.com/) con la configuración del proxy habilitada y deshabilitada en Firefox. Cuando la configuración esté habilitada, la dirección IP será la de una máquina en el centro de datos de Microsoft Azure.

### Extensiones del explorador

A pesar de que la configuración del explorador para que use el túnel funciona, normalmente no desearía enrutar todo el tráfico a través del túnel. Las extensiones del explorador, como [FoxyProxy](http://getfoxyproxy.org/), son compatibles con la coincidencia de patrones para las solicitudes de dirección URL (FoxyProxy Standard o Plus solamente), de manera tal que solo las solicitudes para direcciones URL específicas se enviarán a través del túnel.

Si instaló FoxyProxy Standard, use los siguientes pasos para configurarlo para que solo desvíe tráfico para HDInsight a través del túnel.

1. Abra la extensión FoxyProxy en el explorador. Por ejemplo, en Firefox, seleccione el icono de FoxyProxy que se encuentra junto al campo de dirección.

	![icono de foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxy.png)

2. Seleccione **Agregar proxy nuevo**, la pestaña **General** y, a continuación, escriba un nombre de proxy de **HDInsightProxy**.

	![configuración general de foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxygeneral.png)

3. Seleccione la pestaña **Detalles de proxy** y rellene los campos siguientes:

	* **Host y dirección IP**: localhost, debido a que usamos un túnel SSH en la máquina local.

	* **Puerto**: el puerto que usó para el túnel SSH.

	* **Proxy SOCKS**: seleccione para permitir que el explorador use el túnel como proxy.

	* **SOCKS v5**: seleccione para definir la versión requerida del proxy.

	![proxy foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxyproxyproxy.png)

4. Seleccione la pestaña **Patrones de dirección URL** y, a continuación, seleccione **Agregar patrón nuevo**. Use lo siguiente para definir el patrón y, a continuación, haga clic en **Aceptar**:

	* **Nombre de patrón** - **zeppelinnotebook**: es solo un nombre descriptivo para el patrón.

	* **Patrón de URL** - **\*hn0\***: define un patrón que coincide con el nombre de dominio completo interno del punto de conexión donde se hospedan los cuadernos de Zeppelin. Como los cuadernos de Zeppelin solo están disponibles en el nodo principal headnode0 del clúster y el punto de conexión suele ser `http://hn0-<string>.internal.cloudapp.net`, utilizando el patrón **hn0**, garantizaría que la solicitud se redirige al punto de conexión de Zeppelin.

		![patrón de foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/foxypattern.png)

4. Seleccione **Aceptar** para agregar el proxy y cierre la **Configuración de proxy**.

5. En la parte superior del cuadro de diálogo FoxyProxy, cambie **Seleccionar modo** a **Uso de servidores proxy según sus patrones y prioridades predefinidos** y, a continuación, haga clic en **Cerrar**.

	![seleccionar modo de foxyproxy](./media/hdinsight-apache-spark-use-zeppelin-notebook/selectmode.png)

Después de seguir estos pasos, solo las solicitudes de direcciones URL que contienen la cadena __internal.cloudapp.net__ se enrutarán a través del túnel SSL.

## Acceso al cuaderno de Zeppelin

Después de haber configurado la tunelización SSH, puede usar los pasos siguientes para acceder al cuaderno de Zeppelin en el clúster Spark.

1. En el explorador web, abra el siguiente punto de conexión:

		http://hn0-myspar:9995

	* **hn0** denota headnode0 (nodo principal 0).
	* **myspar** son las seis primeras letras del nombre del clúster Spark.
	* **9995** es el puerto donde se puede acceder al cuaderno de Zeppelin.

2. Cree un nuevo notebook. En el panel de encabezado, haga clic en **Notebook** y luego en **Create New Note** (Crear nota).

	![Crear un nuevo cuaderno de Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.createnewnote.png "Crear un nuevo cuaderno de Zeppelin")

	En la misma página, en el encabezado **Notebook**, debería ver un nuevo cuaderno con un nombre que empiece por **Note XXXXXXXXX** (Nota XXXXXXXXX). Haga clic en el nuevo cuaderno.

3. En la página web del nuevo cuaderno, haga clic en el encabezado y cambie el nombre del cuaderno si quiere. Presione ENTRAR para guardar el cambio de nombre. Además, asegúrese de que el encabezado de cuaderno muestre el estado **Connected** (Conectado) en la esquina superior derecha.

	![Estado del cuaderno de Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.newnote.connected.png "Estado del cuaderno de Zeppelin")

4. Cargue los datos de ejemplo en una tabla temporal. Cuando crea un clúster Spark en HDInsight, el archivo de datos de ejemplo, **hvac.csv**, se copia en la cuenta de almacenamiento asociada en **\\HdiSamples\\SensorSampleData\\hvac**.

	En el párrafo vacío que se crea de manera predeterminada en el nuevo cuaderno, pegue el siguiente fragmento.

		// Create an RDD using the default Spark context, sc
		val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		// Define a schema
		case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
		
		// Map the values in the .csv file to the schema
		val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
    		s => Hvac(s(0), 
            		s(1),
            		s(2).toInt,
            		s(3).toInt,
            		s(6)
        	)
		).toDF()
		
		// Register as a temporary table called "hvac"
		hvac.registerTempTable("hvac")
		
	Presione **MAYÚS + ENTRAR** o haga clic en el botón **Reproducir** del párrafo para ejecutar el fragmento de código. El estado en la esquina derecha del párrafo debería avanzar de READY (Listo), PENDING (Pendiente) o RUNNING (En ejecución) a FINISHED (Finalizado). El resultado se muestra en la parte inferior del mismo párrafo. La captura de pantalla es similar a la siguiente:

	![Crear una tabla temporal de datos sin procesar](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.loaddataintotable.png "Crear una tabla temporal de datos sin procesar")

	También puede proporcionar un título para cada párrafo. En la esquina derecha, haga clic en el icono **Settings** (Configuración) y luego haga clic en **Show title** (Mostrar título).

5. Ahora puede ejecutar instrucciones Spark SQL en la tabla **hvac**. Pegue la siguiente consulta en un nuevo párrafo. La consulta recupera el identificador del edificio y la diferencia entre la temperatura objetivo y la real para cada edificio en una fecha determinada. Presione **MAYÚS + ENTRAR**.

		%sql
		select buildingID, (targettemp - actualtemp) as temp_diff, date 
		from hvac
		where date = "6/1/13" 

	La instrucción **%sql** al principio indica al cuaderno que use el intérprete Spark SQL. Puede consultar los intérpretes definidos en la pestaña **Interpreter** (Intérprete) en el encabezado del cuaderno.

	En la captura de pantalla siguiente se muestra el resultado.

	![Ejecutar una instrucción Spark SQL mediante el cuaderno](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery1.png "Ejecutar una instrucción Spark SQL mediante el cuaderno")

	 Haga clic en las opciones de presentación (resaltadas con el rectángulo) para alternar entre diferentes representaciones del mismo resultado. Haga clic en **Settings** (Configuración) para elegir qué constituye la clave y los valores de la salida. En la captura de pantalla anterior se usa **buildingID** como clave y la media de **temp\_diff** como valor.

	
6. También puede ejecutar instrucciones Spark SQL usando variables en la consulta. El siguiente fragmento de código muestra cómo definir una variable, **Temp**, en la consulta con los valores posibles con los que quiere hacer la consulta. Cuando ejecuta la consulta por primera vez, se rellena una lista desplegable automáticamente con los valores especificados para la variable.

		%sql
		select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff
		from hvac
		where targettemp > "${Temp = 65,65|75|85}" 

	Pegue este fragmento de código en un nuevo párrafo y presione **MAYÚS + ENTRAR**. En la captura de pantalla siguiente se muestra el resultado.

	![Ejecutar una instrucción Spark SQL mediante el cuaderno](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.note.sparksqlquery2.png "Ejecutar una instrucción Spark SQL mediante el cuaderno")

	Para las consultas posteriores, puede seleccionar un nuevo valor en la lista desplegable y después volver a ejecutar la consulta. Haga clic en **Settings** (Configuración) para elegir qué constituye la clave y los valores de la salida. La captura de pantalla anterior usa **buildingID** como clave, la media de **temp\_diff** como valor y **targettemp** como grupo.

7. Reinicie el intérprete Spark SQL para salir de la aplicación. Haga clic en la pestaña **Interpreter** (Intérprete) en la parte superior y, para el intérprete Spark, haga clic en **Restart** (Reiniciar).

	![Reiniciar el intérprete Zeppelin](./media/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql-v1/hdispark.zeppelin.restart.interpreter.png "Reinicie el intepretador Zeppelin")


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

* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Administración de recursos

* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->