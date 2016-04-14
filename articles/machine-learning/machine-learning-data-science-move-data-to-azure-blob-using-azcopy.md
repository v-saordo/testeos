<properties 
	pageTitle="Mover datos hacia y desde el almacenamiento de blobs de Azure con AzCopy | Microsoft Azure" 
	description="Mover datos hacia y desde el almacenamiento de blobs de Azure con AzCopy" 
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

# Mover datos hacia y desde el almacenamiento de blobs de Azure con AzCopy

## Introducción

AzCopy es una utilidad de línea de comandos diseñada para realizar operaciones de alto rendimiento de carga, descarga y copia de datos a y desde el Almacenamiento de blobs, archivos y tablas de Microsoft Azure.

Para obtener instrucciones sobre la instalación de AzCopy y de información adicional sobre su uso con la plataforma de Azure, consulte [Introducción a la utilidad de línea de comandos AzCopy](../storage-use-azcopy.md).

A continuación se ofrecen vínculos de orientación sobre las tecnologías que se usan para mover datos hacia o desde el almacenamiento de blobs de Azure:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Si está usando la máquina virtual que se configuró con los scripts ofrecidos por [Máquinas virtuales de ciencia de datos en Azure](machine-learning-data-science-virtual-machines.md), AzCopy ya está instalado en la máquina virtual.

> [AZURE.NOTE] Para ver una introducción completa al almacenamiento de blobs de Azure, consulte [Aspectos básicos del blob de Azure](../storage-dotnet-how-to-use-blobs.md) y [Servicio BLOB de Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Requisitos previos

En este documento se supone que tiene una suscripción de Azure y una cuenta de almacenamiento y la clave de almacenamiento correspondiente para dicha cuenta. Antes de cargar o descargar datos, debe conocer su nombre de cuenta de almacenamiento de Azure y la clave de cuenta.

- Para configurar una suscripción de Azure, consulte [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
- Para obtener instrucciones sobre la creación de una cuenta de almacenamiento y para obtener información de cuentas y claves, vea [Acerca de las cuentas de almacenamiento de Azure](../storage-create-storage-account.md).

## Carga de archivos en un blob de Azure

Para cargar un archivo, use el siguiente comando en la opción AzCopy es una línea de comandos.

	# Upload from local file system
	AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S 

## Descarga de archivos de un blob de Azure

Para descargar un archivo de un blob de Azure, use el siguiente comando en la opción AzCopy es una línea de comandos.

	# Downloading blobs to local file system
	AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S

## Transferir blobs de un contenedor de Azure a otro

Para transferir blobs de un contenedor de Azure a otro, use el siguiente comando en la opción AzCopy es una línea de comandos.

	# Transferring blobs between Azure containers
	AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S
	
	<your_account_name>: your storage account name
	<your_account_key>: your storage account key
	<your_container_name>: your container name
	<your_sub_directory_at_blob>: the sub directory in the container 
	<your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
	<file_pattern>: pattern of file names to be transferred. The standard wildcards are supported

## Sugerencias de uso de AzCopy

> [AZURE.TIP]   
1\. Al cargar archivos, /S cargará los archivos de forma recursiva. Sin este parámetro, no se cargarán los archivos del subdirectorio. 2. Al descargar el archivo, /S buscará el contenedor de manera recursiva hasta que se descarguen todos los archivos del directorio especificado y sus subdirectorios, o todos los archivos que coincidan con el patrón especificado en el directorio especificado y sus subdirectorios. 3. No se puede especificar un archivo de blob específico para descargar mediante el parámetro /Source. Para descargar un archivo específico, especifique el nombre del archivo blob que se va a descargar mediante el parámetro /Pattern. El parámetro /S se puede usar para que AzCopy busque de forma recursiva un patrón de nombre de archivo. Sin el parámetro de patrón, AzCopy descargará todos los archivos en ese directorio.

<!---HONumber=AcomDC_0211_2016-->