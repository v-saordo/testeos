<properties
	pageTitle="Usar Azure PowerShell con Almacenamiento de Azure | Microsoft Azure"
	description="Aprenda a usar los cmdlets de Azure PowerShell para el almacenamiento de Azure para crear y administrar cuentas de almacenamiento; trabajar con blobs, tablas, colas y archivos; configurar y consultar el análisis de almacenamiento, y crear firmas de acceso compartido."
	services="storage"
	documentationCenter="na"
	authors="robinsh" 
	manager="carmonm"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/19/2016"
	ms.author="robinsh"/>

# Usar Azure PowerShell con Almacenamiento de Azure

## Información general

Azure PowerShell es un módulo que ofrece cmdlets para administrar Azure mediante Windows PowerShell. Es un Shell de línea de comandos y un lenguaje de scripting basados en tareas y diseñados especialmente para la administración del sistema. Con PowerShell, podrá controlar y automatizar fácilmente la administración de los servicios y aplicaciones de Azure. Por ejemplo, puede usar los cmdlets para realizar las mismas tareas que podría realizar a través del [Portal de Azure](https://portal.azure.com).

En esta guía, exploraremos cómo usar los [Cmdlets de Almacenamiento de Azure](https://msdn.microsoft.com/library/azure/mt269418.aspx) para realizar diversas tareas de desarrollo y administración con el Almacenamiento de Azure.

En esta guía se da por sentado que ya tiene experiencia previa en el uso de [Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/) y de [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx). La guía le proporcionará una serie de scripts para que pueda aprender a usar PowerShell con Almacenamiento de Azure. Antes de ejecutar cada script, deberá actualizar las variables de los mismos según su configuración.

La primera sección de esta guía le permitirá echar un rápido vistazo a Almacenamiento de Azure y a PowerShell. Para obtener información detallada e instrucciones, puede comenzar por los [requisitos previos para usar Azure PowerShell con Almacenamiento de Azure](#prerequisites-for-using-azure-powershell-with-azure-storage).


## Introducción de 5 minutos a Almacenamiento de Azure y PowerShell

En esta sección, aprenderá en 5 minutos a acceder a Almacenamiento de Azure a través de PowerShell.

**Nuevo en Azure:** obtenga una suscripción de Microsoft Azure y una cuenta de Microsoft asociada a dicha suscripción. Para obtener más información sobre las opciones de compra de Azure, consulte [Evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/), [Opciones de compra](https://azure.microsoft.com/pricing/purchase-options/) y [Ofertas para miembros](https://azure.microsoft.com/pricing/member-offers/) (para miembros de MSDN, Microsoft Partner Network, BizSpark y otros programas de Microsoft).

Para obtener más información sobre las suscripciones a Azure, consulte [Asignación de roles de administrador en Azure Active Directory (Azure AD)](https://msdn.microsoft.com/library/azure/hh531793.aspx).

**Después de crear una suscripción y una cuenta de Microsoft Azure:**

1.	Descargue e instale [Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
2.	Inicie el Entorno de scripting integrado (ISE) de Windows PowerShell: en el equipo local, vaya al menú **Inicio**. Escriba **Herramientas administrativas** y haga clic en ella para ejecutarla. En la ventana **Herramientas administrativas**, haga clic con el botón derecho en **Windows PowerShell ISE** y haga clic en **Ejecutar como administrador**.
3.	En **Windows PowerShell ISE**, haga clic en **Archivo** > **Nuevo** para crear un nuevo archivo de script.
4.	A continuación le proporcionaremos un simple script que le mostrará los comandos básicos de PowerShell para poder acceder a Almacenamiento de Azure. El script le pedirá sus credenciales de cuenta de Azure para agregar su cuenta al entorno local de PowerShell. Una vez hecho esto, el script establecerá una suscripción predeterminada de Azure y creará una nueva cuenta de almacenamiento en Azure. Posteriormente, el script creará un nuevo contenedor en la nueva cuenta de almacenamiento y cargará un archivo de imagen existente (blob) en ese contenedor. Una vez el script ha enumerado todos los blobs en ese contenedor, creará un nuevo directorio de destino en el equipo local y descargará el archivo de imagen.
5.	En la siguiente sección de código, seleccione el script que se encuentra entre las notas **#begin** y **#end**. Presione CTRL+C para copiarlo en el portapapeles.

    	#begin
    	# Update with the name of your subscription.
    	$SubscriptionName="YourSubscriptionName"

    	# Give a name to your new storage account. It must be lowercase!
    	$StorageAccountName="yourstorageaccountname"

    	# Choose "West US" as an example.
    	$Location = "West US"

    	# Give a name to your new container.
    	$ContainerName = "imagecontainer"

    	# Have an image file and a source directory in your local computer.
    	$ImageToUpload = "C:\Images\HelloWorld.png"

    	# A destination directory in your local computer.
    	$DestinationFolder = "C:\DownloadImages"

    	# Add your Azure account to the local PowerShell environment.
    	Add-AzureAccount

    	# Set a default Azure subscription.
    	Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

    	# Create a new storage account.
    	New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $Location

    	# Set a default storage account.
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

    	# Create a new container.
    	New-AzureStorageContainer -Name $ContainerName -Permission Off

    	# Upload a blob into a container.
    	Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

    	# List all blobs in a container.
    	Get-AzureStorageBlob -Container $ContainerName

    	# Download blobs from the container:
    	# Get a reference to a list of all blobs in a container.
    	$blobs = Get-AzureStorageBlob -Container $ContainerName

    	# Create the destination directory.
    	New-Item -Path $DestinationFolder -ItemType Directory -Force  

    	# Download blobs into the local destination directory.
    	$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder
    	#end

5.	En el **Windows PowerShell ISE**, presione CTRL+V para copiar el script. Haga clic en **Archivo** > **Guardar**. En el cuadro de diálogo **Guardar como**, escriba el nombre del archivo de script, por ejemplo, "mystoragescript". Haga clic en **Guardar**.

6.	Ahora deberá actualizar las variables del script basadas en la configuración. Debe actualizar la variable **$SubscriptionName** con su propia suscripción. Puede mantener las demás variables tal y como se especifican en el script o actualizarlas a su gusto.

	- **$SubscriptionName:** debe actualizar esta variable con su propia suscripción. Siga una de las tres maneras siguientes para buscar el nombre de su suscripción:

		a. En **Windows PowerShell ISE**, haga clic en **Archivo** > **Nuevo** para crear un nuevo archivo de script. Copie el siguiente script al nuevo archivo de script y haga clic en **Depurar** > **Ejecutar**. El script le pedirá las credenciales de su cuenta de Azure para así agregar su cuenta al entorno local de PowerShell y mostrar todas las suscripciones conectadas a la sesión local de PowerShell. Anote el nombre de la suscripción que desea usar para realizar este tutorial:
		
			Add-AzureAccount
				Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName
		
		b. Para ubicar y copiar el nombre de la suscripción en el [Portal de Azure](https://portal.azure.com), en el menú del concentrador que se encuentra a la izquierda, haga clic en **suscripciones**. Copie el nombre de la suscripción que desee usar para ejecutar los scripts que se proporcionan en esta guía.
		
		![Portal de Azure][Image2]
		  
		c. Para ubicar y copiar el nombre de la suscripción en el [Portal de Azure clásico](https://manage.windowsazure.com/), desplácese hacia abajo y haga clic en la opción **Configuración** que encontrará en el lado izquierdo del portal. Haga clic en **Suscripciones** para ver una lista de las suscripciones. Copie el nombre de la suscripción que desee usar para ejecutar los scripts que se proporcionan en esta guía.
		
		![Portal de Azure clásico][Image1]

	- **$StorageAccountName:** use el nombre proporcionado en el script o escriba un nuevo nombre para la cuenta de almacenamiento. **Importante:** el nombre de la cuenta de almacenamiento debe ser exclusivo en Azure. También debe estar en minúscula.

	- **$Location:** use el comando "Oeste de EE. UU." proporcionado en el script o elija otra ubicación de Azure como "Este de EE. UU", "Norte de Europa", etc.

	- **$ContainerName:** use el nombre proporcionado en el script o escriba un nuevo nombre para el contenedor.

	- **$ImageToUpload:** escriba una ruta de acceso a una imagen en el equipo local, como por ejemplo: "C:\\Images\\HelloWorld.png".

	- **$DestinationFolder:** escriba una ruta de acceso a un directorio local para almacenar los archivos descargados desde Almacenamiento de Azure, como por ejemplo: "C:\\DownloadImages".

7.	Después de actualizar las variables del script en el archivo "mystoragescript.ps1", haga clic en **Archivo** > **Guardar**. A continuación, haga clic en **Depurar** > **Ejecutar** o presione **F5** para ejecutar el script.

Una vez ejecutado el script, debería tener una carpeta de destino local que incluye el archivo de imagen descargado. La siguiente captura de pantalla le muestra un ejemplo del resultado:

![de blobs][Image3]


> [AZURE.NOTE] La sección "Introducción de 5 minutos a Almacenamiento de Azure y PowerShell" le proporcionará una breve introducción sobre cómo usar Azure PowerShell con Almacenamiento de Azure. Para obtener información detallada e instrucciones, le recomendamos que lea las secciones siguientes.

## Requisitos previos para usar Azure PowerShell con Almacenamiento de Azure
Necesita una suscripción de Azure y una cuenta para poder ejecutar los cmdlets de PowerShell que se proporcionan en esta guía, como se describe anteriormente.

Azure PowerShell es un módulo que ofrece cmdlets para administrar Azure mediante Windows PowerShell. Para obtener más información acerca de la instalación y configuración de Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md). Le recomendamos que descargue e instale o que actualice el módulo de Azure PowerShell para tener la versión más reciente, antes de comenzar a usar esta guía.

Puede ejecutar los cmdlets en la consola de PowerShell de Azure, la consola de Windows PowerShell estándar o en el entorno de scripting integrado (ISE) de Windows PowerShell. Por ejemplo, para abrir la **consola de Azure PowerShell**, vaya al menú Inicio, escriba Microsoft Azure PowerShell, haga clic derecho en él y ejecútelo como administrador. Para abrir **Windows PowerShell ISE**, vaya al menú Inicio, escriba Herramientas administrativas y haga clic en la aplicación para ejecutarla. En la ventana Herramientas administrativas, haga clic derecho en Windows PowerShell ISE y haga clic en Ejecutar como administrador.

## Cómo administrar cuentas de almacenamiento en Azure

### Cómo establecer una suscripción predeterminada de Azure
Para administrar Almacenamiento de Azure con Azure PowerShell, necesita autenticar su entorno de cliente con Azure a través de una autenticación basada en certificados o la autenticación de Azure Active Directory. Para obtener información detallada, vea el tutorial [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md). Esta guía usa la autenticación de Azure Active Directory.

1.	En la consola de Azure PowerShell o en Windows PowerShell ISE, escriba el siguiente comando para agregar su cuenta de Azure al entorno local de PowerShell:

    `Add-AzureAccount`

2.	En la ventana "Iniciar sesión en Microsoft Azure", escriba la dirección de correo electrónico y contraseña asociadas a su cuenta. Azure autentica y guarda las credenciales y, a continuación, cierra la ventana.

3.	Seguidamente, ejecute el siguiente comando para ver las cuentas de Azure que están en el entorno local de PowerShell y verifique que su cuenta se encuentra entre ellas:

	`Get-AzureAccount`

4.	Después, ejecute el siguiente cmdlet para ver todas las suscripciones que están conectadas a la sesión local de PowerShell y compruebe que su suscripción aparece entre ellas:

	`Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`

5.	Para establecer una suscripción de Azure predeterminada, ejecute el cmdlet Select-AzureSubscription:

	    $SubscriptionName = 'Your subscription Name'
    	Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

6.	Compruebe el nombre de la suscripción predeterminada cuando ejecute el cmdlet Get-AzureSubscription:

	`Get-AzureSubscription -Default`

7.	Para ver todos los cmdlets de PowerShell disponibles para Almacenamiento de Azure, ejecute:

	`Get-Command -Module Azure -Noun *Storage*`

### Cómo crear una nueva cuenta de almacenamiento de Azure
Para utilizar Almacenamiento de Azure, necesitará una cuenta de almacenamiento. Puede crear una nueva cuenta de almacenamiento de Azure después de configurar el equipo para que pueda conectarse a su suscripción.

1.	Ejecute el cmdlet Get-AzureLocation para buscar todas las ubicaciones de centro de datos disponibles:

    `Get-AzureLocation | format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.	Inmediatamente después, ejecute el cmdlet New-AzureStorageAccount para crear una nueva cuenta de almacenamiento. En el ejemplo siguiente se crea una nueva cuenta de almacenamiento en el centro de datos de la región "Oeste de EE. UU".

    	$location = "West US"
	    $StorageAccountName = "yourstorageaccount"
	    New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $location

> [AZURE.IMPORTANT] El nombre de la cuenta de almacenamiento debe ser único en Azure y estar en minúscula. Para las convenciones de nomenclatura y otras restricciones, consulte [Acerca de las cuentas de Almacenamiento de Azure](storage-create-storage-account.md) y [Convenciones de nomenclatura y referencia de contenedores, blobs y metadatos](http://msdn.microsoft.com/library/azure/dd135715.aspx).

### Cómo configurar una cuenta de almacenamiento de Azure predeterminada
Puede tener varias cuentas de almacenamiento en su suscripción. Puede elegir una de ellas y establecerla como cuenta de almacenamiento predeterminada para todos los comandos de almacenamiento en la misma sesión de PowerShell. Esto le permite ejecutar los comandos de almacenamiento de Azure PowerShell sin especificar explícitamente el contexto de almacenamiento.

1.	Para establecer una cuenta de almacenamiento predeterminada para su suscripción, puede ejecutar el cmdlet Set-AzureSubscription.

		$SubscriptionName = "Your subscription name"
     	$StorageAccountName = "yourstorageaccount"  
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

2.	Seguidamente, ejecute el cmdlet Get-AzureSubscription para comprobar que la cuenta de almacenamiento está asociada a la cuenta de suscripción predeterminada. Este comando devuelve las propiedades de suscripción de la suscripción actual, incluyendo su cuenta de almacenamiento actual.

	    Get-AzureSubscription –Current

### Cómo enumerar todas las cuentas de almacenamiento de Azure en una suscripción
Cada suscripción de Azure puede tener hasta 100 cuentas de almacenamiento. Para obtener la última información sobre los límites, consulte [Suscripción de Azure y límites de servicio, cuotas y restricciones](../azure-subscription-service-limits.md).

Ejecute el siguiente cmdlet para averiguar el nombre y el estado de las cuentas de almacenamiento en la suscripción actual:

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### Cómo crear un contexto de almacenamiento de Azure
El contexto de almacenamiento de Azure es un objeto de PowerShell que sirve para encapsular las credenciales de almacenamiento. Si usa el contexto de almacenamiento mientras ejecuta cualquier cmdlet posterior, podrá autenticar la solicitud sin tener que especificar la cuenta de almacenamiento y su clave de acceso. Puede crear un contexto de almacenamiento de muchas formas; por ejemplo, mediante el uso de la clave de acceso y el nombre de la cuenta de almacenamiento, el token de firma de acceso compartido (SAS), la cadena de conexión o de forma anónima. Para obtener más información, consulte [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx).

Use una de las tres formas que se presentan para crear un contexto de almacenamiento:

- Ejecute el cmdlet [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) para conseguir la clave de acceso de almacenamiento principal de la cuenta de almacenamiento de Azure. A continuación, llame al cmdlet [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn806380.aspx) para crear un contexto de almacenamiento:

    	$StorageAccountName = "yourstorageaccount"
    	$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    	$Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- Genere un token de firma de acceso compartido para un contenedor de almacenamiento de Azure y úselo para crear un contexto de almacenamiento:

    	$sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
    	$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

	Para obtener más información, consulte [New-AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx) y [Firmas de acceso compartido, parte 1: Descripción del modelo de firmas de acceso compartido (SAS)](storage-dotnet-shared-access-signature-part-1.md).

- Es posible que en algunos casos quiera precisar los extremos de servicio cuando crea un nuevo contexto de almacenamiento. Esto puede resultarle necesario si ha registrado un nombre de dominio personalizado para la cuenta de almacenamiento con el servicio Blob o si desea usar una firma de acceso compartido para tener acceso a los recursos de almacenamiento. Deberá configurar los extremos de servicio en una cadena de conexión y usarlos para crear un nuevo contexto de almacenamiento tal como se muestra a continuación:

    	$ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
    	$Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

Para obtener más información sobre cómo configurar una cadena de conexión de almacenamiento, consulte [Configurar cadenas de conexión](storage-configure-connection-string.md).

Ahora ya tiene el equipo configurado y sabe cómo administrar suscripciones y cuentas de almacenamiento con Azure PowerShell. Vaya a la siguiente sección para aprender a administrar los blobs de Azure y las instantáneas de blob.

## Administrar blobs de Azure
El almacenamiento de blobs de Azure es un servicio para almacenar grandes cantidades de datos no estructurados, como texto o datos binarios, a los que puede acceder desde cualquier lugar del mundo a través de HTTP o HTTPS. Para realizar esta sección, supondremos que ya está familiarizado con los conceptos del Servicio de almacenamiento de blobs de Azure. Para obtener más información, consulte [Introducción al Almacenamiento de blobs mediante .NET](storage-dotnet-how-to-use-blobs.md) y [Conceptos del servicio Blob](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### Cómo crear un contenedor
Todos los blobs del almacenamiento de Azure han de estar en un contenedor. Puede crear un contenedor privado usando el cmdlet New-AzureStorageContainer:

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE] Existen tres niveles de acceso de lectura anónimo: **Desactivado**, **Blob** y **Contenedor**. Para evitar el acceso anónimo a los blobs, establezca el parámetro de permiso en **Desactivado**. El nuevo contenedor es privado por defecto y solo puede acceder a él el propietario de la cuenta. Para permitir un acceso de lectura público y anónimo a los recursos de blob, pero no a los metadatos del contenedor ni a la lista de blobs del contenedor, seleccione **Blob** en el parámetro Permiso. Para hacer que el acceso de lectura a los recursos de blob, a los metadatos del contenedor y a la lista de blobs del contenedor sean totalmente públicos, elija **Contenedor** en el parámetro Permiso. Para obtener más información, consulte [Administración del acceso de lectura anónimo a contenedores y blobs](storage-manage-access-to-resources.md).

### Cómo cargar un blob en un contenedor
El almacenamiento de blobs de Azure admite blobs en bloques y en páginas. Para obtener más información, consulte [Introducción a los blobs en bloques, los blobs de anexión y los blobs en páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Para cargar blobs en un contenedor, puede usar el cmdlet [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx). Este comando carga de forma predeterminada los archivos locales a un blob en bloque. Para especificar el tipo de blob, puede usar el parámetro -BlobType.

El siguiente ejemplo ejecuta el cmdlet [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) para obtener todos los archivos en la carpeta especificada y pasarlos al siguiente cmdlet mediante el operador de canalización. El cmdlet [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) carga los archivos locales al contenedor:

    Get-ChildItem –Path C:\Images* | Set-AzureStorageBlobContent -Container "yourcontainername"

### Cómo descargar blobs de un contenedor
En el siguiente ejemplo le mostraremos cómo descargar blobs de un contenedor. Primero, el ejemplo establece una conexión a Almacenamiento de Azure mediante el contexto de cuenta de almacenamiento, en el cual se incluyen el nombre de la cuenta y su clave de acceso principal. Después, el ejemplo recupera una referencia de blob usando el cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx). Seguidamente, el ejemplo usa el cmdlet [Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) para descargar los blobs en la carpeta de destino local.

    #Define the variables.
    $ContainerName = "yourcontainername"
    $DestinationFolder = "C:\DownloadImages"

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Download blobs from a container.
    New-Item -Path $DestinationFolder -ItemType Directory -Force
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### Cómo copiar blobs de un contenedor de almacenamiento a otro
Puede copiar blobs entre cuentas de almacenamiento y regiones de forma asincrónica. En el siguiente ejemplo le mostraremos cómo copiar blobs de un contenedor de almacenamiento a otro, en dos cuentas de almacenamiento diferentes. Primero, el ejemplo establece las variables de las cuentas de almacenamiento de origen y destino para después crear un contexto de almacenamiento para cada cuenta. A continuación, copia los blobs del contenedor de origen al contenedor de destino usando el cmdlet [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx). Recuerde que en este ejemplo se asume que las cuentas de almacenamiento de origen y destino ya existen.

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey

    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey

    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext

    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

Tenga en cuenta que este ejemplo realiza una copia asincrónica. Puede supervisar el estado de cada copia ejecutando el cmdlet [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx).

### Cómo copiar blobs desde una ubicación secundaria
Puede copiar blobs de la ubicación secundaria de una cuenta habilitada con RA-GRS.

    #define secondary storage context using a connection string constructed from secondary endpoints. 
    $SrcContext = New-AzureStorageContext -ConnectionString "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***;BlobEndpoint=http://***-secondary.blob.core.windows.net;FileEndpoint=http://***-secondary.file.core.windows.net;QueueEndpoint=http://***-secondary.queue.core.windows.net; TableEndpoint=http://***-secondary.table.core.windows.net;"
    Start-AzureStorageBlobCopy –Container *** -Blob *** -Context $SrcContext –DestContainer *** -DestBlob *** -DestContext $DestContext

### Cómo eliminar un blob
Para eliminar un blob, primero deberá obtener una referencia de blob y después llamar al cmdlet Remove-AzureStorageBlob que se encuentra en ella. El siguiente ejemplo elimina todos los blobs de un contenedor. Primero, establece las variables de una cuenta de almacenamiento y después crea un contexto de almacenamiento. Seguidamente, el ejemplo recupera una referencia de blob mediante el cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) y ejecuta el cmdlet [Remove-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806399.aspx) para eliminar los blobs de un contenedor en el almacenamiento de Azure.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob

## Cómo administrar las instantáneas de blob de Azure
Azure permite crear una instantánea de un blob. Una instantánea es una versión de solo lectura de un blob que se ha realizado en un momento dado. Una vez se crea la instantánea, puede leerla, copiarla o eliminarla, pero no modificarla. Las instantáneas le ofrecen una oportunidad de realizar una copia de seguridad de un blob en el momento en que éste aparezca. Para obtener más información, consulte [Crear una instantánea de un blob](http://msdn.microsoft.com/library/azure/hh488361.aspx).

### Cómo crear una instantánea de blob
Para crear una instantánea de un blob, deberá conseguir una referencia de blob y después llamar al método `ICloudBlob.CreateSnapshot` que se encuentra en ella. El siguiente ejemplo primero establece las variables de una cuenta de almacenamiento y después crea un contexto de almacenamiento. A continuación, el ejemplo recupera una referencia de blob mediante el cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) y ejecuta el método [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) para crear una instantánea.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName

    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

### Cómo enumerar las instantáneas de un blob
Puede crear tantas instantáneas como desee para un blob. Es más, puede enumerar las instantáneas asociadas al blob para hacer un seguimiento de las instantáneas que tenga en ese momento. El siguiente ejemplo usa un blob predefinido y llama al cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) para así poder enumerar las instantáneas del blob.

    #Define the blob name.
    $BlobName = "yourblobname"

    #List the snapshots of a blob.
    Get-AzureStorageBlob –Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### Cómo copiar una instantánea de un blob
Puede copiar una instantánea de un blob para restaurar la instantánea. Para obtener más información y detalles acerca de las restricciones, consulte [Crear una instantánea de un blob](http://msdn.microsoft.com/library/azure/hh488361.aspx). El siguiente ejemplo primero establece las variables de una cuenta de almacenamiento y después crea un contexto de almacenamiento. A continuación precisa las variables del contenedor y del nombre del blob. El ejemplo recupera una referencia de blob mediante el cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) y ejecuta el método [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) para crear una instantánea. Después, el ejemplo ejecuta el cmdlet [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) para copiar la instantánea de un blob mediante el objeto ICloudBlob del blob de origen. Asegúrese de actualizar las variables según su configuración antes de ejecutar el ejemplo. Tenga en cuenta que en siguiente ejemplo se supone que los contenedores de origen y destino, así como el blob de origen, ya existen.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName

    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy –Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

Ahora que ya sabe cómo administrar los blobs de Azure y las instantáneas de blobs con Azure PowerShell, puede ir a la siguiente sección para aprender a administrar tablas, colas y archivos.

## Cómo administrar las entidades de tabla y las tablas de Azure
El servicio de almacenamiento de tablas Azure es un almacén de datos NoSQL que puede utilizar para almacenar y consultar conjuntos grandes de datos estructurados y no relacionales. Los componentes principales del servicio son tablas, entidades y propiedades. Una tabla es una colección de entidades. Una entidad es un conjunto de propiedades. Cada entidad puede tener hasta 252 propiedades, las cuales son todas par nombre-valor. Para realizar esta sección, supondremos que ya está familiarizado con los conceptos del Servicio de almacenamiento de tablas de Azure. Para obtener más información, consulte [Descripción del modelo de datos del servicio de tabla](http://msdn.microsoft.com/library/azure/dd179338.aspx) y [Introducción al Almacenamiento de tablas de Azure mediante .NET](storage-dotnet-how-to-use-tables.md).

En las subsecciones siguientes, aprenderá a administrar el servicio de almacenamiento de tabla de Azure mediante Azure PowerShell. Los escenarios que aquí se describirán incluyen **crear**, **eliminar** y **recuperar** **tablas**, así como **agregar**, **consultar** y **eliminar entidades de tabla**.

### Cómo crear una tabla
Todas las tablas deberán estar en una cuenta de almacenamiento de Azure. En el siguiente ejemplo le indicaremos cómo crear una tabla en Almacenamiento de Azure. Primero, el ejemplo establece una conexión a Almacenamiento de Azure mediante el contexto de cuenta de almacenamiento, en el cual se incluyen el nombre de la cuenta y su clave de acceso. Seguidamente, usa el cmdlet [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) para crear una tabla en Almacenamiento de Azure.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable –Name $tabName –Context $Ctx

### Cómo recuperar una tabla
Puede consultar y recuperar una o todas las tablas de una cuenta de almacenamiento. En el siguiente ejemplo le indicaremos cómo recuperar una tabla determinada mediante el cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx).

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable –Name $tabName –Context $Ctx

Si llama al cmdlet Get-AzureStorageTable sin parámetros, éste obtendrá todas las tablas de almacenamiento de una cuenta de almacenamiento.

### Cómo eliminar una tabla
Puede eliminar una tabla de una cuenta de almacenamiento usando el cmdlet [Remove-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx).

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable –Name $tabName –Context $Ctx

### Cómo administrar las entidades de tabla
Actualmente, Azure PowerShell no proporciona cmdlets para administrar entidades de tabla de forma directa. Para realizar operaciones en entidades de tabla, puede usar las clases de la [Biblioteca de cliente del Almacenamiento de Azure para .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).

#### Cómo agregar entidades de tabla
Para agregar una entidad a una tabla, primero deberá crear un objeto que defina las propiedades de la entidad. Una entidad puede tener hasta 255 propiedades, incluyendo 3 propiedades de sistema: **PartitionKey**, **RowKey** y **Timestamp**. Usted será el que deba encargarse de insertar y actualizar los valores de **PartitionKey** y **RowKey**. El servidor se encargará de administrar el valor **Timestamp**, así que no podrá modificarlo. Tanto **PartitionKey** como **RowKey**, identifican de forma exclusiva todas las entidades de una tabla.

-	**PartitionKey**: determina la partición en la que se almacena la entidad.
-	**RowKey**: identifica de forma única la entidad dentro de la partición.

Puede definir hasta 252 propiedades personalizadas para una entidad. Para obtener más información, consulte [Descripción del modelo de datos del servicio Tabla](http://msdn.microsoft.com/library/azure/dd179338.aspx).

En el siguiente ejemplo le indicaremos cómo agregar entidades a una tabla. El ejemplo le mostrará cómo recuperar la tabla de empleados y cómo agregar varias entidades. En primer lugar, establece una conexión a Almacenamiento de Azure mediante el contexto de cuenta de almacenamiento, en el cual se incluyen el nombre de la cuenta y su clave de acceso. A continuación, recupera la tabla proporcionada mediante el cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx). Si la tabla no existe, se usará el cmdlet [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) para crear una tabla en Almacenamiento de Azure. Seguidamente, el ejemplo definirá una función Add-Entity personalizada para así poder agregar entidades a la tabla, especificando la partición de cada entidad y su clave de fila. La función Add-Entity llama al cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) que se encuentra en la clase [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) para crear un objeto de entidad. A continuación, el ejemplo llama al método [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) en este objeto de entidad para agregarlo a una tabla.

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity() {
        [CmdletBinding()]
        param(
           $table,
           [String]$partitionKey,
           [String]$rowKey,
           [String]$name,
           [Int]$id
        )

      $entity = New-Object -TypeName Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity -ArgumentList $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)

      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $TableName = "Employees"

    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx -ErrorAction Ignore

    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable –Name $TableName -Context $Ctx
    }

    #Add multiple entities to a table.
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row1 -Name Chris -Id 1
    Add-Entity -Table $table -PartitionKey Partition1 -RowKey Row2 -Name Jessie -Id 2
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row1 -Name Christine -Id 3
    Add-Entity -Table $table -PartitionKey Partition2 -RowKey Row2 -Name Steven -Id 4

#### Cómo consultar entidades de tabla
Para consultar una tabla, use la clase [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx). Para realizar el siguiente ejemplo, tendrá que tener ejecutado el script que se le proporcionó en la sección Cómo agregar entidades de esta guía. Primero, el ejemplo establece una conexión a Almacenamiento de Azure mediante el contexto de almacenamiento, en el cual se incluyen el nombre de la cuenta y su clave de acceso. A continuación, intenta recuperar la tabla "Empleados" que se creó antes mediante el cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx). Al llamar al cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) que se encuentra en la clase Microsoft.WindowsAzure.Storage.Table.TableQuery, se crea un nuevo objeto de consulta. En el ejemplo, buscaremos las entidades que tienen una columna "ID" cuyo valor es 1, tal y como se especificó en un filtro de cadena. Para obtener información detallada, vea [Consultar tablas y entidades](http://msdn.microsoft.com/library/azure/dd894031.aspx). Al ejecutar esta consulta, devolverá todas las entidades que coinciden con los criterios del filtro.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary;
    $TableName = "Employees"

    #Get a reference to a table.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx

    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery

    #Define columns to select.
    $list = New-Object System.Collections.Generic.List[string]
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")

    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20

    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)

    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties[“ID”].Int32Value}} -AutoSize

#### Cómo eliminar entidades de tabla
Puede eliminar una entidad usando sus claves de partición y fila. Para realizar el siguiente ejemplo, tendrá que tener ejecutado el script que se le proporcionó en la sección Cómo agregar entidades de esta guía. Primero, el ejemplo establece una conexión a Almacenamiento de Azure mediante el contexto de almacenamiento, en el cual se incluyen el nombre de la cuenta y su clave de acceso. A continuación, intenta recuperar la tabla "Empleados" que se creó antes mediante el cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx). Si la tabla existe, el ejemplo llama al método [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) para recuperar una entidad basada en sus valores clave de partición y fila. A continuación, pase la entidad al método [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) para eliminarla.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null) {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve(“Partition2”, "Row1"))
       $entity = $tableResult.Result;
    if ($entity -ne $null)
    {
       #Delete the entity.$table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## Cómo administrar las colas de Azure y la cola de mensajes
El almacenamiento en cola de Azure es un servicio para almacenar grandes cantidades de mensajes a los que puede obtenerse acceso desde cualquier lugar del mundo a través de llamadas autenticadas con HTTP o HTTPS. Para realizar esta sección supondremos que ya está familiarizado con los conceptos del Servicio de almacenamiento de colas de Azure. Para obtener más información, consulte [Introducción al Almacenamiento en cola de Azure mediante .NET](storage-dotnet-how-to-use-queues.md).

En esta sección le mostraremos cómo administrar el servicio de almacenamiento de cola de Azure con Azure PowerShell. Entre los escenarios que aquí se describen, se incluyen la **inserción** y la **eliminación** de mensajes de cola, así como la **creación**, **eliminación** y **recuperación de colas**.

### Creación de una cola
En el siguiente ejemplo, primero se establece una conexión a Almacenamiento de Azure mediante el contexto de cuenta de almacenamiento, en el cual se incluyen el nombre de la cuenta y su clave de acceso. A continuación llama al cmdlet [New-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx) para crear una cola llamada "queuename".

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue –Name $QueueName -Context $Ctx

Para obtener información sobre las convenciones de nomenclatura del servicio Cola de Azure, consulte [Nomenclatura de colas y metadatos](http://msdn.microsoft.com/library/azure/dd179349.aspx).

### Cómo recuperar una cola
Puede consultar y recuperar una cola específica o una lista de todas las colas de una cuenta de almacenamiento. En el siguiente ejemplo le mostraremos cómo recuperar una cola específica usando el cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx).

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue –Name $QueueName –Context $Ctx

Si llama al cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) sin parámetros, este obtendrá una lista de todas las colas.

### Como eliminar una cola
Para eliminar una cola y todos los mensajes que contenga, llame al cmdlet Remove-AzureStorageQueue. En el siguiente ejemplo se muestra cómo eliminar una cola específica usando el cmdlet Remove-AzureStorageQueue.

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue –Name $QueueName –Context $Ctx

#### Cómo insertar un mensaje en una cola
Para insertar un mensaje en una cola ya existente, primero deberá crear una instancia nueva de la clase [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx). A continuación, llame al método [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx). Se puede crear un CloudQueueMessage a partir de una cadena (en formato UTF-8) o de una matriz de bytes.

En el siguiente ejemplo le mostraremos cómo agregar un mensaje a una cola. Primero, el ejemplo establece una conexión a Almacenamiento de Azure mediante el contexto de cuenta de almacenamiento, en el cual se incluyen el nombre de la cuenta y su clave de acceso. A continuación, este recupera la cola especificada mediante el cmdlet [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx). Si la cola existe, se usa el cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) para crear una instancia de la clase [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx). Seguidamente, el ejemplo llama al método [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) en este objeto de mensaje para agregarlo a la cola. Aquí tiene el código que recupera una cola e inserta el mensaje 'MessageInfo':

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    #If the queue exists, add a new message.
    if ($Queue -ne $null) {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage -ArgumentList MessageInfo

       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    }


#### Cómo extraer el siguiente mensaje de la cola
El código quita un mensaje de una cola en dos pasos. Cuando llama al método [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx), verá el siguiente mensaje en la cola. Un mensaje devuelto por **GetMessage** se hace invisible a cualquier otro código de lectura de mensajes de esta cola. Para terminar de eliminar el mensaje de la cola, debe llamar también al método [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx). Este proceso extracción de un mensaje que consta de dos pasos garantiza que si su código no puede procesar un mensaje a causa de un error de hardware o software, otra instancia de su código puede obtener el mismo mensaje e intentarlo de nuevo. El código llama a **DeleteMessage** justo después de que se haya procesado el mensaje.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)

    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage)

## Cómo administrar archivos y recursos compartidos de archivos de Azure
El Almacenamiento de archivos de Azure ofrece almacenamiento compartido para aplicaciones que usan el protocolo SMB estándar. Las máquinas virtuales y los servicios en la nube de Microsoft Azure pueden compartir datos de archivo entre componentes de aplicaciones a través de recursos compartidos montados y las aplicaciones locales pueden acceder a datos de archivo de un recurso compartido a través de la API de Almacenamiento de archivos o Azure PowerShell.

Para obtener más información sobre el Almacenamiento de archivos de Azure, consulte [Introducción a Almacenamiento de archivos de Azure en Windows](storage-dotnet-how-to-use-files.md) y [API de REST del servicio de archivos](http://msdn.microsoft.com/library/azure/dn167006.aspx).

## Cómo establecer y consultar el análisis de almacenamiento
Puede usar el [Análisis de almacenamiento de Azure](storage-analytics.md) para recopilar las métricas de sus cuentas de almacenamiento de Azure y de los datos de registro acerca de las solicitudes enviadas a su cuenta de almacenamiento. Puede usar las métricas de almacenamiento para supervisar el estado de una cuenta de almacenamiento y los registros de almacenamiento para diagnosticar y solucionar los problemas que surjan con su cuenta de almacenamiento. Las métricas de almacenamiento no están habilitadas de forma predeterminada para los servicios de almacenamiento. Puede habilitar la supervisión a través del Portal de Azure o Windows PowerShell o mediante programación a través de la biblioteca del cliente de almacenamiento. Los registros de almacenamiento se dan en el lado servidor y le permiten grabar en la cuenta de almacenamiento detalles de solicitudes correctas e incorrectas. Estos registros le permiten no solo ver detalles de lectura y escritura, sino que también le permitirán eliminar operaciones de tablas, colas, blobs y las razones por las cuales las solicitudes fallaron.

Para obtener más información sobre cómo habilitar y ver los datos de las métricas de almacenamiento mediante PowerShell, consulte [Cómo habilitar las métricas de almacenamiento mediante PowerShell](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell).

Para obtener información sobre cómo habilitar y recuperar los datos de registro del almacenamiento con PowerShell, vea [Cómo habilitar el registro de almacenamiento con PowerShell](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell) y [Buscar sus datos de registro de almacenamiento](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata). Para obtener información detallada sobre el uso de las Métricas de almacenamiento y los Registros de almacenamiento para solucionar los problemas de almacenamiento que surjan, consulte [Supervisar, diagnosticar y solucionar problemas en Almacenamiento de Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## Cómo administrar una firma de acceso compartido (SAS) y la directiva de acceso almacenada
Las firmas de acceso compartido son una parte importante del modelo de seguridad de cualquier aplicación que use Almacenamiento de Azure, ya que, entre otras cosas, facilitan permisos limitados a su cuenta de almacenamiento a aquellos clientes que no han de tener la clave de cuenta. De forma predeterminada, solamente el dueño de la cuenta de almacenamiento puede obtener acceso a blobs, tablas y colas en esa cuenta. Si su servicio o aplicación han de facilitar estos recursos a otros clientes sin tener que compartir la clave de acceso, tiene tres opciones:

- Establecer los permisos de un contenedor para permitir el acceso de lectura anónimo al contenedor y a sus blobs. Esto no se permite para tablas o colas.
- Usar una firma de acceso compartido que conceda derechos de acceso restringido a los contenedores, blobs, colas y tablas durante un intervalo concreto de tiempo.
- Usar una directiva de acceso almacenada para obtener un nivel adicional de control sobre las firmas de acceso compartido de un contenedor o sus blobs, de una cola o de una tabla. La directiva de acceso almacenado le permite cambiar la hora de inicio, la hora de finalización, los permisos de una firma o revocarla una vez se ha emitido.

Una firma de acceso compartido puede presentar una de estas dos formas:

- **SAS ad hoc **: cuando se crea una SAS ad hoc, la hora de inicio, la hora de finalización y los permisos para la Firma de acceso compartido se especifican en el URI de la SAS. Este tipo de SAS puede crearse en un contenedor, blob, tabla o cola y no se puede revocar.
- **SAS con directiva de acceso almacenada:** una directiva de acceso almacenada se define en un contenedor de recursos, un contenedor de blobs, una tabla o una cola, y puede usarla para administrar las restricciones de una o varias firmas de acceso compartido. Cuando asocia una SAS a una directiva de acceso almacenada, la SAS hereda las restricciones (hora de inicio, hora de expiración y permisos) definidas para la directiva de acceso almacenada. Este tipo de SAS se puede revocar.

Para obtener más información, consulte [Firmas de acceso compartido: Descripción del modelo de firmas de acceso compartido](storage-dotnet-shared-access-signature-part-1.md) y [Administración del acceso de lectura anónimo a contenedores y blobs](storage-manage-access-to-resources.md).

En las siguientes secciones, obtendrá información sobre cómo crear un token de firma de acceso compartido y una directiva de acceso almacenada para las tablas de Azure. Azure PowerShell también proporciona cmdlets similares para contenedores, blobs y colas. Para ejecutar los scripts de esta sección, descargue [la versión 0.8.14 de Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) o posterior.

### Cómo crear una directiva basada en el token de firma de acceso compartido
Use el cmdlet New-AzureStorageTableStoredAccessPolicy para crear una nueva directiva de acceso almacenada. A continuación, llame al cmdlet [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) para crear un nuevo token de firma de acceso compartido basada en las nuevas directivas, para una tabla de Almacenamiento de Azure.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### Cómo crear un token de firma de acceso compartido ad hoc (no revocable)
Use el cmdlet [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) para crear un nuevo token de firma de acceso compartido ad hoc (no revocable) para una tabla de almacenamiento de Azure:

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### Cómo crear una directiva de acceso almacenada
Use el cmdlet New-AzureStorageTableStoredAccessPolicy para crear una nueva directiva de acceso almacenada para una tabla de almacenamiento de Azure:

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### Cómo actualizar una directiva de acceso almacenada
Use el cmdlet Set-AzureStorageTableStoredAccessPolicy para actualizar una directiva de acceso almacenada existente para una tabla de almacenamiento de Azure:

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### Cómo eliminar una directiva de acceso almacenada
Use el cmdlet Remove-AzureStorageTableStoredAccessPolicy para eliminar una directiva de acceso almacenada en una tabla de almacenamiento de Azure:

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## Cómo usar el servicio Almacenamiento de Azure desarrollado para el gobierno de Estados Unidos y Azure desarrollado para China
Un entorno de Azure es una implementación independiente de Microsoft Azure como, por ejemplo, [Azure Government para el gobierno de EE. UU.](https://azure.microsoft.com/features/gov/), [AzureCloud para el servicio global de Azure](https://portal.azure.com) y [AzureChinaCloud para el servicio de Azure operado por 21Vianet en China](http://www.windowsazure.cn/). Puede implementar el nuevo entorno de Azure para el gobierno de los EE. UU. y Azure para China.

Para usar Almacenamiento de Azure con AzureChinaCloud, necesitará crear un contexto de almacenamiento que esté asociado con AzureChinaCloud. Siga estos pasos para comenzar:

1.	Ejecute el cmdlet [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) para ver los entornos de Azure disponibles:

    `Get-AzureEnvironment`

2.	Agregue una cuenta de Azure para China a Windows PowerShell:

    `Add-AzureAccount –Environment AzureChinaCloud`

3.	Cree un contexto de almacenamiento para una cuenta AzureChinaCloud:

    	$Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

Para usar el almacenamiento de Azure con [Azure Government para EE. UU.](https://azure.microsoft.com/features/gov/), deberá precisar un nuevo entorno y crear un nuevo contexto de almacenamiento con este entorno:

1. Llame al cmdlet [Add-AzureEnvironment](http://msdn.microsoft.com/library/azure/dn790364.aspx) para crear un nuevo entorno de Azure para su centro de datos privado.

    	Add-AzureEnvironment -Name $EnvironmentName -PublishSettingsFileUrl $publishSettingsFileUrl -ServiceEndpoint $serviceEndpoint -ManagementPortalUrl $managementPortalUrl -StorageEndpoint $storageEndpoint -ActiveDirectoryEndpoint $activeDirectoryEndpoint -ResourceManagerEndpoint $resourceManagerEndpoint -GalleryEndpoint $galleryEndpoint -ActiveDirectoryServiceEndpointResourceId $activeDirectoryServiceEndpointResourceId -GraphEndpoint $graphEndpoint -SubscriptionDataFile $subscriptionDataFile

2. Ejecute el cmdlet [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) para crear un nuevo contexto de almacenamiento para este nuevo entorno, tal y como se muestra a continuación.

	    $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment $EnvironmentName

Para más información, consulte:

- [Guía para desarrolladores de Microsoft Azure Government](../azure-government-developer-guide.md).
- [Información general de las diferencias en la creación de una aplicación de servicio de China](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## Pasos siguientes
En esta guía ha aprendido a administrar Almacenamiento de Azure con Azure PowerShell. A continuación encontrará algunos artículos relacionados y recursos para obtener más información acerca de estos servicios.

- [Documentación de Almacenamiento de Azure](https://azure.microsoft.com/documentation/services/storage/)
- [Cmdlets de PowerShell de Almacenamiento de Azure.](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Referencia de Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Getting started with Azure Storage and PowerShell in 5 minutes]: #getstart
[Prerequisites for using Azure PowerShell with Azure Storage]: #pre
[How to manage storage accounts in Azure]: #manageaccount
[How to set a default Azure subscription]: #setdefsub
[How to create a new Azure storage account]: #createaccount
[How to set a default Azure storage account]: #defaultaccount
[How to list all Azure storage accounts in a subscription]: #listaccounts
[How to create an Azure storage context]: #createctx
[How to manage Azure blobs and blob snapshots]: #manageblobs
[How to create a container]: #container
[How to upload a blob into a container]: #uploadblob
[How to download blobs from a container]: #downblob
[How to copy blobs from one storage container to another]: #copyblob
[How to delete a blob]: #deleteblob
[How to manage Azure blob snapshots]: #manageshots
[How to create a blob snapshot]: #createshot
[How to list snapshots of a blob]: #listshot
[How to copy a snapshot of a blob]: #copyshot
[How to manage Azure tables and table entities]: #managetables
[How to create a table]: #createtable
[How to retrieve a table]: #gettable
[How to delete a table]: #remtable
[How to manage table entities]: #mngentity
[How to add table entities]: #addentity
[How to query table entities]: #queryentity
[How to delete table entities]: #deleteentity
[How to manage Azure queues and queue messages]: #managequeues
[How to create a queue]: #createqueue
[How to retrieve a queue]: #getqueue
[How to delete a queue]: #remqueue
[How to manage queue messages]: #mngqueuemsg
[How to insert a message into a queue]: #addqueuemsg
[How to de-queue at the next message]: #dequeuemsg
[How to manage Azure file shares and files]: #files
[How to set and query storage analytics]: #stganalytics
[How to manage Shared Access Signature (SAS) and Stored Access Policy]: #sas
[How to use Azure Storage for U.S. government and Azure China]: #gov
[Next Steps]: #next
 

<!---HONumber=AcomDC_0224_2016-->