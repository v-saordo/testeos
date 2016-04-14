<properties
	pageTitle="Proceso de análisis de Cortana en acción: uso de SQL Server | Microsoft Azure"
	description="Tecnología y procesos de análisis avanzado en acción"  
	services="machine-learning"
	documentationCenter=""
	authors="msolhab"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016" 
	ms.author="mohabib;fashah;bradsev"/>


# Proceso de análisis de Cortana en acción: uso de SQL Server

En este tutorial, se describe la creación e implementación de un modelo con un conjunto de datos disponible públicamente, el conjunto de datos [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/). El procedimiento sigue el flujo de trabajo del proceso de análisis de Cortana (CAP).


## <a name="dataset"></a>Descripción del conjunto de datos NYC Taxi Trip

El conjunto de datos NYC Taxi Trips consiste en aproximadamente 20 GB de archivos de valores separados por comas (CSV) comprimidos (aproximadamente, 48 GB sin comprimir), que incluyen más de 173 millones de carreras individuales y las tarifas pagadas por cada carrera. Cada registro de carrera incluye la hora y la ubicación de recogida y de entrega, el número de licencia de (del conductor) anónimo y el número de ida y vuelta incluye la ubicación de entrega y recogida y el tiempo, la número de licencia y el número de identificador único del taxi. Los datos cubren todos los viajes del año 2013 y se proporcionan en los dos conjuntos de datos siguientes para cada mes:

1. El archivo CSV 'trip\_data' contiene información detallada de las carreras, como el número de pasajeros, los puntos de recogida y destino, la duración de las carreras y la longitud del recorrido. Estos son algunos registros de ejemplo:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. El archivo CSV 'trip\_fare' contiene información detallada de la tarifa que se paga en cada carrera, como el tipo de pago, el importe de la tarifa, los suplementos e impuestos, las propinas y peajes, y el importe total pagado. Estos son algunos registros de ejemplo:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La clave única para unir trip\\_data y trip\\_fare se compone de los campos: medallion, hack\\_licence y pickup\\_datetime.

## <a name="mltasks"></a>Ejemplos de tareas de predicción

Se formularán tres problemas de predicción basados en *tip\_amount*, a saber:

1. Clasificación binaria: predecir si se pagó una propina tras una carrera, o no; es decir, un valor de *tip\_amount* mayor que 0 $ es un ejemplo positivo, mientras que un valor de *tip\_amount* de 0 $ es un ejemplo negativo.

2. Clasificación con múltiples clases: para predecir el intervalo de la propina de la carrera. Dividimos *tip\_amount* en cinco ubicaciones o clases:

		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. Tarea de regresión: para predecir la cantidad de propina pagada en una carrera.


## <a name="setup"></a>Configuración del entorno de ciencia de datos de Azure para análisis avanzado

Como puede ver en la guía [Planear su entorno de ciencia de datos de aprendizaje automático de Azure](machine-learning-data-science-plan-your-environment.md), existen varias opciones para trabajar con el conjunto de datos NYC Taxi Trips en Azure:

- Trabajar con los datos en blobs de Azure y, a continuación, modelar en Aprendizaje automático de Azure.
- Cargar los datos en una base de datos de SQL Server y, a continuación, modelar en Aprendizaje automático de Azure.

En este tutorial se mostrará la importación paralela en bloque de los datos en SQL Server, la exploración de los datos, el diseño de características y la reducción de la muestra con SQL Server Management Studio, así como con un Bloc de notas de IPython. Los [scripts de muestra](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) y [Blocs de notas de IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) se comparten en GitHub. Hay un Bloc de notas de IPython de ejemplo disponible para trabajar con los datos de blobs de Azure en la misma ubicación.

Para configurar el entorno de ciencia de datos de Azure:

1. [Cree una cuenta de almacenamiento](../storage-create-storage-account.md)

2. [Cree un área de trabajo de Aprendizaje automático de Azure](machine-learning-create-workspace.md)

3. [Aprovisione una máquina virtual de ciencia de datos](machine-learning-data-science-setup-sql-server-virtual-machine.md), que actuará no solo como servidor de SQL Server, sino también como servidor de Blocs de notas de IPython.

	> [AZURE.NOTE] Los scripts y Blocs de notas de IPython de ejemplo se descargarán en la máquina virtual de ciencia de datos durante el proceso de instalación. Cuando se complete el script posterior a la instalación de la máquina virtual, los ejemplos estarán en la biblioteca de documentos de su máquina virtual: - Scripts de ejemplo: `C:\Users<user_name>\Documents\Data Science Scripts` - Blocs de notas de IPython de ejemplo: `C:\Users<user_name>\Documents\IPython Notebooks\DataScienceSamples` donde `<user_name>` es el nombre de inicio de sesión de Windows de la máquina virtual. Se hará referencia a las carpetas de ejemplo como **Scripts de ejemplo** y **Blocs de notas de IPython de ejemplo**.


Teniendo en cuenta el tamaño del conjunto de datos, la ubicación del origen de datos y el entorno destino de Azure seleccionado, este escenario es similar a [Escenario nº 5: Conjunto de datos grande de archivos locales, con SQL Server en una máquina virtual de Azure como destino](../machine-learning-data-science-plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Obtener los datos del origen público

Para obtener el conjunto de datos [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) de su ubicación pública, puede usar cualquiera de los métodos descritos en [Mover datos hacia y desde el almacenamiento de blobs de Azure](machine-learning-data-science-move-azure-blob.md) para copiar los datos en su nueva máquina virtual.

Para copiar los datos mediante AzCopy:

1. Inicie sesión en la máquina virtual (VM)

2. Cree un nuevo directorio en disco de datos de la máquina virtual (Nota: no use el disco temporal que se incluye con la máquina virtual como disco de datos).

3. En una ventana de símbolo del sistema, ejecute la siguiente línea de comandos de Azcopy, reemplazando <path_to_data_folder> por la carpeta de datos que se creó en (2):

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

	Cuando se complete la operación AzCopy, debe haber un total de 24 archivos CSV comprimidos (12 para trip\\_data y 12 para trip\\_fare) en la carpeta de datos.

4. Descomprima los archivos descargados. Observe la carpeta donde se encuentran los archivos sin comprimir. Se hará referencia a esta carpeta como <path\_to\_data\_files>.

## <a name="dbload"></a>Importación masiva de datos en una base de datos de SQL Server

Para mejorar tanto el rendimiento de la carga y transferencia de grandes cantidades de datos a una base de datos SQL como las consultas posteriores, utilice _tablas y vistas con particiones_. En esta sección, se seguirán las instrucciones que se describen en [Importación paralela de conjuntos masivos de datos mediante tablas de partición de SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md) para crear una nueva base de datos y cargar los datos en tablas con particiones en paralelo.

1. Con la sesión iniciada en la máquina virtual, inicie **SQL Server Management Studio**.

2. Conéctese con la autenticación de Windows.

	![Conexión SSMS][12]

3. Si aún no ha cambiado el modo de autenticación de SQL Server ni ha creado un nuevo usuario de inicio de sesión de SQL, abra el archivo de script **change\\_auth.sql** de la carpeta **Scripts de ejemplo**. Cambie el nombre de usuario y la contraseña predeterminados. Haga clic en **!Ejecutar** en la barra de herramientas para ejecutar el script.

	![Ejecutar script][13]

4. Compruebe o cambie las carpetas de registro y las bases de datos de SQL Server predeterminadas para asegurarse de que las bases de datos recién creadas se almacenarán en un disco de datos. La imagen de la máquina virtual de SQL Server que está optimizada para cargas de almacén de datos está configurada previamente con discos de registros y datos. Si la máquina virtual no incluía un disco de datos y agregó nuevos discos duros virtuales durante el proceso de instalación de la máquina virtual, cambie las carpetas predeterminadas de la manera siguiente:

	- Haga clic con el botón derecho en el nombre del servidor SQL Server en el panel izquierdo y haga clic en **Propiedades**.

		![Propiedades de SQL Server][14]

	- Seleccione **Configuración de base de datos** en la lista **Seleccionar una página** de la izquierda.

	- Compruebe o cambie los datos del apartado **Ubicaciones predeterminadas de la base de datos** por las ubicaciones del **disco de datos** que prefiera. Aquí es donde se almacenarán las nuevas bases de datos si se crean con la configuración de ubicación predeterminada.

		![Valores predeterminados de Base de datos SQL][15]

5. Para crear una nueva base de datos y un conjunto de grupos de archivos para almacenar las tablas con particiones, abra el script de ejemplo **create\\_db\\_default.sql**. El script creará una nueva base de datos llamada **TaxiNYC** y doce grupos de archivos en la ubicación de datos predeterminada. Cada uno de estos grupos de archivos contendrá un mes de datos de trip\_data y trip\_fare. Modifique el nombre de la base de datos, si lo desea. Haga clic en **!Ejecutar** para ejecutar el script.

6. A continuación, cree dos tablas con particiones, una para trip\_data y otra para trip\_fare. Abra el script de ejemplo **create\\_partitioned\\_table.sql**, que:

	- Creará una función de partición para dividir los datos por mes.
	- Creará un esquema de partición para asignar los datos de cada mes a un grupo de archivos distinto.
	- Creará dos tablas con particiones asignadas al esquema de partición: **nyctaxi\_trip** contendrá los datos de trip\_data y **nyctaxi\_fare** los de trip\_fare.

	Haga clic en **!Ejecutar** para ejecutar el script y crear las tablas con particiones.

7. En la carpeta **Scripts de ejemplo**, hay dos scripts de PowerShell de ejemplo para mostrar las importaciones en bloque paralelas de datos en tablas de SQL Server.

	- **bcp\\_parallel\\_generic.ps1** es un script genérico para importar datos en bloque y de forma paralela en una tabla. Modifique este script para establecer las variables de entrada y de destino, como se indica en las líneas de comentario del script.
	- **bcp\\_parallel\\_nyctaxi.ps1** es una versión preconfigurada del script genérico y se puede usar para cargar ambas tablas para los datos de NYC Taxi Trips.  

8. Haga clic con el botón derecho en el nombre de script **bcp\\_parallel\\_nyctaxi.ps1** y, a continuación, en **Editar** para abrirlo en PowerShell. Revise las variables preestablecidas y modifíquelas según el nombre de la base de datos seleccionada, la carpeta de datos de entrada, la carpeta de registro de destino y las rutas de acceso a los archivos de formato de ejemplo **nyctaxi\_trip.xml** y **nyctaxi\\_fare.xml** (que se incluyen en la carpeta **Scripts de ejemplo**).

	![Datos de importación en bloque][16]

	También puede seleccionar el modo de autenticación, cuyo valor predeterminado es Autenticación de Windows. Haga clic en la flecha verde de la barra de herramientas para iniciar la ejecución. El script ejecutará 24 operaciones de importación masiva en paralelo, 12 para cada tabla con particiones. Puede supervisar el progreso de la importación de datos si abre la carpeta de datos predeterminada de SQL Server como el conjunto anterior.

9. El script de PowerShell informa de las horas de inicio y finalización. Cuando se completan todas las importaciones masivas, se notifica la hora de finalización. Revise la carpeta de registro de destino para comprobar que las importaciones masivas se realizaron correctamente, es decir, que no se informó de errores en la carpeta de registro de destino.

10. La base de datos ya está preparada para la exploración, diseño de características y otras operaciones que desee. Dado que las particiones de las tablas se crean según el campo **pickup\\_datetime**, las consultas que incluyan condiciones **pickup\\_datetime** en la cláusula **WHERE** se beneficiarán del esquema de partición.

11. En **SQL Server Management Studio**, explore el script de ejemplo proporcionado, **sample\\_queries.sql**. Para ejecutar cualquiera de las consultas de ejemplo, resalte las líneas de la consulta y haga clic en **!Ejecutar** en la barra de herramientas.

12. Los datos de NYC Taxi Trips se cargan en dos tablas distintas. Para mejorar las operaciones de combinación, se recomienda la indexación de las tablas. El script de ejemplo **create\\_partitioned\\_index.sql** crea índices con particiones en la clave de combinación compuesta **medallion, hack\\_license, and pickup\\_datetime**.

## <a name="dbexplore"></a>Exploración de datos e ingeniería de características en SQL Server

En esta sección, se llevará a cabo la exploración de datos y la generación de características mediante la ejecución de consultas SQL directamente en **SQL Server Management Studio** con la base de datos de SQL Server creada anteriormente. Se proporciona un script de ejemplo llamado **sample\\_queries.sql** en la carpeta **Scripts de ejemplo**. Modifique el script para cambiar el nombre de la base de datos, en caso de que no sea el predeterminado: **TaxiNYC**.

En este ejercicio, se hará lo siguiente:

- Conectarse a **SQL Server Management Studio** mediante Autenticación de Windows o con Autenticación de SQL y el nombre de inicio de sesión y la contraseña de SQL.
- Explorar distribuciones de datos de algunos campos en diferentes ventanas de tiempo.
- Investigar la calidad de los datos de los campos de longitud y latitud.
- Generar etiquetas de clasificación binaria y multiclase según **tip\\_amount**.
- Generar características y calcular o comparar distancias de carreras.
- Combinar las dos tablas y extraer una muestra aleatoria que se usará para generar modelos.

Cuando esté listo para continuar con Aprendizaje automático de Azure, puede:

1. Guardar la consulta SQL final para extraer y muestrear los datos, y copiar y pegar la consulta directamente en un módulo [Lector][reader] de Aprendizaje automático de Azure; o bien
2. Conservar los datos muestreados y de ingeniería que planea usar para la generación de modelos de una nueva tabla de base de datos y usar la nueva tabla en el módulo [Lector][reader] de Aprendizaje automático de Azure.

En esta sección se guardará la consulta final para extraer y muestrear los datos. El segundo método se muestra en la sección [Exploración de datos e ingeniería de características en el Bloc de notas de IPython](#ipnb).

Para una comprobación rápida del número de filas y columnas en las tablas que se rellenaron anteriormente mediante la importación masiva paralela,

	-- Report number of rows in table nyctaxi_trip without table scan
	SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

	-- Report number of columns in table nyctaxi_trip
	SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### Exploración: distribución de carreras por licencia

Este ejemplo identifica las licencias (números de taxi) con más de 100 carreras dentro de un período de tiempo. La consulta se beneficiaría del acceso de la tabla con particiones, ya que está condicionada por el esquema de partición de **pickup\_datetime**. La consulta el conjunto de datos completo también hará uso de la tabla con particiones o del recorrido de índice.

	SELECT medallion, COUNT(*)
	FROM nyctaxi_fare
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	GROUP BY medallion
	HAVING COUNT(*) > 100

#### Exploración: distribución de carreras por medallion y hack\_license

	SELECT medallion, hack_license, COUNT(*)
	FROM nyctaxi_fare
	WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
	GROUP BY medallion, hack_license
	HAVING COUNT(*) > 100

#### Evaluación de la calidad de los datos: comprobar los registros con longitud o latitud incorrectas

En este ejemplo se investiga si alguno de los campos de longitud y latitud contiene un valor no válido (los grados radianes deben encontrarse entre -90 y 90) o tienen coordenadas (0, 0).

	SELECT COUNT(*) FROM nyctaxi_trip
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### Exploración: distribución de carreras con propinas frente a sin propinas

En este ejemplo se busca el número de carreras en las que se han dado propinas frente a aquellas en las que no se han dado, en un período de tiempo determinado (o en el conjunto de datos completo si se abarca todo el año). Esta distribución refleja la distribución de etiquetas binarias que se usará más adelante para el modelado de clasificación binaria.

	SELECT tipped, COUNT(*) AS tip_freq FROM (
	  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
	  FROM nyctaxi_fare
	  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tipped

#### Exploración: distribución por intervalos y clases de propinas

Este ejemplo calcula la distribución de los intervalos de propinas de un período de tiempo determinado (o en el conjunto de datos completo si abarca todo el año). Esta es la distribución de las clases de etiquetas que se usarán posteriormente para el modelado de clasificación multiclase.

	SELECT tip_class, COUNT(*) AS tip_freq FROM (
		SELECT CASE
			WHEN (tip_amount = 0) THEN 0
			WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
			WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
			WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
			ELSE 4
		END AS tip_class
	FROM nyctaxi_fare
	WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tip_class

#### Exploración: calcular y comparar la distancia de la carrera

En este ejemplo se convierte la longitud y latitud de los puntos de recogida y destino a puntos geográficos de SQL, se calcula la distancia de la carrera mediante la diferencia de puntos geográficos de SQL y se devuelve una muestra aleatoria de los resultados de la comparación. En el ejemplo se limitan los resultados a coordenadas válidas usando solo la consulta de evaluación de calidad de datos tratada anteriormente.

	SELECT
	pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
	,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
	,trip_distance
	,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
	FROM nyctaxi_trip
	tablesample(0.01 percent)
	WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
	AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
	AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### Diseño de características en consultas SQL

Las consultas de exploración de conversión geográfica y la generación de etiquetas pueden usarse también para generar etiquetas y características mediante la eliminación de la parte de recuento. En la sección [Exploración de datos e ingeniería de características en el Bloc de notas de IPython](#ipnb) se ofrecen ejemplos SQL de ingeniería de características adicionales. Resulta más eficaz ejecutar consultas de generación de características sobre el conjunto de datos completo o un subconjunto grande mediante consultas SQL que se ejecuten directamente en la instancia de base de datos de SQL Server. Las consultas se pueden ejecutar en **SQL Server Management Studio**, el Bloc de notas de IPython o cualquier herramienta o entorno de desarrollo que tenga acceso local o remoto a la base de datos local.

#### Preparación de los datos para la creación del modelo

La siguiente consulta combina las tablas **nyctaxi\_trip** y **nyctaxi\_fare**, genera una etiqueta de clasificación binaria **tipped** (con propina), una etiqueta de clasificación multiclase **tip\\_class** y extrae una muestra aleatoria de un 1 % del conjunto de datos combinado completo. Esta consulta se puede copiar y pegar directamente en el módulo [Lector](https://studio.azureml.net) del [Estudio de aprendizaje automático de Azure][reader] para la ingesta directa de datos de la instancia de base de datos de SQL Server en Azure. La consulta excluye los registros con coordenadas (0, 0) incorrectas.

	SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, 	f.total_amount, f.tip_amount,
	    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
	    CASE WHEN (tip_amount = 0) THEN 0
	        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
	        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
	        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
	        ELSE 4
	    END AS tip_class
	FROM nyctaxi_trip t, nyctaxi_fare f
	TABLESAMPLE (1 percent)
	WHERE t.medallion = f.medallion
	AND   t.hack_license = f.hack_license
	AND   t.pickup_datetime = f.pickup_datetime
	AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>Exploración de datos e ingeniería de características en el Bloc de notas de IPython

En esta sección, se llevará a cabo la exploración de datos y la generación de características con consultas Pyhton y SQL en la base de datos de SQL Server creada anteriormente. Se proporciona un Bloc de notas de IPython de ejemplo llamado **machine-Learning-data-science-process-sql-story.ipynb** en la carpeta **Blocs de notas de IPython**. Este bloc de notas también está disponible en [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

La secuencia recomendada al trabajar con big data es la siguiente:

- Leer una pequeña muestra de los datos en una trama de datos en memoria.
- Realizar algunas visualizaciones y exploraciones con los datos de muestreo.
- Experimentar con el diseño de características con los datos de muestreo.
- Para el diseño de características, la exploración y la manipulación de datos más grandes, usar Python para emitir consultas SQL directamente sobre la base de datos de SQL Server en la máquina virtual de Azure.
- Decidir el tamaño de muestra que se usará para la creación del modelo de Aprendizaje automático de Azure.

Cuando esté listo para continuar con Aprendizaje automático de Azure, puede:

1. Guardar la consulta SQL final para extraer y muestrear los datos, y copiar y pegar la consulta directamente en un módulo [Lector][reader] de Aprendizaje automático de Azure. Este método se muestra en la sección [Generación de modelos en Aprendizaje automático de Azure](#mlmodel).    
2. Conservar los datos muestreados y de ingeniería que planea usar para la generación de modelos de una nueva tabla de base de datos y usar la nueva tabla en el módulo del [Lector][reader].

A continuación, se muestran algunas exploraciones de datos, visualizaciones de datos y ejemplos de diseño de características. Para obtener más ejemplos, vea el Bloc de notas de IPython de SQL de ejemplo en la carpeta **Blocs de notas de IPython** de ejemplo.

#### Inicializar las credenciales de la base de datos

Inicialice la configuración de conexión de base de datos en las variables siguientes:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### Crear conexiones de base de datos

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### Informe con el número de filas y columnas en la tabla nyctaxi\_trip

    nrows = pd.read_sql('''
		SELECT SUM(rows) FROM sys.partitions
		WHERE object_id = OBJECT_ID('nyctaxi_trip')
	''', conn)

	print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
		SELECT COUNT(*) FROM information_schema.columns
		WHERE table_name = ('nyctaxi_trip')
	''', conn)

	print 'Total number of columns = %d' % ncols.iloc[0,0]

- Número total de filas = 173179759  
- Número total de columnas = 14

#### Lectura de una muestra de datos pequeña de la base de datos de SQL Server

    t0 = time.time()

	query = '''
		SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
			f.tolls_amount, f.total_amount, f.tip_amount
		FROM nyctaxi_trip t, nyctaxi_fare f
		TABLESAMPLE (0.05 PERCENT)
		WHERE t.medallion = f.medallion
		AND   t.hack_license = f.hack_license
		AND   t.pickup_datetime = f.pickup_datetime
	'''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

El tiempo empleado en leer la tabla de ejemplo es 6,492000 segundos Número de filas y columnas recuperadas = (84952, 21)

#### Estadísticas descriptivas

Ya se pueden explorar los datos de muestreo. Comenzamos echando un vistazo a las estadísticas descriptivas del campo **trip\_distance** (o cualquier otro):

    df1['trip_distance'].describe()

#### Visualización: ejemplo de diagrama de caja

A continuación, observaremos el diagrama de caja de la distancia de la carrera para ver los cuantiles

    df1.boxplot(column='trip_distance',return_type='dict')

![Diagrama 1][1]

#### Visualización: ejemplo de diagrama de distribución

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Diagrama 2][2]

#### Visualización: diagramas de barras y líneas

En este ejemplo, se discretiza la distancia de la carrera en cinco discretizaciones y se visualizan los resultados de la discretización.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Podemos trazar la distribución de discretización anterior en un gráfico de barras o líneas, como se indica a continuación

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Diagrama 3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Diagrama 4][4]

#### Visualización: ejemplo de gráfico de dispersión

Se muestra el gráfico de dispersión entre **trip\_time\_in\_secs** y **trip\_distance** para ver si existe algún tipo de correlación

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Diagrama 6][6]

También podemos comprobar la relación entre **rate\_code** y **trip\_distance**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Diagrama 8][8]

### Submuestreo de los datos en SQL

Al preparar los datos para la creación del modelo en [Estudio de aprendizaje automático de Azure](https://studio.azureml.net), puede decidir **usar consultas SQL directamente en el módulo del Lector** o conservar los datos de ingeniería y muestreados en una tabla nueva, que puede utilizar en el módulo del [Lector][reader] con una simple instrucción **SELECT * FROM <your\_new\_table\_name>**.

En esta sección se creará una nueva tabla para almacenar los datos de ingeniería y muestreados. En la sección [Exploración de datos e ingeniería de características en SQL Server](#dbexplore) se proporciona un ejemplo de una consulta SQL directa para la creación del modelo.

#### Cree una tabla de ejemplo y rellénela con el 1 % de las tablas combinadas. Quite la tabla primero si existe.

En esta sección, se combinan las tablas **nyctaxi\_trip** y **nyctaxi\_fare**, se extrae una muestra aleatoria de un 1 % y se conservan los datos del muestreo en un nuevo nombre de tabla **nyctaxi\_one\_percent**:

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
		INTO nyctaxi_one_percent
		FROM nyctaxi_trip t, nyctaxi_fare f
		TABLESAMPLE (1 PERCENT)
		WHERE t.medallion = f.medallion
		AND   t.hack_license = f.hack_license
		AND   t.pickup_datetime = f.pickup_datetime
		AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### Exploración de datos mediante consultas SQL en Blocs de notas de IPython

En esta sección, se explorarán las distribuciones de datos con los datos de 1 % de muestreo que se conservan en la nueva tabla que se creó anteriormente. Tenga en cuenta que se pueden realizar exploraciones similares mediante las tablas originales, con el uso opcional de **TABLESAMPLE** para limitar el ejemplo de exploración o mediante la limitación de los resultados a un período de tiempo determinado con las particiones **pickup\_datetime**, como se muestra en la sección [Exploración de datos y diseño de características en SQL Server](#dbexplore).

#### Exploración: distribución diaria de carreras

    query = '''
		SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
		FROM nyctaxi_one_percent
		GROUP BY CONVERT(date, dropoff_datetime)
	'''

    pd.read_sql(query,conn)

#### Exploración: distribución de carreras por licencia

    query = '''
		SELECT medallion,count(*) AS c
		FROM nyctaxi_one_percent
		GROUP BY medallion
	'''

	pd.read_sql(query,conn)

### Generación de características mediante consultas SQL en Blocs de notas de IPython

En esta sección, se generarán nuevas etiquetas y características directamente mediante consultas SQL, que funcionarán con la muestra de 1 % que se creó en la sección anterior.

#### Generación de etiquetas: generar etiquetas de clase

En el ejemplo siguiente, se generan dos conjuntos de etiquetas que se usan para el modelado:

1. Etiquetas de clase binaria **tipped** (que predecirán si se dará propina)
2. Etiquetas multiclase **tip\_class** (que predecirán el intervalo o la discretización de la propina)

		nyctaxi_one_percent_add_col = '''
			ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
		'''

		cursor.execute(nyctaxi_one_percent_add_col)
		cursor.commit()

    	nyctaxi_one_percent_update_col = '''
        	UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''

    	cursor.execute(nyctaxi_one_percent_update_col)
		cursor.commit()

#### Ingeniería de características: características de recuento de columnas de categorías

En este ejemplo se transforma un campo de categoría en un campo numérico mediante el reemplazo de cada categoría por el recuento de sus apariciones en los datos.

    nyctaxi_one_percent_insert_col = '''
		ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
	'''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
		WITH B AS
		(
			SELECT medallion, hack_license,
				SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
				SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
			FROM nyctaxi_one_percent
			GROUP BY medallion, hack_license
		)

		UPDATE nyctaxi_one_percent
		SET nyctaxi_one_percent.cmt_count = B.cmt_count,
			nyctaxi_one_percent.vts_count = B.vts_count
		FROM nyctaxi_one_percent A INNER JOIN B
		ON A.medallion = B.medallion AND A.hack_license = B.hack_license
	'''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### Ingeniería de características: características de discretización de columnas numéricas

En este ejemplo se transforma un campo numérico continuo en intervalos de categoría preestablecidos; por ejemplo, la transformación de un campo numérico en un campo de categoría.

    nyctaxi_one_percent_insert_col = '''
		ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
	'''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
		WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
		(
			SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
			NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
		)

		UPDATE nyctaxi_one_percent
		SET trip_time_bin = B.BinNumber
		FROM nyctaxi_one_percent A INNER JOIN B
		ON A.medallion = B.medallion
		AND A.hack_license = B.hack_license
		AND A.pickup_datetime = B.pickup_datetime
	'''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### Ingeniería de características: extraer las características de ubicación a partir de una latitud o longitud decimal

En este ejemplo se desglosa la representación decimal de un campo de longitud o latitud en varios campos de región de granularidad diferente, como país, provincia, localidad, bloque, etc. Tenga en cuenta que los nuevos campos geográficos no están asignados a ubicaciones reales. Para obtener información sobre la asignación de ubicaciones de códigos geográficos, consulte [Servicios REST de Mapas de Bing](https://msdn.microsoft.com/library/ff701710.aspx).

    nyctaxi_one_percent_insert_col = '''
		ALTER TABLE nyctaxi_one_percent
		ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
			l5 varchar(3), l6 varchar(3), l7 varchar(3)
	'''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
		UPDATE nyctaxi_one_percent
		SET l1=round(pickup_longitude,0)
			, l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
			, l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
			, l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
			, l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
			, l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
			, l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
	'''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### Comprobar el formulario final de la tabla con características

    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

Ya está todo listo para pasar a la creación del modelo y la implementación del mismo en [Aprendizaje automático de Azure](https://studio.azureml.net). Los datos están listos para cualquiera de los problemas de predicción identificados anteriormente, a saber:

1. Clasificación binaria: para predecir si se dio propina en una carrera, o no.

2. Clasificación multiclase: para predecir el intervalo de la propina dada, según las clases definidas anteriormente.

3. Tarea de regresión: para predecir la cantidad de propina pagada en una carrera.


## <a name="mlmodel"></a>Creación de modelos en Aprendizaje automático de Azure

Para iniciar el ejercicio de modelado, inicie sesión en el área de trabajo de Aprendizaje automático de Azure. Si aún no ha creado un área de trabajo de aprendizaje automático, consulte [Creación de un área de trabajo de Aprendizaje automático de Azure](machine-learning-create-workspace.md).

1. Para empezar a usar el Aprendizaje automático de Azure, consulte [¿Qué es Estudio de aprendizaje automático de Microsoft Azure?](machine-learning-what-is-ml-studio.md)

2. Inicie sesión en [Estudio de aprendizaje automático de Azure](https://studio.azureml.net).

3. La página principal del Estudio ofrece una gran cantidad de información, vídeos, tutoriales, vínculos a referencias de módulos y otros recursos. Para obtener más información acerca de Aprendizaje automático de Azure, consulte el [Centro de documentación de Aprendizaje automático de Azure](https://azure.microsoft.com/documentation/services/machine-learning/).

Un experimento de entrenamiento típico consta de las siguientes acciones:

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

En este ejercicio, ya se han explorado y diseñado los datos en SQL Server, y también se ha decidido el tamaño de la muestra para la ingesta en Aprendizaje automático de Azure. Para crear uno o varios de los modelos de predicción, se decidió:

1. Proporcionar los datos a Aprendizaje automático de Azure con el módulo [Lector][reader], disponible en la sección **Entrada y salida de datos**. Para obtener más información, consulte la página de referencia del módulo [Lector][reader].

	![Lector de Aprendizaje automático de Azure][17]

2. Seleccionar **Base de datos SQL de Azure** como **Origen de datos** en el panel **Propiedades**.

3. Escribir el nombre DNS de la base de datos en el campo **Nombre del servidor de la base de datos**. Formato: `tcp:<your_virtual_machine_DNS_name>,1433`

4. Escribir el **nombre de la base de datos** en el campo correspondiente.

5. Escribir el **nombre de usuario de SQL** en **Nombre de la cuenta de usuario del servidor y la contraseña en **Contraseña de la cuenta de usuario del servidor**.

6. Activar la opción **Aceptar cualquier certificado de servidor**.

7. En el área de texto editable **Consulta de base de datos**, pegar la consulta que extrae los campos de la base de datos necesarios (incluidos los campos calculados, como las etiquetas) y reducir la muestra al tamaño de muestra deseado.

En la ilustración siguiente se muestra un ejemplo de un experimento de clasificación binaria que lee datos directamente de la base de datos de SQL Server. Se pueden construir experimentos similares para problemas de clasificación multiclase y de regresión.

![Entrenamiento de Aprendizaje automático de Azure][10]

> [AZURE.IMPORTANT] En los ejemplos de consultas de extracción y muestreo de datos de modelado de las secciones anteriores, **las etiquetas de los tres ejercicios de modelado se incluyen en la consulta**. Un paso importante (requerido) en cada uno de los ejercicios de modelado consiste en **excluir** las etiquetas innecesarias de los otros dos problemas y cualquier otra **fuga de destino**. Por ejemplo., cuando use clasificación binaria, utilice la etiqueta **tipped** y excluya los campos **tip\_class**, **tip\_amount** y **total\_amount**. Estos últimos son fugas de destino ya que implican que se pagó propina.
>
> Para excluir columnas innecesarias o fugas de destino, puede usar el módulo [Proyectar columnas][project-columns] o el [Editor de metadatos][metadata-editor]. Para obtener más información, consulte las páginas de referencia de [Proyectar columnas][project-columns] y [Editor de metadatos][metadata-editor].

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

Para recapitular, en este tutorial paso a paso se ha creado un entorno de ciencia de datos de Azure, se ha trabajado con un conjunto grande de datos público de principio a fin, desde la adquisición de los datos al entrenamiento del modelo, y la implementación de un servicio web de Aprendizaje automático de Azure.

### Información de licencia

Microsoft comparte este tutorial de ejemplo y sus scripts adjuntos y Blocs de notas de IPython bajo la licencia MIT. Consulte el archivo LICENSE.txt que se encuentra en el directorio del código de ejemplo en GitHub para obtener más detalles.

### Referencias

• [Página de descarga de NYC Taxi Trips de Andrés Monroy](http://www.andresmh.com/nyctaxitrips/) • [FOILing NYC's Taxi Trip Data de Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/) • [Estadísticas e investigación de la Comisión de taxis y limusinas de la Ciudad de Nueva York](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sql-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sql-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sql-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sql-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sql-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sql-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sql-walkthrough/amlscoring.png


<!-- Module References -->
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0211_2016-->