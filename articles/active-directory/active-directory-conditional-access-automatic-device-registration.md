<properties
	pageTitle="Registro automático de dispositivos en Azure Active Directory para dispositivos Windows unidos a un dominio | Microsoft Azure"
	description="El administrador de TI puede elegir tener sus dispositivos Windows unidos a un dominio para registrarse de forma automática y silenciosa en Azure Active Directory (Azure AD)."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/24/2015"
	ms.author="femila"/>

# Registro automático de dispositivos en Azure Active Directory para dispositivos Windows unidos a un dominio

Como administrador de TI, puede elegir registrar sus dispositivos Windows unidos a un dominio de forma automática y silenciosa en Azure Active Directory (Azure AD). Esto puede ser útil si ha configurado directivas de acceso condicional basadas en dispositivos para aplicaciones de Office 365 o aplicaciones administradas de forma local por AD FS. Puede obtener más información acerca de los escenarios de registro de dispositivos leyendo la [información general sobre el registro de dispositivos de Azure Active Directory](active-directory-conditional-access-device-registration-overview.md).

El registro automático de dispositivos en Azure Active Directory está disponible para los equipos con Windows 7 y Windows 8.1 que se han unido a un dominio de Active Directory. Suelen ser equipos corporativos que se han proporcionado a los trabajadores de la información.

Para empezar a registrar sus dispositivos Windows unidos a un dominio en Azure AD, siga los requisitos previos siguientes. Una vez que complete los requisitos previos, configure el registro automático de dispositivos para sus dispositivos Windows unidos a un dominio.

## Requisitos previos para el registro automático de dispositivos de dispositivos Windows unidos a un dominio en Azure Active Directory

Implemente AD FS y conéctese a Azure Active Directory con Azure Active Directory Connect
----------------------------------------------------------------------------------------------
1. Use Azure Active Directory Connect para implementar Servicios de federación de Active Directory (AD FS) con Windows Server 2012 R2 y configurar una relación de federación con Azure Active Directory.
2. Configure una regla de notificaciones de confianza para usuario de confianza de Azure Active Directory.
3. Abra la consola de administración de AD FS y vaya a **AD FS**>**Relaciones de confianza > Relaciones de confianza para usuario autenticado**. Haga clic con el botón derecho en el objeto de confianza para usuario de confianza Plataforma de identidad de Microsoft Office 365 y seleccione **Editar reglas de notificaciones…**
4. En la pestaña **Reglas de transformación de emisión**, seleccione **Agregar regla**.
5. Seleccione **Enviar notificaciones con una regla personalizada** en el cuadro desplegable de plantillas **Regla de notificaciones**. Seleccione **Siguiente**.
6. Escriba *Regla de notificaciones de método de autenticación* en el cuadro de texto **Nombre de regla de notificaciones:**.
7. Escriba la siguiente regla de notificaciones en el cuadro de texto **Regla de notificaciones**:

        c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
        => issue(claim = c);
    
8. Haga clic en **Aceptar** dos veces para completar el cuadro de diálogo.

Configure una referencia de clase de autenticación de relación de confianza para usuario de confianza de Azure Active Directory adicional.
-----------------------------------------------------------------------------------------------------
En el servidor de federación, abra una ventana de comandos de Windows PowerShell y escriba:


  `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`
   
Donde <RPObjectName> es el nombre del objeto para usuario de confianza para su objeto de confianza para usuario de confianza de Azure Active Directory. Este objeto normalmente se denomina Plataforma de identidad de Microsoft Office 365.

Directiva de autenticación global de AD FS
-----------------------------------------------------------------------------
Configure la directiva de autenticación principal global de AD FS para permitir la autenticación integrada de Windows para la intranet (se trata del valor predeterminado).


Configuración de Internet Explorer
------------------------------------------------------------------------------
Configure las siguientes opciones en Internet Explorer en sus dispositivos Windows para la zona de seguridad de intranet local:

- No solicite la selección de certificado de cliente si solo existe un certificado: **Habilitar**.
- Permita el scripting: **Habilitar**.
- Inicio de sesión automático solo en la zona de intranet: **Activado**

Esta es la configuración predeterminada para la zona de seguridad de intranet local de Internet Explorer. Puede ver o administrar esta configuración en Internet Explorer yendo a **Opciones de Internet** > **Seguridad** > Intranet local > Nivel personalizado. También puede configurar estas opciones mediante la Directiva de grupo de Active Directory.

Conectividad de red
-------------------------------------------------------------
Los dispositivos Windows unidos a un dominio deben tener conectividad con AD FS y un controlador de dominio de Active Directory para registrarse automáticamente en Azure AD. Normalmente, esto significa que el equipo debe conectarse a la red corporativa. Esto puede incluir una conexión con cable, una conexión Wi-Fi, DirectAccess o VPN.

## Configuración de la detección del Registro de dispositivos de Azure Active Directory
Los dispositivos Windows 7 y Windows 8.1 detectarán el servidor del Registro de dispositivos combinando el nombre de cuenta del usuario con un nombre de servidor del Registro de dispositivos conocido. Debe crear un registro CNAME de DNS que apunte al registro A asociado al servicio Registro de dispositivos de Azure Active Directory. El registro CNAME debe usar el prefijo **enterpriseregistration** conocido seguido del sufijo UPN que usan las cuentas de usuario en su organización. Si su organización usa varios sufijos UPN, deben crearse varios registros CNAME en DNS.

Por ejemplo, si usa dos sufijos UPN en la organización denominados @contoso.com y @region.contoso.com, cree los siguientes registros DNS.

| Entrada | Tipo | Dirección |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

##Configure el registro automático de dispositivos para dispositivos Windows 7 y Windows 8.1 unidos a un dominio

Configure el registro automático de dispositivos para sus dispositivos Windows 7 y Windows 8.1 unidos a un dominio con los vínculos siguientes. Asegúrese de que ha completado los requisitos previos anteriores antes de continuar.

* [Configure el registro automático de dispositivos para dispositivos Windows 8.1 unidos a un dominio](active-directory-conditional-access-automatic-device-registration-windows8_1.md)

* [Configure el registro automático de dispositivos para dispositivos Windows 7 unidos a un dominio](active-directory-conditional-access-automatic-device-registration-windows7.md)

Notas adicionales
--------------------------------------------------------------------

El registro de dispositivos en Azure AD proporciona el conjunto más amplio de capacidades de dispositivo. Con el registro de dispositivos de Azure AD, puede registrar tanto dispositivos móviles (BYOD) personales como dispositivos unidos a un dominio propiedad de la empresa. Los dispositivos pueden utilizarse con ambos servicios hospedados, como Office365 y servicios administrados de forma local con AD FS.

Las empresas que usan tanto dispositivos móviles como tradicionales, o que utilizan Office 365, Azure AD u otros servicios de Microsoft deben registrar los dispositivos en Azure AD con el servicio Registro de dispositivos de Azure AD. Si su empresa no usa servicios de Microsoft como Office 365, Azure AD o Microsoft Intune y, en su lugar, alberga solo aplicaciones locales, puede elegir registrar dispositivos en Active Directory con AD FS.

Puede obtener más información acerca de cómo implementar el registro de dispositivos con AD FS [aquí](https://technet.microsoft.com/library/dn486831.aspx).

## Otros temas

- [Información general sobre el Registro de dispositivos de Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)
- [Configure el registro automático de dispositivos para dispositivos Windows 7 unidos a un dominio](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Configuración del registro automático de dispositivos para dispositivos Windows 8.1 unidos a un dominio](active-directory-conditional-access-automatic-device-registration-windows8_1.md)

<!---HONumber=AcomDC_1203_2015-->