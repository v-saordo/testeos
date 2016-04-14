<properties 
   pageTitle="Introducción a Análisis de Azure Data Lake mediante Interfaz de línea de comandos (CLI) de Azure | Microsoft Azure" 
   description="Aprenda a usar la interfaz de línea de comandos de Azure para crear una cuenta de Almacén de Data Lake, crear un trabajo de Análisis de Data Lake mediante U-SQL y enviar el trabajo." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/10/2016"
   ms.author="jgao"/>

# Tutorial: Introducción a Análisis de Azure Data Lake mediante Interfaz de línea de comandos (CLI) de Azure

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Aprenda a usar la CLI de Azure para crear cuentas de Análisis de Azure Data Lake, definir trabajos de Análisis de Data Lake en [U-SQL](data-lake-analytics-u-sql-get-started.md) y enviar trabajos a cuentas de Análisis de Data Lake. Para obtener más información acerca de Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

En este tutorial, desarrollará un trabajo que lee un archivo de valores separados por tabulaciones (TSV) y lo convierte en un otro de valores separados por comas (CSV). Para recorrer el mismo tutorial con otras herramientas compatibles, haga clic en las pestañas situadas en la parte superior de esta sección.

**Proceso básico de Análisis de Data Lake:**

![Diagrama de flujo de proceso de Análisis de Azure Data Lake](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. Cree una cuenta de Análisis de Data Lake.
2. Prepare los datos de origen. Los trabajos de Análisis de Data Lake pueden leer datos desde cuentas de Almacén de Azure Data Lake o desde cuentas de almacenamiento de blobs de Azure.   
3. Desarrolle un script U-SQL.
4. Envíe un trabajo (script U-SQL) a la cuenta de Análisis de Data Lake. El trabajo lee desde el origen de datos, procesa los datos como se indica en el script U-SQL y después guarda la salida en una cuenta de Almacén de Data Lake o de almacenamiento de blobs.

**Requisitos previos**

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **CLI de Azure** Consulte [Instalación y configuración de la CLI de Azure](../xplat-cli-install.md).
	- Descargue e instale la **versión preliminar** de las [herramientas de la CLI de Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases) para completar esta demostración.
- **Autenticación**, mediante el comando siguiente:

		azure login
	Para obtener más información acerca de cómo autenticarse con una cuenta profesional o educativa, consulte [Conexión a una suscripción de Azure desde la CLI de Azure](xplat-cli-connect.md).
- **Cambiar al modo de Administrador de recursos de Azure** con el siguiente comando:

		azure config mode arm
		
## Creación de una cuenta de Análisis de Data Lake

Debe tener una cuenta de Análisis de Data Lake para poder ejecutar trabajos. Para crear una cuenta de Análisis de Data Lake, debe especificar lo siguiente:

- **Grupo de recursos de Azure**: se debe crear una cuenta de Análisis de Data Lake dentro de un grupo de recursos de Azure. El [Administrador de recursos de Azure](../resource-group-overview.md) permite trabajar con los recursos de la aplicación como un grupo. Puede implementar, actualizar o eliminar todos los recursos de la aplicación en una operación única y coordinada.  

	Para enumerar los grupos de recursos para la suscripción:
    
    	azure group list 
    
	Para crear un nuevo grupo de recursos:

		azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Nombre de la cuenta de Análisis de Data Lake**
- **Ubicación**: uno de los centros de datos de Azure que admite Análisis de Data Lake.
- **Cuenta predeterminada de Data Lake**: cada cuenta de Análisis de Data Lake tiene una cuenta predeterminada de Data Lake.

	Para enumerar la cuenta de Data Lake existente:
	
		azure datalake store account list

	Para crear una nueva cuenta de Data Lake:

		azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"

	> [AZURE.NOTE] El nombre de cuenta de Data Lake solo debe contener letras minúsculas y números.



**Para crear una cuenta de Análisis de Data Lake**

		azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

		azure datalake analytics account list
		azure datalake analytics account show "<Data Lake Analytics Account Name>"			

![Cuenta de Análisis de Data Lake](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [AZURE.NOTE] El nombre de cuenta de Análisis de Data Lake solo debe contener letras minúsculas y números.


## Carga de datos en el Almacén Data Lake

En este tutorial, va a procesar algunos registros de búsqueda. El registro de búsqueda se puede almacenar en el Almacén de Data Lake o en el almacenamiento de blobs de Azure.

El Portal de Azure proporciona una interfaz de usuario para copiar algunos archivos de datos de ejemplo a la cuenta predeterminada de Data Lake, entre los que se incluye un archivo de registro de búsqueda. Consulte [Preparar los datos de origen](data-lake-analytics-get-started-portal.md#prepare-source-data) para cargar los datos en la cuenta del Almacén Data Lake.

Para cargar archivos con la CLI, use el siguiente comando:

  	azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
  	azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Análisis de Data Lake también puede acceder al almacenamiento de blobs de Azure. Para cargar datos al almacenamiento de blobs de Azure, consulte [Uso de la CLI de Azure con Almacenamiento de Azure](../storage/storage-azure-cli.md).

## Envío de trabajos de Análisis de Data Lake

Los trabajos de Análisis de Data Lake se escriben en el lenguaje U-SQL. Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL](data-lake-analytics-u-sql-get-started.md) y [Referencia sobre el lenguaje U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Para crear un script de trabajo de Análisis de Data Lake**

- Cree un archivo de texto con el siguiente script U-SQL y guarde el archivo de texto en la estación de trabajo:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Este script U-SQL lee el archivo de datos de origen mediante **Extractors.Tsv()** y después crea un archivo csv mediante **Outputters.Csv()**.
    
    No modifique ninguna de las dos rutas a menos que copie el archivo de origen en una ubicación diferente. Análisis de Data Lake creará la carpeta de salida si no existe.
	
	Es más fácil usar rutas de acceso relativas para los archivos almacenados en cuentas predeterminadas de Data Lake. También puede usar rutas de acceso absolutas. Por ejemplo:
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Debe usar rutas de acceso absolutas para acceder a los archivos de cuentas de almacenamiento vinculadas. La sintaxis de los archivos almacenados en la cuenta de Almacenamiento de Azure vinculada es:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Los contenedores de blobs de Azure con permisos de acceso de contenedores públicos o blobs públicos no se admiten actualmente.

	
**Para enviar el trabajo**


	azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"
    
    
Los siguientes comandos se pueden usar para enumerar los trabajos, obtener detalles de los trabajos y cancelar trabajos:

  	azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
  	azure datalake analytics job list "<Data Lake Analytics Account Name>"
	azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

Después de finalizar el trabajo, puede usar los siguientes cmdlets para mostrar el archivo y descargarlo:
	
    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
	azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
	azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## Consulte también

- Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestañas en la parte superior de la página.
- Para ver una consulta más compleja, consulte [Análisis de registros de sitios web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
- Para empezar a desarrollar aplicaciones con U-SQL, consulte [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
- Para las tareas de administración, consulte [Administración de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-manage-use-portal.md).
- Para obtener información general sobre Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0302_2016-->