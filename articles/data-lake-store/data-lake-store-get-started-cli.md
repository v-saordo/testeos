<properties 
   pageTitle="Introducción al Almacén de Data Lake mediante la interfaz de línea de comandos multiplataforma | Microsoft Azure"
   description="Uso de la línea de comandos multiplataforma de Azure para crear una cuenta de Almacén de Data Lake y realización de operaciones básicas" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/04/2016"
   ms.author="nitinme"/>

# Introducción al Almacén de Azure Data Lake mediante la línea de comandos de Azure

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)
- [Using Node.js](data-lake-store-manage-use-nodejs.md)

Aprenda a usar la interfaz de la línea de comandos de Azure para crear una cuenta del Almacén de Azure Data Lake y realizar operaciones básicas como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta, etc. Para obtener más información acerca del Almacén de Data Lake, consulte [Información general del Almacén de Data Lake](data-lake-store-overview.md).

La CLI de Azure se implementa en Node.js y se puede usar en cualquier plataforma compatible con Node.js, entre las que se incluyen Windows, Mac y Linux. La CLI de Azure es código abierto. El código fuente se administra en GitHub en <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. Este artículo solo trata del uso de la CLI de Azure con Almacén de Data Lake. Para una guía general de cómo usar la CLI de Azure, vea [Cómo usar la CLI de Azure][azure-command-line-tools].


##Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite su suscripción de Azure** para la versión de vista previa pública del Almacén de Data Lake. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup). 
- **CLI de Azure** - Vea [Instalar y configurar la CLI de Azure](../xplat-cli-install.md) para obtener información de instalación y configuración. Asegúrese de reiniciar el equipo después de instalar la CLI.

##Iniciar sesión en su suscripción de Azure

Siga los pasos que se documentan en [Conexión a una suscripción de Azure desde la interfaz de la línea de comandos de Azure (CLI de Azure)](xplat-cli-connect.md) y conéctese a su suscripción con el método __login__.


## Creación de una cuenta de Almacén de Azure Data Lake

Abra un símbolo del sistema, el shell o sesión de Terminal y ejecute los comandos siguientes.

1. Inicie sesión en la suscripción de Azure:

		azure login

	Se le pedirá que abra una página web y escriba un código de autenticación. Siga las instrucciones de la página para iniciar sesión en su suscripción de Azure.

2. Cambie al modo de Administrador de recursos de Azure con el siguiente comando:

		azure config mode arm


3. Enumere las suscripciones de Azure para su cuenta.

		azure account list


4. Si tiene varias suscripciones de Azure, puede usar el comando siguiente para establecer la suscripción que usarán los comandos de la CLI de Azure:

		azure account set <subscriptionname>

5. Cree un nuevo grupo de recursos. En el siguiente comando, proporcione los valores de los parámetros que desee usar.

		azure group create <resourceGroup> <location>

	Si el nombre de la ubicación contiene espacios, escríbalo entre comillas. Por ejemplo "East US 2".

5. Cree una cuenta de Almacén de Data Lake.

		azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## Creación de carpetas en el Almacén de Data Lake

Puede crear carpetas en su cuenta de Almacén de Azure Data Lake para administrar y almacenar datos. Use el siguiente comando para crear una carpeta denominada "miNuevaCarpeta" en la raíz del Almacén de Data Lake.

	azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Por ejemplo:

	azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## Carga de datos en el Almacén de Data Lake

Puede cargar los datos en el Almacén de Data Lake directamente en el nivel raíz o en la carpeta que creó en la cuenta. Los fragmentos de código siguientes muestran cómo cargar algunos datos de ejemplo en la carpeta (**miNuevaCarpeta**) que creó en la sección anterior.

Si busca datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Descargue el archivo y almacénelo en un directorio local en el equipo, como C:\\sampledata.

	azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Por ejemplo:

	azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## Enumeración de archivos en Almacén de Data Lake

Use el siguiente comando para enumera los archivos de una cuenta de Almacén de Data Lake.

	azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Por ejemplo:

	azure datalake store filesystem list mynewdatalakestore /mynewfolder

La salida de este comando debe ser similar a la siguiente:

	info:    Executing command datalake store filesystem list
	data:    accessTime: 1446245025257
	data:    blockSize: 268435456
	data:    group: NotSupportYet
	data:    length: 1589881
	data:    modificationTime: 1446245105763
	data:    owner: NotSupportYet
	data:    pathSuffix: vehicle1_09142014.csv
	data:    permission: 777
	data:    replication: 0
	data:    type: FILE
	data:    ------------------------------------------------------------------------------------
	info:    datalake store filesystem list command OK

## Cambio del nombre, descarga y eliminación de los datos del Almacén de Data Lake

* **Para cambiar el nombre de un archivo**, use el comando siguiente:

    	azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

	Por ejemplo:

		azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **Para descargar un archivo**, use el comando siguiente: Asegúrese de que la ruta de destino especificada ya existe.

		azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

	Por ejemplo:

		azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **Para eliminar un archivo**, use el comando siguiente:

		azure datalake store filesystem delete <dataLakeStoreAccountName> <path> 

	Por ejemplo:

		azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv
	
	Cuando se le solicite, escriba **Y** para eliminar el elemento.

## Visualización de la lista de control de acceso de una carpeta del Almacén de Data Lake

Use el comando siguiente para ver las ACL en una carpeta del Almacén de Data Lake. En la versión actual, las ACL pueden establecerse solo en la raíz del Almacén de Data Lake. Por lo tanto, el parámetro de la ruta de acceso siguiente siempre será el raíz (/).

	azure datalake store permissions show <dataLakeStoreName> <path>

Por ejemplo:

	azure datalake store permissions show mynewdatalakestore /


## Eliminación de una cuenta del Almacén de Data Lake

Use el comando siguiente para eliminar la cuenta del Almacén de Data Lake.

	azure datalake store account delete <dataLakeStoreAccountName>

Por ejemplo:

	azure datalake store account delete mynewdatalakestore

Cuando se le solicite, escriba **Y** para eliminar la cuenta.

## Otras formas de crear una cuenta de Almacén de Data Lake

- [Introducción al Almacén de Data Lake mediante el Portal](data-lake-store-get-started-portal.md)
- [Introducción al Almacén de Data Lake mediante SDK de .NET](data-lake-store-get-started-net-sdk.md)
- [Introducción al Almacén de Azure Data Lake mediante PowerShell](data-lake-store-get-started-powershell.md)


## Pasos siguientes

- [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
- [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](data-lake-analytics-get-started-portal.md)
- [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md

<!---HONumber=AcomDC_0107_2016-->