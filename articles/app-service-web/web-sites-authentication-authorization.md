<properties 
	pageTitle="Usar Active Directory para la autenticación en Servicio de aplicaciones de Azure" 
	description="Obtenga información sobre las distintas opciones de autenticación y autorización para las aplicaciones de línea de negocio que se implementan en Aplicaciones web del servicio de aplicaciones de Azure" 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="12/10/2015" 
	ms.author="cephalin"/>

# Usar Active Directory para la autenticación en Servicio de aplicaciones de Azure #

[Aplicaciones web del servicio de aplicaciones de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) permite escenarios de aplicaciones de línea de negocio empresariales al admitir el inicio de sesión único (SSO) de usuarios si tienen acceso a la aplicación desde el entorno local o desde Internet pública. Se puede integrar con [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) (AAD) o con un servicio de token seguro (STS) local, como los Servicios de federación de Active Directory (AD FS), para autenticar a los usuarios de Active Directory (AD) internos y autorizarles correctamente.

## Autorización y autenticación perfecta ##

Con unos pocos clics, puede habilitar la autenticación y autorización para su aplicación web. La configuración de estilo de casillas en las aplicaciones web de Azure proporciona el control de acceso básico de la aplicación web de línea de negocio. Para ello, exige HTTPS y la autenticación en un inquilino de Azure AD de su elección antes de conceder a los usuarios acceso al contenido de la aplicación web. Para obtener más información, consulte [Autenticación y autorización de aplicaciones web](https://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/).

>[AZURE.NOTE] Esta funcionalidad actualmente está en su versión preliminar.

## Implementación manual de la autenticación y la autorización ##

En muchos escenarios, quiere personalizar el comportamiento de la autenticación y la autorización de la aplicación, como una página de inicio y cierre de sesión, la lógica de la autorización personalizada, el comportamiento de la aplicación con varios inquilinos, etc. En estos casos, puede ser mejor configurar la autenticación y la autorización manualmente para obtener una mayor flexibilidad de sus características. A continuación se muestran las dos principales opciones

-	[Azure AD](web-sites-dotnet-lob-application-azure-ad.md): puede implementar la autenticación y la autorización para la aplicación web con Azure AD. Con Azure AD como proveedor de identidades tiene las siguientes características:
	-	Admite protocolos de autenticación populares, como [OAuth 2.0](http://oauth.net/2/), [OpenID Connect](http://openid.net/connect/) y [SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0). Para obtener una lista completa de los protocolos compatibles, consulte [Protocolos de autenticación de Azure Active Directory](http://msdn.microsoft.com/library/azure/dn151124.aspx).
	-	Puede usar un proveedor de identidades solo de Azure sin ninguna infraestructura local.
	-	También puede configurar la sincronización de directorios con un AD local (administrado de forma local).
	-	Azure AD con sincronización de directorios desde su dominio de AD local permite una experiencia de SSO sin problemas con la aplicación web cuando los usuarios de AD tienen acceso desde la intranet e Internet. Desde la intranet, los usuarios de AD pueden tener acceso automáticamente a la aplicación web a través de la autenticación integrada. Desde Internet, los usuarios de AD pueden iniciar sesión en la aplicación web con sus credenciales de Windows.
	-	Proporciona SSO para [todas las aplicaciones compatibles con Azure AD](/marketplace/active-directory/), incluidas Azure, Office 365, Dynamics CRM Online, Microsoft Intune y miles de aplicaciones en la nube que no son de Microsoft. 
	-	Azure AD delega la administración de aplicaciones de [usuarios de confianza](http://en.wikipedia.org/wiki/Relying_party) a los roles que son administradores, mientras que los administradores globales tienen que seguir configurando el acceso a los datos de directorios confidenciales de la aplicación.
	-	Envía un conjunto de uso general de tipos de notificaciones para todas las aplicaciones de usuarios de confianza. Para conocer la lista de tipos de notificaciones, consulte [Tokens compatibles y tipos de notificaciones](http://msdn.microsoft.com/library/azure/dn195587.aspx). Las notificaciones no se pueden personalizar.
	-	La [API gráfica de Azure AD](http://msdn.microsoft.com/library/azure/hh974476.aspx) permite el acceso a datos de directorios de la aplicación en Azure AD.
-	[Servicio de token seguro (STS) local, como AD FS](../web-sites-dotnet-lob-application-adfs/): puede implementar la autenticación y la autorización para la aplicación web con un STS local como AD FS. Con AD FS local dispone de las siguientes características:
	-	La topología de AD FS debe implementarse localmente, con la sobrecarga de administración y coste.
	-	Es la mejor opción cuando la directiva de la empresa requiere que los datos de AD se almacenen localmente.
	-	Solo los administradores de AD FS pueden configurar las [confianzas de los usuarios confianza y las reglas de notificaciones](http://technet.microsoft.com/library/dd807108.aspx).
	-	Puede administrar [notificaciones](http://technet.microsoft.com/library/ee913571.aspx) en una base por aplicación.
	-	Debe tener una solución independiente para tener acceso a datos de AD locales a través del firewall corporativo.

>[AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
 

<!---HONumber=AcomDC_0128_2016-->