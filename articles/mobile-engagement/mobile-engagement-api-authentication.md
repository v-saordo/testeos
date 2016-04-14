<properties 
	pageTitle="Introducción a las API de autenticación de Azure Mobile Engagement"
	description="Se trata de una guía de migración para ayudar a los clientes que estaban usando la autenticación con las API de Capptain y ahora deben autenticarse con las nuevas API de Azure Mobile Engagement." 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="wesmc7777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="02/29/2016"
	ms.author="wesmc"/>

# Azure Mobile Engagement: uso de las API para la autenticación

## Información general

En este documento se describe con detalle cómo hacer que el mecanismo de autenticación funcione para las nuevas API.

Se supone que tiene una suscripción válida a Azure y que ha creado una aplicación de Mobile Engagement con uno de nuestros [tutoriales para desarrolladores](mobile-engagement-windows-store-dotnet-get-started.md).

## Autenticación

Es necesario usar un token de OAuth basado en Microsoft Azure Active Directory para la autenticación.

A fin de autenticar una solicitud de API, es necesario agregar un encabezado de autorización a cada solicitud. Debe tener la forma siguiente:

	Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Los tokens de Azure Active Directory caducan en 1 hora.

Existen diferentes formas de obtener un token. Puesto que a las API se las suele llamar desde un servicio en la nube, es probable que prefiera usar una clave de API. En la terminología de Azure, a una clave de API se la conoce como contraseña de entidad de servicio. El siguiente procedimiento describe un modo de configurarla manualmente.

> [AZURE.WARNING] La clave que aparece en Azure Active Directory NO es la clave de API de Mobile Engagement que se muestra en el Portal. El concepto de clave de API de Mobile Engagement está en desuso y se ha reemplazado por la autenticación AAD descrita en este documento.

#### Instalación única (manual)

Cuando siga este procedimiento, anote la siguiente información, ya que la necesitará más adelante:
	
1. Cree una aplicación de Azure Active Directory usando [esta guía](../resource-group-create-service-principal-portal.md).

	- El nombre de la aplicación que eligió, denominado `{AD_APP_NAME}` en este documento.
	- El identificador de cliente que se muestra en el menú de configuración, que se denomina `{CLIENT_ID}` en este documento.
	- La clave que aparece solo una vez después de guardar, que se denomina `{CLIENT_SECRET}` en este documento.
	- Haga clic en el botón **VER PUNTOS DE CONEXIÓN** de la barra inferior, copie la **DIRECCIÓN URL DE PUNTO DE CONEXIÓN DE TOKEN DE OAUTH 2.0**, denominado `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token` en este documento. <br/>                                    
2. Asigne un rol a la entidad de servicio, como lector o propietario, mediante la [CLI de Azure](../xplat-cli-install.md).

	Si se encuentra en Windows, modifique la variable de entorno `PATH` para que incluya `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI\bin` con el fin de poder usar los comandos de Azure.

	Ejecute los comandos siguientes para configurar la cuenta con la interfaz de línea de comandos (CLI) de Azure:

		azure config mode arm 
		azure login

	A continuación, utilice este comando para buscar el identificador de objeto de la aplicación.

		$ azure ad sp show --search {AD_APP_NAME} 
		info: Executing command ad sp show 
		+ Getting active directory service principals
		data: Object Id: 71785194-b7f5-4701-a9d6-fefb6cd32d18 
		data: Display Name: {AD_APP_NAME} 
		data: Service Principal Names:
		data: {AD_APP_URI}
		data: 8cdaf270-763c-4577-b2a2-ce559b47d353 
		data: 
		info: ad sp show command OK

	Observe el valor `Object Id` de la salida.

	A continuación, asigne el rol `Owner` a la entidad de servicio con este comando:
  
		$ azure role assignment create --objectId {OBJECT_ID} -o Owner 
		info: Executing command role assignment create
		+ Finding role with specified name
		-data: RoleAssignmentId :
		/subscriptions/{SUBSCRIPTION_ID}/providers/Microsoft.Authorization/roleAssignm
		ents/009392b1-2b7c-4de8-8f70-1fccb2e0a331 
		data: RoleDefinitionName : Reader
		data: RoleDefinitionId : acdd72a7-3385-48ef-bd42-f606fba81ae7 
		data: Scope : /subscriptions/{SUBSCRIPTION_ID} 
		data: Display Name : {AD_APP_NAME}
		data: SignInName :
		data: ObjectId : {OBJECT_ID} 
		data: ObjectType : ServicePrincipal

#### Instalación única (mediante script)

Esta es una manera alternativa de realizar los pasos mencionados anteriormente mediante un script de PowerShell.

1. Obtenga la versión más reciente de Azure PowerShell. Consulte este [vínculo](../powershell-install-configure.md) para ver las instrucciones de descarga. 

2. Abra Windows PowerShell en modo de administrador y asegúrese de que ha instalado los [cmdlets de Azure Resource Manager](https://msdn.microsoft.com/library/mt125356.aspx).

		Install-Module AzureRM
		Install-AzureRM

3. Ejecute el siguiente comando:

		Import-Module AzureRM.profile

4. Ejecute el siguiente comando para iniciar un cuadro de diálogo de inicio de sesión. Después de iniciar sesión, el comando mostrará la suscripción "activa"; es decir, la que se verá afectada por los comandos que ejecute.

		Add-AzureRmAccount

5. Ejecute el siguiente comando para enumerar todas las suscripciones. Copie el GUID de la que se va a utilizar.

		Get-AzureRmSubscription

6. Ejecute el siguiente comando proporcionando el GUID de la suscripción para configurar la suscripción que se usará. Esto resulta especialmente útil cuando tiene varias suscripciones.

		Select-AzureRmSubscription –SubscriptionId <subscriptionId>

7. Copie el texto del script [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) en el equipo local y ejecútelo.

	>[Azure.Note] Es posible que la directiva de seguridad predeterminada le impida ejecutar scripts de PowerShell. Si es así, configure temporalmente la directiva de ejecución para permitir la ejecución de scripts mediante el comando siguiente:

	>	Set-ExecutionPolicy RemoteSigned

	El script le pedirá un "nombre" para asignar a su valor de ServicePrincipal. Aquí puede proporcionar cualquier nombre que desee.

	Una vez completado el script, mostrará cuatro valores necesarios para autenticarse mediante programación con AD: **TenantId**, **SubscriptionId**, **ApplicationId** y **Secret**.

	Copie estos valores para su referencia. Para obtener ahora un token de acceso, usará TenantId como `{TENANT_ID}`, ApplicationId como `{CLIENT_ID}` y Secret como `{CLIENT_SECRET}`.

8. Compruebe en el Portal de administración de Azure que hay una nueva aplicación de AD bajo **Mostrar aplicaciones que posee mi empresa**.

#### Pasos para obtener un token válido

Para obtener un nuevo token, llame a esta API:

	https://login.microsoftonline.com/{TENANT_ID}/oauth2/token 

A continuación se muestra un ejemplo de solicitud:

	POST /{TENANT_ID}/oauth2/token HTTP/1.1
	Host: login.microsoftonline.com
	Content-Type: application/x-www-form-urlencoded Content-Length: 195
	grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
	urce=https%3A%2F%2Fmanagement.core.windows.net%2F

Este es un ejemplo de respuesta:

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=utf-8
	Content-Length: 1234
	
	{"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
	5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_T
	OKEN}}

Este ejemplo incluye la codificación de la dirección URL de los parámetros POST, el valor de `resource` es en realidad `https://management.core.windows.net/`. Tenga cuidado también al codificar la dirección URL `{CLIENT_SECRET}`, ya que puede contener caracteres especiales.

Ahora, en cada llamada de API, incluya el encabezado de la solicitud de autorización:

	Authorization: Bearer {ACCESS_TOKEN}

Si se le devuelve un código de estado 401, compruebe el cuerpo de la respuesta, ya que puede indicar que el token ha caducado. En ese caso, obtenga un nuevo token.

##Uso de las API

Ahora que tiene un token válido, está listo para realizar las llamadas de API.

1. En cada solicitud de API, debe pasar un token válido y no caducado que haya obtenido en la sección anterior.

2. Deberá especificar algunos parámetros en el URI de la solicitud que identifica la aplicación. El URI de la solicitud será similar al siguiente:

		https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/MobileEngagement/
		providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

	Para obtener los parámetros, haga clic en el nombre de la aplicación y luego en el Panel; así aparecerá una página similar a la siguiente con los tres parámetros.

	- **1** `{subscription-id}`
	- **2** `{app-collection}`
	- **3** `{app-resource-name}`

	![](./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png)

>[AZURE.NOTE] <br/>
>1. Omita la dirección raíz de la API, ya que era para las API anteriores.<br/> 2. Debe usar el nombre del recurso de la aplicación que es diferente del nombre de la propia aplicación. 

<!---HONumber=AcomDC_0302_2016-->