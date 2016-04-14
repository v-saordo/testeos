<properties
   pageTitle="Autenticación de una entidad de servicio con Azure Resource Manager | Microsoft Azure"
   description="Describe cómo conceder acceso a una entidad de servicio a través del control de acceso basado en rol y autenticarla. Muestra cómo realizar estas tareas con PowerShell y la CLI de Azure."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="02/29/2016"
   ms.author="tomfitz"/>

# Autenticación de una entidad de servicio con el Administrador de recursos de Azure

En este tema se muestra cómo permitir que una entidad de servicio (por ejemplo, un proceso, una aplicación o un servicio automatizado) tenga acceso a otros recursos de la suscripción. Con el Administrador de recursos de Azure, puede usar el control de acceso basado en rol para conceder las acciones permitidas a una entidad de servicio y autenticar a esa entidad de servicio.

En este tema se muestra cómo usar Azure PowerShell o la CLI de Azure para Mac, Linux y Windows para crear una aplicación y una entidad de servicio, asignar un rol a la entidad de servicio y autenticarse como la entidad de servicio. Si no tiene instalado Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](./powershell-install-configure.md). Si no tiene la CLI de Azure instalada, consulte [Instalación y configuración de la interfaz de la línea de comandos de Azure](xplat-cli-install.md). Para información sobre el uso del portal para llevar a cabo estos pasos, vea [Creación de aplicación de Active Directory y entidad de servicio mediante el portal](resource-group-create-service-principal-portal.md).

## Conceptos
1. Azure Active Directory: un servicio de administración de identidades y acceso para la nube. Para obtener más información, consulte [¿Qué es Azure Active Directory?](active-directory/active-directory-whatis.md)
2. Entidad de servicio: una instancia de una aplicación en un directorio que necesita acceder a otros recursos.
3. Aplicación de Active Directory: un registro de directorio que identifica una aplicación en AAD.

Para obtener una explicación más detallada de las aplicaciones y entidades de servicio, consulte [Objetos de aplicación y de entidad de servicio](active-directory/active-directory-application-objects.md). Para obtener más información acerca de la autenticación de Active Directory, vea [Escenarios de autenticación en Azure AD](active-directory/active-directory-authentication-scenarios.md).

Este tema muestra 4 rutas para crear una aplicación de Active Directory y autenticar esa aplicación. Las rutas varían en función de si desea utilizar una contraseña o un certificado para la autenticación y si prefiere usar PowerShell o la CLI de Azure. Las rutas son:

- [Autenticar con contraseña: PowerShell](#authenticate-with-password---powershell)
- [Autenticar con certificado: PowerShell](#authenticate-with-certificate---powershell)
- [Autenticar con contraseña: CLI de Azure](#authenticate-with-password---azure-cli)
- [Autenticar con certificado: CLI de Azure](#authenticate-with-certificate---azure-cli)

## Autenticar con contraseña: PowerShell

En esta sección, llevará a cabo los pasos para crear una entidad de servicio para una aplicación de Azure Active Directory, asignar un rol a la entidad de servicio y autenticarse como la entidad de servicio proporcionando el identificador de la aplicación y la contraseña.

1. Inicie sesión en su cuenta.

        PS C:\> Login-AzureRmAccount

1. Cree una nueva aplicación de Active Directory ejecutando el comando **New-AzureRmADApplication**. Proporcione un nombre para mostrar para la aplicación, el URI para una página que describe la aplicación (no se comprueba el vínculo), los URI que identifican la aplicación y la contraseña para la identidad de aplicación.

        PS C:\> $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -Password "<Your_Password>"

     Examine el nuevo objeto de aplicación. La propiedad **ApplicationId** es necesaria para la creación de entidades de servicio, las asignaciones de roles y la adquisición del token de acceso.

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        ApplicationObjectId     : c95e67a3-403c-40ac-9377-115fa48f8f39
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}

2. Cree una entidad de servicio para la aplicación pasando el identificador de la aplicación de Active Directory.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

     Ahora ha creado una entidad de servicio en el directorio, pero el servicio no tiene asignado ningún permiso o ámbito. Debe conceder explícitamente permisos a la entidad de servicio a fin de realizar operaciones en cierto ámbito.

3. Conceda los permisos de la entidad de servicio en su suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para el parámetro **ServicePrincipalName**, ofrezca el valor de **ApplicationId** o **IdentifierUris** que usó al crear la aplicación. Para más información sobre el control de acceso basado en roles, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

Ha creado una aplicación de Active Directory y una entidad de servicio para esa aplicación. Ha asignado la entidad de servicio a un rol. Ahora, debe iniciar sesión como entidad de servicio para realizar operaciones como entidad de servicio. En este tema se muestran tres opciones:

- [Proporcionar credenciales a través de PowerShell de forma manual](#manually-provide-credentials-through-powershell)
- [Proporcionar credenciales a través de un script automatizado de PowerShell](#provide-credentials-through-automated-powershell-script)
- [Proporcionar credenciales a través de código en una aplicación](#provide-credentials-through-code-in-an-application)

<a id="manually-provide-credentials-through-powershell" />
### Proporcionar credenciales a través de PowerShell de forma manual

Puede proporcionar manualmente las credenciales para la entidad de servicio al ejecutar comandos o scripts a petición.

1. Cree un nuevo objeto **PSCredential** que contiene las credenciales mediante la ejecución del comando **Get-Credential**.

        PS C:\> $creds = Get-Credential

2. Se le pedirá que escriba sus credenciales. Para el nombre de usuario, utilice el valor de **ApplicationId** o **IdentifierUris** que utilizó al crear la aplicación. Para la contraseña, use la que especificó al crear la cuenta.

     ![escribir credenciales][1]

3. Recupere la suscripción en la que se creó la asignación de roles. Esta suscripción se utilizará para obtener el valor de **TenantId** del inquilino en el que reside la asignación de rol de la entidad de servicio.

        PS C:\> $subscription = Get-AzureRmSubscription

     Si ha creado la asignación de rol en una suscripción que no sea la suscripción seleccionada actualmente, puede especificar los parámetros **SubscriptionId** o **SubscriptionName** para recuperar una suscripción diferente.

4. Inicie sesión como la entidad de servicio mediante el uso del cmdlet **Login-AzureRmAccount**, pero proporcione el objeto de credenciales y especifique que esta cuenta es una entidad de servicio.

        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -Tenant $subscription.TenantId
        Environment           : AzureCloud
        Account               : {guid}
        Tenant                : {guid}
        Subscription          : {guid}
        CurrentStorageAccount :

Ahora está autenticado como la entidad de servicio para la aplicación de Active Directory que ha creado.

<a id="provide-credentials-through-automated-powershell-script" />
### Proporcionar credenciales a través de un script automatizado de PowerShell

Esta sección muestra cómo iniciar sesión como la entidad de servicio sin tener que escribir manualmente las credenciales. No es necesario proporcionar manualmente la contraseña porque se recuperan desde un Almacén de claves.

> [AZURE.NOTE] La inclusión directa de una contraseña en el script de PowerShell no es segura porque la contraseña se expone como texto. Por ello, utilice en su lugar un servicio como el Almacén de claves para almacenar la contraseña y recuperarla al ejecutar el script.

En estos pasos se supone que ha configurado un Almacén de claves y un secreto que almacena la contraseña. Para implementar un Almacén de claves y un secreto a través de una plantilla, consulte [Key Vault template format]() (Formato de plantilla de almacén de claves). Para obtener información sobre el Almacén de claves, consulte [Introducción al Almacén de claves de Azure](./key-vault/key-vault-get-started.md).

1. Recupere su contraseña (en el ejemplo siguiente, está almacenada como secreto con el nombre **appPassword**) del Almacén de claves.

        PS C:\> $secret = Get-AzureKeyVaultSecret -VaultName examplevault -Name appPassword
        
2. Obtenga su aplicación de Active Directory. Tendrá que especificar el identificador de la aplicación al iniciar sesión.

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"

3. Cree un nuevo objeto **PSCredential** proporcionando el identificador de la aplicación y la contraseña como credenciales.

        PS C:\> $creds = New-Object System.Management.Automation.PSCredential ($azureAdApplication.ApplicationId, $secret.SecretValue)
    
4. Recupere la suscripción en la que se creó la asignación de roles. Esta suscripción se utilizará más adelante para obtener el valor de **TenantId** del inquilino en el que reside la asignación de rol de la entidad de servicio.

        PS C:\> $subscription = Get-AzureRmSubscription

     Si ha creado la asignación de rol en una suscripción que no sea la suscripción seleccionada actualmente, puede especificar los parámetros **SubscriptionId** o **SubscriptionName** para recuperar una suscripción diferente.

5. Inicie sesión como la entidad de servicio mediante el uso del cmdlet **Login-AzureRmAccount**, pero proporcione el objeto de credenciales y especifique que esta cuenta es una entidad de servicio.
    
        PS C:\> Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $subscription.TenantId

Ahora está autenticado como la entidad de servicio para la aplicación de Active Directory que ha creado.

<a id="provide-credentials-through-code-in-an-application" />
### Proporcionar credenciales a través de código en una aplicación

Para autenticarse desde una aplicación .NET, incluya el código siguiente. Necesitará el identificador de aplicación para la aplicación de Active Directory, la contraseña y el identificador del inquilino para la suscripción. Después de recuperar el token de acceso, podrá trabajar con los recursos de la suscripción.

    public static string GetAccessToken()
    {
      var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
      var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
      var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

      if (result == null) {
        throw new InvalidOperationException("Failed to obtain the JWT token");
      }

      string token = result.AccessToken;

      return token;
    }


## Autenticar con certificado: PowerShell

En esta sección, llevará a cabo los pasos para crear una entidad de servicio para una aplicación de Azure Active Directory, asignar un rol a la entidad de servicio y autenticarse como la entidad de servicio proporcionando un certificado. En este tema se supone que se le ha emitido un certificado.

Muestra dos maneras de trabajar con certificados: credenciales de clave y valores de clave. Puede utilizar cualquier enfoque.

En primer lugar, debe configurar algunos valores en PowerShell que utilizará más adelante al crear la aplicación.

1. Inicie sesión en su cuenta.

        PS C:\> Login-AzureRmAccount

1. Para ambos planteamientos, cree un objeto X509Certificate2 desde su certificado y recupere el valor de la clave. Utilice la ruta de acceso a su certificado y la contraseña de ese certificado.

        $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")
        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Si usa credenciales de clave, cree el objeto de credenciales de clave y establezca su valor en `$keyValue` del paso anterior. En el ejemplo siguiente se incluye una llamada a `Add-Type` para agregar un tipo de un ensamblado. La ruta que se muestra en el ejemplo debería ser similar a la suya, pero con algunas diferencias.

        Add-Type -Path 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ResourceManager\AzureResourceManager\AzureRM.Resources\Microsoft.Azure.Commands.Resources.dll'
        $currentDate = Get-Date
        $endDate = $currentDate.AddYears(1)
        $keyId = [guid]::NewGuid()
        $keyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $keyCredential.StartDate = $currentDate
        $keyCredential.EndDate= $endDate
        $keyCredential.KeyId = $keyId
        $keyCredential.Type = "AsymmetricX509Cert"
        $keyCredential.Usage = "Verify"
        $keyCredential.Value = $keyValue

3. Cree una aplicación en el directorio. El primer comando muestra cómo utilizar valores de clave.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert       
        
    O bien, utilice el segundo ejemplo para asignar credenciales de clave.

         $azureAdApplication = New-AzureRmADApplication -DisplayName "example" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyCredentials $keyCredential

    Examine el nuevo objeto de aplicación. La propiedad **ApplicationId** es necesaria para la creación de entidades de servicio, las asignaciones de roles y la adquisición de tokens de acceso.

        PS C:\> $azureAdApplication

        DisplayName             : exampleapp
        Type                    : Application
        ApplicationId           : 1975a4fd-1528-4086-a992-787dbd23c46b
        ApplicationObjectId     : 9665e5f3-84b7-4344-92e2-8cc20ad16a8b
        AvailableToOtherTenants : False
        AppPermissions          : {}
        IdentifierUris          : {https://www.contoso.org/example}
        ReplyUrls               : {}       

4. Cree una entidad de servicio para la aplicación pasando el identificador de la aplicación de Active Directory.

        PS C:\> New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    Ahora ha creado una entidad de servicio en el directorio, pero el servicio no tiene asignado ningún permiso o ámbito. Debe conceder explícitamente permisos a la entidad de servicio a fin de realizar operaciones en cierto ámbito.

5. Conceda los permisos de la entidad de servicio en su suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para el parámetro **ServicePrincipalName**, ofrezca el valor de **ApplicationId** o **IdentifierUris** que usó al crear la aplicación. Para más información sobre el control de acceso basado en roles, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

        PS C:\> New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $azureAdApplication.ApplicationId

Ha creado una aplicación de Active Directory y una entidad de servicio para esa aplicación. Ha asignado la entidad de servicio a un rol. Ahora, debe iniciar sesión como entidad de servicio para realizar operaciones como entidad de servicio. En este tema se muestran dos opciones:

- [Proporcionar un certificado a través de un script automatizado de PowerShell](#provide-certificate-through-automated-powershell-script)
- [Proporcionar un certificado a través de código en una aplicación](#provide-certificate-through-code-in-an-application)

<a id="provide-certificate-through-automated-powershell-script" />
### Proporcionar un certificado a través de un script automatizado de PowerShell

1. Obtenga su aplicación de Active Directory. Tendrá que especificar el identificador de la aplicación al iniciar sesión.

        PS C:\> $azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://www.contoso.org/example"
        
2. Recupere la suscripción en la que se creó la asignación de roles. Esta suscripción se utilizará más adelante para obtener el valor de **TenantId** del inquilino en el que reside la asignación de rol de la entidad de servicio.

        PS C:\> $subscription = Get-AzureRmSubscription

     Si ha creado la asignación de rol en una suscripción que no sea la suscripción seleccionada actualmente, puede especificar los parámetros **SubscriptionId** o **SubscriptionName** para recuperar una suscripción diferente.

3. Obtenga el certificado que se va a utilizar para la autenticación.

        PS C:\> $cert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @("C:\certificates\examplecert.pfx", "yourpassword")

4. Para autenticar en PowerShell, proporcione la huella digital del certificado, el identificador de la aplicación y el identificador del inquilino.

        PS C:\> Login-AzureRmAccount -CertificateThumbprint $cert.Thumbprint -ApplicationId $azureAdApplication.ApplicationId -ServicePrincipal -TenantId $subscription.TenantId

Ahora está autenticado como la entidad de servicio para la aplicación de Active Directory que ha creado.

<a id="provide-certificate-through-code-in-an-application" />
### Proporcionar un certificado a través de código en una aplicación

Para autenticarse desde una aplicación .NET, incluya el código siguiente. Tras la recuperación del cliente, puede tener acceso a recursos de la suscripción.

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
    try 
    { 
      store.Open(OpenFlags.ReadOnly); 
      var certCollection = store.Certificates; 
      var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
      cert = certs[0]; 
    } 
    finally 
    { 
      store.Close(); 
    }        

    var certCred = new ClientAssertionCertificate(clientId, cert); 
    var token = authContext.AcquireToken("https://management.core.windows.net/", certCred); 
    var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken); 
    var client = new ResourceManagementClient(creds); 
        

## Autenticar con contraseña: CLI de Azure

Comenzará creando una entidad de servicio. Para ello, debemos crear una aplicación en el directorio. Esta sección le guiará a través de la creación de una nueva aplicación en el directorio.

1. Cambie al modo de Administrador de recursos de Azure e inicie sesión en su cuenta.

        azure config mode arm
        azure login

2. Cree una nueva aplicación de Active Directory ejecutando el comando **azure ad app create**. Proporcione un nombre para mostrar para la aplicación, el URI para una página que describe la aplicación (no se comprueba el vínculo), los URI que identifican la aplicación y la contraseña para la identidad de aplicación.

        azure ad app create --name "exampleapp" --home-page "https://www.contoso.org" --identifier-uris "https://www.contoso.org/example" --password <Your_Password>
        
    Se devuelve la aplicación de Active Directory. La propiedad AppId es necesaria para la creación de entidades de servicio, las asignaciones de roles y la adquisición de tokens de acceso.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

3. Cree a una entidad de servicio para la aplicación. Proporcione el identificador de la aplicación que se devolvió en el paso anterior.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Se devuelve la nueva entidad de servicio. Cuando se conceden permisos, es necesario el identificador de objeto.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK

    Ahora ha creado una entidad de servicio en el directorio, pero el servicio no tiene asignado ningún permiso o ámbito. Debe conceder explícitamente permisos a la entidad de servicio a fin de realizar operaciones en cierto ámbito.

4. Conceda los permisos de la entidad de servicio en su suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para más información sobre el control de acceso basado en roles, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

Ha creado una aplicación de Active Directory y una entidad de servicio para esa aplicación. Ha asignado la entidad de servicio a un rol. Ahora, debe iniciar sesión como entidad de servicio para realizar operaciones como entidad de servicio. En este tema se muestran tres opciones:

- [Proporcionar credenciales a través de la CLI de Azure de forma manual](#manually-provide-credentials-through-azure-cli)
- [Proporcionar credenciales a través de un script automatizado de la CLI de Azure](#provide-credentials-through-automated-azure-cli-script)
- [Proporcionar credenciales a través de código en una aplicación](#provide-credentials-through-code-in-an-application-cli)

<a id="manually-provide-credentials-through-Azure-cli" />
### Proporcionar credenciales a través de la CLI de Azure de forma manual

Si desea iniciar sesión manualmente como la entidad de servicio, puede usar el comando **azure login** . Debe indicar el identificador del inquilino, el identificador de la aplicación y la contraseña. La inclusión directa de la contraseña en el script no es segura porque la contraseña se almacena en el archivo. Consulte la sección siguiente para conocer la mejor opción cuando se ejecuta un script automatizado.

1. Determine el valor de **TenantId** de la suscripción que contiene la entidad de servicio. Debe quitar las comillas dobles del principio y el final que se devuelven de la salida json antes de pasarlo como parámetro.

        tenantId=$(azure account show -s <subscriptionId> --json | jq '.[0].tenantId' | sed -e 's/^"//' -e 's/"$//')

2. Para el nombre de usuario, emplee el **AppId** que utilizó al crear la entidad de servicio. Si necesita recuperar el identificador de aplicación, use el siguiente comando. Proporcione el nombre de la aplicación de Active Directory en el parámetro **search**.

        appId=$(azure ad app show --search exampleapp --json | jq '.[0].appId' | sed -e 's/^"//' -e 's/"$//')

3. Inicie sesión como la entidad de servicio:

        azure login -u "$appId" --service-principal --tenant "$tenantId"

Cuando se le solicite, proporcione la contraseña que especificó al crear la cuenta.

    info:    Executing command login
    Password: ********

Ahora está autenticado como la entidad de servicio para la aplicación de Active Directory que ha creado.

<a id="provide-credentials-through-automated-azure-cli-script" />
### Proporcionar credenciales a través de un script automatizado de la CLI de Azure

Esta sección muestra cómo iniciar sesión como la entidad de servicio sin tener que escribir manualmente las credenciales.

> [AZURE.NOTE] La inclusión de una contraseña en el script de la CLI de Azure no es segura porque la contraseña se expone como texto. Por ello, utilice en su lugar un servicio como el Almacén de claves para almacenar la contraseña y recuperarla al ejecutar el script.

En estos pasos se supone que ha configurado un Almacén de claves y un secreto que almacena la contraseña. Para implementar un Almacén de claves y un secreto a través de una plantilla, consulte [Key Vault template format]() (Formato de plantilla de almacén de claves). Para obtener información sobre el Almacén de claves, consulte [Introducción al Almacén de claves de Azure](./key-vault/key-vault-get-started.md).

1. Recupere su contraseña (en el ejemplo siguiente, está almacenada como secreto con el nombre **appPassword**) del Almacén de claves. Debe quitar las comillas dobles del principio y el final que se devuelven de la salida json antes de pasarlo como parámetro de contraseña.

        secret=$(azure keyvault secret show --vault-name examplevault --secret-name appPassword --json | jq '.value' | sed -e 's/^"//' -e 's/"$//')
    
2. Determine el valor de **TenantId** de la suscripción que contiene la entidad de servicio.

        tenantId=$(azure account show -s <subscriptionId> --json | jq '.[0].tenantId' | sed -e 's/^"//' -e 's/"$//')

3. Para el nombre de usuario, emplee el **AppId** que utilizó al crear la entidad de servicio. Si necesita recuperar el identificador de aplicación, use el siguiente comando. Proporcione el nombre de la aplicación de Active Directory en el parámetro **search**.

        appId=$(azure ad app show --search exampleapp --json | jq '.[0].appId' | sed -e 's/^"//' -e 's/"$//')

4. Inicie sesión como la entidad de servicio proporcionando el identificador de la aplicación, la contraseña del Almacén de claves y el identificador del inquilino.

        azure login -u "$appId" -p "$secret" --service-principal --tenant "$tenantId"
    
Ahora está autenticado como la entidad de servicio para la aplicación de Active Directory que ha creado.

<a id="provide-credentials-through-code-in-an-application-cli" />
### Proporcionar credenciales a través de código en una aplicación

Para autenticarse desde una aplicación .NET, incluya el código siguiente. Necesitará el identificador de aplicación para la aplicación de Active Directory, la contraseña y el identificador del inquilino para la suscripción. Después de recuperar el token de acceso, podrá trabajar con los recursos de la suscripción.

    public static string GetAccessToken()
    {
      var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantId or tenant name}");  
      var credential = new ClientCredential(clientId: "{application id}", clientSecret: "{application password}");
      var result = authenticationContext.AcquireToken(resource: "https://management.core.windows.net/", clientCredential:credential);

      if (result == null) {
        throw new InvalidOperationException("Failed to obtain the JWT token");
      }

      string token = result.AccessToken;

      return token;
    }

## Autenticar con certificado: CLI de Azure

En esta sección llevará a cabo los pasos para crear una entidad de servicio para una aplicación Azure Active Directory que usa un certificado para la autenticación. En este tema se supone que se le ha emitido un certificado y que ha instalado [OpenSSL](http://www.openssl.org/).

1. Cree un archivo **.pem** con:

        openssl pkcs12 -in C:\certificates\examplecert.pfx -out C:\certificates\examplecert.pem -nodes

2. Abra el archivo **.pem** y copie los datos del certificado. Busque la secuencia larga de caracteres entre **-----BEGIN CERTIFICATE-----** y **-----END CERTIFICATE-----**.

3. Para crear una nueva aplicación de Active Directory, ejecute el comando **azure ad app create** e indique los datos del certificado que copió en el paso anterior como el valor de la clave.

        azure ad app create -n "exampleapp" --home-page "https://www.contoso.org" -i "https://www.contoso.org/example" --key-value <certificate data>

    Se devuelve la aplicación de Active Directory. La propiedad AppId es necesaria para la creación de entidades de servicio, las asignaciones de roles y la adquisición de tokens de acceso.

        data:    AppId:          4fd39843-c338-417d-b549-a545f584a745
        data:    ObjectId:       4f8ee977-216a-45c1-9fa3-d023089b2962
        data:    DisplayName:    exampleapp
        ...
        info:    ad app create command OK

4. Cree a una entidad de servicio para la aplicación. Proporcione el identificador de la aplicación que se devolvió en el paso anterior.

        azure ad sp create 4fd39843-c338-417d-b549-a545f584a745
        
    Se devuelve la nueva entidad de servicio. Cuando se conceden permisos, es necesario el identificador de objeto.
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
5. Conceda los permisos de la entidad de servicio en su suscripción. En este ejemplo se concederá a la entidad de servicio el permiso de lectura para todos los recursos de la suscripción. Para más información sobre el control de acceso basado en roles, vea [Control de acceso basado en roles de Azure](./active-directory/role-based-access-control-configure.md).

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

Ha creado una aplicación de Active Directory y una entidad de servicio para esa aplicación. Ha asignado la entidad de servicio a un rol. Ahora, debe iniciar sesión como entidad de servicio para realizar operaciones como entidad de servicio. En este tema se muestran dos opciones:

- [Proporcionar un certificado a través de un script automatizado de la CLI de Azure](#provide-certificate-through-automated-azure-cli-script)
- [Proporcionar un certificado a través de código en una aplicación](#provide-certificate-through-code-in-an-application-cli)

<a id="provide-certificate-through-automated-azure-cli-script" />
### Proporcionar un certificado a través de un script automatizado de la CLI de Azure

1. Debe recuperar la huella digital del certificado y quitar los caracteres innecesarios.

        cert=$(openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g')
    
     De este modo, se devuelve un valor de huella digital similar al siguiente:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Determine el valor de **TenantId** de la suscripción que contiene la entidad de servicio.

        tenantId=$(azure account show -s <subscriptionId> --json | jq '.[0].tenantId' | sed -e 's/^"//' -e 's/"$//')

3. Para el nombre de usuario, emplee el **AppId** que utilizó al crear la entidad de servicio. Si necesita recuperar el identificador de aplicación, use el siguiente comando. Proporcione el nombre de la aplicación de Active Directory en el parámetro **search**.

        appId=$(azure ad app show --search exampleapp --json | jq '.[0].appId' | sed -e 's/^"//' -e 's/"$//')

4. Para autenticar con la CLI de Azure, proporcione la huella digital del certificado, el archivo del certificado, el identificador de la aplicación y el identificador del inquilino.

        azure login --service-principal --tenant "$tenantId" -u "$appId" --certificate-file C:\certificates\examplecert.pem --thumbprint "$cert"

Ahora está autenticado como la entidad de servicio para la aplicación de Active Directory que ha creado.

<a id="provide-certificate-through-code-in-an-application-cli" />
### Proporcionar un certificado a través de código en una aplicación

Para autenticarse desde una aplicación .NET, incluya el código siguiente. Tras la recuperación del cliente, puede tener acceso a recursos de la suscripción.

    var clientId = "<Application ID for your AAD app>"; 
    var subscriptionId = "<Your Azure SubscriptionId>"; 
    var tenant = "<Tenant id or tenant name>"; 
    var certName = "examplecert"; 

    var authContext = new AuthenticationContext(string.Format("https://login.windows.net/{0}", tenant)); 

    X509Certificate2 cert = null; 
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser); 
    
    try 
    { 
      store.Open(OpenFlags.ReadOnly); 
      var certCollection = store.Certificates; 
      var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false); 
      cert = certs[0]; 
    } 
    finally 
    { 
      store.Close(); 
    }        

    var certCred = new ClientAssertionCertificate(clientId, cert); 
    var token = authContext.AcquireToken("https://management.core.windows.net/", certCred); 
    var creds = new TokenCloudCredentials(subscriptionId, token.AccessToken); 
    var client = new ResourceManagementClient(creds); 
       
Para obtener más información acerca del uso de certificados y la CLI de Azure, consulte [Certificate-based auth with Azure Service Principals from Linux command line ](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx) (Autenticación basada en certificados con entidades de servicio de Azure desde la línea de comandos de Linux).

## Pasos siguientes
  
- Para obtener información sobre cómo usar el portal con entidades de seguridad de servicio, consulte [Creación de una entidad de servicio de Azure mediante el Portal de Azure](./resource-group-create-service-principal-portal.md).  
- Para obtener instrucciones sobre cómo implementar la seguridad con el Administrador de recursos de Azure, consulte [Consideraciones de seguridad para el Administrador de recursos de Azure](best-practices-resource-manager-security.md)


<!-- Images. -->
[1]: ./media/resource-group-authenticate-service-principal/arm-get-credential.png

<!---HONumber=AcomDC_0302_2016-->