<properties
	pageTitle="Solución de problemas del proxy de aplicación | Microsoft Azure"
	description="Explica cómo solucionar errores en Proxy de aplicación de Azure AD."
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
	ms.date="01/06/2016"
	ms.author="kgremban"/>



# Solucionar problemas de Proxy de aplicación


> [AZURE.NOTE]Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

Si se producen errores al obtener acceso a una aplicación publicada o al publicar aplicaciones, compruebe las siguientes opciones para ver si Proxy de aplicación de Microsoft Azure AD funciona correctamente:

- Abra la consola de Servicios de Windows y compruebe que el servicio “conector del proxy de aplicación de Microsoft AAD” está habilitado y en ejecución. Puede que también desee consultar la página de propiedades del servicio Proxy de aplicación, como se muestra en la imagen siguiente:

  ![Captura de pantalla de la ventana de propiedades del conector del proxy de aplicación de Microsoft AAD](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)

- Abra el Visor de eventos y busque eventos relacionados con el conector del proxy de aplicación ubicado en **Registros de aplicaciones y servicios** > **Microsoft** > **AadApplicationProxy** > **Conector** > **Administrador**.
- Si es necesario, puede disponer de registros más detallados si activa los registros de análisis y depuración, así como el registro de sesiones del conector del proxy de aplicación.


## Errores generales

Error | Descripción | Resolución
--- | --- | ---
No se puede tener acceso a esta aplicación corporativa. No está autorizado para tener acceso a esta aplicación. Error de autorización. Asegúrese de asignar el usuario con acceso a esta aplicación. | Puede que no haya asignado el usuario para esta aplicación. | Vaya a la pestaña **Aplicación** y, en **Usuarios y grupos**, asigne este usuario o grupo de usuarios a esta aplicación.
No se puede tener acceso a esta aplicación corporativa. No está autorizado para tener acceso a esta aplicación. Error de autorización. Asegúrese de que el usuario tiene una licencia de Azure Active Directory Premium o Basic. | Es posible que su usuario reciba este error al intentar obtener acceso a la aplicación que publicó si el administrador del suscriptor no asignó explícitamente el usuario que intentó obtener acceso a la aplicación con una licencia Premium o Basic. | Vaya a la pestaña **Licencias** de Active Directory del suscriptor y asegúrese de que se asigne a este usuario o grupo de usuarios una licencia Premium o Básica.


## Solución de problemas del conector
Si se produce un error en el registro durante la instalación del asistente del conector, puede ver el motivo del error si busca en el registro de eventos en **Registros de Windows** > **Aplicación** o ejecuta el siguiente comando de Windows PowerShell.

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

| Error | Descripción | Resolución |
| --- | --- | --- |
| Error de registro del conector: asegúrese de haber habilitado Proxy de aplicación en el Portal de administración de Azure, así como de haber escrito correctamente su nombre de usuario y contraseña de Active Directory. Error: 'Se han producido uno o más errores'. | Cerró la ventana de registro sin iniciar sesión en Azure AD. | Vuelva a ejecutar el asistente del conector y regístrelo. |
| Error de registro del conector: asegúrese de haber habilitado Proxy de aplicación en el Portal de administración de Azure, así como de haber escrito correctamente su nombre de usuario y contraseña de Active Directory. Error: 'AADSTS50001: el recurso `https://proxy.cloudwebappproxy.net/registerapp` está deshabilitado'. | Proxy de aplicación está deshabilitado. | Asegúrese de habilitar Proxy de aplicación en el Portal de Azure clásico antes de intentar registrar el conector. Para obtener más información acerca de cómo habilitar Proxy de aplicación, consulte [Habilitar servicios de Proxy de aplicación](active-directory-application-proxy-enable.md). |
| Error de registro del conector: asegúrese de haber habilitado Proxy de aplicación en el Portal de administración de Azure, así como de haber escrito correctamente su nombre de usuario y contraseña de Active Directory. Error: 'Se han producido uno o más errores'. | Si se abre la ventana de registro y, a continuación, se cierra inmediatamente sin permitirle iniciar sesión, es probable que reciba este error. Este error se produce cuando hay algún tipo de error de red en su sistema. | Asegúrese de que es posible conectarse desde un explorador a un sitio web público y de que los puertos están abiertos como se especifica en [Requisitos previos del proxy de aplicación](active-directory-application-proxy-enable.md). |
| Error de registro del conector: asegúrese de que su equipo está conectado a Internet. Error: 'No había ningún extremo escuchando en `https://connector.msappproxy.net:9090/register/RegisterConnector` que pudiera aceptar el mensaje. La causa suele ser una dirección o una acción SOAP incorrecta. Consulte InnerException, si está presente, para obtener más información'. | Si inicia sesión con su nombre de usuario y contraseña de Azure AD y, a continuación, recibe este error, es posible que se bloqueen todos los puertos por encima de 8081. | Asegúrese de que los puertos necesarios están abiertos. Para obtener más información, consulte [Requisitos previos del proxy de aplicación](active-directory-application-proxy-enable.md). |
| Aparecerá un error de borrado en la ventana de registro. No se puede continuar, solo para cerrar la ventana | Escribió el nombre de usuario o contraseña incorrectos. | Inténtelo de nuevo. |
| Error de registro del conector: asegúrese de haber habilitado Proxy de aplicación en el Portal de administración de Azure, así como de haber escrito correctamente su nombre de usuario y contraseña de Active Directory. Error: ' AADSTS50059: No se ha producido ningún error en la información de identificación del inquilino en la solicitud o implícita en ninguna credencial proporcionada y búsqueda por URI de principio de servicio. | Intenta iniciar sesión con una cuenta Microsoft y no un dominio que forma parte del id. de organización del directorio al que intenta tener acceso. | Asegúrese de que el administrador forma parte del mismo nombre de dominio que el dominio del inquilino, por ejemplo, si el dominio de Azure AD es contoso.com, el administrador debe ser admin@contoso.com. |
| Error al recuperar la directiva de ejecución actual para ejecutar scripts de PowerShell | Si se produce un error en la instalación del conector, compruebe que la directiva de ejecución de PowerShell no está deshabilitada. | Abra el Editor de directivas de grupo. Vaya a Configuración del equipo > Plantillas administrativas > Componentes de Windows > Windows PowerShell y haga doble clic en **Activar la ejecución de scripts**. Esto se puede establecer en **No configurado** o **Habilitado**. Si se establece en **Habilitado**, asegúrese de que, en Opciones, la directiva de ejecución se establece en **Permitir scripts locales y scripts remotos firmados** o en **Permitir todos los scripts**. |
| El conector no pudo descargar la configuración | El certificado de cliente del conector, que se utiliza para la autenticación, ha expirado. Esto también puede ocurrir si tiene instalado el conector detrás de un proxy. En este caso, el conector no puede tener acceso a Internet ni tampoco podrá proporcionar aplicaciones a usuarios remotos. | Renueve la confianza manualmente mediante el cmdlet `Register-AppProxyConnector` en Windows PowerShell. Si su conector está detrás de un proxy, es necesario conceder acceso a Internet a las cuentas del conector "servicios de red" y "sistema local". Esto puede realizarse concediéndoles acceso al proxy o estableciéndoles para omitir el proxy. |
| Error de registro del conector: asegúrese de que es administrador global de su Active Directory para registrar el conector. Error: 'Se ha denegado la solicitud de registro'. | El alias con el que intenta iniciar sesión no es administrador en este dominio. Su conector siempre está instalado para el directorio que posee el dominio del usuario. | Asegúrese de que el administrador como el que intenta iniciar sesión tiene permisos globales para el inquilino de Azure AD.|


## Errores de Kerberos

| Error | Descripción | Resolución |
| --- | --- | --- |
| Error al recuperar la directiva de ejecución actual para ejecutar scripts de PowerShell. | Si se produce un error en la instalación del conector, compruebe que la directiva de ejecución de PowerShell no está deshabilitada. | Abra el Editor de directivas de grupo. Vaya a Configuración del equipo > Plantillas administrativas > Componentes de Windows > Windows PowerShell y haga doble clic en **Activar la ejecución de scripts**. Esto se puede establecer en **No configurado** o **Habilitado**. Si se establece en **Habilitado**, asegúrese de que, en Opciones, la directiva de ejecución se establece en **Permitir scripts locales y scripts remotos firmados** o en **Permitir todos los scripts**. |
| 12008: Azure AD superó el número máximo de intentos de autenticación Kerberos permitidos en el servidor backend. | Este evento puede indicar una configuración incorrecta entre Azure AD y el servidor de aplicaciones backend o un problema en la configuración de fecha y hora de ambos equipos. | El servidor backend rechazó el vale Kerberos creado por Azure AD. Compruebe que la configuración de Azure AD y el servidor de aplicaciones backend es correcta. Asegúrese de que la configuración de fecha y hora de Azure AD y el servidor de aplicaciones backend está sincronizada. |
| 13016: Azure AD no puede recuperar un vale Kerberos en nombre del usuario porque no hay ningún UPN en el token perimetral o en la cookie de acceso. | Hay un problema con la configuración de STS. | Corrija la configuración de notificación de UPN en STS. |
| 13019: Azure AD no puede recuperar un vale Kerberos en nombre del usuario debido al siguiente error general API. | Este evento puede indicar una configuración incorrecta entre Azure AD y el servidor controlador de dominios o un problema en la configuración de fecha y hora de ambos equipos. | El controlador de dominio rechazó el vale Kerberos creado por Azure AD. Compruebe que la configuración de Azure AD y el servidor de aplicaciones backend es correcta, especialmente la configuración de SPN. Asegúrese de que Azure AD está unido mediante dominio al mismo dominio que el controlador de dominio para garantizar que este establezca la confianza con Azure AD. Asegúrese de que la configuración de fecha y hora de Azure AD y el controlador de dominio está sincronizada. |
| 13020: Azure AD no puede recuperar un vale Kerberos en nombre del usuario al no estar definido el SPN del servidor backend. | Este evento puede indicar una configuración incorrecta entre Azure AD y el servidor controlador de dominios o un problema en la configuración de fecha y hora de ambos equipos. | El controlador de dominio rechazó el vale Kerberos creado por Azure AD. Compruebe que la configuración de Azure AD y el servidor de aplicaciones backend es correcta, especialmente la configuración de SPN. Asegúrese de que Azure AD está unido mediante dominio al mismo dominio que el controlador de dominio para garantizar que este establezca la confianza con Azure AD. Asegúrese de que la configuración de fecha y hora de Azure AD y el controlador de dominio está sincronizada. |
| 13022: Azure AD no puede autenticar el usuario porque el servidor backend responde a los intentos de autenticación Kerberos con un error HTTP 401. | Este evento puede indicar una configuración incorrecta entre Azure AD y el servidor de aplicaciones backend o un problema en la configuración de fecha y hora de ambos equipos. | El servidor backend rechazó el vale Kerberos creado por Azure AD. Compruebe que la configuración de Azure AD y el servidor de aplicaciones backend es correcta. Asegúrese de que la configuración de fecha y hora de Azure AD y el servidor de aplicaciones backend está sincronizada. |
| El sitio web no puede mostrar la página. | Es posible que su usuario reciba este error al intentar obtener acceso a la aplicación que publicó si la aplicación es una aplicación IWA. El SPN definido para esta aplicación puede ser incorrecto. | Para las aplicaciones IWA: asegúrese de que el SPN configurado para esta aplicación es correcto. |
| El sitio web no puede mostrar la página. | Es posible que su usuario reciba este error al intentar obtener acceso a la aplicación que publicó si la aplicación es una aplicación OWA. Esto podría deberse a uno de los motivos siguientes: <br> - El SPN definido para esta aplicación es incorrecto. <br> - El usuario que intentó obtener acceso a la aplicación usa una cuenta Microsoft en lugar de la cuenta corporativa adecuada para iniciar sesión, o bien el usuario es un usuario invitado. <br> - El usuario que intentó obtener acceso a la aplicación no está correctamente definido para esta aplicación a nivel local. | Los pasos para mitigar según corresponda: <br> -Asegúrese de que el SPN configurado para esta aplicación es correcto. <br> -Asegúrese de que el usuario inicia sesión con la cuenta corporativa que coincide con el dominio de la aplicación publicada. Los usuarios y el invitado de la Cuenta de Microsoft no pueden obtener acceso a las aplicaciones IWA. <br> - Asegúrese de que este usuario tiene los permisos adecuados como se define para esta aplicación backend en el equipo local. |
| No se puede tener acceso a esta aplicación corporativa. No está autorizado para tener acceso a esta aplicación. Error de autorización. Asegúrese de asignar el usuario con acceso a esta aplicación. | Es posible que su usuario reciba este error al intentar obtener acceso a la aplicación que publicó si el usuario que intentó obtener acceso a la aplicación usa una cuenta Microsoft en lugar de la cuenta corporativa adecuada para iniciar sesión, o bien el usuario es un usuario invitado. | Los usuarios y los invitados de la Cuenta Microsoft no pueden tener acceso a aplicaciones IWA. Asegúrese de que el usuario inicia sesión con la cuenta corporativa que coincide con el dominio de la aplicación publicada. |
| No se puede tener acceso a esta aplicación corporativa en este momento. Inténtelo de nuevo más tarde... Se agotó el tiempo de espera del conector. | Es posible que su usuario reciba este error al intentar obtener acceso a la aplicación que publicó si el usuario que intentó obtener acceso a la aplicación no está correctamente definido para esta aplicación a nivel local. | Asegúrese de que este usuario tiene los permisos adecuados como se define para esta aplicación backend en el equipo local. |


## Consulte también
Hay mucho más que puede hacer con el proxy de la aplicación:


- [Publicar aplicaciones con Proxy de aplicación](active-directory-application-proxy-publish.md)
- [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
- [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabajar con las aplicaciones para notificaciones](active-directory-application-proxy-claims-aware-apps.md)

## Obtenga más información acerca del proxy de la aplicación
- [Eche un vistazo para ver nuestra ayuda en línea](active-directory-application-proxy-enable.md)
- [Consulte el blog del proxy de la aplicación](http://blogs.technet.com/b/applicationproxyblog/)
- [Vea nuestros vídeos de Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png

<!---HONumber=AcomDC_0114_2016-->