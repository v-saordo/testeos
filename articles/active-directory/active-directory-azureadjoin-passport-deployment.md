<properties 
	pageTitle="Habilitar Microsoft Passport for Work en la organización | Microsoft Azure" 
	description="Instrucciones de implementación para habilitar Microsoft Passport en su organización." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="femila"/>

# Habilitar Microsoft Passport para el trabajo en la organización

Una vez conectados dispositivos Windows 10 unidos a un dominio con Azure AD, realice lo siguiente para habilitar Microsoft Passport para el trabajo de la organización.

## Implemente la versión 1509 de System Center Configuration Manager para Technical Preview
Para implementar certificados de usuario según las claves de Microsoft Passport, necesita lo siguiente:

- **Versión 1509 de System Center Configuration Manager para Technical Preview**. Para obtener más información, consulte [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update). y [Blog del equipo de System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
- **Infraestructura PKI**: para habilitar Microsoft Passport for Work mediante certificados de usuario debe tener una infraestructura de PKI establecida. Si no tiene una o no desea usar certificados de usuario, puede implementar controladores de dominio (DC) de la nueva versión de Windows Server:
 - **Implementar un controlador de dominio de la nueva versión de Windows Server**: en un Windows Server nuevo, compilación 10551 o posterior (los ISO están disponibles para descargarse en [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true)) siga los pasos para [instalar una réplica de controlador de dominio en un dominio existente](https://technet.microsoft.com/es-ES/library/jj574134.aspx) o para [instalar un nuevo bosque de Active Directory si desea crear un nuevo entorno](https://technet.microsoft.com/es-ES/library/jj574134.aspx).

## Configurar Microsoft Passport for Work a través de directivas de grupo en Active Directory

 Puede usar una directiva de grupo de Active Directory para configurar los dispositivos unidos al dominio de Windows 10 para proporcionar las credenciales de usuario de Microsoft Passport tras el inicio de sesión de este en Windows:

1. 	Abra el Administrador del servidor y vaya a **Herramientas** > **Administración de directivas de grupo**.
2.	En Administración de directivas de grupo, vaya al nodo de dominio que corresponde al dominio en el que desea habilitar la unión a Azure AD.
3.	Haga clic con el botón derecho en **Objetos de directiva de grupo** y seleccione **Nuevo**. Asigne un nombre a su objeto de directiva de grupo, por ejemplo, **Habilitar Microsoft Passport**. Haga clic en **Aceptar**.
4.	Haga clic con el botón derecho en el nuevo objeto de directiva de grupo y, a continuación, seleccione **Editar**.
5.	Vaya a **Configuración del equipo** > **Directivas** > **Plantillas administrativas** > **Componentes de Windows** > **Passport for Work**.
6.	Haga clic con el botón derecho en **Habilitar Passport for Work** y, a continuación, seleccione **Editar**.
7.	Seleccione el botón de opción **Habilitado** y, a continuación, haga clic en **Aplicar**. Haga clic en **Aceptar**.
8.	Ahora puede vincular el objeto de directiva de grupo a una ubicación de su elección. Para habilitar esta directiva para todos los dispositivos Windows 10 unidos a un dominio en su organización, vincule la directiva de grupo al dominio. Por ejemplo:
 - Una unidad organizativa (OU) específica en AD donde se encontrarán los equipos unidos a un dominio de Windows 10.
 - Un grupo de seguridad específico que contiene equipos unidos a un dominio de Windows 10 que se registrarán automáticamente con Azure AD.

## Configurar Microsoft Passport for Work mediante la implementación de PowerShell a través del administrador de configuración 

Ejecute el siguiente comando de PowerShell:

    powershell.exe -ExecutionPolicy Bypass -NoLogo -NoProfile -Command "& {New-ItemProperty "HKLM:\Software\Policies\Microsoft\PassportForWork" -Name "Enabled" -Value 1 -PropertyType "DWord" -Force}"

## Configurar el perfil de certificado para inscribir el certificado de inscripción de "Passport for Work" en Administrador de configuración
Para usar el inicio de sesión/Microsoft Hello basado en certificado de Passport for Work, configure el Perfil de certificado (Activos y cumplimiento -> Configuración de cumplimiento -> Acceso a recursos de empresa -> Perfiles de certificado) seleccionando una plantilla con EKU de inicio de sesión de tarjeta inteligente.

##Configurar una tarea programada, que se activa cuando el contenedor de Passport for Work está habilitado y solicita la evaluación de certificados
Se trata de una corrección a corto plazo que los administradores necesitan para crear una tarea programada que escuche una creación de contenedor de Passport for Work y solicite una evaluación de certificados. Esto reduce el retraso en la configuración del contenedor y el PIN y su capacidad para usarlo en el siguiente inicio de sesión.

**Para crear la tarea programada, use el siguiente comando o la interfaz de usuario** schtasks /create /xml %0..<EnrollCertificate.xml> /tn <Task Name>

El xml de ejemplo es como el facilitado a continuación:

    <?xml version="1.0" encoding="UTF-16"?>
    <Task version="1.2" xmlns="http://schemas.microsoft.com/windows/2004/02/mit/task">
       <RegistrationInfo>
         <Date>2015-09-04T17:48:45.9973761</Date>
          <Author><DomainName/UserName></Author>
        <URI>\Enroll Certificates</URI>
      </RegistrationInfo>
      <Triggers>
        <EventTrigger>
          <Enabled>true</Enabled>
          <Subscription>&lt;QueryList&gt;&lt;Query Id="0" Path="Microsoft-Windows-User Device Registration/Admin"&gt;&lt;Select Path="Microsoft-Windows-User Device Registration/Admin"&gt;*[System[Provider[@Name='Microsoft-Windows-User Device Registration'] and EventID=300]]&lt;/Select&gt;&lt;/Query&gt;&lt;/QueryList&gt;</Subscription>
        </EventTrigger>
      </Triggers>
      <Principals>
      </Principals>
      <Settings>
        <MultipleInstancesPolicy>IgnoreNew</MultipleInstancesPolicy>
        <DisallowStartIfOnBatteries>false</DisallowStartIfOnBatteries>
        <StopIfGoingOnBatteries>true</StopIfGoingOnBatteries>
        <AllowHardTerminate>true</AllowHardTerminate>
        <StartWhenAvailable>true</StartWhenAvailable>
        <RunOnlyIfNetworkAvailable>false</RunOnlyIfNetworkAvailable>
        <IdleSettings>
          <StopOnIdleEnd>true</StopOnIdleEnd>
          <RestartOnIdle>false</RestartOnIdle>
        </IdleSettings>
        <AllowStartOnDemand>true</AllowStartOnDemand>
        <Enabled>true</Enabled>
        <Hidden>false</Hidden>
        <RunOnlyIfIdle>false</RunOnlyIfIdle>
        <WakeToRun>false</WakeToRun>
        <ExecutionTimeLimit>PT1H</ExecutionTimeLimit>
        <Priority>7</Priority>
        <RestartOnFailure>
          <Interval>PT1M</Interval>
          <Count>3</Count>
        </RestartOnFailure>
      </Settings>
      <Actions Context="Author">
        <Exec>
          <Command>wmic</Command>
          <Arguments>/namespace:\\root\ccm\dcm path SMS_DesiredConfiguration call TriggerEvaluation 1,0,"ScopeId_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy_db89c51e-1d1b-4a17-8e42-a5459b742ccd",8</Arguments>
        </Exec>
      </Actions>
    </Task>

Donde ScopeId\_73F3BB5E-5EDC-4928-87BD-4E75EB4BBC34/ConfigurationPolicy\_db89c51e-1d1b-4a17-8e42-a5459b742ccd es el identificador del perfil de certificado creado en el paso "Configurar el perfil de certificado para inscribir Passport para el certificado de inscripción de trabajo en Administrador de configuración" y <8> es la versión.

## Información adicional
* [Windows 10 para empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->