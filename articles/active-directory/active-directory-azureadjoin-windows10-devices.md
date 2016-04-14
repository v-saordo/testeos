<properties
	pageTitle="Uso de dispositivos con Windows 10 en el área de trabajo | Microsoft Azure"
	description="Ofrece una instantánea de las funcionalidades para los usuarios y los responsables de TI, comparando las distintas formas en que un dispositivo se puede aprovisionar y usar en una empresa con Windows 10."
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
	ms.date="02/26/2016"
	ms.author="femila"/>

# Uso de dispositivos de Windows 10 en el área de trabajo

Windows 10 ofrece tres modelos a las organizaciones para que los usuarios puedan acceder a los recursos profesionales de una manera segura y cómoda.

- **Azure Active Directory Join** (Azure AD Join): para los trabajadores que acceden a los recursos (como Office 365) principalmente en la nube. Azure AD Join es una experiencia de aprovisionamiento de trabajo de autoservicio nueva en Windows 10.
- **Unión a un dominio**: para las organizaciones que tienen inversiones en aplicaciones y recursos locales. Ofrece una mejor experiencia en Windows 10 cuando se conecta a Azure AD.
- **Una nueva experiencia BYOD simplificada**: permite a los usuarios agregar una cuenta profesional o educativa a Windows para acceder fácilmente a los recursos de los dispositivos personales.

En la tabla siguiente se presenta una instantánea de las funcionalidades para los usuarios y los administradores de TI, en la que se comparan las distintas formas en que un dispositivo se puede aprovisionar y usar en una empresa con Windows 10:

| | Unión a un dominio | Azure AD Join | Dispositivo personal |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| Inicio de sesión de dispositivos de Windows para cuentas profesionales o educativas. | Sí | Sí | No |
| Inicio de sesión único de usuario (SSO) para aplicaciones de Office 365 y Azure AD. SSO es la capacidad de iniciar sesión solo una vez para acceder a recursos de la organización. | Sí | Sí | Sí |
| Inicio de sesión único de usuario a las aplicaciones de Kerberos/NTLM. | Sí | Limitado | Sí, a través de VPN |
| Autorización segura y práctico inicio de sesión único para cuentas profesionales o educativas con Microsoft Passport y Windows Hello. | Sí | Sí | Sí |
| Acceso a la Tienda Windows para empresas con una cuenta profesional o educativa (no una cuenta de Microsoft). | Sí | Sí | Sí |
| Movilidad de las configuraciones de usuario conforme a la empresa entre dispositivos que usan cuentas profesionales o educativas. | Sí | Sí | Sí |
| Capacidad para restringir el acceso a las aplicaciones de la organización solo a los dispositivos que cumplen sus directivas. | Sí | Sí | Sí |
| Autoservicio de usuarios para el aprovisionamiento de dispositivos para trabajar desde cualquier lugar. | No | Sí | Sí |
| Capacidad para administrar dispositivos. | Sí, a través de GP/SCCM | Sí | Sí |

## Uso de dispositivos de trabajo con Azure AD Join y la unión a un dominio en Windows 10

Windows 10 ofrece dos modelos para que los dispositivos de trabajo accedan a los recursos de trabajo:

- Azure AD Join
- Unión a un dominio

 Ambos pueden ser opciones válidas según las necesidades y los requisitos de una organización. En algunos casos, las organizaciones pueden beneficiarse de la habilitación de ambos métodos de implementación.

## ¿Cuándo usar Azure Active Directory Join?

Azure AD Join es una nueva experiencia de aprovisionamiento de trabajo de autoservicio en Windows 10. Está destinada a trabajadores que acceden a los recursos de trabajo, como Office 365, principalmente en la nube. Es una forma sencilla de configurar los equipos, tabletas y teléfonos para la empresa. Los dispositivos se administran mediante la administración de dispositivos móviles, con controles coherentes entre plataformas de Windows.

**Use Azure AD Join por cualquiera de estos motivos**:

- Desea habilitar el autoservicio de aprovisionamiento de dispositivos de los trabajadores en cualquier parte.
- Desea proporcionar a los usuarios dispositivos móviles de trabajo como tabletas y teléfonos.
- Desea administrar un conjunto de usuarios en Azure AD en lugar de en Active Directory, como trabajadores estacionales, contratistas o alumnos.
- Desea proporcionar capacidades de unión a los trabajadores de sucursales remotas con infraestructura local limitada.
- No tiene ningún entorno de Active Directory local.

Algunas organizaciones usarán Azure AD Join como medio principal para implementar los dispositivos de trabajo, especialmente a medida que todos o la mayoría de los recursos migran a la nube. Las organizaciones híbridas con Active Directory y Azure AD también pueden implementar un método u otro, según el usuario o el departamento.

Por ejemplo, los distritos escolares y las universidades pueden administrar al personal en Active Directory y a los estudiantes en Azure AD. Algunas compañías pueden desear administrar las oficinas o los departamentos de ventas remotos en Azure AD. Ambos métodos, Azure AD Join y la unión a un dominio, pueden usarse dentro de una organización híbrida. Azure AD Join puede ser un excelente complemento a la unión a un dominio para implementar dispositivos en un entorno de trabajo.

**Si el acceso más habitual a los recursos de la empresa se realiza a través de la nube, la organización puede disfrutar de ventajas adicionales**:

- Puede quitar las dependencias de infraestructura de identidad local.
- Puede simplificar el modelo de implementación de dispositivos dejando las soluciones de creación de imágenes y permitiendo la configuración de autoservicio.
- Puede usar la administración de dispositivos móviles para administrar todos los dispositivos en diferentes plataformas.

Para más información sobre Azure AD Join, consulte [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-overview.md).

## ¿Cuándo usar la unión a un dominio (o seguir usándola)?

Durante los últimos 15 años, muchas organizaciones han usado la unión a un dominio para conectar dispositivos de trabajo. Permite a los usuarios iniciar sesión en sus dispositivos con sus cuentas profesionales o educativas de Active Directory. La unión a un dominio también permite a los responsables de TI administrar dichos dispositivos de forma central y total. Normalmente, las organizaciones confían en los métodos de creación de imágenes para aprovisionar los dispositivos y suelen usar System Center Configuration Manager (SCCM) o la directiva de grupo para administrarlos.

**Su empresa debería usar la unión a un dominio (o seguir usándola) por cualquiera de los siguientes motivos**:

- Tiene las aplicaciones Win32 implementadas en estos dispositivos que usan NTLM o Kerberos.
- Requiere GP o SCCM/DCM para administrar dispositivos.
- Desea seguir usando las soluciones de creación de imágenes para configurar los dispositivos de los empleados.

**La unión a un dominio en Windows 10 también proporciona las siguientes ventajas cuando está conectado a Azure AD**:

- Autenticación sólida de dispositivo enlazado y práctico inicio de sesión único para cuentas profesionales o educativas con Microsoft Passport y Windows Hello.
- Acceso a la Tienda Windows de la empresa para dispositivos con cuentas profesionales o educativas (sin necesidad de una cuenta de Microsoft).
- Movilidad de las configuraciones de usuario conforme a la empresa entre dispositivos que usan cuentas profesionales o educativas (sin necesidad de una cuenta de Microsoft).
- Capacidad para restringir el acceso a las aplicaciones de la organización solo a los dispositivos que cumplen sus directivas.

Para más información sobre Azure AD Join, consulte [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-overview.md).

## Habilitación de la unión de dispositivos personales para el trabajo o la escuela

Para admitir BYOD en la empresa, Windows 10 proporciona al usuario la capacidad de "agregar una cuenta profesional o educativa" a su equipo, tableta o teléfono. Después de que el usuario agregue una cuenta profesional o educativa, el dispositivo se registra con Azure AD y, opcionalmente, se inscribe en el sistema de administración de dispositivos móviles que la organización ha configurado. El directorio mostrará estos dispositivos como "Registrado" en lugar de como "Unido a Azure AD". Los administradores de TI pueden aplicar diferentes directivas según esta información, proporcionando un enfoque más ligero en dispositivos personales que en dispositivos de trabajo, si así lo desea.

Los usuarios pueden agregar un cuenta profesional o educativa a su dispositivo personal de una forma muy cómoda. Pueden hacerlo la primera vez que acceden a una aplicación de trabajo, o bien pueden hacerlo manualmente mediante el menú Configuración. Esta cuenta proporcionará SSO a los recursos de la organización.

Para más información sobre Azure AD Join, consulte [Conexión de dispositivos unidos a un dominio a Azure AD para experiencias en Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## Habilitación de la unión a un dominio o Azure AD Join

Todos los métodos descritos anteriormente (unión a un dominio, Azure AD Join y "agregar una cuenta profesional o educativa") tienen puntos de entrada en la experiencia de usuario de Windows 10. Sin embargo, todos requieren que un administrador de TI habilite la funcionalidad en la infraestructura antes de que funcione la experiencia.

## Requisitos para implementar Azure AD Join

Para implementar Azure AD Join para cualquier conjunto de usuarios necesita lo siguiente:

- Una suscripción de Azure AD.
- Una suscripción de Azure AD Premium, como la inscripción automática en administración de dispositivos móviles, si requiere más funcionalidades.
- Administración de dispositivos móviles: por ejemplo una suscripción a Microsoft Intune, administración de dispositivos móviles para Office 365 o cualquiera de los proveedores de administración de dispositivos móviles asociados integrados en Azure AD. (Para más información, consulte la sección [Preguntas más frecuentes](#frequently-asked-questions) al final de este artículo).

Si sus instalaciones son híbridas, se recomienda encarecidamente implementar Azure AD Connect para ampliar el directorio local a Azure AD.

## Requisitos para usar la unión a un dominio con Azure AD

La unión a un dominio continúa funcionando como siempre. Sin embargo, para tener las ventajas de Azure AD, es necesario lo siguiente:

- Una suscripción de Azure AD.
- Una implementación de Azure AD Connect para ampliar el directorio local a Azure AD.
- Una directiva que permite que los dispositivos unidos a un dominio tengan acceso condicional a Azure AD.
- Una directiva que permite acceder a dispositivos unidos a un dominio si se desea poder restringir el acceso para algunos dispositivos.
- System Center Configuration Manager version 1509 for Technical Preview para habilitar las reglas para requerir dispositivos compatibles. (Consulte la documentación de TechNet y publicaciones en blogs).

Para más información acerca de la unión a un dominio en Windows 10, consulte <link-to-DJ-in-Win10-deployment-guide>.

## Requisitos para usar BYOD y "Agregar una cuenta profesional o educativa"

Para habilitar Bring Your Own Device (BYOD) con cuentas profesionales o educativas, necesita lo siguiente:

- Una suscripción de Azure AD.
- Una suscripción de Azure AD Premium, como la inscripción automática en administración de dispositivos móviles, si requiere más funcionalidades.

## Requisitos para usar Microsoft Passport

Para habilitar Microsoft Passport, necesitará lo siguiente:

- Una infraestructura de clave pública (PKI) para compatibilidad de autenticación basada en certificados que usa Microsoft Passport.
- Una suscripción de Intune para compatibilidad de autenticación basada en certificados que usa Microsoft Passport para Azure AD Join y cuentas profesionales o educativas.
- System Center Configuration Manager version 1509 for Technical Preview (consulte la documentación de TechNet y las publicaciones de los blog) para compatibilidad de autenticación basada en certificados mediante Microsoft Passport para la unión a un dominio.
- Una directiva para habilitar Microsoft Passport en la organización.

Como alternativa a usar una PKI, puede habilitar Microsoft Passport basado en claves del modo siguiente:

- Implemente controladores de dominio de 'Versión preliminar de producción 1' en Windows Server 2016 (no es necesario el nivel funcional del bosque o del dominio y bastarán varios controladores de dominio para la redundancia que da servicio a cada sitio de Active Directory).
- Establezca una directiva para habilitar Microsoft Passport en la organización.

Para más información acerca de Microsoft Passport y Windows Hello en Windows 10, consulte <link-to-MS-Passport-and-Windows-Hello-document>.

## Preguntas más frecuentes
### ¿Qué productos de administración de dispositivos móviles de asociados se integran con Azure AD?

Los siguientes productos de proveedor se integran con Azure AD para ofrecer inscripción unificada y acceso condicional en Windows 10:

- AirWatch de VMware
- Citrix Xenmobile
- Lightspeed Mobile Manager
- Administración de dispositivos móviles locales SOTI

### ¿Qué ocurre con la unión al área de trabajo en Windows 10?
La unión al lugar de trabajo en Windows 8.1 se usaba para habilitar BYOD. En Windows 10, BYOD se habilita mediante "Agregar una cuenta profesional o educativa", como se explicó anteriormente en este documento. Para las organizaciones que no integran la administración de dispositivos móviles con Azure AD, los usuarios pueden inscribir el dispositivo en la administración manualmente mediante **Configuración** > **Cuentas** > **Acceso al trabajo**.


### ¿Los usuarios pueden conectar su cuenta de Microsoft a su cuenta de dominio en Windows 10?
No en Windows 10. En Windows 8.1, los usuarios de dispositivos unidos a un dominio podían "conectar" su cuenta de Microsoft (por ejemplo, de Hotmail, Live, Outlook, XBox, etc.) a su cuenta de dominio para habilitar ciertas experiencias como SSO a servicios de Live, el uso de la Tienda Windows y la movilidad de configuraciones de usuario entre dispositivos. En Windows 10, se ha retirado la funcionalidad de conexión de la cuenta de Microsoft. El usuario puede agregar una o más cuentas de Microsoft como cuentas adicionales para habilitar el SSO para los servicios de consumidor, como la Tienda Windows. Puede hacerlo en **Configuración** > **Cuentas** > **Su cuenta**.

Los usuarios que actualizan desde dispositivos unidos a un dominio de Windows 8.1 y que tenían su cuenta de Microsoft conectada, tendrán automáticamente su cuenta de Microsoft conectada agregada a la lista de cuentas adicionales que usan.


## Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-->