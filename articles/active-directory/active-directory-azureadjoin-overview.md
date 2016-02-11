<properties 
	pageTitle="Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join | Microsoft Azure" 
	description="Ofrece información general detallada de cómo los dispositivos de Windows 10 pueden usar Azure AD Join para registrarse en Azure Active Directory." 
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
	ms.date="12/07/2015" 
	ms.author="femila"/>

# Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join

## ¿Qué es Azure Active Directory Join? 
Azure Active Directory Join (Azure AD) es la funcionalidad que registra un dispositivo propiedad de la empresa en Active Directory para permitir la administración centralizada. Significa que los usuarios (empleados, estudiantes) ahora se pueden conectar a la nube de la empresa a través de Azure Active Directory. Esto simplifica las implementaciones de Windows y el acceso a los recursos y las aplicaciones de la organización desde cualquier dispositivo de Windows, tanto propiedad de la empresa como personal (BYOD).

Azure AD Join está pensado para empresas que es la primera vez que operan en la nube o que solo funcionan en ella (normalmente pequeñas y medianas empresas) que carecen de una infraestructura de Active Directory local. Dicho esto, las grandes organizaciones pueden usar y usarán Azure AD en dispositivos que sean incapaces de unirse a un dominio de la manera tradicional (como es el caso de los dispositivos móviles, por ejemplo) o para usuarios que necesiten principalmente acceso a Office 365 u otras aplicaciones SaaS de Azure AD.

Mientras que la unión a un dominio de la manera tradicional le va a seguir proporcionando la mejor experiencia local en dispositivos que son capaces de ello, Azure AD Join resulta adecuado para dispositivos que no pueden hacerlo y en zonas en las que desea administrar a los usuarios en la nube con capacidades MDM en lugar del uso tradicional de la administración de dominios con directiva de grupo y SCCM.

![](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## ¿Por qué las empresas deben adoptar Azure AD Join? 

* **Si su empresa opera principalmente en la nube**: si tiene, o se va a pasar a, un modelo donde piensa reducir su superficie local y desea funcionar más en la nube, Azure AD Join puede resultarle ventajoso. Quizá ha creado cuentas de Azure AD manualmente o a través de la sincronización de su AD local. En cualquier caso, tiene una cuenta de Azure AD y puede usarla para iniciar sesión en Windows 10. Los usuarios pueden unir sus equipos a Azure AD a través del proceso de configuración rápida o mediante la experiencia de configuración básica. Ahora, los usuarios disfrutan de acceso SSO a sus recursos de nube, como Office 365 en el explorador o en las aplicaciones de Office. 
* **Instituciones educativas**: uno de los escenarios más importantes sobre los que oímos hablar es el de las instituciones educativas y sus dos tipos de usuarios: profesores y estudiantes. A los profesores se les considera miembros más a largo plazo de la organización, y lo deseable es crear cuentas locales para ellos. Pero los estudiantes son miembros más a corto plazo de la organización y, por lo tanto, se pueden administrar en Azure AD de modo que la escala de directorio se puede mover a la nube en lugar de al entorno local. Los estudiantes ahora pueden iniciar sesión en Windows con su cuenta de Azure AD y obtener acceso a los recursos de Office 365 en las aplicaciones de Office. 
* **Negocios minoristas**: otra área de preocupación para los clientes es su deseo de simplificar la administración de los trabajadores de temporada. De nuevo, los empleados a jornada completa, más a largo plazo, se pueden crear como cuentas locales y usarían normalmente equipos unidos a un dominio. Pero los trabajadores de temporada son miembros más a corto plazo de la organización y, por lo tanto, es necesario administrarlos con licencias de usuario que se puedan mover fácilmente. La creación de estos usuarios en la nube con licencias de Office 365 permite a estos usuarios obtener los beneficios de iniciar sesión en aplicaciones de Windows y Office con una cuenta de Azure AD, y mantener al mismo tiempo la movilidad de sus licencias una vez que abandonan su puesto de trabajo. 
* **Otras empresas**: y aparte de los escenarios que se han descrito anteriormente, puede encontrar que aun manteniendo a los usuarios en su directorio local de AD, podría beneficiarse de la unión de sus usuarios a Azure AD gracias a la experiencia de unión simplificada, la administración de dispositivos en Azure AD, la inscripción automática de MDM y el inicio de sesión único en recursos de Azure AD y locales.  

## Funciones de Azure AD Join
Con Azure AD Join, obtendrá lo siguiente:

* **Aprovisionamiento automático de dispositivos propiedad de la empresa**: con Windows 10, los usuarios finales pueden configurar un dispositivo completamente nuevo de forma inmediata, sin necesidad de que intervenga el departamento de TI.


* **Compatibilidad con factores de forma modernos**: Azure AD Join funciona en dispositivos que carecen de las funcionalidades tradicionales para unirse a un dominio.


* **Uso de las cuentas organizativas existentes**: los usuarios finales ya no necesitan crear y mantener una cuenta Microsoft personal para obtener la mejor experiencia en los dispositivos emitidos por la empresa, como hacían en Windows 8. Ahora, pueden usar su cuenta profesional existente en Azure AD. Para muchas organizaciones, esto significa básicamente configurar e iniciar sesión en Windows con las mismas credenciales que usan para acceder a Office 365.


* **Inscripción automática de MDM**: los dispositivos se pueden inscribir automáticamente en la administración cuando se conectan a Azure AD. Esto funciona con Microsoft Intune y MDM de otros fabricantes. Cuando se administran con Intune, el departamento de TI podrá controlar y administrar los dispositivos unidos a Azure AD junto con los dispositivos unidos a un dominio en la consola de administración de SCCM.


* **Inicio de sesión único en recursos de la empresa**: los usuarios disfrutan de un inicio de sesión único desde el escritorio de Windows en las aplicaciones y los recursos de la nube, como Office 365 y miles de aplicaciones empresariales que basan su autenticación en Azure AD a través de [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md). Los dispositivos propiedad de la empresa que se han unido a Azure AD también disfrutarán de SSO en recursos locales cuando el dispositivo esté en la red corporativa, y desde cualquier lugar, cuando estos recursos se expongan a través del [proxy de la aplicación de Azure AD](https://msdn.microsoft.com/library/azure/Dn768219.aspx).


* **Movilidad de estado del SO**: cosas como la configuración de accesibilidad, los sitios web y las contraseñas Wi-Fi se sincronizarán entre dispositivos propiedad de la empresa sin necesidad de una cuenta Microsoft personal.


* **Tienda Windows preparada para la empresa**: la Tienda Windows admitirá la adquisición de aplicaciones y licencias con cuentas de Azure AD. Las organizaciones podrán adquirir aplicaciones de licencias por volumen y ponerlas a disposición de los usuarios en su organización.

## Cómo dispositivos diferentes funcionan con Azure AD Join

| Dispositivo de la empresa (unido al dominio local) | Dispositivo de la empresa (unido a la nube) | Dispositivo personal |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Los usuarios inician sesión en Windows con las credenciales de trabajo (como lo hacen hoy) | Los usuarios pueden iniciar sesión en Windows con las credenciales de trabajo que se administran en Azure AD. Esto es importante para los dispositivos corporativos en tres casos: la organización no tiene AD local (por ejemplo, las pequeñas empresas), la organización no crea todas las cuentas de usuario en AD (por ejemplo, los estudiantes, los consultores y los trabajadores de temporada), los dispositivos corporativos no se pueden unir a un dominio (local), como los teléfonos o tabletas con un SKU móvil. Por ejemplo, un dispositivo secundario llevado a la planta de fábrica o comercial, funciona para organizaciones administradas y federadas. | Los usuarios inician sesión en Windows con sus credenciales de MSA personales (sin cambios). |
| Los usuarios tienen acceso móvil a la configuración y a la Tienda Windows; estos servicios funcionan con la cuenta profesional (sin necesidad de MSA personal) y es necesario que las organizaciones conecten su AD local a Azure AD. | Configuración de autoservicio: los usuarios pueden realizar la configuración rápida (FRX) a través de su cuenta profesional (como una alternativa al aprovisionamiento de TI de los dispositivos; se admiten ambos métodos). | Es sencillísimo agregar una cuenta de trabajo administrada en AD o Azure AD. |
| Inicio de sesión único (SSO) desde el escritorio para trabajar en aplicaciones, sitios web y recursos de forma local y en aplicaciones en la nube que usan Azure AD para la autenticación. | Registro automático en el directorio empresarial (Azure AD) e inscripción automática en MDM. (Característica de Azure AD Premium) | Proporciona SSO entre aplicaciones y en sitios web y recursos con esta cuenta profesional. |
| Los usuarios pueden agregar su MSA personal para tener acceso a sus imágenes y archivos personales sin que por ello se vean afectados los datos empresariales (la configuración de movilidad sigue funcionando con la cuenta profesional). La cuenta MSA permite SSO y ya no controla la movilidad de la configuración. | Autoservicio de restablecimiento de contraseña (SSPR) en Winlogon (posibilidad de restablecer contraseñas olvidadas) (Para esto, necesita la edición Premium). | Proporciona acceso a la Tienda corporativa para que los usuarios puedan comprar y usar aplicaciones de línea de negocio en sus dispositivos personales. | |


## Información adicional
* [Windows 10 para la empresa: formas de usar dispositivos para trabajar](active-directory-azureadjoin-windows10-devices-overview.md)
* [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Autenticación de identidades sin contraseñas a través de Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Conozca los escenarios de uso de Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Experiencias de conexión de dispositivos unidos a un dominio a Azure AD para Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configuración de Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1210_2015-->