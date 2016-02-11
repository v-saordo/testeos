<properties
	pageTitle="Información general sobre el Registro de dispositivos de Azure Active Directory| Microsoft Azure"
	description="es la base de los escenarios de acceso condicional basado en dispositivos. Cuando se registra un dispositivo, el Registro de dispositivos de Azure Active Directory aprovisiona el dispositivo con una identidad que sirve para autenticar el dispositivo cuando el usuario inicia sesión."
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

# Información general sobre el Registro de dispositivos de Azure Active Directory

El Registro de dispositivos de Azure Active Directory es la base de los escenarios de acceso condicional basado en dispositivos. Cuando se registra un dispositivo, el Registro de dispositivos de Azure Active Directory aprovisiona el dispositivo con una identidad que sirve para autenticar el dispositivo cuando el usuario inicia sesión. El dispositivo autenticado y los atributos del dispositivo pueden utilizarse para aplicar directivas de acceso condicional para las aplicaciones que se hospedan en la nube y locales. Cuando se combina con una solución de administración de dispositivos móviles como Intune, los atributos del dispositivo en Azure Active Directory se actualizarán con información adicional sobre el dispositivo. Esto permite crear reglas de acceso condicional que exigen que el acceso desde dispositivos cumpla los estándares de seguridad y cumplimiento. El Registro de dispositivos de Azure Active Directory está disponible en Azure Active Directory. El servicio incluye compatibilidad con dispositivos iOS, Android y Windows. Los distintos escenarios que usan el Registro de dispositivos de Azure Active Directory pueden tener requisitos y compatibilidad con la plataforma más específicos. Siga leyendo para obtener más información sobre los escenarios concretos que están disponibles hoy en día.

## Escenarios habilitados para el Registro de dispositivos de Azure Active Directory

El Registro de dispositivos de AD de Azure puede considerarse la base de diversos escenarios. En general, el servicio incluye compatibilidad con dispositivos iOS, Android y Windows. Los distintos escenarios que usan el Registro de dispositivos de Azure AD pueden tener requisitos y compatibilidad con la plataforma más específicos. Estos escenarios son los siguientes: acceso condicional a aplicaciones hospedadas en local, puede usar dispositivos registrados con directivas de acceso en aplicaciones configuradas para usar AD FS con Windows Server 2012 R2. Para obtener más información sobre cómo configurar el acceso condicional en local, consulte Configuración del acceso condicional local mediante el Registro de dispositivos de Azure Active Directory.

Acceso condicional para aplicaciones de Office 365 con Microsoft Intune: los administradores de TI pueden aprovisionar directivas de dispositivos de acceso condicional para proteger los recursos corporativos, al tiempo que permiten que los trabajadores de información en dispositivos conformes tengan acceso a los servicios. Para obtener más información, vea Directivas de dispositivos de acceso condicional para servicios de Office 365

##Configuración del Registro de dispositivos de Azure Active Directory

Los siguientes ajustes están disponibles para el servicio Registro de dispositivos de Azure Active Directory: habilitación del Registro de dispositivos de Azure AD en el Portal de Azure.

Los dispositivos Windows detectan el servicio buscando registros DNS conocidos. Debe configurar el DNS de su compañía para que los dispositivos Windows 7 y Windows 8.1 puedan detectar y usar el servicio.

Puede ver y habilitar o deshabilitar dispositivos registrados mediante el Portal de administrador de Azure Active Directory.

## Habilitación del Registro de dispositivos de Azure Active Directory
En la siguiente sesión se describe cómo habilitar el servicio Registro de dispositivos de Azure Active Directory en su directorio.
Para habilitar el servicio Registro de dispositivos de Azure Active Directory
-------------------------------------------------------------
1. Inicie sesión en el Portal de Azure como administrador.
1. En el panel izquierdo, seleccione **Active Directory**.
1. En la pestaña **Directorio**, seleccione su directorio.
1. Seleccione la pestaña **Configurar**.
1. Desplácese hasta la sección llamada **Dispositivos**.
1. Seleccione **TODOS** en **LOS USUARIOS PUEDEN UNIR DISPOSITIVOS AL ÁREA DE TRABAJO**.
1. Seleccione el número máximo de dispositivos que quiere autorizar por usuario.

>[AZURE.NOTE]La inscripción en Microsoft Intune o Administración de dispositivos móviles para Office 365 requiere la unión al área de trabajo. Si configuró alguno de estos servicios, se seleccionará TODOS y se deshabilitará el botón NINGUNO.


De forma predeterminada, la autenticación en dos fases no está habilitada para el servicio. Aunque se recomienda usar la autenticación en dos fases al registrar un dispositivo.

* Antes de requerir la autenticación en dos fases para este servicio, debe configurar un proveedor de autenticación en dos fases en Azure Active Directory y configurar las cuentas de usuario para Multi-Factor Authentication. Vea Adición de Multi-Factor Authentication a Azure Active Directory.

* Si usa AD FS con Windows Server 2012 R2, debe configurar un módulo de autenticación en dos fases en AD FS. Vea Uso de Multi-Factor Authentication con Servicios de federación de Azure Active Directory.

## Configuración de la detección del Registro de dispositivos de Azure Active Directory
Los dispositivos Windows 7 y Windows 8.1 detectarán el servidor del Registro de dispositivos combinando el nombre de cuenta del usuario con un nombre de servidor del Registro de dispositivos conocido. Debe crear un registro CNAME de DNS que apunte al registro A asociado al servicio Registro de dispositivos de Azure Active Directory. El registro CNAME debe usar el prefijo enterpriseregistration conocido seguido del sufijo UPN que usan las cuentas de usuario en su organización. Si su organización usa varios sufijos UPN, deben crearse varios registros CNAME en DNS.

Por ejemplo, si usa dos sufijos UPN en la organización denominados @contoso.com y @region.contoso.com, cree los siguientes registros DNS.
 
| Entrada | Tipo | Dirección |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com | CNAME | enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com | CNAME | enterpriseregistration.windows.net |

## Visualización y administración de objetos de dispositivo en Azure Active Directory
1. En el Portal de administrador de Azure, puede ver, bloquear y desbloquear dispositivos. Un dispositivo bloqueado dejará de tener acceso a las aplicaciones configuradas para permitir solo dispositivos registrados.
1. Inicie sesión en el Portal de Microsoft Azure como administrador.
1. En el panel izquierdo, seleccione **Active Directory**.
1. Seleccione el directorio.
1. Seleccione la pestaña **Usuarios**. Luego seleccione un usuario para ver sus dispositivos.
1. Seleccione la pestaña **Dispositivos**.
1. Seleccione **Dispositivos registrados** en el menú desplegable.
1. Aquí puede ver, bloquear o desbloquear dispositivos registrados de usuarios. 

## Otros temas

Puede registrar los dispositivos Windows 7 y Windows 8.1 unidos a un dominio en el Registro de dispositivos de Azure AD. En el siguiente tema se ofrece más información sobre los requisitos previos y los pasos necesarios para configurar el Registro de dispositivos en dispositivos Windows 7 y Windows 8.1.

- [Registro automático de dispositivos en Azure Active Directory para dispositivos Windows unidos a un dominio](active-directory-conditional-access-automatic-device-registration.md) 
- [Configure el registro automático de dispositivos para dispositivos Windows 7 unidos a un dominio](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Configuración del registro automático de dispositivos para dispositivos Windows 8.1 unidos a un dominio](active-directory-conditional-access-automatic-device-registration-windows8_1.md)

<!---HONumber=AcomDC_1203_2015-->