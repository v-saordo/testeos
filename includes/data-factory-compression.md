### Compatibilidad con la compresión  
El procesamiento de grandes conjuntos de datos puede provocar cuellos de botella de E/S y red. Por lo tanto, los datos comprimidos en almacenes pueden no solo acelerar la transferencia de datos a través de la red y ahorrar espacio en disco, sino también introducir importantes mejoras de rendimiento en el procesamiento de macrodatos. En este momento, se admite la compresión para almacenes de datos basados en archivos como blobs de Azure o el sistema de archivos local.

> [AZURE.NOTE] Esta vez no se admite la configuración de compresión de los datos que se encuentran en **AvroFormat**.

Para especificar la compresión para un conjunto de datos, use la propiedad **compression** del conjunto de datos JSON como en el ejemplo siguiente:

	{  
		"name": "AzureBlobDataSet",  
	  	"properties": {  
	    	"availability": {  
	    		"frequency": "Day",  
	    	  	"interval": 1  
	    	},  
	    	"type": "AzureBlob",  
	    	"linkedServiceName": "StorageLinkedService",  
	    	"typeProperties": {  
	    		"fileName": "pagecounts.csv.gz",  
	    	  	"folderPath": "compression/file/",  
	    	  	"compression": {  
	    	    	"type": "GZip",  
	    	    	"level": "Optimal"  
	    	  	}  
    		}  
	  	}  
	}  
 
Observe que la sección **compression** tiene dos propiedades:
  
- **Type:** el códec de compresión, que puede ser **GZIP**, **Deflate** o **BZIP2**.  
- **Level:** la relación de compresión, que puede ser **Optimal** o **Fastest**. 
	- **Fastest:** la operación de compresión debe completarse tan pronto como sea posible, incluso si el archivo resultante no se comprime de forma óptima. 
	- **Optimal:** la operación de compresión se debe comprimir óptimamente, incluso si tarda más tiempo en completarse. 
	
	Consulte el tema [Nivel de compresión](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) para obtener más información.

Supongamos que el conjunto de datos de ejemplo anterior se usa como salida de una actividad de copia; la actividad de copia comprimirá los datos de salida con el códec GZIP con una proporción óptima y, luego, escribirá los datos comprimidos en un archivo denominado pagecounts.csv.gz en el almacenamiento de blobs de Azure.

Cuando se especifica la propiedad compression en un conjunto de datos de entrada JSON, la canalización puede leer los datos comprimidos desde el origen, y cuando se especifica la propiedad en un conjunto de datos de salida JSON, la actividad de copia puede escribir datos comprimidos en el destino. Estos son algunos escenarios de ejemplo:

- Leer datos comprimidos con GZIP de un blob de Azure, descomprimirlos y escribir los datos de resultado en una base de datos SQL de Azure. Defina el conjunto de datos de blob de Azure de entrada con la propiedad JSON compression en este caso. 
- Leer datos de un archivo de texto sin formato del sistema de archivos local, comprimirlos con formato GZip y escribir los datos comprimidos en un blob de Azure. Defina un conjunto de datos de blob de Azure de salida con la propiedad JSON compression en este caso.  
- Leer datos comprimidos con GZIP de un blob de Azure, descomprimirlos, comprimirlos con BZIP2 y escribir los datos de resultado en un blob de Azure. Defina el conjunto de datos de blob de Azure de entrada con el tipo de compresión establecido en GZIP y el conjunto de datos de salida con el tipo de compresión establecido en BZIP2 en este caso.   

<!---HONumber=AcomDC_0224_2016-->