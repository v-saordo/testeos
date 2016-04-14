<properties
	pageTitle="Publicación de aplicaciones con el proxy de la aplicación de Azure AD | Microsoft Azure"
	description="Describe cómo publicar aplicaciones locales mediante el Proxy de aplicación de Azure AD."
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


# Publicación de aplicaciones mediante el proxy de aplicación de Azure AD

> [AZURE.NOTE] Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

Después de habilitar el Proxy de aplicación de Microsoft Azure Active Directory (AD), puede publicar las aplicaciones para que los usuarios de fuera de la red privada puedan tener acceso a ellas.

Este artículo le guiará por los pasos para publicar aplicaciones que se ejecutan en la red local para las que desea habilitar el acceso remoto seguro desde fuera de la red.

> [AZURE.NOTE] Para comprobar que el conector funciona correctamente, la primera aplicación que publique debe ser cualquier sitio accesible desde dentro de su red privada, para tener la seguridad de que los usuarios pueden tener acceso a él desde Internet, antes de publicar una aplicación real.


## Publicación de una aplicación mediante el Asistente

1. Abra el explorador de su preferencia y vaya al Portal de Azure clásico.
2. En el panel izquierdo del Portal de Azure clásico, haga clic en la pestaña **Active Directory**.
3. Haga clic en el directorio en el que ha habilitado el Proxy de aplicación y para el que desea publicar una aplicación (por ejemplo, Wingtip Toys).
4. Haga clic en la pestaña **Aplicaciones** y después haga clic en el botón **Agregar** situado en la parte inferior de la pantalla.

	![Agregar aplicación](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Publicar una aplicación que será accesible desde fuera de la red**.

	![Nueva aplicación que será accesible desde fuera de la red](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

6. Siga las instrucciones que aparecen en pantalla para facilitar la información siguiente sobre su aplicación:

| **Propiedad** | **Detalles** |
|---|---|
| URL externa | Esta es la dirección URL del servicio en la nube que se utiliza para tener acceso a la aplicación desde fuera de la red privada. La dirección URL se genera automáticamente a partir del nombre que haya facilitado y el sufijo msappproxy.net. |
| Método de autenticación previa | Establezca el tipo de método de autenticación previa que quiera que use la aplicación: <br><br> a. Azure Active Directory: cuando un usuario intente tener acceso a una aplicación, Proxy de aplicación lo redirigirá para que inicie sesión con Azure AD, lo que autenticará al usuario y garantizará que tenga los permisos necesarios para el directorio y la aplicación. <br><br> b. Acceso directo: no se realiza la autenticación previa. |
| Protocolo URL externo | De manera predeterminada, las aplicaciones se publican usando el protocolo HTTPS. El servicio redireccionará automáticamente a los usuarios que escriban la dirección URL con http. <br><br> Para habilitar HTTP para una aplicación interna, debe establecer el método de autenticación previa en Acceso directo y entonces podrá cambiar el Protocolo URL externo de HTTPS a HTTP. Tenga en cuenta que publicar aplicaciones mediante el uso de HTTP puede crear problemas de seguridad para la aplicación y sus usuarios. <br><br> Puede insertar un dominio personalizado en lugar de usar el sufijo predeterminado msappproxy.net. Para más información, vea [Working with custom domains](active-directory-application-proxy-custom-domains.md) (Trabajo con dominios personalizados). |
| Dirección URL interna | Se trata de la dirección URL interna que usa el conector de Proxy de aplicación para obtener acceso a la aplicación internamente. Debe ser la dirección URL de la aplicación publicada que se utiliza para tener acceso a la aplicación desde dentro de la red privada. Se trata de una dirección URL válida sin espacios ni símbolos. <br><br> Puede especificar una ruta de acceso específica en el servidor back-end para publicar, mientras que el resto del servidor no se publica. Esto permite publicar, por ejemplo, diferentes sitios ubicados en el mismo servidor de SharePoint con reglas de acceso y nombres diferentes. <br><br> La ruta de acceso se especifica en el campo de dirección URL interna y será visible en la dirección URL externa. Las rutas de acceso internas y externas deben ser idénticas. |

  ![Propiedades de la aplicación](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)

  Para finalizar al asistente, haga clic en la marca de verificación de la parte inferior de la pantalla. La aplicación ya está definida en Azure AD.


## Asignación de usuarios y grupos a la aplicación

1. Para las aplicaciones con autenticación previa, debe asignar los usuarios y grupos que tendrán acceso a la aplicación. Para las aplicaciones que son de acceso directo, el acceso está disponible para todos los usuarios. Sin embargo, para que el usuario vea la aplicación en su lista de aplicaciones, debe asignar la aplicación a ese usuario.

2. Tras finalizar el Asistente para agregar aplicaciones, se muestra la página Inicio rápido de Proxy de aplicación. Para asignar los usuarios, haga clic en **Asignar usuarios**.

	![Pantalla de inicio rápido de Proxy de aplicación -- asignar usuarios](./media/active-directory-application-proxy-publish/quickstart.png)

3. Seleccione cada usuario o grupo que desea asignar a esta aplicación y haga clic en **Asignar**.

> [AZURE.NOTE] Para las aplicaciones de Autenticación de Windows integrada, puede asignar solo a los usuarios y grupos que se sincronizan desde Active Directory local. A los usuarios que inicien sesión con una cuenta de Microsoft y a los invitados no se les pueden asignar aplicaciones publicadas con Proxy de aplicación de Azure Active Directory. Asegúrese de que los usuarios que asigne inician sesión con sus credenciales en el mismo dominio que la aplicación que está publicando.


## Configuración avanzada

1. Puede modificar las aplicaciones publicadas o configurar opciones avanzadas, como SSO en aplicaciones locales, desde la página Configurar.

	![Configuración avanzada](./media/active-directory-application-proxy-publish/advancedconfig.png)

2. Seleccione la aplicación y haga clic en **Configurar**. Están disponibles las siguientes opciones:

**Configuración** | **Detalles**
---|---
Nombre | Escriba un nombre descriptivo para la aplicación
URL externa | Esta es la dirección URL del servicio en la nube que se utiliza para tener acceso a la aplicación desde fuera de la red privada. La dirección URL se genera automáticamente a partir del nombre que haya facilitado y el sufijo msappproxy.net.
Método de autenticación previa | Establezca el tipo de método de autenticación previa que quiera que use la aplicación: <br><br> a. Azure Active Directory: cuando un usuario intente tener acceso a una aplicación, Proxy de aplicación lo redirigirá para que inicie sesión con Azure AD, lo que autenticará al usuario y garantizará que tenga los permisos necesarios para el directorio y la aplicación. <br><br> b. Acceso directo: no se realiza la autenticación previa.
Protocolo URL externo | De manera predeterminada, las aplicaciones se publican usando el protocolo HTTPS. El servicio redireccionará automáticamente a los usuarios que escriban la dirección URL con http. <br><br> Para habilitar HTTP para una aplicación interna, debe establecer el método de autenticación previa en Acceso directo y entonces podrá cambiar el Protocolo URL externo de HTTPS a HTTP. Tenga en cuenta que publicar aplicaciones mediante el uso de HTTP puede crear problemas de seguridad para la aplicación y sus usuarios. <br><br> Puede insertar un dominio personalizado en lugar de usar el sufijo predeterminado msappproxy.net. Para más información, vea [Working with custom domains](active-directory-application-proxy-custom-domains.md) (Trabajo con dominios personalizados).
Dirección URL interna | Se trata de la dirección URL interna que usa el conector de Proxy de aplicación para obtener acceso a la aplicación internamente. Debe ser la dirección URL de la aplicación publicada que se utiliza para tener acceso a la aplicación desde dentro de la red privada. Se trata de una dirección URL válida sin espacios ni símbolos. <br><br> Puede especificar una ruta de acceso específica en el servidor back-end para publicar, mientras que el resto del servidor no se publica. Esto permite publicar, por ejemplo, diferentes sitios ubicados en el mismo servidor de SharePoint con reglas de acceso y nombres diferentes. <br><br> La ruta de acceso se especifica en el campo de dirección URL interna y será visible en la dirección URL externa. Las rutas de acceso internas y externas deben ser idénticas.
Traducir URL en encabezados | Para las aplicaciones (como algunas configuraciones de SharePoint) que requieren que los encabezados de host HTTP no se traduzcan, establezca este parámetro en **No**. Así se deshabilitará la traducción de los encabezados de solicitud y respuesta.
Método de autenticación interno | Si usa el Proxy de aplicación para la autenticación previa, puede establecer un método de autenticación interno para permitir a los usuarios beneficiarse de inicio de sesión único (SSO) para esta aplicación. <br><br> Seleccione **Autenticación integrada de Windows** (IWA) si su aplicación usa IWA y puede configurar la delegación limitada de Kerberos (KCD) para habilitar SSO en esta aplicación. Las aplicaciones que usan IWA deben configurarse mediante KCD; en caso contrario, Proxy de aplicación no podrá publicar estas aplicaciones. <br><br> Seleccione **Ninguno** si la aplicación no usa IWA. <br><br> Para más información, vea [Inicio de sesión único con contraseña](active-directory-application-proxy-sso-using-kcd.md).
SPN de la aplicación interno | Este es el nombre principal de servicio (SPN) de la aplicación interna como está configurado en el Proxy de aplicación local. El conector del Proxy de aplicación utiliza el SPN para captar los tokens de Kerberos para las aplicaciones que usan la delegación limitada de Kerberos (KCD). <br><br> Para más información, vea [Enable single-sign on](active-directory-application-proxy-sso-using-kcd.md) (Habilitación del inicio de sesión único).

Después de publicar las aplicaciones mediante el Proxy de aplicación de Azure Active Directory, estas aparecen en la lista de aplicaciones en Azure AD y se pueden administrar desde ahí.

Si deshabilita los servicios de Proxy de aplicación después de publicar aplicaciones, estas no se eliminan, pero ya no estarán accesibles desde fuera de la red privada.

Para ver una aplicación y asegurarse de que es accesible, haga doble clic en el nombre de la aplicación. Si se deshabilita el servicio de Proxy de aplicación y la aplicación no está disponible, aparece un mensaje de advertencia en la parte superior de la pantalla.

Para eliminar una aplicación, seleccione una aplicación en la lista y después haga clic en **Eliminar**.

## Consulte también
Hay mucho más que puede hacer con el proxy de la aplicación:

- [Habilitación del proxy de la aplicación](active-directory-application-proxy-enable.md)
- [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
- [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)
- [Trabajar con las aplicaciones para notificaciones](active-directory-application-proxy-claims-aware-apps.md)
- [Solucionar los problemas que tiene con el Proxy de aplicación](active-directory-application-proxy-troubleshoot.md)

## Obtenga más información acerca del proxy de la aplicación
- [Eche un vistazo para ver nuestra ayuda en línea](active-directory-application-proxy-enable.md)
- [Consulte el blog del proxy de la aplicación](http://blogs.technet.com/b/applicationproxyblog/)
- [Vea nuestros vídeos de Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Recursos adicionales
- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
- [Sobre la delegación limitada de Kerberos](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=AcomDC_0211_2016-->