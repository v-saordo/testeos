<properties 
	pageTitle="Uso de dispositivos con Windows 10 en el área de trabajo | Microsoft Azure" 
	description="Ofrece una instantánea de las capacidades que pueden obtener los usuarios y los responsables de TI, comparando las distintas formas en que un dispositivo se puede aprovisionar y usar en una empresa con Windows 10." 
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

# Uso de dispositivos de Windows 10 en el área de trabajo

Windows 10 ofrece a las organizaciones tres modelos para que los usuarios puedan acceder a los recursos de trabajo de una manera segura y conveniente.

1. **Azure AD Join**, una nueva experiencia de aprovisionamiento de trabajo de autoservicio en Windows 10 destinada a trabajadores que acceden principalmente a los recursos de trabajo en la nube como Office 365.
1. **Unión a un dominio**, que funciona mejor en Windows 10 si se conecta a Azure AD, pensado para organizaciones con grandes inversiones en aplicaciones y recursos locales.
1. **Una nueva experiencia BYOD simplificada**, que permite a los usuarios agregar una cuenta profesional en Windows para tener acceso fácilmente a los recursos de trabajo en dispositivos personales.

En la tabla siguiente se presenta una instantánea de las capacidades que pueden obtener los usuarios y los responsables de TI, comparando las distintas formas en que un dispositivo se puede aprovisionar y usar en una empresa con Windows 10:

| | Unión a un dominio | Azure AD Join | Dispositivo personal |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| Inicio de sesión en un dispositivo Windows mediante cuentas profesionales | Sí | Sí | No |
| Inicio de sesión único de usuario a las aplicaciones de Office365 y Azure AD, [1] inicio de sesión único o la capacidad de tener acceso a los recursos de la empresa sin escribir credenciales de nuevo después de iniciar sesión una vez. | Sí | Sí | Sí |
| Inicio de sesión único de usuario a las aplicaciones de Kerberos/NTLM | Sí | Limitado | A través de VPN |
| Autenticación sólida e inicio de sesión único para cuentas profesionales con Microsoft Passport y Windows Hello. | Sí | Sí | Sí |
| Acceso a la Tienda Windows para empresas mediante una cuenta profesional (sin necesidad de una cuenta Microsoft). | Sí | Sí | Sí |
| Movilidad de las configuraciones de usuario conforme a la empresa entre dispositivos que usan cuentas profesionales | Sí | Sí | Sí |
| Acceso restringido a las aplicaciones de la organización desde dispositivos conformes a las directivas | Sí | Sí | Sí |
| Aprovisionamiento de autoservicio de usuarios de dispositivos para trabajar desde cualquier lugar | No | Sí | Sí |
| Capacidad para administrar dispositivos | Sí, a través de GP/SCCM | Sí | Sí |

##Dispositivos de trabajo: Azure AD Join y unión a un dominio

Windows 10 ofrece dos modelos para dispositivos de trabajo para obtener acceso a los recursos de trabajo:

- Azure AD Join
- Unión a un dominio.

 Ambos son modelos válidos según las necesidades y los requisitos de la organización. En algunos casos, las organizaciones pueden beneficiarse de la habilitación de ambos métodos de implementación.

## ¿Cuándo usar Azure Active Directory Join?

Azure AD Join es una nueva experiencia de aprovisionamiento de trabajo de autoservicio en Windows 10. Está destinada a trabajadores que acceden principalmente a los recursos de trabajo en la nube como Office 365. Es una forma sencilla de configurar los equipos, tabletas y teléfonos para la empresa. Los dispositivos se administran a través de MDM mediante controles coherentes entre plataformas de Windows.

**Use Azure AD Join por cualquiera de estos motivos**:

1.	Desea habilitar el aprovisionamiento de autoservicio en los dispositivos de los trabajadores en cualquier parte.
2.	Desea proporcionar a los usuarios dispositivos móviles de trabajo como tabletas y teléfonos.
3.	Desea administrar un conjunto de usuarios en Azure AD en lugar de en AD, como trabajadores estacionales, contratistas o alumnos.
4.	Desea proporcionar capacidades de unión a los trabajadores de sucursales remotas con infraestructura local limitada.
5.	No tiene AD local.

Algunas organizaciones usarán Azure AD Join como medio principal para implementar los dispositivos de trabajo, especialmente a medida que todos o la mayoría de los recursos migran a la nube. Las organizaciones híbridas con AD y Azure AD también pueden implementar un método u otro según el usuario o el departamento. Los distritos escolares y las universidades pueden administrar el personal en Active Directory y los alumnos en Azure AD o algunas compañías podrían necesitar oficinas o departamentos de ventas remotos en Azure AD. Ambos modelos, Azure AD Join y unión a un dominio, pueden usarse dentro de una organización híbrida. Azure AD Join puede ser un excelente complemento para la implementación de dispositivos en un entorno de trabajo.

**Si la mayoría de los accesos a recursos de la empresa se realiza a través de la nube, una organización puede disfrutar de ventajas adicionales**:

- Puede quitar las dependencias de infraestructura de identidad local.
- Puede simplificar el modelo de implementación de dispositivos dejando las soluciones de creación de imágenes y permitiendo la configuración de autoservicio.
- Puede usar MDM para administrar todos los dispositivos a través de diferentes plataformas.

Para más información sobre Azure AD Join, consulte [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-overview.md).

## ¿Cuándo usar unión a un dominio (o seguir usándolo)?

Durante los últimos 15 años o más, las organizaciones emplean el método tradicional de unirse a un dominio para que los dispositivos conectados funcionen. Así, ha permitido a los usuarios iniciar sesión en sus dispositivos con sus cuentas profesionales de AD y a los responsables de TI administrar centralmente y por completo estos dispositivos. Las organizaciones suelen confiar en los métodos de creación de imágenes para aprovisionar los dispositivos y usan generalmente System Center Configuration Manager (SCCM) o la directiva de grupo para administrarlos.

**Use la unión a un dominio en la empresa por alguno de estos motivos**:

- Tiene las aplicaciones Win32 implementadas en estos dispositivos que usan NTLM o Kerberos.
- Requiere GP o SCCM/DCM para administrar dispositivos.
- Desea seguir usando las soluciones de creación de imágenes para configurar los dispositivos de los empleados.

**La unión a un dominio en Windows 10 proporcionará las siguientes ventajas después de haberse conectado a Azure AD**:

- Autenticación sólida de dispositivo enlazado e inicio de sesión único para cuentas profesionales con Microsoft Passport y Windows Hello.
- Acceso a la Tienda Windows para empresas mediante cuentas profesionales (sin necesidad de una cuenta Microsoft).
- Movilidad de las configuraciones de usuario conforme a la empresa entre dispositivos que usan cuentas profesionales (sin necesidad de una cuenta Microsoft).
- Capacidad para restringir el acceso a las aplicaciones de la organización desde dispositivos conformes a las directivas.
- Para más información sobre Azure AD Join, consulte [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-overview.md).

##Habilitación de dispositivos personales para trabajar con cuentas profesionales

Para admitir BYOD en la empresa, Windows 10 proporciona al usuario la capacidad de "agregar una cuenta profesional o educativa" a su PC, tableta o teléfono. La adición de una cuenta profesional registra el dispositivo con Azure AD y, opcionalmente, lo inscribe en la MDM que la organización haya configurado para los dispositivos. El directorio mostrará estos dispositivos como "Registrado" en lugar de como "Unido a Azure AD". Los responsables de TI pueden aplicar diferentes directivas según esta información, proporcionando un enfoque más ligero en un dispositivo personal que en un dispositivo de trabajo si así lo desea.

Mediante el acceso a una aplicación de trabajo por primera vez o, manualmente a través de la aplicación de configuración, los usuarios pueden agregar una cuenta profesional en su dispositivo personal de una forma muy cómoda. Esta cuenta profesional proporcionará SSO a los recursos de la organización.

Para más información sobre Azure AD Join, consulte [Conexión de dispositivos unidos a un dominio a Azure AD para experiencias en Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## Habilitación de unión a un dominio o Azure AD Join 

Todos los métodos descritos anteriormente (unión a un dominio, Azure AD Join y Agregar cuenta profesional) tienen puntos de entrada en la experiencia de usuario de Windows 10, sin embargo, requieren de los responsables de TI para habilitar la funcionalidad de la infraestructura para que la experiencia funcione.

##Azure AD Join

Para implementar Azure AD Join para cualquier conjunto de usuarios necesita lo siguiente:
---------------------------------------------------------------------------
- Una suscripción de Azure AD.
- Otras capacidades requerirán una suscripción de Azure AD Premium como la inscripción automática de MDM.
- Una suscripción de Azure AD Premium.
- Una MDM (suscripción de Intune o MDM para Office365 o cualquiera de los proveedores de MDM independientes que integran Azure AD: consulte la sección de preguntas más frecuentes para más información)
- Si es una organización híbrida, es muy recomendable hacer lo siguiente:
- Implementar Azure AD Connect para extender el directorio local a Azure AD.

##Unión a un dominio

Unión a un dominio continúa funcionando como siempre, sin embargo, para aprovechar las ventajas de Azure AD se necesita lo siguiente:

- Una suscripción de Azure AD.
- Implementar Azure AD Connect para extender el directorio local a Azure AD.
- Establecer la directiva para conectar los dispositivos unidos a un dominio a Azure AD
- Para más información acerca de la unión a un dominio de Windows 10, consulte <link-to-DJ-in-Win10-deployment-guide>.
- Para obtener acceso condicional, puede crear una directiva que permita el acceso a dispositivos ‘unidos a un dominio’. Para habilitar las reglas para requerir dispositivos compatibles necesita lo siguiente:
- Versión 1509 de System Center Configuration Manager para vista previa técnica (consulte la documentación y las entradas de blog de TechNet).

##BYOD y Agregar cuenta profesional

Para habilitar BYOD con cuentas profesionales necesita lo siguiente:

- Una suscripción de Azure AD.
- Otras capacidades requerirán una suscripción de Azure AD Premium como la inscripción automática de MDM.
- Una suscripción de Azure AD Premium.
- Una MDM (suscripción de Intune o MDM para Office365 o cualquiera de los proveedores de MDM independientes que integran Azure AD: consulte la sección de preguntas más frecuentes para más información)

##Microsoft Passport

Para habilitar Microsoft Passport además, necesitará lo siguiente:

- Infraestructura PKI para compatibilidad de autenticación basada en certificados mediante Microsoft Passport.
- Suscripción de Intune para compatibilidad de autenticación basada en certificados mediante Microsoft Passport para Azure AD Join y cuentas profesionales.
- Versión 1509 de System Center Configuration Manager para vista previa técnica (consulte la documentación y las entradas de blog de TechNet) para compatibilidad de autenticación basada en certificados mediante Microsoft Passport para unión a dominio.
- Establezca una directiva para habilitar Microsoft Passport en la organización.

Como alternativa a tener una infraestructura PKI puede habilitar Microsoft Passport basado en claves haciendo lo siguiente:

- Implemente controladores de dominio de 'Vista previa de producción 1' en Windows Server 2016 (no es necesario el nivel funcional del bosque o del dominio y bastarán un par de controladores de dominio para la redundancia que da servicio a cada sitio de AD).
- Establezca una directiva para habilitar Microsoft Passport en la organización.
- Para más información acerca de Microsoft Passport y Windows Hello en Windows 10, consulte <link-to-MS-Passport-and-Windows-Hello-document>.

## Preguntas más frecuentes

###¿Qué productos de proveedores de MDM independientes integran Azure AD?

Los siguientes productos de proveedor se integran con Azure AD para ofrecer inscripción unificada y acceso condicional en Windows 10:

- AirWatch de VMware
- Citrix Xenmobile
- Lightspeed Mobile Manager
- MDM local de SOTI

###¿Qué ocurre con la unión al área de trabajo en Windows 10?
La unión al área de trabajo en Windows 8.1 se usaba para habilitar BYOD. En Windows 10, BYOD se habilita a través de la opción Agregar una cuenta profesional como se explicó anteriormente en este documento. Para las organizaciones que no integran su MDM con Azure AD, los usuarios pueden inscribir el dispositivo en administración manualmente a través de **Configuración** > **Cuentas** > **Acceso al trabajo**.

###¿Puede el usuario conectar su cuenta de Microsoft (MSA) a su cuenta de dominio en Windows 10?
No en Windows 10. En Windows 8.1, los usuarios de dispositivos unidos a un dominio podían "conectar" la MSA (por ejemplo, de Hotmail, Live, Outlook, XBox, etc.) a su cuenta de dominio para habilitar ciertas experiencias como SSO a servicios de Live, el uso de la Tienda Windows y la movilidad de configuraciones de usuario entre dispositivos. En Windows 10, se ha retirado la funcionalidad de conexión de MSA. El usuario puede agregar una o más MSA como cuentas adicionales para habilitar el SSO a los servicios al consumidor, como la Tienda Windows. Puede hacerlo en **Configuración** > **Cuentas** > **Su cuenta**.

Los usuarios que actualizan desde dispositivos unidos a un dominio de Windows 8.1 que tenían su MSA conectada, tendrán automáticamente su MSA conectada agregada a la lista de cuentas adicionales que usan.


## Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->