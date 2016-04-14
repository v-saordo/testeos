<properties 
	pageTitle="Proceso de datos de SQL Azure | Azure" 
	description="Procesar datos de SQL Azure" 
	services="machine-learning" 
	documentationCenter="" 
	authors="fashah" 
	manager="paulettm" 
	editor="" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/08/2016" 
	ms.author="fashah;garye" />

#<a name="heading"></a>Proceso de datos en una máquina virtual de SQL Server en Azure

En este documento se aborda cómo explorar datos y generar características para los datos almacenados en una VM de SQL Server en Azure. Esto puede hacerse mediante la administración de datos usando SQL o mediante un lenguaje de programación como Python.


> [AZURE.NOTE] En las instrucciones SQL de ejemplo de este documento se supone que los datos están en SQL Server. Si no lo están, consulte el mapa de proceso de ciencia de datos en la nube para obtener información sobre cómo mover los datos a SQL Server.

##<a name="SQL"></a>Uso de SQL

En esta sección, se describen las siguientes tareas de tratamiento de datos mediante SQL:

1. [Exploración de datos](#sql-dataexploration)
2. [Generación de características](#sql-featuregen)

###<a name="sql-dataexploration"></a>Exploración de datos
A continuación se muestran algunos scripts de SQL de ejemplo que se pueden usar para explorar los almacenes de datos en SQL Server.


> [AZURE.NOTE] Para obtener un ejemplo práctico, puede usar el [conjunto de datos de los taxis de la Ciudad de Nueva York](http://www.andresmh.com/nyctaxitrips/) y consultar el IPNB llamado [Tratamiento de datos de la Ciudad de Nueva York mediante un Bloc de notas de IPython y SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb), que se trata de un tutorial completo.

1. Obtener el número de observaciones por día

	`SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)`

2. Obtenga los niveles de una columna de categorías

	`select  distinct <column_name> from <databasename>`

3. Obtener el número de niveles de combinación de dos columnas de categorías

	`select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. Obtener la distribución para columnas numéricas

	`select <column_name>, count(*) from <tablename> group by <column_name>`


###<a name="sql-featuregen"></a>Generación de características

En esta sección, se describen formas de generar características mediante SQL:

1. [Generación de características basadas en recuentos](#sql-countfeature)
2. [Generación de características de discretización](#sql-binningfeature)
3. [Implementación de las características de una sola columna](#sql-featurerollout)


> [AZURE.NOTE] Cuando genere características adicionales, puede agregarlas como columnas a la tabla existente o crear una nueva tabla con las características adicionales y la clave principal, que se pueden combinar con la tabla original.

###<a name="sql-countfeature"></a>Generación de características basadas en recuentos

En este documento se muestran dos maneras de generar características de recuento. El primer método usa la suma condicional y el segundo utiliza la cláusula 'where'. Estos pueden entonces combinarse con la tabla original (con columnas de clave principal) para disponer de características de recuento junto con los datos originales.

	select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

	select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
	where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

###<a name="sql-binningfeature"></a>Generación de características de discretización

En el ejemplo siguiente se muestra cómo generar características discretizadas mediante la discretización (con 5 discretizaciones) de una columna numérica que puede usarse en su lugar como una característica:

	`SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


###<a name="sql-featurerollout"></a>Implementación de las características de una sola columna

En esta sección, se muestra cómo se implementa una sola columna de una tabla para generar características adicionales. En el ejemplo se supone que hay una columna de latitud o longitud en la tabla a partir de la cual está intentando generar características.

Aquí se incluye un breve manual sobre los datos de ubicación de latitud y longitud (extraído de stackoverflow `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Resulta útil para comprender bien todo antes de caracterizar el campo de ubicación:

- La señal indica si estamos en el norte o sur, y este u oeste del mundo.
- Un dígito de las centenas distinto de cero indica que se usa la longitud y no la latitud.
- El dígito de las decenas ofrece una posición a aproximadamente 1.000 kilómetros. Nos brinda información útil sobre el continente u océano en el que nos encontramos.
- El dígito de las unidades (un grado decimal) indica una posición de hasta 111 kilómetros (60 millas náuticas, aproximadamente 69 millas). Puede informarnos aproximadamente del estado grande o país en que nos encontramos.
- La primera posición decimal tiene un valor de hasta 11,1 km: puede distinguir la posición de una ciudad grande de otra ciudad grande vecina.
- La segundo posición decimal tiene un valor de hasta 1,1 km: puede separar un pueblo del siguiente.
- La tercera posición decimal tiene un valor de hasta 110 m: puede identificar un campo agrícola extenso o campus universitario.
- La cuarta posición decimal tiene un valor de hasta 11 m: puede identificar una parcela de tierra. Es comparable a la precisión típica de una unidad GPS sin corregir y sin interferencias.
- La quinta posición decimal tiene un valor de hasta 1,1 m: puede distinguir entre distintos árboles. Solo es posible conseguir una precisión de este nivel con unidades GPS comerciales con corrección diferencial.
- La sexta posición decimal tiene un valor de hasta 0,11 m: puede usarse para diseñar estructuras en detalle, para el diseño de paisajes o la construcción de carreteras. Debería ser más que suficiente para realizar el seguimiento de los movimientos de glaciares y ríos. Esto se consigue al tomar medidas meticulosas con GPS, como GPS corregido de forma diferencial.

La información de ubicación se puede caracterizar como sigue, con diferencias entre la información de región, ubicación y ciudad. Tenga en cuenta que también es posible llamar a un extremo de REST, como la API de mapas de Bing disponible en `https://msdn.microsoft.com/library/ff701710.aspx` para obtener la información de la región o el distrito.

	select 
		<location_columnname>
		,round(<location_columnname>,0) as l1		
		,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end 	
		,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end 	
		,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end 	
		,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end 	
		,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end 	
		,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end 	
	from <tablename>

Las características basadas en ubicación anteriores se pueden usar aún más para generar características de recuento adicionales, tal y como se describió anteriormente.


> [AZURE.TIP] Puede insertar mediante programación los registros con el lenguaje que prefiera. Es posible que deba insertar los datos en fragmentos para mejorar el rendimiento de escritura [Consulte el ejemplo sobre cómo hacerlo mediante pyodbc aquí.](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)
 

> [AZURE.TIP] Otra alternativa consiste en insertar datos en la base de datos mediante la [utilidad BCP](https://msdn.microsoft.com/library/ms162802.aspx)

###<a name="sql-aml"></a>Conexión con Aprendizaje automático de Azure

La característica recién generada se puede agregar como una columna a una tabla existente o se puede almacenar en una tabla nueva y combinar con la tabla original para el aprendizaje automático. Es posible generar o tener acceso a las características si ya se han creado, mediante el módulo [Lector][reader] en Aprendizaje automático de Azure, como se muestra a continuación:

![Lectores de azureml][1]

##<a name="python"></a>Uso de un lenguaje de programación como Python

Usar Python para generar explorar datos y generar características cuando los datos están en SQL Server es parecido a procesar los datos en Blob de Azure mediante Python, como se documenta en [Proceso de datos de Blob de Azure en su entorno de ciencia de datos](machine-learning-data-science-process-data-blob.md). Los datos deben cargarse desde la base de datos en una trama de datos de Pandas y, a continuación, se pueden procesar aún más. Se documenta el proceso de conexión a la base de datos y carga de los datos en la trama de datos de esta sección.

El formato de cadena de conexión siguiente puede usarse para conectarse a una base de datos de SQL Server desde Python mediante pyodbc (reemplace servername, dbname, username y password con sus valores específicos):

	#Set up the SQL Azure connection
	import pyodbc	
	conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

La [biblioteca Pandas](http://pandas.pydata.org/) en Python ofrece un amplio conjunto de herramientas de análisis de datos y estructuras de datos para la manipulación de datos para la programación en Python. El código siguiente lee los resultados que se devuelven desde una base de datos de SQL Server en una trama de datos de Pandas:

	# Query database and load the returned results in pandas data frame
	data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Ya puede trabajar con la trama de datos de Pandas como se explica en los temas [Procesar datos de Blob de Azure en su entorno de ciencia de datos](machine-learning-data-science-process-data-blob.md).

## Ejemplo de ciencia de datos de Azure en acción

Para obtener un ejemplo de tutorial completo del Proceso de ciencia de datos de Azure mediante un conjunto de datos público, consulte [Proceso de ciencia de datos de Azure en acción](machine-learning-data-science-process-sql-walkthrough.md).

[1]: ./media/machine-learning-data-science-process-sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=AcomDC_0211_2016-->