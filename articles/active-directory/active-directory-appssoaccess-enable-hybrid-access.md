<properties
	pageTitle="Habilitación del acceso híbrido con el proxy de aplicación| Microsoft Azure"
	description="Habilite el acceso a aplicaciones que se ejecutan dentro de su red privada desde fuera de la red mediante Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	keywords="acceso a la aplicación, Proxy de aplicación, acceso a híbrido"
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/10/2016"
	ms.author="femila"/>

# Habilitación del acceso híbrido con el proxy de aplicación
Con el Proxy de la aplicación de Microsoft Azure Active Directory (AD), puede ofrecer acceso a las aplicaciones que se encuentran dentro de la red privada de manera segura, desde cualquier lugar y en cualquier dispositivo. Cuando haya instalado un conector de proxy de aplicación dentro de su entorno, se podrá configurar con facilidad con Azure AD.

Solo existe un requisito para habilitar el acceso a una aplicación web: la aplicación web debe ser accesible para el servidor donde esté instalado el conector. Es decir, puede instalar el conector en el propio servidor de aplicaciones o en cualquier otro servidor del entorno siempre que el conector pueda obtener acceso a la aplicación web.

##Cómo funciona
### Descripción general
1. Los conectores se implementan en la red local. (Se pueden implementar varios conectores para mejorar la redundancia y escalabilidad).
2. El conector se conecta al servicio en la nube.
3. El conector y el servicio en la nube enrutan el tráfico de usuario a las aplicaciones.

 ![Diagrama del proxy de aplicación de Azure AD](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

### Un examen más profundo
1. El usuario obtiene acceso a la aplicación a través del proxy de aplicación y se le dirige a la página de inicio de sesión de Azure AD para que se autentique.
2. Después de iniciar sesión correctamente, se genera un token y se envía al usuario.
3. El usuario envía el token al proxy de aplicación, que recupera el nombre principal de usuario (UPN) y el nombre de entidad de seguridad (SPN) del token y dirige la solicitud al conector.
4. En nombre del usuario, el conector solicita un vale Kerberos que se puede usar para la autenticación interna (Windows). Esto se conoce como delegación limitada de Kerberos.
5. Se recupera un vale Kerberos de Active Directory.
6. El vale se envía al servidor de aplicaciones y se comprueba.
7. La respuesta se envía a través del proxy de aplicación al usuario.

## Artículos relacionados
- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
- [Habilitación del proxy de la aplicación de Azure AD](active-directory-application-proxy-enable.md#step-1-enable-application-proxy-in-azure-ad)
- [Publicación de aplicaciones a través del proxy de aplicación de Azure AD](active-directory-application-proxy-publish.md)

<!---HONumber=AcomDC_0218_2016-->