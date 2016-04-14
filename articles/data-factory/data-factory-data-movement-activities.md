<properties 
	pageTitle="Actividades de movimiento de datos" 
	description="Obtenga información sobre las entidades de Factoría de datos que puede usar en las canalizaciones de Factoría de datos para mover datos." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="spelluru"/>

# Actividades de movimiento de datos
La [actividad de copia](#copyactivity) realiza el movimiento de datos en Factoría de datos de Azure y la actividad funciona con un [servicio disponible generalmente](#global) que puede copiar datos entre varios almacenes de datos de forma segura, confiable y escalable. El servicio elige automáticamente la región más óptima para realizar el movimiento de datos. Se utiliza la región más cercana al almacén de datos receptor.
 
Analicemos cómo se produce este movimiento de datos en escenarios diferentes.

## Copia de datos entre dos almacenes de datos en la nube
Cuando los almacenes de datos de origen y receptor (destino) residen en la nube, la actividad de copia pasa por las siguientes fases para copiar o mover datos desde el origen hasta el receptor. El servicio que alimenta a la actividad de copia realiza lo siguiente:

1. lee datos del almacén de datos de origen,
2.	realiza serialización y deserialización, compresión y descompresión, asignación de columnas y conversión de tipos en función de las configuraciones del conjunto de datos de entrada, del conjunto de datos de salida y la actividad de copia, 
3.	escribe datos en el almacén de datos de destino

![copia de la nube a la nube](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## Copia de datos entre un almacén de datos local y un almacén de datos en la nube
Para [mover datos entre almacenes de datos locales detrás del firewall corporativo y un almacén de datos en la nube de forma segura](#moveonpremtocloud), necesitará instalar Data Management Gateway, que es un agente que permite el procesamiento y movimiento de datos híbridos, en el equipo local. Data Management Gateway puede instalarse en la misma máquina que el propio almacén de datos o en una máquina independiente que tenga acceso para alcanzar el almacén de datos. En este escenario, la serialización y deserialización, la compresión y descompresión, la asignación de columnas y la conversión de tipos se realizan mediante Data Management Gateway. Los datos no fluyen a través del servicio Factoría de datos de Azure en ese caso. Data Management Gateway escribe directamente los datos en el almacén de destino.

![copia de local a la nube](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

## Copia de datos con un almacén de datos en una VM IaaS de Azure 
También puede mover datos desde y hacia almacenes de datos compatibles hospedados en VM IaaS de Azure (máquinas virtuales de infraestructura como servicio) mediante Data Management Gateway. En este caso, Data Management Gateway puede instalarse en la misma VM de Azure que el propio almacén de datos o en una VM independiente que tenga acceso para alcanzar el almacén de datos.

## Almacenes de datos que se admiten
La actividad de copia copia los datos de un almacén de datos de **origen** a un almacén de datos **receptor**. Factoría de datos admite los siguientes almacenes de datos y **se pueden escribir datos de cualquier origen en cualquier receptor**. Haga clic en un almacén de datos para obtener información sobre cómo copiar datos desde/a ese almacén.

| Orígenes| Receptores |
|:------- | :---- |
| <ul><li>[Blob de Azure](data-factory-azure-blob-connector.md)</li><li>[Tabla de Azure](data-factory-azure-table-connector.md)</li><li>[Base de datos SQL de Azure](data-factory-azure-sql-connector.md)</li><li>[Almacenamiento de datos SQL de Azure](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (consulte la nota siguiente)](data-factory-azure-documentdb-connector.md)</li><li>[Almacén de Azure Data Lake](data-factory-azure-datalake-connector.md)</li><li>[SQL Server local/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[Sistema de archivos local/Azure IaaS](data-factory-onprem-file-system-connector.md)</li><li>[Base de datos de Oracle local/Azure IaaS](data-factory-onprem-oracle-connector.md)</li><li>[Base de datos MySQL local/Azure IaaS ](data-factory-onprem-mysql-connector.md)</li><li>[Base de datos DB2 local/Azure IaaS](data-factory-onprem-db2-connector.md)</li><li>[Base de datos Teradata local/Azure IaaS ](data-factory-onprem-teradata-connector.md)</li><li>[Base de datos Sybase local/Azure IaaS](data-factory-onprem-sybase-connector.md)</li><li>[Base de datos PostgreSQL local/Azure IaaS](data-factory-onprem-postgresql-connector.md)</li><li>[Orígenes de datos ODBC locales/Azure IaaS](data-factory-odbc-connector.md)</li><li>[Sistema de archivos distribuido de Hadoop (HDFS) local /Azure IaaS](data-factory-hdfs-connector.md)</li><li>[Orígenes de OData](data-factory-odata-connector.md)</li><li>[Tabla web](data-factory-web-table-connector.md)</li></ul> | <ul><li>[Blob de Azure](data-factory-azure-blob-connector.md)</li><li>[Tabla de Azure](data-factory-azure-table-connector.md)</li><li>[Base de datos SQL de Azure](data-factory-azure-sql-connector.md)</li><li>[Almacenamiento de datos SQL de Azure](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (consulte la nota siguiente)](data-factory-azure-documentdb-connector.md)</li><li>[Almacén de Azure Data Lake](data-factory-azure-datalake-connector.md)</li><li>[SQL Server local/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[Sistema de archivos local/Azure IaaS](data-factory-onprem-file-system-connector.md)</li></ul> |


> [AZURE.NOTE] Solo se puede mover a/desde Azure DocumentDB desde/hasta otros servicios de Azure como Blob de Azure, Tabla de Azure, Base de datos SQL de Azure, Almacenamiento de datos SQL de Azure, Azure DocumentDB y Almacenamiento de Azure Data Lake. La matriz completa para Azure DocumentDB también se admitiría en breve.

## Tutorial
Para obtener un tutorial rápido sobre el uso de la actividad de copia, consulte [Tutorial: Uso de la actividad de copia en una canalización de la Factoría de datos de Azure](data-factory-get-started.md). En el tutorial, usará la actividad de copia para copiar datos desde un almacenamiento de blobs de Azure en una base de datos SQL de Azure.

## <a name="copyactivity"></a>Actividad de copia
Actividad de copia toma un conjunto de datos de entrada (**origen**) y un conjunto de datos de salida (**receptor**). La copia de datos se realiza por lotes según la programación especificada en la actividad. Para obtener información acerca de cómo definir actividades en general, consulte el artículo [Descripción de canalizaciones y actividades](data-factory-create-pipelines.md).

La actividad de copia proporciona las siguientes capacidades:

### <a name="global"></a>Movimiento de datos disponible globalmente
Aunque la propia Factoría de datos de Azure solamente esté disponible en las regiones oeste de EE. UU. y Europa del Norte, el servicio que permite la actividad de copia está disponible globalmente en las siguientes regiones y zonas geográficas. La topología disponible globalmente garantiza un movimiento de datos eficiente que evita saltos entre regiones en la mayoría de los casos.

Tanto **Data Management Gateway** como **Factoría de datos de Azure** realizan el movimiento de datos en función de la ubicación de los almacenes de datos de origen y destino en una operación de copia. Para más información, consulte la tabla siguiente:

Ubicación de almacén de datos de origen | Ubicación de almacén de datos de destino | El movimiento de datos lo realiza  
-------------------------- | ------------------------------- | ----------------------------- 
local o VM de Azure (IaaS) | nube | **Data Management Gateway** en un equipo local y VM de Azure. Los datos no fluyen a través del servicio en la nube. <p> Nota: Data Management Gateway puede estar en el mismo equipo local o VM de Azure que el almacén de datos o en uno diferente, siempre que se pueda conectar a ambos almacenes de datos.</p>
nube | local o VM de Azure (IaaS) | Igual que el anterior. 
local o VM de Azure (IaaS) | local o VM de Azure | **Data Management Gateway asociado al origen**. Los datos no fluyen a través del servicio en la nube. Consulte la nota anterior.   
nube | nube | <p>** El servicio en la nube que se permite la actividad de copia **. Factoría de datos de Azure usa la implementación de este servicio en la región más cercana a la ubicación del receptor en la misma zona geográfica. Consulte la tabla siguiente para realizar la asignación: </p><table><tr><th>Región del almacén de datos de destino</th> <th>Región usada para el movimiento de datos</th></tr><tr><td>Este de EE. UU.</td><td>Este de EE. UU.</td></tr><tr><td>Este de EE. UU. 2</td><td>Este de EE. UU. 2</td><tr/><tr><td>Centro de EE. UU.</td><td>Centro de EE. UU.</td><tr/><tr><td>Oeste de EE. UU.</td><td>Oeste de EE. UU.</td></tr><tr><td>Centro-norte de EE. UU.</td><td>Centro-norte de EE. UU.</td></tr><tr><td>Centro-sur de EE. UU.</td><td>Centro-sur de EE. UU.</td></tr><tr><td>Europa del Norte</td><td>Europa del Norte</td></tr><tr><td>Europa Occidental</td><td>Europa Occidental</td></tr><tr><td>Sudeste Asiático</td><td>Sudeste Asiático</td></tr><tr><td>Asia Oriental</td><td>Asia Suroriental</td></tr><tr><td>Japón Oriental</td><td>Japón Oriental</td></tr><tr><td>Japón Occidental</td><td>Japón Oriental</td></tr><tr><td>Sur de Brasil</td><td>Sur de Brasil</td></tr><tr><td>Este de Australia</td><td>Este de Australia</td></tr><tr><td>Sudeste de Australia</td><td>Sudeste de Australia</td></tr></table>


> [AZURE.NOTE] Si la región del almacén de datos de destino no está en la lista anterior, se producirá un error en la actividad de copia, en lugar de atravesar una región alternativa.



### <a name="moveonpremtocloud"></a>Movimiento de datos seguro entre la nube y la ubicación local
Uno de los desafíos de la integración de datos moderna es mover datos sin problemas entre ubicación la local y la nube. Data management gateway es un agente que puede instalar de forma local para permitir canalizaciones de datos híbridas.

La puerta de enlace de datos proporciona las siguientes capacidades:

1.	Administrar el acceso a almacenes de datos locales de forma segura.
2.	Usar modelos de almacenes de datos locales y almacenes de datos en la nube dentro de la misma factoría de datos y mover los datos.
3.	Tener un solo panel de vidrio para la supervisión y administración con visibilidad del estado de la puerta de enlace mediante el panel basado en la nube de la factoría de datos.

Debe tratar el origen de datos como un origen de datos local (que está detrás de un firewall) aunque use **ExpressRoute** y **usar la puerta de enlace** para establecer la conectividad entre el servicio y el origen de datos.

Para más información, consulte [Movimiento de datos entre la ubicación local y la nube](data-factory-move-data-between-onprem-and-cloud.md).

### Movimiento de datos confiable y rentable
La actividad de copia está diseñada para mover grandes volúmenes de datos de forma segura, resistente a errores transitorios a través de una gran variedad de orígenes de datos. Se pueden copiar datos de una manera rentable con la posibilidad de habilitar la compresión por la red.

### Conversión de los tipos entre sistemas de tipos diferentes
Los diferentes almacenes de datos tienen sistemas con distintos tipos nativos. La actividad de copia realiza conversiones automáticas de los tipos del origen a los tipos del receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Puede encontrar la asignación de un determinado sistema de tipo nativo a .NET para el almacén de datos en los respectivos artículos del conector del almacén de datos. Puede usar estas asignaciones para determinar los tipos adecuados al crear las tablas, de forma que se realicen las conversiones adecuadas durante la actividad de copia.

### Trabajo con diferentes formatos de archivo
En los almacenes basados en archivos, la actividad de copia admite una gran variedad de formatos de archivo, incluidos los formatos binario, texto y Avro. Puede usar la actividad de copia para convertir los datos de un formato a otro. Ejemplo: texto (CSV) para Avro. Si los datos no están estructurados, puede omitir la propiedad **Structure** en la definición de JSON del [conjunto de datos](data-factory-create-datasets.md).

### Propiedades de la actividad de copia
Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades. Por otra parte, las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad.

En el caso de la actividad de copia, la sección **typeProperties** varía en función de los tipos de origen y receptor. En cada la página específica del almacén de datos en los documentos enumerados anteriormente se encuentran las propiedades específicas del tipo de almacén de datos.


### Optimización y rendimiento de la actividad de copia 
Vea el artículo [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md), en el que se describen los factores claves que afectan al rendimiento del movimiento de datos (actividad de copia) en la Factoría de datos de Azure. También muestra el rendimiento observado durante las pruebas internas y trata diversas maneras de optimizar el rendimiento de la actividad de copia.

<!---HONumber=AcomDC_0302_2016-->