<properties 
   pageTitle="Creación de un clúster de Hadoop en HDInsight con el Almacén de Azure Data Lake mediante el portal | Azure" 
   description="Use el Portal de Azure para crear y usar clústeres de Hadoop en HDInsight con el Almacén de Azure Data Lake." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/20/2016"
   ms.author="nitinme"/>

# Creación de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Using PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Aprenda a usar el Portal de Azure para crear un clúster de HDInsight (Hadoop, HBase o Storm) con acceso al Almacén de Azure Data Lake. Algunas consideraciones importantes sobre esta versión:

* **En clústeres de Hadoop (Windows y Linux)**, el Almacén de Data Lake solo puede usarse como cuenta de almacenamiento adicional. La cuenta de almacenamiento predeterminada para los clústeres de este tipo seguirán Blobs de almacenamiento de Azure (WASB).

* **En clústeres de Storm (Windows y Linux)**, el Almacén de Data Lake se puede utilizar para escribir datos de una topología de Storm. Almacén de Data Lake también puede utilizarse para almacenar datos de referencia que luego puede leer una topología de Storm.

* **En clústeres HBase (Windows y Linux)**, el almacén de Data Lake puede usarse como almacenamiento predeterminado o almacenamiento adicional.


En este artículo, aprovisionamos un clúster de Hadoop con el Almacén de Data Lake como almacenamiento adicional. La configuración de HDInsight para trabajar con el Almacén de Data Lake mediante el Portal de Azure consta de los pasos siguientes:

* Creación de un clúster de HDInsight con autenticación ante una entidad de servicio de Azure Active Directory
* Configuración del acceso al Almacén de Data Lake con la misma entidad de servicio
* Ejecución de un trabajo de prueba en el clúster

## Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite su suscripción de Azure** para la versión de vista previa pública del almacén de Data Lake. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup).


## Creación de un clúster de HDInsight con autenticación ante una entidad de servicio de Azure Active Directory

En esta sección, se crea un clúster de Hadoop en HDInsight que usa el Almacén de Data Lake como almacenamiento adicional. En esta versión, para un clúster de Hadoop, el Almacén de Data Lake solo sirve como almacenamiento adicional para el clúster. El almacenamiento predeterminado seguirá siendo los blobs de almacenamiento de Azure (WASB). En primer lugar, crearemos la cuenta de almacenamiento y los contenedores de almacenamiento necesarios para el clúster.

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).

2. Siga los pasos descritos en [Creación de clústeres de Hadoop en HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) para iniciar el aprovisionamiento de un clúster de HDInsight.
 
3. En la hoja **Configuración opcional**, haga clic en **Origen de datos**. En la hoja **Origen de datos**, especifique los detalles de la cuenta de almacenamiento y el contenedor de almacenamiento, especifique **Ubicación** como **Este de EE. UU. 2** y después haga clic en **Identidad de AAD de clúster**.

	![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Agregar entidad de servicio al clúster de HDInsight")

4. En la hoja **Identidad de AAD de clúster**, puede seleccionar una entidad de servicio existente o crear una nueva.
	
	* **Cree una nueva entidad de servicio**. En la hoja **Identidad de AAD de clúster**, haga clic en **Crear nuevo**. Haga clic en **Entidad de servicio** y después, en la hoja **Crear una entidad de servicio**, proporcione valores para crear una nueva entidad de servicio. En este paso, también se crean un certificado y una aplicación de Azure Active Directory. Haga clic en **Crear**.

		![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "Agregar entidad de servicio al clúster de HDInsight")

		En la hoja **Identidad de AAD de clúster**, haga clic en **Seleccionar** para continuar con la entidad de servicio que se va a crear.

		![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Agregar entidad de servicio al clúster de HDInsight")


	* **Elija una entidad de servicio existente**. En la hoja **Identidad de AAD de clúster**, haga clic en **Usar existente**. Haga clic en **Entidad de servicio** y después, en la hoja **Seleccionar una entidad de servicio**, busque una entidad de servicio existente. Haga clic en un nombre de entidad de servicio y después en **Seleccionar**.

		![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Agregar entidad de servicio al clúster de HDInsight")

		En la hoja **Identidad de AAD de clúster**, cargue el certificado (.pfx) que creó antes y proporcione la contraseña que usó para crear el certificado. Haga clic en **Seleccionar**. Así se completa la configuración de Azure Active Directory para el clúster de HDInsight.

		![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Agregar entidad de servicio al clúster de HDInsight")

6. Haga clic en **Seleccionar** en la hoja **Origen de datos** y continúe con el aprovisionamiento del clúster tal y como se describe en [Creación de clústeres de Hadoop en HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal).

7. Una vez aprovisionado el clúster, puede comprobar que la entidad de servicio esté asociada con el clúster de HDInsight. Para hacerlo, en la hoja del clúster, haga clic en **Configuración** y en **Identidad de AAD de clúster**; debería ver la entidad de servicio asociada.

	![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Agregar entidad de servicio al clúster de HDInsight")

## <a name="acl"></a>Configuración de la entidad de servicio para acceder al sistema de archivos del Almacén de Data Lake

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).

2. Si no tiene una cuenta de Almacén de Data Lake, créela. Siga las instrucciones que se describen en [Introducción al Almacén de Azure Data Lake mediante el Portal de Azure](data-lake-store-get-started-portal.md).

	Si ya tiene una cuenta de Almacén de Data Lake, en el panel izquierdo, haga clic en **Examinar**, en **Almacén de Data Lake** y después en el nombre de cuenta a la que desea conceder acceso.

	Realice las tareas siguientes en su cuenta de Almacén de Data Lake.

	* [Cree una carpeta en el Almacén de Data Lake](data-lake-store-get-started-portal.md#createfolder).
	* [Cargue un archivo a su Almacén de Data Lake](data-lake-store-get-started-portal.md#uploaddata). Si busca datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

	Usará los archivos cargados más adelante al probar la cuenta de Almacén de Data Lake con el clúster de HDInsight.

3. En la hoja del Almacén de Data Lake, haga clic en **Explorador de datos**.

	![Explorador de datos](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.start.data.explorer.png "Explorador de datos")

4. En la hoja **Explorador de datos**, haga clic en la raíz de su cuenta y después, en la hoja de la cuenta, haga clic en el icono **Acceso**.

	![Establecer las ACL en el sistema de archivos de Data Lake](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.1.png "Establecer las ACL en el sistema de archivos de Data Lake")

5. La hoja **Acceso** enumera el acceso estándar y el acceso personalizado ya asignados a la raíz. Haga clic en el icono **Agregar** para agregar las ACL de nivel personalizado e incluir la entidad de servicio que creó antes.

	![Mostrar acceso estándar y personalizado](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.2.png "Mostrar acceso estándar y personalizado")

6. Haga clic en el icono **Agregar** para abrir la hoja **Agregar acceso personalizado**. En esta hoja, haga clic en **Seleccionar usuario o grupo** y después, en la hoja **Seleccionar usuario o grupo**, busque la entidad de servicio que creó antes en Azure Active Directory. El nombre de la entidad de servicio creada antes es **HDIADL**. Haga clic en el nombre de la entidad de servicio y después en **Seleccionar**.

	![Agregar un grupo](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.3.png "Agregar un grupo")

7. Haga clic en **Seleccionar permisos**, seleccione los permisos que desea asignar a la entidad de servicio y después haga clic en **Aceptar**.

	![Asignar permisos al grupo](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.4.png "Asignar permisos al grupo")

8. En la hoja **Agregar acceso personalizado**, haga clic en **Aceptar**. El grupo recién agregado, con los permisos asociados, se mostrará ahora en la hoja **Acceso**.

	![Asignar permisos al grupo](./media/data-lake-store-hdinsight-hadoop-use-portal/adl.acl.5.png "Asignar permisos al grupo")

7. Si es necesario, también puede modificar los permisos de acceso después de agregar la entidad de servicio. Active o desactive la casilla para cada tipo de permiso (lectura, escritura, ejecución) en función de si desea quitarlo o asignarlo. Haga clic en **Guardar** para guardar los cambios o en **Descartar** para deshacerlos.



## Ejecución de trabajos de prueba en el clúster de HDInsight para usar el Almacén de Azure Data Lake

Después de configurar un clúster de HDInsight, puede ejecutar trabajos de prueba en el clúster para probar que el clúster de HDInsight puede acceder a datos en el Almacén de Azure Data Lake. Para hacerlo, vamos a ejecutar algunas consultas de Hive que tienen como destino el Almacén de Data Lake.

### En un clúster de Linux

1. Abra la hoja del clúster que acaba de aprovisionar y después haga clic en **Panel**. Se abre Ambari para el clúster de Linux. Al acceder a Ambari, se le pedirá autenticarse en el sitio. Escriba el nombre de cuenta y la contraseña del administrador (el administrador predeterminado) que usó al crear el clúster.

	![Inicie el panel del clúster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Inicie el panel del clúster")

	También puede navegar directamente a Ambari, para lo que debe dirigirse a https://CLUSTERNAME.azurehdinsight.net en un explorador web (donde **CLUSTERNAME** es el nombre del clúster de HDInsight).

2. Abra la vista de Hive. Seleccione el conjunto de cuadrados en el menú de la página (junto al vínculo **Admin** y el botón de la derecha de la página) para enumerar las vistas disponibles. Seleccione la vista de **Hive**.

	![Selección de vistas de Ambari](./media/data-lake-store-hdinsight-hadoop-use-portal/selecthiveview.png)

3. Debería ver una página similar a la siguiente:

	![Imagen de la página de vista de Hive, que contiene una sección del Editor de consultas](./media/data-lake-store-hdinsight-hadoop-use-portal/hiveview.png)

4. En la sección **Editor de consultas** de la página, pegue la siguiente instrucción de HiveQL en la hoja de cálculo:

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

5. Haga clic en el botón **Ejecutar** de la parte inferior del **Editor de consultas** para iniciar la consulta. Debe aparecer la sección **Resultados del proceso de consulta** debajo del **Editor de consultas**, en la que se muestra información acerca del trabajo.

6. Una vez que haya finalizado la consulta, la sección **Resultados del proceso de consulta** mostrará los resultados de la operación. La pestaña **Resultados** debe contener la información siguiente:

7. Ejecute la siguiente consulta para comprobar que se creó la tabla.

		SHOW TABLES;

	La pestaña **Resultados** debe mostrar lo siguiente:

		hivesampletable
		vehicles

	**vehicles** es la tabla que creó antes. La tabla de ejemplo **hivesampletable** está disponible en todos los clústeres de HDInsight de forma predeterminada.

8. También puede ejecutar una consulta para recuperar datos de la tabla **vehicles**.

		SELECT * FROM vehicles LIMIT 5;

### En un clúster de Windows

1. Abra la hoja del clúster que acaba de aprovisionar y después haga clic en **Panel**.

	![Inicie el panel del clúster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Inicie el panel del clúster")

	Cuando se le solicite, escriba las credenciales de administrador para el clúster.

2. Se abre la consola de consulta de HDInsight de Microsoft Azure. Haga clic en **Editor de Hive**.

	![Abrir el Editor de Hive](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster2.png "Abrir el Editor de Hive")

3. En el Editor de Hive, escriba la siguiente consulta y después haga clic en **Enviar**.

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

	En esta consulta de Hive, creamos una tabla con los datos almacenados en el almacén de Data Lake en `adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder`. Esta ubicación contiene un archivo de datos de ejemplo que debería haber cargado antes.

	La tabla **Sesión de trabajo** en la parte inferior muestra el estado del trabajo, que cambia de **Inicializando** a **Ejecutando** y después a **Completado**. También puede hacer clic en **Ver detalles** para obtener más información sobre el trabajo completado.

	![Crear tabla](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster3.png "Crear tabla")

4. Ejecute la siguiente consulta para comprobar que se creó la tabla.

		SHOW TABLES;

	Haga clic en la opción **Ver detalles** de esta consulta y la salida debe mostrar lo siguiente:

		hivesampletable
		vehicles

	**vehicles** es la tabla que creó antes. La tabla de ejemplo **hivesampletable** está disponible en todos los clústeres de HDInsight de forma predeterminada.

5. También puede ejecutar una consulta para recuperar datos de la tabla **vehicles**.

		SELECT * FROM vehicles LIMIT 5;


## Acceso al Almacén de Data Lake mediante comandos de HDFS

Una vez que configure el clúster de HDInsight para que use el Almacén de Data Lake, puede usar los comandos de shell de HDFS para acceder al almacén.

### En un clúster de Linux

En esta sección, se usará SSH en el clúster y se ejecutarán los comandos de HDFS. Windows no proporciona ningún cliente SSH integrado. Se recomienda usar **PuTTY**, que se puede descargar en [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para más información sobre el uso de PuTTY, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Una vez conectado, utilice el siguiente comando del sistema de archivos HDFS para enumerar los archivos del Almacén de Data Lake.

	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

Se debería incluir el archivo que cargó antes en el Almacén de Data Lake.

	15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
	Found 1 items
	-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

También puede usar el comando `hdfs dfs -put` para cargar algunos archivos en el almacén de Data Lake y después usar `hdfs dfs -ls` para comprobar si los archivos se cargaron correctamente.


### En un clúster de Windows

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).

2. Haga clic en **Examinar**, en **Clústeres de HDInsight** y después en el clúster de HDInsight que creó.

3. En la hoja del clúster, haga clic en **Escritorio remoto** y después, en la hoja **Escritorio remoto**, haga clic en **Conectar**.

	![Conexión remota con un clúster de HDI](./media/data-lake-store-hdinsight-hadoop-use-portal/ADL.HDI.PS.Remote.Desktop.png "Crear un grupo de recursos de Azure")

	Cuando se le solicite, escriba las credenciales que proporcionó para el usuario de Escritorio remoto.

4. En la sesión remota, inicie Windows PowerShell y use los comandos del sistema de archivos de HDFS para enumerar los archivos en el Almacén de Azure Data Lake.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	Se debería incluir el archivo que cargó antes en el Almacén de Data Lake.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder

	También puede usar el comando `hdfs dfs -put` para cargar algunos archivos en el almacén de Data Lake y después usar `hdfs dfs -ls` para comprobar si los archivos se cargaron correctamente.

## Consideraciones para aprovisionar un clúster de HBase que use el Almacén de Data Lake como almacenamiento predeterminado

Para los clústeres de HBase, puede usar cuentas de Almacén de Data Lake como almacenamiento predeterminado. Si decide hacerlo, para aprovisionar un clúster correctamente, la entidad de servicio asociada con el clúster **debe** tener acceso a la cuenta del almacén de Data Lake. Puede asegurarse de esto de dos maneras:

* **Si usa una entidad de servicio existente**, debe comprobar que la entidad de servicio se agregue a la ACL en el nivel raíz del sistema de archivos del almacén de Data Lake antes de iniciar el aprovisionamiento del clúster.
* **Si opta por crear una nueva entidad de servicio** como parte del aprovisionamiento del clúster, en cuanto se inicie dicho proceso, debe agregar la entidad de servicio recién creada al nivel raíz del sistema de archivos del almacén de Data Lake. Si no lo hace, el clúster se aprovisionará, pero los servicios de HBase no se podrán iniciar. Para solucionar este problema, deberá agregar la entidad de servicio a la ACL de la cuenta de Almacén de Data Lake y después reiniciar los servicios de HBase mediante la interfaz de usuario de Web de Ambari.

Para obtener instrucciones sobre cómo agregar una entidad de servicio a un sistema de archivos del almacén de Data Lake, consulte [Configuración de la entidad de servicio para acceder al sistema de archivos del almacén de Data Lake](#acl).

## Uso del Almacén de Data Lake en una topología de Storm

El Almacén de Data Lake se puede usar para escribir datos de una topología de Storm. Para obtener instrucciones sobre cómo lograr este escenario, consulte [Use Azure Data Lake Store with Apache Storm with HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md) (Uso del Almacén de Azure Data Lake con Apache Storm con HDInsight).

## Consulte también

* [Aprovisionamiento de un clúster de HDInsight con el Almacén de Data Lake mediante Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0121_2016-->