<properties
   pageTitle="Cifrado y descifrado de blobs en Almacenamiento de Microsoft Azure con Almacén de claves de Azure | Microsoft Azure"
   description="Este tutorial le guiará por los pasos para cifrar y descifrar un blob mediante el cifrado del lado cliente de Almacenamiento de Microsoft Azure con Almacén de claves de Azure."
   services="storage"
   documentationCenter=""
   authors="adhurwit"
   manager=""
   editor="tysonn"/>

<tags
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="01/06/2016"
   ms.author="lakasa"/>

# Cifrado y descifrado de blobs en Almacenamiento de Microsoft Azure con Almacén de claves de Azure

## Introducción

Este tutorial explica cómo hacer uso del cifrado de almacenamiento del lado cliente con Almacén de claves de Azure. Se explica cómo cifrar y descifrar un blob en una aplicación de consola con estas tecnologías.

**Tiempo estimado para completar el tutorial:** 20 minutos

Para obtener información general sobre el Almacén de claves de Azure, consulte [¿Qué es el Almacén de clave de Azure?](../key-vault/key-vault-whatis.md)

Para obtener información general acerca del cifrado de cliente para Almacenamiento de Azure, consulte [Cifrado del lado de cliente y Almacén de claves de Azure para el Almacenamiento de Microsoft Azure](storage-client-side-encryption.md).


## Requisitos previos

Para realizar este tutorial, necesitará lo siguiente:

- Una cuenta de almacenamiento de Azure.
- Visual Studio 2013 o posterior.
- Azure PowerShell


## Información general sobre el cifrado del lado cliente

Para obtener información general acerca del cifrado de cliente para Almacenamiento de Azure, consulte [Cifrado del lado de cliente y Almacén de claves de Azure para el Almacenamiento de Microsoft Azure](storage-client-side-encryption.md).

Esta es una breve descripción de cómo funciona el cifrado del lado cliente:

1. El SDK de cliente de Almacenamiento de Azure genera una clave de cifrado de contenido (CEK), que es una clave simétrica de un solo uso.
2. Los datos de usuario se cifran mediante esta CEK.
3. Se encapsula la CEK (cifrada) con la clave de cifrado de clave (KEK). La KEK se identifica mediante un identificador de clave y puede ser un par de clave asimétrico o una clave simétrica que puede administrarse de forma local o guardarse en Almacén de claves de Azure. El propio cliente de almacenamiento no tiene acceso a KEK. Simplemente invoca el algoritmo de ajuste de clave proporcionado por Almacén de claves. Los clientes pueden elegir usar proveedores personalizados para el ajuste y desajuste de la clave si así lo desean.
4. A continuación, se cargan los datos cifrados en el servicio Almacenamiento de Azure.


## Configuración de Almacén de claves de Azure
Para continuar con este tutorial, debe seguir los siguientes pasos que se describe en el tutorial: [Introducción a Almacén de claves de Azure](../key-vault/key-vault-get-started.md)

- Cree un almacén de claves.
- Agregue una clave o un secreto al almacén de claves.
- Registre una aplicación con Azure Active Directory.
- Autorice la aplicación para usar la clave o el secreto.

Anote el ClientID y ClientSecret que se generaron al registrar una aplicación con Azure Active Directory.

Cree ambas claves en el almacén de claves. A partir de aquí asumiremos para el resto del tutorial que ha usado los nombres siguientes: ContosoKeyVault y TestRSAKey1.


## Creación de una aplicación de consola con paquetes y AppSettings

En Visual Studio, cree una nueva aplicación de consola.

Agregue los paquetes de NuGet necesarios en la Consola del Administrador de paquetes.

	Install-Package WindowsAzure.Storage

	// This is the latest stable release for ADAL.
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	Install-Package Microsoft.Azure.KeyVault
	Install-Package Microsoft.Azure.KeyVault.Extensions


Agregar AppSettings al archivo App.Config.

	<appSettings>
	    <add key="accountName" value="myaccount"/>
	    <add key="accountKey" value="theaccountkey"/>
	    <add key="clientId" value="theclientid"/>
	    <add key="clientSecret" value="theclientsecret"/>
    	<add key="container" value="stuff"/>
	</appSettings>

Agregue las siguientes instrucciones `using` y asegúrese de agregar una referencia el proyecto System.Configuration.

	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Configuration;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using Microsoft.Azure.KeyVault;
	using System.Threading;		
	using System.IO;


## Agregar un método para obtener un token para la aplicación de consola

El siguiente método lo usan las clases de Almacén de claves que tienen que autenticarse para poder acceder a su almacén de claves.

	private async static Task<string> GetToken(string authority, string resource, string scope)
	{
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(
	        ConfigurationManager.AppSettings["clientId"],
	        ConfigurationManager.AppSettings["clientSecret"]);
		AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

	    if (result == null)
	        throw new InvalidOperationException("Failed to obtain the JWT token");

	    return result.AccessToken;
	}

## Acceso a Almacenamiento y Almacén de claves en el programa

En la función Main, agregue el código siguiente:

	// This is standard code to interact with Blob storage.
	StorageCredentials creds = new StorageCredentials(
		ConfigurationManager.AppSettings["accountName"],
       	ConfigurationManager.AppSettings["accountKey"]);
	CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
	CloudBlobClient client = account.CreateCloudBlobClient();
	CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
	contain.CreateIfNotExists();

	// The Resolver object is used to interact with Key Vault for Azure Storage.
	// This is where the GetToken method from above is used.
	KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);


> [AZURE.NOTE] Modelos de objetos de Almacén de claves
>
>Es importante entender que hay en realidad dos modelos de objeto de Almacén de claves a tener en cuenta: uno se basa en la API de REST (espacio de nombres KeyVault) y el otro es una extensión para el cifrado de cliente.

> El cliente de Almacén de claves interactúa con la API de REST y entiende las claves web JSON y los secretos de los dos tipos de elementos que están contenidos en Almacén de claves.

> Las extensiones de Almacén de claves son clases que parecen creadas específicamente para el cifrado de cliente en Almacenamiento de Azure. Contienen una interfaz para claves (IKey) y clases basadas en el concepto de un solucionador de claves. Hay dos implementaciones de IKey que necesita conocer: RSAKey y SymmetricKey. Ahora coinciden con las cosas que están contenidas en un almacén de datos, pero en este momento son clases independientes (la clave y el secreto recuperado por el cliente de almacén de claves no implementan IKey).


## Cifrado y carga de blob
Agregue el código siguiente para cifrar un blob y cargarlo en la cuenta de almacenamiento de Azure. El método **ResolveKeyAsync** que se usa devuelve una IKey.


	// Retrieve the key that you created previously.
	// The IKey that is returned here is an RsaKey.
	// Remember that we used the names contosokeyvault and testrsakey1.
    var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();


	// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

	// Reference a block blob.
	CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

	// Upload using the UploadFromStream method.
	using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
		blob.UploadFromStream(stream, stream.Length, null, options, null);


La siguiente es una captura de pantalla del [Portal de Azure clásico](https://manage.windowsazure.com) para un blob que se ha cifrado mediante el cifrado del lado cliente con una clave almacenada en Almacén de claves. La propiedad **KeyId** es el URI de la clave en Almacén de claves que actúa como la clave de cifrado de claves (KEK). La propiedad **EncryptedKey** contiene la versión cifrada de la CEK.

![Captura de pantalla en la que se muestran los metadatos del blob que incluyen los metadatos de cifrado][1]

> [AZURE.NOTE] Si observa el constructor BlobEncryptionPolicy, verá que puede aceptar una clave o una resolución. Tenga en cuenta que ahora no puede utilizar una resolución para el cifrado porque en la actualidad no admite una clave predeterminada.



## Descifrado y carga del blob
Es en el descifrado realmente cuando las clases de solucionador tienen sentido. El identificador de la clave usada para el cifrado se asocia con el blob en sus metadatos, así que no hace falta que recupere la clave ni que recuerde la asociación entre la clave y el blob. Solo tiene que asegurarse de que la clave se mantiene en Almacén de claves.

La clave privada de una clave RSA permanece en Almacén de claves, por lo que para que se produzca el descifrado, la clave cifrada de los metadatos del blob que contiene la CEC (clave de cifrado de contenido) se envía a Almacén de claves.

Agregue lo siguiente para descifrar el blob que acaba de cargar.

	// In this case, we will not pass a key and only pass the resolver because
	// this policy will only be used for downloading / decrypting.
	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
	    blob.DownloadToStream(np, null, options, null);


> [AZURE.NOTE] Existen un par de otros tipos de solucionadores que facilitan la administración de claves, incluidos AggregateKeyResolver y CachingKeyResolver.


## Uso de secretos de Almacén de claves
La manera de usar un secreto con cifrado del lado cliente es mediante la clase SymmetricKey, porque un secreto es esencialmente una clave simétrica. Sin embargo, como se mencionó anteriormente, un secreto en Almacén de claves no se asigna exactamente a una SymmetricKey. Existen algunos aspectos que debe comprender:


- La clave en una SymmetricKey tiene que tener una longitud fija: 128, 192, 256, 384 o 512 bits
- La clave en una SymmetricKey debe estar codificada en Base64.
- Un secreto de Almacén de clave que se use como una SymmetricKey tiene que tener un tipo de contenido de "application/octet-stream" en Almacén de claves.

Este es un ejemplo en PowerShell de creación de un secreto en Almacén de claves que se puede usar como una SymmetricKey. NOTA: El valor codificado, $key, es solo para fines de demostración. En su código, es recomendable generar esta clave.

	// Here we are making a 128-bit key so we have 16 characters.
	// 	The characters are in the ASCII range of UTF8 so they are
	//	each 1 byte. 16 x 8 = 128.
	$key = "qwertyuiopasdfgh"
	$b = [System.Text.Encoding]::UTF8.GetBytes($key)
	$enc = [System.Convert]::ToBase64String($b)
	$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

	// Substitute the VaultName and Name in this command.
	$secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"

En la aplicación de consola, puede usar la misma llamada que antes para recuperar este secreto como una SymmetricKey.

	SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    	"https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
        CancellationToken.None).GetAwaiter().GetResult();

Eso es todo. ¡Disfrute!

## Pasos siguientes

Para obtener más información sobre el uso de Almacenamiento de Microsoft Azure con C#, consulte [Biblioteca de cliente de Almacenamiento de Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Para obtener más información sobre la API de REST de blobs, consulte [API de REST del servicio de blobs](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Para obtener la información más reciente sobre Almacenamiento de Microsoft Azure, vaya al [Blog del equipo de Almacenamiento de Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/).


<!--Image references-->
[1]: ./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png

<!---HONumber=AcomDC_0218_2016-->