<properties
	pageTitle="Varios dominios de Azure AD Connect"
	description="En este documento se describe cómo instalar y configurar varios dominios de nivel superior con O365 y Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="billmath"/>

#Compatibilidad con varios dominios

Muchos de ustedes han preguntado cómo configurar varios dominios y subdominios de Office 365 o Azure AD de nivel superior con federación. Aunque la mayor parte puede hacerse de forma bastante sencilla, debido a algunas acciones que se realizan en segundo plano, hay un par de sugerencias y trucos que debe conocer a fin de evitar los problemas siguientes:

- Mensajes de error al intentar configurar dominios adicionales por federación
- Los usuarios de subdominios no pueden iniciar sesión tras haberse configurado varios dominios de nivel superior por federación

## Varios dominios de nivel superior
Se le guiará por la configuración de una organización de ejemplo, contoso.com, que quiere tener un dominio adicional denominado fabrikam.com.

Supongamos que, en el sistema local, se ha configurado AD FS con fs.contoso100.com como nombre del servicio de federación.

Al suscribirse a Office 365 o Azure AD por primera vez, se configura contoso.com como primer dominio de inicio de sesión. Se puede hacer a través de Azure AD Connect o Azure AD PowerShell mediante el uso de MsolFederatedDomain.

A continuación, analicemos los valores predeterminados de dos de las nuevas propiedades de configuración del nuevo dominio de Azure AD (se pueden consultar con Get-MsolDomainFederationSettings):

| Nombre de propiedad | Valor | Descripción|
| ----- | ----- | -----|
|IssuerURI | http://fs.contoso100.com/adfs/services/trust| Aunque parece una dirección URL, esta propiedad es simplemente un nombre para el sistema de autenticación local y, por tanto, no es necesario que la ruta de acceso se resuelva en nada. De forma predeterminada, Azure AD lo establece en el valor del identificador del servicio de federación en la configuración local de AD FS.
|PassiveClientSignInUrl|https://fs.contoso100.com/adfs/ls/|This es la ubicación a la que se enviarán solicitudes de inicio de sesión pasivas y se resuelve en el sistema real de AD FS. En realidad hay varias propiedades "*Url", pero basta con echar un vistazo a un ejemplo para ver la diferencia entre esta propiedad y un URI como IssuerURI.

Ahora, imagine que se agrega el segundo dominio fabrikam.com. Una vez más, esto puede hacerse ejecutando el asistente de Azure AD Connect por segunda vez o a través de PowerShell.

Si se intenta agregar el segundo dominio como federado con Azure AD PowerShell, se recibirá un error.

La causa del mismo es una restricción en Azure AD por la cual IssuerURI no puede tener el mismo valor para más de un dominio. Para superar esta limitación, debe usar otro IssuerURI para el nuevo dominio. A efectos prácticos, esto es lo que hace el parámetro SupportMultipleDomain. Cuando este parámetro se usa con los cmdlet para configurar la federación (New-, Convert y Update-MsolFederatedDomain), hace que Azure AD configure el objeto IssuerURI en función del nombre de dominio, que debe ser único entre los inquilinos de Azure AD, como único a su vez. También se ha producido un cambio en las reglas de notificaciones, pero se hablará sobre ello en un momento.

Así pues, en PowerShell, si se agrega fabrikam.com con el parámetro SupportMultipleDomain,

    PS C:\>New-MsolFederatedDomain -DomainName fabrikam.com –SupportMultipleDomain

se obtendrá la siguiente configuración en Azure AD:

- DomainName: fabrikam.com
- IssuerURI: http://fabrikam.com/adfs/services/trust
- PassiveClientSignInUrl: https://fs.contoso100.com/adfs/ls/

Tenga en cuenta que, aunque IssuerURI se ha establecido en un valor basado en el dominio (siendo, por tanto, único), los valores de dirección URL del punto de conexión siguen configurados para apuntar al servicio de federación en fs.contoso100.com, del mismo modo que para el dominio contoso.com original. Por lo tanto, todos los dominios seguirán apuntando al mismo sistema de AD FS.

SupportMultipleDomain también se asegura de que el sistema de AD FS incluya el valor Emisor correcto en los tokens emitidos para Azure AD. Para ello, toma la parte de dominio del UPN de los usuarios y la establece como dominio en IssuerURI, es decir, https://{upn suffix} / adfs/services/trust. Por lo tanto, durante la autenticación a Azure AD u Office 365, el elemento Emisor del token del usuario se usa para buscar el dominio en Azure AD. Si no se encuentra una coincidencia, se producirá un error en la autenticación.

Por ejemplo, si el UPN de un usuario es johndoe@fabrikam.com, el elemento Emisor de los problemas de AD FS del token se establecerá en http://fabrikam.com/adfs/services/trust. Coincidirá con la configuración de Azure AD y la autenticación se realizará correctamente.

A continuación se muestra la regla de notificaciones personalizada que implementa esta lógica:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));

A continuación, en la configuración, se había registrado contoso.com primero sin el modificador supportMultipleDomains y con el valor de IssuerURI predeterminado. Al agregarse fabrikam.com, realmente es necesario asegurarse de que contoso.com se haya configurado también para usar el modificador SupportMultipleDomains, pues la actualización de la regla de notificaciones dejará de enviar el valor de IssuerURI predeterminado y se producirá un error en la autenticación al no coincidir este. No se preocupe, se le advertirá sobre ello antes de que pueda usar el modificador supportMultipleDomain en otro dominio.

Para remediarlo, también hace falta actualizar la configuración para el dominio contoso.com. El asistente de Azure AD Connect sabe muy bien cuándo debe aplicarse todo lo anterior, así como hacer lo correcto cuando agrega un segundo dominio. De primeras, si ya se encuentra en la configuración de SupportMultipleDomain, no se le sobrescribirá.

En PowerShell, debe proporcionar el modificador SupportMultipleDomain manualmente.

Consulte a continuación todos los pasos para pasar de un solo dominio a varios de forma detallada.

Una vez hecho esto, tendremos la configuración de dos dominios en Azure AD:

- DomainName: contoso.com
- IssuerURI: http://contoso.com/adfs/services/trust
- PassiveClientSignInUrl: https://fs.contoso100.com/adfs/ls/
- DomainName: fabrikam.com
- IssuerURI: http://fabrikam.com/adfs/services/trust
- PassiveClientSignInUrl: https://fs.contoso100.com/adfs/ls/

Ahora funcionará el inicio de sesión federado para los usuarios de los dominios contoso.com y fabrikam.com. Solo hay un problema: el inicio de sesión para los usuarios de subdominios.

##Subdominios
Supongamos que se agrega el subdominio sub.contoso.com a Azure AD. Debido al modo en que Azure AD administra los dominios, el subdominio heredará la configuración del dominio primario, en este caso contoso.com. Esto significa que IssuerURI para user@sub.contoso.com deberá ser http://contoso.com/adfs/services/trust. Pero la regla estándar implementada antes para

Azure AD generará un token con un emisor como http://sub.contoso.com/adfs/services/trust, que no coincidirá con el valor requerido del dominio y se producirá un error en la autenticación. Afortunadamente, existe también una solución alternativa para esto, pero no está tan bien integrada en nuestras herramientas. Tiene que actualizar la relación de confianza para usuario autenticado de AD FS para Microsoft Online de forma manual.

Debe configurar la regla de notificaciones personalizada para eliminar cualquier subdominio del sufijo UPN del usuario al construir el valor Emisor personalizado. En los pasos siguientes se le explicará exactamente cómo hacer esto.

Entonces, en resumen, puede tener varios dominios con nombres dispares, así como subdominios, todos ellos federados al mismo servidor de AD FS. A fin de establecer correctamente los valores Emisor para todos los usuarios, solo serán necesarios algunos pasos adicionales.

<!---HONumber=AcomDC_0128_2016-->