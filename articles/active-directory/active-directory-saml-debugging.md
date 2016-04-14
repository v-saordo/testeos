<properties 
    pageTitle="Cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory | Microsoft Azure" 
    description="Obtenga información acerca de cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory" 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#Cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory

Cuando se depura una integración de la aplicación basada en SAML, a menudo resulta útil usar una herramienta como [Fiddler](http://www.telerik.com/fiddler) para ver la solicitud SAML, la respuesta de SAML y el token SAML real que se emite a la aplicación. Mediante el examen del token SAML, puede asegurarse de que todos los atributos necesarios, el nombre de usuario del SAML Subject y el URI del emisor se logren del modo esperado.

![][1]

Normalmente, la respuesta de Azure AD que contiene el token SAML es la que se produce después de un redireccionamiento HTTP 302 desde https://login.windows.net, y se envía a la **Dirección URL de respuesta** configurada de la aplicación.
 
Puede ver el token SAML seleccionando esta línea y, a continuación, seleccionando la pestaña **Inspectores > WebForms** del panel derecho. Desde este, haga clic con el botón derecho en el valor **SAMLResponse** y seleccione **Enviar a TextWizard**. A continuación, seleccione **De Base64** desde el menú **Transformar** para descodificar el token y ver su contenido.
 
**Nota**: Para ver el contenido de esta solicitud HTTP, es posible que Fiddler le solicite configurar el cifrado del tráfico HTTPS, que tendrá que llevar a cabo.

## Artículos relacionados

- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
- [Configuración del inicio de sesión único en aplicaciones que no están en la Galería de aplicaciones de Azure Active Directory](active-directory-saas-custom-apps.md)
- [Personalización de notificaciones emitidas en el token SAML para aplicaciones previamente integradas en Azure Active Directory](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ./media/active-directory-saml-debugging/fiddler.png

<!---HONumber=AcomDC_0211_2016-->