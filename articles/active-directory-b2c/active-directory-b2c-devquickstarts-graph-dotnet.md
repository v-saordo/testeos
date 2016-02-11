<properties
	pageTitle="Versión preliminar
de Azure AD B2C: uso de la API Graph | Microsoft Azure"
	description="Cómo llamar a la API Graph para un inquilino de B2C mediante una identidad de aplicación para automatizar el proceso."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Vista previa de Azure AD B2C: Uso de la API Graph

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

Los inquilinos de Azure AD B2C tienden a ser muy grandes, lo que supone que muchas tareas comunes de administración de inquilinos necesitan realizarse mediante programación. Un ejemplo importante es la administración de usuarios: podría tener que migrar un almacén de usuarios existente a un inquilino de B2C o puede que desee hospedar el registro de usuarios en su propia página mediante la creación de cuentas de usuario en Azure AD en un segundo plano. Estos tipos de tareas requieren la capacidad de crear, leer, actualizar y eliminar cuentas de usuario, lo que puede realizar mediante la API de Azure AD Graph.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Para los inquilinos de B2C, existen principalmente dos modos de comunicación con la API Graph.

- Para las tareas interactivas, que se ejecutan una vez, probablemente deseará realizar tareas de administración con una cuenta de administrador en el inquilino de B2C. Este modo requiere un administrador para iniciar sesión con sus credenciales antes de realizar las llamadas a la API Graph.
- Para las tareas automatizadas y continuas, es posible que desee realizar tareas de administración mediante algún tipo de cuenta de servicio a la que conceda los privilegios necesarios. En Azure AD, puede hacerlo registrando y autenticando una aplicación en Azure AD con una "identidad de aplicación", mediante la [concesión de credenciales de cliente OAuth 2.0](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). En este caso, la aplicación llama a la API Graph que actúa como sí misma, en lugar de como un usuario concreto.  

En este artículo, le mostraremos cómo realizar este último caso de uso automatizado. Para demostrarlo, crearemos un "B2CGraphClient" de .NET 4.5 que realiza operaciones CRUD de usuario. Para fines de prueba, el cliente tendrá una interfaz de línea de comandos de Windows que permite invocar diversos métodos. Sin embargo, el código se escribe para que se comporte de forma no interactiva y automatizada. Comencemos.

## Obtención de un inquilino de Azure AD B2C

Antes de crear la aplicación, usuarios o interactuar con Azure AD, necesitará un inquilino de Azure AD B2C y una cuenta de administrador global en ese inquilino. Si no tiene ninguno todavía, siga la guía de [Introducción a Azure AD B2C](active-directory-b2c-get-started.md).

## Registro de una aplicación de servicio en el inquilino

Ahora que ya tiene un inquilino de B2C, deberá crear la aplicación de servicio con los cmdlets de Azure AD Powershell. En primer lugar, descargue e instale [Microsoft Online Services - Ayudante para el inicio de sesión](http://go.microsoft.com/fwlink/?LinkID=286152). A continuación, puede descargar e instalar el [módulo de Azure Active Directory de 64 bits para Windows Powershell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.NOTE]
Para usar la API Graph con su inquilino B2C, deberá registrar una aplicación dedicada con PowerShell mediante estas instrucciones. No puede volver a usar las aplicaciones B2C ya existentes que registró en el Portal de Azure. Se trata de una limitación de la vista previa de Azure AD B2C que se eliminará en un futuro cercano, momento en el cual actualizaremos este artículo.

Una vez instalado el módulo de Powershell, abra PowerShell y conéctese al inquilino de B2C. Después de ejecutar `Get-Credential`, se le pedirá un nombre de usuario y una contraseña: escriba la información de su cuenta de administrador del inquilino B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Antes de crear la aplicación, debe generar un nuevo "secreto de cliente". La aplicación usará el secreto de cliente para autenticarse en Azure AD y adquirir tokens de acceso. Puede generar un secreto válido de Powershell:

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

El comando final anterior debe imprimir el nuevo secreto de cliente. Cópielo en un lugar seguro, ya que pronto lo necesitará de nuevo. Ahora puede crear la aplicación al proporcionar el nuevo secreto de cliente como una credencial para la aplicación:

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

Si la creación de la aplicación se realiza correctamente, debe imprimir algunas propiedades de la aplicación como las anteriores. Necesitará tanto `ObjectId` como `AppPrincipalId`, así que anote también esos valores.

Una vez creada una aplicación en el inquilino de B2C, debe asignarle los permisos necesarios para efectuar operaciones CRUD de usuario. Tendrá que asignar a la aplicación tres roles distintos: lectores de directorio (para leer usuarios), escritores de directorio (para crear y actualizar usuarios) y administrador de cuentas de usuario (para eliminar usuarios). Estos roles tienen identificadores conocidos, por lo que puede ejecutar los comandos siguientes reemplazando el parámetro `-RoleMemberObjectId` por el `ObjectId` anterior. Para ver la lista de todos los roles de directorio, intente ejecutar `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```  

Ahora tiene una aplicación con permiso para crear, leer, actualizar y eliminar usuarios del inquilino de B2C; vamos a escribir un código que la usa.

## Descarga, configuración y compilación del código de ejemplo

En primer lugar, vamos a descargar el código de ejemplo y a ejecutarlo. A continuación, podemos echamos un vistazo a lo que está ocurriendo en segundo plano. También puede [descargar el código de ejemplo como .zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) o clonarlo en el directorio que elija:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Abra la solución de Visual Studio `B2CGraphClient\B2CGraphClient.sln` en Visual Studio. En el proyecto `B2CGraphClient`, abra el archivo `App.config`. Reemplace las tres configuraciones de aplicación por sus propios valores, del modo siguiente:

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Ahora, haga clic en la solución `B2CGraphClient` y vuelva a compilar el ejemplo. Si es correcto, ahora debería tener un archivo ejecutable `B2C.exe` ubicado en `B2CGraphClient\bin\Debug`.

## CRUD de usuario con la API Graph

Para usar B2CGraphClient, abra un símbolo del sistema de Windows y ejecute cmd y cd al directorio `Debug`. A continuación, ejecute el comando `B2C Help`.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Se mostrará una breve descripción de cada comando. Cuando se llama a cualquiera de estos comandos, B2CGraphClient realizará una solicitud a la API de Azure AD Graph.

### Obtención de un token de acceso

Cualquier solicitud a la API Graph requerirá un token de acceso para la autenticación. B2CGraphClient usa el código abierto Active Directory Authentication Library (ADAL) para adquirir tokens de acceso. No es necesario usar ADAL para obtener tokens: puede obtenerlos diseñando usted mismo las solicitudes HTTP. ADAL facilita la adquisición de tokens al proporcionar una API sencilla y ocuparse de algunos detalles importantes, como el almacenamiento en caché de tokens de acceso.

> [AZURE.NOTE]
	Este ejemplo de código usa deliberadamente ADAL v2, la versión de ADAL disponible con carácter general. NO usa ADAL v4, que es una versión de vista previa diseñada para funcionar con Azure AD B2C. Para la vista previa de Azure AD B2C, debe usar ADAL v2 para comunicarse con la API Graph. Con el tiempo, habilitaremos el acceso a la API Graph con ADAL v4, por lo que no es necesario que use dos versiones diferentes de ADAL en la solución completa de Azure AD B2C.

Cuando se ejecuta B2CGraphClient, se crea una instancia de la clase `B2CGraphClient`. El constructor para esta clase configura scaffolding de autenticación de ADAL:

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
	// The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
	this.clientId = clientId;
	this.clientSecret = clientSecret;
	this.tenant = tenant;

	// The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
	this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

	// The ClientCredential is where you pass in your client_id and client_secret, which are
	// provided to Azure AD in order to receive an access_token using the app's identity.
	this.credential = new ClientCredential(clientId, clientSecret);
}
```

Vamos a usar el comando `B2C Get-User` como ejemplo. Cuando se invoca `Get-User` sin ninguna entrada adicional, la CLI llama al método `B2CGraphClient.GetAllUsers(...)`. Este método llama a `B2CGraphClient.SendGraphGetRequest(...)`, que envía una solicitud HTTP GET a la API Graph. Antes de enviar la solicitud GET, primero obtiene un token de acceso mediante ADAL:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	...

```

Como puede ver, puede obtener un token de acceso para la API Graph mediante una llamada al método `AuthenticationContext.AcquireToken(...)` de ADAL. ADAL devolverá un access\_token que representa la identidad de la aplicación.

### Lectura de usuarios

Si desea obtener una lista de usuarios de la API Graph u obtener un usuario determinado, puede enviar una solicitud HTTP GET al extremo `/users`. Una solicitud para todos los usuarios de un inquilino tendría el siguiente aspecto:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```  

Para ver esta solicitud en acción, intente ejecutar:

 ```
 > B2C Get-User
 ```

Hay dos aspectos importantes que se deben tener en cuenta aquí:

- El token de acceso adquirido mediante ADAL se ha agregado al encabezado `Authorization` según el esquema `Bearer`.
- Para los inquilinos de B2C, debe usar el parámetro de consulta `api-version=beta`.


> [AZURE.NOTE]
	La versión beta de la API de Azure AD Graph proporciona funcionalidad de vista previa. Consulte [esta entrada de blog del equipo de API Graph](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx) para obtener detalles sobre la versión beta.

Estos dos detalles se controlan en el método `B2CGraphClient.SendGraphGetRequest(...)`:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...

	// For B2C user managment, be sure to use the beta Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=beta";
	if (!string.IsNullOrEmpty(query))
	{
		url += "&" + query;
	}

	// Append the access token for the Graph API to the Authorization header of the request, using the Bearer scheme.
	HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
	request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
	HttpResponseMessage response = await http.SendAsync(request);

	...
```

### Creación de cuentas de usuario de consumidor

Al crear cuentas de usuario en el inquilino de B2C, puede enviar una solicitud HTTP POST al punto de conexión `/users`:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// These properties are all required for creating consumer users.

	"accountEnabled": true,
	"alternativeSignInNamesInfo": [             // controls what identifier the user uses to sign into their account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "NameCoexistence",          // always set to 'NameCoexistence'
	"displayName": "Joe Consumer",				// a value that can be used for diplaying to the end-user
	"mailNickname": "joec",						// a mail alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	},
	"passwordPolicies": "DisablePasswordExpiration"
}
```

Cada una de las propiedades incluidas en la solicitud anterior es necesaria para crear usuarios de consumidor. Se han incluido comentarios `//` para ilustración; no los incluya en una solicitud real.

Para ver esta solicitud en acción, intente ejecutar uno de los siguientes comandos:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

El comando `Create-User` toma un archivo `.json` como parámetro de entrada que contiene una representación de JSON de un objeto de usuario. Hay dos archivos `.json` de ejemplo incluidos en el código de ejemplo, `usertemplate-email.json` y `usertemplate-username.json`, que se pueden modificar para adaptarse a sus necesidades. Además de los campos obligatorios anteriores, hay algunos campos opcionales incluidos en estos archivos que puede usar. Encontrará detalles sobre estos otros campos en la [referencia de entidad de la API de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity).

Puede ver cómo se construye esta solicitud POST en `B2CGraphClient.SendGraphPostRequest(...)`, que:

- Adjunta un token de acceso al encabezado `Authorization` de la solicitud.
- Establece `api-version=beta`.
- Incluye el objeto de usuario JSON en el cuerpo de la solicitud.

### Actualización de cuentas de usuario de consumidor

Actualizar objetos de usuario es muy similar a crear objetos de usuario pero en este caso se usa el verbo HTTP PATCH:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request only updates the user's displayName
}
```

Puede intentar actualizar un usuario actualizando los archivos JSON con nuevos datos y usando B2CGraphClient para ejecutar uno de los siguientes comandos:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Consulte el método `B2CGraphClient.SendGraphPatchRequest(...)` para obtener más información sobre cómo enviar esta solicitud.

### Eliminación de usuarios

La eliminación de un usuario se realiza directamente: use el verbo HTTP DELETE y construya la dirección URL con el Id. de objeto correcto:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver un ejemplo, pruebe el siguiente comando y vea la solicitud DELETE resultante que se imprime en la consola:

```
> B2C Delete-User <object-id-of-user>
```

Consulte el método `B2CGraphClient.SendGraphDeleteRequest(...)` para obtener más información sobre cómo enviar esta solicitud.

Hay muchas otras acciones que puede realizar con la API de Azure AD Graph además de la administración de usuarios. La [referencia de la API de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) proporciona detalles sobre cada acción, junto con solicitudes de ejemplo.


## Uso de atributos personalizados

Casi todas las aplicaciones de consumidor necesitan almacenar algún tipo de información de perfil de usuario personalizada. Una manera de hacerlo es definir un atributo personalizado en el inquilino de B2C, que le permitirá tratar ese atributo igual que cualquier otra propiedad en un objeto de usuario. Puede actualizar el atributo, eliminarlo, consultarlo, enviarlo como notificación en tokens de inicio de sesión, etc.

Para definir un atributo personalizado en el inquilino de B2C, consulte la [referencia de atributos personalizados de vista previa de B2C](active-directory-b2c-reference-custom-attr.md).

Puede ver los atributos personalizados definidos en el inquilino de B2C mediante B2CGraphClient:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

El resultado de estas funciones mostrará los detalles de cada atributo personalizado, como:

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Puede usar el nombre completo, por ejemplo `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, como propiedad en los objetos de usuario. Simplemente, actualice el archivo `.json` con la nueva propiedad, un valor para la propiedad y ejecute:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

¡Y esto es todo! Con B2CGraphClient, ahora tiene una aplicación de servicio que puede administrar los usuarios del inquilino de B2C mediante programación. Usa su propia identidad de aplicación para autenticarse en la API de Azure AD Graph y adquiere tokens mediante un client\_secret. Según vaya incorporando esta funcionalidad a su propia aplicación, debe recordar algunos puntos clave para las aplicaciones B2C:

- Tiene que conceder a la aplicación los permisos adecuados en el inquilino
- Por ahora, deberá usar ADAL v2 para obtener tokens de acceso (o puede enviar mensajes de protocolo directamente, sin una biblioteca)
- Al llamar a la API Graph, use [`api-version=beta`](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx).
- A la hora de crear y actualizar los usuarios de consumidor, hay algunas propiedades necesarias, descritas anteriormente.

> [AZURE.IMPORTANT]
Necesitará dar cuenta de las características de replicación del servicio de directorio que subyacen bajo Azure AD B2C (lea [este](http://blogs.technet.com/b/ad/archive/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-geo-distributed-cloud-directory.aspx) artículo para obtener más información) cuando use la API Graph de Azure AD en la aplicación B2C. Después de que un consumidor se suscriba a la aplicación B2C mediante una directiva de **suscripción**, si sale inmediatamente e intenta leer el objeto de usuario mediante la API Graph de Azure AD en su aplicación, puede que no esté disponible. Tendrá que esperar unos segundos para que se complete el proceso de replicación. Publicaremos instrucciones más concretas en la "garantía de coherencia de lectura y escritura" proporcionada por la API Graph de Azure AD y el servicio de directorio en disponibilidad general.

Si tiene alguna pregunta o solicitud para las acciones que desea realizar con la API Graph en el inquilino de B2C, ¡somos todo oídos! Deje un comentario sobre el artículo o registre un problema en el repositorio de GitHub de ejemplos de código.

<!---HONumber=AcomDC_0128_2016-->