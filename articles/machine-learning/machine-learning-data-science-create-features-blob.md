<properties 
	pageTitle="Creación de características para los datos de almacenamiento de blobs de Azure mediante Panda | Microsoft Azure" 
	description="Cómo crear características para los datos almacenados en un contenedor de blobs de Azure mediante el paquete Panda de Python." 
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
	ms.date="02/05/2016" 
	ms.author="fashah;garye;bradsev" />

#Creación de características para los datos de almacenamiento de blobs de Azure mediante Panda


##Introducción

Este documento explica cómo crear características para datos que se almacenan en el contenedor de blobs de Azure mediante el paquete de Python [Pandas](http://pandas.pydata.org/). Después de esquematizar cómo cargar en una trama de datos de Panda, se muestra cómo generar características categóricas con valores de indicador y características de discretización, mediante scripts de Python.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]
Este **menú** vincula a temas en los que se describe cómo crear características para datos en diversos entornos. Esta tarea es un paso del [proceso de Cortana Analytics (CAP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## Requisitos previos
En este artículo se supone que ha:

* Creado una cuenta de almacenamiento de blobs de Azure y que ha almacenado ahí los datos. Si necesita instrucciones para configurar una cuenta, consulte [Creación de una cuenta de almacenamiento de Azure](../hdinsight-get-started.md#storage).


## Carga de los datos en una trama de datos Pandas
Para explorar y manipular un conjunto de datos, se debe descargar desde el origen de blob en un archivo local que se pueda cargar en una trama de datos de Pandas. Estos son los pasos a seguir para realizar este procedimiento:

1. Descargue los datos del blob de Azure con el siguiente código de Python de ejemplo mediante el servicio BLOB. Reemplace la variable en el código siguiente por sus valores específicos: 

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
	
##<a name="blob-featuregen"></a>Generación de características
	
Las dos secciones siguientes muestran cómo generar características de categorías con valores de indicador y características de discretización mediante scripts de Python.

###<a name="blob-countfeature"></a>Generación de características basada en el valor de indicador

Las características de categorías se pueden crear como sigue:

1. Inspeccione la distribución de la columna de categorías:
	
		dataframe_blobdata['<categorical_column>'].value_counts()

2. Genere valores de indicador para cada uno de los valores de columna

		#generate the indicator column
		dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. Combine la columna de indicador con la trama de datos original
 
			#Join the dummy variables back to the original data frame
			dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. Quite la propia variable original:

		#Remove the original column rate_code in df1_with_dummy
		dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
	
###<a name="blob-binningfeature"></a>Generación de características de discretización

Para generar características discretizadas, se procede de la siguiente manera:

1. Agregue una secuencia de columnas para discretizar una columna numérica
 
		bins = [0, 1, 2, 4, 10, 40]
		dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
		
2. Convierta la discretización en una secuencia de variables booleanas

		dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
	
3. Por último, combine las variables de prueba con la trama de datos original

		dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool)	

##<a name="sql-featuregen"></a>Reescritura de datos en un blob de Azure y consumo en Aprendizaje automático de Azure

Cuando haya explorado los datos y creado las características necesarias, puede cargar los datos (muestreados o con características) en un blob de Azure y consumirlos en Aprendizaje automático de Azure, mediante los siguientes pasos. Tenga en cuenta que también se pueden crear características adicionales en Estudio de aprendizaje automático de Azure 1. Escriba la trama de datos en el archivo local

		dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Cargue los datos en el blob de Azure como se indica a continuación:

		from azure.storage import BlobService
    	import tables

		STORAGEACCOUNTNAME= <storage_account_name>
		LOCALFILENAME= <local_file_name>
		STORAGEACCOUNTKEY= <storage_account_key>
		CONTAINERNAME= <container_name>
		BLOBNAME= <blob_name>

	    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
	    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
	    
	    try:
	   
	    #perform upload
	    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
	    
	    except:	        
		    print ("Something went wrong with uploading blob:"+BLOBNAME)

3. Ahora se pueden leer los datos del blob mediante el módulo [Lector](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) de Aprendizaje automático de Azure, como se muestra en la pantalla siguiente:
 
![lector de blobs](./media/machine-learning-data-science-process-data-blob/reader_blob.png)


 

<!---HONumber=AcomDC_0211_2016-->