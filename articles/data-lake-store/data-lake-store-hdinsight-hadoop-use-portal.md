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
   ms.date="02/03/2016"
   ms.author="nitinme"/>

# Creación de un clúster de HDInsight con el Almacén de Data Lake mediante el Portal de Azure

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Using PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Aprenda a usar el Portal de Azure para crear un clúster de HDInsight (Hadoop, HBase o Storm) con acceso al Almacén de Azure Data Lake. Algunas consideraciones importantes sobre esta versión:

* **En clústeres de Hadoop (Windows y Linux)**, el Almacén de Data Lake solo puede usarse como cuenta de almacenamiento adicional. La cuenta de almacenamiento predeterminada para los clústeres de este tipo seguirán Blobs de almacenamiento de Azure (WASB).

* **En clústeres de Storm (Windows y Linux)**, el Almacén de Data Lake se puede utilizar para escribir datos de una topología de Storm. Almacén de Data Lake también puede utilizarse para almacenar datos de referencia que luego puede leer una topología de Storm.

* **En clústeres HBase (Windows y Linux)**, el almacén de Data Lake puede usarse como almacenamiento predeterminado o almacenamiento adicional. La opción de crear clústeres de HBase con acceso al Almacén de Data Lake solo está disponible si usa las versiones 3.1 o 3.2 de HDI (para Windows) o la versión 3.2 de HDI (para Linux).


## Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite su suscripción de Azure** para la versión de vista previa pública del almacén de Data Lake. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup).
- **Una cuenta de Almacén de Azure Data Lake**. Siga las instrucciones que se describen en [Introducción al Almacén de Azure Data Lake mediante el Portal de Azure](data-lake-store-get-started-portal.md). Una vez que haya creado la cuenta, realice las siguientes tareas para cargar datos de ejemplo. Necesitará estos datos más adelante en el tutorial para ejecutar trabajos desde un clúster de HDInsight que accedan a datos en el Almacén de Data Lake. 

	* [Cree una carpeta en el Almacén de Data Lake](data-lake-store-get-started-portal.md#createfolder).
	* [Cargue un archivo a su Almacén de Data Lake](data-lake-store-get-started-portal.md#uploaddata). Si busca datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).


## Creación de un clúster de HDInsight con acceso al Almacén de Azure Data Lake

En esta sección, se crea un clúster de Hadoop en HDInsight que usa el Almacén de Data Lake como almacenamiento adicional. En esta versión, para un clúster de Hadoop, el Almacén de Data Lake solo sirve como almacenamiento adicional para el clúster. El almacenamiento predeterminado seguirá siendo los blobs de almacenamiento de Azure (WASB). En primer lugar, crearemos la cuenta de almacenamiento y los contenedores de almacenamiento necesarios para el clúster.

1. Inicie sesión en el nuevo [Portal de Azure](https://portal.azure.com).

2. Siga los pasos descritos en [Creación de clústeres de Hadoop en HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal) para iniciar el aprovisionamiento de un clúster de HDInsight.
 
3. En la hoja **Configuración opcional**, haga clic en **Origen de datos**. En la hoja **Origen de datos**, especifique los detalles de la cuenta de almacenamiento y el contenedor de almacenamiento, especifique **Ubicación** como **Este de EE. UU. 2** y después haga clic en **Identidad de AAD de clúster**.

	![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Agregar entidad de servicio al clúster de HDInsight")

4. En la hoja **Identidad de AAD de clúster**, puede seleccionar una entidad de servicio existente o crear una nueva.
	
	* **Cree una nueva entidad de servicio**
	
		* En la hoja **Identidad de AAD del clúster**, haga clic en **Crear nuevo**y en **Entidad de servicio** y después, en la hoja **Crear entidad de servicio**, proporcione valores para crear una nueva entidad de servicio. En este paso, también se crean un certificado y una aplicación de Azure Active Directory. Haga clic en **Crear**.

			![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Agregar entidad de servicio al clúster de HDInsight")

		* En la hoja **Identidad de AAD de clúster**, haga clic en **Administrar acceso a ADLS**. En el panel, aparecen las cuentas de Almacén de Data Lake asociadas a la suscripción. Sin embargo, puede establecer los permisos solo para la cuenta que creó. Seleccione los permisos de lectura, escritura y ejecución para la cuenta que desea asociar con el clúster de HDInsight y después haga clic en **Guardar permisos**.

			![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Agregar entidad de servicio al clúster de HDInsight")

		* En la hoja **Identidad de AAD de clúster**, haga clic en **Descargar certificado** para descargar el certificado asociado a la entidad de servicio creada. Esto es útil si desea usar la misma entidad de servicio en el futuro, cuando cree más clústeres de HDInsight. Haga clic en **Seleccionar**.

			![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.4.png "Agregar entidad de servicio al clúster de HDInsight")


	* **Elija una entidad de servicio existente**.

		* En la hoja **Identidad de AAD de clúster**, haga clic en **Usar existente** y en **Entidad de servicio**, y después, en la hoja **Seleccionar entidad de servicio**, busque una entidad de servicio existente. Haga clic en un nombre de entidad de servicio y después en **Seleccionar**.

			![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Agregar entidad de servicio al clúster de HDInsight")

		* En la hoja **Identidad de AAD de clúster**, cargue el certificado (.pfx) asociado a la entidad de servicio que seleccionó y después proporcione la contraseña del certificado.
		
		* Haga clic en **Administrar acceso a ADLS**. En el panel, aparecen las cuentas de Almacén de Data Lake asociadas a la suscripción. Sin embargo, puede establecer los permisos solo para la cuenta que creó. Seleccione los permisos de lectura, escritura y ejecución para la cuenta que desea asociar con el clúster de HDInsight y después haga clic en **Guardar permisos**.

			![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.existing.save.png "Agregar entidad de servicio al clúster de HDInsight")

		* Haga clic en **Guardar permisos** y después en **Seleccionar**.

6. Haga clic en **Seleccionar** en la hoja **Origen de datos** y continúe con el aprovisionamiento del clúster tal y como se describe en [Creación de clústeres de Hadoop en HDInsight](../hdinsight/hdinsight-provision-clusters.md#create-using-the-preview-portal).

7. Una vez aprovisionado el clúster, puede comprobar que la entidad de servicio esté asociada con el clúster de HDInsight. Para hacerlo, en la hoja del clúster, haga clic en **Configuración** y en **Identidad de AAD de clúster**; debería ver la entidad de servicio asociada.

	![Agregar entidad de servicio al clúster de HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Agregar entidad de servicio al clúster de HDInsight")

## Ejecución de trabajos de prueba en el clúster de HDInsight para usar el Almacén de Azure Data Lake

Después de configurar un clúster de HDInsight, puede ejecutar trabajos de prueba en el clúster para probar que el clúster de HDInsight puede acceder a datos en el Almacén de Azure Data Lake. Para hacerlo, vamos a ejecutar algunas consultas de Hive que tienen como destino el Almacén de Data Lake.

### En un clúster de Linux

1. Abra la hoja del clúster que acaba de aprovisionar y después haga clic en **Panel**. Se abre Ambari para el clúster de Linux. Al acceder a Ambari, se le pedirá autenticarse en el sitio. Escriba el nombre de cuenta y la contraseña del administrador (el administrador predeterminado) que usó al crear el clúster.

	![Inicie el panel del clúster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdiadlcluster1.png "Inicie el panel del clúster")

	También puede ir directamente a Ambari, para lo que debe dirigirse a https://CLUSTERNAME.azurehdinsight.net en un explorador web (donde **CLUSTERNAME** es el nombre del clúster de HDInsight).

2. Abra la vista de Hive. Seleccione el grupo de cuadrados en el menú de la página (junto al vínculo **Admin** y el botón de la derecha de la página) para mostrar las vistas disponibles. Seleccione la vista de **Hive**.

	![Selección de vistas de Ambari](./media/data-lake-store-hdinsight-hadoop-use-portal/selecthiveview.png)

3. Debería ver una página similar a la siguiente:

	![Imagen de la página de vista de Hive, que contiene una sección del Editor de consultas](./media/data-lake-store-hdinsight-hadoop-use-portal/hiveview.png)

4. En la sección **Editor de consultas** de la página, pegue la siguiente instrucción de HiveQL en la hoja de cálculo:

		CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder'

5. Haga clic en el botón **Ejecutar** de la parte inferior del **Editor de consultas** para iniciar la consulta. Debe aparecer la sección **Resultados del proceso de consulta** bajo el **Editor de consultas** y mostrar información sobre el trabajo.

6. Cuando haya finalizado la consulta, la sección **Resultados del proceso de consulta** mostrará los resultados de la operación. La pestaña **Resultados** debe contener la información siguiente:

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


## Uso del Almacén de Data Lake en una topología de Storm

El Almacén de Data Lake se puede usar para escribir datos de una topología de Storm. Para instrucciones sobre cómo lograr este escenario, consulte [Uso del Almacén de Azure Data Lake con Apache Storm con HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## Consulte también

* [Aprovisionamiento de un clúster de HDInsight con el Almacén de Data Lake mediante Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=AcomDC_0204_2016-->