<properties 
	pageTitle="Llamada a una API personalizada en aplicaciones lógicas" 
	description="Uso de la API personalizada hospedada en Servicio de aplicaciones con aplicaciones lógicas" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"	
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="stepsic"/>
	
# Uso de la API personalizada hospedada en Servicio de aplicaciones con aplicaciones lógicas

Aunque Aplicaciones lógicas tiene un conjunto completo de más de 40 conectores para una gran variedad de servicios, puede que desee recurrir a su propia API personalizada que puede ejecutar su propio código. Una de las maneras más fáciles y escalables de hospedar sus propias API web personalizadas es usar Servicio de aplicaciones. En este artículo se explica cómo llamar a cualquier API web hospedada en una aplicación de API del Servicio de aplicaciones, en una aplicación web o en una aplicación móvil.

## Implementación de la aplicación web

En primer lugar, necesitará implementar la API como aplicación web en Servicio de aplicaciones. En estas instrucciones, se explica la implementación básica: [Creación de una aplicación web ASP.NET en Servicio de aplicaciones de Azure](../app-service-web/web-sites-dotnet-get-started.md). Aunque puede llamar a cualquier API desde una aplicación lógica, para obtener la mejor experiencia le recomendamos que agregue metadatos de Swagger para integrarse fácilmente con acciones de aplicaciones lógicas. Puede encontrar más información sobre [cómo agregar Swagger](../app-service-api/app-service-api-dotnet-get-started.md/#use-swagger-metadata-and-ui).

### Configuración de la API

Para que el diseñador de aplicaciones lógicas analice su Swagger, es importante que habilite CORS y establezca las propiedades de definición de la API de su aplicación web. Es muy fácil establecerlas dentro del Portal de Azure. Solo tiene que abrir la hoja de configuración de la aplicación web y, en la sección de la API, establezca el campo "Definición de la API" como la dirección URL de su archivo swagger.json (que normalmente es https://{name}.azurewebsites.net/swagger/docs/v1)) y agregue una directiva CORS a "*" para permitir las solicitudes del diseñador de aplicaciones lógicas.

## Llamada a la API

Cuando se encuentre dentro del portal de aplicaciones lógicas, si ha establecido CORS y las propiedades de Definición de la API, debería poder agregar fácilmente acciones de API personalizadas dentro de su flujo. En el diseñador, puede seleccionar si quiere examinar los sitios web de suscripción para enumerar los sitios web que tengan definida una dirección URL de Swagger. También puede usar la acción HTTP + Swagger para apuntar a Swagger y enumerar las acciones y las entradas disponibles. Por último, siempre puede crear una solicitud mediante la acción HTTP para llamar a cualquier API, incluso aquellas que no tengan ni expongan un documento de Swagger.

Si desea proteger su API, existen distintas formas de hacerlo:

1. No se requiere ningún cambio de código; se puede usar Azure Active Directory para proteger su API sin necesidad de cambiar el código ni volver a implementarlo.
1. Exija autenticación básica, autenticación de AAD o autenticación de certificado en el código de la API.

## Protección de las llamadas a la API sin cambios en el código 

En esta sección, creará dos aplicaciones de Azure Active Directory: una para la aplicación lógica y otra para la aplicación web. Podrá autenticar las llamadas a la aplicación web mediante la entidad de servicio (id. de cliente y secreto) asociada a la aplicación AAD para la aplicación lógica. Por último, incluirá el identificador de la aplicación en la definición de la aplicación lógica.

### Parte 1: Configurar una identidad de aplicación para la aplicación lógica

Esto es lo que la aplicación lógica usa para autenticarse en Active Directory. Solo *necesita* hacer esto una vez para su directorio. Por ejemplo, puede usar la misma identidad para todas las aplicaciones lógicas, aunque también puede crear una única identidad para cada aplicación lógica si lo desea. Puede hacerlo en la interfaz de usuario o usar PowerShell.

#### Creación de la identidad de aplicación mediante el Portal de Azure clásico

1. Vaya a [Active Directory en el Portal de Azure clásico](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) y seleccione el directorio que usa para la aplicación web.
2. Haga clic en la pestaña **Aplicaciones**.
3. Haga clic en **Agregar** en la barra de comandos de la parte inferior de la página.
4. Asigne un nombre a la identidad y haga clic en la flecha Siguiente.
5. Indique una cadena única con formato de dominio en ambos cuadros de texto y haga clic en la marca de verificación.
6. Haga clic en la pestaña **Configurar** para la aplicación.
7. Copie el **identificador de cliente**; se usará en la aplicación lógica.
8. En la sección **Claves**, haga clic **Seleccionar duración** y elija 1 año o 2 años.
9. Haga clic en el botón **Guardar** en la parte inferior de la pantalla (puede que deba esperar unos segundos).
10. No se olvide de copiar la clave en el cuadro. También la usará en la aplicación lógica.

#### Creación de la identidad de aplicación mediante PowerShell
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. No olvide copiar el **identificador de inquilino**, el **identificador de aplicación** y la contraseña usada.

### Parte 2: Proteger su aplicación web con una identidad de aplicación AAD

Si ya se implementó la aplicación web, basta con habilitarla en el portal. De lo contrario, puede incluir la habilitación de la autorización como parte de su implementación del Administrador de recursos de Azure.

#### Habilitación de la autorización en el Portal de Azure

1. Vaya a la aplicación web y haga clic en **Configuración** en la barra de comandos.
2. Haga clic en **Autorización/Autenticación**. 
3. **Actívelo**.

En este momento, se crea una aplicación automáticamente. Necesita el identificador de cliente de esta aplicación para la parte 3, por lo que tendrá que hacer lo siguiente:

1. Vaya a [Active Directory en el Portal de Azure clásico](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) y seleccione su directorio. 
2. Busque la aplicación en el cuadro de búsqueda.
3. Haga clic en ella en la lista.
4. Haga clic en la pestaña **Configurar**.
5. Debería ver el **identificador de cliente**.

#### Implementación de la aplicación web mediante una plantilla de ARM

En primer lugar, debe crear una aplicación para la aplicación web. Debería ser diferente de la aplicación que se usa para la aplicación lógica. Empiece siguiendo los pasos anteriores de la parte 1, pero ahora, para **HomePage** e **IdentifierUris**, use la https://**URL** real de la aplicación web.

>[AZURE.NOTE]Al crear la aplicación para la aplicación web, debe usar el [enfoque con el Portal de Azure clásico](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory), ya que el cmdlet de PowerShell no configura los permisos necesarios para iniciar la sesión de los usuarios en un sitio web.

Una vez que disponga del identificador de cliente y el de inquilino, incluya lo siguiente como subrecurso de la aplicación web en la plantilla de implementación:

```
"resources" : [
	{
		"apiVersion" : "2015-08-01",
		"name" : "web",
		"type" : "config",
		"dependsOn" : [
			"[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
		],
		"properties" : {
			"siteAuthEnabled": true,
			"siteAuthSettings": {
			  "clientId": "<<clientID>>",
			  "issuer": "https://sts.windows.net/<<tenantID>>/",
			}
		}
	}
]
```

Para ejecutar automáticamente una implementación conjunta de una aplicación web en blanco y una aplicación lógica que usen AAD, haga clic en el siguiente botón: [![Implementación en Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Para ver la plantilla completa, consulte la página sobre las [llamadas de aplicación lógica a una API personalizada hospedada en Servicio de aplicaciones y protegida mediante AAD](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).


### Parte 3: Rellenar la sección de autorización en la aplicación lógica

En la sección **Autorización** de la acción **HTTP**: `{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Elemento | Descripción |
|---------|-------------|
| type | Tipo de autenticación. En la autenticación ActiveDirectoryOAuth, el valor es ActiveDirectoryOAuth. |
| tenant | Identificador de inquilino que se usa para identificar al inquilino de AD. |
| audience | Obligatorio. El recurso al que se está conectando. |
| clientID | Identificador de cliente para la aplicación de Azure AD. |
| secret | Obligatorio. Secreto del cliente que solicita el token. | 

La plantilla anterior ya tiene esta configuración pero, si va a crear la aplicación lógica directamente, deberá incluir la sección de autorización completa.

## Protección de la API en el código

### Autenticación de certificado

Puede usar certificados de cliente para validar las solicitudes entrantes a la aplicación web. Consulte [Configuración de la autenticación mutua de TLS para una aplicación web](../app-service-web/app-service-web-configure-tls-mutual-auth.md) para ver cómo configurar su código.

En la sección *Autorización*, debe proporcionar: `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`.

| Elemento | Descripción |
|---------|-------------|
| type | Obligatorio. Tipo de autenticación. En los certificados de cliente SSL, el valor debe ser ClientCertificate. |
| pfx | Obligatorio. Contenido codificado en base 64 del archivo PFX. |
| contraseña | Obligatorio. Contraseña de acceso al archivo PFX. |

### Autenticación básica

Puede usar la autenticación básica (por ejemplo, nombre de usuario y contraseña) para validar las solicitudes entrantes. La autenticación básica es un patrón común que se puede usar en cualquier lenguaje en que compile la aplicación.

En la sección *Autorización*, debe proporcionar: `{"type": "basic","username": "test","password": "test"}`.

| Elemento | Descripción |
|---------|-------------|
| type | Obligatorio. Tipo de autenticación. En la autenticación básica, el valor debe ser Basic. |
| nombre de usuario | Obligatorio. Nombre de usuario para autenticar. |
| contraseña | Obligatorio. Contraseña para autenticar. |
 
### Control de la autenticación de AAD en el código

De forma predeterminada, la autenticación de Azure Active Directory que se habilita en el Portal no lleva a cabo una autorización específica. Por ejemplo, no bloquea la API para una aplicación o un usuario específicos, sino solo para un inquilino determinado.

Por ejemplo, si desea limitar la API solamente a la aplicación lógica, puede extraer en el código el encabezado que contiene el JWT, comprobar quién efectuó la llamada y rechazar las solicitudes que no coincidan.

Además, si desea implementarla totalmente en su propio código y no aprovechar la característica del Portal, lea este artículo: [Usar Active Directory para la autenticación en Servicio de aplicaciones de Azure](../app-service-web/web-sites-authentication-authorization.md).

Necesita seguir los pasos anteriores para crear una identidad de aplicación para la aplicación lógica y usarla para llamar a la API.

<!---HONumber=AcomDC_0224_2016-->