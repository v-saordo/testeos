<properties 
	pageTitle="Movimiento de datos hacia y desde DocumentDB | Factoría de datos de Azure" 
	description="Obtenga información acerca de cómo mover los datos hacia y desde DocumentDB de Azure mediante Factoría de datos de Azure" 
	services="data-factory, documentdb" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="multiple" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2016" 
	ms.author="spelluru"/>

# Movimiento de datos hacia y desde DocumentDB mediante Factoría de datos de Azure

En este artículo se describe cómo puede usar la actividad de copia en la Factoría de datos de Azure para mover datos a DocumentDB de Azure desde otro almacén de datos y viceversa. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

En los siguientes ejemplos, se muestra cómo copiar datos entre Azure DocumentDB y Almacenamiento de blobs de Azure. Sin embargo, los datos se pueden copiar **directamente** de cualquiera de los orígenes a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en Factoría de datos de Azure.


## Ejemplo: copia de datos de DocumentDB a un blob de Azure

El ejemplo siguiente muestra:

1. Un servicio vinculado de tipo [DocumentDb](#azure-documentdb-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia los datos en DocumentDB de Azure a un blob de Azure. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado de DocumentDB de Azure:**

	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
	    }
	  }
	}

**Servicio vinculado de almacenamiento de blobs de Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de datos de entrada de DocumentDB de Azure:**

El ejemplo supone que tiene una colección denominada **Person** en una base de datos de DocumentDB de Azure.
 
Si se establece "external": "true" y se especifica la directiva externalData, se informa al servicio Factoría de datos de Azure que la tabla es externa a la factoría de datos y que no se produce por ninguna actividad de la factoría de datos.

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}


**Conjunto de datos de salida de blob de Azure:**

Los datos se copian a un blob nuevo cada hora con la ruta de acceso para el blob que refleja la fecha y hora específicas con granularidad de hora.

	{
	  "name": "PersonBlobTableOut",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "docdb",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "nullValue": "NULL"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

Documento JSON de ejemplo de la colección Person en una base de datos de DocumentDB:

	{
	  "PersonId": 2,
	  "Name": {
	    "First": "Jane",
	    "Middle": "",
	    "Last": "Doe"
	  }
	}

DocumentDB admite la consulta de documentos mediante una sintaxis similar a la de SQL en documentos jerárquicos JSON.

Ejemplo: SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person

La siguiente canalización copia los datos de la colección Person de la base de datos de DocumentDB a un blob de Azure. Como parte de la actividad de copia, se han especificado los conjuntos de datos de entrada y de salida.
	
	{
	  "name": "DocDbToBlobPipeline",
	  "properties": {
	    "activities": [
	      {
	        "type": "Copy",
	        "typeProperties": {
	          "source": {
	            "type": "DocumentDbCollectionSource",
	            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
	            "nestingSeparator": "."
	          },
	          "sink": {
	            "type": "BlobSink",
	            "blobWriterAddHeader": true,
	            "writeBatchSize": 1000,
	            "writeBatchTimeout": "00:00:59"
	          }
	        },
	        "inputs": [
	          {
	            "name": "PersonDocumentDbTable"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "PersonBlobTableOut"
	          }
	        ],
	        "policy": {
	          "concurrency": 1
	        },
	        "name": "CopyFromDocDbToBlob"
	      }
	    ],
	    "start": "2015-04-01T00:00:00Z",
	    "end": "2015-04-02T00:00:00Z"
	  }
	}

## Ejemplo: copia de datos de un blob de Azure a DocumentDB de Azure

El ejemplo siguiente muestra:

1. Un servicio vinculado de tipo [DocumentDb](#azure-documentdb-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) y [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).


El ejemplo copia datos de un blob de Azure a DocumentDB de Azure Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado de almacenamiento de blobs de Azure:**
	
	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Servicio vinculado de DocumentDB de Azure:**
	
	{
	  "name": "DocumentDbLinkedService",
	  "properties": {
	    "type": "DocumentDb",
	    "typeProperties": {
	      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
	    }
	  }
	}

**Conjunto de datos de entrada de blob de Azure:**

	{
	  "name": "PersonBlobTableIn",
	  "properties": {
	    "structure": [
	      {
	        "name": "Id",
	        "type": "Int"
	      },
	      {
	        "name": "FirstName",
	        "type": "String"
	      },
	      {
	        "name": "MiddleName",
	        "type": "String"
	      },
	      {
	        "name": "LastName",
	        "type": "String"
	      }
	    ],
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "fileName": "input.csv",
	      "folderPath": "docdb",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "nullValue": "NULL"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Conjunto de datos de salida de DocumentDB de Azure:**

El ejemplo copia los datos a una colección denominada "Person".

	{
	  "name": "PersonDocumentDbTableOut",
	  "properties": {
	    "structure": [
	      {
	        "name": "Id",
	        "type": "Int"
	      },
	      {
	        "name": "Name.First",
	        "type": "String"
	      },
	      {
	        "name": "Name.Middle",
	        "type": "String"
	      },
	      {
	        "name": "Name.Last",
	        "type": "String"
	      }
	    ],
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

La siguiente canalización copia los datos de un blob de Azure a la colección Person de DocumentDB. Como parte de la actividad de copia, se han especificado los conjuntos de datos de entrada y de salida.
	
	{
	  "name": "BlobToDocDbPipeline",
	  "properties": {
	    "activities": [
	      {
	        "type": "Copy",
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "DocumentDbCollectionSink",
	            "nestingSeparator": ".",
	            "writeBatchSize": 2,
	            "writeBatchTimeout": "00:00:00"
	          }
	          "translator": {
	              "type": "TabularTranslator",
	              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
	          }
	        },
	        "inputs": [
	          {
	            "name": "PersonBlobTableIn"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "PersonDocumentDbTableOut"
	          }
	        ],
	        "policy": {
	          "concurrency": 1
	        },
	        "name": "CopyFromBlobToDocDb"
	      }
	    ],
	    "start": "2015-04-14T00:00:00Z",
	    "end": "2015-04-15T00:00:00Z"
	  }
	}
 
Si la entrada de blob de ejemplo es como:

	1,John,,Doe

Entonces, el resultado JSON en DocumentDB será como:

	{
	  "Id": 1,
	  "Name": {
	    "First": "John",
	    "Middle": null,
	    "Last": "Doe"
	  },
	  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
	}
	
DocumentDB es un almacén NoSQL para documentos JSON, en el que se permiten estructuras anidadas. Factoría de datos de Azure permite al usuario indicar la jerarquía a través de **nestingSeparator** que es "." en este ejemplo. Con el separador, la actividad de copia generará el objeto "Name" con tres elementos secundarios First, Middle y Last, según "Name.First", "Name.Middle" y "Name.Last" en la definición de tabla.

## Propiedades del servicio vinculado de DocumentDB de Azure

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de DocumentDB de Azure.

| **Propiedad** | **Descripción** | **Obligatorio** |
| -------- | ----------- | --------- |
| type | La propiedad type debe establecerse en: **DocumentDb** | Sí |
| connectionString | Especifique la información necesaria para conectarse a la base de datos de DocumentDB de Azure. | Sí |

## Propiedades de tipo de conjunto de datos de DocumentDB de Azure

Para obtener una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).
 
La sección typeProperties es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo **DocumentDbCollection** tiene las propiedades siguientes.

| **Propiedad** | **Descripción** | **Obligatorio** |
| -------- | ----------- | -------- |
| collectionName | Nombre de la colección de documentos de DocumentDB. | Sí |


Ejemplo:

	{
	  "name": "PersonDocumentDbTable",
	  "properties": {
	    "type": "DocumentDbCollection",
	    "linkedServiceName": "DocumentDbLinkedService",
	    "typeProperties": {
	      "collectionName": "Person"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

### Esquema de Data Factory
En los almacenes de datos sin esquemas como DocumentDB, el servicio Data Factory deduce el esquema de una de las maneras siguientes:

1.	Si especifica la estructura de los datos mediante la propiedad **structure** en la definición del conjunto de datos, el servicio Data Factory respeta esta estructura como estructura del esquema. En este caso, si una fila no contiene un valor para una columna, se le proporcionará un valor nulo.
2.	Si no especifica la estructura de los datos mediante la propiedad **structure** en la definición del conjunto de datos, el servicio Data Factory deduce el esquema utilizando la primera fila en los datos. En este caso, si la primera fila no contiene el esquema completo, algunas columnas se pueden perder en el resultado de la operación de copia.

Por lo tanto, para los orígenes de datos sin esquemas, lo mejor es especificar la estructura de los datos mediante la propiedad **structure**.

## Propiedades de tipo de actividad de copia de DocumentDB de Azure

Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.
 
**Nota:** la actividad de copia toma solo una entrada y genera una única salida.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

En caso de la actividad de copia si el origen es de tipo **DocumentDbCollectionSource**, están disponibles las propiedades siguientes en la sección **typeProperties**:

| **Propiedad** | **Descripción** | **Valores permitidos** | **Obligatorio** |
| ------------ | --------------- | ------------------ | ------------ |
| query | Especifique la consulta para leer los datos. | Cadena de consulta compatible con DocumentDB. <p>Ejemplo: SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > "2009-01-01T00:00:00"</p> | No <p>Si no se especifica, la instrucción SQL que se ejecuta: select <columns defined in structure> from mycollection </p>
| nestingSeparator | Carácter especial para indicar que el documento está anidado | Cualquier carácter. <p>DocumentDB es un almacén NoSQL para documentos JSON, en el que se permiten estructuras anidadas. Factoría de datos de Azure permite al usuario indicar la jerarquía a través de nestingSeparator que es "." en los ejemplos anteriores. Con el separador, la actividad de copia generará el objeto "Name" con tres elementos secundarios First, Middle y Last, según "Name.First", "Name.Middle" y "Name.Last" en la definición de tabla.</p> | No

**DocumentDbCollectionSink** admite las siguientes propiedades:

| **Propiedad** | **Descripción** | **Valores permitidos** | **Obligatorio** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | Un carácter especial en el nombre de columna de origen que indica que el documento anidado es necesario. <p>Ejemplo de lo anterior: Name.First en la tabla de salida produce la siguiente estructura JSON en el documento de DocumentDB:</p><p>"Name": {<br/> "First": "John"<br/>},</p> | Carácter que se usa para separar los niveles de anidamiento.<p>El valor predeterminado es . (punto).</p> | Carácter que se usa para separar los niveles de anidamiento. <p>El valor predeterminado es . (punto).</p> | No | 
| writeBatchSize | Número de solicitudes paralelas al servicio DocumentDB para crear documentos.<p>Puede ajustar el rendimiento cuando se copian datos a y desde DocumentDB mediante esta propiedad. Puede esperar un rendimiento mejor al aumentar writeBatchSize porque se envían más solicitudes paralelas a DocumentDB. Sin embargo, deberá evitar una limitación de peticiones que puede generar el mensaje de error: "Request rate is large" (La tasa de solicitud es grande).</p><p>La limitación de peticiones se decide mediante una serie de factores, incluidos tamaño de los documentos, número de términos en los documentos, directiva de indexación de colección de destino, etc. Para las operaciones de copia, puede usar una colección mejor (por ejemplo, S3) para obtener el máximo rendimiento disponible (2.500 unidades de solicitudes por segundo).</p> | Valor entero | No |
| writeBatchTimeout | Tiempo de espera para que la operación se complete antes de que se agote el tiempo de espera. | (Unidad = intervalo de tiempo) Ejemplo: "00:30:00" (30 minutos). | No |
 
## Anexo
1. **Pregunta:** ¿Admite la actividad de copia la actualización de los registros existentes?

	**Respuesta:** No.

2. **Pregunta:** ¿Cómo trata un reintento de una copia en DocumentDB con registros ya copiados?

	**Respuesta:** Si los registros tienen un campo "Id" y la operación de copia intenta insertar un registro con el mismo Id., la operación de copia genera un error.
 
3. **Pregunta:** ¿Admite la factoría de datos el [intervalo o las particiones de datos basadas en hash]( https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)?

	**Respuesta:** No. 
4. **Pregunta:** ¿Puedo especificar más de una colección de DocumentDB para una tabla?
	
	**Respuesta:** No. Solo se puede especificar una colección cada vez.
     

<!----HONumber=AcomDC_0302_2016-->