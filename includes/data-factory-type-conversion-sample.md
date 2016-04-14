### Ejemplo de conversión de tipo
El siguiente es un ejemplo de la copia de datos de un Blob en SQL de Azure con conversiones de tipo.

Supongamos que el conjunto de datos Blob está en formato CSV y contiene 3 columnas. Una de ellas es una columna de fecha y hora con un formato de fecha y hora personalizado mediante los nombres abreviados de los días de la semana en francés.

Va a definir el conjunto de datos de origen de Blob como se indica a continuación junto con las definiciones de tipo para las columnas.

	{
	    "name": "AzureBlobTypeSystemInput",
	    "properties":
	    {
	         "structure": 
	          [
	                { "name": "userid", "type": "Int64"},
	                { "name": "name", "type": "String"},
	                { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
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
	        "external": true,
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
			}
	    }
	}

Teniendo en cuenta la anterior tabla de asignaciones de tipo SQL a tipo .NET, tendrá que definir la tabla de SQL de Azure con el siguiente esquema.

| Nombre de columna | Tipo SQL |
| ----------- | -------- |
| userid | bigint |
| name | text |
| lastlogindate | datetime |

A continuación definirá el conjunto de datos de SQL de Azure como sigue. Nota: no es necesario especificar la sección "structure" con información de tipo porque ya se especificó en el almacén de datos subyacente.

	{
	    "name": "AzureSQLOutput",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

En este caso la factoría de datos realizará automáticamente las conversiones de tipo, incluido el campo de fecha y hora con el formato personalizado de fecha y hora usando la referencia cultural fr-fr, al mover datos de Blob a SQL deAzure.

<!---HONumber=Oct15_HO3-->