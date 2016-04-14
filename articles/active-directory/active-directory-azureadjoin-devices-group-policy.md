<properties
	pageTitle="Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10 | Microsoft Azure"
	description="Explica cómo los administradores pueden configurar directivas de grupo para permitir que los dispositivos se unan mediante dominio a la red empresarial."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags ms.service="active-directory" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article"

	ms.date="02/26/2016"

	ms.author="femila"/>

# Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10

Durante los últimos 15 años o más, las organizaciones emplean el método tradicional de unirse a un dominio para que los dispositivos conectados funcionen. Esto ha hecho posible que los usuarios inicien sesión en sus dispositivos con sus cuentas profesionales o educativas de Windows Server Active Directory (Active Directory) y que los administradores de TI puedan administrar esos dispositivos por completo. Las organizaciones suelen confían en los métodos de creación de imágenes para aprovisionar los dispositivos de los usuarios y usan generalmente System Center Configuration Manager (SCCM) o la directiva de grupo para administrarlos.

Unirse a un dominio en Windows 10 proporcionará las siguientes ventajas después de haber conectado los dispositivos a Azure Active Directory (Azure AD):

- Inicio de sesión único (SSO) a los recursos de Azure AD desde cualquier lugar
- Acceso a la Tienda Windows para empresas mediante cuentas profesionales o educativas (sin necesidad de una cuenta Microsoft)
- Movilidad de las configuraciones de usuario conforme a la empresa entre dispositivos que usan cuentas profesionales o educativas (sin necesidad de una cuenta Microsoft)
- Autenticación segura y práctico inicio de sesión para cuentas profesionales o educativas con Microsoft Passport y Windows Hello
- Posibilidad de limitar el acceso únicamente a los dispositivos que cumplan con la configuración de la directiva de grupo para los dispositivos de la organización

## Requisitos previos

Unirse a un dominio sigue resultando útil. Sin embargo, para disfrutar de los beneficios de Azure AD respecto del inicio de sesión único, la movilidad de la configuración con las cuentas profesionales o educativas y el acceso a la Tienda Windows con las cuentas profesionales o educativas, necesitará lo siguiente:

- Suscripción de Azure AD
- Azure AD Connect para ampliar el directorio local a Azure AD
- Establecimiento de la directiva para conectar los dispositivos unidos a un dominio a Azure AD
- Compilación de Windows 10 (compilación 10551 o posterior) en los dispositivos.

Para habilitar Microsoft Passport for Work y Windows Hello, necesitará además lo siguiente:

- Infraestructura de clave pública (PKI) para la emisión de certificados de usuario.
- Versión 1509 de System Center Configuration Manager para Technical Preview. Para obtener más información, consulte [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) (Versión preliminar técnica de Microsoft System Center Configuration Manager) y [System Center Configuration Manager Team Blog](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx) (Bloque del equipo de System Center Configuration Manager). Esta información es necesaria para implementar certificados de usuario según las claves de Microsoft Passport.

Como alternativa al requisito de implementación de PKI, puede hacer lo siguiente:

- Tener algunos controladores de dominio con los Servicios de dominio de Active Directory para Windows Server 2016.

Para habilitar el acceso condicional, puede crear una configuración de directiva de grupo que permita el acceso a dispositivos unidos a un dominio sin ninguna implementación adicional. Para administrar el control de acceso basado en el cumplimiento del dispositivo, necesitará lo siguiente:

- La versión 1509 de System Center Configuration Manager para Technical Preview para escenarios de Passport

## Instrucciones de implementación



### Paso 1: Implementar Azure Active Directory Connect

Azure AD Connect permitirá el aprovisionamiento de los equipos del entorno local como objetos de dispositivo en la nube. Para implementar Azure AD Connect, consulte "Instalación de Azure AD Connect" en el artículo [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect/#install-azure-ad-connect).

 - Si siguió la [instalación personalizada para Azure AD Connect](active-directory-aadconnect-get-started-custom.md) (no la instalación rápida), debe seguir el procedimiento **Creación de un punto de conexión de servicio en Active Directory local** que se describe a continuación.
 - Si tiene una configuración federada con Azure AD antes de instalar Azure AD Connect (por ejemplo, si ha implementado antes los Servicios de federación de Active Directory (AD FS)), siga el procedimiento **Configuración de reglas de notificaciones de AD FS** que se describe más adelante.

#### Creación de un punto de conexión de servicio en Active Directory local

Los dispositivos unidos a un dominio usarán el punto de conexión de servicio para detectar información del inquilino de Azure AD en el momento del registro automático en el servicio de registro de dispositivos de Azure.

En el servidor de Azure AD Connect, ejecute los siguientes comandos de PowerShell:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


Al ejecutar el cmdlet $aadAdminCred = Get-Credential, use el formato *user@example.com* para el nombre de usuario de la credencial que se especifica cuando aparece la ventana emergente de Get-Credential.

Al ejecutar el cmdlet Initialize-ADSyncDomainJoinedComputerSync ..., reemplace [*nombre de cuenta del conector*] por la cuenta de dominio que se usa como la cuenta de conector de Active Directory.

#### Configuración de reglas de notificaciones de AD FS
La configuración de reglas de notificaciones de AD FS habilita el registro instantáneo de un equipo en el servicio de registro de dispositivos de Azure al permitir que los equipos se autentiquen mediante Kerberos/NTLM a través de AD FS. Sin este paso, los equipos llegarán a Azure AD con retraso (en función de los tiempos de sincronización de Azure AD Connect).

>[AZURE.NOTE]
Si no dispone de AD FS como servidor de federación local, siga las instrucciones de su proveedor para crear las reglas de notificaciones.

En el servidor de AD FS (o en una sesión conectada al servidor de AD FS), ejecute los siguientes comandos de PowerShell:

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $rule1 = '@RuleName = "Issue object GUID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain joined computers"
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

    $rule3 = '@RuleName = "Pass through primary SID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(claim = c2);'

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

>[AZURE.NOTE]
Los equipos con Windows 10 se autenticarán con la autenticación integrada de Windows en un punto de conexión activo de WS-Trust hospedado por AD FS. Debe asegurarse de que este punto de conexión esté habilitado. Si usa el proxy de autenticación web, asegúrese también de que este punto de conexión se publique a través del proxy. Para ello, compruebe adfs/services/trust/13/windowstransport. Debe aparecer como habilitado en la consola de administración de AD FS en **Servicio** > **Puntos de conexión**.


### Paso 2: Configurar el registro automático de dispositivos mediante la directiva de grupo de Active Directory

Puede usar una directiva de grupo de Active Directory para configurar sus dispositivos de Windows 10 unidos a un dominio y registrarse automáticamente en Azure AD. Para ello, consulte las siguientes instrucciones paso a paso:

1. 	Abra el Administrador del servidor y vaya a **Herramientas** > **Administración de directivas de grupo**.
2.	En Administración de directivas de grupo, vaya al nodo de dominio que corresponde al dominio en el que desea habilitar la unión a Azure AD.
3.	Haga clic con el botón derecho en **Objetos de directiva de grupo** y seleccione **Nuevo**. Asigne un nombre a su objeto de directiva de grupo, por ejemplo, Unión automática a Azure AD. Haga clic en **Aceptar**.
4.	Haga clic con el botón derecho en el nuevo objeto de directiva de grupo y luego seleccione **Editar**.
5.	Vaya a **Configuración del equipo** > **Directivas** > **Plantillas administrativas** > **Componentes de Windows** > **Unión al área de trabajo**.
6.	Haga clic con el botón derecho en **Unir los equipos cliente al lugar de trabajo automáticamente** y luego seleccione **Editar**.
7.	Seleccione el botón de opción **Habilitado** y luego haga clic en **Aplicar**. Haga clic en **Aceptar**.
8.	Vincule el objeto de la directiva de grupo a una ubicación de su elección. Para habilitar esta directiva para todos los dispositivos de Windows 10 unidos a un dominio en su organización, vincule el objeto de la directiva de grupo al dominio. Por ejemplo:
 - Una unidad organizativa (OU) específica en Active Directory donde se encontrarán los equipos unidos a un dominio de Windows 10.
 - Un grupo de seguridad específico que contiene equipos unidos a un dominio de Windows 10 que se registrarán automáticamente en Azure AD.

>[AZURE.NOTE]
Esta plantilla de directiva de grupo ha cambiado su nombre en Windows 10. Si va a ejecutar la herramienta de directiva de grupo desde un equipo con Windows 10, la directiva aparecerá como: <br> **Registrar los equipos asociados a un dominio como dispositivos**<br> y la directiva se encontrará en la siguiente ubicación:<br> ***Configuración de equipos/Directivas/Plantillas administrativas/Componentes de Windows/Registro de dispositivos***.


## Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-->