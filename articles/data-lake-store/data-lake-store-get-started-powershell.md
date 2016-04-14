<properties
   pageTitle="Introducción al Almacén de Data Lake | Azure"
   description="Uso de Azure PowerShell para crear una cuenta de Almacén de Data Lake y realización de operaciones básicas"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/04/2016"
   ms.author="nitinme"/>

# Introducción al Almacén de Azure Data Lake mediante Azure PowerShell

> [AZURE.SELECTOR]
- [Uso del Portal](data-lake-store-get-started-portal.md)
- [Uso de PowerShell](data-lake-store-get-started-powershell.md)
- [Uso del SDK de .NET](data-lake-store-get-started-net-sdk.md)
- [Uso de la CLI de Azure](data-lake-store-get-started-cli.md)
- [Uso de Node.js](data-lake-store-manage-use-nodejs.md)

Aprenda a usar Azure PowerShell para crear una cuenta del Almacén de Azure Data Lake y realizar operaciones básicas como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta, etc. Para obtener más información acerca del Almacén de Data Lake, consulte [Información general del Almacén de Data Lake](data-lake-store-overview.md).

## Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite su suscripción de Azure** para la versión de vista previa pública del Almacén de Data Lake. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup).


##Instalar Azure PowerShell 1.0 o versiones posteriores

Consulte la sección de requisitos previos de [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md#prerequisites).

## Creación de una cuenta de Almacén de Azure Data Lake

1. Desde el escritorio, abra una nueva ventana de Azure PowerShell y escriba el siguiente fragmento de código para iniciar sesión en su cuenta de Azure, establecer la suscripción y registrar el proveedor del Almacén de Data Lake. Cuando se le solicite iniciar sesión, asegúrese de iniciarla como uno de los administradores o propietario de la suscripción:

        # Log in to your Azure account
		Login-AzureRmAccount

		# List all the subscriptions associated to your account
		Get-AzureRmSubscription

		# Select a subscription
		Set-AzureRmContext -SubscriptionId <subscription ID>

		# Register for Azure Data Lake Store
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"


2. La cuenta de Almacén de Azure Data Lake se asocia con un grupo de recursos de Azure. Comience creando un grupo de recursos de Azure.

		$resourceGroupName = "<your new resource group name>"
    	New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

	![Creación de un grupo de recursos de Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Creación de un grupo de recursos de Azure")

2. Cree una cuenta del Almacén de Azure Data Lake. El nombre que especifique debe contener solo letras minúsculas y números.

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![Creación de una cuenta de Almacén de Azure Data Lake](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Creación de una cuenta de Almacén de Azure Data Lake")

3. Compruebe que la cuenta se creó correctamente.

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	El resultado debe ser **True**.

## Creación de estructuras de directorios en el Almacén de Azure Data Lake

Puede crear directorios bajo su cuenta de Almacén de Azure Data Lake para administrar y almacenar datos.

1. Especifique un directorio raíz.

		$myrootdir = "/"

2. Cree un nuevo directorio denominado **mynewdirectory** en la raíz especificada.

		New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. Compruebe que el nuevo directorio se creó correctamente.

		Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

	Debe mostrar un resultado parecido al siguiente:

	![Comprobación del directorio](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Comprobación del directorio")


## Carga de datos en el Almacén de Azure Data Lake

Puede cargar los datos en el Almacén de Data Lake directamente en el nivel raíz o en un directorio que creó en la cuenta. Los fragmentos de código siguientes muestran cómo cargar datos de ejemplo en el directorio (**mynewdirectory**) que creó en la sección anterior.

Si busca algunos datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Descargue el archivo y almacénelo en un directorio local en el equipo, como C:\\sampledata.

	Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## Cambio del nombre, descarga y eliminación de los datos del Almacén de Data Lake

Para cambiar el nombre de un archivo, use el comando siguiente:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Para descargar un archivo, use el comando siguiente:

	Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Para eliminar un archivo, use el comando siguiente:

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Cuando se le solicite, escriba **Y** para eliminar el elemento. Si tiene más de un archivo para eliminar, puede proporcionar todas las rutas de acceso separadas por comas.

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## Eliminación de una cuenta del Almacén de Azure Data Lake

Use el siguiente comando para eliminar la cuenta del Almacén de Data Lake.

	Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Cuando se le solicite, escriba **Y** para eliminar la cuenta.


## Otras formas de crear una cuenta de Almacén de Data Lake

- [Introducción al Almacén de Data Lake mediante el Portal](data-lake-store-get-started-portal.md)
- [Introducción al Almacén de Data Lake mediante SDK de .NET](data-lake-store-get-started-net-sdk.md)
- [Introducción al Almacén de Data Lake mediante la CLI de Azure](data-lake-store-get-started-cli.md)


## Pasos siguientes

- [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
- [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0309_2016-->