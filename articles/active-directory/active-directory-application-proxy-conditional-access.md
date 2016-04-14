<properties
	pageTitle="Acceso condicional para aplicaciones publicadas con el proxy de la aplicación de Azure AD"
	description="Explica cómo configurar el acceso condicional para que se tenga acceso remoto con el proxy de la aplicación de Azure AD a las aplicaciones que se publiquen."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="kgremban"/>

# Uso de acceso condicional
> [AZURE.NOTE] Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

Ahora puede habilitar reglas de acceso para conceder acceso condicional a usuarios y grupos que accedan a las aplicaciones publicadas con el proxy de la aplicación. Esto le permite:

- Exigir la autenticación multifactor por aplicación
- Exigir la autenticación multifactor solo cuando los usuarios no están en el trabajo
- Impedir que los usuarios tengan acceso a la aplicación cuando no se encuentran en el trabajo

Estas reglas pueden aplicarse a todos los usuarios y grupos o solo a usuarios y grupos específicos. La regla se aplicará de forma predeterminada a todos los usuarios que tengan acceso a la aplicación. Sin embargo, también puede restringirse a usuarios que sean miembros de los grupos de seguridad especificados.

Cuando un usuario tiene acceso a una aplicación federada que usa OAuth 2.0, OpenID Connect, SAML o WS-Federation, se evalúan las reglas de acceso. Además, las reglas de acceso se evalúan con OAuth 2.0 y OpenID Connect cuando se usa un token de actualización para adquirir un token de acceso.

## Requisitos previos de acceso condicional

- Suscripción a Azure Active Directory Premium
- Un inquilino de Azure Active Directory administrado o federado
- Los inquilinos federados requieren que el servicio Multi-Factor Authentication (MFA) esté habilitado

![Configurar reglas de acceso - exigir Multi-Factor Authentication](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## Configuración de Multi-Factor Authentication por aplicación
1. Inicie sesión como administrador en el Portal de Azure clásico.
2. Vaya a Active Directory y seleccione el directorio en el que desea habilitar el proxy de la aplicación.
3. Haga clic en **Aplicaciones** y desplácese hacia abajo hasta la sección **Reglas de acceso**. La sección Reglas de acceso solo aparece para aplicaciones publicadas con el proxy de la aplicación que usa autenticación federada.
4. Habilite la regla seleccionando **Habilitar reglas de acceso ** como **Activada**.
5. Especifique los usuarios y grupos a los que se aplican las reglas. Use el botón **Agregar grupo** botón para seleccionar uno o más grupos a los que se aplicará la regla de acceso. Este cuadro de diálogo también sirve para quitar grupos seleccionados. Cuando las reglas se seleccionan para que se apliquen a grupos, las reglas de acceso solo se aplicarán a los usuarios que pertenezcan a uno de los grupos de seguridad especificados.  

  - Para excluir explícitamente grupos de seguridad de la regla, active **Excepto** y especifique uno o más grupos. No se requerirá a los usuarios que sean miembros de un grupo de la lista Excepto que realicen autenticación multifactor.  

  - Si un usuario se configuró con la característica de autenticación multifactor por usuario, esta configuración tendrá prioridad sobre las reglas de autenticación multifactor de la aplicación. Esto significa que un usuario configurado con Multi-Factor Authentication por usuario tendrá que realizar autenticación multifactor aunque esté exento de las reglas de autenticación multifactor de la aplicación. Obtenga más información sobre la [autenticación multifactor y la configuración por usuario](../multi-factor-authentication/multi-factor-authentication.md).

6. Seleccione la regla de acceso que quiere establecer:
	- **Requerir autenticación multifactor**: los usuarios a los que se apliquen reglas de acceso tendrán que llevar a cabo autenticación multifactor para tener acceso a la aplicación a la que se aplica la regla.
	- **Requerir autenticación multifactor fuera del trabajo**: los usuarios que intenten tener acceso a la aplicación desde una dirección IP de confianza no tendrán que realizar autenticación multifactor. Los intervalos de direcciones IP de confianza pueden configurarse en la página de configuración de Multi-Factor Authentication.
	- **Bloquear acceso cuando no está en trabajo**: los usuarios que intentan obtener acceso a la aplicación desde fuera de su red corporativa no podrán tener acceso a la aplicación.


## Configuración de MFA para servicios de federación
En el caso de los inquilinos federados, puede que Azure Active Directory o el servidor local de AD FS ejecute Multi-Factor Authentication (MFA). De forma predeterminada, MFA se producirá en cualquier página hospedada por Azure Active Directory. Para configurar MFA local, ejecute Windows PowerShell y use la propiedad –SupportsMFA para definir el módulo de Azure AD.

En el ejemplo siguiente se muestra cómo habilitar MFA local mediante el cmdlet [Set-MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) en el inquilino contoso.com: `Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

Además de establecer esta marca, la instancia de AD FS de inquilinos federados debe configurarse para llevar a cabo Multi-Factor Authentication. Siga las instrucciones que se indican en [Implementación local de Microsoft Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).


## Consulte también
Hay mucho más que puede hacer con el proxy de la aplicación:

- [Publicar aplicaciones con Proxy de aplicación](active-directory-application-proxy-publish.md)
- [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
- [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Trabajar con las aplicaciones para notificaciones](active-directory-application-proxy-claims-aware-apps.md)
- [Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)

## Obtenga más información acerca del proxy de la aplicación
- [Eche un vistazo para ver nuestra ayuda en línea](active-directory-application-proxy-enable.md)
- [Consulte el blog del proxy de la aplicación](http://blogs.technet.com/b/applicationproxyblog/)
- [Vea nuestros vídeos de Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)


## Recursos adicionales
- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
- [Registro en Azure como una organización](sign-up-organization.md)
- [Identidad de Azure](fundamentals-identity.md)

<!----HONumber=AcomDC_0211_2016-->