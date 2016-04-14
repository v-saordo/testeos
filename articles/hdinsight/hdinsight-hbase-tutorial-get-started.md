<properties
	pageTitle="Tutorial de HBase: introducción a HBase en Hadoop | Microsoft Azure"
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
	ms.date="02/05/2016"
	ms.author="jgao"/>



# Tutorial de HBase: Introducción al uso de Apache HBase con Hadoop en HDInsight basado en Windows

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Aprenda a crear un clúster de HBase en HDInsight, a crear tablas de HBase y a consultar las tablas mediante Apache Hive. Para obtener información general de HBase, vea [Información general de HBase de HDInsight][hdinsight-hbase-overview].

La información contenida en este documento es específica de los clústeres de HDInsight basados en Windows. Para más información acerca de los clústeres basados en Windows, utilice el Selector de pestañas de la parte superior de la página para cambiar.

> [AZURE.NOTE] HBase (versión 0.98.0) en HDInsight basado en Windows solo está disponible para su uso con clústeres de HDInsight 3.1 (basados en Apache Hadoop y YARN 2.4.0). Para obtener información de la versión, consulte [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight][hdinsight-versions].

###Requisitos previos

Antes de empezar este tutorial de HBase, debe contar con lo siguiente:

- **Una suscripción de Microsoft Azure válida**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Una estación de trabajo** con Visual Studio 2013 o posterior. Para obtener instrucciones, vea [Instalación de Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

## Creación del clúster de HBase.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Para crear un clúster de HBase mediante el Portal de Azure, siga estos pasos:**

1. Inicie sesión en el [Portal de Azure][azure-management-portal].
2. Haga clic en **Nuevo** o en **+** en la esquina superior izquierda y luego en **Datos y análisis**, **HDInsight**.
3. Escriba los siguientes valores:

	- **Nombre de clúster**: escriba un nombre que identifique este clúster.
	- **Tipo de clúster**: seleccione **HBase**.
	- **Sistema operativo de clústeres**: seleccione **Windows**. Para crear un clúster de HBase basado en Linux, vea [Tutorial de HBase: Introducción al uso de Apache HBase con Hadoop en HDInsight (Linux)](hdinsight-hbase-tutorial-get-started-linux.md).
	- **Versión**: seleccione una versión de HBase.
	- **Suscripción**: seleccione la suscripción de Azure que se usará para crear este clúster.
	- **Grupo de recursos**: cree un grupo de recursos de Azure o seleccione uno existente. Para más información, vea [Información general del Administrador de recursos de Azure](resource-group-overview.md).
	- **Credenciales**: en el caso de clústeres basados en Windows, puede crear un usuario de clúster (también denominado usuario HTTP, usuario de servicio web HTTP) y un usuario de Escritorio remoto. Haga clic en **Habilitar Escritorio remoto** para agregar las credenciales de usuario de Escritorio remoto. La siguiente sección requiere RDP.
	- **Origen de datos**: cree una cuenta de almacenamiento de Azure o seleccione una existente para usarla como sistema de archivos predeterminado para el clúster. La ubicación predeterminada de la cuenta de almacenamiento determina la ubicación del clúster. La cuenta de almacenamiento predeterminada y el clúster deben colocarse en el mismo centro de datos.
	- **Niveles de precios de nodo**: seleccione el número de servidores de región para el clúster de HBase.

		> [AZURE.WARNING] Para lograr alta disponibilidad de servicios de HBase, debe crear un clúster que contenga al menos **tres** nodos. Esto garantiza que, si un nodo deja de funcionar, las regiones de datos de HBase están disponibles en otros nodos.

		> Si está aprendiendo HBase, elija 1 siempre para el tamaño del clúster y elimine el clúster después de cada uso para reducir el coste.

	- **Configuración opcional**: configure la red virtual de Azure, configure acciones de script y agregue otras cuentas de almacenamiento.

4. Haga clic en **Crear**.

>[AZURE.NOTE] Después de eliminar un clúster de HBase, puede crear otro clúster de HBase con el mismo contenedor de blobs predeterminado y la misma cuenta de almacenamiento. El nuevo clúster seleccionará las tablas de HBase que creó en el clúster original.

## Creación de tablas e inserción de datos

Actualmente, hay dos formas de tener acceso a HBase. En esta sección se trata el uso del shell de HBase. En la sección siguiente se describe el uso del SDK de .NET.

Para la mayoría de las personas, los datos aparecen en formato tabular:

![datos tabulares de hbase de hdinsight][img-hbase-sample-data-tabular]

En HBase, que es una implementación de BigTable, los mismos datos tienen un aspecto similar al siguiente:

![datos bigtable de hbase de hdinsight][img-hbase-sample-data-bigtable]

Tendrá más sentido cuando termine el siguiente procedimiento.

**Para usar el shell de HBase, siga estos pasos:**

1. Use RDP para conectarse a su clúster de HBase en HDInsight. Para consultar las instrucciones de RDP, vea [Administración de clústeres en HDInsight mediante el Portal de Azure][hdinsight-manage-portal].
2. En la sesión de RDP, haga clic en el acceso directo de la **línea de comandos de Hadoop** situada en el escritorio.
3. Abra del shell de HBase:

		cd %HBASE_HOME%\bin
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

	Para obtener más información acerca del esquema de la tabla de Hbase, consulte [Introducción al diseño de esquema de HBase][hbase-schema]. Para ver más comandos de HBase, consulte [Guía de referencia de Apache HBase][hbase-quick-start].


6. Salga del shell

		exit

**Para cargar datos de forma masiva en la tabla HBase de contactos**

HBase incluye varios métodos de carga de datos en las tablas. Para obtener más información, vea [Carga masiva](http://hbase.apache.org/book.html#arch.bulk.load).


Se ha cargado un archivo de datos de ejemplo en un contenedor de blobs público, wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt. El contenido del archivo de datos es:

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

> [AZURE.NOTE] Este procedimiento usa la tabla HBase de contactos que creó en el último procedimiento.

1. En la sesión de RDP, haga clic en el acceso directo de la **línea de comandos de Hadoop** situada en el escritorio.
2. Cambie el directorio:

		cd %HBASE_HOME%\bin

3. Ejecute el siguiente comando para transformar el archivo de datos en StoreFiles y almacene en una ruta de acceso relativa especificada por Dimporttsv.bulk.output:

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Ejecute el siguiente comando para cargar los datos desde /example/data/storeDataFileOutput en la tabla HBase:

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Puede abrir el shell de HBase y usar el comando de análisis para mostrar el contenido de la tabla.



## Uso de Hive para consultar tablas de HBase

Puede consultar datos en tablas de HBase mediante Hive. En esta sección se crea una tabla de Hive que se asigna a la tabla de HBase y se usa para consultar los datos en la tabla de HBase.

**Para abrir el panel de clúster, siga estos pasos:**

1. Vaya a **https://<HDInsight Cluster Name>.azurehdinsight.net/**.
5. Escriba el nombre de usuario y la contraseña de la cuenta de usuario de Hadoop. El nombre de usuario predeterminado es **admin** y la contraseña es la que escribió durante el proceso de creación del clúster. Se abre una nueva pestaña del explorador.
6. Haga clic en **Editor de Hive** en la parte superior de la página. El Editor de Hive tiene el siguiente aspecto:

	![Panel de clúster de HDInsight.][img-hdinsight-hbase-hive-editor]

**Para ejecutar consultas de Hive, siga estos pasos:**

1. Escriba el siguiente script de HiveQL en el Editor de Hive y haga clic en **Enviar** para crear una tabla de Hive que se asigne a la tabla de HBase. Antes de ejecutar esta instrucción, asegúrese de haber creado la tabla de ejemplo a la que se hace referencia aquí en HBase mediante el shell de HBase.

		CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

	Espere hasta que el **Estado** se actualice a **Completado**.

2. Escriba el siguiente script de HiveQL en el Editor de Hive y haga clic en el botón **Enviar**. La consulta de Hive consulta los datos de la tabla HBase:

     	SELECT count(*) FROM hbasecontacts;

4. Para recuperar los resultados de la consulta de Hive, haga clic en el vínculo **Ver detalles** de la ventana **Sesión de trabajo** cuando el trabajo termine de ejecutarse. Habrá un único archivo de salida de trabajo porque se coloca un registro en la tabla de HBase.




**Para examinar el archivo de salida, siga estos pasos:**

1. En la Consola de consultas, haga clic en **Explorador de archivos**.
2. Haga clic en la cuenta de almacenamiento de Azure usada como sistema de archivos predeterminado para el clúster de HBase.
3. Haga clic en el nombre del clúster de HBase. El contenedor de la cuenta de almacenamiento de Azure predeterminado usa el nombre del clúster.
4. Haga clic en **Usuario**, y, a continuación, haga clic en **Admin**. Este es el nombre de usuario de Hadoop.
6. Haga clic en el nombre del trabajo cuya hora de **Última modificación** coincida con la hora a la que se ejecutó la consulta SELECT de Hive.
4. Haga clic en **stdout**. Guarde el archivo y ábralo con el Bloc de notas. Habrá un archivo de salida.

	![Explorador de archivos de Editor de Hive de HBase para HDInsight][img-hdinsight-hbase-file-browser]

## Usar la biblioteca de cliente la API de REST de HBase de .NET

Debe descargar la biblioteca de cliente de la API de REST de HBase para .NET desde GitHub y compilar el proyecto para que pueda usar el SDK para .NET de HBase. El siguiente procedimiento incluye las instrucciones para esta tarea.

1. Cree una nueva aplicación C# de la consola de Escritorio de Windows de Visual Studio.
2. Abra la Consola del Administrador de paquetes NuGet. Para ello, haga clic en **Herramientas** > **Administrador de paquetes de NuGet** > **Consola del Administrador de paquetes**.
3. Ejecute el siguiente comando NuGet en la consola:

		Install-Package Microsoft.HBase.Client

5. Agregue las siguientes instrucciones **using** en la parte superior del archivo:

		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Reemplace la función **Main** por el siguiente contenido:

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version.
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

		    //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
		    Scanner scanSettings = new Scanner()
		    {
    		    batch = 10,
    		    startRow = BitConverter.GetBytes(25),
    		    endRow = BitConverter.GetBytes(35)
		    };

		    ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
		    CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
		    {
    		    foreach (CellSet.Row row in next.rows)
    		    {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
    		    }
		    }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. Establezca las tres primeras variables en la función **Main**.
8. Presione **F5** para ejecutar la aplicación.

## Comprobar el estado del clúster

HBase en HDInsight se incluye con una interfaz de usuario web para la supervisión de clústeres. Mediante la interfaz de usuario web, puede solicitar estadísticas o información acerca de las regiones.

Para abrir la interfaz de usuario web, debe aplicar RDP en el clúster y, a continuación, hacer clic en el acceso directo de la interfaz de usuario web de información de HMaster en su escritorio o usar la siguiente dirección URL en un explorador web:

	http://zookeeper[0-2]:60010/master-status

En un clúster de alta disponibilidad, encontrará un vínculo al nodo maestro de HBase activo actual que hospeda la interfaz de usuario web.




## Pasos siguientes
En este tutorial de HBase para HDInsight, ha aprendido a aprovisionar un clúster HBase, a crear tablas y a ver los datos de esas tablas en el shell de HBase. También ha aprendido a usar una consulta de datos de Hive en las tablas de HBase y a usar las API de REST de C# para HBase para crear una tabla de HBase y recuperar los datos de la tabla.

Para más información, consulte:

- [Información general de HBase de HDInsight][hdinsight-hbase-overview] HBase es una base de datos NoSQL de código abierto Apache basada en Hadoop que ofrece un acceso aleatorio y una coherencia fuerte para grandes cantidades de datos no estructurados y semiestructurados.
- [Creación de clústeres de HBase en Red virtual de Azure][hdinsight-hbase-provision-vnet]. Con la integración de red virtual, los clústeres de HBase se pueden implementar en la misma red que sus aplicaciones para que estas puedan comunicarse directamente con HBase.
- [Configuración de la replicación de HBase en HDInsight](hdinsight-hbase-geo-replication.md). Aprenda a configurar la replicación de HBase entre dos centros de datos de Azure.
- [Análisis de opiniones de Twitter con HBase en HDInsight][hbase-twitter-sentiment]. Descubra cómo realizar [análisis de opinión](http://en.wikipedia.org/wiki/Sentiment_analysis) de macrodatos en tiempo real con HBase en un clúster de Hadoop en HDInsight.

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
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-bigtable.png

<!---HONumber=AcomDC_0211_2016-->