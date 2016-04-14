<properties 
	pageTitle="Mover datos hacia y desde el almacenamiento de blobs de Azure con Python | Microsoft Azure" 
	description="Mover datos hacia y desde el almacenamiento de blobs de Azure con Python" 
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
	ms.author="bradsev" />

# Mover datos hacia y desde el almacenamiento de blobs de Azure con Python

## Introducción
En este tema se describe cómo enumerar, cargar y descargar blobs usando la API de Python.

Con la API de Python proporcionada en el SDK de Azure, puede

- Crear un contenedor
- Cargar un blob en un contenedor
- Descargar blobs
- Enumerar los blobs de un contenedor
- Eliminar un blob

Para obtener más información sobre el uso de la API de Python, consulte [Uso del servicio de almacenamiento de blobs desde Python](../storage-python-how-to-use-blob-storage.md).

A continuación se ofrecen vínculos de orientación sobre las tecnologías que se usan para mover datos hacia o desde el almacenamiento de blobs de Azure:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Si está usando la máquina virtual que se configuró con los scripts ofrecidos por [Máquinas virtuales de ciencia de datos en Azure](machine-learning-data-science-virtual-machines.md), AzCopy ya está instalado en la máquina virtual.

> [AZURE.NOTE] Para ver una introducción completa al almacenamiento de blobs de Azure, consulte [Aspectos básicos del blob de Azure](../storage-dotnet-how-to-use-blobs.md) y [Servicio BLOB de Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Requisitos previos

En este documento se supone que tiene una suscripción de Azure y una cuenta de almacenamiento y la clave de almacenamiento correspondiente para dicha cuenta. Antes de cargar o descargar datos, debe conocer su nombre de cuenta de almacenamiento de Azure y la clave de cuenta.

- Para configurar una suscripción de Azure, consulte [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
- Para obtener instrucciones sobre la creación de una cuenta de almacenamiento y para obtener información de cuentas y claves, vea [Acerca de las cuentas de almacenamiento de Azure](../storage-create-storage-account.md).

## Cargar datos en blob

Agregue el siguiente fragmento de código cerca de la parte superior de cualquier código de Python en el que desee obtener acceso mediante programación al almacenamiento de Azure:

	from azure.storage import BlobService

El objeto **BlobService** permite trabajar con contenedores y blobs. El código siguiente crea un objeto BlobService utilizando el nombre de la cuenta de almacenamiento y la clave de la cuenta: Reemplace el nombre de la cuenta y la clave de cuenta por la cuenta real y la clave.
	
	blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Use los siguientes métodos para cargar datos en un blob:
 
1. put\_block\_blob\_from\_path (carga el contenido de un archivo desde la ruta especificada)
2. put\_block\_blob\_from\_file (carga el contenido de una secuencia o archivo ya abierto)
3. put\_block\_blob\_from\_bytes (carga una matriz de bytes)
4. put\_block\_blob\_from\_text (carga el valor de texto especificado con la codificación especificada)
 
El siguiente código de ejemplo carga un archivo local en un contenedor:
	
	blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

El siguiente código de ejemplo carga todos los archivos (excepto los directorios) en un directorio local para el almacenamiento de blobs:

	from azure.storage import BlobService
	from os import listdir
	from os.path import isfile, join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	# find all files in the LOCAL_DIRECT (excluding directory)
	local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]
	
	file_num = len(local_file_list)
	for i in range(file_num):
	    local_file = join(LOCAL_DIRECT, local_file_list[i])
	    blob_name = local_file_list[i]
	    try:
	        blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
	    except:
	        print "something wrong happened when uploading the data %s"%blob_name

## Descargar datos del blob

Utilice los siguientes métodos para descargar datos de un blob: 1. get\_blob\_to\_path 2. get\_blob\_to\_file 3. get\_blob\_to\_bytes 4. get\_blob\_to\_text

Estos métodos que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

El código de ejemplo siguiente descarga el contenido de un blob de un contenedor en un archivo local:

	blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

El siguiente código de ejemplo descarga todos los blobs de un contenedor. Usa list\_blobs para obtener la lista de blobs disponibles del contenedor y los descarga en un directorio local.

	from azure.storage import BlobService
	from os.path import join
	
	# Set parameters here
	ACCOUNT_NAME = "<your_account_name>"
	ACCOUNT_KEY = "<your_account_key>"
	CONTAINER_NAME = "<your_container_name>"
	LOCAL_DIRECT = "<your_local_directory>"		
	
	blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
	
	# List all blobs and download them one by one
	blobs = blob_service.list_blobs(CONTAINER_NAME)
	for blob in blobs:
	    local_file = join(LOCAL_DIRECT, blob.name)
	    try:
	        blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
	    except:
	        print "something wrong happened when downloading the data %s"%blob.name

<!---HONumber=AcomDC_0211_2016-->