<properties
	pageTitle="Cómo utilizar el almacenamiento de blobs de Xamarin (vista previa) | Microsoft Azure"
	description="La biblioteca de cliente de almacenamiento de Azure para la vista previa de Xamarin permite a los desarrolladores crear aplicaciones de iOS, Android y de la Tienda Windows con sus interfaces de usuario nativas. Este tutorial muestra cómo utilizar Xamarin para crear una aplicación Android que usa el Almacenamiento de blobs de Azure."
	services="storage"
	documentationCenter="xamarin"
	authors="micurd"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/21/2016"
	ms.author="micurd"/>

# Cómo utilizar el almacenamiento de blobs de Xamarin (vista previa)

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Información general

Xamarin permite a los desarrolladores usar un código base C# compartida para crear aplicaciones de iOS, Android y de la Tienda Windows con sus interfaces de usuario nativas. La biblioteca de cliente de almacenamiento de Azure para Xamarin es una biblioteca de vista previa; tenga en cuenta que puede cambiar en el futuro.

Este tutorial muestra cómo utilizar el almacenamiento de blobs de Azure con una aplicación de Android Xamarin. Para obtener información sobre el Almacenamiento de Azure antes de acceder al código, consulte la sección [Pasos siguientes](#next-steps) que se encuentra al final de este documento.

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Generar una firma de acceso compartido

Cuando se desarrolla con la biblioteca de cliente de Almacenamiento de Azure para Xamarin, no se puede autenticar el acceso a una cuenta de Almacenamiento de Azure mediante las claves de acceso de cuenta. Esto es para evitar que las credenciales de cuenta se distribuyan a los usuarios que descarguen la aplicación. En su lugar, recomendamos el uso de firmas de acceso compartido (SAS) que no exponen sus credenciales de cuenta.

En esta guía, usaremos Azure PowerShell para generar un token SAS. A continuación, crearemos una aplicación Xamarin que usa la SAS generada.

En primer lugar, necesitará instalar Azure PowerShell. Consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md#Install) para obtener instrucciones.

A continuación, abra Azure PowerShell y ejecute los comandos siguientes. No olvide reemplazar `ACCOUNT_NAME` y `ACCOUNT_KEY== ` por las credenciales de su cuenta de almacenamiento. Reemplace `CONTAINER_NAME` por el nombre que desee.

    PS C:\> $context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
	PS C:\> New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
	PS C:\> $now = Get-Date
	PS C:\> New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

El URI de la firma de acceso compartido para el nuevo contenedor debe ser similar al siguiente:

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

La firma de acceso compartido que creó en el contenedor será válida durante el día siguiente. La firma concede permisos totales (*es decir*, lectura, escritura, eliminación y enumeración) para blobs situados dentro del contenedor.

Para obtener más información sobre firmas de acceso compartido, vea [Firmas de acceso compartido: Creación y uso de una SAS con Almacenamiento de blobs](storage-dotnet-shared-access-signature-part-2.md).

## Creación de una nueva aplicación Xamarin

Para este tutorial, crearemos nuestra aplicación Xamarin en Visual Studio. Siga estos pasos para crear la aplicación:

1. Descargue e instale [Visual Studio](https://www.visualstudio.com/).
2. Descargue e instale [Xamarin](http://xamarin.com/platform).
3. Abra Visual Studio y seleccione **Archivo > Nuevo > Proyecto > Android > Aplicación en blanco(Android)**.
4. Haga clic con el botón derecho en el proyecto en el panel del Explorador de soluciones y seleccione **Administrar paquetes de NuGet**. A continuación, busque **Almacenamiento de Azure** e instale **Almacenamiento de Azure 4.4.0-vista previa**.

Ahora debería tener una aplicación que le permita hacer clic en un botón e incrementar un contador.

## Utilice la firma de acceso compartido para realizar operaciones de contenedor

A continuación, agregue código para llevar a cabo una serie de operaciones de contenedor con el URI de SAS que generó.

Primero agregue lo siguiente **mediante** instrucciones:

	using System.IO;
	using System.Text;
	using System.Threading.Tasks;
	using Microsoft.WindowsAzure.Storage.Blob;


A continuación, agregue una línea para el token SAS. Reemplace la cadena `"SAS_URI"` por el URI de SAS que generó en Azure PowerShell. A continuación, agregue una línea para una llamada al método `UseContainerSAS` que crearemos a continuación. Tenga en cuenta que la palabra clave **async** se ha agregado antes del delegado.


	public class MainActivity : Activity
	{
    	int count = 1;
    	string sas = "SAS_URI";
    	protected override void OnCreate(Bundle bundle)
    	{
        	base.OnCreate(bundle);

        	// Set our view from the "main" layout resource
        	SetContentView(Resource.Layout.Main);

        	// Get our button from the layout resource, and attach an event to it
        	Button button = FindViewById<Button>(Resource.Id.MyButton);

        	button.Click += async delegate	{
             	button.Text = string.Format("{0} clicks!", count++);
             	await UseContainerSAS(sas);
         	};
     }

Agregue un nuevo método, `UseContainerSAS`, en el método `OnCreate`.

	static async Task UseContainerSAS(string sas)
	{
    	//Try performing container operations with the SAS provided.

    	//Return a reference to the container using the SAS URI.
    	CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));
    	string date = DateTime.Now.ToString();
    	try
    	{
        	//Write operation: write a new blob to the container.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_" + date + ".txt");

        	string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        	MemoryStream msWrite = new
        	MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        	msWrite.Position = 0;
        	using (msWrite)
         	{
             	await blob.UploadFromStreamAsync(msWrite);
         	}
         	Console.WriteLine("Write operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Write operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
     	try
     	{
        	//Read operation: Get a reference to one of the blobs in the container and read it.
        	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
        	string data = await blob.DownloadTextAsync();

        	Console.WriteLine("Read operation succeeded for SAS " + sas);
        	Console.WriteLine("Blob contents: " + data);
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Additional error information: " + e.Message);
       		Console.WriteLine("Read operation failed for SAS " + sas);
        	Console.WriteLine();
     	}
     	Console.WriteLine();
     	try
     	{
        	//Delete operation: Delete a blob in the container.
         	CloudBlockBlob blob = container.GetBlockBlobReference("sasblob_” + date + “.txt");
         	await blob.DeleteAsync();

         	Console.WriteLine("Delete operation succeeded for SAS " + sas);
         	Console.WriteLine();
     	}
     	catch (Exception e)
     	{
        	Console.WriteLine("Delete operation failed for SAS " + sas);
        	Console.WriteLine("Additional error information: " + e.Message);
        	Console.WriteLine();
     	}
	}

## Ejecución de la aplicación

Ahora puede ejecutar esta aplicación en un emulador o un dispositivo Android.

Aunque esta introducción se ha centrado en Android, puede utilizar el método `UseContainerSAS` en sus aplicaciones de Tienda Windows o de iOS. Xamarin también permite a los desarrolladores crear aplicaciones de Windows Phone; sin embargo, nuestra biblioteca todavía no admite esto.

## Pasos siguientes

En este tutorial, ha aprendido a utilizar el Almacenamiento de blobs de Azure y SAS con una aplicación Xamarin. Como un ejercicio adicional, puede aplicarse un patrón similar para generar un token SAS para una tabla o una cola de Azure.

Obtenga más información acerca de blobs, tablas y colas mediante la consulta de los vínculos siguientes:

- [Introducción a Almacenamiento de Microsoft Azure](storage-introduction.md)
- [Introducción al Almacenamiento de blobs de Azure mediante .NET](storage-dotnet-how-to-use-blobs.md)
- [Introducción al Almacenamiento de tablas de Azure mediante .NET](storage-dotnet-how-to-use-tables.md)
- [Introducción al Almacenamiento en cola de Azure mediante .NET](storage-dotnet-how-to-use-queues.md)
- [Introducción a Almacenamiento de archivos de Azure en Windows](storage-dotnet-how-to-use-files.md)
- [Introducción a la utilidad de línea de comandos AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0224_2016-->