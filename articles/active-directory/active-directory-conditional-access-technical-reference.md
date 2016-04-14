
<properties
	pageTitle="Referencia técnica: acceso condicional a aplicaciones de Azure AD | Microsoft Azure"
	description="Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que se eligen al autenticar al usuario y antes de permitirle acceso a la aplicación. Si se cumplen las condiciones, el usuario queda autenticado y se le permite el acceso a la aplicación."
    services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="02/11/2016"
	ms.author="femila"/>

# Referencia técnica: acceso condicional a aplicaciones de Azure AD

## Servicios habilitados con acceso condicional
Las reglas de acceso condicional se admiten en varios tipos de aplicaciones de Azure AD. Esta lista incluye:

- Aplicaciones federadas de la Galería de aplicaciones de Azure AD
- Aplicaciones de inicio de sesión único con contraseña de la Galería de aplicaciones de Azure AD
- Aplicaciones registradas con el proxy de aplicación de Azure
- Aplicaciones desarrolladas de línea de negocio y multiinquilino registradas con Azure AD
- Visual Studio Online
- Azure RemoteApp

## Habilitación de reglas de acceso

Cada regla puede habilitarse o deshabilitarse en función de la aplicación. Cuando las reglas están 'ACTIVADAS', se habilitarán y se exigirán a los usuarios que acceden a la aplicación. Cuando están 'DESACTIVADAS', no se utilizarán ni afectarán a la experiencia de inicio de sesión de los usuarios.

## Aplicación de reglas a usuarios específicos
Las reglas se pueden aplicar a conjuntos específicos de usuarios basados en un grupo de seguridad mediante la configuración de 'Aplicar a'. 'Aplicar a' puede establecerse en 'Todos los usuarios' o 'Grupos. Cuando esté establecido en 'Todos los usuarios', las reglas se aplicarán a cualquier usuario con acceso a la aplicación. La opción 'Grupos' permite seleccionar grupos de seguridad y distribución específicos; las reglas solo se aplicarán a estos grupos. Al implementar por primera vez una regla, se suele aplicar primero a un conjunto limitado de usuarios, que sean miembros de grupos de proyectos piloto. Una vez finalizada la regla, se puede aplicar a "Todos los usuarios"; esto hará que la regla se pueda exigir a todos los usuarios de la organización. Asimismo, se pueden excluir de la directiva grupos seleccionados mediante la opción 'Excepto'. Aunque aparezcan en un grupo incluido, todos los miembros de estos grupos quedarán excluidos.

## Redes "en el trabajo"

Las reglas de acceso condicional que utilizan una red "en el trabajo" se basan en intervalos IP de confianza que se han configurado en Azure AD. Estas reglas incluyen:

- Requerir autenticación multifactor fuera del trabajo
- Bloquear el acceso fuera del trabajo

Los intervalos IP de confianza se pueden configurar en la [página de configuración de autenticación multifactor](../multi-factor-authentication/multi-factor-authentication-whats-next.md). La directiva de acceso condicional usará los intervalos configurados en cada solicitud de autenticación y emisión de tokens para evaluar las reglas. La notificación de la red corporativa interna no se utiliza, ya que no está disponible para sesiones en directo más largas, como tokens de actualización en aplicaciones móviles.

## Reglas por aplicación
Las reglas se configuran por aplicación, lo que permite a los servicios de alto valor estar protegidos sin que se vea afectado el acceso a otros servicios. Se pueden configurar reglas de acceso condicional en la pestaña 'Configurar' de la aplicación.

Las reglas que se ofrecen actualmente son:

- Requerir autenticación multifactor
 - Todos los usuarios a los que se aplica la directiva deberán haber realizado la autenticación multifactor al menos una vez.
- Requerir autenticación multifactor fuera del trabajo
 - Todos los usuarios a los que se aplica la directiva deberán haber realizado la autenticación multifactor al menos una vez si acceden al servicio desde una ubicación remota. Si pasan de un trabajo a una ubicación remota, se les exigirá que realicen la autenticación multifactor cuando accedan al servicio.
- Bloquear el acceso fuera del trabajo 
 - Se bloqueará el acceso a todos los usuarios a los que se aplica la directiva cuando accedan al servicio desde una ubicación remota. Si pasan de un trabajo a una ubicación remota, se les permitirá el acceso cuando estén en una ubicación de trabajo.

<!---HONumber=AcomDC_0218_2016-->