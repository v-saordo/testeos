## Asignación de columnas con reglas de traductor
La asignación de columnas puede usarse para especificar cómo se asignan las columnas especificadas en "structure" de la tabla de origen a las columnas especificadas en "structure" de la tabla receptora. La propiedad **columnMapping** está disponible en la sección **typeProperties** de la actividad de copia.

La asignación de columnas admite los siguientes escenarios:

1.	Todas las columnas de la tabla de origen "structure" están asignadas a todas las columnas en la tabla receptora "structure".
2.	Un subconjunto de columnas de la tabla de origen "structure" están asignadas a todas las columnas en la tabla receptora "structure".

Las siguientes son las condiciones de error, estas condiciones producirán una excepción:

1.	O bien menos columnas o más columnas en "structure" de la tabla receptora de las que se especifican en la asignación.
2.	Asignación duplicada.
3.	El resultado de la consulta SQL no tiene un nombre de columna que esté especificado en la asignación.

## Ejemplos de asignación de columnas
> [AZURE.NOTE] Los ejemplos siguientes son para SQL Azure y Azure Blob, pero son aplicables a cualquier almacén de datos que admita conjuntos de datos rectangulares. Tendrá que ajustar el conjunto de datos y las definiciones de servicios vinculados en los ejemplos siguientes para que apunten a los datos en el origen de datos correspondiente.

### Ejemplo 1: asignación de columnas de SQL Server a un blob de Azure
En este ejemplo la tabla de entrada tiene una estructura y apunta a una tabla SQL en una base de datos de SQL de Azure.

	{
	    "name": "AzureSQLInput",
	    "properties": {
	        "structure": 
	         [
	           { "name": "userid"},
	           { "name": "name"},
	           { "name": "group"}
	         ],
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true,
			"policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
			}
	    }
	}

En este ejemplo, la tabla de salida tiene una estructura y apunta a un blob en un almacenamiento de blobs de Azure.

	{
	    "name": "AzureBlobOutput",
	    "properties":
	    {
	         "structure": 
	          [
	                { "name": "myuserid"},
	                { "name": "myname" },
	                { "name": "mygroup"}
	          ],
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/myfolder",
	            "fileName":"myfile.csv",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            }
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

A continuación se muestra el JSON para la actividad. Las columnas del origen se asignan a columnas del receptor (**columnMappings**) usando la propiedad **Translator**.

	{
	    "name": "CopyActivity",
	    "description": "description", 
	    "type": "Copy",
	    "inputs":  [ { "name": "AzureSQLInput"  } ],
	    "outputs":  [ { "name": "AzureBlobOutput" } ],
	    "typeProperties":    {
	        "source":
	        {
	            "type": "SqlSource"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        },
	        "translator": 
	        {
	            "type": "TabularTranslator",
	            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
	        }
	    },
	   "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        }
	}

**Flujo de asignación de columnas:**

![Flujo de asignación de columnas](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow.png)

### Ejemplo 2: asignación de columnas con una consulta SQL de SQL de Azure a un blob de Azure
En este ejemplo, se usa una consulta SQL para extraer datos de SQL de Azure en lugar de especificar simplemente el nombre de tabla y los nombres de columna en la sección "structure".

	{
	    "name": "CopyActivity",
	    "description": "description", 
	    "type": "CopyActivity",
	    "inputs":  [ { "name": " AzureSQLInput"  } ],
	    "outputs":  [ { "name": " AzureBlobOutput" } ],
	    "typeProperties":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        },
	        "Translator": 
	        {
	            "type": "TabularTranslator",
	            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
	        }
	    },
	    "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        }
	}

En este caso, los resultados de consulta se asignan primero a las columnas especificadas en "structure" del origen. A continuación, las columnas dev"structure" de origen se asignan a columnas "structure" de receptor con las reglas especificadas en columnMappings. Supongamos que la consulta devuelve 5 columnas, dos columnas adicionales y aquellas especificadas en "structure" de origen.

**Flujo de asignación de columnas**

![Flujo de asignación de columnas 2](./media/data-factory-data-stores-with-rectangular-tables/column-mapping-flow-2.png)

<!---HONumber=AcomDC_0224_2016-->