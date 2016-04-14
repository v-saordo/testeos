<properties
	pageTitle="Configurar una API para conectarse al sistema back-end en un dominio de Azure Active Directory en PowerApps | Microsoft Azure"
	description="Configurar una API para conectarse a un sistema de back-end protegido por AAD en PowerApps"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="guayan"/>

# Configurar una API para conectarse a un recurso de back-end en un dominio de Azure Active Directory
A medida que más usuarios van creando dominios en Azure Active Directory (AAD), también se van agregando recursos de back-end a estos dominios de AAD. Puede crear y configurar API para conectarse a estos recursos de back-end.

#### Requisitos previos para empezar

- Suscríbase a [PowerApps Enterprise](powerapps-get-started-azure-portal.md).
- Cree un [entorno del Servicio de aplicaciones](powerapps-get-started-azure-portal.md).
- Instale [Azure PowerShell][11] 1.0 Preview o una versión superior.
- Registre una API en su [entorno del Servicio de aplicaciones](powerapps-register-api-hosted-in-app-service.md).

## Paso 1: Crear una aplicación de Active Directory y concederle permisos

Para tener acceso al sistema de back-end en un dominio de AAD, cree una aplicación de AAD y asígnele los permisos adecuados para el back-end existente (que también es una aplicación de AAD). Pasos:

1. En el [Portal de Azure clásico][13], vaya a Azure Active Directory, abra el inquilino (o directorio) y seleccione la pestaña **aplicaciones**: ![][14]
2. Seleccione el botón **Agregar** situado en la parte inferior. A continuación:  

	a) Elija **Agregar una aplicación que mi organización está desarrollando**. b) Escriba un nombre para la aplicación y seleccione **Aplicación Web y/o API web**. c) En **Dirección URL de inicio de sesión** e **Identificador URI del Id. de la aplicación**, escriba las direcciones URL únicas dentro de su AAD y direcciones URL que tengan sentido para su organización. Por ejemplo, puede introducir http://powerappssignon.contoso.com o http://powerappsappid.contoso.com: Es recomendable usar una dirección URL en el dominio de AAD de su organización. Las direcciones URL se usan como identificadores y no es necesario que existan. Nadie va a examinar las direcciones URL que especifique. Puede especificar HTTP o HTTPS.

3. En la página de aplicación AAD recién creada, vaya a la pestaña **Configurar**: ![][15]
4. En la sección **claves**, use la lista desplegable para seleccionar una duración. Tenga en cuenta que la clave se muestra después de seleccionar **Guardar**: ![][16]
5. En **Inicio de sesión único**, agregue ``https://<your App Service Environment name>.azure-apim.net:456/redirect`` como **dirección URL de respuesta**.
6. En **permisos para otras aplicaciones**:  

	a) Seleccione **Agregar una aplicación**. En la ventana emergente, elija la aplicación de AAD que protege el back-end existente: ![][17]

	b) Use la lista desplegable para agregar los permisos: ![][18]

7. Seleccione **Guardar** en la parte inferior.
8. Copia el **Id. de cliente** y la **clave** y almacénelos. La clave no aparece de nuevo después de cerrar el portal de Azure. 

Consulte [Integración de aplicaciones con Azure Active Directory](../active-directory-integrating-applications.md) para obtener más información acerca de aplicaciones de AAD.

## Paso 2: Configurar la API mediante Azure PowerShell

En este momento, no hay ninguna compatibilidad con el Portal de Azure para inicializar la configuración necesaria para la API. Para configurar la API en el Portal de Azure, use el siguiente script de Azure PowerShell:

> [AZURE.TIP]Para obtener información sobre cómo instalar, configurar y ejecutar Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell][11]. El siguiente script funciona con Azure PowerShell 1.0 preview o versiones superiores.

```powershell
# get the API resource
$api = Get-AzureRmResource -ResourceType Microsoft.Web/apiManagementAccounts/apis -ResourceName <App Service Environment name>/<API name> -ResourceGroupName <resource group name>

# configure the API resource for AAD authentication
$connectionParameters = @{
    token = @{
        type = "oauthSetting";
        oAuthSettings = @{
            identityProvider = "aad";
            clientId = "<your AAD app client id>";
            clientSecret = "<your AAD app key>";
            customParameters = @{
                TenantId = @{ # this property is optional
                    value = "<your AAD tenant ID>"
                };
                ResourceUri = @{ # this property is required
                    value = "<the app ID URI of the AAD app protecting your backend>"
                }
            }
        }
    }
}
Add-Member -InputObject $api.Properties -MemberType NoteProperty -Name ConnectionParameters -Value $connectionParameters -Force

# update the API resource
New-AzureRmResource -Location $api.Location -ResourceId $api.ResourceId -Properties $api.Properties
```

**Tenga en cuenta** que el nombre del parámetro de conexión de **token** es importante. Puede elegir su propio nombre, siempre y cuando aparezca en CamelCase. Usará este nombre más adelante en el código de back-end o la directiva de la API.

A continuación, vaya al [Portal de Azure][19] y a la hoja de configuración **General** de la API. Deberá ver las opciones de configuración adicionales: ![][21]


## Prueba

Abra una aplicación en PowerApps. En **Conexiones disponibles**, aparecerá la nueva API. Al seleccionar **Conectar**, muestra una ventana de inicio de sesión de AAD. Escriba los detalles de la cuenta de AAD de su organización y se creará la conexión.

Ahora cuando se efectúe una llamada de tiempo de ejecución desde su aplicación a la API mediante esta conexión, el back-end recibe el token de AAD del usuario en el encabezado HTTP **x-ms-apim-tokens** en el siguiente formato de [codificación Base64][20]\:

```json
{
  "token": {
    "AccessToken": ""
    // ...
  }
}
```

**Tenga en cuenta** que el **token** del nombre de la propiedad coincide con el nombre del parámetro de conexión usado al establecer la configuración.

El código de back-end puede, a continuación, obtener el token de AAD de la propiedad **AccessToken** y usarlo si fuera necesario. El entorno del Servicio de aplicaciones actualiza el token automáticamente.

## Configurar la directiva de la API

Si lo desea, también puede usar la directiva de la API para establecer el token de ADD en el encabezado de **autorización** HTTP estándar. De este modo, si su código de back-end necesita usar el token de AAD, puede obtenerlo de una manera estándar en lugar de buscar en un encabezado HTTP personalizado y realizar la descodificación de Base64. Para ello, vaya al Portal de Azure, vaya a la hoja **Directiva** de su API y establezca la siguiente directiva:

```xml
<policies>
	<inbound>
		<base/>
		<choose>
			<when condition="@(context.Variables.ContainsKey(";tokens";) &amp;&amp; ((JObject)context.Variables[";tokens";])[";token";] != null &amp;&amp; !String.IsNullOrEmpty((string)((JObject)context.Variables[";tokens";])[";token";][";AccessToken";]))">
				<set-header exists-action="override" name="Authorization">
					<value>@("Bearer " + (string)((JObject)context.Variables["tokens"])[";token";]["AccessToken"])</value>
				</set-header>
			</when>
		</choose>
	</inbound>
	<backend>
		<base/>
	</backend>
	<outbound>
		<base/>
	</outbound>
</policies>
```

Examinando esta directiva, básicamente le permite hacer referencia a los valores del encabezado **x-ms-apim-tokens** como un JObject descodificado usando una variable de **tokens**. A continuación, puede usar la directiva **set-header** para obtener el token de AAD real y establecerlo en el encabezado **Autorización**. Esta es la misma directiva usada por [Administración de API de Azure](https://azure.microsoft.com/services/api-management/). Para obtener más información, consulte [Directivas de Administración de API de Azure](../api-management-howto-policies.md).

**Tenga en cuenta** que el **token** del nombre de la propiedad coincide con el nombre del parámetro de conexión usado al establecer la configuración.

## Resumen y pasos siguientes

En este tema, se ha revisado cómo configurar una API para conectarse (y autenticar) a un recurso de back-end en un dominio de Azure Active Directory. Estos son algunos temas y recursos relacionados que le permitirán obtener más información acerca de PowerApps.

- [Desarrollar una API para PowerApps](powerapps-develop-api.md)


<!--References-->
[11]: ../powershell-install-configure.md
[13]: https://manage.windowsazure.com
[14]: ./media/powerapps-configure-apis-aad/aad-applications-tab.png
[15]: ./media/powerapps-configure-apis-aad/aad-application-configure-tab.png
[16]: ./media/powerapps-configure-apis-aad/aad-application-configure-keys.png
[17]: ./media/powerapps-configure-apis-aad/aad-application-add-other-application.png
[18]: ./media/powerapps-configure-apis-aad/aad-application-add-permissions.png
[19]: https://portal.azure.com
[20]: https://tools.ietf.org/html/rfc4648
[21]: ./media/powerapps-configure-apis-aad/api-settings-aad.png

<!---HONumber=AcomDC_1203_2015-->