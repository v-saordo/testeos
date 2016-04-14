<properties 
	pageTitle="Análisis de datos de retraso de vuelos con Hive en HDInsight basado en Linux | Microsoft Azure" 
	description="Aprenda a usar Hive para analizar datos de vuelos en HDInsight basado en Linux y luego exporte los datos a la Base de datos SQL con Sqoop." 
	services="hdinsight" 
	documentationCenter="" 
	authors="Blackmist" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="larryfr"/>

#Análisis de datos de retraso de vuelos con Hive en HDInsight

Aprenda a analizar datos de retrasos de vuelos con Hive en HDInsight basado en Linux y luego exportar los datos a la Base de datos SQL de Azure con Sqoop.

> [AZURE.NOTE] Aunque se pueden usar distintas partes de este documento con clústeres de HDInsight basados en Windows (Python y Hive, por ejemplo), muchos de los pasos son específicos de los clústeres basados en Linux. Para conocer el procedimiento que funcionará con un clúster basado en Windows, vea [Analizar datos de retrasos de vuelos en Hive en HDInsight](hdinsight-analyze-flight-delay-data.md)

###Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Un clúster de HDInsight__. Vea [Introducción al uso de Hadoop con Hive en HDInsight en Linux](hdinsight-hadoop-linux-tutorial-get-started.md) para conocer los pasos sobre la creación de un clúster de HDInsight basado en Linux.

- __Base de datos de SQL Azure__. Usará una Base de datos SQL de Azure como almacén de datos de destino. Si no dispone todavía de una Base de datos SQL, consulte [Tutorial de Base de datos SQL: creación de una Base de datos SQL en cuestión de minutos](../sql-database/sql-database-get-started.md).

- __Azure CLI__. Si no ha instalado la CLI de Azure, vea [Instalación y configuración de la interfaz de la línea de comandos (CLI) de Azure](../xplat-cli-install.md) para conocer más pasos.


##Descarga de los datos de vuelo

1. Diríjase a [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].
2. En la página, seleccione los siguientes valores:

	| Nombre | Valor | | Año de filtro | 2013 | | Período de filtro | Enero | | Campos | Year, FlightDate, UniqueCarrier, Carrier, FlightNum, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. Borrado de los demás campos |

3. Haga clic en **Descargar**.

##Carga de los datos

1. Use el siguiente comando para cargar el archivo .zip en el nodo principal del clúster de HDInsight:

		scp FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

	Reemplace __FILENAME__ por el nombre del archivo .zip. Reemplace __USERNAME__ por el inicio de sesión SSH para el clúster de HDInsight. Reemplace CLUSTERNAME por el nombre del clúster de HDInsight.
	
	> [AZURE.NOTE] Si usa una contraseña para proteger el inicio de sesión SSH, se le pedirá la contraseña. Si utiliza una clave pública, puede que tenga que usar el parámetro `-i` y especificar la ruta de acceso a la correspondiente clave privada. Por ejemplo: `scp -i ~/.ssh/id_rsa FILENAME.csv USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

2. Tras completar la carga, conéctese al clúster mediante SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Para obtener más información sobre el uso de SSH con HDInsight basado en Linux, vea los siguientes artículos:
	
	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
	
3. Una vez conectado, use lo siguiente para descomprimir el archivo .zip:

		unzip FILENAME.zip
	
	Así extrae un archivo .csv que tiene un tamaño aproximado de 60 MB.
	
4. Use la instrucción siguiente para crear un nuevo directorio en WASB (el almacén de datos distribuidos que usa HDInsight) y copie el archivo:

	hadoop fs -mkdir -p /tutorials/flightdelays/data hadoop fs -copyFromLocal FILENAME.csv /tutorials/flightdelays/data/FILENAME.csv
	
##Creación y ejecución de HiveQL

Siga estos pasos para importar datos desde el archivo CSV en una tabla de Hive denominada __Delays__.

1. Use la instrucción siguiente para crear y editar un nuevo archivo denominado __flightdelays.hql__:

		nano flightdelays.hql
		
	Use el siguiente contenido para este archivo:
	
		DROP TABLE delays_raw;
		-- Creates an external table over the csv file
		CREATE EXTERNAL TABLE delays_raw (
			YEAR string,
			FL_DATE string,
			UNIQUE_CARRIER string,
			CARRIER string,
			FL_NUM string,
			ORIGIN_AIRPORT_ID string,
			ORIGIN string,
			ORIGIN_CITY_NAME string,
			ORIGIN_CITY_NAME_TEMP string,
			ORIGIN_STATE_ABR string,
			DEST_AIRPORT_ID string,
			DEST string,
			DEST_CITY_NAME string,
			DEST_CITY_NAME_TEMP string,
			DEST_STATE_ABR string,
			DEP_DELAY_NEW float,
			ARR_DELAY_NEW float,
			CARRIER_DELAY float,
			WEATHER_DELAY float,
			NAS_DELAY float,
			SECURITY_DELAY float,
			LATE_AIRCRAFT_DELAY float)
		-- The following lines describe the format and location of the file
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE
		LOCATION '/tutorials/flightdelays/data';
		
		-- Drop the delays table if it exists
		DROP TABLE delays;
		-- Create the delays table and populate it with data
		-- pulled in from the CSV file (via the external table defined previously)
		CREATE TABLE delays AS
		SELECT YEAR AS year,
		    FL_DATE AS flight_date,
		    substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
		    substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
		    substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
		    ORIGIN_AIRPORT_ID AS origin_airport_id,
		    substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
		    substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
		    substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
		    DEST_AIRPORT_ID AS dest_airport_id,
		    substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
		    substring(DEST_CITY_NAME,2) AS dest_city_name,
		    substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
		    DEP_DELAY_NEW AS dep_delay_new,
		    ARR_DELAY_NEW AS arr_delay_new,
		    CARRIER_DELAY AS carrier_delay,
		    WEATHER_DELAY AS weather_delay,
		    NAS_DELAY AS nas_delay,
		    SECURITY_DELAY AS security_delay,
		    LATE_AIRCRAFT_DELAY AS late_aircraft_delay
		FROM delays_raw;
		
2. Use __Ctrl+X__ y luego __Y__ para guardar el archivo.

3. Use la instrucción siguiente para iniciar Hive y ejecutar el archivo __flightdelays.hql__:

		hive -i flightdelays.hql
		
	Se ejecuta el archivo y luego devuelve un símbolo del sistema `hive>`.

4. Cuando reciba el símbolo del sistema `hive>`, use lo siguiente para recuperar datos de los datos de retrasos de vuelos importados.

		INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
		SELECT regexp_replace(origin_city_name, '''', ''),
			avg(weather_delay)
		FROM delays
		WHERE weather_delay IS NOT NULL
		GROUP BY origin_city_name;

	Se recupera una lista de ciudades que experimentaron demoras debidas a inclemencias del tiempo, junto con el tiempo medio de retraso; guárdela en `/tutorials/flightdelays/output`. Más adelante, Sqoop leerá los datos desde esta ubicación y los exportará a la Base de datos SQL Azure.

##una Base de datos SQL

Siga estos pasos para crear una Base de datos SQL de Azure: Esta se usará para almacenar los datos exportados desde HDInsight a través de Sqoop.

1. Desde el entorno de desarrollo donde está instalada la CLI de Azure, use el comando siguiente para crear una nueva Base de datos SQL Azure:

		azure sql server create <adminLogin> <adminPassword> <region>

	Por ejemplo, `azure sql server create admin password "West US"`.

	Cuando finalice el comando, recibirá una respuesta similar a lo siguiente:

		info:    Executing command sql server create
		+ Creating SQL Server
		data:    Server Name i1qwc540ts
		info:    sql server create command OK

> [AZURE.IMPORTANT] Tenga en cuenta el nombre del servidor que devuelve este comando. Este es el nombre corto del servidor de la base de datos SQL que se creó. El nombre completo de dominio (FQDN) es `<shortname>.database.windows.net`.

2. Utilice el siguiente comando para crear una base de datos denominada **sqooptest** en el servidor de la base de datos SQL:

        azure sql db create [options] <serverName> sqooptest <adminLogin> <adminPassword>

    Esto devolverá el mensaje "Aceptar" cuando termine.

	> [AZURE.NOTE] Si recibe un error en el que se indica que no tiene acceso, puede que necesite agregar la dirección IP de la estación de trabajo del cliente en el firewall de la base de datos SQL mediante el siguiente comando:
	>
	> `sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>`

##Creación de una tabla de Base de datos SQL

> [AZURE.NOTE] Hay muchas maneras de conectarse a la base de datos SQL para crear una tabla. En los siguientes pasos se utiliza [FreeTDS](http://www.freetds.org/) desde el clúster de HDInsight.

1. Use SSH para conectarse al clúster de HDInsight basado en Linux y ejecute los siguientes pasos en la sesión SSH.

3. Use el siguiente comando para instalar FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Una vez se ha instalado FreeTDS, use el comando siguiente para conectarse al servidor de la base de datos SQL que creó anteriormente:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Recibirá un resultado similar al siguiente.

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. En el símbolo del sistema `1>`, introduzca las líneas siguientes:

        CREATE TABLE [dbo].[delays](
		[origin_city_name] [nvarchar](50) NOT NULL,
		[weather_delay] float,
		CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
		([origin_city_name] ASC))
		GO

    Cuando se haya especificado la instrucción `GO`, se evaluarán las instrucciones anteriores. Se creará una nueva tabla denominada __delays__ con un índice agrupado (que requiere la Base de datos SQL).

    Para comprobar que se ha creado la tabla, utilice lo siguiente:

        SELECT * FROM information_schema.tables
        GO

    Debería ver una salida similar a la siguiente:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     delays      BASE TABLE

8. Para salir de la utilidad tsql, escriba `exit` en el símbolo del sistema `1>`.
	
##Exportación de datos con Sqoop

1. Utilice el siguiente comando para crear un vínculo a JDBC Driver para SQL Server desde el directorio de la biblioteca de Sqoop. Esto permite a Sqoop utilizar este controlador para comunicarse con la base de datos SQL:

		sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc4.jar /usr/hdp/current/sqoop-client/lib/sqljdbc4.jar

2. Utilice el siguiente comando para comprobar que Sqoop puede ver la base de datos SQL:

		sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

	Esto debe devolver una lista de bases de datos, incluida la base de datos sqooptest que creó antes.

3. Use el comando siguiente para exportar los datos de hivesampletable a la tabla mobiledata:

		sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir 'wasb:///tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1

	Esto indica a Sqoop que debe conectarse a la Base de datos SQL, a la base de datos sqooptest, y exportar los datos de wasb:///tutorials/flightdelays/output (donde se almacenó la salida de la consulta de Hive anterior) a la tabla delays.

4. Una vez completado el comando, utilice lo siguiente para conectarse a la base de datos mediante TSQL:

		TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

	Una vez conectada, use las instrucciones siguientes para comprobar que los datos se exportaron a la tabla mobiledata:
	
		SELECT * FROM delays
		GO

	Debería ver una lista de los datos de la tabla. Escriba `exit` para salir de la utilidad de tsql.

##<a id="nextsteps"></a> Pasos siguientes
Ahora sabe cómo cargar un archivo en el almacenamiento de blobs de Azure, cómo rellenar una tabla de Hive con los datos del almacenamiento de blobs de Azure, cómo ejecutar consultas de Hive y cómo usar Sqoop para exportar datos de HDFS a la Base de datos SQL de Azure. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight][hdinsight-get-started]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Uso de Oozie con HDInsight][hdinsight-use-oozie]
* [Uso de Sqoop con HDInsight][hdinsight-use-sqoop]
* [Uso de Pig con HDInsight][hdinsight-use-pig]
* [Desarrollo de programas de MapReduce de Java para HDInsight][hdinsight-develop-mapreduce]
* [Desarrollo de programas de streaming para HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx


 

<!---HONumber=AcomDC_0204_2016-->