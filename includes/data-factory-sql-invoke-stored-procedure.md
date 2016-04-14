## Invocación del procedimiento almacenado para el receptor de SQL

Al copiar datos en SQL Server o en Base de datos SQL de Azure/SQL Server, se puede configurar e invocar un procedimiento almacenado especificado por el usuario con parámetros adicionales.

Cuando los mecanismos de copia integrada no prestan el servicio, se puede aprovechar un procedimiento almacenado. Normalmente, este aprovechamiento se realiza cuando es necesario realizar procesos adicionales (combinación de columnas, buscar valores adicionales, inserción en varias tablas...) antes de la inserción final de datos de origen en la tabla de destino.

Puede invocar un procedimiento almacenado de su elección. En el ejemplo siguiente se muestra cómo usar un procedimiento almacenado para realizar una inserción simple en una tabla de la base de datos.

**Conjunto de datos de salida**

En este ejemplo, el tipo está definido como SqlServerTable. Establézcalo en AzureSqlTable para usarlo con una base de datos de SQL de Azure.

	{
	  "name": "SqlOutput",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "SqlLinkedService",
	    "typeProperties": {
	      "tableName": "Marketing"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}
	
Defina la sección SqlSink en el JSON de actividad de copia como se indica a continuación. Llame a un procedimiento almacenado al insertar datos, las propiedades SqlWriterStoredProcedureName y SqlWriterTableType son necesarias.

	"sink":
	{
	    "type": "SqlSink",
	    "SqlWriterTableType": "MarketingType",
	    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
	    "storedProcedureParameters":
	            {
	                "stringData": 
	                {
	                    "value": "str1"     
	                }
	            }
	}

En la base de datos, defina el procedimiento almacenado con el mismo nombre que SqlWriterStoredProcedureName. De este modo, se administran los datos de entrada desde el origen especificado y se insertan en la tabla de salida. Tenga en cuenta que el nombre del parámetro del procedimiento almacenado tiene que ser el mismo que el de TableName definido en el archivo JSON de la tabla.

	CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
	AS
	BEGIN
	    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
	    INSERT [dbo].[Marketing](ProfileID, State)
	    SELECT * FROM @Marketing
	END

En la base de datos, defina el tipo de tabla con el mismo nombre que SqlWriterTableType. Tenga en cuenta que el esquema del tipo de tabla debe ser el mismo que el esquema devuelto por los datos de entrada.

	CREATE TYPE [dbo].[MarketingType] AS TABLE(
	    [ProfileID] [varchar](256) NOT NULL,
	    [State] [varchar](256) NOT NULL,
	)

La característica de procedimiento almacenado aprovecha los [parámetros con valores de tabla](https://msdn.microsoft.com/library/bb675163.aspx).

<!---HONumber=Oct15_HO3-->