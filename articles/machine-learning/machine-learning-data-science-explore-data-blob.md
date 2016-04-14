<properties 
	pageTitle="Exploración de datos en el almacenamiento de blobs de Azure con Pandas | Microsoft Azure" 
	description="Cómo explorar los datos almacenados en el contenedor de blobs de Azure mediante Pandas." 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/08/2016" 
	ms.author="fashah;garye;bradsev" />

#Exploración de datos en el almacenamiento de blobs de Azure con Pandas

## Introducción

Este documento explica cómo explorar los datos almacenados en el contenedor de blobs de Azure mediante el paquete de Python [Pandas](http://pandas.pydata.org/).

El **menú** a continuación vincula a temas que describen cómo usar herramientas para explorar los datos desde varios entornos de almacenamiento. Esta tarea es un paso en el proceso de análisis de Cortana (CAP).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]


## Requisitos previos
En este artículo se supone que ha:

* Creado una cuenta de almacenamiento de Azure. Si necesita instrucciones, consulte [Creación de una cuenta de almacenamiento de Azure](../hdinsight-get-started.md#storage).
* Almacenó los datos en una cuenta de almacenamiento de blobs de Azure.

## Carga de los datos en una trama de datos Pandas
Para explorar y manipular un conjunto de datos, se debe descargar desde el origen de blob en un archivo local que se pueda cargar en una trama de datos de Pandas. Estos son los pasos a seguir para realizar este procedimiento:

1. Descargue los datos del blob de Azure con el siguiente código de ejemplo Python mediante el servicio BLOB. Reemplace la variable en el código siguiente por sus valores específicos: 

	    from azure.storage import BlobService
    	import tables
    	
		STORAGEACCOUNTNAME= <storage_account_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		LOCALFILENAME= <local_file_name>		
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

    	#download from blob
    	t1=time.time()
    	blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
    	blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
    	t2=time.time()
    	print(("It takes %s seconds to download "+blobname) % (t2 - t1))


2. Lea los datos en una trama de datos de Pandas desde el archivo descargado.

	    #LOCALFILE is the file path	
    	dataframe_blobdata = pd.read_csv(LOCALFILE)

Ya puede explorar los datos y generar características en este conjunto de datos.

##<a name="blob-dataexploration"></a>Ejemplos de exploración de datos con Pandas

A continuación, se muestran algunos ejemplos de formas de explorar datos mediante Pandas:

1. Inspeccionar el **número de filas y columnas** 

		print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape

2. **Inspeccionar** las primeras o las últimas **filas** del conjunto de datos, como se indica a continuación:

		dataframe_blobdata.head(10)
		
		dataframe_blobdata.tail(10)

3. Comprobar el **tipo de datos** como el que se importó cada columna mediante el siguiente código de ejemplo:
 	
		for col in dataframe_blobdata.columns:
		    print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype

4. Comprobar las **estadísticas básicas** de las columnas del conjunto de datos de la siguiente forma:
 
		dataframe_blobdata.describe()
	
5. Observar el número de entradas de cada valor de columna, como se indica a continuación

		dataframe_blobdata['<column_name>'].value_counts()

6. **Contar los valores que faltan** frente al número real de entradas de cada columna, mediante el siguiente código de ejemplo:

		miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
		print miss_num
	 
7.	Si hay **valores que faltan** para una columna determinada en los datos, puede quitarlos como se indica:

		dataframe_blobdata_noNA = dataframe_blobdata.dropna()
		dataframe_blobdata_noNA.shape

	Otra forma de reemplazar los valores que faltan es a través de la función de modo:
	
		dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})		

8. Crear un gráfico de **histograma** con un número variable de discretizaciones para trazar la distribución de una variable.
	
		dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')
		
		np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
	
9. Examinar las **correlaciones** entre las variables mediante un gráfico de dispersión o con la función de correlación integrada.

		#relationship between column_a and column_b using scatter plot
		plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])
		
		#correlation between column_a and column_b
		dataframe_blobdata[['<column_a>', '<column_b>']].corr()

<!---HONumber=AcomDC_0211_2016-->