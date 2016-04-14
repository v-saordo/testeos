<properties
	pageTitle="Trabajar con las aplicaciones para notificaciones en Proxy de aplicación"
	description="Explica cómo poner en funcionamiento el proxy de la aplicación de Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="kgremban"/>



# Trabajar con las aplicaciones para notificaciones en Proxy de aplicación

> [AZURE.IMPORTANT] Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

Las aplicaciones para notificaciones realizan una redirección al servicio de token de seguridad (STS), que a su vez solicita las credenciales del usuario a cambio de un token antes de redirigir al usuario a la aplicación. Para permitir que el proxy de aplicación funcione con estas redirecciones, deben realizarse los siguientes pasos.

## Requisito previo

Antes de realizar este procedimiento, asegúrese de que el STS al que redirige la aplicación para notificaciones está disponible fuera de la red local.


### Configuración del Portal de Azure clásico

1. Publique la aplicación según las instrucciones de [Publicar aplicaciones con el proxy de aplicación](active-directory-application-proxy-publish.md).
2. En la lista de aplicaciones, seleccione la aplicación para notificaciones y haga clic en **Configurar**.
3. Si elige **Acceso directo** como su **Método de autenticación previa**, asegúrese de seleccionar **HTTPS** como su esquema **Dirección URL externa**.
4. Si elige **Azure Active Directory** como **Método de autenticación previa**, seleccione **Ninguno** como **Método de autenticación interno**.


### Configuración de AD FS

1. Abra Administración de AD FS.
2. Vaya a **Relaciones de confianza para usuario autenticado**, haga clic con el botón derecho en la aplicación que va a publicar con el proxy de aplicación y seleccione **Propiedades**. ![Relaciones de confianza para usuario autenticado: haga clic con el botón derecho en el nombre de la aplicación - captura de pantalla](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. En la pestaña **Extremos**, en **Tipo de extremo**, seleccione **WS-Federation**.
4. En **Dirección URL de confianza**, escriba la dirección URL que indicó en Proxy de aplicación en **Dirección URL externa** y haga clic en **Aceptar**. ![Agregar un extremo: establezca el valor de Dirección URL de confianza - captura de pantalla](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## Consulte también
Hay mucho más que puede hacer con el proxy de la aplicación:

- [Publicar aplicaciones con Proxy de aplicación](active-directory-application-proxy-publish.md)
- [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
- [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)

## Obtenga más información acerca del proxy de la aplicación
- [Eche un vistazo para ver nuestra ayuda en línea](active-directory-application-proxy-enable.md)
- [Consulte el blog del proxy de la aplicación](http://blogs.technet.com/b/applicationproxyblog/)
- [Vea nuestros vídeos de Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Recursos adicionales

- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
- [Provisión de acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md)
- [Habilitación de las aplicaciones cliente nativas para interactuar con el proxy de la aplicación](active-directory-application-proxy-native-client.md)
- [Registro en Azure como una organización](sign-up-organization.md)
- [Identidad de Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_0211_2016-->