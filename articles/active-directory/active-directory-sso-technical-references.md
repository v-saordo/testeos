<properties
   pageTitle="Biblioteca de documentos para administrar aplicaciones | Microsoft Azure"
   description="Temas de administración de aplicaciones de Azure Active Directory, con vínculos de referencia técnica para los procedimientos, solución de problemas y preguntas más frecuentes"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/03/2015"
   ms.author="kgremban"/>

# Biblioteca de documentos para administrar aplicaciones

Esta página contiene vínculos útiles para las principales características de inicio de sesión único con Azure Active Directory. Los vínculos están organizados en las siguientes categorías:

- **Información general:** visión general de la característica con casos de uso e introducciones de temas.
- **Procedimientos:** instrucciones detalladas para empezar a trabajar con escenarios concretos.
- **Solución de problemas:** sugerencias para diagnosticar problemas comunes.
- **Preguntas más frecuentes:** respuestas a preguntas e inquietudes típicas.  

## Detección de aplicaciones de nube

| | |
| ------ | ------ |
| Información general | [¿Cómo puedo detectar aplicaciones en la nube no sancionadas que se usan dentro de mi organización?](active-directory-cloudappdiscovery-whatis.md) |
| Procedimiento | [Introducción a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx) <br><br> [Consideraciones de seguridad y privacidad de Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) <br><br> [Guía de implementación de directivas de grupo de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx) <br><br> [Guía de implementación de System Center de Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx) <br><br> [Configuración del registro de Cloud App Discovery para servicios de proxy](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| P+F | [Cloud App Discovery - preguntas más frecuentes](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |

## Asignación de grupos

| | |
| ------ | ------ |
| Información general | [Administración del acceso a los recursos con grupos de Azure Active Directory](active-directory-manage-groups.md) |
| Procedimiento | [Creación de una regla sencilla para configurar pertenencias dinámicas para un grupo](active-directory-accessmanagement-simplerulegroup.md) <br><br> [Uso de un grupo para administrar el acceso a aplicaciones SaaS](active-directory-accessmanagement-group-saasapps.md) <br><br> [Configuración de Azure AD para la administración de acceso de aplicaciones de autoservicio](active-directory-accessmanagement-self-service-group-management.md) <br><br> [Administrar propietarios para un grupo](active-directory-accessmanagement-managing-group-owners.md) <br><br> [Administración de grupos de seguridad de Azure Active Directory](active-directory-accessmanagement-manage-groups.md) <br><br> [Grupos dedicados en Azure Active Directory](active-directory-accessmanagement-dedicated-groups.md) <br><br> [Uso de atributos para crear reglas avanzadas](active-directory-accessmanagement-groups-with-advanced-rules.md) <br><br> [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md) <br><br> [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md) |

## Inicio de sesión único federado

| | |
| ------ | ------ |
| Información general |[Configuración de federación con AD FS](active-directory-aadconnect-get-started-custom.md)
| Procedimiento | [DirSync con inicio de sesión único](https://msdn.microsoft.com/library/azure/dn441213.aspx) <br><br> [Microsoft Azure ahora admite la federación con Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory/) <br><br> [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md) <br><br> [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/) |
| Solución de problemas | [Solución de problemas del Asistente para configuración de la herramienta DirSync de Azure Active Directory](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx) <br><br> [Cómo solucionar problemas de la instalación de la herramienta de sincronización de Azure Active Directory y errores del Asistente para configuración](https://support.microsoft.com/kb/2684395) <br><br> [Solucionar problemas de sincronización de directorios](https://msdn.microsoft.com/library/azure/jj151787.aspx) <br><br> [Solucionar problemas de inicio de sesión único](https://msdn.microsoft.com/library/azure/jj151834.aspx) |
| P+F | [Administración de certificados para inicio de sesión único federado en Azure Active Directory](active-directory-sso-certs.md) <br><br> [Preguntas más frecuentes sobre Azure Active Directory Connect](active-directory-aadconnect-faq.md) |

## Inicio de sesión único con contraseña

| | |
| ------ | ------ |
| Procedimiento | [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md) <br><br> [DirSync con sincronización de contraseñas](https://msdn.microsoft.com/library/azure/dn441214.aspx) |
| Solución de problemas | [Solución de problemas del Asistente para configuración de la herramienta DirSync de Azure Active Directory](http://social.technet.microsoft.com/wiki/contents/articles/19100.troubleshooting-azure-ad-dirsync-tool-configuration-wizard-failed-to-get-address-for-method-createidentityhandle2.aspx) <br><br> [Cómo solucionar problemas de la instalación de la herramienta de sincronización de Azure Active Directory y errores del Asistente para configuración](https://support.microsoft.com/kb/2684395) <br><br> [Solucionar problemas de sincronización de directorios](https://msdn.microsoft.com/library/azure/jj151787.aspx) |
| P+F | [Preguntas más frecuentes sobre Azure Active Directory Connect](active-directory-aadconnect-faq.md) |

## Aprovisionamiento de aplicaciones enriquecidas

| | |
| ------ | ------ |
| Información general | [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md) |
| Procedimiento | [Tutorial: integración de Azure Active Directory con Concur](active-directory-saas-concur-tutorial.md) <br><br> [Tutorial: Integración de Azure Active Directory con DocuSign](active-directory-saas-docussign-tutorial.md) <br><br> [Tutorial: Integración de Azure Active Directory con Dropbox para Empresas](active-directory-saas-dropboxforbusiness-tutorial.md) <br><br> [Tutorial: Integración de Google Apps con Azure Active Directory](active-directory-saas-google-apps-tutorial.md) <br><br> [Tutorial: integración de Salesforce con Azure Active Directory](active-directory-saas-salesforce-tutorial.md) <br><br> [Tutorial: integración de Azure Active Directory con ServiceNow](active-directory-saas-servicenow-tutorial.md) <br><br> [Tutorial: configuración de Workday para sincronización de entrada](active-directory-saas-workday-inbound-tutorial.md) |

## Galería de aplicaciones de Azure AD

| | |
| ------ | ------ |
| Procedimiento | [Anuncio de la aplicación en la galería de aplicaciones de Azure Active Directory](active-directory-app-gallery-listing.md) <br><br> ["Traiga su propia aplicación" con la configuración de SALM de autoservicio de Azure AD](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) |

## Aplicaciones personalizadas

| | |
| ------ | ------ |
| Información general | ["Traiga su propia aplicación" con la configuración de SALM de autoservicio de Azure AD](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx) |
| Procedimiento | [Tutorial: Integración de Salesforce con Azure Active Directory](active-directory-saas-salesforce-tutorial.md) |

## Proxy de aplicaciones

| | |
| ------ | ------ |
| Información general | [Provisión de acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md) |
| Procedimiento | [Habilitar el proxy de la aplicación de Azure AD](active-directory-application-proxy-enable.md) <br><br> [Publicación de aplicaciones mediante el proxy de aplicación de Azure AD](active-directory-application-proxy-publish.md) <br><br> [Trabajar con el acceso condicional](active-directory-application-proxy-conditional-access.md) <br><br> [Trabajar con dominios personalizados en el proxy de la aplicación de Azure AD](active-directory-application-proxy-custom-domains.md) <br><br> [Trabajar con las aplicaciones para notificaciones en Proxy de la aplicación](active-directory-application-proxy-claims-aware-apps.md) <br><br> [Inicio de sesión único con el Proxy de la aplicación](active-directory-application-proxy-sso-using-kcd.md) |
| Solución de problemas | [Solucionar problemas de Proxy de aplicación](active-directory-application-proxy-troubleshoot.md) |

## Experiencia de inicio de aplicaciones

| | |
| ------ | ------ |
| Información general | [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md) |
| Procedimiento | [Panel de acceso de Azure Active Directory - EMS](http://blogs.msdn.com/b/haddy_el-haggan_blog/archive/2015/04/02/azure-active-directory-access-panel-ems.aspx) <br><br> [Integración con Azure Active Directory](active-directory-how-to-integrate.md) |

<!---HONumber=AcomDC_1210_2015-->