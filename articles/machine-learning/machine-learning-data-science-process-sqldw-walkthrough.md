<properties
	pageTitle="Proceso de Cortana Analytics en acción: uso de Almacenamiento de datos SQL | Microsoft Azure"
	description="Tecnología y procesos de análisis avanzado en acción"  
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,hangzh-msft,wguo123"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016" 
	ms.author="bradsev;hangzh;weig"/>


# Proceso de Cortana Analytics en acción: uso de Almacenamiento de datos SQL

En este tutorial le guiaremos a través de la creación e implementación de un modelo de aprendizaje automático mediante Almacenamiento de datos SQL (SQL DW) para un conjunto de datos disponible públicamente: el conjunto de datos [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/). El modelo de clasificación binaria construido predice si se va a pagar o no una propina para la carrera, y también se describen los modelos de clasificación y regresión multiclase que predicen la distribución de los importes pagados en concepto de propina.

El procedimiento sigue el flujo de trabajo del [proceso de Cortana Analytics (CAP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/). Se muestra cómo configurar un entorno de ciencia de datos, cómo cargar los datos en Almacenamiento de datos SQL y cómo usar Almacenamiento de datos SQL o un IPython Notebook para explorar las características de datos y de diseño para modelar. Luego se muestra cómo compilar e implementar un modelo con Aprendizaje automático de Azure.


## <a name="dataset"></a>Conjunto de datos NYC Taxi Trips

El conjunto de datos NYC Taxi Trips consta de aproximadamente 20 GB de archivos de valores separados por comas (CSV) comprimidos (aproximadamente, 48 GB sin comprimir), que registran más de 173 millones de carreras individuales y las tarifas pagadas por cada carrera. Cada registro de carrera incluye la hora y la ubicación de recogida y de entrega, el número de licencia de (del conductor) anónimo y el número de ida y vuelta incluye la ubicación de entrega y recogida y el tiempo, la número de licencia y el número de identificador único del taxi. Los datos cubren todos los viajes del año 2013 y se proporcionan en los dos conjuntos de datos siguientes para cada mes:

1. El archivo **trip\_data.csv** contiene información detallada de las carreras, como el número de pasajeros, los puntos de recogida y destino, la duración de las carreras y la longitud del recorrido. Estos son algunos registros de ejemplo:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. El archivo **trip\_fare.csv** contiene información detallada de la tarifa que se paga en cada carrera, como el tipo de pago, el importe de la tarifa, los suplementos e impuestos, las propinas y los peajes, y el importe total pagado. Estos son algunos registros de ejemplo:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La **clave única** para unir trip\_data y trip\_fare se compone de los tres campos siguientes:

- medallion, 
- hack\_license y 
- pickup\_datetime.

## <a name="mltasks"></a>Realicemos tres tipos de tareas de predicción 

Se formulan tres problemas de predicción basados en el valor *tip\_amount* para mostrar tres tipos de tareas de modelado:

1. **Clasificación binaria**: para predecir si se pagó o no una propina tras una carrera, es decir, un valor de *tip\_amount* mayor que 0 USD es un ejemplo positivo, mientras que un valor de *tip\_amount* de 0 USD es un ejemplo negativo.

2. **Clasificación con múltiples clases**: para predecir el intervalo de la propina de la carrera. Dividimos *tip\_amount* en cinco ubicaciones o clases:

		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **Tarea de regresión**: para predecir la cantidad de propina pagada en una carrera.


## <a name="setup"></a>Configuración del entorno de ciencia de datos de Azure para análisis avanzado

Para configurar el entorno de ciencia de datos de Azure, siga estos pasos.

**Cree su propia cuenta de Almacenamiento de blobs de Azure.**

- Cuando aprovisiona su propio Almacenamiento de blobs de Azure, elija una ubicación geográfica para el Almacenamiento de blobs de Azure en la región **centro-sur de EE. UU.**, o lo más cerca posible de esta región, ya que es donde se almacenan los datos de NYC Taxi. Los datos se copian con AzCopy desde el contenedor de Almacenamiento de blobs público a un contenedor de su propia cuenta de almacenamiento. Cuanto más se acerque el Almacenamiento de blobs de Azure a la región centro-sur de EE. UU., más rápida se completará esta tarea (paso 4). 
- Para crear su propia cuenta de almacenamiento de Azure, siga los pasos descritos en [Acerca de las cuentas de almacenamiento de Azure](storage-create-storage-account.md). Asegúrese de hacer anotaciones en los valores de las credenciales de la cuenta de almacenamiento siguientes, que necesitará más adelante en este tutorial. 

  - **Nombre de cuenta de almacenamiento**
  - **Clave de cuenta de almacenamiento**
  - **Nombre de contenedor** (en donde los datos se van a almacenar en el Almacenamiento de blobs de Azure)

**Aprovisione la instancia de Almacenamiento de datos SQL de Azure.** Siga la documentación en [Creación de Almacenamiento de datos SQL](sql-data-warehouse-get-started-provision.md) para aprovisionar una instancia de Almacenamiento de datos SQL. Asegúrese de que hacer anotaciones en las credenciales de Almacenamiento de datos SQL siguientes que se usarán en los pasos posteriores.
 
  - **Nombre del servidor**: <server Name>.database.windows.net
  - **Nombre de SQLDW (base de datos)** 
  - **Nombre de usuario**
  - **Password**

**Instale Visual Studio 2015 y SQL Server Data Tools.** Para obtener instrucciones, vea [Instalación de Visual Studio 2015 y SSDT para Almacenamiento de datos SQL](sql-data-warehouse-install-visual-studio.md).

**Conéctese a Almacenamiento de datos SQL de Azure con Visual Studio.** Para obtener instrucciones, vea los pasos 1 y 2 de [Conexión a Almacenamiento de datos SQL con Visual Studio](sql-data-warehouse-get-started-connect.md).

>[AZURE.NOTE] Ejecute la siguiente consulta SQL en la base de datos que creó en el Almacenamiento de datos SQL (en lugar de la consulta proporcionada en el paso 3 del tema sobre la conexión) para **crear una clave maestra**.

	BEGIN TRY
	       --Try to create the master key
	    CREATE MASTER KEY
	END TRY
	BEGIN CATCH
	       --If the master key exists, do nothing
	END CATCH;

**Cree un área de trabajo de Aprendizaje automático de Azure en su suscripción de Azure.** Para obtener instrucciones, vea [Creación de un área de trabajo de Aprendizaje automático de Azure](machine-learning-create-workspace.md).

## <a name="getdata"></a>Carga de datos en Almacenamiento de datos SQL

Abra una consola de comandos de Windows PowerShell. Ejecute los comandos PowerShell siguientes para descargar los archivos de script SQL de ejemplo que compartimos en Github a un directorio local especificado con el parámetro *-DestDir*. Puede cambiar el valor del parámetro *-DestDir* en cualquier directorio local. Si *-DestDir* no existe, lo creará el script de PowerShell.

>[AZURE.NOTE] Es posible que necesite **ejecutar como administrador** el siguiente script de PowerShell si su directorio *DestDir* necesita privilegios de administrador para crearlo o escribir en él.

	$source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
	$ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
	$wc = New-Object System.Net.WebClient
	$wc.DownloadFile($source, $ps1_dest) 
	.\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Cuando se haya ejecutado correctamente, el directorio de trabajo actual cambia a *-DestDir*. Debe ver una pantalla similar a la siguiente:

![][19]

En *-DestDir*, ejecute el siguiente script de PowerShell en modo de administrador:

	./SQLDW_Data_Import.ps1

Cuando se ejecuta el script de PowerShell por primera vez, se le pedirá que introduzca información desde Almacenamiento de datos SQL de Azure y la cuenta de Almacenamiento de blobs de Azure. Cuando se complete este script de PowerShell al ejecutarse por primera vez, las credenciales indicadas se habrán escrito en el archivo de configuración SQLDW.conf en el directorio de trabajo actual. La ejecución futura de este archivo de script de PowerShell puede leer todos los parámetros necesarios de este archivo de configuración. Si necesita cambiar algunos parámetros, puede elegir escribir los parámetros en la pantalla después del aviso mediante la eliminación de este archivo de configuración y de escribir los valores de parámetros cuando se le solicite o cambiar los valores de parámetros mediante la edición del archivo SQLDW.conf en el directorio *-DestDir*.

>[AZURE.NOTE] Para evitar conflictos de nombres de esquema con los que ya existen en Almacenamiento de datos SQL de Azure, al leer los parámetros directamente desde el archivo SQLDW.conf, se agrega un número aleatorio de tres dígitos al nombre del esquema desde el archivo SQLDW.conf como el nombre de esquema predeterminado para cada ejecución. El script de PowerShell puede pedirle un nombre de esquema: se puede especificar el nombre a discreción del usuario.

Este archivo de **script de PowerShell** realiza las tareas siguientes:

- **Descarga e instala AzCopy**, si AzCopy no se ha instalado aún

		$AzCopy_path = SearchAzCopy
    	if ($AzCopy_path -eq $null){
       		Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
        	InstallAzCopy
        	$AzCopy_path = SearchAzCopy
    	}
			$env_path = $env:Path
			for ($i=0; $i -lt $AzCopy_path.count; $i++){
				if ($AzCopy_path.count -eq 1){
					$AzCopy_path_i = $AzCopy_path
				} else {
					$AzCopy_path_i = $AzCopy_path[$i]
				}
				if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
					Write-Host $AzCopy_path_i 'not in system path, add it...'
					[Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
					$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") 
					$env_path = $env:Path
				}	

- **Copia datos en la cuenta de Almacenamiento de blobs privada** desde el blob público con AzCopy

		Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"	
		$start_time = Get-Date
		AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
		$end_time = Get-Date
    	$time_span = $end_time - $start_time
    	$total_seconds = [math]::Round($time_span.TotalSeconds,2)
    	Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
    	Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"


- **Carga datos mediante Polybase (mediante la ejecución de LoadDataToSQLDW.sql) para el Almacenamiento de datos SQL de Azure** de la cuenta de almacenamiento de blobs privada con los siguientes comandos.
	
	- Creación de un esquema

			EXEC (''CREATE SCHEMA {schemaname};'');

	- Creación de una credencial con ámbito de base de datos
			
			CREATE DATABASE SCOPED CREDENTIAL {KeyAlias} 
			WITH IDENTITY = ''asbkey'' , 
			Secret = ''{StorageAccountKey}''

	- Creación de un origen de datos externo para un blob de Almacenamiento de Azure

			CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage} 
			WITH
			(
    			TYPE = HADOOP,
    			LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
    			CREDENTIAL = {KeyAlias}
			)
			;

			CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage} 
			WITH
			(
    			TYPE = HADOOP,
    			LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
    			CREDENTIAL = {KeyAlias}
			)
			;

	- Cree un formato de archivo externo para un archivo .csv. Los datos se descomprimen y los campos se separan con el carácter de barra vertical.

			CREATE EXTERNAL FILE FORMAT {csv_file_format} 
			WITH 
			(   
    			FORMAT_TYPE = DELIMITEDTEXT, 
    			FORMAT_OPTIONS  
    			(
        			FIELD_TERMINATOR ='','',
        			USE_TYPE_DEFAULT = TRUE
    			)
			)
			;
		
	- Cree tablas externas de tarifas y propinas para el conjunto de datos de NYC Taxi en el Almacenamiento de blobs de Azure.

			CREATE EXTERNAL TABLE {external_nyctaxi_fare}
			(
				medallion varchar(50) not null,
				hack_license varchar(50) not null,
				vendor_id char(3),
				pickup_datetime datetime not null,
				payment_type char(3),
				fare_amount float,
				surcharge float,
				mta_tax float,
				tip_amount float,
				tolls_amount float,
				total_amount float
			)
			with (
    			LOCATION    = ''/nyctaxifare/'',
    			DATA_SOURCE = {nyctaxi_fare_storage},
    			FILE_FORMAT = {csv_file_format},
				REJECT_TYPE = VALUE,
				REJECT_VALUE = 12     
			)  


			CREATE EXTERNAL TABLE {external_nyctaxi_trip}
			(
       			medallion varchar(50) not null,
       			hack_license varchar(50)  not null,
       			vendor_id char(3),
       			rate_code char(3),
       			store_and_fwd_flag char(3),
       			pickup_datetime datetime  not null,
       			dropoff_datetime datetime, 
       			passenger_count int,
       			trip_time_in_secs bigint,
       			trip_distance float,
       			pickup_longitude varchar(30),
       			pickup_latitude varchar(30),
       			dropoff_longitude varchar(30),
       			dropoff_latitude varchar(30)
			)
			with (
    			LOCATION    = ''/nyctaxitrip/'',
    			DATA_SOURCE = {nyctaxi_trip_storage},
    			FILE_FORMAT = {csv_file_format},
    			REJECT_TYPE = VALUE,
				REJECT_VALUE = 12         
			)

	- Carga de datos de tablas externas del Almacenamiento de blobs de Azure en Almacenamiento de datos SQL

			CREATE TABLE {schemaname}.{nyctaxi_fare}
			WITH 
			(   
    			CLUSTERED COLUMNSTORE INDEX,
				DISTRIBUTION = HASH(medallion)
			)
			AS 
			SELECT * 
			FROM   {external_nyctaxi_fare}
			;

			CREATE TABLE {schemaname}.{nyctaxi_trip}
			WITH 
			(   
    			CLUSTERED COLUMNSTORE INDEX,
				DISTRIBUTION = HASH(medallion)
			)
			AS 
			SELECT * 
			FROM   {external_nyctaxi_trip}
			;

	- Cree una tabla de datos de ejemplo (NYCTaxi\_Sample) e inserte datos en ella mediante la selección de consultas SQL en las tablas de carreras y tarifas. (Algunos pasos de este tutorial necesitan usar esta tabla de ejemplo).

			CREATE TABLE {schemaname}.{nyctaxi_sample}
			WITH 
			(   
    			CLUSTERED COLUMNSTORE INDEX,
				DISTRIBUTION = HASH(medallion)
			)
			AS 
			(
	    		SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
				tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
				tip_class = CASE 
						WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
	    		FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
    			WHERE datepart("mi",t.pickup_datetime) = 1
				AND t.medallion = f.medallion
    			AND   t.hack_license = f.hack_license
    			AND   t.pickup_datetime = f.pickup_datetime
    			AND   pickup_longitude <> ''0''
        		AND   dropoff_longitude <> ''0''
			)
			;

>[AZURE.NOTE] Según la ubicación geográfica de la cuenta de almacenamiento de blobs privada, el proceso de copiar datos de un blob público a su cuenta de almacenamiento privada puede tardar unos 15 minutos o incluso más tiempo, y el proceso de carga de datos desde la cuenta de almacenamiento al Almacenamiento de datos SQL de Azure podría tardar 20 minutos o más tiempo.

>[AZURE.NOTE] Si los archivos .csv que se van a copiar desde el almacenamiento de blobs público a la cuenta de almacenamiento de blobs privada ya existen en la cuenta de almacenamiento de blobs privada, AzCopy le preguntará si desea sobrescribirlos. Si no desea sobrescribirlos, escriba **n** cuando se le solicite. Si desea sobrescribir **todos** ellos, escriba **a** cuando se le solicite. También puede escribir **y** para sobrescribir los archivos .csv individualmente.

![Diagrama 21][21]

>[AZURE.TIP] **Use sus propios datos:** si los datos están en la máquina local en la aplicación de la vida real, todavía puede usar AzCopy para cargar los datos locales al Almacenamiento de blobs de Azure privado. Solo tiene que cambiar la ubicación de **origen**, `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, en el comando de AzCopy del archivo de scripts de PowerShell por un directorio local que contiene los datos.
	
>[AZURE.TIP] Si los datos ya están en el Almacenamiento de blobs de Azure privado en la aplicación de la vida real, puede omitir el paso de AzCopy en el script de PowerShell y cargar directamente los datos en Almacenamiento de datos SQL de Azure. Esto requerirá modificaciones adicionales del script para adaptarlo al formato de los datos.


Este script de Powershell también conecta la información de Almacenamiento de datos SQL de Azure en los archivos de ejemplo de exploración de datos SQLDW\_Explorations.sql, SQLDW\_Explorations.ipynb y SQLDW\_Explorations\_Scripts.py para que estos tres archivos están listos para ser probados de inmediato cuando finalice el script de PowerShell.

Después de una ejecución correcta, verá una pantalla similar a la siguiente:

![][20]

## <a name="dbexplore"></a>Exploración de datos y diseño de características en Almacenamiento de datos SQL de Azure

En esta sección, realizamos la generación de características y la exploración de datos mediante la ejecución de consultas SQL en Almacenamiento de datos SQL de Azure directamente mediante **Visual Studio Data Tools**. Todas las consultas SQL que se usan en esta sección se pueden encontrar en el script de ejemplo llamado *SQLDW\_Explorations.sql*. Este archivo ya lo ha descargado en el directorio local el script de PowerShell. También puede recuperarlo en [Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Pero el archivo de Github no tiene la información de Almacenamiento de datos SQL de Azure conectada.

Conéctese con Almacenamiento de datos SQL de Azure utilizando Visual Studio con el nombre de inicio de sesión de Almacenamiento de datos SQL y la contraseña y abra el **Explorador de objetos SQL** para confirmar que la base de datos y las tablas se han importado. Recupere el archivo *SQLDW\_Explorations.sql*.

>[AZURE.NOTE] Para abrir un editor de consultas de Almacenamiento de datos paralelos (PDW), utilice el comando **Nueva consulta** mientras el PDW está seleccionado en el **Explorador de objetos SQL**. El editor de consultas estándar de SQL no es compatible con PDW.

A continuación se muestra el tipo de tareas de exploración de datos y de generación de características realizado en esta sección:

- Explorar distribuciones de datos de algunos campos en diferentes ventanas de tiempo.
- Investigar la calidad de los datos de los campos de longitud y latitud.
- Generar etiquetas de clasificación binaria y multiclase según **tip\\_amount**.
- Generar características y calcular o comparar distancias de carreras.
- Combinar las dos tablas y extraer una muestra aleatoria que se usará para generar modelos.

### Comprobación de la importación de datos

Estas consultas proporcionan una comprobación rápida del número de filas y columnas en las tablas que se rellenaron anteriormente mediante la importación masiva paralela de Polybase.

	-- Report number of rows in table <nyctaxi_trip> without table scan
	SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

	-- Report number of columns in table <nyctaxi_trip>
	SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Salida:** debe obtener 173 179 759 filas y 14 columnas.

### Exploración: distribución de carreras por licencia

Esta consulta de ejemplo identifica las licencias (números de taxi) que han completado más de 100 carreras dentro de un período de tiempo especificado. La consulta se beneficiaría del acceso de la tabla con particiones, ya que está condicionada por el esquema de partición de **pickup\_datetime**. La consulta el conjunto de datos completo también hará uso de la tabla con particiones o del recorrido de índice.

	SELECT medallion, COUNT(*)
	FROM <schemaname>.<nyctaxi_fare>
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	GROUP BY medallion
	HAVING COUNT(*) > 100

**Salida:** la consulta debe devolver una tabla con filas en las que se especifiquen las 13 369 licencias (taxis) y el número de carreras completadas por ellos en 2013. La última columna contiene el recuento del número de carreras realizadas.

### Exploración: distribución de carreras por medallion y hack\_license

Este ejemplo identifica las licencias (números de taxi) y los números de hack\_license (conductores) que han completado más de 100 carreras dentro de un período de tiempo.

	SELECT medallion, hack_license, COUNT(*)
	FROM <schemaname>.<nyctaxi_fare>
	WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
	GROUP BY medallion, hack_license
	HAVING COUNT(*) > 100

**Salida:** la consulta debe devolver una tabla con 13 369 filas en las que se especifiquen los 13 369 identificadores de vehículo/conductor que han realizado más de 100 carreras en 2013. La última columna contiene el recuento del número de carreras realizadas.

### Evaluación de la calidad de los datos: comprobar los registros con longitud o latitud incorrectas

En este ejemplo se investiga si alguno de los campos de longitud y latitud contiene un valor no válido (los grados radianes deben encontrarse entre -90 y 90) o tienen coordenadas (0, 0).

	SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Salida:** la consulta devuelve 837 467 carreras con campos de latitud o longitud no válidos.

### Exploración: distribución de carreras con propinas frente a sin propinas

Este ejemplo busca el número de carreras en las que se han dado propinas frente a aquellas en las que no se han dado en un período de tiempo especificado (o en el conjunto de datos completo si se abarca todo el año, como se establece aquí). Esta distribución refleja la distribución de etiquetas binarias que se usará más adelante para el modelado de clasificación binaria.

	SELECT tipped, COUNT(*) AS tip_freq FROM (
	  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
	  FROM <schemaname>.<nyctaxi_fare>
	  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tipped

**Salida**: la consulta debe devolver las siguientes frecuencias de propinas para el año 2013: 90 447 622 con propina y 82 264 709 sin propina.

### Exploración: distribución por intervalos y clases de propinas

Este ejemplo calcula la distribución de los intervalos de propinas de un período de tiempo determinado (o en el conjunto de datos completo si abarca todo el año). Esta es la distribución de las clases de etiquetas que se usarán posteriormente para el modelado de clasificación multiclase.

	SELECT tip_class, COUNT(*) AS tip_freq FROM (
		SELECT CASE
			WHEN (tip_amount = 0) THEN 0
			WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
			WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
			WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
			ELSE 4
		END AS tip_class
	FROM <schemaname>.<nyctaxi_fare>
	WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tip_class

**Salida:**

|tip\_class | tip\_freq |
| --------- | -------|
|1 | 82 230 915 |
|2 | 6 198 803 |
|3 | 1 932 223 |
|0 | 82 264 625 |
|4 | 85 765 |

### Exploración: proceso y comparación de la distancia de la carrera

En este ejemplo se convierte la longitud y latitud de los puntos de recogida y destino a puntos geográficos de SQL, se calcula la distancia de la carrera mediante la diferencia de puntos geográficos de SQL y se devuelve una muestra aleatoria de los resultados de la comparación. En el ejemplo se limitan los resultados a coordenadas válidas usando solo la consulta de evaluación de calidad de datos tratada anteriormente.

	/****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
	SET ANSI_NULLS ON
	GO

	SET QUOTED_IDENTIFIER ON
	GO

	IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
	  DROP FUNCTION fnCalculateDistance
	GO

	-- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
	CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)
	
	RETURNS float
	AS
	BEGIN
	  	DECLARE @distance decimal(28, 10)
  		-- Convert to radians
  		SET @Lat1 = @Lat1 / 57.2958
  		SET @Long1 = @Long1 / 57.2958
  		SET @Lat2 = @Lat2 / 57.2958
  		SET @Long2 = @Long2 / 57.2958
  		-- Calculate distance
  		SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
  		--Convert to miles
  		IF @distance <> 0
  		BEGIN
    		SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
  		END
  		RETURN @distance
	END
	GO

	SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude, 
	dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
	FROM <schemaname>.<nyctaxi_trip>
	WHERE datepart("mi",pickup_datetime)=1
	AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
	AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
	AND pickup_longitude != '0' AND dropoff_longitude != '0'

### Diseño de características con las funciones de SQL

A veces, las funciones de SQL pueden ser una opción eficiente de diseño de características. En este tutorial, hemos definido una función SQL para calcular la distancia directa entre las ubicaciones de recogida y entrega. Puede ejecutar los scripts SQL siguientes en **Visual Studio Data Tools**.

Este es el script SQL que define la función de distancia.

	SET ANSI_NULLS ON
	GO

	SET QUOTED_IDENTIFIER ON
	GO

	IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
	  DROP FUNCTION fnCalculateDistance
	GO

	-- User-defined function calculate the direct distance between two geographical coordinates.
	CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)
	
	RETURNS float
	AS
	BEGIN
	  	DECLARE @distance decimal(28, 10)
  		-- Convert to radians
  		SET @Lat1 = @Lat1 / 57.2958
  		SET @Long1 = @Long1 / 57.2958
  		SET @Lat2 = @Lat2 / 57.2958
  		SET @Long2 = @Long2 / 57.2958
  		-- Calculate distance
  		SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
  		--Convert to miles
  		IF @distance <> 0
  		BEGIN
    		SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
  		END
  		RETURN @distance
	END
	GO 

Este es un ejemplo para llamar a esta función para generar características en la consulta SQL:

	-- Sample query to call the function to create features
	SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude, 
	dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
	FROM <schemaname>.<nyctaxi_trip>
	WHERE datepart("mi",pickup_datetime)=1
	AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
	AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
	AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Salida:** esta consulta genera una tabla (con 2 803 538 filas) con las latitudes y longitud de los puntos de origen y destino y las distancias directas correspondientes en millas. Estos son los resultados de las tres primeras filas:

|pickup\_latitude | pickup\_longitude | dropoff\_latitude |dropoff\_longitude | DirectDistance |
|---| --------- | -------|-------| --------- | -------|
|1 | 40,731804 | -74,001083 | 40,736622 | -73,988953 | 0,7169601222 |
|2 | 40,715794 | -74,010635 | 40,725338 | -74,00399 | 0,7448343721 |
|3 | 40,761456 | -73,999886 | 40,766544 | -73,988228 | 0,7037227967 |



### Preparar datos para la generación de modelos

La siguiente consulta combina las tablas **nyctaxi\_trip** y **nyctaxi\_fare**, genera una etiqueta de clasificación binaria **tipped**, una etiqueta de clasificación multiclase **tip\\_class** y extrae una muestra del conjunto de datos combinado completo. El muestreo se realiza mediante la recuperación de un subconjunto de los viajes en función del tiempo de recogida. Esta consulta se puede copiar y pegar directamente en el módulo [Lector](https://studio.azureml.net) del [Estudio de aprendizaje automático de Azure][reader] para la ingesta directa de datos de la instancia de Base de datos SQL en Azure. La consulta excluye los registros con coordenadas (0, 0) incorrectas.

	SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, 	f.total_amount, f.tip_amount,
	    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
	    CASE WHEN (tip_amount = 0) THEN 0
	        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
	        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
	        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
	        ELSE 4
	    END AS tip_class
	FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
	WHERE datepart("mi",t.pickup_datetime) = 1
	AND   t.medallion = f.medallion
	AND   t.hack_license = f.hack_license
	AND   t.pickup_datetime = f.pickup_datetime
	AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Cuando esté listo para continuar con Aprendizaje automático de Azure, puede:

1. Guardar la consulta SQL final para extraer y muestrear los datos, y copiar y pegar la consulta directamente en un módulo [Lector][reader] de Aprendizaje automático de Azure; o bien
2. Conservar los datos muestreados y de ingeniería que planea usar para la generación de modelos de una nueva tabla de Almacenamiento de datos SQL y usar la nueva tabla en el módulo [Lector][reader] de Aprendizaje automático de Azure. El script de PowerShell en el paso anterior se ha encargado de hacerlo. Puede leer directamente desde esta tabla en el módulo Lector. 


## <a name="ipnb"></a>Exploración de datos e ingeniería de características en IPython Notebook

En esta sección, se llevará a cabo la exploración de datos y la generación de características con consultas Python y SQL en el Almacenamiento de datos SQL creado anteriormente. Se han descargado un cuaderno de IPython Notebook de ejemplo denominado **SQLDW\_Explorations.ipynb** y un archivo de script de Python **SQLDW\_Explorations\_Scripts.py** en el directorio local. También están disponibles en [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Estos dos archivos son idénticos en los scripts de Python. El archivo de script de Python se proporciona en caso de que no tenga un servidor de IPython Notebook. Estos dos archivos de Python de muestra están diseñados en **Python 2.7**.

La información necesaria de Almacenamiento de datos SQL de Azure en el cuaderno de IPython Notebook de ejemplo y el archivo de script de Python descargados en el equipo local está conectada por el script PowerShell. Son ejecutables sin ninguna modificación.

Si ya ha configurado un área de trabajo de Aprendizaje automático de Azure, puede cargar el cuaderno de IPython Notebook de ejemplo en el servicio IPython Notebook de Aprendizaje automático de Azure y comenzar a ejecutarlo. Estos son los pasos para cargar en el servicio IPython Notebook de Aprendizaje automático de Azure:

1. Inicie sesión en el área de trabajo de Aprendizaje automático de Azure, haga clic en "Studio" en la parte superior y después en "CUADERNOS" en el lado izquierdo de la página web. 

	![Diagrama 22][22]

2. Haga clic en "NUEVO" en la esquina inferior izquierda de la página web y seleccione "Python 2". A continuación, proporcione un nombre para el cuaderno y haga clic en la marca de verificación para crear el nuevo cuaderno de IPython Notebook en blanco.

	![Diagrama 23][23]

3. Haga clic en el símbolo "Jupyter" en la esquina superior izquierda del nuevo cuaderno de IPython Notebook.

	![Diagrama 24][24]

4. Arrastre y coloque el cuaderno de IPython Notebook de ejemplo en la página **árbol** del servicio IPython Notebook de Aprendizaje automático de Azure y haga clic en **Cargar**. A continuación, se cargará el cuaderno de IPython Notebook de ejemplo en el servicio IPython Notebook de Aprendizaje automático de Azure.

	![Diagrama 25][25]

Para ejecutar el cuaderno de IPython Notebook de ejemplo o el archivo de script de Python, son necesarios los siguientes paquetes Python. Si usa el servicio IPython Notebook de Aprendizaje automático de Azure, estos paquetes ya están preinstalados.

	- pandas
	- numpy
	- matplotlib
	- pyodbc
	- PyTables

La secuencia recomendada al crear soluciones analíticas avanzadas en Aprendizaje automático de Azure con gran cantidad de datos es la siguiente:

- Leer una pequeña muestra de los datos en una trama de datos en memoria.
- Realizar algunas visualizaciones y exploraciones con los datos de muestreo.
- Experimentar con el diseño de características con los datos de muestreo.
- Para el diseño de características, la exploración y la manipulación de datos más grandes, usar Python para emitir consultas SQL directamente en Almacenamiento de datos SQL.
- Decidir el tamaño de muestra que se usará para la creación del modelo de Aprendizaje automático de Azure.

A continuación, se muestran algunas exploraciones de datos, visualizaciones de datos y ejemplos de diseño de características. Se pueden encontrar más exploraciones de datos en el cuaderno de IPython Notebook de ejemplo y en el archivo de script de Python de ejemplo.

### Inicialización de las credenciales de la base de datos

Inicialice la configuración de conexión de base de datos en las variables siguientes:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### Creación de conexiones de base de datos

Esta es la cadena de conexión que crea la conexión a la base de datos.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### Notificación del número de filas y columnas de la tabla <nyctaxi_trip>

    nrows = pd.read_sql('''
		SELECT SUM(rows) FROM sys.partitions
		WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
	''', conn)

	print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
		SELECT COUNT(*) FROM information_schema.columns
		WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
	''', conn)

	print 'Total number of columns = %d' % ncols.iloc[0,0]

- Número total de filas = 173179759  
- Número total de columnas = 14

### Notificación del número de filas y columnas de la tabla <nyctaxi_fare>

    nrows = pd.read_sql('''
		SELECT SUM(rows) FROM sys.partitions
		WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
	''', conn)

	print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
		SELECT COUNT(*) FROM information_schema.columns
		WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
	''', conn)

	print 'Total number of columns = %d' % ncols.iloc[0,0]

- Número total de filas = 173179759  
- Número total de columnas = 11

### Lectura de una muestra de datos pequeña de la base de datos de Almacenamiento de datos SQL

    t0 = time.time()

	query = '''
		SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
			f.tolls_amount, f.total_amount, f.tip_amount
		FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
		WHERE datepart("mi",t.pickup_datetime) = 1
		AND   t.medallion = f.medallion
		AND   t.hack_license = f.hack_license
		AND   t.pickup_datetime = f.pickup_datetime
	'''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

El tiempo empleado en leer la tabla de ejemplo es 14,096495 segundos Número de filas y columnas recuperadas = (1000, 21)

### Estadísticas descriptivas

Ya puede explorar los datos de muestreo. Comenzamos echando un vistazo a algunas estadísticas descriptivas del campo **trip\_distance** (o de cualquier otro que elija).

    df1['trip_distance'].describe()

### Visualización: ejemplo de diagrama de caja

A continuación, observaremos el diagrama de caja de la distancia de la carrera para ver los cuantiles.

    df1.boxplot(column='trip_distance',return_type='dict')

![Diagrama 1][1]

### Visualización: ejemplo de diagrama de distribución

Diagramas que visualizan la distribución y un histograma de las distancias de las carreras muestreadas.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Diagrama 2][2]

### Visualización: diagramas de barras y líneas

En este ejemplo, se discretiza la distancia de la carrera en cinco discretizaciones y se visualizan los resultados de la discretización.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Podemos trazar la distribución de discretización anterior en un gráfico de barras o líneas con:

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Diagrama 3][3]

y

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Diagrama 4][4]

### Visualización: ejemplos de gráfico de dispersión

Se muestra el gráfico de dispersión entre **trip\_time\_in\_secs** y **trip\_distance** para ver si existe algún tipo de correlación

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Diagrama 6][6]

También podemos comprobar la relación entre **rate\_code** y **trip\_distance**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Diagrama 8][8]


### Exploración de datos en datos de muestreo mediante consultas SQL en IPython Notebook

En esta sección, se explorarán las distribuciones de datos con los datos de muestreo que se conservan en la nueva tabla que se creó anteriormente. Tenga en cuenta que se pueden realizar exploraciones similares con las tablas originales.

#### Exploración: notificación del número de filas y columnas de la tabla de muestreo

	nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
	print 'Number of rows in sample = %d' % nrows.iloc[0,0]

	ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
	print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### Exploración: distribución con propinas y sin propinas

	query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

	pd.read_sql(query, conn)

#### Exploración: distribución de clases de propinas

	query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
	'''

	tip_class_dist = pd.read_sql(query, conn)

#### Exploración: trazado de la distribución de propinas por clase

	tip_class_dist['tip_freq'].plot(kind='bar')

![Diagrama 26][26]


#### Exploración: distribución diaria de carreras

    query = '''
		SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
		FROM <schemaname>.<nyctaxi_sample>
		GROUP BY CONVERT(date, dropoff_datetime)
	'''

    pd.read_sql(query,conn)

#### Exploración: distribución de carreras por licencia

    query = '''
		SELECT medallion,count(*) AS c
		FROM <schemaname>.<nyctaxi_sample>
		GROUP BY medallion
	'''

	pd.read_sql(query,conn)

#### Exploración: distribución de carreras por licencia y hack\_license

	query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
	pd.read_sql(query,conn)


#### Exploración: distribución del tiempo de la carrera

	query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
	pd.read_sql(query,conn)

#### Exploración: distribución de la distancia de la carrera

	query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
	pd.read_sql(query,conn)

#### Exploración: distribución del tipo de pago

	query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
	pd.read_sql(query,conn)

#### Comprobar el formulario final de la tabla con características

    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Creación de modelos en Aprendizaje automático de Azure

Ya está todo listo para pasar a la creación del modelo y la implementación del mismo en [Aprendizaje automático de Azure](https://studio.azureml.net). Los datos están listos para usarse en cualquiera de los problemas de predicción identificados anteriormente, a saber:

1. **Clasificación binaria**: para predecir si se dio propina en una carrera, o no.

2. **Clasificación multiclase**: para predecir el intervalo de la propina dada, según las clases definidas anteriormente.

3. **Tarea de regresión**: para predecir la cantidad de propina pagada en una carrera.



Para iniciar el ejercicio de modelado, inicie sesión en el área de trabajo de **Aprendizaje automático de Azure**. Si aún no ha creado un área de trabajo de aprendizaje automático, consulte [Creación de un área de trabajo de Aprendizaje automático de Azure](machine-learning-create-workspace.md).

1. Para empezar a usar el Aprendizaje automático de Azure, consulte [¿Qué es Estudio de aprendizaje automático de Microsoft Azure?](machine-learning-what-is-ml-studio.md)

2. Inicie sesión en [Estudio de aprendizaje automático de Azure](https://studio.azureml.net).

3. La página principal del Estudio ofrece una gran cantidad de información, vídeos, tutoriales, vínculos a referencias de módulos y otros recursos. Para más información sobre Aprendizaje automático de Azure, vea el [Centro de documentación de aprendizaje automático de Azure](https://azure.microsoft.com/documentation/services/machine-learning/).

Un experimento de entrenamiento típico consta de los pasos siguientes:

1. Crear un experimento **+NUEVO**.
2. Proporcionar los datos a Aprendizaje automático de Azure.
3. Preprocesar, transformar y manipular los datos según sea necesario.
4. Generar características según sea necesario.
5. Dividir los datos en conjuntos de datos de entrenamiento, validación y pruebas (o disponer de conjuntos de datos independientes para cada uno).
6. Seleccionar uno o varios algoritmos de aprendizaje automático, según el problema de aprendizaje que se quiera resolver. Por ejemplo: clasificación binaria, clasificación multiclase, regresión.
7. Entrenar uno o varios modelos utilizando el conjunto de datos de entrenamiento.
8. Puntuar el conjunto de datos de validación con los modelos entrenados.
9. Evaluar los modelos para calcular las métricas relevantes para el problema de aprendizaje.
10. Ajustar los modelos y seleccionar el mejor para su implementación.

En este ejercicio, ya se han explorado y diseñado los datos en Almacenamiento de datos SQL, y también se ha decidido el tamaño de la muestra para la ingesta en Aprendizaje automático de Azure. Este es el procedimiento para crear uno o varios de los modelos de predicción:

1. Proporcionar los datos a Aprendizaje automático de Azure con el módulo [Lector][reader], disponible en la sección **Entrada y salida de datos**. Para obtener más información, consulte la página de referencia del módulo [Lector][reader].

	![Lector de Aprendizaje automático de Azure][17]

2. Seleccionar **Base de datos SQL de Azure** como **Origen de datos** en el panel **Propiedades**.

3. Escribir el nombre DNS de la base de datos en el campo **Nombre del servidor de la base de datos**. Formato: `tcp:<your_virtual_machine_DNS_name>,1433`

4. Escribir el **nombre de la base de datos** en el campo correspondiente.

5. Escribir el *nombre de usuario de SQL* en **Nombre de la cuenta de usuario del servidor** y la *contraseña* en **Contraseña de la cuenta de usuario del servidor**.

6. Activar la opción **Aceptar cualquier certificado de servidor**.

7. En el área de texto editable **Consulta de base de datos**, pegar la consulta que extrae los campos de la base de datos necesarios (incluidos los campos calculados, como las etiquetas) y reducir la muestra al tamaño de muestra deseado.

En la ilustración siguiente se muestra un ejemplo de un experimento de clasificación binaria que lee datos directamente desde la base de datos de Almacenamiento de datos SQL (no olvide reemplazar los nombres de tabla nyctaxi\_trip y nyctaxi\_fare por el nombre de esquema y los nombres de tabla que utilizó en el tutorial). Se pueden construir experimentos similares para problemas de clasificación multiclase y de regresión.

![Entrenamiento de Aprendizaje automático de Azure][10]

> [AZURE.IMPORTANT] En los ejemplos de consultas de extracción y muestreo de datos de modelado de las secciones anteriores, **las etiquetas de los tres ejercicios de modelado se incluyen en la consulta**. Un paso importante (requerido) en cada uno de los ejercicios de modelado consiste en **excluir** las etiquetas innecesarias de los otros dos problemas y cualquier otra **fuga de destino**. Por ejemplo, cuando use clasificación binaria, utilice la etiqueta **tipped** y excluya los campos **tip\_class**, **tip\_amount** y **total\_amount**. Estos últimos son fugas de destino ya que implican que se pagó propina.
>
> Para excluir cualquier columna innecesaria o fugas de destino, puede usar el módulo [Proyectar columnas][project-columns] o el [Editor de metadatos][metadata-editor]. Para obtener más información, consulte las páginas de referencia de [Proyectar columnas][project-columns] y [Editor de metadatos][metadata-editor].

## <a name="mldeploy"></a>Implementación de modelos en Aprendizaje automático de Azure

Cuando el modelo esté listo, podrá implementarlo fácilmente como un servicio web directamente desde el experimento. Para obtener más información sobre la implementación de servicios web de Aprendizaje automático de Azure, vea [Implementación de un servicio web de Aprendizaje automático de Azure](machine-learning-publish-a-machine-learning-web-service.md).

Para implementar un nuevo servicio web, deberá:

1. Crear un experimento de puntuación.
2. Implemente el servicio web.

Para crear un experimento de puntuación a partir de un experimento de entrenamiento **Finalizado**, haga clic en **CREAR EXPERIMENTO DE PUNTUACIÓN** en la barra de acciones inferior.

![Puntuación de Azure][18]

Aprendizaje automático de Azure intentará crear un experimento de puntuación en función de los componentes del experimento de entrenamiento. En concreto, hará lo siguiente:

1. Guardar el modelo entrenado y quitar los módulos de entrenamiento del modelo.
2. Identificar un **puerto de entrada** lógico que represente el esquema de datos de entrada esperado.
3. Identificar un **puerto de salida** lógico que represente el esquema de salida del servicio web.

Cuando se crea el experimento de puntuación, revíselo y ajústelo según sea necesario. Un ajuste común consiste en reemplazar la consulta o el conjunto de datos de entrada por uno que excluya los campos de etiqueta, ya que estos no estarán disponibles cuando se llame al servicio. También es una buena práctica reducir el tamaño de la consulta o del conjunto de datos de entrada a unos pocos registros, los necesarios para indicar el esquema de entrada. En el caso del puerto de salida, es común excluir todos los campos de entrada e incluir solo las **Etiquetas puntuadas** y las **Probabilidades puntuadas** en la salida mediante el módulo [Proyectar columnas][project-columns].

En la ilustración siguiente se muestra un ejemplo de experimento de puntuación. Cuando todo esté listo para implementar, haga clic en el botón **PUBLICAR SERVICIO WEB** de la barra de acciones inferior.

![Publicación de Aprendizaje automático de Azure][11]


## Resumen
Para recapitular lo que hemos hecho, en este tutorial paso a paso se ha creado un entorno de ciencia de datos de Azure, se ha trabajado con un conjunto grande de datos público de principio a fin, llevándolos a través del proceso de Cortana Analytics, desde la adquisición de los datos al entrenamiento del modelo, y después a la implementación de un servicio web de Aprendizaje automático de Azure.

### Información de licencia

Microsoft comparte este tutorial de ejemplo y sus scripts adjuntos y Blocs de notas de IPython bajo la licencia MIT. Consulte el archivo LICENSE.txt que se encuentra en el directorio del código de ejemplo en GitHub para obtener más detalles.

## Referencias

• [Página de descarga de NYC Taxi Trips de Andrés Monroy](http://www.andresmh.com/nyctaxitrips/) • [FOILing NYC's Taxi Trip Data de Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/) • [Estadísticas e investigación de la Comisión de taxis y limusinas de la Ciudad de Nueva York](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sqldw-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sqldw-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlscoring.png
[19]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_load_data.png
[21]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/machine-learning-data-science-process-sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0211_2016-->