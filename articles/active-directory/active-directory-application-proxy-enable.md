<properties
	pageTitle="Habilitación del proxy de la aplicación de Azure AD | Microsoft Azure"
	description="Explica cómo poner en funcionamiento el proxy de la aplicación de Azure AD."
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

# Habilitación del proxy de la aplicación de Azure AD
> [AZURE.NOTE] Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

Proxy de aplicación de Microsoft Azure Active Directory permite publicar aplicaciones, como sitios de SharePoint, Outlook Web Access y aplicaciones basadas en IIS, en la red privada, y ofrece un acceso seguro a los usuarios externos a la red. Los empleados pueden iniciar sesión en sus aplicaciones desde casa, en sus propios dispositivos, y autenticarse a través de este proxy basado en la nube.

El proxy de la aplicación funciona mediante la instalación de un pequeño servicio de Windows denominado Conector dentro de la red. El conector mantiene una conexión saliente desde dentro de la red al servicio de proxy. Cuando los usuarios tienen acceso a una aplicación publicada, el proxy usa esta conexión para ofrecer acceso a la aplicación.

Este artículo le guía a través de la habilitación del proxy de la aplicación de Microsoft Azure AD para el directorio en la nube en Azure AD, la instalación del conector del proxy de la aplicación en la red privada y el registro del conector con su suscripción de inquilino de Microsoft Azure AD.

## Requisitos previos del proxy de la aplicación
Antes de poder habilitar y usar los servicios del proxy de la aplicación, debe tener lo siguiente:

- Una [suscripción de nivel Básico o Premium](active-directory-editions.md) de Microsoft Azure AD y un directorio de Azure AD del cual es usted administrador global.
- Un servidor que ejecuta Windows Server 2012 R2 o Windows 8.1 o posterior en el que se puede instalar el conector del proxy de la aplicación. El servidor debe ser capaz de enviar solicitudes HTTPS a los servicios del proxy de la aplicación en la nube, y debe tener una conexión HTTPS a las aplicaciones que desea publicar.
- Si un servidor de seguridad se coloca en la ruta de acceso, asegúrese de que el firewall está abierto para permitir las solicitudes de HTTPS (TCP) que se originan desde el conector al proxy de la aplicación. El conector utiliza estos puertos junto con los subdominios que forman parte de los dominios de alto nivel: msappproxy.net y servicebus.windows.net. Asegúrese de abrir **todos** los siguientes puertos para el tráfico **saliente**:

Número de puerto | Descripción
--- | ---
80 | Para habilitar el tráfico HTTP saliente para la validación de seguridad.
443 | Para habilitar la autenticación de usuario en Azure AD (solo es necesario para el proceso de registro del conector).
10100 - 10120 | Para habilitar las respuestas LOB HTTP enviadas de nuevo hacia el servidor proxy.
9352, 5671 | Para habilitar la comunicación entre el conector hacia el servicio de Azure para las solicitudes entrantes.
9350 | Opcional, para habilitar un mejor rendimiento para las solicitudes entrantes.
8080 | Para habilitar la secuencia de arranque del conector y la actualización automática del conector.
9090 | Para habilitar el registro de conector (solo es necesario para el proceso de registro del conector).
9091 | Para habilitar la renovación automática de certificados de confianza del conector.

Si el firewall fuerza el tráfico según los usuarios que se originan, abra estos puertos para el tráfico procedente de los servicios de Windows que se ejecutan como un servicio de red. Además, asegúrese de habilitar el puerto 8080 para NT Authority\\System.


## Paso 1: Habilitar el proxy de la aplicación en Azure AD
1. Inicie sesión como administrador en el Portal de Azure clásico.
2. Vaya a Active Directory y seleccione el directorio en el que desea habilitar el proxy de la aplicación.
3. Haga clic en **Configurar**, desplácese hasta el proxy de la aplicación y cambie Habilitar servicios de proxy de la aplicación para este directorio a **Habilitado**.

	![Habilitación del proxy de la aplicación](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

4. Haga clic en **Descargar ahora** en la parte inferior de la pantalla. Esto le llevará a la página de descarga. Lea y acepte los términos de licencia y haga clic en **Descargar** para guardar el archivo de Windows Installer (.exe) para el conector del proxy de la aplicación.

## Paso 2: Instalar y registrar el conector
1. Ejecute `AADApplicationProxyConnectorInstaller.exe` en el servidor que ha preparado (vea arriba Requisitos previos del proxy de la aplicación).
2. Siga las instrucciones del asistente para instalar.
3. Durante la instalación se le pedirá que registre el conector con el proxy de la aplicación de su inquilino de Azure AD.

  - Proporcione sus credenciales de administrador global de Azure AD. Su inquilino de administrador global puede ser diferente de sus credenciales de Microsoft Azure.
  - Asegúrese de que el administrador que registra el conector está en el mismo directorio en que ha habilitado el servicio de proxy de la aplicación; por ejemplo, si el dominio del inquilino es contoso.com, el administrador debe ser admin@contoso.com o cualquier otro alias en ese dominio.
  - Si la configuración de seguridad mejorada de Internet Explorer está **Activada** en el servidor donde va a instalar el conector de Azure AD, podría bloquearse la pantalla de registro. Si esto ocurre, siga las instrucciones del mensaje de error para permitir el acceso. Asegúrese de que Internet Explorer Enhanced Security está desactivado.
  - Si el registro del conector no funciona, vea [Troubleshoot Application Proxy](active-directory-application-proxy-troubleshoot.md) (Solucionar problemas de Proxy de la aplicación).  

4. Cuando finalice la instalación, se agregan dos nuevos servicios a su servidor, como se muestra a continuación. Se trata del servicio del conector, que permite la conectividad, y un servicio de actualización automatizada, que comprueba periódicamente las nuevas versiones del conector y actualiza el conector según sea necesario. Haga clic en **Finalizar** en la ventana de instalación para completar la instalación.

	![Servicios de conector del proxy de la aplicación](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. Ahora está listo para [publicar aplicaciones con el proxy de la aplicación](active-directory-application-proxy-publish.md).

Si quiere disfrutar de una alta disponibilidad, debe implementar al menos un conector adicional. Para implementar un conector adicional, repita los pasos 2 y 3 explicados anteriormente. Cada conector debe estar registrado de manera independiente.

Si quiere desinstalar el conector, desinstale tanto el servicio de conector como el servicio de actualización, y después asegúrese de reiniciar el equipo para quitar completamente el servicio.


## Consulte también
Hay mucho más que puede hacer con el proxy de la aplicación:

- [Publicar aplicaciones con Proxy de aplicación](active-directory-application-proxy-publish.md)
- [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
- [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabajar con las aplicaciones para notificaciones](active-directory-application-proxy-claims-aware-apps.md)
- [Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)

## Obtenga más información acerca del proxy de la aplicación
- [Consulte el blog del proxy de la aplicación](http://blogs.technet.com/b/applicationproxyblog/)
- [Vea nuestros vídeos de Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Recursos adicionales
- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
- [Registro en Azure como una organización](sign-up-organization.md)
- [Identidad de Azure](fundamentals-identity.md)
- [Publicación de aplicaciones con el proxy de la aplicación](active-directory-application-proxy-publish.md)

<!---HONumber=AcomDC_0211_2016-->