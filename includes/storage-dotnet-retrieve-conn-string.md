### Recuperación de la cadena de conexión
Puede usar el tipo **CloudStorageAccount** para representar la información de la cuenta de almacenamiento. Si está utilizando una plantilla de proyecto de Azure o hace referencia al espacio de nombres Microsoft.WindowsAzure.CloudConfigurationManager, puede utilizar el tipo **CloudConfigurationManager** para recuperar la cadena de conexión de almacenamiento y la información de la cuenta de almacenamiento de la configuración del servicio de Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Si está creando una aplicación sin ninguna referencia a Microsoft.WindowsAzure.CloudConfigurationManager y la cadena de conexión está situada en `web.config` o `app.config`, como se muestra anteriormente, puede utilizar **ConfigurationManager** para recuperar dicha cadena. Deberá agregar una referencia a System.Configuration.dll a su proyecto, así como otra declaración de espacio de nombres para ella:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.AppSettings["StorageConnectionString"]);

<!---HONumber=AcomDC_0309_2016-->