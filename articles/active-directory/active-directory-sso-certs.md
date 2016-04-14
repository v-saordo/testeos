<properties
	pageTitle="Administración de certificados de federación en AD de Azure | Microsoft Azure"
	description="Aprenda a personalizar la fecha de expiración de los certificados de federación y a renovar certificados que expiran pronto."
	services="active-directory"
	documentationCenter=""
	authors="liviodlc"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="liviodlc"/>

#Administración de certificados para inicio de sesión único federado en Azure Active Directory

Este artículo aborda preguntas comunes relacionadas con los certificados que crea Azure Active Directory para establecer el inicio de sesión único federado (SSO) para las aplicaciones SaaS.

Este artículo solo es relevante para las aplicaciones que están configuradas para usar **Inicio de sesión único de Azure AD **, como se muestra en el ejemplo siguiente:

![Inicio de sesión único de Azure AD](./media/active-directory-sso-certs/fed-sso.PNG)

##Personalización de la fecha de expiración para el certificado de la federación

De forma predeterminada, los certificados se establecen para que expiren después de dos años. Puede elegir una fecha de expiración diferente para el certificado siguiendo estos pasos. Las capturas de pantalla incluidas usan Salesforce como ejemplo, pero estos pasos se pueden aplicar a cualquier aplicación SaaS federada.

1. En Azure Active Directory, en la página Inicio rápido de la aplicación, haga clic en **Configurar inicio de sesión único**.

	![Abra el asistente para configuración de SSO.](./media/active-directory-sso-certs/config-sso.png)

2. Seleccione **Inicio de sesión único de Azure AD** y luego haga clic en **Siguiente**.

3. Escriba la información correspondiente en **Dirección URL de inicio de sesión** de la aplicación y seleccione la casilla **Configurar el certificado usado para el inicio de sesión único federado**. A continuación, haga clic en **Siguiente**.

	![Inicio de sesión único de Azure AD](./media/active-directory-sso-certs/new-app-config-sso.PNG)

4. En la siguiente página, seleccione **Generar un certificado nuevo** y elija cuánto tiempo desea que el certificado sea válido. A continuación, haga clic en **Siguiente**.

	![Generación de un nuevo certificado](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Luego haga clic en **Descargar certificado**. Para obtener información sobre cómo cargar el certificado a la aplicación SaaS determinada, haga clic en **Ver instrucciones de configuración**.

	![Descarga y carga del certificado](./media/active-directory-sso-certs/new-app-config-app.PNG)

6. El certificado no se habilitará hasta que seleccione la casilla confirmación situada en la parte inferior del cuadro de diálogo y luego presione Enviar.

##Renovación de un certificado que expirará pronto

Lo ideal es que los pasos de renovación que se muestran a continuación no supongan un tiempo de inactividad considerable para los usuarios. Las capturas de pantalla que se usan en esta sección incorporan Salesforce como ejemplo, pero estos pasos se pueden aplicar a cualquier aplicación SaaS federada.

1. En Azure Active Directory, en la página Inicio rápido de la aplicación, haga clic en **Configurar inicio de sesión único**.

	![Apertura del asistente para configuración de SSO](./media/active-directory-sso-certs/renew-sso-button.PNG)

2. En la primera página del cuadro de diálogo, **Inicio de sesión único de Azure AD** ya debería estar seleccionado; por tanto, haga clic en **Siguiente**.

3. En la segunda página, seleccione la casilla **Configurar el certificado usado para el inicio de sesión único federado**. A continuación, haga clic en **Siguiente**.

	![Inicio de sesión único de Azure AD](./media/active-directory-sso-certs/renew-config-sso.PNG)

4. En la siguiente página, seleccione **Generar un certificado nuevo** y elija cuánto tiempo desea que el nuevo certificado sea válido. A continuación, haga clic en **Siguiente**.

	![Generación de un nuevo certificado](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. Haga clic en **Descargar certificado**. Para renovar el certificado correctamente, debe realizar los dos pasos siguientes:

	- Cargue el nuevo certificado en la pantalla de configuración de inicio de sesión único de la aplicación SaaS. Para obtener información sobre cómo hacer esto para su aplicación SaaS, haga clic en **Ver instrucciones de configuración**.

	- En Azure AD, active la casilla de confirmación en la parte inferior del cuadro de diálogo para habilitar el nuevo certificado y luego haga clic en **Siguiente** para enviar.

	> [AZURE.IMPORTANT] El inicio de sesión único para la aplicación se deshabilitará en el momento en el que cualquiera de estos dos pasos se complete, pero se volverá a habilitar una vez completado el segundo paso. Por lo tanto, para minimizar el tiempo de inactividad, prepárese para realizar ambos pasos con muy poco tiempo entre cada uno de ellos.

	![Descarga y carga del certificado](./media/active-directory-sso-certs/renew-config-app.PNG)

## Artículos relacionados

- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
- [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
- [Cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory](active-directory-saml-debugging.md)

<!---HONumber=AcomDC_0211_2016-->