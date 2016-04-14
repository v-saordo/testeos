## Servicio vinculado de Almacenamiento de Azure

El **servicio vinculado de Almacenamiento de Azure** le permite vincular una cuenta de Almacenamiento de Azure a una Factoría de datos de Azure con una **clave de cuenta**. De este modo, la factoría de datos obtendrá acceso global al Almacenamiento de Azure. En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de Almacenamiento de Azure.

| Propiedad | Descripción | Obligatorio |
| :-------- | :----------- | :-------- |
| type | La propiedad type debe establecerse en: **AzureStorage** | Sí |
| connectionString | Especifique la información necesaria para conectarse a Almacenamiento de Azure para la propiedad connectionString. | Sí |

Consulte el siguiente artículo para conocer los pasos para ver o copiar la clave de cuenta del Almacenamiento de Azure: [Visualización, copia y regeneración de las claves de acceso de almacenamiento](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

**Ejemplo:**
  
	{  
		"name": "StorageLinkedService",  
		"properties": {  
			"type": "AzureStorage",  
			"typeProperties": {  
				"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
			}  
		}  
	}  


## Servicio vinculado de SAS de Almacenamiento de Azure  
Una Firma de acceso compartido (SAS) ofrece acceso delegado a los recursos en la cuenta de almacenamiento. Esto significa que puede conceder permisos limitados de los clientes a objetos en su cuenta de almacenamiento durante un período específico y con un conjunto determinado de permisos sin tener que compartir las claves de acceso a las cuentas. La SAS es un URI que incluye en sus parámetros de consulta toda la información necesaria para el acceso autenticado a un recurso de almacenamiento. Para obtener acceso a los recursos de almacenamiento con la SAS, el cliente solo tiene que pasar la SAS al método o constructor adecuados. Para obtener información detallada acerca de SAS, consulte [Firmas de acceso compartido: Descripción del modelo SAS](../articles/storage/storage-dotnet-shared-access-signature-part-1.md).
  
El servicio vinculado de SAS de Almacenamiento de Azure le permite vincular una cuenta de Almacenamiento de Azure a una Factoría de datos de Azure con una Firma de acceso compartido (SAS). Esto proporciona a la factoría de datos acceso restringido o limitado por el tiempo a todos los recursos o a algunos específicos (blob o contenedor) del almacenamiento. En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de SAS de Almacenamiento de Azure.

| Propiedad | Descripción | Obligatorio |
| :-------- | :----------- | :-------- |
| type | La propiedad type debe establecerse en: **AzureStorageSas** | Sí |
| sasUri | Especifique el URI de Firma de acceso compartido a los recursos de Almacenamiento de Azure como blob, contenedor o tabla. Consulte las notas siguientes para obtener detalles. | Sí | 


**Ejemplo:**
  
	{  
		"name": "StorageSasLinkedService",  
		"properties": {  
			"type": "AzureStorageSas",  
			"typeProperties": {  
				"sasUri": "<storageUri>?<sasToken>"   
			}  
		}  
	}  

Al crear un **URI de SAS**, tenga en cuenta lo siguiente:

- La Factoría de datos de Azure solo admite **SAS de servicio**, no SAS de cuenta. Consulte [Tipos de firmas de acceso compartido](../articles/storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) para obtener más información acerca de estos dos tipos.
- Se deben establecer los **permisos** de lectura y escritura adecuados en los objetos, en función de cómo se utilizará el servicio vinculado (lectura, escritura, lectura y escritura) de la factoría de datos.
- Debe establecerse correctamente la **Hora de expiración**. Asegúrese de que el acceso a los objetos de Almacenamiento de Azure no expirará durante el período activo de la canalización.
- El URI debe crearse en el nivel correcto del contenedor/blob o la tabla, en función de la necesidad. Un URI de SAS de un blob de Azure permite que el servicio de la Factoría de datos tenga acceso a ese blob en particular. Un URI de SAS de un contenedor de blob de Azure permite que el servicio de la Factoría de datos procese una iteración en los blobs de ese contenedor. Si necesita proporcionar acceso a más o menos objetos más adelante, o actualizar el URI de SAS, no olvide actualizar el servicio vinculado con el nuevo URI.   

<!---HONumber=AcomDC_0224_2016-->