<properties pageTitle="Usar el Almacén de claves de Azure desde una aplicación web | Microsoft Azure" description="Use este tutorial como ayuda para aprender a usar el Almacén de claves de Azure desde una aplicación web." services="key-vault" documentationCenter="" authors="adamhurwitz" manager="" tags="azure-resource-manager"//>

<tags 
	ms.service="key-vault" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2016" 
	ms.author="adhurwit"/>

# Uso del Almacén de claves de Azure desde una aplicación web #

## Introducción  
Use este tutorial como ayuda para aprender a usar el Almacén de claves de Azure desde una aplicación web. Le guiará por el proceso de obtener acceso a un secreto de un almacén de claves de Azure para que se pueda usar en su aplicación web.

**Tiempo estimado para completar el tutorial:** 15 minutos.


Para obtener información general sobre el Almacén de claves de Azure, consulte [¿Qué es el Almacén de clave de Azure?](key-vault-whatis.md)

## Requisitos previos 

Para realizar este tutorial, necesitará lo siguiente:

- Un URI de un secreto en un almacén de claves de Azure.
- Un Id. de cliente y un secreto de cliente para una aplicación web registrada en Azure Active Directory que tenga acceso a su Almacén de claves.
- Una aplicación web. Vamos a mostrarle los pasos para una aplicación ASP.NET MVC implementada en Azure como una aplicación web. 

> [AZURE.NOTE]  Para los fines de este tutorial, es esencial que haya realizado los pasos enumerados en [Introducción al Almacén de claves de Azure](key-vault-get-started.md) para así disponer del URI de un secreto y del id. de cliente y el secreto de cliente de una aplicación web.

La aplicación web que va a tener acceso el Almacén de claves es la que está registrada en Azure Active Directory y a la que se le ha concedido para él. Si este no es el caso, vuelva al apartado sobre cómo registrar una aplicación del tutorial de introducción y repita los pasos enumerados.

Este tutorial está diseñado para desarrolladores web que comprendan los conceptos básicos de la creación de aplicaciones web en Azure. Para obtener más información sobre las aplicaciones web de Azure, consulte [Información general de aplicaciones web](../app-service-web-overview.md).



## <a id="packages"></a>Incorporación de paquetes NuGet ##
Hay dos paquetes que la aplicación web debe tener instalados.

- Biblioteca de autenticación de Active Directory: contiene métodos para interactuar con Azure Active Directory y administrar la identidad de usuario.
- Biblioteca de Almacén claves de Azure: contiene métodos para interactuar con el Almacén de claves de Azure.


Los dos paquetes se pueden instalar mediante la Consola del Administrador de paquetes con el comando Install-Package.

	// this is currently the latest stable version of ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	Install-Package Microsoft.Azure.KeyVault 


## <a id="webconfig"></a>Modificación de Web.Config ##
Hay tres configuraciones de aplicaciones que deben agregarse al archivo web.config como se indica a continuación.

	<!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

	<!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


Si no va a hospedar la aplicación como una aplicación web de Azure, debe agregar los valores reales de ClientId, Secreto de cliente y URI de secreto al web.config. De lo contrario, deje estos valores ficticios porque iremos agregando los valores reales en el Portal de Azure para lograr un nivel de seguridad adicional.


## <a id="gettoken"></a>Agregar el método para obtener un token de acceso ##
Para usar la API del Almacén de claves necesita un token de acceso. El cliente del Almacén de claves controla las llamadas a la API del Almacén de claves, pero deberá suministrarle una función que obtenga el token de acceso.

A continuación se muestra el código para obtener un token de acceso de Azure Active Directory. Este código puede estar en cualquier parte de la aplicación. Quiero agregar una clase Utils o EncryptionHelper.

	//add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Web.Configuration;
	
	//this is an optional property to hold the secret after it is retrieved
	public static string EncryptSecret { get; set; }

	//the method that will be provided to the KeyVaultClient
	public async static Task<string> GetToken(string authority, string resource, string scope)
    {
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
	    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	    
	    if (result == null)
	    	throw new InvalidOperationException("Failed to obtain the JWT token");
	    
	    return result.AccessToken;
    }

> [AZURE.NOTE] La forma más sencilla de autenticar una aplicación de Azure AD es mediante un secreto de cliente que use un Id. de cliente y un secreto del cliente. Y su uso en una aplicación web permite la separación de deberes y proporciona mayor control sobre la administración de claves. Pero se basa en colocar el secreto de cliente en las opciones de configuración, lo que, para algunos, puede ser tan arriesgado como poner el secreto que se desea proteger en las opciones de configuración. A continuación encontrará información sobre el uso de un Id. de cliente y un certificado, en lugar de un Id. de cliente y un secreto de cliente para autenticar la aplicación de Azure AD.



## <a id="appstart"></a>Recuperación del secreto en Inicio de aplicación ##
Ahora tenemos el código para llamar a la API de Almacén de claves y recuperar el secreto. El siguiente código se puede colocar en cualquier parte siempre que se llame antes de que sea necesario usarlo. He colocado este código en el evento Inicio de aplicación en Global.asax para que se ejecute una vez en el inicio y permita que esté disponible el secreto para la aplicación.

	//add these using statements
	using Microsoft.Azure.KeyVault;
	using System.Web.Configuration;

	// I put my GetToken method in a Utils class. Change for wherever you placed your method. 
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

	var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;
	
	//I put a variable in a Utils class to hold the secret for general  application use. 
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Agregar la configuración de la aplicación en el Portal de Azure (opcional) ##
Si tiene una aplicación web de Azure ahora puede agregar los valores reales para AppSettings en el Portal de Azure. De esta manera, los valores reales no estarán en web.config sino que estarán protegidos a través del Portal donde cuenta con capacidades de control de acceso independientes. Estos valores se sustituirán por los valores que escribió en web.config. Asegúrese de que los nombres sean los mismos.

![Configuración de la aplicación mostrada en el Portal de Azure][1]


## Autenticación con un certificado, en lugar de con un secreto de cliente 
Otra forma de autenticar una aplicación de Azure AD es mediante el uso de un Id. de cliente y un certificado, en lugar de un Id. de cliente y un secreto de cliente. Estos son los pasos necesarios para usar un certificado en una aplicación web de Azure:

1. Obtener o crear un certificado
2. Asociar el certificado a una aplicación de Azure AD
3. Agregar código a la aplicación web para que use el certificado
4. Agregar un certificado a la aplicación web


**Obtener o crear un certificado** Para nuestros propósito, crearemos un certificado de prueba. A continuación encontrará un par de comandos que se pueden usar en un símbolo del sistema para desarrolladores para crear un certificado. Cambie al directorio en el que desea que se creen los archivos de certificado.

	makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
	pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

Tome nota de la fecha de finalización y la contraseña del archivo .pfx (en este ejemplo: 31/07/2016 y test123). Las necesitará más adelante.

Para más información sobre cómo crear un certificado de prueba, vea [Procedimientos para crear su propio certificado de prueba](https://msdn.microsoft.com/library/ff699202.aspx).


**Asociar el certificado a una aplicación de Azure AD** Ahora que tiene un certificado, tendrá que asociarlo a una aplicación de Azure AD. Pero actualmente el Portal de administración de Azure no lo admite. Por consiguiente, tendrá que usar Powershell. A continuación verá los comandos que debe ejecutar:

	$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
	
	PS C:\> $x509.Import("C:\data\KVWebApp.cer")
	
	PS C:\> $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())
	
	PS C:\> $now = [System.DateTime]::Now
	
	# this is where the end date from the cert above is used
	PS C:\> $yearfromnow = [System.DateTime]::Parse("2016-07-31") 
	
	PS C:\> $adapp = New-AzureADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow
	
	PS C:\> $sp = New-AzureADServicePrincipal -ApplicationId $adapp.ApplicationId
	
	PS C:\> Set-AzureKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToKeys all -ResourceGroupName 'contosorg'
	
	# get the thumbprint to use in your app settings
	PS C:\>$x509.Thumbprint

Después de ejecutar estos comandos, podrá ver la aplicación en Azure AD. Si no ve la aplicación al principio, busque "Aplicaciones que tiene mi compañía", en lugar de "Aplicaciones que usa mi compañía".

Para obtener más información acerca de los objetos Application y los objetos ServicePrincipal de Azure AD, consulte [Objetos Application y objetos ServicePrincipal](../active-directory/active-directory-application-objects.md)



**Agregar código a la aplicación web para que use el certificado** Ahora agregaremos código a la aplicación web para tener acceso al certificado y usarlo para la autenticación.

En primer lugar, este es el código para tener acceso al certificado.

    public static class CertificateHelper
    {
        public static X509Certificate2 FindCertificateByThumbprint(string findValue)
        {
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            try
            {
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint, 
                    findValue, false); // Don't validate certs, since the test root isn't installed.
                if (col == null || col.Count == 0)
                    return null;
                return col[0];
            }
            finally
            {
                store.Close();
            }
        }
    }


Observe que StoreLocation es CurrentUser, en lugar de LocalMachine. Fíjese también en que usamos 'false' en el método Find, ya que usamos un certificado de prueba.


El siguiente código usa CertificateHelper y crea ClientAssertionCertificate, que es necesario para la autenticación.

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


Este es el nuevo código para obtener el token de acceso. Reemplaza el método GetToken anterior. Para su comodidad, le hemos dado un nombre diferente.

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

Para facilitar su uso, hemos colocado todo este código en la clase Utils del proyecto de aplicación web.

El último cambio de código se da en el método Application\_Start. En primer lugar, es preciso llamar al método GetCert() para cargar ClientAssertionCertificate. Y, a continuación, se cambia el método de devolución de llamada que se suministra al crear un nuevo KeyVaultClient. Tenga en cuenta que esto reemplazará el código anterior.

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**Agregar un certificado a la aplicación web** La adición de un certificado a una aplicación web es un sencillo proceso de dos pasos. En primer lugar, vaya al Portal de Azure y navegue a la aplicación web. En la hoja Configuración de la aplicación web, haga clic en la entrada de "Dominios personalizados y SSL". En la hoja que se abre podrá cargar el certificado que creó anteriormente, KVWebApp.pfx, asegúrese de que recuerda la contraseña del archivo pfx.

![Adición de un certificado a una aplicación web en el Portal de Azure][2]


Lo último que debe hacer es agregar una configuración de aplicación a la aplicación web cuyo nombre es WEBSITE\_LOAD\_CERTIFICATES y su valor es *. Esto garantizará que se cargan todos los certificados. Si desea cargar solo los certificados que ha cargado, puede escribir una lista separada por comas de sus huellas digitales.

Para obtener más información sobre cómo agregar un certificado a una aplicación Web, consulte [Uso de certificados en las aplicaciones de Sitios web de Azure](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)



## <a id="next"></a>Pasos siguientes ##


Para conocer las referencias de programación, consulte [Referencia de la API de cliente de C# del Almacén de claves](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 

<!---HONumber=AcomDC_0204_2016-->