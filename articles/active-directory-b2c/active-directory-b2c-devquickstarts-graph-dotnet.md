<properties
	pageTitle="Versión preliminar de Azure Active Directory B2C: uso de la API Graph | Microsoft Azure"
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

# Versión preliminar de Azure AD B2C: uso de la API Graph


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

Los inquilinos de Azure Active Directory (Azure AD) B2C tienden a ser muy grandes. Esto supone que muchas tareas comunes de administración de inquilinos necesitan realizarse mediante programación. Un ejemplo importante es la administración de usuarios. Quizá necesite migrar un almacén de usuario existente a un inquilino de B2C. Puede que desee hospedar un registro de usuarios en su propia página y crear cuentas de usuario en Azure AD en segundo plano. Estos tipos de tareas requieren la capacidad de crear, leer, actualizar y eliminar cuentas de usuario. Puede realizar estas tareas mediante la API de Azure AD Graph.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Para los inquilinos de B2C, existen dos modos de comunicación principales con la API Graph.

- Para las tareas interactivas, que se ejecutan una vez, deberá actuar como una cuenta de administrador en el inquilino de B2C al ejecutar las tareas. Este modo requiere que un administrador inicie sesión con credenciales antes de que este pueda realizar llamadas a la API Graph.
- Para las tareas automatizadas y continuas, tiene que usar algún tipo de cuenta de servicio a la que conceda los privilegios necesarios para realizar tareas de administración. En Azure AD, puede hacerlo mediante el registro de una aplicación y la autenticación en Azure AD. Esto se realiza con un **Id. de aplicación** que usa la [concesión de credenciales de cliente OAuth 2.0](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). En este caso, la aplicación actúa como tal, no como un usuario, para llamar a la API Graph.

En este artículo, abordaremos cómo realizar este caso de uso automatizado. Para demostrarlo, crearemos un `B2CGraphClient` de .NET 4.5 que realiza operaciones de creación, lectura, actualización y eliminación (CRUD) de usuario. El cliente tendrá una interfaz de línea de comandos (CLI) de Windows que permite invocar diversos métodos. Sin embargo, el código se escribe para que se comporte de forma no interactiva y automatizada.

## Obtención de un inquilino de Azure AD B2C

Antes de crear aplicaciones, usuarios o interactuar con Azure AD, necesitará un inquilino de Azure AD B2C y una cuenta de administrador global en ese inquilino. Si aún no tiene un inquilino, [comience con la introducción a Azure AD B2C](active-directory-b2c-get-started.md).

## Registro de una aplicación de servicio en el inquilino

Cuando tenga un inquilino de B2C, deberá crear la aplicación de servicio con los cmdlets de Azure AD PowerShell. En primer lugar, descargue e instale [Microsoft Online Services - Ayudante para el inicio de sesión](http://go.microsoft.com/fwlink/?LinkID=286152). A continuación, descargue e instale el [módulo de Azure Active Directory de 64 bits para Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.NOTE]
Para usar la API Graph con su inquilino de B2C, deberá registrar una aplicación dedicada con PowerShell. Para ello, siga las instrucciones de este artículo. No puede volver a usar las aplicaciones B2C ya existentes que registró en el Portal de Azure. Se trata de una limitación de la versión preliminar de Azure AD B2C que se eliminará previsiblemente en un futuro cercano. Actualizaremos este artículo cuando suceda.

Una vez instalado el módulo de PowerShell, abra PowerShell y conéctese al inquilino de B2C. Después de ejecutar `Get-Credential`, se le pedirá un nombre de usuario y una contraseña. Escriba el nombre de usuario y la contraseña de la cuenta del administrador de inquilinos de B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Antes de crear la aplicación, debe generar un nuevo **secreto de cliente**. La aplicación usará el secreto de cliente para autenticarse en Azure AD y adquirir tokens de acceso. Puede generar un secreto válido de PowerShell:

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

El comando final debe imprimir el nuevo secreto de cliente. Cópielo en un lugar seguro. Lo necesitará más adelante. Ahora puede crear la aplicación al proporcionar el nuevo secreto de cliente como credencial para la aplicación:

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

Si la creación de la aplicación se realiza correctamente, debe imprimir propiedades de la aplicación como las anteriores. Necesitará tanto `ObjectId` como `AppPrincipalId`, así que copie también esos valores.

Una vez creada una aplicación en el inquilino de B2C, debe asignarle los permisos necesarios para efectuar operaciones CRUD de usuario. Asigne a la aplicación tres roles: lectores de directorio (para lectura de usuarios), escritores de directorio (para creación y actualización de usuarios) y un administrador de cuenta de usuario (para eliminación de usuarios). Estos roles tienen identificadores conocidos, por lo que puede reemplazar el parámetro `-RoleMemberObjectId` por el parámetro `ObjectId` anterior y ejecutar los comandos siguientes. Para ver la lista de todos los roles de directorio, intente ejecutar `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

Ahora tiene una aplicación con permiso para crear, leer, actualizar y eliminar usuarios del inquilino de B2C.

## Descarga, configuración y compilación del código de ejemplo

En primer lugar, descargue el código de ejemplo y ejecútelo. A continuación, lo examinaremos más de cerca. Puede [descargar el código de ejemplo como archivo .zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). También puede clonarlo en un directorio de su elección:

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Abra la solución de Visual Studio `B2CGraphClient\B2CGraphClient.sln` en Visual Studio. En el proyecto `B2CGraphClient`, abra el archivo `App.config`. Reemplace las tres configuraciones de la aplicación por sus propios valores:

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

A continuación, haga clic con el botón derecho en la solución `B2CGraphClient` y vuelva a compilar el ejemplo. Si todo es correcto, ahora debería tener un archivo ejecutable `B2C.exe` que se encuentra en `B2CGraphClient\bin\Debug`.

## Compilación de operaciones CRUD de usuario mediante la API Graph

Para usar B2CGraphClient, abra un símbolo del sistema de Windows `cmd` y cambie al directorio `Debug`. A continuación, ejecute el comando `B2C Help`.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Se mostrará una breve descripción de cada comando. Cada vez que invoque uno de estos comandos, `B2CGraphClient` realiza una solicitud a la API de Azure AD Graph.

### Obtención de un token de acceso

Cualquier solicitud a la API Graph requiere un token de acceso para la autenticación. `B2CGraphClient` utiliza la Biblioteca de autenticación de Active Directory (ADAL) para ayudarle a adquirir tokens de acceso. ADAL facilita la adquisición de tokens al proporcionar una API sencilla y ocuparse de algunos detalles importantes, como el almacenamiento en caché de tokens de acceso. Sin embargo, no tiene que usar ADAL para obtener tokens. También puede obtener tokens elaborando solicitudes HTTP.

> [AZURE.NOTE]
	Este ejemplo de código usa ADAL v2, la versión de ADAL disponible con carácter general. No usa ADAL v4, que es una versión preliminar diseñada para funcionar con Azure AD B2C. Para la vista previa de Azure AD B2C, debe usar ADAL v2 para comunicarse con la API Graph. Con el tiempo, esperamos proporcionar acceso a la API Graph con ADAL v4, por lo que no tendrá que usar dos versiones de ADAL en la solución completa de Azure AD B2C.

Cuando se ejecuta `B2CGraphClient`, se crea una instancia de la clase `B2CGraphClient`. El constructor para esta clase configura scaffolding de autenticación de ADAL:

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
	// provided to Azure AD in order to receive an access_token by using the app's identity.
	this.credential = new ClientCredential(clientId, clientSecret);
}
```

Vamos a usar el comando `B2C Get-User` como ejemplo. Cuando se invoca `B2C Get-User` sin ninguna entrada adicional, la CLI llama al método `B2CGraphClient.GetAllUsers(...)`. Este método llama a `B2CGraphClient.SendGraphGetRequest(...)`, que envía una solicitud HTTP GET a la API Graph. Antes de que `B2CGraphClient.SendGraphGetRequest(...)` envíe la solicitud GET, primero obtiene un token de acceso mediante ADAL:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token by using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	...

```

Puede obtener un token de acceso para la API Graph mediante una llamada al método `AuthenticationContext.AcquireToken(...)` de ADAL. ADAL devuelve un `access_token` que representa la identidad de la aplicación.

### Lectura de usuarios

Si desea obtener una lista de usuarios o un usuario determinado de la API Graph, puede enviar una solicitud HTTP GET `GET` al punto de conexión `/users`. Una solicitud para todos los usuarios de un inquilino tiene este aspecto:

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver esta solicitud, ejecute:

 ```
 > B2C Get-User
 ```

Hay dos aspectos importantes que se deben tener en cuenta:

- El token de acceso adquirido mediante ADAL se agrega al encabezado `Authorization` según el esquema `Bearer`.
- Para los inquilinos de B2C, debe usar el parámetro de consulta `api-version=beta`.


> [AZURE.NOTE]
	La versión beta de la API de Azure AD Graph proporciona funcionalidad de vista previa. Consulte [esta entrada de blog del equipo de API Graph](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx) para obtener detalles sobre la versión beta.

Estos dos detalles se controlan en el método `B2CGraphClient.SendGraphGetRequest(...)`:

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...

	// For B2C user management, be sure to use the beta Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=beta";
	if (!string.IsNullOrEmpty(query))
	{
		url += "&" + query;
	}

	// Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
	HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
	request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
	HttpResponseMessage response = await http.SendAsync(request);

	...
```

### Creación de cuentas de usuario consumidor

Al crear cuentas de usuario en el inquilino de B2C, puede enviar una solicitud HTTP `POST` al punto de conexión `/users`:

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// All of these properties are required to create consumer users.

	"accountEnabled": true,
	"alternativeSignInNamesInfo": [             // controls which identifier the user uses to sign in to the account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "NameCoexistence",          // always set to 'NameCoexistence'
	"displayName": "Joe Consumer",				// a value that can be used for displaying to the end user
	"mailNickname": "joec",						// an email alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	},
	"passwordPolicies": "DisablePasswordExpiration"
}
```

Todas las propiedades de esta solicitud son necesarias para crear usuarios consumidor. Los comentarios `//` se han incluido con fines ilustrativos. No los incluya en una solicitud real.

Para ver la solicitud, ejecute uno de los siguientes comandos:

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

El comando `Create-User` toma un archivo .json como parámetro de entrada. Contiene una representación JSON de un objeto de usuario. Hay dos archivos .json de ejemplo en el código de ejemplo: `usertemplate-email.json` y `usertemplate-username.json`. Puede modificar estos archivos para satisfacer sus necesidades. Además de los campos obligatorios anteriores, hay varios campos opcionales que puede usar incluidos en estos archivos. Encontrará detalles sobre los campos opcionales en la [referencia de entidad de la API de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity).

Puede ver cómo se construye la solicitud POST en `B2CGraphClient.SendGraphPostRequest(...)`.

- Adjunta un token de acceso al encabezado `Authorization` de la solicitud.
- Establece `api-version=beta`.
- Incluye el objeto de usuario JSON en el cuerpo de la solicitud.

### Actualización de cuentas de usuario consumidor

Al actualizar objetos de usuario, el proceso es similar al que se utiliza para crear objetos de usuario. Sin embargo, este proceso usa el método HTTP `PATCH`:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request updates only the user's displayName
}
```

Intente actualizar un usuario mediante la actualización de los archivos JSON con nuevos datos. A continuación, puede usar `B2CGraphClient` para ejecutar uno de estos comandos:

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Consulte el método `B2CGraphClient.SendGraphPatchRequest(...)` para obtener más información sobre cómo enviar esta solicitud.

### Eliminación de usuarios

El proceso para eliminar un usuario es sencillo. Use el método HTTP `DELETE` y construya la dirección URL con el id. de objeto correcto:

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Para ver un ejemplo, ejecute este comando y vea la solicitud Delete que se imprime en la consola:

```
> B2C Delete-User <object-id-of-user>
```

Consulte el método `B2CGraphClient.SendGraphDeleteRequest(...)` para obtener más información sobre cómo enviar esta solicitud.

Puede realizar muchas otras acciones con la API de Azure AD Graph además de la administración de usuarios. La [referencia de la API de Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) proporciona detalles sobre cada acción, junto con solicitudes de ejemplo.

## Uso de atributos personalizados

La mayoría de las aplicaciones de consumidor necesita almacenar algún tipo de información de perfil de usuario personalizada. Una manera de hacerlo es definiendo un atributo personalizado en el inquilino de B2C. Puede tratar este atributo de la misma manera que trataría cualquier otra propiedad en un objeto de usuario. Puede actualizar el atributo, eliminarlo, consultarlo, enviarlo como notificación en tokens de inicio de sesión, etc.

Para definir un atributo personalizado en el inquilino de B2C, consulte la [referencia de atributos personalizados de versión preliminar de B2C](active-directory-b2c-reference-custom-attr.md).

Puede ver los atributos personalizados definidos en el inquilino de B2C mediante `B2CGraphClient`:

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

El resultado de estas funciones muestra los detalles de cada atributo personalizado, como:

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

Puede usar el nombre completo, por ejemplo `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, como propiedad en los objetos de usuario. Actualice el archivo .json con la nueva propiedad, un valor para la propiedad y ejecute:

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Con `B2CGraphClient`, tiene una aplicación de servicio que puede administrar los usuarios del inquilino de B2C mediante programación. `B2CGraphClient` utiliza su propia identidad de aplicación para autenticarse en la API de Azure AD Graph. También adquiere tokens mediante un secreto de cliente. Según vaya incorporando esta funcionalidad a su aplicación, debe recordar algunos puntos clave para las aplicaciones B2C:

- Tiene que conceder a la aplicación los permisos adecuados en el inquilino.
- Por ahora, debe usar ADAL v2 para obtener tokens de acceso. (Puede también enviar mensajes de protocolo directamente, sin usar una biblioteca.)
- Cuando llama a la API Graph, utilice [`api-version=beta`](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx).
- Al crear y actualizar los usuarios consumidores, hay algunas propiedades obligatorias, tal como se describió anteriormente.

> [AZURE.IMPORTANT] Debe dar cuenta de las características de replicación del servicio de directorio que subyace bajo Azure AD B2C cuando use la API de Azure AD Graph en la aplicación B2C. (Lea [este artículo](http://blogs.technet.com/b/ad/archive/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-geo-distributed-cloud-directory.aspx) para más información.) Después de que un consumidor se suscriba a la aplicación B2C mediante una directiva de **suscripción**, si intenta leer inmediatamente el objeto de usuario mediante la API de Azure AD Graph en su aplicación, puede que no esté disponible. Tendrá que esperar unos segundos para que se complete el proceso de replicación. Vamos a publicar instrucciones más concretas en la "garantía de coherencia de lectura y escritura" proporcionada por la API de Azure AD Graph y el servicio de directorio en disponibilidad general.

Si tiene alguna pregunta o desea presentar una solicitud para acciones que le gustaría realizar con la API Graph en su inquilino B2C, deje un comentario en este artículo o registre un problema en el repositorio de ejemplos de código de GitHub.

<!---HONumber=AcomDC_0302_2016-->