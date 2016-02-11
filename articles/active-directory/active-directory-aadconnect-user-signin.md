<properties
	pageTitle="Inicio de sesión de usuarios de Azure AD Connect | Microsoft Azure"
	description="Inicio de sesión de usuarios de Azure AD Connect para la configuración personalizada."
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



# Opciones para el inicio de sesión de los usuarios en Azure AD Connect

Azure AD Connect permite que los usuarios inicien sesión en los recursos en la nube y locales con las mismas contraseñas. Puede elegir entre varias maneras de habilitar esta opción.


### Sincronización de contraseñas
Con la sincronización de contraseñas, se sincronizan los valores hash de las contraseñas de usuario de Active Directory local con Azure AD. Cuando las contraseñas se cambian o se restablecen de forma local, las nuevas contraseñas se sincronizan de inmediato con Azure AD para que los usuarios puedan utilizar siempre en los recursos en la nube la misma contraseña que utilizan a nivel local. Las contraseñas nunca se envían a Azure AD ni se almacenan en Azure AD en texto no cifrado. La sincronización de contraseñas puede utilizarse junto con la escritura diferida de contraseñas para habilitar el autoservicio de restablecimiento de contraseña en Azure AD.

<center>![Nube](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[Más información acerca de la sincronización de contraseñas](https://msdn.microsoft.com/library/azure/dn246918.aspx)


### Federación con AD FS nuevo o existente en granja de Windows Server 2012 R2
Con el inicio de sesión federado, los usuarios pueden iniciar sesión en los servicios basados en Azure AD con sus contraseñas locales y, mientras se encuentren en la red corporativa, sin necesidad de volver a introducir sus contraseñas. La opción de federación con AD FS le permite implementar servicios AD FS nuevos o especificar servicios AD FS existentes en la granja de Windows Server 2012 R2. Si decide especificar una granja existente, Azure AD Connect configurará la confianza entre la granja y Azure AD para que los usuarios puedan iniciar sesión.

<center>![Nube](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### Para implementar la federación con AD FS en Windows Server 2012 R2, necesitará lo siguiente.
Si va a implementar una nueva granja:

- Un servidor Windows Server 2012 R2 para el servidor de federación.
- Un servidor Windows Server 2012 R2 para el Proxy de aplicación web.
- un archivo .pfx con un certificado SSL para el nombre de servicio de federación previsto, como fs.contoso.com.

Si va a implementar una nueva granja o va a utilizar una granja existente:

- Credenciales de administrador local en los servidores de federación.
- Credenciales de administrador local en cualquier servidor de grupo de trabajo (no unido a dominio) en el que pretenda implementar el rol Proxy de aplicación web.
- La máquina en la que se ejecuta al asistente debe poder conectarse a otras máquinas en las que va a instalar AD FS o el Proxy de aplicación web a través de Administración remota de Windows.

#### Inicio de sesión con una versión anterior de AD FS o una solución de terceros
Si ya ha configurado el inicio de sesión en la nube con una versión anterior de AD FS (por ejemplo, AD FS 2.0) o un proveedor de federación de terceros, puede optar por omitir la configuración del inicio de sesión de usuarios a través de Azure AD Connect. De este modo, podrá obtener la sincronización más reciente y otras capacidades de Azure AD Connect mientras sigue usando la solución existente para el inicio de sesión.

### Selección de un método de inicio de sesión para los usuarios de su organización
Para la mayoría de las organizaciones que simplemente desean habilitar el inicio de sesión de usuarios en Office 365, aplicaciones SaaS y otros recursos basados en Azure AD, se recomienda la opción de sincronización de contraseña predeterminada. Sin embargo, algunas organizaciones tienen razones concretas para usar una opción de inicio de sesión federado, como AD FS. Entre ellas se incluyen las siguientes:

- Su organización ya tiene implementado AD FS o un proveedor de federación de terceros.
- La directiva de seguridad prohíbe la sincronización de los valores hash de contraseña en la nube.
- Se requiere que los usuarios experimenten un inicio de sesión único fluido (sin solicitudes de contraseña adicionales) al tener acceso a recursos en la nube a partir de equipos unidos a dominio en la red corporativa.
- Se requieren algunas de las capacidades específicas de AD FS:
	- Multi-Factor Authentication a nivel local mediante un proveedor de terceros o tarjetas inteligentes (obtenga información acerca de los proveedores de MFA de terceros para AD FS en Windows Server 2012 R2).
	- Características de integración de Active Directory, como el bloqueo de cuenta no rígido o la directiva de horas de trabajo y contraseña de AD.
	- Acceso condicional a los recursos locales y en la nube mediante el registro de dispositivos, la unión a Azure AD o las directivas MDM de Intune.


## Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->