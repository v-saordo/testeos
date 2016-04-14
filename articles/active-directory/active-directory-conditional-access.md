<properties
	pageTitle="Protección del acceso a Office 365 y otras aplicaciones conectadas a Azure Active Directory | Microsoft Azure"  
    description="Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que se eligen al autenticar al usuario y antes de permitirle acceso a la aplicación. Si se cumplen las condiciones, el usuario queda autenticado y se le permite el acceso a la aplicación."  
    services="active-directory" 
	keywords="acceso a aplicaciones, acceso seguro a recursos de empresa, directivas de acceso condicional" 
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="02/10/2016"
	ms.author="femila"/>


# Protección del acceso a Office 365 y otras aplicaciones conectadas a Azure Active Directory  
  
La protección del acceso a los recursos de la empresa es importante para todas las organizaciones. Con la llegada de los dispositivos móviles y los servicios en la nube, han cambiado significativamente las formas en que los usuarios acceden a los recursos de la empresa. Esto requiere cambios en la estrategia para proteger los recursos corporativos.
  
## ¿Por qué el acceso condicional  
 Las funcionalidades de control de acceso condicional en Azure Active Directory ofrecen a las empresas varias maneras sencillas de proteger sus recursos en la nube y locales. Si necesita algo como "impedir el acceso a mis recursos mediante una contraseña robada" o "requerir un dispositivo administrado y sin problemas para acceder al contenido de mi empresa", Azure Active Directory satisface sus necesidades.

## ¿Cómo se aplica el control de acceso condicional?  
 Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que elige al autenticar a un usuario, antes de permitirle el acceso a una aplicación. Si se cumplen las condiciones, el usuario queda autenticado y se le permite el acceso a la aplicación.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Acceso basado en usuario a los recursos
  
- **Atributos de usuario**: en el nivel de atributos de usuario, puede aplicar directivas para asegurarse de que solo los usuarios autorizados pueden acceder a los recursos de la empresa.   
- **Pertenencia a grupos de un usuario**: también puede controlar el nivel de acceso proporcionado a un usuario basándose en su pertenencia a un grupo o grupos.   
- **Autenticación multifactor (MFA)**: además, también puede aplicar directivas cuando un usuario tenga que autenticar su identidad mediante un sistema de autenticación multifactor. Por ejemplo, puede obligar a un usuario a confirmar un PIN en un teléfono móvil personal para garantizar un nivel de seguridad adicional. La autenticación de MFA protege los recursos impidiendo que acceda a ellos un usuario no autorizado que haya obtenido acceso al nombre de usuario y a la contraseña de un usuario válido. 

## Acceso condicional basado en dispositivo 

- **Dispositivos registrados**: en el nivel de dispositivo, puede establecer directivas que exijan que solo tengan permiso de acceso los dispositivos registrados o conocidos. Además, puede utilizar una solución de administración de dispositivos móviles (MDM) como Microsoft Intune para garantizar que solo los dispositivos administrados tengan acceso a los recursos. El acceso condicional del nivel de dispositivo garantiza que solo puedan tener acceso los dispositivos que sean compatibles con directivas como exigir el PIN en un dispositivo. Además, mediante soluciones MDM, puede asegurarse de que los datos corporativos de un dispositivo perdido o robado se puedan borrar de forma remota.  
- **Directivas de dispositivo**: también puede establecer directivas para restringir el acceso en función de la aplicación. Asimismo, puede configurar igualmente el nivel de acceso basándose en la ubicación física del dispositivo, es decir, si la solicitud procede de una red corporativa conocida o externa.  

El nivel de acceso que se puede establecer mediante estas directivas puede aplicarse a los recursos en la nube o locales. Los recursos en la nube pueden ser aplicaciones como Office 365 y aplicaciones SaaS de terceros. Además, también pueden ser aplicaciones de línea de negocio que ha hospedado en la nube.
  
## Acceso condicional: mapa de contenido  
El mapa de contenido siguiente enumera los documentos que debe consultar para obtener más información sobre la habilitación de acceso condicional en su implementación actual.


| Escenario | Artículos |
|------------------------------------------------------|----------|
| Proteger los recursos corporativos contra los ataques de suplantación de identidad |[Acceso condicional de Azure en versión de vista previa para aplicaciones SaaS](active-directory-conditional-access-azuread-connected-apps.md)<br><br>[Referencia técnica: acceso condicional a aplicaciones de Azure AD](active-directory-conditional-access-technical-reference.md)<br><br>[Introducción a Azure Multi-Factor Authentication en la nube](multi-factor-authentication-get-started-cloud.md)<br><br>[Configurar métodos de autenticación adicionales para AD FS](https://technet.microsoft.com/library/dn758113.aspx)<br><br>[Problemas con Azure Multi-Factor Authentication](multi-factor-authentication-end-user-manage-settings.md)<br><br>[Protección de recursos en la nube con Azure Multi-Factor Authentication y AD FS](multi-factor-authentication-get-started-adfs-cloud.md)|
| Proteger los datos corporativos en dispositivos perdidos o robados |[Información general sobre el Registro de dispositivos de Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)<br><br> [Configuración de Azure AD Join en su organización](active-directory-azureadjoin-setup.md)<br><br> [Configuración del acceso condicional local mediante el registro de dispositivos de Azure Active Directory](active-directory-conditional-access-on-premises-setup.md) <br><br>[Configuración del registro automático de dispositivos para dispositivos Windows 7 unidos a un dominio](active-directory-conditional-access-automatic-device-registration-windows7.md) <br><br>[Configuración del registro automático de dispositivos para dispositivos Windows 8.1 unidos a un dominio](active-directory-conditional-access-automatic-device-registration-windows8_1.md) <br><br>[Directivas de dispositivo de acceso condicional para servicios de Office 365](active-directory-conditional-access-device-policies.md)|
| Información adicional |[Preguntas más frecuentes sobre acceso condicional](active-directory-conditional-faqs.md)|

<!---HONumber=AcomDC_0218_2016-->