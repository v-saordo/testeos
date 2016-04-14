<properties
	pageTitle="Inicio de sesión único con el proxy de aplicación | Microsoft Azure"
	description="Explica cómo proporcionar el inicio de sesión único mediante el Proxy de aplicación de Azure AD."
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


# Inicio de sesión único con el Proxy de aplicación

> [AZURE.NOTE] Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

El inicio de sesión único es un elemento clave de Proxy de aplicación de Azure AD. Proporciona la mejor experiencia de usuario: un usuario inicia sesión en la nube, todas las validaciones de seguridad se realizan en la nube (autenticación previa) y después, cuando la solicitud se envía a la aplicación local, el conector de Proxy de aplicación suplanta al usuario de forma que la aplicación de back-end piensa que se trata de un usuario normal procedente de un dispositivo unido al dominio.

![Diagrama de acceso desde el usuario final, a través del Proxy de aplicación, a la red corporativa](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_diagram.png)

Proxy de aplicación de Azure AD permite proporcionar una experiencia de inicio de sesión único (SSO) para los usuarios. Use las siguientes instrucciones para publicar las aplicaciones mediante SSO:


## SSO para aplicaciones IWA locales mediante KCD con Proxy de aplicación
Puede habilitar el inicio de sesión único a sus aplicaciones mediante la autenticación de Windows integrada (IWA) dando permiso a los conectores del proxy de aplicación en Active Directory para suplantar a los usuarios y enviar y recibir tokens en su nombre.


### Diagrama de red

![Diagrama de flujos de autenticación de Microsoft AAD](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

En este diagrama se explica el flujo cuando un usuario intenta tener acceso a una aplicación local que usa IWA. El flujo general es:

1. El usuario escribe la dirección URL para tener acceso a la aplicación local a través de Proxy de aplicación.
2. Proxy de aplicación redirige la solicitud a los servicios de autenticación de Azure AD para realizar la autenticación previa. En este momento, Azure AD aplica cualquier autenticación correspondiente, así como directivas de autorización, como la autenticación multifactor. Si se valida el usuario, Azure AD crea un token y lo envía al usuario.
3. El usuario pasa el token a Proxy de aplicación.
4. Proxy de aplicación valida el token y recupera del mismo el nombre principal del usuario (UPN), enviando a continuación la solicitud, el UPN y el nombre de entidad de seguridad de servicio (SPN) al conector a través de un canal seguro doblemente autenticado.
5. El conector realiza la negociación de la delegación limitada de Kerberos (KCD) con AD local, suplantando al usuario para obtener un token de Kerberos para la aplicación.
6. Active Directory envía el token de Kerberos para la aplicación al conector.
7. El conector envía la solicitud original al servidor de aplicaciones, con el token de Kerberos que recibió de AD.
8. La aplicación envía la respuesta al conector que, a continuación, se devuelve al servicio Proxy de aplicación y, finalmente, al usuario.

### Requisitos previos

1. Asegúrese de que sus aplicaciones, como las aplicaciones web de SharePoint, se establecen para usar la autenticación de Windows integrada. Para obtener más información, consulte [Habilitar la compatibilidad con la autenticación Kerberos](https://technet.microsoft.com/library/dd759186.aspx) o, para SharePoint, consulte [Planear la autenticación Kerberos en SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
2. Cree nombres de entidad de seguridad de servicio para sus aplicaciones.
3. Asegúrese de que el servidor que ejecuta el conector y el servidor que ejecuta la aplicación que va a publicar se unen mediante dominio y forman parte del mismo dominio. Para obtener más información sobre la unión a un dominio, consulte [Unir un equipo a un dominio](https://technet.microsoft.com/library/dd807102.aspx).


### Configuración de Active Directory

La configuración de Active Directory varía, dependiendo de si su conector del proxy de aplicación y el servidor publicado están en el mismo dominio o no.

#### Conector y servidor publicado en el mismo dominio

1. En Active Directory, vaya a **Herramientas** > **Usuarios y equipos**.
2. Seleccione el servidor que ejecuta el conector.
3. Haga clic con el botón derecho y seleccione **Propiedades** > **Delegación**.
4. Seleccione **Confiar en este equipo para la delegación solo a los servicios especificados** y, en **Servicios a los que esta cuenta puede presentar credenciales delegadas**, agregue el valor de la identidad del SPN del servidor de aplicaciones.
5. Esto permite al conector del proxy de aplicación suplantar a los usuarios en AD en las aplicaciones definidas en la lista.

![Captura de pantalla de ventana Conector-Propiedades SVR](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

#### Conector y servidor publicado en dominios distintos

1. Para obtener una lista de requisitos previos para trabajar con KCD entre dominios, vea [Delegación limitada Kerberos entre dominios](https://technet.microsoft.com/library/hh831477.aspx).
2. En Windows 2012 R2, utilice la propiedad `principalsallowedtodelegateto` en el servidor de Conector para permitir a Proxy de aplicación delegar para el servidor de Conector, donde el servidor publicado es `sharepointserviceaccount` y, el servidor de delegación, `connectormachineaccount`.

		$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

		Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

		Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE] `sharepointserviceaccount` puede ser la cuenta del equipo SPS o una cuenta de servicio con la que se ejecuta el grupo de aplicaciones SPS.


### Configuración del Portal de Azure clásico

1. Publique la aplicación según las instrucciones de [Publicar aplicaciones con el proxy de aplicación](active-directory-application-proxy-publish.md). Asegúrese de seleccionar **Azure Active Directory** como **Método de autenticación previa**.
2. Cuando su aplicación aparezca en la lista de aplicaciones, selecciónela y haga clic en **Configurar**.
3. En **Propiedades**, establezca **Método de autenticación interno** en **Autenticación integrada de Windows**. ![Configuración avanzada de aplicaciones](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)  
4. Escriba el **SPN de la aplicación interno** del servidor de aplicaciones. En este ejemplo, el SPN para nuestra aplicación publicada es http/lob.contoso.com.  

>[AZURE.IMPORTANT] Los UPN en Azure Active Directory deben ser idénticos a los UPN en su Active Directory local para que funcione la autenticación previa. Asegúrese de que su Azure AD se sincroniza con su AD local.

| | |
| --- | --- |
| Método de autenticación interno | Si utiliza Azure AD para la autenticación previa, puede establecer un método de autenticación interno para permitir a los usuarios beneficiarse de inicio de sesión único (SSO) para esta aplicación. <br><br> Seleccione **Autenticación integrada de Windows** (IWA) si su aplicación usa IWA y puede configurar la delegación limitada de Kerberos (KCD) para habilitar SSO en esta aplicación. Las aplicaciones que usan IWA deben configurarse mediante KCD; en caso contrario, Proxy de aplicación no podrá publicar estas aplicaciones. <br><br> Seleccione **Ninguno** si la aplicación no usa IWA. |
| SPN de la aplicación interno | Este es el nombre principal del servicio (SPN) de la aplicación interna como está configurado en Azure AD local. El conector del proxy de aplicación utiliza el SPN para obtener los tokens de Kerberos para la aplicación que usa KCD. |


## SSO para las aplicaciones no son de Windows
El flujo de la delegación de Kerberos del Proxy de aplicación de Azure AD se inicia cuando Azure AD autentica al usuario en la nube. Una vez que la solicitud se recibe en local, el conector de Proxy de aplicación de Azure AD emite un vale Kerberos en nombre del usuario mediante la interacción con Active Directory local. Este proceso se conoce como Delegación limitada de Kerberos (KCD). En la fase siguiente, se envía una solicitud a la aplicación back-end con este vale Kerberos. Existen números de protocolo que definen cómo enviar estas solicitudes. La mayoría de los servidores que no son Windows esperan Negotiate/SPNego, que ahora se admite en el proxy de aplicación de Azure AD.

![Diagrama de SSO que no es de Windows](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_nonwindows_diagram.png)

### Identidad delegada parcial
Las aplicaciones que no son de Windows suelen obtener la identidad del usuario en forma de un nombre de usuario o un nombre de cuenta SAM, no como una dirección de correo electrónico (username@domain). Esto es diferente de la mayoría de los sistemas basados en Windows, que prefieren un UPN que es más concluyente y garantiza que no haya ninguna duplicación entre dominios.

Por este motivo, el Proxy de aplicación permite seleccionar qué identidad aparece en el vale Kerberos, por aplicación. Algunas de estas opciones son adecuadas para los sistemas que no aceptan el formato de dirección de correo electrónico.

![Captura de pantalla de parámetro de identidad de inicio de sesión delegado](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

Si se usa la identidad parcial, y esta identidad pudiera no ser única para todos los dominios o bosques de la organización, puede que le interese publicar estas aplicaciones dos veces usando dos grupos distintos de conectores. Puesto que cada aplicación tiene una audiencia de usuarios diferente, puede unir sus conectores a un dominio diferente.


## Trabajar con SSO cuando las identidades locales y en la nube no son idénticas
A menos que se configuren de otra manera, el Proxy de aplicación supone que los usuarios tienen exactamente la misma identidad en la nube y en local. Para cada aplicación, puede configurar qué identidad se debe usar al realizar el inicio de sesión único.

Esta funcionalidad permite a muchas organizaciones que tienen identidades locales y en la nube diferentes disponer de SSO en aplicaciones locales desde aplicaciones en la nube sin que los usuarios tengan que escribir nombres de usuario y contraseñas diferentes. Esto incluye las organizaciones que:

- Tienen varios dominios internamente (joe@us.contoso.com, joe@eu.contoso.com) y un único dominio en la nube (joe@contoso.com).

- Tienen un nombre de dominio no enrutable internamente (joe@contoso.usa) y uno legal en la nube.

- No utilizan nombres de dominio internamente (joe)

- Usan distintos alias locales y en la nube. Por ejemplo, joe-johns@contoso.com frente a joej@contoso.com.

También le ayudará con aplicaciones que no aceptan direcciones en forma de dirección de correo electrónico, que es un escenario muy común en servidores back-end que no son Windows.


### Configuración de SSO para diferentes identidades en la nube y locales diferentes

1. Defina la configuración de Azure AD Connect para que la identidad principal sea la dirección de correo electrónico (correo). Esto se realiza como parte del proceso de personalización, cambiando el campo **Nombre principal del usuario** en la configuración de sincronización. Tenga en cuenta que esta configuración también determina cómo los usuarios inician sesión en dispositivos de Office365, Windows10 y otras aplicaciones que usan Azure AD como su almacén de identidades. ![Captura de pantalla de la identificación de usuarios - lista desplegable de Nombre principal de usuario](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. En las opciones de Configuración de la aplicación correspondientes a la aplicación que quiere modificar, seleccione la información en **Identidad de inicio de sesión delegada** que quiere usar:
  - Nombre principal del usuario: joe@contoso.com  
  - Nombre principal del usuario alternativo: joed@contoso.local  
  - Parte del nombre de usuario del nombre principal del usuario: joe  
  - Parte del nombre de usuario del nombre principal del usuario alternativo: joe  
  - Nombre de cuenta SAM local: depende de la configuración de controlador de dominio local.

  ![Captura de pantalla del menú desplegable de identidad de inicio de sesión delegada](./media/active-directory-application-proxy-sso-using-kcd/app_proxy_sso_diff_id_upn.png)

### Solución de problemas de SSO para diferentes identidades
Si se produce un error en el proceso SSO, aparecerá en el registro de eventos de la máquina del conector como se explica en [Solución de problemas](active-directory-application-proxy-troubleshoot.md). Pero, en algunos casos, la solicitud se enviará correctamente a la aplicación back-end mientras que esta aplicación responderá en otras diferentes respuestas HTTP. La solución de problemas de estos casos debe empezar examinando el número de evento 24029 en la máquina del conector en el registro de eventos de sesión del Proxy de aplicación. La identidad del usuario que se usó para la delegación aparecerá en el campo “usuario” dentro de los detalles del evento. Para activar el registro de sesión, seleccione **Mostrar registros de análisis y depuración** en el menú de vista del Visor de eventos.


## Consulte también
Hay mucho más que puede hacer con el proxy de la aplicación:


- [Publicar aplicaciones con Proxy de aplicación](active-directory-application-proxy-publish.md)
- [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
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


<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg

<!---HONumber=AcomDC_0211_2016-->