<properties
	pageTitle="Uso de la API de administración de servicios (Python) - Guía de características"
	description="Vea cómo ejecutar tareas comunes de administración de servicios mediante programación con Python."
	services="cloud-services"
	documentationCenter="python"
	authors="huguesv"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="11/03/2015"
	ms.author="huvalo"/>

# Uso de la administración de servicios con Python

En esta guía se describe cómo ejecutar tareas comunes de administración de servicios mediante programación con Python. La clase **ServiceManagementService** del [SDK de Azure para Python](../python-how-to-install.md) admite el acceso mediante programación a gran parte de la funcionalidad relacionada con la administración de servicios que se encuentra disponible en el [Portal de Azure clásico][management-portal] (como **crear, actualizar y eliminar servicios en la nube, implementaciones, servicios de administración de datos, máquinas virtuales y grupos de afinidad**). Esta funcionalidad puede resultar útil para compilar aplicaciones que precisan de acceso mediante programación a la administración de servicios.

> [AZURE.NOTE] API de administración de servicios se ha sustituido por la nueva API de administración de recursos, actualmente disponible en una versión preliminar. Consulte la [documentación de administración de recursos de Azure](http://azure-sdk-for-python.readthedocs.org/) para obtener más información acerca del uso de la nueva API de administración de recursos de Python.


## <a name="WhatIs"> </a>Qué es la administración de servicios
La API de administración de servicios proporciona acceso mediante programación a gran parte de la funcionalidad de administración de servicios disponible a través del [Portal de Azure clásico][management-portal]. El SDK de Azure para Python le permite administrar los servicios en la nube y las cuentas de almacenamiento.

Para usar la API de administración de servicios, necesita [crear una cuenta de Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Conceptos
El SDK de Azure para Python ajusta la [API de administración de servicios de Azure][svc-mgmt-rest-api], que es una API de REST. Todas las operaciones de la API se realizan mediante SSL y se autentican mutuamente con los certificados X.509 v3. Se puede obtener acceso al servicio de administración desde un servicio que se ejecute en Azure, o bien directamente a través de Internet desde cualquier aplicación que envíe una solicitud HTTPS y reciba una respuesta HTTPS.

## <a name="Connect"> </a>Conexión a la administración de servicios
Para conectarse al extremo de la administración de servicios, necesita el identificador de la suscripción a Azure y un certificado de administración válido. Puede obtener el identificador de la suscripción a través del [Portal de Azure clásico][management-portal].

> [AZURE.NOTE] A partir del SDK de Azure para Python v0.8.0, es posible usar certificados creados con OpenSSL al ejecutar en Windows. Esto requiere Python 2.7.4 o posterior. Recomendamos que los usuarios usen OpenSSL en lugar de .pfx, ya que la compatibilidad con certificados .pfx probablemente se eliminará en el futuro.

### Certificados de administración en Windows/Mac/Linux (OpenSSL)
Puede usar [OpenSSL](http://www.openssl.org/) para crear el certificado de administración. Necesitará crear dos certificados, uno para el servidor (un archivo `.cer`) y otro para el cliente (un archivo `.pem`). Para crear el archivo `.pem`, ejecute este comando:

	`openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem`

Para crear el certificado `.cer`, ejecute este comando:

	`openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer`

Para obtener más información sobre los certificados de Azure, consulte [Información general sobre los certificados de Servicios en la nube de Azure](./cloud-services-certs-create.md). Para obtener una descripción completa de los parámetros de OpenSSL, consulte la documentación en [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Después de haber creado estos archivos, necesitará actualizar el archivo `.cer` en Azure a través de la acción "Cargar" de la pestaña "Configuración" del [Portal de Azure clásico][management-portal]; además, deberá anotar dónde ha guardado el archivo `.pem`.

Tras haber obtenido el identificador de la suscripción, creado un certificado y cargado el archivo `.cer` en Azure, puede conectarse al extremo de administración de Azure; para ello, transfiera el identificador de la suscripción y la ruta de acceso al archivo `.pem` a **ServiceManagementService**:

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = '<path_to_.pem_certificate>'

	sms = ServiceManagementService(subscription_id, certificate_path)

En el ejemplo anterior, `sms` es un objeto **ServiceManagementService**. La clase **ServiceManagementService** es la clase principal usada para administrar los servicios de Azure.

### Certificados de administración en Windows (MakeCert)

Puede crear un certificado de administración autofirmado en la máquina con `makecert.exe`. Abra un **símbolo del sistema de Visual Studio** como **administrador** y use el siguiente comando, donde debe reemplazar *AzureCertificate* por el nombre del certificado que desea usar.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

El comando creará el archivo `.cer` y lo instalará en el almacén de certificados **Personal**. Para obtener más información, consulte [Introducción a los certificados para los servicios en la nube de Azure](./cloud-services-certs-create.md).

Después de haber creado el certificado, necesitará cargar el archivo `.cer` en Azure mediante la acción "Cargar" de la pestaña "Configuración" del [Portal de Azure clásico][management-portal].

Tras haber obtenido el identificador de la suscripción, haber creado un certificado y haber cargado el archivo `.cer` en Azure, puede conectarse al extremo de administración de Azure; para ello, transfiera el identificador de la suscripción y la ubicación del certificado del almacén de certificados **Personal** a **ServiceManagementService** (nuevamente, reemplace *AzureCertificate* por el nombre de su certificado):

	from azure import *
	from azure.servicemanagement import *

	subscription_id = '<your_subscription_id>'
	certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

	sms = ServiceManagementService(subscription_id, certificate_path)

En el ejemplo anterior, `sms` es un objeto **ServiceManagementService**. La clase **ServiceManagementService** es la clase principal usada para administrar los servicios de Azure.

## <a name="ListAvailableLocations"> </a>Enumeración de las ubicaciones disponibles

Para enumerar las ubicaciones disponibles para los servicios hospedados, use el método **list\_locations**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_locations()
	for location in result:
		print(location.name)

Al crear un servicio en la nube o un servicio de almacenamiento, necesitará proporcionar una ubicación válida. El método **list\_locations** siempre devolverá una lista actualizada de las ubicaciones disponibles actualmente. En el momento de la redacción de este documento, las ubicaciones disponibles son:

- Europa occidental
- Europa del Norte
- Sudeste asiático
- Asia oriental
- Central EE. UU.:
- Centro-Norte de EE. UU
- Centro-Sur de EE. UU
- Oeste de EE. UU.
- Este de EE. UU.
- Este de Japón
- Oeste de Japón
- Sur de Brasil
- Australia Oriental
- Sudeste de Australia

## <a name="CreateCloudService"> </a>Creación de un servicio en la nube

Al crear una aplicación y ejecutarla en Azure, al código y a la configuración se les denomina de forma conjunta un [servicio en la nube] de Azure (conocido como un *servicio hospedado* en las versiones anteriores de Azure). El método **create\_hosted\_service** le permite crear un nuevo servicio hospedado, para lo que debe proporcionar un nombre de servicio hospedado (que debe ser exclusivo en Azure), una etiqueta (codificada automáticamente como base 64), una descripción y una ubicación.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myhostedservice'
	label = 'myhostedservice'
	desc = 'my hosted service'
	location = 'West US'

	sms.create_hosted_service(name, label, desc, location)

Puede enumerar todos los servicios hospedados para la suscripción con el método **list\_hosted\_services**:

	result = sms.list_hosted_services()

	for hosted_service in result:
		print('Service name: ' + hosted_service.service_name)
		print('Management URL: ' + hosted_service.url)
		print('Location: ' + hosted_service.hosted_service_properties.location)
		print('')

Si desea obtener información acerca de un servicio hospedado en particular, puede hacerlo si transfiere el nombre del servicio hospedado al método **get\_hosted\_service\_properties**:

	hosted_service = sms.get_hosted_service_properties('myhostedservice')

	print('Service name: ' + hosted_service.service_name)
	print('Management URL: ' + hosted_service.url)
	print('Location: ' + hosted_service.hosted_service_properties.location)

Después de haber creado un servicio en la nube, puede implementar el código en el servicio con el método **create\_deployment**.

## <a name="DeleteCloudService"> </a>Eliminación de un servicio en la nube

Puede eliminar un servicio en la nube si transfiere el nombre del archivo al método **delete\_hosted\_service**:

	sms.delete_hosted_service('myhostedservice')

Tenga en cuenta que antes de eliminar un servicio, primero es necesario eliminar todas las implementaciones de dicho servicio. (Vea [Eliminación de una implementación](#DeleteDeployment) para obtener información detallada).

## <a name="DeleteDeployment"> </a>Eliminación de una implementación

Para eliminar una implementación, use el método **delete\_deployment**. En el ejemplo siguiente se muestra cómo eliminar una implementación llamada `v1`.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Creación de un servicio de almacenamiento

Un [servicio de almacenamiento](../storage/storage-create-storage-account.md) le proporciona acceso a [blobs](../storage/storage-python-how-to-use-blob-storage.md), [tablas](../storage/storage-python-how-to-use-table-storage.md) y [colas](../storage/storage-python-how-to-use-queue-storage.md). Para crear un servicio de almacenamiento, necesita un nombre para el servicio (entre 3 y 24 caracteres en minúscula y exclusivos en Azure), una descripción, una etiqueta (hasta 100 caracteres, codificados automáticamente como base64) y una ubicación. En el ejemplo siguiente se muestra cómo crear un servicio de almacenamiento mediante la definición de una ubicación.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mystorageaccount'
	label = 'mystorageaccount'
	location = 'West US'
	desc = 'My storage account description.'

	result = sms.create_storage_account(name, desc, label, location=location)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

Observe en el ejemplo anterior que el estado de la operación **create\_storage\_account** puede recuperarse transfiriendo el resultado devuelto por **create\_storage\_account** al método **get\_operation\_status**.

Puede enumerar las cuentas de almacenamiento y sus propiedades con el método **list\_storage\_accounts**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_storage_accounts()
	for account in result:
		print('Service name: ' + account.service_name)
		print('Location: ' + account.storage_service_properties.location)
		print('')

## <a name="DeleteStorageService"> </a>Eliminación de un servicio de almacenamiento

Puede eliminar un servicio de almacenamiento si transfiere el nombre de dicho servicio de almacenamiento al método **delete\_storage\_account**. Con la eliminación del servicio de almacenamiento también se eliminarán todos los datos almacenados en el servicio (blobs, tablas y colas).

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Enumeración de los sistemas operativos disponibles

Para enumerar los sistemas operativos que están disponibles para los servicios hospedados, use el método **list\_operating\_systems**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.list_operating_systems()

	for os in result:
		print('OS: ' + os.label)
		print('Family: ' + os.family_label)
		print('Active: ' + str(os.is_active))

De manera alternativa, también puede usar el método **list\_operating\_system\_families**, que agrupa los sistemas operativos por familia:

	result = sms.list_operating_system_families()

	for family in result:
		print('Family: ' + family.label)
		for os in family.operating_systems:
			if os.is_active:
				print('OS: ' + os.label)
				print('Version: ' + os.version)
		print('')

## <a name="CreateVMImage"> </a>Creación de una imagen de sistema operativo

Para agregar una imagen del sistema operativo al repositorio de imágenes, use el método **add\_os\_image**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'mycentos'
	label = 'mycentos'
	os = 'Linux' # Linux or Windows
	media_link = 'url_to_storage_blob_for_source_image_vhd'

	result = sms.add_os_image(label, media_link, name, os)

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

Para enumerar las imágenes del sistema operativo disponibles, use el método **list\_os\_images**. Estas imágenes comprenden todas las imágenes de la plataforma y las imágenes de usuario:

	result = sms.list_os_images()

	for image in result:
		print('Name: ' + image.name)
		print('Label: ' + image.label)
		print('OS: ' + image.os)
		print('Category: ' + image.category)
		print('Description: ' + image.description)
		print('Location: ' + image.location)
		print('Media link: ' + image.media_link)
		print('')

## <a name="DeleteVMImage"> </a>Eliminación de una imagen de sistema operativo

Para eliminar una imagen de usuario, use el método **delete\_os\_image**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	result = sms.delete_os_image('mycentos')

	operation_result = sms.get_operation_status(result.request_id)
	print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Creación de una máquina virtual

Para crear una máquina virtual, primero debe crear un [servicio en la nube](#CreateCloudService). A continuación, cree la implementación de máquina virtual con el método **create\_virtual\_machine\_deployment**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	#Set the location
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	# Name of an os image as returned by list_os_images
	image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-es-ES-30GB.vhd'

	# Destination storage account container/blob where the VM disk
	# will be created
	media_link = 'url_to_target_storage_blob_for_vm_hd'

	# Linux VM configuration, you can use WindowsConfigurationSet
	# for a Windows VM instead
	linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

	os_hd = OSVirtualHardDisk(image_name, media_link)

	sms.create_virtual_machine_deployment(service_name=name,
		deployment_name=name,
		deployment_slot='production',
		label=name,
		role_name=name,
		system_config=linux_config,
		os_virtual_hard_disk=os_hd,
		role_size='Small')

## <a name="DeleteVM"> </a>Eliminación de una máquina virtual

Para eliminar una máquina virtual, primero debe eliminar la implementación con el método **delete\_deployment**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	sms.delete_deployment(service_name='myvm',
		deployment_name='myvm')

El servicio en la nube puede eliminarse entonces con el método**delete\_hosted\_service**:

	sms.delete_hosted_service(service_name='myvm')

##Creación de una máquina virtual a partir de una imagen de máquina virtual capturada.

Para capturar una imagen de máquina virtual, primero llame al método **capture\_vm\_image**:

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	# replace the below three parameters with actual values
	hosted_service_name = 'hs1'
	deployment_name = 'dep1'
	vm_name = 'vm1'

	image_name = vm_name + 'image'
	image = CaptureRoleAsVMImage	('Specialized',
		image_name,
		image_name + 'label',
		image_name + 'description',
		'english',
		'mygroup')

	result = sms.capture_vm_image(
			hosted_service_name,
			deployment_name,
			vm_name,
			image
		)

A continuación, para asegurarse de que ha capturado la imagen correctamente, use la API **list\_vm\_images** y asegúrese de que la imagen se muestra en los resultados:

	images = sms.list_vm_images()

Para crear finalmente la máquina virtual con la imagen capturada, use el método **create\_virtual\_machine\_deployment** como se ha descrito anteriormente, pero esta vez páselo en vm\_image\_name.

	from azure import *
	from azure.servicemanagement import *

	sms = ServiceManagementService(subscription_id, certificate_path)

	name = 'myvm'
	location = 'West US'

	#Set the location
	sms.create_hosted_service(service_name=name,
		label=name,
		location=location)

	sms.create_virtual_machine_deployment(service_name=name,
		deployment_name=name,
		deployment_slot='production',
		label=name,
		role_name=name,
		system_config=linux_config,
		os_virtual_hard_disk=None,
		role_size='Small',
		vm_image_name = image_name)

Para obtener más información acerca de cómo capturar una máquina Virtual Linux, consulte [Captura de una máquina virtual de Linux.](../virtual-machines/virtual-machines-linux-capture-image.md)

Para obtener más información acerca de cómo capturar una máquina virtual de Windows, consulte [Captura de una máquina virtual de Windows](../virtual-machines/virtual-machines-capture-image-windows-server.md).

## <a name="What's Next"> </a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de la administración de servicios, puede tener acceso a la [Documentación de referencia completa de la API para el SDK de Azure para Python](http://azure-sdk-for-python.readthedocs.org/) y realizar tareas complejas fácilmente para administrar la aplicación Python.

Para obtener más información, consulte el [Centro para desarrolladores de Python](/develop/python/).

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[servicio en la nube]: https://azure.microsoft.com/es-ES/documentation/services/cloud-services/

<!---HONumber=AcomDC_0128_2016-->