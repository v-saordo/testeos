<properties 
   pageTitle="Copia de datos desde blobs de almacenamiento de Azure a Almacén de Data Lake| Microsoft Azure"
   description="Use la herramienta AdlCopy para copiar datos desde los blobs de almacenamiento de Azure a Almacén de Data Lake." 
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
   ms.date="01/05/2016"
   ms.author="nitinme"/>

# Copia de datos de los blobs de almacenamiento de Azure en el Almacén de Data Lake

El Almacén de Azure Data Lake proporciona una herramienta de línea de comandos, [AdlCopy](http://aka.ms/downloadadlcopy), para copiar datos **desde los blobs de Almacenamiento de Azure al Almacén de Data Lake**. No puede utilizar AdlCopy para copiar datos del Almacén de Data Lake a los blobs de Almacenamiento de Azure.

Puede utilizar la herramienta AdlCopy de dos maneras:

* **Independiente**, donde la herramienta usa recursos de Almacén de Data Lake para realizar la tarea.
* **Con una cuenta de Análisis de Data Lake**, donde las unidades asignadas a la cuenta de Análisis de Data Lake se usan para realizar la operación de copia. Puede que desee usar esta opción cuando quiera realizar las tareas de copia de forma predecible.

##Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Habilite su suscripción de Azure** para la versión de vista previa pública del Almacén de Data Lake. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup). 
- Contenedor de **blobs de almacenamiento de Azure** con algunos datos.
- **Cuenta de Análisis de Azure Data Lake (opcional)**. Consulte [Introducción a Análisis de Azure Data Lake](data-lake-analytics/data-lake-analytics-get-started-portal.md) para obtener instrucciones sobre cómo crear una cuenta de Almacén de Data Lake.
- **Herramienta AdlCopy**. Instale la herramienta AdlCopy desde [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## Sintaxis de la herramienta AdlCopy

Use la siguiente sintaxis para trabajar con la herramienta AdlCopy.

	AdlCopy /Source <Blob source> /Dest <ADLS destination> /SourceKey <Key for Blob account> /Account <ADLA account> /Unit <Number of Analytics units>

A continuación, se describen los parámetros de la sintaxis:

| Opción | Descripción |
|-----------|------------|
| Origen | Especifica la ubicación de los datos de origen en el blob de almacenamiento de Azure. El origen puede ser un blob o un contenedor de blobs. |
| Dest | Especifica el destino de Almacén de Data Lake al que copiar. |
| SourceKey | Especifica la clave de acceso de almacenamiento para el origen de blobs de almacenamiento de Azure. |
| Cuenta | **Opcional**. Utilícelo si desea usar una cuenta de Análisis de Azure Data Lake para ejecutar el trabajo de copia. Si usa la opción /Account en la sintaxis, pero no especifica una cuenta de Análisis de Data Lake, AdlCopy usa una cuenta predeterminada para ejecutar el trabajo. Además, si usa esta opción, debe agregar el origen (blob de almacenamiento de Azure) y el destino (Almacén de Azure Data Lake) como orígenes de datos para la cuenta de Análisis de Data Lake. |
| Unidades | Especifica la cantidad de unidades de Análisis de Data Lake que se usará para el trabajo de copia. Esta opción es obligatoria si usa la opción **/Account** para especificar la cuenta de Análisis de Data Lake.                                                                                                                                                                                                                                                                                                                                               



## Uso de la herramienta AdlCopy de forma independiente

1. Abra un símbolo del sistema y vaya al directorio donde está instalada la herramienta AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.

2. Ejecute el siguiente comando para copiar un blob específico desde el contenedor de origen a un Almacén de Data Lake:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	Por ejemplo:

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== 

	Se le pedirá que escriba las credenciales de la suscripción a Azure en la que tiene la cuenta del Almacén de Data Lake. Verá un resultado similar al siguiente:

		Initializing Copy.
		Copy Started.
		...............
		0.00% data copied.
		. . .
		. . .
		100% data copied.
		Finishing copy.
		....
		Copy Completed.

1. También puede copiar todos los blobs desde un contenedor a la cuenta de Almacén de Data Lake con el siguiente comando:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>		

	Por ejemplo:

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== 

	

## Uso de AdlCopy con la cuenta de Análisis de Data Lake

También puede usar la cuenta de Análisis de Data Lake para ejecutar el trabajo AdlCopy para copiar datos desde los blobs de almacenamiento de Azure al Almacén de Data Lake. Con frecuencia, usaría esta opción cuando los datos que se van a migrar se encuentran en el rango de gigabytes y terabytes y desea obtener un mejor rendimiento predecible.

Para usar la cuenta de Análisis de Data Lake con AdlCopy, se debe agregar el origen (blob de almacenamiento de Azure) y el destino (Almacén de Azure Data Lake) como orígenes de datos para la cuenta de Análisis de Data Lake. Para obtener instrucciones sobre cómo agregar orígenes de datos adicionales a la cuenta de Análisis de Data Lake, consulte [Administración de orígenes de datos de la cuenta de Análisis de Data Lake](data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

Ejecute el siguiente comando:

	AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Por ejemplo:

	AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeaccount /Units 2 

## Facturación

* Si usa la herramienta AdlCopy de forma independiente, se le facturarán los costos de salida por migrar datos, si la cuenta de Almacenamiento de Azure de origen no está en la misma región que el Almacén de Data Lake.

* Si usa la herramienta AdlCopy con la cuenta de Análisis de Data Lake, se aplicarán las [tarifas de facturación de Análisis de Data Lake](https://azure.microsoft.com/pricing/details/data-lake-analytics/) estándar.

## Consideraciones para usar AdlCopy

* AdlCopy no admite la copia de datos de orígenes que tienen más de 1000 archivos y carpetas de forma colectiva. Lo que se podría hacer en este caso es distribuir los archivos o carpetas en subcarpetas diferentes y usar la ruta de acceso a esas subcarpetas como origen en su lugar.

## Pasos siguientes

- [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
- [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](data-lake-analytics-get-started-portal.md)
- [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0107_2016-->