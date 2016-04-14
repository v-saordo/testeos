<properties
	pageTitle="Uso del almacenamiento de blobs de Ruby | Microsoft Azure"
	description="Aprenda a usar Almacenamiento de blobs para cargar, descargar, enumerar y eliminar contenido de blobs. Los ejemplos están escritos en Ruby."
	services="storage"
	documentationCenter="ruby"
	authors="tfitzmac"
	manager="wpickett"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="tomfitz"/>


# Uso del almacenamiento de blobs de Ruby

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Información general

En esta guía se muestra cómo realizar algunas tareas comunes con Almacenamiento de blobs. Los ejemplos están escritos usando la API Ruby. Entre los escenarios descritos se incluyen **cargar, enumerar, descargar** y **eliminar** blobs.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creación de una aplicación de Ruby

Cree una aplicación de Ruby. Para obtener instrucciones, consulte [Aplicación web de Ruby on Rails en una máquina virtual de Azure](../virtual-machines/virtual-machines-ruby-rails-web-app-linux.md).

## Configuración de la aplicación para obtener acceso al almacenamiento

Para usar el almacenamiento de Azure tendrá que descargar y usar el paquete Ruby azure, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### Uso de RubyGems para obtener el paquete

1. Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).

2. Escriba "gem install azure" en la ventana de comandos para instalar la gema y las dependencias.

### Importación del paquete

Con el editor de texto que prefiera, agregue lo siguiente al principio del archivo de Ruby en el que pretenda utilizar el almacenamiento:

	require "azure"

## Configuración de una conexión de almacenamiento de Azure

El módulo azure leerá las variables de entorno **AZURE\_STORAGE\_ACCOUNT** y **AZURE\_STORAGE\_ACCESS\_KEY** para obtener información necesaria para conectarse a su cuenta de almacenamiento de Azure. Si no se establecen estas variables de entorno, debe especificar la información de la cuenta antes de usar **Azure::Blob::BlobService** con el siguiente código:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


Para obtener estos valores desde una cuenta de almacenamiento de ARM o clásica en el Portal de Azure:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Vaya a la cuenta de almacenamiento que desea utilizar.
3. En la hoja Configuración de la derecha, haga clic en **Claves de acceso**.
4. En la hoja Claves de acceso que aparece, verá la clave de acceso 1 y 2. Puede usar cualquiera de estas. 
5. Haga clic en el icono de copia para copiar la clave en el Portapapeles. 

Para obtener estos valores desde una cuenta de almacenamiento clásica en el Portal clásico:

1. Inicie sesión en el [Portal clásico](https://manage.windowsazure.com).
2. Vaya a la cuenta de almacenamiento que desea utilizar.
3. Haga clic en **ADMINISTRAR CLAVES DE ACCESO** en la parte inferior del panel de navegación.
4. En el cuadro de diálogo emergente, verá el nombre de cuenta de almacenamiento, la clave de acceso principal y la clave de acceso secundaria. Para la clave de acceso, puede usar la principal o la secundaria. 
5. Haga clic en el icono de copia para copiar la clave en el Portapapeles.

## Crear un contenedor

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

El objeto **Azure::Blob::BlobService** permite trabajar con contenedores y blobs. Para crear un contenedor, use el método**create\_container()**.

En el siguiente ejemplo de código se crea un contenedor o se imprime el error, si hay alguno.

	azure_blob_service = Azure::Blob::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

Si desea hacer públicos los archivos del contenedor, debe configurar los permisos del contenedor.

Puede modificar solo la llamada <strong>create\\_container()</strong> para pasar la opción **:public\\_access\\_level**:

	container = azure_blob_service.create_container("test-container",
	  :public_access_level => "<public access level>")


Los valores válidos para la opción **:public\_access\_level** son:

* **blob:** habilita el acceso de lectura público completo a los datos del contenedor y los blobs. Los clientes pueden enumerar los blobs del contenedor a través de una solicitud anónima, pero no pueden enumerar los contenedores que están en la cuenta de almacenamiento.

* **contenedor:** habilita el acceso de lectura público a los blobs. Los datos de blob dentro de este contenedor pueden leerse a través de una solicitud anónima, pero los datos del contenedor no están disponibles. Los clientes no pueden enumerar los blobs incluidos en el contenedor mediante una solicitud anónima.

También puede modificar el nivel de acceso público de un contenedor usando el método **set\_container\_acl()** para especificarlo.

El ejemplo de código siguiente cambia el nivel de acceso público a **container**:

	azure_blob_service.set_container_acl('test-container', "container")

## Cargar un blob en un contenedor

Para cargar contenido a un blob, use el método **create\_block\_blob()** para crear el blob y use un archivo o una cadena como contenido del blob.

El siguiente código carga el archivo **test.png** como un blob nuevo llamado "image-blob" en el contenedor.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## Enumerar los blobs de un contenedor

Para enumerar los contenedores, use el método **list\_containers()**. Para enumerar los blobs de un contenedor, use el método **list\_blobs()**.

Esta acción obtiene como resultado las URL de todos los blobs en todos los contenedores para la cuenta.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## Descargar blobs

Para descargar blobs, use el método **get\_blob()** para recuperar el contenido.

El ejemplo de código siguiente muestra cómo utilizar **get\_blob()** para descargar el contenido de "image-blob" y escribirlo en un archivo local.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## un blob
Finalmente, para eliminar un blob, use el método **delete\_blob()**. En el siguiente ejemplo de código se muestra cómo eliminar un blob.

	azure_blob_service.delete_blob(container.name, "image-blob")

## Pasos siguientes

Para obtener información acerca de tareas de almacenamiento más complejas, siga estos vínculos:

- [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Repositorio de [SDK de Azure para Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) en GitHub
- [Transferencia de datos con la utilidad en línea de comandos AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0224_2016-->