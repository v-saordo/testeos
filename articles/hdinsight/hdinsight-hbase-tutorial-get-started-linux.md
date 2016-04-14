<properties
	pageTitle="Tutorial de HBase: Introducción a clústeres de HBase basados en Linux en Hadoop | Microsoft Azure"
	description="Siga este tutorial de HBase para empezar a usar Apache HBase con Hadoop en HDInsight. Cree tablas desde el shell de HBase y consúltelas mediante Hive."
	keywords="apache hbase,hbase,shell de hbase,tutorial de hbase"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/03/2016"
	ms.author="jgao"/>



# Tutorial de HBase: Introducción al uso de Apache HBase con Hadoop en HDInsight basado en Linux 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Aprenda a crear un clúster de HBase en HDInsight, a crear tablas de HBase y a consultar tablas mediante Hive. Para obtener información general de HBase, vea [Información general de HBase de HDInsight][hdinsight-hbase-overview].

La información contenida en este documento es específica de los clústeres de HDInsight basados en Linux. Para más información acerca de los clústeres basados en Windows, utilice el Selector de pestañas de la parte superior de la página para cambiar.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

###Requisitos previos

Antes de empezar este tutorial de HBase, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Secure Shell (SSU)](hdinsight-hadoop-linux-use-ssh-unix.md). 
- [curl](http://curl.haxx.se/download.html).

## Creación del clúster de HBase.

El siguiente procedimiento utiliza una plantilla ARM de Azure para crear un clúster de HBase. Para comprender los parámetros utilizados en el procedimiento y otros métodos de creación del clúster, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Haga clic en la imagen siguiente para abrir una plantilla ARM en el Portal de Azure. La plantilla ARM se encuentra en un contenedor de blobs público. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/es-ES/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. En la hoja **Parámetros**, escriba lo siguiente:

    - **ClusterName**: escriba un nombre para el clúster de HBase que va a crear.
    - **Nombre de inicio de sesión y contraseña de clúster**: el nombre de inicio de sesión predeterminado es **admin**.
    - **Nombre de usuario y contraseña de SSH**: el nombre de usuario predeterminado es **sshuser**. Puede cambiarlo.
     
    Otros parámetros son opcionales.
    
    Cada clúster tiene una dependencia de cuenta de Almacenamiento de blobs de Azure. Después de eliminar un clúster, los datos permanecen en la cuenta de almacenamiento. El nombre de cuenta de almacenamiento de clúster predeterminado es el nombre del clúster con "store" anexado. Está codificado en la sección de variables de la plantilla.
        
3. Haga clic en **Aceptar** para guardar los parámetros.
4. En la hoja **Implementación personalizada**, haga clic en el cuadro desplegable **Grupo de recursos** y, después, haga clic en **Nuevo** para crear un nuevo grupo de recursos. El grupo de recursos es un contenedor que agrupa al clúster, a la cuenta de almacenamiento dependiente y a otros recursos vinculados.
5. Haga clic en **Términos legales** y, luego, en **Crear**.
6. Haga clic en **Crear**. Tarda aproximadamente 20 minutos en crear un clúster.


>[AZURE.NOTE] Después de que se elimine un clúster de HBase, puede crear otro clúster de HBase mediante el mismo contenedor de blobs predeterminado. El nuevo clúster seleccionará las tablas de HBase que creó en el clúster original.

## Creación de tablas e inserción de datos

Puede utilizar SSH para conectarse a clústeres de HBase y utilizar el shell de HBase para crear tablas de HBase e insertar y consultar datos. Para obtener más información sobre la utilización de SSH desde Linux, Unix, OS X y Windows, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md) o [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md).
 

Para la mayoría de las personas, los datos aparecen en formato tabular:

![datos tabulares de hbase de hdinsight][img-hbase-sample-data-tabular]

En HBase, que es una implementación de BigTable, los mismos datos tienen un aspecto similar al siguiente:

![datos bigtable de hbase de hdinsight][img-hbase-sample-data-bigtable]

Tendrá más sentido cuando termine el siguiente procedimiento.


**Para usar el shell de HBase, siga estos pasos:**

1. Desde SSH ejecute el siguiente comando:

		hbase shell

4. Cree un HBase con dos familias de columnas:

		create 'Contacts', 'Personal', 'Office'
		list
5. Inserte algunos datos:

		put 'Contacts', '1000', 'Personal:Name', 'John Dole'
		put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
		put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
		put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
		scan 'Contacts'

	![shell de hbase de hadoop de hdinsight][img-hbase-shell]

6. Obtenga una sola fila

		get 'Contacts', '1000'

	Verá los mismos resultados que con el comando de análisis porque solo hay una fila.

	Para obtener más información acerca del esquema de tabla de Hbase, consulte [Introducción al diseño de esquema de HBase][hbase-schema]. Para ver más comandos de HBase, consulte [Guía de referencia de Apache HBase][hbase-quick-start].

6. Salga del shell

		exit

**Para cargar datos de forma masiva en la tabla HBase de contactos**

HBase incluye varios métodos de carga de datos en las tablas. Para obtener más información, vea [Carga masiva](http://hbase.apache.org/book.html#arch.bulk.load).


Se ha cargado un archivo de datos de ejemplo en un contenedor de blobs público, **wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*. El contenido del archivo de datos es:

	8396	Calvin Raji		230-555-0191	230-555-0191	5415 San Gabriel Dr.
	16600	Karen Wu		646-555-0113	230-555-0192	9265 La Paz
	4324	Karl Xie		508-555-0163	230-555-0193	4912 La Vuelta
	16891	Jonn Jackson	674-555-0110	230-555-0194	40 Ellis St.
	3273	Miguel Miller	397-555-0155	230-555-0195	6696 Anchor Drive
	3588	Osa Agbonile	592-555-0152	230-555-0196	1873 Lion Circle
	10272	Julia Lee		870-555-0110	230-555-0197	3148 Rose Street
	4868	Jose Hayes		599-555-0171	230-555-0198	793 Crawford Street
	4761	Caleb Alexander	670-555-0141	230-555-0199	4775 Kentucky Dr.
	16443	Terry Chander	998-555-0171	230-555-0200	771 Northridge Drive

Puede crear un archivo de texto y cargar el archivo en su propia cuenta de almacenamiento si lo desea. Para obtener instrucciones, consulte [Carga de datos para trabajos de Hadoop en HDInsight][hdinsight-upload-data].

> [AZURE.NOTE] Este procedimiento usa la tabla HBase de contactos que ha creado en el último procedimiento.

1. Desde SSH ejecute el siguiente comando para transformar el archivo de datos en StoreFiles y almacene en una ruta de acceso relativa especificada por Dimporttsv.bulk.output:. Si está en el shell de HBase, utilice el comando de salida para salir.

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Ejecute el siguiente comando para cargar los datos desde /example/data/storeDataFileOutput en la tabla HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Puede abrir el shell de HBase y usar el comando de análisis para mostrar el contenido de la tabla.



## Utilización de Hive para consultar HBase

Puede consultar datos en tablas de HBase mediante el uso de Hive. En esta sección se crea una tabla de Hive que se asigna a la tabla de HBase y se usa para consultar los datos en la tabla de HBase.

1. Abra **PuTTY** y conéctese al clúster. Consulte las instrucciones del procedimiento anterior.
2. Abra el shell de Hive.

	hive
3. Escriba el siguiente script de HiveQL para crear una tabla de Hive que se asigne a la tabla de HBase. Antes de ejecutar esta instrucción, asegúrese de haber creado la tabla de ejemplo a la que se hace referencia aquí en HBase mediante el shell de HBase.

		CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Ejecute el siguiente script de HiveQL. La consulta de Hive consulta los datos de la tabla HBase:

     	SELECT count(*) FROM hbasecontacts;

## Usar las API de REST de HBase con Curl

> [AZURE.NOTE] Al usar Curl o cualquier otra comunicación REST con WebHCat, debe proporcionar el nombre de usuario y la contraseña del administrador del clúster de HDInsight para autenticar las solicitudes. También debe usar el nombre del clúster como parte del identificador uniforme de recursos (URI) que se usa para enviar las solicitudes al servidor.
>
> En el caso de los comandos que aparecen en esta sección, reemplace **USERNAME** por el usuario para autenticación en el clúster y **PASSWORD** por la contraseña de la cuenta de usuario. Reemplace **CLUSTERNAME** por el nombre del clúster.
>
> La API de REST se protege con la [autenticación básica](http://en.wikipedia.org/wiki/Basic_access_authentication). Siempre debe crear solicitudes usando HTTP segura (HTTPS) para así garantizar que las credenciales se envían de manera segura al servidor.

1. Desde una línea de comandos, utilice el siguiente comando para comprobar que puede conectarse al clúster de HDInsight.

		curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

	Debería recibir una respuesta similar a la siguiente:

    {"status":"ok","version":"v1"}

  Los parámetros que se utilizan en este comando son los siguientes:

    * **-u** - The user name and password used to authenticate the request.
    * **-G** - Indicates that this is a GET request.

2. Use el siguiente comando para mostrar las tablas de HBase existentes:

		curl -u <UserName>:<Password> -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Use el siguiente comando para crear una nueva tabla de HBase con dos familias de columnas:

		curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{"@name":"test","ColumnSchema":[{"name":"Personal"},{"name":"Office"}]}"

	El esquema se ofrece con el formato JSon.

4. Use el siguiente comando para instalar algunos datos:

		curl -u <UserName>:<Password> -v -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" -H "Accept: application/json" -H "Content-Type: application/json" -d "{"Row":{"key":"1000","Cell":{"column":"Personal:Name", "$":"John Dole"}}}"

5. Use el siguiente comando para obtener una fila:

		curl -u <UserName>:<Password> -v -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" -H "Accept: application/json"

## Comprobar el estado del clúster

HBase en HDInsight se incluye con una interfaz de usuario web para la supervisión de clústeres. Mediante la interfaz de usuario web, puede solicitar estadísticas o información acerca de las regiones.

SSH también se puede usar para tunelizar las solicitudes locales, como solicitudes web, al clúster de HDInsight. La solicitud se enrutará al recurso solicitado como si se hubiese originado en el nodo principal del clúster de HDInsight. Para obtener más información, consulte [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel).

**Para establecer una sesión de tunelización de SSH**

1. Abra **PuTTY**.  
2. Si especificó una clave SSH al crear la cuenta de usuario durante el proceso de creación, debe realizar el siguiente paso para seleccionar la clave privada que se usará al autenticarse en el clúster:

	En **Category** (Categoría), expanda **Connection** (Conexión), **SSH** y, a continuación, seleccione **Auth** (Autenticar). Finalmente, haga clic en **Browse** (Examinar) y seleccione el archivo .ppk que contiene su clave privada.

3. En **Category** (Categoría), haga clic en **Sesión**.
4. Entre las opciones básicas para su pantalla de sesión de PuTTY, escriba los siguientes valores:

	- **Nombre de host**: dirección de SSH de su servidor de HDInsight en el campo del nombre de host (o dirección IP). La dirección SSH es el nombre de su clúster, seguido de **-ssh.azurehdinsight.net**. Por ejemplo, *mycluster-ssh.azurehdinsight.net*.
	- **Puerto**: 22. El puerto ssh en el odo principal 0 es 22.  
5. En la sección **Categoría**, situada a la izquierda del cuadro de diálogo, expanda **Conexión**, **SSH** y haga clic en **Túneles**.
6. Proporcione la siguiente información en el formulario Opciones que controlan el desvío de puertos SSH:

	- **Source port**: el puerto en el cliente que desea desviar. Por ejemplo, 9876.
	- **Dynamic**: habilita el enrutamiento dinámico del proxy SOCKS.
7. Haga clic en **Agregar** para agregar la configuración.
8. Haga clic en **Abrir**, en la parte inferior del cuadro de diálogo, para abrir una conexión SSH.
9. Cuando se le solicite, inicie sesión en el servidor mediante una cuenta de SSH. Esto establecerá una sesión SSH y habilitará el túnel.

**Para encontrar el FQDN de los zookeepers con Ambari**

1. Vaya a https://<ClusterName>.azurehdinsight.net/.
2. Escriba las credenciales de la cuenta de usuario de clúster dos veces.
3. En el menú izquierdo, haga clic en **zookeeper**.
4. Haga clic en uno de los tres vínculos **Servidor de ZooKeeper** en la lista de resumen.
5. Copie el **Nombre de host**. Por ejemplo, zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net.

**Para configurar un programa de cliente (Firefox) y comprobar el estado del clúster**

1. Abra Firefox.
2. Haga clic en el botón **Abrir menú**
3. Haga clic en **Opciones**.
4. Haga clic en **Avanzadas**, en **Red** y luego en **Configuración**.
5. Seleccione **Configuración manual del proxy**.
6. Escriba los siguientes valores:

	- **Host de Socks**: localhost
	- **Puerto**: use el mismo puerto que configuró en la tunelización SSH de Putty. Por ejemplo, 9876.
	- **SOCKS v5**: (seleccionado)
	- **DNS remoto**: (seleccionado)
7. Haga clic en **Aceptar** para guardar los cambios.
8. Vaya a http://<TheFQDN of a ZooKeeper>:60010/master-status

En un clúster de alta disponibilidad, encontrará un vínculo al nodo maestro de HBase activo actual que hospeda la interfaz de usuario web.

##Eliminación del clúster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Pasos siguientes
En este tutorial de HBase para HDInsight, ha aprendido a aprovisionar un clúster HBase, a crear tablas y a ver los datos de esas tablas en el shell de HBase. También ha aprendido a usar una consulta de datos de Hive en las tablas de HBase y a usar las API de REST de C# para HBase para crear una tabla de HBase y recuperar los datos de la tabla.

Para obtener más información, consulte:

- [Información general de HBase de HDInsight][hdinsight-hbase-overview]\: HBase es una base de datos NoSQL de código abierto Apache basada en Hadoop que proporciona acceso aleatorio y una coherencia sólida para grandes cantidades de datos no estructurados y semiestructurados.


[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png

<!---HONumber=AcomDC_0309_2016-->