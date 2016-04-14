<properties
	pageTitle="Habilitación de la publicación de aplicaciones de cliente nativas con aplicaciones proxy | Microsoft Azure"
	description="Explica cómo habilitar las aplicaciones cliente nativas para comunicarse con el conector del proxy de la aplicación de Azure AD para proporcionar acceso remoto seguro a las aplicaciones locales."
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

# Habilitación de las aplicaciones cliente nativas para interactuar con el proxy de la aplicación

> [AZURE.NOTE] Proxy de aplicación es una característica que solo está disponible si actualizó a la edición Premium o Basic de Azure Active Directory. Para obtener más información, consulte [Ediciones de Azure Active Directory](active-directory-editions.md).

El proxy de la aplicación de Azure Active Directory se usa por lo general para publicar aplicaciones de explorador como SharePoint, Outlook Web Access y aplicaciones de línea de negocio personalizadas. También puede usarse para publicar aplicaciones de back-end HTTP que se consumen mediante clientes nativos. Esto se lleva a cabo mediante la compatibilidad de tokens emitidos por Azure AD que se envían en encabezados HTTP de autorización estándar.

![Relación entre los usuarios finales, Azure Active Directory y las aplicaciones publicadas](./media/active-directory-application-proxy-native-client/richclientflow.png)

El método recomendado para publicar estas aplicaciones es usar Azure AD Authentication Library, que se encarga de todos los problemas de autenticación y admite muchos entornos de cliente diferentes. El proxy de la aplicación se adapta a la [aplicación nativa para el escenario de Web API](active-directory-authentication-scenarios.md#native-application-to-web-api). El proceso para llevarlo a cabo es el siguiente:

## Paso 1: Publicación de la aplicación

Publique su aplicación proxy, al igual que haría con cualquier otra aplicación, asigne los usuarios y concédales licencias básicas o premium. Para obtener más información, consulte [Publicación de aplicaciones con el proxy de la aplicación](active-directory-application-proxy-publish.md).

## Paso 2: Configuración de la aplicación

Configure su aplicación nativa de la manera siguiente:

1. Inicie sesión en el portal clásico de Azure.
2. Haga clic en el icono de Active Directory en el menú de la izquierda y luego en el directorio que quiera.
3. En el menú superior, haga clic en **Aplicaciones**. Si no se han agregado aplicaciones al directorio, esta página solo muestra el vínculo **Agregar una aplicación**. Haga clic en el vínculo, o como alternativa, puede hacer clic en el botón **Agregar** de la barra de comandos.
4. En la página **¿Qué desea hacer?**, haga clic en el vínculo para **Agregar una aplicación que mi organización está desarrollando**.
5. En la página **Proporcione información sobre su aplicación**, especifique un nombre para la aplicación y elija **Aplicación de cliente nativo**, que representa una aplicación que se instala en un dispositivo como un teléfono o un equipo. Una vez finalizado, haga clic en el icono de flecha situado en la esquina inferior derecha de la página.
6. En la página **Propiedades de la aplicación**, indique el **URI de redirección** de una aplicación cliente nativa y haga clic en la casilla situada en la esquina inferior derecha de la página.

La aplicación se agrega y le llevará a la página Inicio rápido de la aplicación.

## Paso 3: Conceder acceso a otras aplicaciones

Habilite la aplicación nativa para que se exponga a otras aplicaciones en el directorio:

1. En el menú superior, haga clic en **Aplicaciones**, seleccione la nueva aplicación nativa y haga clic en **Configurar**.
2. Desplácese hacia abajo hasta la sección **Permisos para otras aplicaciones**. Haga clic en el botón **Agregar aplicación** y seleccione la aplicación proxy a la que desea conceder acceso a la aplicación nativa y haga clic en la marca de verificación de la esquina inferior derecha. En el menú desplegable **Permisos de delegación**, seleccione el nuevo permiso.

![Captura de pantalla de Permisos para otras aplicaciones - agregar aplicación](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## Paso 4: Editar la biblioteca de autenticación de Active Directory

Edite el código de la aplicación nativa en el contexto de autenticación de Active Directory Authentication Library (ADAL) para incluir lo siguiente:

		// Acquire Access Token from AAD for Proxy Application
		AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
		AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

		//Use the Access Token to access the Proxy Application
		HttpClient httpClient = new HttpClient();
		httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
		HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

Las variables deben reemplazarse como sigue:

- **TenantId** puede encontrarse en el GUID de la dirección URL de la página **Configuración** del solicitante, justo después de “/Directory/”.
- **Frontend URL** es la dirección URL de front-end especificada en la aplicación proxy y se encuentra en la página **Configuración** de la aplicación proxy.
- **Client Id** de la aplicación nativa se encuentra en la página **Configurar** de la aplicación nativa.
- **Redirect URI of the native app** se puede encontrar en la página **Configurar** de la aplicación nativa.

![Captura de pantalla de la página de configuración de la nueva aplicación nativa](./media/active-directory-application-proxy-native-client/new_native_app.png)

Para más información sobre el flujo de la aplicación nativa, vea [Aplicación nativa a Web API](active-directory-authentication-scenarios.md#native-application-to-web-api).


## Pasos siguientes
Hay mucho más que puede hacer con el proxy de la aplicación:

- [Publicar aplicaciones mediante su propio nombre de dominio](active-directory-application-proxy-custom-domains.md)
- [Habilitar el inicio de sesión único](active-directory-application-proxy-sso-using-kcd.md)
- [Trabajar con las aplicaciones para notificaciones](active-directory-application-proxy-claims-aware-apps.md)
- [Habilitar el acceso condicional](active-directory-application-proxy-conditional-access.md)


### Obtenga más información acerca del proxy de la aplicación
- [Eche un vistazo para ver nuestra ayuda en línea](active-directory-application-proxy-enable.md)
- [Consulte el blog del proxy de la aplicación](http://blogs.technet.com/b/applicationproxyblog/)
- [Vea nuestros vídeos de Channel 9](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Recursos adicionales
- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
- [Registro en Azure como una organización](sign-up-organization.md)
- [Identidad de Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_0211_2016-->