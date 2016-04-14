
##Instalación del cliente de almacenamiento en el proyecto de servicio móvil

Para poder generar una SAS para cargar imágenes en el almacenamiento de blobs, primero debe agregar el paquete de NuGet que instala la biblioteca de clientes de almacenamiento en el proyecto de servicio móvil.

1. En el **Explorador de soluciones** de Visual Studio, haga clic con el botón secundario en el proyecto de servicio móvil y, a continuación, seleccione **Administrar paquetes de NuGet**.

2. En el panel izquierdo, seleccione la categoría **En línea** y después **Solo estable**, busque **WindowsAzure.Storage**, haga clic en **Instalar** en el paquete **Almacenamiento de Azure** y, a continuación, acepte el contrato de licencia.

  	![](./media/mobile-services-configure-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	Con esto se agrega la biblioteca de clientes para servicios de almacenamiento de Azure al proyecto de servicio móvil.

##Actualización de la definición de TodoItem en el modelo de datos

La clase TodoItem define el objeto de datos, y tiene que agregar las mismas propiedades a esta clase que las que añadió al cliente.

1. En Visual Studio 2013, abra el proyecto de servicio móvil, expanda la carpeta DataObjects y, a continuación, abra el archivo de proyecto TodoItem.cs.
	
2. Agregue las siguientes propiedades nuevas a la clase **TodoItem**:

        public string containerName { get; set; }
		public string resourceName { get; set; }
		public string sasQueryString { get; set; }
		public string imageUri { get; set; } 

	Estas propiedades se utilizan para generar la SAS y para almacenar información de imagen. Tenga en cuenta que el uso de mayúsculas y minúsculas en estas propiedades coincide con la versión de back-end de JavaScript.

	>[AZURE.NOTE]Al usar el inicializador de base de datos predeterminado, Entity Framework eliminará la base de datos y la volverá a crear cuando detecte un cambio del modelo de datos en la definición de Code First. Para realizar este cambio en el modelo de datos y mantener los datos existentes en la base de datos, debe usar Migraciones de Code First. El inicializador predeterminado no se puede usar con una base de datos SQL en Azure. Para obtener más información, vea [Uso de Migraciones de Code First para actualizar el modelo de datos](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

##Actualización del controlador TodoItem para generar una firma de acceso compartido 

El elemento **TodoItemController** existente se actualiza de manera que el método **PostTodoItem** genera una SAS cuando se inserta un nuevo TodoItem. También puede

0. Si todavía no ha creado su cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento].

1. En el [Portal de Azure clásico](https://manage.windowsazure.com/), haga clic en **Almacenamiento**, haga clic en la cuenta de almacenamiento y haga clic en **Administrar claves**.

2. Tome nota de los valores en los campos **Nombre de cuenta de almacenamiento** y **Clave de acceso**.
 
3. En su servicio móvil, haga clic en la pestaña **Configurar**, desplácese hacia abajo hasta **Configuración de la aplicación** y escriba un par de **Nombre** y **Valor** para cada uno de los siguientes que obtuvo desde la cuenta de almacenamiento y, a continuación, haga clic en **Guardar**.

	+ `STORAGE_ACCOUNT_NAME`
	+ `STORAGE_ACCOUNT_ACCESS_KEY`

	![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

	La clave de acceso de la cuenta de almacenamiento se almacena cifrada en la configuración de aplicaciones. Puede tener acceso a esta clave desde cualquier script de servidor en tiempo de ejecución. Para obtener más información, vea [Configuración de aplicación].

4. En el Explorador de soluciones de Visual Studio, abra el archivo Web.config para el proyecto de servicios móviles y agregue la siguiente nueva configuración de aplicaciones reemplazando los marcadores de posición por el nombre de la cuenta de almacenamiento y la clave de acceso que acaba de establecer en el portal:

		<add key="STORAGE_ACCOUNT_NAME" value="**your_account_name**" />
		<add key="STORAGE_ACCOUNT_ACCESS_KEY" value="**your_access_token_secret**" />

	El servicio móvil usa esta configuración almacenada cuando se ejecuta en el equipo local, lo que le permite realizar una prueba del código antes de publicarlo. Cuando se ejecuta en Azure, el servicio móvil usa en su lugar los valores de la configuración de aplicaciones en el portal e ignora esta configuración de proyecto.

7.  En la carpeta Controladores, abra el archivo TodoItemController.cs y agregue las siguientes directivas **using**:

		using System;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
  
8.  Reemplace el método existente **PostTodoItem** por el siguiente código:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            string storageAccountName;
            string storageAccountKey;

            // Try to get the Azure storage account token from app settings.  
            if (!(Services.Settings.TryGetValue("STORAGE_ACCOUNT_NAME", out storageAccountName) |
            Services.Settings.TryGetValue("STORAGE_ACCOUNT_ACCESS_KEY", out storageAccountKey)))
            {
                Services.Log.Error("Could not retrieve storage account settings.");
            }

            // Set the URI for the Blob Storage service.
            Uri blobEndpoint = new Uri(string.Format("https://{0}.blob.core.windows.net", storageAccountName));

            // Create the BLOB service client.
            CloudBlobClient blobClient = new CloudBlobClient(blobEndpoint, 
                new StorageCredentials(storageAccountName, storageAccountKey));

            if (item.containerName != null)
            {
                // Set the BLOB store container name on the item, which must be lowercase.
                item.containerName = item.containerName.ToLower();

                // Create a container, if it doesn't already exist.
                CloudBlobContainer container = blobClient.GetContainerReference(item.containerName);
                await container.CreateIfNotExistsAsync();

                // Create a shared access permission policy. 
                BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

                // Enable anonymous read access to BLOBs.
                containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
                container.SetPermissions(containerPermissions);

                // Define a policy that gives write access to the container for 5 minutes.                                   
                SharedAccessBlobPolicy sasPolicy = new SharedAccessBlobPolicy()
                {
                    SharedAccessStartTime = DateTime.UtcNow,
                    SharedAccessExpiryTime = DateTime.UtcNow.AddMinutes(5),
                    Permissions = SharedAccessBlobPermissions.Write
                };

                // Get the SAS as a string.
                item.sasQueryString = container.GetSharedAccessSignature(sasPolicy); 

                // Set the URL used to store the image.
                item.imageUri = string.Format("{0}{1}/{2}", blobEndpoint.ToString(), 
                    item.containerName, item.resourceName);
            }

            // Complete the insert operation.
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

   	Este método POST genera ahora una SAS nueva para el elemento insertado, válida por 5 minutos, y asigna el valor de la SAS generada a la propiedad `sasQueryString` del elemento devuelto. La propiedad `imageUri` se establece también para la ruta de acceso del recurso del BLOB nuevo a fin de habilitar la visualización de imágenes durante el enlace en la interfaz de usuario de cliente.

	>[AZURE.NOTE]Este código crea una SAS para un BLOB individual. Si necesita cargar varios blobs en un contenedor utilizando la misma SAS, puede llamar al <a href="http://go.microsoft.com/fwlink/?LinkId=390455" target="_blank">método generateSharedAccessSignature</a> con un nombre de recurso de blob vacío, como este: <pre><code>blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);</code></pre>

A continuación, actualizará la aplicación de inicio rápido para agregar la funcionalidad de carga de imágenes usando la SAS generada en la inserción.
 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Creación de una cuenta de almacenamiento]: ../articles/storage/storage-create-storage-account.md
[Configuración de aplicación]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=AcomDC_1203_2015-->