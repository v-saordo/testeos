<properties 
 pageTitle="Administración de la expiración del contenido del blob en la Red de entrega de contenido de Azure (CDN)" 
 description="" 
 services="cdn" 
 documentationCenter=".NET" 
 authors="camsoper" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="cdn" 
 ms.workload="media" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/02/2015" 
 ms.author="casoper"/>


#Administración de la expiración del contenido del blob en la Red de entrega de contenido de Azure (CDN)  

Los bloques que obtienen el máximo beneficio del almacenamiento en caché de la red CDN de Azure son aquellos a los que se accede frecuentemente durante su período de tiempo de vida (TTL). Un blob permanece en la memoria caché durante el período TTL y, a continuación, el servicio de blob lo actualiza una vez transcurrido ese tiempo. A continuación, el proceso se repite.

Dispone de dos opciones para controlar el tiempo TTL.

1.	No establecer valores de caché y utilizar, por tanto, el valor predeterminado de TTL de 7 días. 
2.	Establecer explícitamente la propiedad *x-ms-blob-cache-control* en una solicitud **Poner blob**, **Poner lista de bloques** o **Establecer propiedades del blob**, o usar la biblioteca administrada de Azure para establecer la propiedad [BlobProperties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx). El establecimiento de esta propiedad establecer el valor del encabezado *Cache-Control* para el blob. El valor del encabezado o la propiedad deben especificar el valor apropiado en segundos. Por ejemplo, para establecer el período de almacenamiento en cache máximo en un año, puede especificar el encabezado de solicitud como `x-ms-blob-cache-control: public, max-age=31556926`. Para obtener detalles acerca del establecimiento de encabezados de almacenamiento en caché, vea la [especificación HTTP/1.1](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).  

Cualquier contenido que desee almacenar en caché a través de la red CDN se debe almacenar en su cuenta de almacenamiento de Azure como un blob accesible públicamente. Para obtener más detalles acerca del servicio BLOB de Azure, vea **Conceptos del servicio de blobs**.

Hay varias formas de trabajar con el contenido del servicio de blobs:

-	Usando la API administrada proporcionada por **Referencia de la biblioteca administrada de Azure**.
-	Usando una herramienta de administración de almacenamiento de terceros.
-	Usando la API de REST de servicios de almacenamiento de Azure.  

El siguiente ejemplo de código es una aplicación de consola que usa la biblioteca administrada de Azure para crear un contenedor, establecer sus permisos para acceso público y crear un blob dentro del contenedor. También especifica explícitamente un intervalo de actualización deseado estableciendo el encabezado Cache-Control en el blob.

Suponiendo que ha habilitado CDN como se muestra más arriba, CDN almacena en caché el blob que se crea. Asegúrese de especificar las credenciales de cuenta usando su propia cuenta de almacenamiento y clave de acceso:

	using System;
	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.StorageClient;
	
	namespace BlobsInCDN
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            //Specify storage credentials.
	            StorageCredentialsAccountAndKey credentials = new StorageCredentialsAccountAndKey("storagesample",
	                "m4AHAkXjfhlt2rE2BN/hcUR4U2lkGdCmj2/1ISutZKl+OqlrZN98Mhzq/U2AHYJT992tLmrkFW+mQgw9loIVCg==");
	            
	            //Create a reference to your storage account, passing in your credentials.
	            CloudStorageAccount storageAccount = new CloudStorageAccount(credentials, true);
	            
	            //Create a new client object, which will provide access to Blob service resources.
	            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
	
	            //Create a new container.
	            CloudBlobContainer container = blobClient.GetContainerReference("cdncontent");
	            container.CreateIfNotExist();
	
	            //Specify that the container is publicly accessible.
	            BlobContainerPermissions containerAccess = new BlobContainerPermissions();
	            containerAccess.PublicAccess = BlobContainerPublicAccessType.Container;
	            container.SetPermissions(containerAccess);
	
	            //Create a new blob and write some text to it.
	            CloudBlob blob = blobClient.GetBlobReference("cdncontent/testblob.txt");
	            blob.UploadText("This is a test blob.");
	
	            //Set the Cache-Control header on the blob to specify your desired refresh interval.
	            blob.SetCacheControl("public, max-age=31536000");
	        }
	    }
	
	    public static class BlobExtensions
	    {
	        //A convenience method to set the Cache-Control header.
	        public static void SetCacheControl(this CloudBlob blob, string value)
	        {
	            blob.Properties.CacheControl = value;
	            blob.SetProperties();
	        }
	    }
	}

Compruebe que su blob está disponible a través de la dirección URL específica de la red CDN. Para el blob mostrado anteriormente, la dirección URL será similar a la siguiente:

	http://az1234.vo.msecnd.net/cdncontent/testblob.txt  

Si lo desea, puede utilizar una herramienta como **wget** o Fiddler para examinar los detalles de la solicitud y respuesta.

##Otras referencias

[Administración de la expiración del contenido del servicio en la nube en la Red de entrega de contenido de Azure (CDN)](./cdn-manage-expiration-of-cloud-service-content.md)

<!---HONumber=AcomDC_1203_2015-->