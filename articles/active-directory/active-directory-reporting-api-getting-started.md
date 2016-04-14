<properties
   pageTitle="Introducción a la API de informes de Azure AD"
   description="Introducción a la API de informes de Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/07/2015"
   ms.author="kenhoff"/>


# Introducción a la API de informes de Azure AD

*Esta documentación forma parte de la [guía de informes de Azure Active Directory](active-directory-reporting-guide.md).*

Azure Active Directory proporciona una variedad de informes de actividad, seguridad y auditoría. Estos datos se pueden consumir a través del Portal de Azure, pero también pueden ser muy útiles en muchas otras aplicaciones, como los sistemas SIEM y herramientas de auditoría y de inteligencia empresarial.

Las API de informes de Azure AD proporcionan acceso mediante programación a estos datos a través de un conjunto de API basadas en REST al que se puede llamar desde una variedad de lenguajes y herramientas de programación.

Este artículo le guiará a través del proceso de llamada a las API de informes de Azure AD mediante PowerShell. Puede modificar el script de PowerShell de ejemplo para tener acceso a datos desde cualquiera de los informes disponibles en formato JSON, XML o texto, según lo requiera su escenario.

Para usar este ejemplo, será necesario [Azure Active Directory](active-directory-whatis.md)

## Creación de una aplicación de Azure AD para tener acceso a la API

La API de informes utiliza [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) para autorizar el acceso a las API web. Para tener acceso a información desde su directorio, debe crear una aplicación en Active Directory y concederle los permisos adecuados para tener acceso a los datos de AAD.


### Creación de una aplicación
- Vaya al [Portal de administración de Azure](https://manage.windowsazure.com/).
- Vaya al directorio.
- Vaya a las aplicaciones.
- En la barra inferior, haga clic en "Agregar".
	- Haga clic en "Agregar una aplicación que mi organización está desarrollando".
	- **Nombre**: cualquier nombre es correcto. Se recomienda algo como "Aplicación de API de informes".
	- **Tipo**: seleccione "Aplicación web y/o API web".
	- Haga clic en la flecha para ir a la página siguiente.
	- **URL de inicio de sesión**: ```http://localhost```.
	- **URI de id. de aplicación**: ```http://localhost```.
	- Haga clic en la marca de verificación para terminar de agregar la aplicación.

### Conceder a la aplicación permiso para usar la API
- Vaya a la pestaña Aplicaciones.
- Vaya a la aplicación recién creada.
- Haga clic en la pestaña **Configurar**.
- En la sección "Permisos para otras aplicaciones":
	- En Microsoft Azure Active Directory > Permisos de la aplicación, seleccione **Leer datos de directorio**.
- Haga clic en **Guardar** en la parte inferior.


### Obtención del id. de directorio, el id. de cliente y el secreto de cliente

Los pasos siguientes le guiarán para obtener el id. de cliente y el secreto de cliente de la aplicación. También necesitará saber el nombre del inquilino, que puede ser su *.onmicrosoft.com o un nombre de dominio personalizado. Cópielos en un lugar independiente; los usará para modificar el script.

#### Id. de cliente de la aplicación
- Vaya a la pestaña Aplicaciones.
- Vaya a la aplicación recién creada.
- Vaya a la pestaña **Configurar**.
- El id. de cliente de la aplicación aparece en el campo **Id. de cliente**.

#### Secreto de cliente de la aplicación
- Vaya a la pestaña Aplicaciones.
- Vaya a la aplicación recién creada.
- Vaya a la pestaña Configurar.
- Genere una clave secreta nueva para la aplicación seleccionando una duración en la sección "Claves".
- La clave se mostrará al realizar la operación de guardar. Asegúrese de copiarla y pegarla en una ubicación segura, porque no hay manera de recuperarla más adelante.


## Modificación del script
Edite uno del los scripts siguientes para trabajar con su directorio, reemplace $ClientID, $ClientSecret y $tenantdomain con los valores correctos de "Delegación de acceso en Azure AD".

### Script de PowerShell

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID	  	= "your-application-client-id-here"				# Should be a ~35 character string insert your info here
    $ClientSecret  	= "your-application-client-secret-here"			# Should be a ~44 character string insert your info here
    $loginURL		= "https://login.windows.net"
    $tenantdomain	= "your-directory-name-here.onmicrosoft.com"			# For example, contoso.onmicrosoft.com

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body		= @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth		= Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    if ($oauth.access_token -ne $null) {
    	$headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&`$filter=eventTime gt $7daysago"

    	$myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
    	foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
    		Write-Output ($event | ConvertTo-Json)
    	}
        $myReport.Content | Out-File -FilePath auditEvents.json -Force
    } else {
    	Write-Host "ERROR: No Access Token"
    }

### Script de Bash

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&\$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"

### Python
	# Author: Michael McLaughlin (michmcla@microsoft.com)
	# Date: January 20, 2016
	# This requires the Python Requests module: http://docs.python-requests.org
	
	import requests
	import datetime
	import sys
	
	client_id = 'your-application-client-id-here'
	client_secret = 'your-application-client-secret-here'
	login_url = 'https://login.windows.net/'
	tenant_domain = 'your-directory-name-here.onmicrosoft.com' 
	
	# Get an OAuth access token
	bodyvals = {'client_id': client_id,
	            'client_secret': client_secret,
	            'grant_type': 'client_credentials'}
	
	request_url = login_url + tenant_domain + '/oauth2/token?api-version=1.0'
	token_response = requests.post(request_url, data=bodyvals)
	
	access_token = token_response.json().get('access_token')
	token_type = token_response.json().get('token_type')
	
	if access_token is None or token_type is None:
	    print "ERROR: Couldn't get access token"
	    sys.exit(1)
	
	# Use the access token to make the API request
	yesterday = datetime.date.strftime(datetime.date.today() - datetime.timedelta(days=1), '%Y-%m-%d')
	
	header_params = {'Authorization': token_type + ' ' + access_token}
	request_string = 'https://graph.windows.net/' + tenant_domain + '/reports/auditEvents?api-version=beta&filter=eventTime%20gt%20' + yesterday   
	response = requests.get(request_string, headers = header_params)
	
	if response.status_code is 200:
	    print response.content
	else:
	    print 'ERROR: API request failed'


## Ejecución del script
Una vez que termine de editar el script, ejecútelo y compruebe que el informe AuditEvents devuelve los datos esperados.

El script devuelve listas de todos los informes disponibles y devuelve los resultados del informe AccountProvisioningEvents en la ventana de PowerShell en formato JSON. También crea archivos con la misma salida en JSON, texto y XML. Puede comentar la experiencia de modificar el script para devolver datos de otros informes y puede comentar los formatos de salida que no necesita.

## Notas

- No hay ningún límite en el número de eventos devueltos por la API de informes de Azure AD (mediante la paginación de OData).
	- Para ver los límites de retención de los datos de informes, consulte [Directivas de retención de informes](active-directory-reporting-retention.md).


## Pasos siguientes
- ¿Tiene curiosidad sobre qué informes de actividad, auditoría y seguridad están disponibles? Consulte [Informes de actividad, auditoría y seguridad de Azure AD](active-directory-view-access-usage-reports.md).
- Consulte [Eventos del informe de auditoría de Azure AD](active-directory-reporting-audit-events.md) para obtener más detalles sobre el informe de auditoría.
- Consulte [Informes y eventos de Azure AD (vista previa)](https://msdn.microsoft.com/library/azure/mt126081.aspx) para obtener más detalles sobre el servicio REST de API Graph.

<!---HONumber=AcomDC_0211_2016-->