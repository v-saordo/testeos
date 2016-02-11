<properties
	pageTitle="Instalación silenciosa del conector del proxy de aplicación de Azure AD | Microsoft Azure"
	description="Explica cómo realizar una instalación silenciosa del conector de Proxy de aplicación de Azure AD para proporcionar acceso remoto seguro a las aplicaciones locales."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="kgremban"/>

# Cómo instalar de forma silenciosa el conector de Proxy de aplicación de Azure AD

> [AZURE.NOTE]Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

Quiere poder enviar un script de instalación a varios servidores de Windows o servidores de Windows Server que no tienen habilitada la interfaz de usuario. En este tema se explica cómo crear un script de Windows PowerShell que permite instalación desatendida para instalar y registrar el conector de Proxy de aplicación de Azure AD.

## Habilitación del acceso
El proxy de la aplicación funciona mediante la instalación de un pequeño servicio de Windows Server denominado conector dentro de la red. Para que el conector de Proxy de aplicación funcione debe estar registrado con el directorio de Azure AD mediante un administrador global y una contraseña. Normalmente esto se especifica durante la instalación del conector en un cuadro de diálogo emergente. En su lugar, puede usar Windows PowerShell para crear un objeto de credenciales para escribir la información de registro, o puede crear su propio token y usarlo con esta finalidad.

## Paso 1: Instalar el conector sin registro


Instale los MSI del conector sin registrar el conector de la siguiente manera:


1. Abra el símbolo del sistema.
2. Ejecute el siguiente comando en el que el modificador /q significa instalación desatendida (la instalación no le pedirá que acepte el contrato de licencia de usuario final).

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## Paso 2: Seleccionar el conector con Azure Active Directory
Para ello, puede usar cualquiera de los métodos siguientes:


- Registro del conector mediante un objeto de credenciales de Windows PowerShell
- Registrar el conector utilizando un token creado sin conexión

### Registro del conector mediante un objeto de credenciales de Windows PowerShell


1. Cree el objeto de credenciales de Windows PowerShell ejecutando lo siguiente, donde "<username>" y "<password>" deben reemplazarse por el nombre de usuario y la contraseña para el directorio:

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword

2. Vaya a **C:\\Program Files\\Microsoft AAD App Proxy Connector** y ejecute el script con el objeto de credenciales de PowerShell que creó, donde $cred es el nombre de dicho objeto:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred


### Registrar el conector utilizando un token creado sin conexión

1. Cree un token sin conexión mediante la clase AuthenticationContext con los valores del código, por ejemplo:


        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





2. Una vez que tenga el token cree SecureString con dicho token: <br> `$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. Ejecute el siguiente comando de Windows PowerShell, donde SecureToken es el nombre del token que acaba de crear y tenanID es el GUID de su inquilino: <br> `RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## Pasos siguientes
Hay mucho más que puede hacer con el proxy de la aplicación:


- [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
- [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Trabajar con las aplicaciones para notificaciones](active-directory-application-proxy-claims-aware-apps.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)


### Obtenga más información acerca del proxy de la aplicación
- [Eche un vistazo para ver nuestra ayuda en línea](active-directory-application-proxy-enable.md)
- [Consulte el blog del proxy de la aplicación](http://blogs.technet.com/b/applicationproxyblog/)
- [Vea nuestros vídeos de Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Recursos adicionales
* [Registro en Azure como una organización](sign-up-organization.md)
* [Identidad de Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_0114_2016-->