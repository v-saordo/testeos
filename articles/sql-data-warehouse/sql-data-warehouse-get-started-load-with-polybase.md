<properties
   pageTitle="Tutorial sobre PolyBase en Almacenamiento de datos SQL | Microsoft Azure"
   description="Obtenga información sobre qué es PolyBase y cómo usarlo en escenarios de almacenamiento de datos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="sahajs;barbkess;jrj;sonyama"/>


# Carga de datos con PolyBase en Almacenamiento de datos SQL

> [AZURE.SELECTOR]
- [Factoría de datos](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

Este tutorial muestra cómo cargar datos en Almacenamiento de datos SQL mediante AzCopy y PolyBase. Cuando termine, sabrá cómo:

- Usar AzCopy para copiar datos al almacenamiento de blobs de Azure
- Crear objetos de base de datos para definir los datos externos
- Ejecutar una consulta de T-SQL para cargar los datos

>[AZURE.VIDEO loading-data-with-polybase-in-azure-sql-data-warehouse]

## Requisitos previos

Para seguir paso a paso este tutorial, necesita:

- Una base de datos de Almacenamiento de datos SQL.
- Una cuenta de almacenamiento de Azure del tipo Almacenamiento con redundancia local estándar (LRS estándar), Almacenamiento con redundancia geográfica estándar (GRS estándar) o Almacenamiento con redundancia geográfica de acceso de lectura estándar (RAGRS estándar).
- La utilidad de línea de comandos AzCopy. Descargue e instale la [versión más reciente de AzCopy][], que se instala con las herramientas de Almacenamiento de Microsoft Azure.

    ![Herramientas de almacenamiento de Azure](./media/sql-data-warehouse-get-started-load-with-polybase/install-azcopy.png)


## Paso 1: Adición de datos de ejemplo a Almacenamiento de blobs de Azure

Para cargar los datos, es preciso poner incluir algunos datos de ejemplo en un almacenamiento de blobs de Azure. En este paso, se rellena un almacenamiento de blobs de Azure con datos de ejemplo. Más adelante, se usará PolyBase para cargar estos datos de ejemplo en la base de datos de Almacenamiento de datos SQL.

### A. Preparación de un archivo de texto de ejemplo

Para preparar un archivo de texto de ejemplo:

1. Abra el Bloc de notas y copie las siguientes líneas de datos en un nuevo archivo. Guárdelo en un directorio temporal local como %temp%\\DimDate2.txt.

    ```
    20150301,1,3
    20150501,2,4
    20151001,4,2
    20150201,1,3
    20151201,4,2
    20150801,3,1
    20150601,2,4
    20151101,4,2
    20150401,2,4
    20150701,3,1
    20150901,3,1
    20150101,1,3
    ```

### B. Búsqueda del punto de conexión del servicio BLOB

Para buscar el punto de conexión del servicio BLOB:

1. En el Portal de Azure clásico, seleccione **Examinar** > **Cuentas de almacenamiento**.
2. Haga clic en la cuenta de almacenamiento que desea usar.
3. En la hoja de la cuenta de almacenamiento, haga clic en Blobs.

    ![Haga clic en Blobs](./media/sql-data-warehouse-get-started-load-with-polybase/click-blobs.png)

1. Guardar la dirección URL del punto de conexión del servicio BLOB para usarla más adelante.

    ![Punto de conexión del servicio BLOB](./media/sql-data-warehouse-get-started-load-with-polybase/blob-service.png)

### C. Búsqueda de la clave de almacenamiento de Azure

Para buscar la clave de almacenamiento de Azure:

1. En el pantalla principal, seleccione **Examinar** > **Cuentas de almacenamiento**.
2. Haga clic en la cuenta de almacenamiento que desea usar.
3. Seleccione **Toda la configuración** > **Claves de acceso**.
4. Haga clic en el cuadro de copia para copiar una de las claves de acceso en el Portapapeles.

    ![Copie la clave de almacenamiento de Azure](./media/sql-data-warehouse-get-started-load-with-polybase/access-key.png)

### D. Copia del archivo de ejemplo en Almacenamiento de blobs de Azure

Para copiar los datos al almacenamiento de blobs de Azure:

1. Abra un símbolo del sistema y cambie al directorio de instalación de AzCopy. En los clientes con Windows de 64 bits, este comando cambia al directorio de instalación predeterminado.

    ```
    cd /d "%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy"
    ```

1. Ejecute el siguiente comando para cargar el archivo. Especifique la dirección URL del punto de conexión del servicio BLOB de <blob service endpoint URL> y la clave de la cuenta de almacenamiento de Azure de <azure_storage_account_key>.

    ```
    .\AzCopy.exe /Source:C:\Temp\ /Dest:<blob service endpoint URL> /datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
    ```

Consulte [Introducción a la utilidad de línea de comandos de AzCopy][]

### E. Exploración del contenedor de almacenamiento de blobs

Para ver el archivo cargado en el almacenamiento de blobs:

1. Vuelva a la hoja del servicio BLOB.
2. En Contenedores, haga doble clic en **datacontainer**.
3. Para explorar la ruta de acceso a los datos, haga clic en la carpeta **datedimension** y verá el archivo cargado, **DimDate2.txt**.
4. Para ver sus propiedades, haga clic en **DimDate2.txt**.
5. Tenga en cuenta que en la hoja de propiedades del blob, puede descargar o eliminar el archivo.

    ![Vea el blob de almacenamiento de Azure](./media/sql-data-warehouse-get-started-load-with-polybase/view-blob.png)


## Paso 2: Creación de una tabla externa para los datos de ejemplo

En esta sección se creará una tabla externa que defina los datos de ejemplo.

PolyBase utiliza tablas externas para acceder a los datos de Almacenamiento de blobs de Azure o Hadoop. Dado que los datos no se almacenan en Almacenamiento de datos SQL, PolyBase controla la autenticación de los datos externos mediante una credencial cuyo ámbito es la base de datos.

El ejemplo de este paso usa estas instrucciones Transact-SQL para crear una tabla externa.

- [Create Master Key (Transact-SQL)][] para cifrar el secreto de la credencial cuyo ámbito es la base de datos.
- [Create Database Scoped Credential (Transact-SQL)][] para especificar la información de autenticación de la cuenta de almacenamiento de Azure.
- [Create External Data Source (Transact-SQL)][] para especificar la ubicación del almacenamiento de blobs de Azure.
- [Create External File Format (Transact-SQL)][] para especificar el formato de los datos.
- [Create External Table (Transact-SQL)][] para especificar la definición de la tabla y la ubicación de los datos.

Ejecute esta consulta en la base de datos de Almacenamiento de datos SQL. Se creará una tabla externa denominada DimDate2External en el esquema dbo que apunta a los datos de ejemplo de DimDate2.txt del almacenamiento de blobs de Azure.


```
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);


-- D: Create an external file format
-- FORMAT_TYPE: Type of file format in Azure storage (supported: DELIMITEDTEXT, RCFILE, ORC, PARQUET).
-- FORMAT_OPTIONS: Specify field terminator, string delimiter, date format etc. for delimited text files.
-- Specify DATA_COMPRESSION method if data is compressed.

CREATE EXTERNAL FILE FORMAT TextFile
WITH (
    FORMAT_TYPE = DelimitedText,
    FORMAT_OPTIONS (FIELD_TERMINATOR = ',')
);


-- E: Create the external table
-- Specify column names and data types. This needs to match the data in the sample file.
-- LOCATION: Specify path to file or directory that contains the data (relative to the blob container).
-- To point to all files under the blob container, use LOCATION='.'

CREATE EXTERNAL TABLE dbo.DimDate2External (
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH (
    LOCATION='/datedimension/',
    DATA_SOURCE=AzureStorage,
    FILE_FORMAT=TextFile
);


-- Run a query on the external table

SELECT count(*) FROM dbo.DimDate2External;

```

## Paso 4: Carga de datos en Almacenamiento de datos SQL

Una vez creada la tabla externa, puede cargar los datos en una tabla nueva o insertarlos en una tabla existente.

- Para cargar los datos en una tabla nueva, ejecute la instrucción [CREATE TABLE AS SELECT (Transact-SQL)][]. La nueva tabla hereda las columnas designadas en la consulta. Los tipos de datos de las columnas coincidirán con los tipos de datos de la definición de la tabla externa.
- Para cargar los datos en una tabla existente, use la instrucción [INSERT...SELECT (Transact-SQL)][].

```
-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT * FROM [dbo].[DimDate2External];
```


En el Explorador de objetos de SQL Server de Visual Studio, se puede ver el formato del archivo externo, el origen de datos externo y la tabla DimDate2External.

![Vea la tabla externa](./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png)

## Paso 5: Creación de estadísticas de los datos recién cargados

Almacenamiento de datos SQL no crea ni actualiza automáticamente las estadísticas. Por lo tanto, para lograr un rendimiento elevado de las consultas, es importante crear estadísticas de todas las columna de cada tabla después de la primera carga. También es importante actualizar las estadísticas si se realizan cambios significativos en los datos.

En este ejemplo se crean estadísticas con una sola columna de la nueva tabla DimDate2External.

```
CREATE STATISTICS [DateId] on [DimDate2] ([DateId]);
CREATE STATISTICS [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

Para más información, consulte [Estadísticas][].


## Pasos siguientes
Para más información que debe conocer cuando desarrolle una solución que use PolyBase, consulte la [guía de PolyBase][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-load-with-polybase/external-table.png

<!--Article references-->
[PolyBase in SQL Data Warehouse Tutorial]: sql-data-warehouse-get-started-load-with-polybase.md
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[solution partners]: sql-data-warehouse-solution-partners.md
[development overview]: sql-data-warehouse-overview-develop.md
[Estadísticas]: sql-data-warehouse-develop-statistics.md
[guía de PolyBase]: sql-data-warehouse-load-polybase-guide.md
[Introducción a la utilidad de línea de comandos de AzCopy]: ../storage/storage-use-azcopy.md
[versión más reciente de AzCopy]: ../storage/storage-use-azcopy.md

<!--External references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


[Create External Data Source (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[Create External File Format (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
[Create External Table (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[Create Master Key (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[Create Database Scoped Credential (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!---HONumber=AcomDC_0309_2016-->