<properties
	pageTitle="Índice de artículos sobre la administración de aplicaciones en Azure Active Directory | Microsoft Azure"
	description="Aprenda a personalizar la fecha de expiración de los certificados de federación y a renovar certificados que expiran pronto."
	services="active-directory"
	documentationCenter=""
	authors="MarkusVi"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="markvi;liviodlc"/>

#Índice de artículos sobre la administración de aplicaciones en Azure Active Directory

Esta página proporciona una lista completa de todos los documentos escritos sobre las diversas características relacionadas con aplicaciones de Azure Active Directory (Azure AD).

Hay una breve introducción a cada área de características principal, así como una guía sobre qué artículos leer en función de la información que se busque.

##Artículos de información general

Los artículos siguientes son buenos puntos de partida para quienes solo deseen una breve explicación de las características de administración de aplicaciones de Azure AD.

| Guía de artículos | |
| :---: | --- |
| Una introducción a los problemas de administración de aplicaciones que Azure AD resuelve | [Administración de aplicaciones con Azure Active Directory (AD)](active-directory-enable-sso-scenario.md) |
| Información general sobre las distintas características de Azure AD relacionadas con la habilitación del inicio de sesión único, la definición de quién tiene acceso a las aplicaciones y de la forma en que los usuarios inician las aplicaciones | [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md) |
| Un vistazo a los distintos pasos que deben darse al integrar aplicaciones en Azure AD | [Guía de introducción a la integración de Azure Active Directory con las aplicaciones](active-directory-integrating-applications-getting-started.md)<br /><br />[Integración del inicio de sesión único de Azure Active Directory con aplicaciones SaaS](active-directory-sso-integrate-saas-apps.md)<br /><br />[Administración del acceso a las aplicaciones](active-directory-managing-access-to-apps.md) |
| Una explicación técnica de cómo se representan las aplicaciones de Azure AD | [Cómo y por qué se agregan aplicaciones a Azure AD](active-directory-how-applications-are-added.md) |

##Artículos de solución de problemas

Esta sección proporciona acceso rápido a las guías de solución de problemas pertinentes. Puede encontrar más información acerca de cada área de características en el resto de esta página.

| Área de características | |
| :---: | --- |
| Inicio de sesión único federado | [Cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory](active-directory-saml-debugging.md) |
| Inicio de sesión único con contraseña | [Solución de problemas de la extensión del Panel de acceso para Internet Explorer](active-directory-saas-ie-troubleshooting.md) |
| Proxy de aplicación | [Solucionar problemas del proxy de aplicación](active-directory-application-proxy-troubleshoot.md) |
| Inicio de sesión único entre un AD local y Azure AD | [Solución de problemas de sincronización de contraseña](active-directory-aadconnectsync-implement-password-synchronization.md#managing-password-synchronization)<br /><br />[Solución de problemas de escritura diferida de contraseñas](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) | 
| Pertenencia a grupos dinámicos. | [Solución de problemas de pertenencias a grupos dinámicos](active-directory-accessmanagement-troubleshooting.md) |

##Inicio de sesión único (SSO)

###Inicio de sesión único federado: inicio de sesión en muchas aplicaciones con una sola identidad

El inicio de sesión único permite a los usuarios acceder a varias aplicaciones y servicios con un único conjunto de credenciales. La federación es un método a través del cual se puede habilitar el inicio de sesión único. Cuando los usuarios intentan iniciar sesión en aplicaciones federadas, se les redirigirá a la página de inicio de sesión oficial de su organización representada por Azure Active Directory y luego se le redirigirá a la aplicación tras una autenticación correcta.

| Guía de artículos | |
| :---: | --- |
| Una introducción a la federación y otros tipos de inicio de sesión | [Inicio de sesión único con Azure AD](active-directory-appssoaccess-whatis.md) |
| Miles de aplicaciones SaaS integradas previamente con Azure AD con pasos de configuración de inicio de sesión único simplificados | [Introducción a la Galería de aplicaciones de Azure AD](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[Lista completa de aplicaciones integradas previamente que admiten la federación](http://aka.ms/aadfederatedapps)<br /><br />[Anuncio de la aplicación en la Galería de aplicaciones de Azure AD](active-directory-app-gallery-listing.md) |
| Tutoriales de más de 150 aplicaciones sobre cómo configurar el inicio de sesión único para aplicaciones como [Salesforce](active-directory-saas-salesforce-tutorial.md), [ServiceNow](active-directory-saas-servicenow-tutorial.md), [Google Apps](active-directory-saas-google-apps-tutorial.md), [Workday](active-directory-saas-workday-tutorial.md), entre muchas otras | [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md) |
| Configuración y personalización manual de una configuración de inicio de sesión único | [Configuración del inicio de sesión único en aplicaciones que no están en la Galería de aplicaciones de Azure Active Directory](active-directory-saas-custom-apps.md)<br /><br />[Personalización de notificaciones emitidas en el token SAML para aplicaciones previamente integradas en Azure Active Directory](active-directory-saml-claims-customization.md) |
| Guía de solución de problemas de aplicaciones federadas que utilizan el protocolo SAML | [Cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory](active-directory-saml-debugging.md) |
| Configuración de la fecha de expiración del certificado de la aplicación y renovación de certificados | [Administración de certificados para inicio de sesión único federado en Azure Active Directory](active-directory-sso-certs.md) |

El inicio de sesión único federado está disponible para todas las ediciones de Azure AD para un máximo de diez aplicaciones por usuario. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) admite un número ilimitado de aplicaciones. Si la organización tiene [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) o [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), podrá [utilizar grupos para asignar el acceso a las aplicaciones federadas](#how-to-manage-who-has-access-to-which-apps).

###Inicio de sesión único con contraseña: uso compartido de cuentas y SSO para aplicaciones no federadas

Para habilitar el inicio de sesión único en aplicaciones que no son compatibles con la federación, Azure AD ofrece características de administración de contraseñas que pueden almacenar de forma segura las contraseñas de aplicaciones SaaS e iniciar automáticamente sesiones de usuarios en dichas aplicaciones. Puede distribuir las credenciales de las cuentas recién creadas y compartir las cuentas del equipo con varias personas fácilmente. No es preciso que los usuarios conozcan las credenciales de las cuentas a las que se les otorga acceso.

| Guía de artículos | |
| :---: | --- |
| Una introducción al funcionamiento de SSO con contraseña y una breve introducción técnica | [Inicio de sesión único con contraseña](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) |
| Un resumen de los escenarios relacionados con el uso compartido de cuentas y la forma en que Azure AD resuelve estos problemas | [Uso compartido de cuentas con Azure AD](active-directory-sharing-accounts.md) |
| Cambio automático de la contraseña de ciertas aplicaciones a intervalos regulares | [Sustitución automática de contraseña (vista previa)](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx0) |
| Guías de implementación y solución de problemas de la versión de Internet Explorer de la extensión de administración de contraseñas de Azure AD | [Implementación de la extensión de panel de acceso para Internet Explorer mediante la directiva de grupo](active-directory-saas-ie-group-policy.md)<br /><br />[Solución de problemas de la extensión del panel de acceso para Internet Explorer](active-directory-saas-ie-troubleshooting.md) |

El inicio de sesión único con contraseña está disponible para todas las ediciones de Azure AD para un máximo de diez aplicaciones por usuario. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) admite un número ilimitado de aplicaciones. Si la organización tiene [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) o [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), podrá [utilizar grupos para asignar el acceso a las aplicaciones](#how-to-manage-who-has-access-to-which-apps). La sustitución automática de contraseña es una característica de [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/).

###Proxy de aplicación: inicio de sesión único y acceso remoto a aplicaciones locales

Si en la red privada tiene aplicaciones a las que los usuarios y dispositivos de fuera de la red necesitan acceder, puede utilizar el proxy de aplicación de Azure AD para habilitar el acceso remoto seguro a dichas aplicaciones.

| Guía de artículos | |
| :---: | --- |
| Información general sobre el proxy de aplicación de Azure AD y su funcionamiento | [Provisión de acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md)<br /><br />[Habilitación del acceso híbrido con el proxy de aplicación](active-directory-appssoaccess-enable-hybrid-access.md) |
| Tutoriales sobre cómo configurar el proxy de aplicación y cómo publicar la primera aplicación | [Habilitación del proxy de la aplicación de Azure AD](active-directory-application-proxy-enable.md)<br /><br />[Cómo instalar de forma silenciosa el conector de proxy de aplicación de Azure AD](active-directory-application-proxy-silent-installation.md)<br /><br />[Publicación de aplicaciones mediante el proxy de aplicación de Azure AD](active-directory-application-proxy-publish.md)<br /><br />[Uso de dominios personalizados en el proxy de la aplicación de Azure AD](active-directory-application-proxy-custom-domains.md) |
| Habilitación del inicio de sesión único y del acceso condicional en aplicaciones publicadas con el proxy de aplicación | [Inicio de sesión único con el proxy de aplicación](active-directory-application-proxy-sso-using-kcd.md)<br /><br />[Uso de acceso condicional](active-directory-application-proxy-conditional-access.md) |
| Guía sobre cómo usar el proxy de aplicación en los escenarios siguientes | [Habilitación de las aplicaciones cliente nativas para interactuar con el proxy de la aplicación](active-directory-application-proxy-native-client.md)<br /><br />[Trabajar con las aplicaciones para notificaciones en proxy de aplicación](active-directory-application-proxy-claims-aware-apps.md)<br /><br />[Publicación de aplicaciones en redes independientes y ubicaciones mediante grupos de conectores](active-directory-application-proxy-connectors.md) |
| Guía de solución de problemas del proxy de aplicación | [Solucionar problemas del proxy de aplicación](active-directory-application-proxy-troubleshoot.md) |

El proxy de aplicación está disponible para todas las ediciones de Azure AD para un máximo de diez aplicaciones por usuario. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) admite un número ilimitado de aplicaciones. Si la organización tiene [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) o [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), podrá [utilizar grupos para asignar el acceso a las aplicaciones](#how-to-manage-who-has-access-to-which-apps).

También le puede interesar [Servicios de dominio de Azure AD](../active-directory-domain-services/active-directory-ds-overview.md), ya que le permite migrar las aplicaciones locales a Azure sin dejar de satisfacer las necesidades de identidad de dichas aplicaciones.

###Habilitación del inicio de sesión único entre Azure AD y AD local

Si la organización mantiene una versión local de Windows Server Active Directory junto la versión en la nube de Azure Active Directory, es posible habilitar el inicio de sesión único entre estos dos sistemas. Azure AD Connect (la herramienta que integra estos dos sistemas) proporciona varias opciones para configurar el inicio de sesión único: establecer la federación con ADFS u otro proveedor de federación, o bien habilitar la sincronización de contraseña.

| Guía de artículos | |
| :---: | --- |
| Información general acerca de las opciones de inicio de sesión único que se ofrecen en Azure AD Connect, así como información acerca de la administración de entornos híbridos | [Opciones para el inicio de sesión de los usuarios en Azure AD Connect](active-directory-aadconnect-user-signin.md) |
| Guía general para la administración de entornos con la versión local de Active Directory y Azure Active Directory | [Consideraciones de diseño de identidad híbrida de Azure Active Directory](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md) |
| Instrucciones de uso de la sincronización de contraseñas para habilitar el inicio de sesión único | [Implementación de la sincronización de contraseñas con Azure AD Connect](active-directory-aadconnectsync-implement-password-synchronization.md)<br /><br />[Solución de problemas de sincronización de contraseñas](https://support.microsoft.com/es-ES/kb/2855271) |
| Instrucciones de uso de la sincronización de la escritura diferida de contraseñas para habilitar el inicio de sesión único | [Introducción a la administración de contraseñas en Azure AD](active-directory-passwords-getting-started.md)<br /><br />[Solución de problemas de escritura diferida de contraseñas](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Instrucciones de uso de proveedores externos de identidades para habilitar el inicio de sesión único | [Lista de proveedores externos compatibles de identidades que se puede utilizar para habilitar el inicio de sesión único](https://aka.ms/ssoproviders) | 
| De qué forma pueden disfrutar los usuarios de Windows 10 de las ventajas del inicio de sesión único a través de Azure AD Join | [Ampliación de las capacidades de nube a dispositivos de Windows 10 a través de Azure Active Directory Join](active-directory-azureadjoin-overview.md) |

Azure AD Connect está disponible para [todas las ediciones de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Autoservicio de restablecimiento de contraseña de Azure AD está disponible para [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) y [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Escritura diferida de contraseñas en AD local es una característica de [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/).

###Acceso condicional: aplicación de requisitos de seguridad adicionales en aplicaciones de alto riesgo

Una vez configurado el inicio de sesión único en las aplicaciones y los recursos, es posible proteger aún más las aplicaciones confidenciales. Para ello, es preciso aplicar los requisitos de seguridad específicos a cada inicio de sesión en dicha aplicación. Por ejemplo, puede usar Azure AD para exigir que todo el acceso a una aplicación concreta requiera siempre la autenticación multifactor, independientemente de que la aplicación admita de forma innata dicha funcionalidad. Otro ejemplo común de acceso condicional es requerir que los usuarios estén conectados a la red de confianza de la organización para tener acceso a una aplicación especialmente confidencial.

| Guía de artículos | |
| :---: | --- |
| Una introducción a las funcionalidades de acceso condicional que se ofrecen a través de Azure AD, Office 365 e Intune | [Administración de riesgos con el acceso condicional](active-directory-conditional-access.md) |
| Habilitación del acceso condicional para los siguientes tipos de recursos | [Acceso condicional de Azure en versión de vista previa para aplicaciones SaaS](active-directory-conditional-access-azuread-connected-apps.md)<br /><br />[Directivas de dispositivo de acceso condicional para servicios de Office 365](active-directory-conditional-access-device-policies.md)<br /><br />[Configuración del acceso condicional local mediante el registro de dispositivos de Azure Active Directory](active-directory-conditional-access-on-premises-setup.md)<br /><br />[Uso de acceso condicional](active-directory-application-proxy-conditional-access.md) |
| Registro de los dispositivos en Azure Active Directory, con el fin de habilitar directivas de acceso condicional basado en dispositivo | [Registro automático de dispositivos en Azure Active Directory para dispositivos Windows unidos a un dominio](active-directory-conditional-access-device-registration-overview.md)<br /><br />[Cómo habilitar el registro automático de dispositivos para dispositivos de Windows unidos a un dominio](active-directory-conditional-access-automatic-device-registration.md)<br />: [Pasos para dispositivos Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows8_1.md)<br />: [Pasos para dispositivos Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md) |
| Uso de la versión de Android de la aplicación Azure Authenticator para directivas que implican la autenticación multifactor | [Azure Authenticator para Android](active-directory-conditional-access-azure-authenticator-app.md) |

Acceso condicional es una característica de [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/).


##Aplicaciones y Azure AD

###Cloud App Discovery: búsqueda de las aplicaciones SaaS que se usan en la organización

Cloud App Discovery ayuda a los departamentos de TI a saber qué aplicaciones SaaS se usan en toda la organización. Puede medir el uso y la popularidad de las aplicaciones, con el fin de que TI pueda determinar qué aplicaciones serán las que más se beneficien tanto de estar bajo el control de TI como de integrarse en Azure AD.

| Guía de artículos | |
| :---: | --- |
| Información general de su funcionamiento | [Búsqueda de aplicaciones de nube no sancionadas con Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md) |
| Un análisis más profundo de su funcionamiento, con las respuestas a las preguntas sobre privacidad | [Consideraciones de seguridad y privacidad de Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) |
| Preguntas frecuentes | [Cloud App Discovery - Frequently Asked Questions (Preguntas frecuentes sobre Cloud App Discovery)](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |
| Tutoriales para la implementación de Cloud App Discovery | [Guía de implementación de directivas de grupo de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)<br /><br />[Guía de implementación de System Center](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)<br /><br />[Instalación en servidores proxy con puertos personalizados](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| El registro de cambios de las actualizaciones del agente de Cloud App Discovery | [Registro de cambios](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx) |

Cloud App Discovery es una característica de [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/).

###Aprovisionamiento y desaprovisionamiento automáticos de cuentas de usuario en aplicaciones SaaS

Automatice la creación, el mantenimiento y la eliminación de identidades de usuario en aplicaciones SaaS como Dropbox, Salesforce, ServiceNow, etc. Haga coincidir y sincronice las identidades existentes entre Azure AD y las aplicaciones SaaS, y controle el acceso mediante la deshabilitación automática de cuentas cuando los usuarios dejan la organización.

| Guía de artículos | |
| :---: | --- |
| Información acerca de su funcionamientos y respuestas a preguntas comunes | [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md) |
| Configuración de cómo se asigna información entre Azure AD y una aplicación SaaS | [Personalización de asignaciones de atributos](active-directory-saas-customizing-attribute-mappings.md)<br><br>[Escritura de expresiones para la asignación de atributos en Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| Habilitación del aprovisionamiento automático en todas las aplicaciones que admitan el protocolo SCIM | [Uso de SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory a aplicaciones](active-directory-scim-provisioning.md) |
| Recepción de notificaciones de errores de aprovisionamiento | [Notificaciones de aprovisionamiento de cuentas](active-directory-saas-account-provisioning-notifications.md) |
| Limitación de quiénes se aprovisionan en una aplicación en función de los valores de sus atributos | [Aprovisionamiento de aplicaciones basado en atributos con filtros de ámbito](active-directory-saas-scoping-filters.md) |

El aprovisionamiento automático de usuarios está disponible para todas las ediciones de Azure AD para un máximo de diez aplicaciones por usuario. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) admite un número ilimitado de aplicaciones. Si la organización tiene [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) o [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), podrá [utilizar grupos para administrar qué usuarios reciben el aprovisionamiento](#how-to-manage-who-has-access-to-which-apps).

###Compilación de aplicaciones que se integran con Azure AD

Si su organización desarrolla o mantiene aplicaciones de línea de negocio (LoB) o si desarrollador de aplicaciones y tiene clientes que usen Azure Active Directory, los siguientes tutoriales le ayudará a integrar las aplicaciones en Azure AD.

| Guía de artículos | |
| :---: | --- |
| Guía para profesionales de TI y desarrolladores de aplicaciones para la integración de aplicaciones en Azure AD | [Azure AD y aplicaciones: guía para los desarrolladores](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Guía del desarrollador de Azure Active Directory](active-directory-developers-guide.md) |
| Procedimiento para los proveedores de aplicaciones puedan agregar sus aplicaciones a la Galería de aplicaciones de Azure AD | [Enumeración de aplicaciones en la Galería de aplicaciones de Azure Active Directory](active-directory-app-gallery-listing) |
| Administración del acceso a aplicaciones desarrolladas mediante Azure Active Directory | [Azure AD y aplicaciones: necesidad de asignación de usuario](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Azure AD y aplicaciones: asignación de usuarios a una aplicación](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Azure AD y aplicaciones: asignación de grupos a una aplicación](active-directory-applications-guiding-developers-assigning-groups.md) |

Si está desarrollando aplicaciones orientadas al consumidor, puede que le interese utilizar [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/), con el fin de que no tenga que desarrollar su propio sistema de identidad para administrar los usuarios. [Más información](../active-directory-b2c/active-directory-b2c-overview.md).


##Administración del acceso a las aplicaciones

###Uso de grupos y autoservicio para administrar quién tiene acceso a cada aplicación

Para ayudarle a administrar quién debe tener acceso a cada recurso, Azure Active Directory permite establecer asignaciones y permisos a escala mediante los grupos. El departamento de TI puede habilitar características de autoservicio para que los usuarios puedan solicitar permiso cuando lo necesiten.

| Guía de artículos | |
| :---: | --- |
| Introducción a las características de administración de acceso de Azure AD | [Administración del acceso a las aplicaciones](active-directory-managing-access-to-apps.md)<br /><br />[Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md)<br /><br />[Uso de un grupo para administrar el acceso a las aplicaciones SaaS](active-directory-accessmanagement-group-saasapps.md) |
| Habilitación de la administración autoservicio de aplicaciones y grupos | [Acceso a aplicaciones de autoservicio y administración delegada con Azure Active Directory](active-directory-self-service-application-access.md)<br /><br />[Configuración de Azure Active Directory para la administración del acceso a la aplicación de autoservicio](active-directory-accessmanagement-self-service-group-management.md) |
| Instrucciones para configurar los grupos en Azure AD | [Administración de grupos de seguridad en Azure Active Directory](active-directory-accessmanagement-manage-groups.md)<br /><br />[Administración de propietarios de un grupo](active-directory-accessmanagement-managing-group-owners.md)<br /><br />[Grupos dedicados en Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md) |
| Uso de grupos dinámicos para rellenar automáticamente la pertenencia al grupo mediante reglas de pertenencia basadas en atributos | [Creación de una regla sencilla para configurar pertenencias dinámicas para un grupo](active-directory-accessmanagement-simplerulegroup.md)<br /><br />[Uso de atributos para crear reglas avanzadas](active-directory-accessmanagement-groups-with-advanced-rules.md)<br /><br />[Solución de problemas de pertenencias dinámicas a grupos](active-directory-accessmanagement-troubleshooting.md) |

La administración de acceso a aplicaciones basado en grupos está disponible para [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) y [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). La administración de grupos de autoservicio, la administración de aplicaciones de autoservicio y los grupos dinámicos son características de [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/).

###Colaboración B2B: habilitar el acceso de los asociados a las aplicaciones

Si su empresa se ha asociado con otras compañías, es probable que necesite administrar el acceso de los asociados a las aplicaciones corporativas. La colaboración B2B de Azure Active Directory proporciona una manera fácil y segura de compartir sus aplicaciones con los asociados. Esta funcionalidad actualmente está en su versión preliminar.

| Guía de artículos | |
| :---: | --- |
| Información general sobre las diferentes características de Azure AD que pueden ayudarle a administrar usuarios externos como asociados, clientes, etc. | [Comparación de funcionalidades para administrar identidades externas con Azure Active Directory](active-directory-b2b-compare-external-identities.md) |
| Una introducción a la vista previa de la colaboración B2B y primeros pasos con ella | [Vista previa de colaboración B2B de Azure Active Directory (Azure AD): integración sencilla y segura de los asociados de la nube](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Colaboración B2B de Azure Active Directory (Azure AD) B2B](active-directory-b2b-collaboration-overview.md) |
| Un análisis más profundo sobre la colaboración B2B de Azure AD y cómo usarlo | [Vista previa de colaboración B2B de Azure Active Directory (Azure AD): funcionamiento](active-directory-b2b-how-it-works.md)<br /><br />[Limitaciones actuales de vista previa para la colaboración B2B de Azure Active Directory (Azure AD)](active-directory-b2b-current-preview-limitations.md)<br /><br />[Tutorial detallado sobre cómo usar la vista previa de colaboración B2B de Azure Active Directory (Azure AD)](active-directory-b2b-detailed-walkthrough.md) |
| Artículos de referencia con detalles técnicos acerca del funcionamiento de la colaboración B2B de Azure AD | [Formato de archivo CSV para la vista previa de colaboración B2B de Azure Active Directory (Azure AD)](active-directory-b2b-references-csv-file-format.md)<br /><br />[Cambios de atributos de objeto de usuario externo para la vista previa de colaboración B2B de Azure Active Directory (Azure AD)](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[Formato de token de usuario externo para la vista previa de colaboración B2B de Azure Active Directory (Azure AD)](active-directory-b2b-references-external-user-token-format.md) |

En la actualidad, la vista previa de la colaboración B2B está disponible para [todas las ediciones de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

###Panel de acceso: portal para acceder a las aplicaciones y características de autoservicio

El panel de acceso de Azure AD es el lugar en que los usuarios finales pueden iniciar sus aplicaciones y acceder a las características de autoservicio que les permiten administrar sus aplicaciones y pertenencias a grupos. Además del panel de acceso, en la lista siguiente se incluyen otras opciones para acceder a aplicaciones con SSO habilitado.

| Guía de artículos | |
| :---: | --- |
| Una comparación de las distintas opciones disponibles para la implementación de aplicaciones de inicio de sesión único en los usuarios | [Implementación de aplicaciones integradas en Azure AD en los usuarios](active-directory-appssoaccess-whatis/#deploying-azure-ad-integrated-applications-to-users) |
| Información general sobre el panel de acceso y su MyApps equivalente móvil | [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)<br />— [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Acceso a aplicaciones de Azure AD desde el sitio web de Office 365 | [Le presentamos el iniciador de aplicaciones de Office 365](https://support.office.com/es-ES/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Acceso a aplicaciones de Azure AD desde la aplicación móvil Intune Managed Browser | [Intune Managed Browser](https://technet.microsoft.com/es-ES/library/dn878029.aspx)<br />— [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| Acceso a aplicaciones de Azure AD mediante vínculos profundos para iniciar el inicio de sesión único | [Vínculos de inicio de sesión directos para aplicaciones federadas, con contraseña o existentes](active-directory-appssoaccess-whatis.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

El panel de acceso está disponible para [todas las ediciones de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

###Informes: auditar cambios en los accesos a la aplicaciones y supervisar los inicios de sesión de las aplicaciones de manera sencilla

Azure Active Directory proporciona varios informes y alertas que le ayudan a supervisar el acceso de la organización a las aplicaciones. Puede recibir alertas si se producen inicios de sesión anómalos a sus aplicaciones y puede realizar un seguimiento de cuándo y por qué ha cambiado el acceso de un usuario a una aplicación.

| Guía de artículos | |
| :---: | --- |
| Información general de las características de creación de informes de Azure Active Directory | [Introducción a los informes de Azure Active Directory](active-directory-reporting-getting-started.md) |
| Supervisión de los inicios de sesión de los usuarios y del uso que hacen de las aplicaciones | [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md)<br /><br />[Informe de actividad de inicio de sesión de todos los usuarios de Azure Active Directory](active-directory-reporting-all-user-sign-in-activity-report.md) |
| Seguimiento de los cambios realizados sobre quiénes pueden acceder a una aplicación concreta | [Eventos del Informe de auditoría de Azure Active Directory](active-directory-reporting-audit-events.md) |
| Exportación de los datos de estos informes a sus herramientas preferidas mediante la API de informes | [Introducción a la API de informes de Azure AD](active-directory-reporting-api-getting-started.md) |

Para ver qué informes incluyen las diferentes ediciones de Azure Active Directory, [haga clic aquí](active-directory-view-access-usage-reports.md#report-editions).

##Consulte también

[¿Qué es Azure Active Directory?](active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Servicios de dominio de Azure Active Directory](https://azure.microsoft.com/services/active-directory-ds/)

[Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/)

<!---HONumber=AcomDC_0218_2016-->