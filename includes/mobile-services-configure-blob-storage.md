Se registra un nuevo script de inserción que genera un SAS cuando se inserta un nuevo "Todo item".

0. Si todavía no ha creado su cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento](../storage/storage-create-storage-account.md).

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Almacenamiento**, haga clic en la cuenta de almacenamiento y haga clic en **Administrar claves**.

2. Tome nota de los valores en los campos **Nombre de cuenta de almacenamiento** y **Clave de acceso**.

   	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png)

3. En su servicio móvil, haga clic en la pestaña **Configurar**, desplácese hacia abajo hasta **Configuración de la aplicación** y escriba un par de **Nombre** y **Valor** para cada uno de los siguientes que obtuvo desde la cuenta de almacenamiento y, a continuación, haga clic en **Guardar**.

	+ `STORAGE_ACCOUNT_NAME`
	+ `STORAGE_ACCOUNT_ACCESS_KEY`

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

	La clave de acceso de la cuenta de almacenamiento se almacena cifrada en la configuración de aplicaciones. Puede tener acceso a esta clave desde cualquier script de servidor en tiempo de ejecución. Para obtener más información, vea [Configuración de aplicación].

4. En la pestaña Configurar, asegúrese de que el [Esquema dinámico](http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7) esté habilitado. Necesita que el esquema dinámico esté habilitado para agregar nuevas columnas a la tabla TodoItem. El esquema dinámico no debe habilitarse en ningún servicio de producción.

4. Haga clic en la pestaña **Datos** y, a continuación, haga clic en la tabla **TodoItem**.

5.  En **todoitem**, haga clic en la pestaña **Script** y seleccione **Insertar**, reemplace la función de inserción por el código siguiente y, a continuación, haga clic en **Guardar**:

		var azure = require('azure');
		var qs = require('querystring');
		var appSettings = require('mobileservice-config').appSettings;
		
		function insert(item, user, request) {
		    // Get storage account settings from app settings. 
		    var accountName = appSettings.STORAGE_ACCOUNT_NAME;
		    var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
		    var host = accountName + '.blob.core.windows.net';
		
		    if ((typeof item.containerName !== "undefined") && (
		    item.containerName !== null)) {
		        // Set the BLOB store container name on the item, which must be lowercase.
		        item.containerName = item.containerName.toLowerCase();
		
		        // If it does not already exist, create the container 
		        // with public read access for blobs.        
		        var blobService = azure.createBlobService(accountName, accountKey, host);
		        blobService.createContainerIfNotExists(item.containerName, {
		            publicAccessLevel: 'blob'
		        }, function(error) {
		            if (!error) {
		
		                // Provide write access to the container for the next 5 mins.        
		                var sharedAccessPolicy = {
		                    AccessPolicy: {
		                        Permissions: azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
		                        Expiry: new Date(new Date().getTime() + 5 * 60 * 1000)
		                    }
		                };
		
		                // Generate the upload URL with SAS for the new image.
		                var sasQueryUrl = 
		                blobService.generateSharedAccessSignature(item.containerName, 
		                item.resourceName, sharedAccessPolicy);
		
		                // Set the query string.
		                item.sasQueryString = qs.stringify(sasQueryUrl.queryString);
		
		                // Set the full path on the new new item, 
		                // which is used for data binding on the client. 
		                item.imageUri = sasQueryUrl.baseUrl + sasQueryUrl.path;
		
		            } else {
		                console.error(error);
		            }
		            request.execute();
		        });
		    } else {
		        request.execute();
		    }
		}

   	Así, se reemplaza la función que se invoca cuando se produce una inserción en la tabla TodoItem con un script nuevo. Este script nuevo genera una SAS nueva para la inserción, válida por 5 minutos, y asigna el valor de la SAS generada a la propiedad `sasQueryString` del elemento devuelto. La propiedad `imageUri` se establece también para la ruta de acceso del recurso del BLOB nuevo a fin de habilitar la visualización de imágenes durante el enlace en la interfaz de usuario de cliente.

	>[AZURE.NOTE]Este código crea una SAS para un BLOB individual. Si necesita cargar varios blobs en un contenedor usando la misma SAS, puede llamar al [método generateSharedAccessSignature](http://go.microsoft.com/fwlink/?LinkId=390455)</a> con un nombre de recurso de blob vacío como este:
	>                 
	>     blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);

A continuación, actualizará la aplicación de inicio rápido para agregar la funcionalidad de carga de imágenes usando la SAS generada en la inserción.
 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Configuración de aplicación]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=AcomDC_1203_2015-->