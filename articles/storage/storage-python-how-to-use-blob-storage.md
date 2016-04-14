<properties
	pageTitle="Uso del almacenamiento de blobs de Azure desde Python | Microsoft Azure"
	description="Aprenda a usar el servicio de almacenamiento Blob de Azure desde Python para cargar, enumerar, descargar y eliminar blobs."
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
	ms.date="02/29/2016"
	ms.author="jehine"/>

# Uso del almacenamiento de blobs de Azure desde Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Información general

Este artículo le muestra cómo realizar algunas tareas comunes con el almacenamiento de blobs. Los ejemplos están escritos en Python y usan el [SDK de Almacenamiento de Microsoft Azure para Python]. Entre los escenarios descritos se incluyen cargar, enumerar, descargar y eliminar blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Crear un contenedor

Según el tipo de blob que desee usar, cree un objeto **BlockBlobService**, **AppendBlobService** o **PageBlobService**. El código siguiente usa un objeto **BlockBlobService**. Agregue lo siguiente cerca de la parte superior de todo archivo Python en el que desee obtener acceso al almacenamiento de blobs en bloques de Azure mediante programación.

	from azure.storage.blob import BlockBlobService

El código siguiente crea un objeto **BlockBlobService** usando la clave de la cuenta y el nombre de la cuenta de almacenamiento. Reemplace 'myaccount' y 'mykey' por la clave y el nombre de cuenta.

	block_blob_service = BlockBlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

En el siguiente ejemplo de código, puede usar un objeto **BlockBlobService** para crear el contenedor si no existe:

	block_blob_service.create_container('mycontainer')

De manera predeterminada, el nuevo contenedor es privado, por lo que tiene que especificar su clave de acceso de almacenamiento (como hizo anteriormente) para descargar blobs de él. Si desea poner los blobs del contenedor a disposición de todo el mundo, puede crear el contenedor y pasar al nivel de acceso público usando el código siguiente.

	from azure.storage.blob import PublicAccess
	block_blob_service.create_container('mycontainer', public_access=PublicAccess.Container)

También tiene la posibilidad de modificar un contenedor después de haberlo creado mediante el siguiente código.

	block_blob_service.set_container_acl('mycontainer', public_access=PublicAccess.Container)

Después de este cambio, cualquier usuario de Internet podrá ver los blobs de los contenedores públicos, pero solo usted podrá modificarlos o eliminarlos.

## Cargar un blob en un contenedor

Para crear un blob en bloques y cargar los datos, use los métodos **create\_blob\_from\_path**, **create\_blob\_from\_stream**, **create\_blob\_from\_bytes** o **create\_blob\_from\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

**create\_blob\_from\_path** carga el contenido de un archivo desde la ruta de acceso especificada y **create\_blob\_from\_stream** carga el contenido desde un archivo o una transmisión ya abiertos. **create\_blob\_from\_bytes** carga un conjunto de bytes y **create\_blob\_from\_text** carga el valor de texto especificado usando la codificación especificada (que adopta como valor predeterminado UTF-8).

En el siguiente ejemplo se carga el contenido del archivo **sunset.png** en el blob **myblob**.

	from azure.storage.blob import ContentSettings
	block_blob_service.create_blob_from_path(
        'mycontainer',
        'myblockblob',
        'sunset.png',
        content_settings=ContentSettings(content_type='image/png')
				)

## Enumerar los blobs de un contenedor

Para enumerar los blobs de un contenedor, use el método **list\\_blobs**. Este método devuelve un generador. El código siguiente ofrece el **nombre** de todos los blobs de un contenedor a la consola.

	generator = block_blob_service.list_blobs('mycontainer')
	for blob in generator:
		print(blob.name)

## Descargar blobs

Para descargar datos de un blob, use **get\_blob\_to\_path**, **get\_blob\_to\_stream**, **get\_blob\_to\_bytes** o **get\_blob\_to\_text**. Se trata de métodos de alto nivel que realizan la fragmentación necesaria cuando el tamaño de los datos supera los 64 MB.

En el ejemplo siguiente se muestra cómo usar **get\_blob\_to\_path** para descargar el contenido del blob **myblob** y almacenarlo en el archivo **out-sunset.png**:

	block_blob_service.get_blob_to_path('mycontainer', 'myblockblob', 'out-sunset.png')

## Eliminar un blob

Finalmente, para eliminar un blob, llame a **delete\_blob**.

	block_blob_service.delete_blob('mycontainer', 'myblockblob')

## Escritura en un blob en anexos

Un blob en anexos se optimiza para las operaciones de anexado, como el registro. Como un blob en bloques, un blob en anexos se compone también de bloques, pero en el caso del blob en anexos cuando se agrega un nuevo bloque, siempre se anexa al final del blob. No se puede actualizar o eliminar un bloque existente en un blob en anexos. Los identificadores de bloque para un blob en anexos no está expuestos como lo están en el caso de los blobs en bloques.

Cada bloque en un blob en anexos puede tener un tamaño diferente, hasta un máximo de 4 MB y el blob puede incluir un máximo de 50.000 bloques. El tamaño máximo de un blob en anexos es, por tanto, ligeramente superior a 195 GB (4 MB X 50.000 bloques).

El ejemplo siguiente crea un nuevo blob en anexos y le anexa algunos datos, para simular una operación de registro simple.

	from azure.storage.blob import AppendBlobService
	append_blob_service = AppendBlobService(account_name='myaccount', account_key='mykey')

	# The same containers can hold all types of blobs
	append_blob_service.create_container('mycontainer')

	# Append blobs must be created before they are appended to
	append_blob_service.create_blob('mycontainer', 'myappendblob')
	append_blob_service.append_blob_from_text('mycontainer', 'myappendblob', u'Hello, world!')

	append_blob = append_blob_service.get_blob_to_text('mycontainer', 'myappendblob')

## Pasos siguientes

Ahora que está familiarizado con los aspectos básicos del Almacenamiento de blobs, siga estos vínculos para obtener más información.

- [Centro para desarrolladores de Python](/develop/python/)
- [API de REST de servicios de almacenamiento de Azure](http://msdn.microsoft.com/library/azure/dd179355)
- [Blog del equipo de almacenamiento de Azure]
- [SDK de Almacenamiento de Microsoft Azure para Python]

[Blog del equipo de almacenamiento de Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[SDK de Almacenamiento de Microsoft Azure para Python]: https://github.com/Azure/azure-storage-python

<!---HONumber=AcomDC_0302_2016-->