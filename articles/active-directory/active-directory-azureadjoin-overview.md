<properties
	pageTitle="Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join | Microsoft Azure"
	description="Ofrece información general detallada acerca de cómo los dispositivos de Windows 10 pueden usar Azure AD Join para registrarse en Azure Active Directory."
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

# Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join

## ¿Qué es Azure Active Directory Join?
Azure Active Directory Join (Azure AD) es la funcionalidad que registra un dispositivo propiedad de la empresa en Azure Active Directory para permitir su administración centralizada. Hace posible que los usuarios, como empleados y estudiantes, puedan conectarse a la nube de la empresa mediante Azure Active Directory. Esto simplifica las implementaciones de Windows y el acceso a los recursos y las aplicaciones de la organización desde cualquier dispositivo de Windows, tanto propiedad de la empresa como personal (BYOD).

Azure AD Join está pensado para empresas que es la primera vez que operan en la nube o que solo funcionan en ella, así como pequeñas y medianas empresas que carecen de una infraestructura de Windows Server Active Directory local. Dicho esto, las grandes organizaciones también pueden usar Azure AD en dispositivos que no pueden unirse a un dominio de la manera tradicional (por ejemplo, los dispositivos móviles) o para usuarios que necesitan principalmente acceso a Office 365 u otras aplicaciones SaaS de Azure AD.

Aunque la unión a un dominio de la manera tradicional aún proporciona la mejor experiencia local para los dispositivos que admiten la unión de dominios, Azure AD Join resulta adecuado para dispositivos que no la admiten. Azure AD Join también es adecuado para la administración de usuarios en la nube. Lo hace mediante funcionalidades de administración de dispositivos móviles, en lugar de usar herramientas de administración de dominio tradicionales, como la directiva de grupo y System Center Configuration Manager (SCCM).

![Información general de los dispositivos de la empresa y personales con un entorno de Active Directory y Azure AD local](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## ¿Por qué las empresas deberían adoptar Azure AD Join?

* **Empresas que operan principalmente en la nube**: si ha pasado o va a pasar a un modelo en el que piensa reducir su superficie local y desea funcionar más en la nube, Azure AD Join puede resultarle ventajoso. Quizá ha creado cuentas de Azure AD manualmente o mediante la sincronización de su entorno de Active Directory local. En cualquier caso, tiene una cuenta de Azure AD y puede usarla para iniciar sesión en Windows 10. Los usuarios pueden unir sus equipos a Azure AD mediante la configuración rápida (OOBE) o mediante el menú Configuración. Después de unirse, los usuarios disfrutarán de un acceso de inicio de sesión único (SSO) a recursos en la nube como Office 365, tanto en sus exploradores como en las aplicaciones de Office.

* **Instituciones educativas**: uno de los escenarios que nos suelen comentar es que las instituciones educativas tienen dos tipos de usuarios: profesores y estudiantes. A los profesores se les considera miembros más a largo plazo de la organización, por lo que es deseable crear cuentas locales para ellos. Sin embargo, los estudiantes son miembros de la organización a más corto plazo y, por tanto, se pueden administrar en Azure AD. Esto significa que la escala de directorio se puede trasladar a la nube, en lugar de almacenarse localmente. También significa que los alumnos podrán iniciar sesión en Windows con sus cuentas de Azure AD y obtener acceso a los recursos de Office 365, en sus exploradores o en aplicaciones de Office.

* **Negocios minoristas**: otro escenario que nos suelen comentar es su deseo de simplificar la administración de los trabajadores estacionales. De nuevo, las cuentas para los empleados a jornada completa y a más largo plazo se suelen crear como cuentas locales en equipos unidos a un dominio. Sin embargo, los trabajadores estacionales son miembros a más corto plazo de la organización, por lo que es deseable administrar sus cuentas allí donde las licencias de usuario se puedan mover más fácilmente. La creación de estas cuentas de usuario en la nube con licencias de Office 365 permite a estos usuarios obtener los beneficios de iniciar sesión en aplicaciones de Windows y Office con una cuenta de Azure AD. Además, se mantiene más flexibilidad con sus licencias una vez que abandonan su puesto de trabajo.
* **Otras empresas**: aunque mantenga a los usuarios en un entorno de Active Directory local, se puede beneficiar de tener a los usuarios unidos con Azure AD. Eso es porque Azure AD ofrece una experiencia de unión simplificada, la administración eficaz de dispositivos, la inscripción automática en la administración de dispositivos móviles y el inicio de sesión único a Azure AD y los recursos locales.  

## ¿Qué funcionalidades ofrece Azure AD Join?
Con Azure AD Join, obtendrá lo siguiente:

* **Aprovisionamiento automático de dispositivos de la empresa**: con Windows 10, los usuarios pueden configurar un dispositivo completamente nuevo de forma inmediata, sin necesidad de que intervenga el departamento de TI.


* **Compatibilidad con factores de forma modernos**: Azure AD Join funciona en dispositivos que carecen de las funcionalidades tradicionales para unirse a un dominio.


* **Posibilidad de usar cuentas de organización existentes**: los usuarios ya no necesitan crear y mantener una cuenta de Microsoft personal para obtener la mejor experiencia en los dispositivos de la empresa, como hacían con Windows 8. Ahora, pueden usar sus cuentas profesionales existentes en Azure AD. Para muchas organizaciones, esto significa básicamente que los usuarios pueden configurar e iniciar sesión en Windows con las mismas credenciales que usan para acceder a Office 365.


* **Inscripción automática en administración de dispositivos móviles**: los dispositivos se pueden inscribir automáticamente en la administración de dispositivos móviles cuando se conectan a Azure AD. Este proceso funciona con soluciones de administración de dispositivos móviles de asociados y Microsoft Intune. Cuando la administración de dispositivos se realiza con Intune, los administradores de TI pueden controlar y administrar los dispositivos unidos a Azure AD junto con los dispositivos unidos a un dominio en la consola de administración de SCCM.


* **Inicio de sesión único en recursos de la empresa**: los usuarios disfrutan de un inicio de sesión único desde el escritorio de Windows en las aplicaciones y los recursos en la nube, como Office 365 y miles de aplicaciones empresariales que dependen de Azure AD para la autenticación mediante [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md). Los dispositivos de la empresa que se han unido a Azure AD también disfrutarán de SSO a recursos locales cuando el dispositivo esté en la red corporativa, y desde cualquier lugar, cuando estos recursos se expongan a través del [proxy de la aplicación de Azure AD](https://msdn.microsoft.com/library/azure/Dn768219.aspx).


* **Movilidad de estado del SO**: la configuración de accesibilidad, los sitios web, las contraseñas Wi-Fi y otras opciones se sincronizarán en los dispositivos de la empresa sin necesidad de una cuenta de Microsoft personal.


* **Tienda Windows preparada para la empresa**: la Tienda Windows admite la adquisición de aplicaciones y licencias con cuentas de Azure AD. Las organizaciones pueden adquirir aplicaciones de licencias por volumen y ponerlas a disposición de los usuarios de su organización.

## ¿Cómo funcionan diferentes dispositivos con Azure AD Join?

| Dispositivo de la empresa (unido a un dominio local) | Dispositivo de la empresa (unido a la nube) | Dispositivo personal |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Los usuarios pueden iniciar sesión en Windows con las credenciales de trabajo (como lo hacen hoy). | Los usuarios pueden iniciar sesión en Windows con las credenciales de trabajo que se administran en Azure AD. Esto es importante para los dispositivos de la empresa en tres casos: 1) La organización no tiene Active Directory local (por ejemplo, una pequeña empresa). 2) La organización no crea todas las cuentas de usuario en Active Directory (por ejemplo, las cuentas de estudiantes, consultores o trabajadores estacionales no se crean en Active Directory). 3) La organización tiene dispositivos de la empresa que no se pueden unir a un dominio (local), como los teléfonos o tabletas con una SKU móvil (por ejemplo, un dispositivo secundario llevado a una fábrica o tienda). Azure AD Join permite unir dispositivos de la empresa para organizaciones administradas y federadas. | Los usuarios inician sesión en Windows con las credenciales de su cuenta de Microsoft personal (sin cambios). |
| Los usuarios tienen acceso a la configuración de movilidad y la tienda Windows de la empresa. Estos servicios funcionan con cuentas profesionales y no requieren una cuenta de Microsoft personal. Esto requiere que las organizaciones conecten su entorno de Active Directory local a Azure AD. | Los usuarios pueden hacer la configuración de autoservicio. Pueden realizar la configuración rápida (FRX) mediante su cuenta profesional como alternativa a que el departamento de TI realice el aprovisionamiento de los dispositivos, aunque se admiten ambos métodos. | Los usuarios pueden agregar fácilmente una cuenta profesional administrada en Active Directory o Azure AD. |
| Los usuarios tienen la capacidad de SSO desde el escritorio para aplicaciones profesionales, sitios web y recursos, incluidos recursos locales y aplicaciones en la nube que usan Azure AD para la autenticación. | Automáticamente, los dispositivos se registran en el directorio de empresa (Azure AD) y se inscriben en administración de dispositivos móviles. (Característica de Azure AD Premium). | Los usuarios tienen capacidad de SSO en las aplicaciones, los sitios web y los recursos con esta cuenta profesional. |
| Los usuarios pueden agregar sus cuentas de Microsoft personales para acceder a sus imágenes y archivos personales sin afectar a los datos de la empresa. (La configuración de movilidad sigue funcionando con sus cuentas profesionales). La cuenta de Microsoft permite SSO y ya no controla la movilidad de la configuración. | Los usuarios pueden usar el autoservicio de restablecimiento de contraseña (SSPR) en winlogon, lo que significa que pueden restablecer una contraseña olvidada. (Característica de Azure AD Premium). | Los usuarios tienen acceso a la Tienda Windows de la empresa para adquirir y utilizar aplicaciones de línea de negocio en sus dispositivos personales. | |


## Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0302_2016-->