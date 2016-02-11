<properties
	pageTitle="Vista previa de Servicios de dominio de Azure Active Directory: características | Microsoft Azure"
	description="Características de Servicios de dominio de Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Servicios de dominio de Azure AD *(Vista previa)*

## Características
Las siguientes características están disponibles en la versión de vista previa de Servicios de dominio de Azure AD.

- **Experiencia de implementación sencilla:** puede habilitar Servicios de dominio de Azure AD para el inquilino de Azure AD mediante unos cuantos clics. Independientemente de si el inquilino de Azure AD es un inquilino en la nube o sincronizado con el directorio local, el dominio administrado se puede aprovisionar rápidamente.

- **Compatibilidad para la unión a dominio:** le resultará muy fácil unir a dominios equipos de la red virtual de Azure en la que Servicios de dominio de Azure AD está disponible. La experiencia de unión a dominio en sistemas operativos de cliente y servidor de Windows funciona sin problemas en dominios ofrecidos por Servicios de dominio de Azure AD. También puede utilizar herramientas automatizadas de unión a dominio en esos dominios.

- **Instancia de un dominio por directorio de Azure AD:** puede crear un único dominio de Active Directory para cada directorio de Azure AD.

- **Crear dominios con nombres personalizados:** puede crear dominios con nombres personalizados (por ejemplo, contoso.local) mediante Servicios de dominio de Azure AD. Esto incluye tanto nombres de dominio comprobados como no comprobados. Si lo desea, también puede crear un dominio con el sufijo de dominio integrado (es decir, *. onmicrosoft.com) que se ofrece a través del directorio de Azure AD.

- **Integración con Azure AD:** no es necesario configurar o administrar la replicación en Servicios de dominio de Azure AD. Las cuentas de usuario, las pertenencias a grupos y las credenciales de usuario (contraseñas) del directorio de Azure AD están disponibles automáticamente con Servicios de dominio de Azure AD. Los nuevos usuarios, grupos o cambios de atributos que se producen en el inquilino de Azure AD o en el directorio local se sincronizan automáticamente con Servicios de dominio de Azure AD.

- **Autenticación Kerberos y NTLM:** con compatibilidad para la autenticación Kerberos y NTLM, puede implementar las aplicaciones que dependen de la autenticación integrada de Windows.

- **Utilizar las credenciales y contraseñas corporativas:** las contraseñas para usuarios del inquilino de Azure AD funcionan con Servicios de dominio de Azure AD. Esto significa que los usuarios de la organización pueden usar sus credenciales corporativas en el dominio (para máquinas de unión a dominio, iniciando sesión de forma interactiva o a través de Escritorio remoto, autenticándose en el controlador de dominio, etc.).

- **Enlace LDAP y compatibilidad con lectura LDAP:** puede utilizar las aplicaciones que dependen de enlaces LDAP para autenticar usuarios en dominios ofrecidos por Servicios de dominio de Azure AD. Además, las aplicaciones que utilizan operaciones de lectura LDAP para consultar los atributos de usuario y equipo desde el directorio también pueden trabajar con Servicios de dominio de Azure AD.

- **Directiva de grupo:** puede aprovechar un único GPO integrado para los usuarios y contenedores de equipos para exigir el cumplimiento de directivas de seguridad necesarias para las cuentas de usuario, así como para los equipos unidos a dominios.

- **Disponible en varias regiones de Azure:** consulte la página de [servicios de Azure por región](https://azure.microsoft.com/regions/#services/) para conocer las regiones de Azure en las que están disponibles los Servicios de dominio de Azure AD.

- **Alta disponibilidad:** Servicios de dominio de Azure AD proporciona alta disponibilidad para el dominio. Esta funcionalidad ofrece la garantía de mayor tiempo de actividad de servicio y resistencia a errores. La supervisión de estado integrada ofrece corrección automatiza de errores poniendo en marcha nuevas instancias para reemplazar instancias con errores y proporcionar un servicio continuado para el dominio.

- **Usar herramientas de administración familiares:** puede utilizar herramientas de administración familiares de Windows Server Active Directory, como Centro de administración de Active Directory o Active Directory PowerShell, para administrar dominios proporcionados por Servicios de dominio de Azure AD.

<!---HONumber=AcomDC_0128_2016-->