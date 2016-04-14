<properties
	pageTitle="Uso del almacenamiento de archivos de Azure desde Python | Microsoft Azure"
	description="Aprenda a usar el servicio de almacenamiento de archivos de Azure desde Python para cargar, enumerar, descargar y eliminar archivos."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="emgerner"/>

# Uso del almacenamiento de archivos de Azure desde Python

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

## Información general

Este artículo le muestra cómo realizar algunas tareas comunes con el almacenamiento de archivos. Los ejemplos están escritos en Python y usan el [SDK de Almacenamiento de Microsoft Azure para Python]. Entre los escenarios descritos se incluyen cargar, enumerar, descargar y eliminar archivos.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creación de un recurso compartido

El objeto **FileService** le permite trabajar con archivos, directorios y recursos compartidos. El siguiente código crea un objeto **FileService**. Agregue lo siguiente cerca de la parte superior de cualquier archivo Python en el que desee obtener acceso al almacenamiento de Azure mediante programación.

	from azure.storage.file import FileService

El código siguiente crea un objeto **FileService** usando el nombre de la cuenta de almacenamiento y la clave de la cuenta. Reemplace 'myaccount' y 'mykey' por la clave y el nombre de cuenta.

	file_service = **FileService** (account_name='myaccount', account_key='mykey')

En el siguiente ejemplo de código, puede usar un objeto **FileService** para crear el recurso compartido si no existe.

	file_service.create_share('myshare')

## Carga de un archivo en un recurso compartido

Los recursos compartidos de almacenamiento de archivos de Azure contienen como mínimo un directorio raíz donde pueden residir los archivos. En esta sección, aprenderá cómo cargar un archivo del almacenamiento local en el directorio raíz de un recurso compartido.

Para crear un archivo y cargar los datos, use los métodos **create\_file\_from\_path**, **create\_file\_from\_stream**, **create\_file\_from\_bytes** o **create\_file\_from\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

**create\_file\_from\_path** carga el contenido de un archivo desde la ruta de acceso especificada, y **create\_file\_from\_stream** carga el contenido desde un archivo o una secuencia ya abiertos. **create\_file\_from\_bytes** carga un conjunto de bytes, y **create\_file\_from\_text** carga el valor de texto especificado usando la codificación especificada (que adopta como valor predeterminado UTF-8).

En el siguiente ejemplo se carga el contenido del archivo **sunset.png** en el archivo **myfile**.

	from azure.storage.file import ContentSettings
	file_service.create_file_from_path(
        'myshare',
				None, # We want to create this blob in the root directory, so we specify None for the directory_name
        'myfile',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png'))

## Creación de directorios

También puede organizar el almacenamiento colocando archivos dentro de los subdirectorios en lugar de mantenerlos a todos en el directorio raíz. El servicio de almacenamiento de archivos de Azure permite crear tantos directorios como lo permita su cuenta. El código siguiente creará un subdirectorio denominado **sampledir** bajo el directorio raíz.

	file_service.create_directory('myshare', 'sampledir')

## Enumeración de archivos y directorios en un recurso compartido

Para mostrar los archivos y los directorios en un recurso compartido, use el método **list\_directories\_and\_files**. Este método devuelve un generador. El código siguiente ofrece el **nombre** de todos los archivos y el directorio de un recurso compartido de la consola.

	generator = file_service.list_directories_and_files('myshare')
	for file_or_dir in generator:
		print(file_or_dir.name)

## Descarga de archivos

Para descargar datos desde un archivo, use **get\_file\_to\_path**, **get\_file\_to\_stream**, **get\_file\_to\_bytes** o **get\_file\_to\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

En el ejemplo siguiente se muestra cómo usar **get\_file\_to\_path** para descargar el contenido del archivo **myfile** y almacenarlo en el archivo **out-sunset.png**:

	file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')

## Eliminación de un archivo

Finalmente, para eliminar un archivo, llame a **delete\_file**.

	file_service.delete_file('myshare', None, 'myfile')

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del Almacenamiento de archivos, siga estos vínculos para obtener más información.

- [Centro para desarrolladores de Python](/develop/python/)
- [API de REST de servicios de almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog del equipo de almacenamiento de Azure]
- [SDK de Almacenamiento de Microsoft Azure para Python]

[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[SDK de Almacenamiento de Microsoft Azure para Python]: https://github.com/Azure/azure-storage-python

<!----HONumber=AcomDC_0224_2016-->