<properties
	pageTitle="Empezar a trabajar con un emulador de Hadoop para HDInsight | Microsoft Azure"
	description="Use un emulador instalado con un tutorial de MapReduce y otros ejemplos para aprender el ecosistema de Hadoop. El emulador de HDInsight funciona como un espacio aislado de Hadoop."
	keywords="emulator,hadoop ecosystem,hadoop sandbox,mapreduce tutorial"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	authors="nitinme"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="11/29/2015"
	ms.author="nitinme"/>

# Introducción al ecosistema de Hadoop con el emulador de HDInsight, un espacio aislado de Hadoop

Este tutorial es una introducción a los clústeres de Hadoop en el emulador de Microsoft HDInsight para Azure (anteriormente HDInsight Server Developer Preview). El emulador de HDInsight viene con los mismos componentes del ecosistema Hadoop como HDInsight de Azure. Para conocer los detalles, incluida la información sobre las versiones implementadas, consulte [¿Qué versión de Hadoop tiene HDInsight de Azure?](hdinsight-component-versioning.md)

Cuando se instale el emulador, siga un tutorial de MapReduce para el recuento de palabras y, a continuación, ejecute ejemplos.

> [AZURE.NOTE] El emulador de HDInsight solo incluye un clúster de Hadoop. No incluye HBase ni Storm.


El emulador de HDInsight proporciona un entorno de desarrollo local similar a un espacio aislado de Hadoop. Si está familiarizado con Hadoop, puede empezar con el emulador de HDInsight usando el sistema de archivos distribuidos de Hadoop (HDFS). En HDInsight, el sistema de archivos predeterminado es el almacenamiento de blobs de Azure. Por lo que, finalmente, querrá desarrollar sus trabajos con el almacenamiento de blobs de Azure. Para utilizar el almacenamiento de blobs de Azure con el emulador de HDInsight, debe realizar cambios en la configuración del emulador.

> [AZURE.NOTE] El emulador de HDInsight solo puede usar una implementación de un nodo.


## Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

- El emulador de HDInsight requiere una versión de Windows de 64 bits. Se debe cumplir uno de los siguientes requisitos:

	- Windows 10
	- Windows 8
	- Windows Server 2012

- **Azure PowerShell**. Vea [Instalar y usar Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).


##<a name="install"></a>Instalación del emulador de HDInsight

El emulador de Microsoft HDInsight se puede instalar a través del instalador de plataforma web de Microsoft.

> [AZURE.NOTE] El emulador de HDInsight solo es compatible actualmente con los sistemas operativos en inglés. Si tiene instalada una versión anterior del emulador, debe desinstalar los dos componentes siguientes desde Panel de control/Programas y características antes de instalar la última versión del emulador:
><ul>
> <li>Emulador de Microsoft HDInsight para Azure o HDInsight Developer Preview, el que esté instalado.</li>
> <li>Hortonworks Data Platform</li>
> </ul>


**Para instalar el emulador de HDInsight**

1. Abra Internet Explorer y diríjase a la [página de instalación del emulador de Microsoft HDInsight para Azure][hdinsight-emulator-install].
2. Haga clic en **Instalar ahora**.
3. Haga clic en **Ejecutar** cuando se le pregunte por la instalación de HDINSIGHT.exe en la parte inferior de la página.
4. Haga clic en el botón **Sí** en la ventana **Control de cuentas de usuario** que aparece para completar la instalación. Se muestra la ventana de Instalador de plataforma web.
6. Haga clic en **Instalar** en la parte inferior de la página.
7. Haga clic en **Acepto** para aceptar los términos de la licencia.
8. El Instalador de plataforma web debe indicar **Los siguientes productos se instalaron correctamente**. A continuación, haga clic en **Finalizar**.
9. Haga clic en **Salir** para cerrar la ventana de Instalador de plataforma web.

**Para comprobar la instalación del emulador de HDInsight**

La instalación debería haber instalado tres iconos en el escritorio. Los tres iconos están vinculados como se muestra a continuación:

- **Línea de comandos de Hadoop**: el símbolo del sistema de Hadoop desde el que se ejecutan trabajos de MapReduce, Pig y Hive en el emulador de HDInsight.

- **Estado de NameNode de Hadoop**: el NameNode mantiene el directorio de árbol para todos los archivos en HDFS. Realiza también un seguimiento de dónde se mantienen los datos de todos los archivos en un clúster de Hadoop. Los clientes se comunican con el NameNode para resolver los nodos de datos de todos los archivos que están almacenados.

- **Estado de Yarn de Hadoop**: el seguimiento de trabajo que asigna tareas de MapReduce a los nodos en un clúster.

La instalación debería haber instalado también varios servicios locales. La siguiente es una captura de pantalla de la ventana de servicios:

![Servicios del ecosistema de Hadoop mostrados en la ventana del emulador.][image-hdi-emulator-services]

De manera predeterminada, los servicios relacionados con el emulador de HDInsight no se inician. Para iniciar los servicios, desde la línea de comandos de Hadoop, ejecute **start\_local\_hdp\_services.cmd** en C:\\hdp (ubicación predeterminada). Para iniciar automáticamente los servicios después de reiniciar el equipo, ejecute **set-onebox-autostart.cmd**.

Para obtener información acerca de los problemas conocidos con la instalación y ejecución del emulador de HDInsight, consulte las [Notas de la versión del emulador de HDInsight](hdinsight-emulator-release-notes.md). El registro de instalación se encuentra en **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**.

##<a name="vstools"></a>Uso del emulador con las herramientas de HDInsight para Visual Studio

Puede utilizar herramientas de HDInsight para Visual Studio a fin de conectar con el emulador de HDInsight. Para obtener información sobre cómo utilizar las herramientas de Visual Studio con los clústeres de HDInsight en Azure, consulte [Introducción al uso de las herramientas de Hadoop de HDInsight para Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

### Instalación de las herramientas de HDInsight para el emulador

Para obtener instrucciones sobre cómo instalar las herramientas de Visual Studio de HDInsight, consulte [esta información](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md#installation).

### Conexión con el emulador de HDInsight

1. Abra Visual Studio.
2. En el menú **Ver**, haga clic en **Explorador de servidores** para abrir la ventana Explorador de servidores.
3. Expanda **Azure**, haga clic con el botón secundario en **HDInsight**, y, a continuación, haga clic en **Conectar con el emulador de HDInsight**.

	 ![Vista de Visual Studio: conectarse con el emulador de HDInsight en el menú.](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.connect.vs.png)

4. En el cuadro de diálogo Conectar con el emulador de HDInsight, compruebe los valores para los extremos de WebHCat, HiveServer2 y WebHDFS y, a continuación, haga clic en **Siguiente**. Los valores que se rellenan de forma predeterminada deben funcionar si no ha realizado ningún cambio en la configuración predeterminada del emulador. Si ha realizado algún cambio, actualice los valores en el cuadro de diálogo y, a continuación, haga clic en Siguiente.

	![Cuadro de diálogo para conectarse con el emulador de HDInsight con la configuración.](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.connect.vs.dialog.png)

5. Una vez que la conexión se establezca correctamente, haga clic en **Finalizar**. El emulador de HDInsight debería aparecer ahora en el Explorador de servidores.

	![Explorador de servidores en el que se muestra un emulador local de HDInsight (un espacio aislado de Hadoop) conectado.](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.vs.connected.png)

Una vez que la conexión esté correctamente establecida, puede utilizar las herramientas de VS de HDInsight con el emulador, tal como se usarían con un clúster de HDInsight de Azure. Para obtener instrucciones sobre cómo utilizar las herramientas de VS con clústeres de HDInsight de Azure, consulte [Introducción al uso de las herramientas de Hadoop de HDInsight para Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

## Solución de problemas: conexión de herramientas de HDInsight con el emulador de HDInsight

1. Al conectar con el emulador de HDInsight, incluso aunque el cuadro de diálogo muestra que HiveServer2 se ha conectado correctamente, hay que configurar manualmente la propiedad **hive.security.authorization.enabled** como **false** en el archivo de configuración de Hive, en C:\\hdp\\hive-*versión*\\conf\\hive-site.xml. A continuación, reinicie el emulador local. Las herramientas de HDInsight para Visual Studio se conectan a HiveServer2 solo cuando se muestra una vista previa de las 100 primeras filas de la tabla. Si no desea usar este tipo de consulta, puede dejar la configuración de hive como está.

2. Si usa la asignación de IP dinámica (DHCP) en el equipo que ejecuta el emulador de HDInsight, necesitará actualizar C:\\hdp\\hadoop-*versión*\\etc\\hadoop\\core-site.xml y cambiar el valor de la propiedad **hadoop.proxyuser.hadoop.hosts** a (*). Esto permite a los usuarios de Hadoop conectarse desde todos los hosts para suplantar al usuario que ha escrito en Visual Studio.

		<property>
			<name>hadoop.proxyuser.hadoop.hosts</name>
			<value>*</value>
		</property>

3. Puede aparecer un error cuando Visual Studio intenta conectarse al servicio de WebHCat ("error": "No se pudo encontrar el trabajo job\_XXXX\_0001"). En este caso, debe reiniciar el servicio de WebHCat e intentarlo de nuevo. Para reiniciar el servicio WebHCat, inicie los **Servicios** MMC, haga clic con el botón secundario en **Apache Hadoop Templeton** (es el nombre antiguo para servicio de WebHCat) y haga clic en **Reiniciar**.

##<a name="runwordcount"></a>Tutorial de MapReduce de recuento de palabras

Ahora que ha configurado el Emulador de HDInsight en su estación de trabajo, pruebe este tutorial de MapReduce para probar la instalación. Primero, cargará algunos archivos de datos en HDFS y, posteriormente, ejecutará un trabajo de recuento de palabras de MapReduce para contar la frecuencia de palabras específicas de esos archivos.

El programa de recuento de palabras de MapReduce se empaquetó en *hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar*. El archivo jar se encuentra en la carpeta *C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\hadoop\\mapreduce*.

El trabajo de MapReduce para contar palabras adopta dos argumentos:

- Una carpeta de entrada. Usará **hdfs://localhost/user/HDIUser* como la carpeta de entrada.
- Una carpeta de salida. Usará **hdfs://localhost/user/HDIUser/WordCount_Output* como la carpeta de salida. La carpeta de salida no puede estar en una carpeta existente; de lo contrario, el trabajo de MapReduce producirá un error. Si desea ejecutar el trabajo de MapReduce por segunda vez, debe especificar una carpeta de salida diferente o eliminar la carpeta de salida existente.

**Para ejecutar el trabajo de MapReduce de recuento de palabras**

1. En el escritorio, haga doble clic en la **línea de comandos de Hadoop** para abrir la ventana de línea de comandos de Hadoop. La carpeta actual debe ser:

		c:\hdp\hadoop-2.4.0.2.1.3.0-1981

	De lo contrario, ejecute el siguiente comando:

		cd %hadoop_home%

2. Ejecute los siguientes comandos de Hadoop para crear una carpeta de HDFS para el almacenamiento de los archivos de entrada y salida:

		hadoop fs -mkdir /user
		hadoop fs -mkdir /user/HDIUser

3. Ejecute el siguiente comando de Hadoop para copiar algunos archivos de texto locales en HDFS:

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common*.txt /user/HDIUser

4. Ejecute el siguiente comando para ver en una lista de los archivos de la carpeta /user/HDIUser:

		hadoop fs -ls /user/HDIUser

	Debería ver los siguientes archivos:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -ls /user/HDIUser
		Found 4 items
		-rw-r--r--   1 username hdfs     574261 2014-09-08 12:56 /user/HDIUser/CHANGES.txt
		-rw-r--r--   1 username hdfs      15748 2014-09-08 12:56 /user/HDIUser/LICENSE.txt
		-rw-r--r--   1 username hdfs        103 2014-09-08 12:56 /user/HDIUser/NOTICE.txt
		-rw-r--r--   1 username hdfs       1397 2014-09-08 12:56 /user/HDIUser/README.txt

5. Ejecute el siguiente comando para ejecutar el trabajo de MapReduce de recuento de palabras:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop jar C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6. Ejecute el siguiente comando para mostrar en una lista el número de palabras que incluyen "ventanas" desde el archivo de salida:

		hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

	La salida debe ser:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
		windows 4
		windows.        2
		windows/cygwin. 1

Para obtener más información sobre los comandos de Hadoop, consulte el [manual de comandos de Hadoop][hadoop-commands-manual].

##<a name="rungetstartedsamples"></a> Analizar los datos de registro web de ejemplo

La instalación del emulador de HDInsight ofrece algunas muestras para que los usuarios se inicien el aprendizaje de los servicios basados en Hadoop de Apache en Windows. Estas muestras incluyen algunas tareas que suelen ser necesarias cuando se procesa un conjunto de datos grande. Basándonos en el tutorial MapReduce anterior, los ejemplos le ayudarán a familiarizarse más con el modelo de programación de MapReduce y su ecosistema.

Los datos de muestra se organizan en torno al procesamiento de datos de registro de IIS World Wide Web Consortium (W3C). Se proporciona una herramienta de generación de datos para crear e importar los conjuntos de datos de diferentes tamaños a HDFS o al almacenamiento de blobs de Azure. (Consulte [Uso del almacenamiento de blobs de Azure para HDInsight](hdinsight-hadoop-use-blob-storage.md) para obtener más información). Luego, los trabajos de MapReduce, Pig o Hive se pueden ejecutar en las páginas de datos generadas por el script de Azure PowerShell. Los scripts de Pig y Hive constituyen una capa de abstracción sobre MapReduce que, al final, se compilan en programas de MapReduce. Puede ejecutar una serie de trabajos para observar los efectos de usar estas distintas tecnologías y la forma en que el tamaño de los datos afecta a la ejecución de las tareas de procesamiento.

### En esta sección

- [Los escenarios de datos del registro de IIS W3C](#scenarios)
- [Carga de los datos de muestra del registro de W3C](#loaddata)
- [Ejecución de trabajos de MapReduce de Java](#javamapreduce)
- [Ejecución de un trabajo de Hive](#hive)
- [Ejecución de trabajos de Pig](#pig)
- [Compilación de muestras](#rebuild)

###<a name="scenarios"></a>Los escenarios de datos del registro de IIS W3C

El escenario de W3C genera e importa datos de registro de IIS W3C en tres tamaños a HDFS o al almacenamiento de blobs de Azure: 1 MB (pequeño), 500 MB (mediano) y 2 GB (grande). Proporciona tres tipos de trabajos y los implementa en C#, Java, Pig y Hive.

- **totalhits**: calcula la cantidad total de solicitudes de una página determinada.
- **avgtime**: calcula el tiempo promedio utilizado (en segundos) para una solicitud por página.
- **errors**: calcula la cantidad de errores por página y por hora para las solicitudes cuyo estado era 404 o 500.

Estos ejemplos y su documentación no proporcionan un estudio en profundidad ni una implementación completa de las tecnologías clave de Hadoop. El clúster usado tiene un solo nodo y, por lo tanto, no se puede observar el efecto de agregar más nodos en esta versión.

###<a name="loaddata"></a>Carga de los datos de muestra del registro de W3C

La generación e importación de los datos a HDFS se hace usando el script importdata.ps1 de Azure PowerShell.

**Para importar los datos de muestra del registro de W3C**

1. Abra la línea de comandos de Hadoop desde el escritorio.
2. Cambie el directorio a **C:\\hdp\\GettingStarted**.
3. Ejecute el siguiente comando para generar e importar datos a HDFS:

		powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted

	Si, en cambio, desea cargar datos en el almacenamiento de blobs de Azure, consulte [Conexión al almacenamiento de blobs de Azure](#blobstorage).

4. Ejecute el siguiente comando desde la línea de comandos de Hadoop para mostrar los archivos importados en HDFS:

		hadoop fs -ls -R /w3c

	La salida debe ser similar a la siguiente:

		C:\hdp\GettingStarted>hadoop fs -ls -R /w3c
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:41 /w3c/input/large
		-rw-r--r--   1 username hdfs  543683503 2014-09-08 15:41 /w3c/input/large/data_w3c_large.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input/medium
		-rw-r--r--   1 username hdfs  272435159 2014-09-08 15:40 /w3c/input/medium/data_w3c_medium.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:39 /w3c/input/small
		-rw-r--r--   1 username hdfs    1058423 2014-09-08 15:39 /w3c/input/small/data_w3c_small.txt

5. Si desea comprobar el contenido del archivo, ejecute el siguiente comando para mostrar uno de los archivos de datos en la ventana de la consola:

		hadoop fs -cat /w3c/input/small/data_w3c_small.txt

Ahora, los archivos de datos se han creado e importado en HDFS. Puede empezar a ejecutar diferentes trabajos de Hadoop.

###<a name="javamapreduce"></a> Ejecución de trabajos de MapReduce de Java

MapReduce es el motor de cálculo básico de Hadoop. De manera predeterminada, está implementado en Java, pero también hay ejemplos que aprovechan el streaming de .NET y Hadoop que usan C#. La sintaxis para ejecutar un trabajo de MapReduce es:

	hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

El archivo jar y los archivos de origen se encuentran en la carpeta C:\\Hadoop\\GettingStarted\\Java.

**Para ejecutar un trabajo de MapReduce para calcular las visitas a la página web**

1. Abra la línea de comandos de Hadoop.
2. Cambie el directorio a **C:\\hdp\\GettingStarted**.
3. Ejecute el siguiente comando para quitar el directorio de salida en caso de que exista la carpeta. El trabajo de MapReduce producirá un error si la carpeta de salida ya existe.

		hadoop fs -rm -r /w3c/output

3. Ejecute el siguiente comando:

		hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

	En la siguiente tabla se describen los elementos del comando:
	<table border="1">
	<tr><td>Parámetro</td><td>Nota:</td></tr>
	<tr><td>w3c_scenarios.jar</td><td>El archivo jar se encuentra en la carpeta C:\hdp\GettingStarted\Java.</td></tr>
	<tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>El tipo se puede sustituir por una de las siguientes opciones:
	<ul>
	<li>microsoft.hadoop.w3c.AverageTimeTaken</li>
	<li>microsoft.hadoop.w3c.ErrorsByPage</li>
	</ul></td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td>El archivo de entrada se puede sustituir por lo siguiente:
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul></td></tr>
	<tr><td>/w3c/output</td><td>Este es el nombre de la carpeta de salida.</td></tr>
	</table>

4. Ejecute el siguiente comando para ver el archivo de salida:

		hadoop fs -cat /w3c/output/part-00000

	La salida debe ser similar a:

		c:\Hadoop\GettingStarted>hadoop fs -cat /w3c/output/part-00000
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126

	La página Default.aspx recibe 3360 visitas y así sucesivamente. Intente ejecutar de nuevo los comandos reemplazando los valores tal como se sugiere en la tabla anterior y observe cómo cambia el resultado en función del tipo de trabajo y el tamaño de los datos.

### <a name="hive"></a>Ejecución de trabajos de Hive
A los analistas con profundos conocimientos de lenguaje de consulta estructurado (SQL), el motor de consulta de Hive les resultará familiar. Proporciona una interfaz similar a SQL y un modelo de datos relacionales para HDFS. Hive usa un lenguaje denominado HiveQL, muy similar a SQL. Hive proporciona una capa de abstracción sobre el marco de MapReduce basado en Java, y las consultas de Hive se compilan en MapReduce en tiempo de ejecución.

**Para ejecutar un trabajo de Hive**

1. Abra una línea de comandos de Hadoop.
2. Cambie el directorio a **C:\\hdp\\GettingStarted**.
3. Ejecute el siguiente comando para eliminar la carpeta **/w3c/hive/input**, en caso de que exista. El trabajo de Hive producirá un error si la carpeta existe.

		hadoop fs -rmr /w3c/hive/input

4. Ejecute el siguiente comando para crear las carpetas **/w3c/hive/input** y copie los archivos de datos en la carpeta /hive/input:

        hadoop fs -mkdir /w3c/hive
		hadoop fs -mkdir /w3c/hive/input

		hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5. Ejecute el siguiente comando para ejecutar el archivo de script **w3ccreate.hql**. El script crea una tabla de Hive y carga datos en la tabla de Hive:

	> [AZURE.NOTE] En esta fase, también puede utilizar las herramientas de HDInsight para Visual Studio a fin de ejecutar la consulta de Hive. Abra Visual Studio, cree un nuevo proyecto y, en la plantilla de HDInsight, elija **Aplicación de Hive**. Una vez que se abra el proyecto, agregue la consulta como un nuevo elemento. La consulta está disponible en **C:/hdp/GettingStarted/Hive/w3c**. Una vez que la consulta se agregue al proyecto, reemplace **${hiveconf:input}** por **/w3c/hive/input** y, posteriormente, presione **Enviar**.

		C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

	La salida debe ser similar a la siguiente:

		Logging initialized using configuration in file:/C:/hdp/hive-0.13.0.2.1.3.0-1981	/conf/hive-log4j.properties
		OK
		Time taken: 1.137 seconds
		OK
		Time taken: 4.403 seconds
		Loading data to table default.w3c
		Moved: 'hdfs://HDINSIGHT02:8020/hive/warehouse/w3c' to trash at: hdfs://HDINSIGHT02:8020/user/<username>/.Trash/Current
		Table default.w3c stats: [numFiles=1, numRows=0, totalSize=1058423, rawDataSize=0]
		OK
		Time taken: 2.881 seconds

6. Ejecute el siguiente comando para ejecutar el archivo de script de HiveQL **w3ctotalhitsbypage.hql**.

	> [AZURE.NOTE] Como se explicó anteriormente, también puede ejecutar esta consulta mediante las herramientas de HDInsight para Visual Studio.

        C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

	En la siguiente tabla se describen los elementos del comando:
	<table border="1">
	<tr><td>Archivo</td><td>Descripción</td></tr>
	<tr><td>C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd</td><td>El script de comando de Hive.</td></tr>
	<tr><td>C:\hdp\GettingStarted\Hive\w3c\w3ctotalhitsbypage.hql</td><td> Puede sustituir el archivo de script de Hive por una de las siguientes opciones:
	<ul>
	<li>C:\hdp\GettingStarted\Hive\w3c\w3caveragetimetaken.hql</li>
	<li>C:\hdp\GettingStarted\Hive\w3c\w3cerrorsbypage.hql</li>
	</ul>
	</td></tr>
	
	</table>

	El script de HiveQL w3ctotalhitsbypage.hql es:

		SELECT filtered.cs_uri_stem,COUNT(*)
		FROM (
		  SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
		) filtered
		GROUP BY (filtered.cs_uri_stem);

	El final de la salida debe ser similar a la siguiente:

		MapReduce Total cumulative CPU time: 5 seconds 391 msec
		Ended Job = job_1410201800143_0008
		MapReduce Jobs Launched:
		Job 0: Map: 1  Reduce: 1   Cumulative CPU: 5.391 sec   HDFS Read: 1058638 HDFS Write: 53 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 391 msec
		OK
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126
		Time taken: 49.304 seconds, Fetched: 3 row(s)

Tenga en cuenta que como primer paso en cada uno de los trabajos, se creará una tabla y los datos se cargarán en la tabla desde el archivo que se creó anteriormente. Puede buscar el archivo que se creó en el nodo /Hive en HDFS con el siguiente comando:

	hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Ejecución de trabajos de Pig

El procesamiento de Pig usa un lenguaje de flujo de datos llamado *Pig Latin*. Las abstracciones de Pig Latin proporcionan estructuras de datos más detalladas que las de MapReduce y hacen en Hadoop lo que SQL hace en los sistemas de administración de bases de datos relacionales.


**Para ejecutar trabajos de pig**

1. Abra una línea de comandos de Hadoop.
2. Cambie el directorio a la carpeta **C:\\hdp\\GettingStarted**.
3. Ejecute el siguiente comando para enviar un trabajo de Pig:

		C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

	En la siguiente tabla se muestran los elementos del comando:
	<table border="1">
	<tr><td>Archivo</td><td>Descripción</td></tr>
	<tr><td>C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd</td><td>El script de comando de Pig.</td></tr>
	<tr><td>C:\hdp\GettingStarted\Pig\w3c\TotalHitsForPage.pig</td><td> Puede sustituir el archivo de script de Pig Latin por una de las siguientes opciones:
	<ul>
	<li>C:\hdp\GettingStarted\Pig\w3c\AverageTimeTaken.pig</li>
	<li>C:\hdp\GettingStarted\Pig\w3c\ErrorsByPage.pig</li>
	</ul>
	</td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td> Puede sustituir el parámetro por un archivo más grande:
	
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul>
	
	</td></tr>
	</table>

	La salida debe ser similar a la siguiente:

		(/Info.aspx,1135)
		(/UserService,1126)
		(/Default.aspx,3380)

Tenga en cuenta que debido a que los scripts de Pig se compilan en trabajos de MapReduce y posiblemente en más de uno de tales trabajos, puede que se vean varios trabajos de MapReduce que se ejecuten durante el procesamiento de un trabajo de Pig.

<!---
### <a name="rebuild"></a>Rebuild the samples
The samples currently contain all the required binaries, so building is not required. If you'd like to make changes to the Java or .NET samples, you can rebuild them by using either the Microsoft Build Engine (MSBuild) or the included Azure PowerShell script.


**To rebuild the samples**

1. Open a Hadoop command line.
2. Run the following command:

		powershell -F buildsamples.ps1
--->

##<a name="blobstorage"></a>Conexión con el almacenamiento de blobs de Azure
El emulador de HDInsight utiliza HDFS como sistema de archivos predeterminado. Sin embargo, HDInsight de Azure usa el almacenamiento de blobs de Azure como sistema de archivos predeterminado. Se puede configurar el emulador de HDInsight para que use el almacenamiento de blobs de Azure en lugar del almacenamiento local. Siga las instrucciones que se ofrecen a continuación para crear un contenedor de almacenamiento en Azure y conectarlo al emulador de HDInsight.

>[AZURE.NOTE] Para obtener más información sobre cómo usa HDInsight el almacenamiento de blobs de Azure, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).

Antes de comenzar con las instrucciones dadas a continuación, debe haber creado una cuenta de almacenamiento. Para obtener instrucciones, consulte [Creación de una cuenta de almacenamiento](../storage/storage-create-storage-account.md).

**Para crear un contenedor**

1. Inicie sesión en el [Portal de Azure](https://ms.portal.azure.com/).
2. Haga clic en **NUEVO** a la izquierda, en **Datos y almacenamiento** y luego en **Almacenamiento**.
3. En la hoja de cuenta de almacenamiento, configure las propiedades como se muestran en la siguiente captura de pantalla.
	
	![Crear una cuenta de almacenamiento](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.create.storage.png)

	Seleccione **Anclar a Panel de inicio** y haga clic en **Crear**.
4. Cuando se cree la cuenta de almacenamiento, en la nueva hoja de cuenta de almacenamiento, haga clic en **Contenedores** para abrir la hoja de contenedores y luego en **Agregar**.
5. Escriba el nombre del contenedor y después haga clic en **Seleccionar**.

	![Crear un contenedor](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.create.container.png)

Para que pueda tener acceso a una cuenta de almacenamiento de Azure, debe agregar el nombre y la clave de la cuenta al archivo de configuración.

**Para configurar la conexión a una cuenta de almacenamiento de Azure**

1. Abra **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\etc\\hadoop\\core-site.xml** en el Bloc de notas.
2. Agregue la siguiente etiqueta <property> junto a las otras etiquetas <property>:

		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

	Debe sustituir <StorageAccountName> y <StorageAccountKey> por los valores que coinciden con la información de su cuenta de almacenamiento.

3. Guarde el cambio. No es necesario reiniciar los servicios de Hadoop.

Use la siguiente sintaxis para acceder a la cuenta de almacenamiento:

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Por ejemplo:

	hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/


##<a name="powershell"></a> Ejecución de Azure PowerShell
Algunos de los cmdlets de Azure PowerShell también son compatibles con el emulador de HDInsight. Estos cmdlets incluyen:

- Cmdlets de definición del trabajo de HDInsight

	- New-AzureHDInsightSqoopJobDefinition
	- New-AzureHDInsightStreamingMapReduceJobDefinition
	- New-AzureHDInsightPigJobDefinition
	- New-AzureHDInsightHiveJobDefinition
	- New-AzureHDInsightMapReduceJobDefinition
- Start-AzureHDInsightJob
- Get-AzureHDInsightJob
- Wait-AzureHDInsightJob

La siguiente es un ejemplo para enviar un trabajo de Hadoop:

	$creds = Get-Credential (hadoop as username, password can be anything)
	$hdinsightJob = <JobDefinition>
	Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Recibirá una indicación cuando llame a Get-Credential. Debe usar **hadoop** como nombre de usuario. La contraseña puede ser cualquier cadena. El nombre del clúster siempre es ****http://localhost:50111**.

Para obtener más información sobre el envío de trabajos de Hadoop, consulte [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md). Para obtener más información sobre los cmdlets de Azure PowerShell, consulte [Documentación de referencia de los cmdlets de HDInsight][hdinsight-powershell-reference].


##<a name="remove"></a> Eliminación del emulador de HDInsight
En el equipo en el que tiene el emulador instalado, abra el Panel de Control y, en **Programas**, haga clic en **Desinstalar un programa**. En la lista de programas instalados, haga clic con el botón secundario en **Emulador de Microsoft HDInsight para Azure** y, a continuación, haga clic en **Desinstalar**.


##<a name="nextsteps"></a> Pasos siguientes
En este tutorial de MapReduce, ha instalado un Emulador de HDInsight (un espacio aislado de Hadoop) y ha ejecutado algunos trabajos de Hadoop. Para obtener más información, consulte los artículos siguientes:

- [Introducción al uso de HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Desarrollo de programas de MapReduce de Java para HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
- [Desarrollo de programas de MapReduce de streaming de Hadoop C# para HDInsight](hdinsight-hadoop-develop-deploy-streaming-jobs.md)
- [Notas de la versión del emulador de HDInsight](hdinsight-emulator-release-notes.md)
- [Foro de MSDN para el análisis de HDInsight](http://social.msdn.microsoft.com/Forums/hdinsight)



[azure-sdk]: http://azure.microsoft.com/downloads/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-management-portal]: https://manage.windowsazure.com/
[netstat-url]: http://technet.microsoft.com/library/ff961504.aspx

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[hdinsight-emulator-install]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
[hdinsight-emulator-release-notes]: hdinsight-emulator-release-notes.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-deploy-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-versions]: hdinsight-component-versioning.md

[Powershell-install-configure]: powershell-install-configure.md

[hadoop-commands-manual]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html

[image-hdi-emulator-services]: ./media/hdinsight-hadoop-emulator-get-started/HDI.Emulator.Services.png
 

<!---HONumber=AcomDC_0302_2016-->