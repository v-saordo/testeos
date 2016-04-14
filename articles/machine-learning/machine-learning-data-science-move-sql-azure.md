<properties 
	pageTitle="Mover datos a una base de datos de SQL de Azure para Aprendizaje automático de Azure | Azure" 
	description="Crear tabla SQL y cargar datos en ella" 
	services="machine-learning" 
	documentationCenter="" 
	authors="fashah" 
	manager="jacob.spoelstra" 
	editor="" 
	videoId=""
	scriptId="" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/08/2016" 
	ms.author="fashah;bradsev" />

# Mover datos a una base de datos de SQL de Azure para Aprendizaje automático de Azure

## Introducción
**En este tema** se describen las opciones para mover datos de archivos planos (formatos CSV o TSV) o de datos almacenados en un servidor de SQL Server local a una base de datos SQL de Azure. Estas tareas de movimiento de datos a la nube forman parte del proceso de análisis de Cortana proporcionado por Azure.

Para ver un tema que describa las opciones para mover datos a un servidor de SQL Server local para Aprendizaje automático, vea [Mover datos a un servidor SQL Server en una máquina virtual de Azure](machine-learning-data-science-move-sql-server-virtual-machine.md).

El **menú** a continuación vincula a temas en los que se describe cómo introducir datos en los entornos de destino en que sea posible almacenar y procesar datos durante el proceso de Cortana Analytics (CAPS).

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

En la tabla siguiente se resumen las opciones para mover datos a una base de datos SQL de Azure.

<b>ORIGEN</b> |<b>DESTINO: Base de datos SQL de Azure</b> |
-------------- |--------------------------------|
<b>Archivo plano (formatos CSV o TSV)</b> |<a href="#bulk-insert-sql-query">Consulta SQL de inserción masiva |
<b>Servidor SQL Server local</b> | 1\. <a href="#export-flat-file">Exportación a un archivo plano<br> 2. <a href="#insert-tables-bcp">Asistente para migración de Base de datos SQL<br> 3. <a href="#db-migration">Copia de seguridad y restauración de la base de datos<br> 4. <a href="#adf">Factoría de datos de Azure |


## <a name="prereqs"></a>Requisitos previos
El procedimiento aquí descrito requiere disponer de:

* Una **suscripción de Azure**. Si no tiene una suscripción, puede registrarse para obtener una [prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Una **cuenta de almacenamiento de Azure**. En este tutorial, usará una cuenta de almacenamiento de Azure para almacenar los datos. Si no dispone de una cuenta de almacenamiento de Azure, vea el artículo [Creación de una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account). Tras crear la cuenta de almacenamiento, tendrá que obtener la clave de cuenta que se usa para tener acceso al almacenamiento. Vea [Vista, copia y regeneración de las claves de acceso de almacenamiento](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Acceso a una **base de datos SQL de Azure**. Si debe configurar una base de datos de SQL de Azure, [Introducción a Base de datos de SQL de Microsoft Azure](sql-database-get-started.md) proporciona información sobre cómo aprovisionar una nueva instancia de una base de datos de SQL de Azure.
* **Azure PowerShell** instalado y configurado de forma local. Para obtener instrucciones, consulte [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).

**Datos**: los procesos de migración se demuestran con el [conjunto de datos de taxis de Nueva York](http://chriswhong.com/open-data/foil_nyc_taxi/). El conjunto de datos de taxis de Nueva York contiene información sobre los datos de carreras y las tarifas, y está disponible, como se especificó en esa publicación, en el almacenamiento de blobs de Azure: [Datos de taxis de Nueva York](http://www.andresmh.com/nyctaxitrips/). En [Descripción del conjunto de datos de carreras de taxi de Nueva York](machine-learning-data-science-process-sql-walkthrough.md#dataset), se ofrece un ejemplo y una descripción de estos archivos.
 
Puede adaptar los procedimientos que se describen aquí para un conjunto de datos propios o seguir los pasos descritos para el uso del conjunto de datos de taxis de Nueva York. Para cargar el conjunto de datos de taxis de Nueva York en la base de datos de SQL Server local, siga el procedimiento descrito en [Importación masiva de datos en una base de datos de SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Estas instrucciones son para un servidor SQL Server en una máquina virtual de Azure, pero el procedimiento para realizar la carga en el servidor SQL Server local es el mismo.

## <a name="file-to-azure-sql-database"></a> Mover datos desde un origen de archivo plano a una base de datos SQL de Azure

Los datos de archivos planos (formatos CSV o TSV) se pueden mover a una base de datos de SQL de Azure mediante una consulta SQL de inserción masiva.

### <a name="bulk-insert-sql-query"></a> Consulta SQL de inserción masiva

Los pasos del procedimiento en el que se usa la consulta SQL de inserción masiva son similares a los descritos en las secciones en las que se explica el movimiento de datos desde un origen de archivo plano a un servidor SQL Server en una máquina virtual de Azure. Para detalles, vea [Consulta SQL de inserción masiva](machine-learning-data-science-move-sql-server-virtual-machine.md#insert-tables-bulkquery).

##<a name="sql-on-prem-to-sazure-sql-database"></a> Mover datos desde un servidor SQL Server local a una base de datos SQL de Azure

Si los datos de origen están almacenados en un servidor SQL Server local, hay varias alternativas para mover los datos a una base de datos de SQL de Azure:

1. [Exportación a un archivo plano](#export-flat-file) 
2. [Asistente para migración de Base de datos SQL](#insert-tables-bcp)
3. [Copia de seguridad y restauración de la base de datos](#db-migration)
4. [Factoría de datos de Azure](#adf)

Los pasos para las tres primeras opciones son muy similares a los de la sección [Mover datos a un servidor SQL Server en una máquina virtual de Azure](machine-learning-data-science-move-sql-server-virtual-machine.md), en la que se explican los mismos procedimientos. A continuación, se proporcionan vínculos a las secciones correspondientes de ese tema.

###<a name="export-flat-file"></a>Exportación a un archivo plano

Los pasos para esta exportación a un archivo plano son similares a los que se explican en [Exportación a un archivo plano](machine-learning-data-science-move-sql-server-virtual-machine.md#export-flat-file).

###<a name="insert-tables-bcp"></a>Asistente para migración de Base de datos SQL

Los pasos para usar el Asistente para migración de base de datos SQL son similares a los que se explican en [Asistente para migración de base de datos de SQL](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-migration).

###<a name="db-migration"></a>Copia de seguridad y restauración de la base de datos

Los pasos para usar la copia de seguridad y restauración de la base de datos son similares a los que se explican en [Copia de seguridad y restauración de la base de datos](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-backup).

###<a name="adf"></a>Factoría de datos de Azure

El procedimiento para mover datos a una base de datos SQL de Azure con la Factoría de datos de Azure (ADF) explica en el tema [Mover datos desde un servidor SQL Server local a SQL Azure con la Factoría de datos de Azure](machine-learning-data-science-move-sql-azure-adf.md). En este tema se muestra cómo mover datos desde una base de datos de SQL Server local a una base de datos SQL de Azure a través del almacenamiento de blobs de Azure mediante la ADF.

Considere el uso de la ADF cuando los datos deban migrarse continuamente en un escenario híbrido en el que se tenga acceso a recursos locales y de nube, y cuando los datos se transfieran o deban modificarse o tener lógica de negocios agregada mientras se migran. La ADF permite la programación y supervisión de trabajos mediante scripts JSON sencillos que administran el movimiento de datos de forma periódica. La ADF también tiene otras capacidades como la compatibilidad con operaciones complejas.

<!---HONumber=AcomDC_0211_2016-->