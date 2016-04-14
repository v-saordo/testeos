<properties
	pageTitle="Acceso condicional de Azure en versión de vista previa para aplicaciones SaaS | Microsoft Azure"
	description="El acceso condicional en Azure AD permite la configuración de reglas de acceso de autenticación multifactor por aplicación y la capacidad de bloquear el acceso de los usuarios en una red que no es de confianza."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="femila"/>

# Acceso condicional de Azure en versión de vista previa para aplicaciones SaaS

El Acceso condicional de Azure para aplicaciones SaaS está disponible en versión de vista previa pública. La vista previa permite la configuración de reglas de acceso de autenticación multifactor por aplicación y la capacidad de bloquear el acceso de los usuarios en una red que no es de confianza.

La regla de autenticación multifactor puede aplicarse a todos los usuarios que están asignados a la aplicación o solo a los que pertenecen a grupos de seguridad especificados. Los usuarios pueden excluirse del requisito de autenticación multifactor si van a tener acceso a la aplicación desde una dirección IP que se encuentre dentro de la red de la organización. Estas funcionalidades estarán disponibles para los clientes que hayan adquirido una licencia de Azure Active Directory Premium.

## Requisitos previos de escenario
* Suscripción a Azure Active Directory Premium

* Inquilino de Azure Active Directory federado o administrado

* Los inquilinos federados requieren que Multi-Factor Authentication (MFA) esté habilitada

## Problemas conocidos de esta versión de vista previa
Esta vista previa solo se aplica a las aplicaciones SaaS federadas previamente integradas, a las aplicaciones que utilizan inicio de sesión único con contraseña, las aplicaciones de línea de negocio y registradas y desarrolladas y el proxy de la aplicación de Azure AD. Se siguen habilitando algunas aplicaciones adicionales.

##Configuración de las reglas de acceso por aplicación

En esta sección se describe cómo configurar las reglas de acceso por aplicación.

1. Inicie sesión en el Portal de Microsoft Azure como administrador.
2. En el panel izquierdo, seleccione **Active Directory**.
3. En la pestaña Directorio, seleccione su directorio.
4. Seleccione la pestaña **Aplicaciones**.
5. Seleccione la aplicación para la que se establecerá la regla.
6. Seleccione la pestaña **Configurar**.
7. Desplácese hasta la sección de reglas de acceso. Seleccione la regla de acceso deseada.
8. Especifique los usuarios a los que se aplicará la regla.
9. Habilite la directiva; para ello, seleccione **Activado en Habilitado**.

##Descripción de las reglas de acceso

En esta sección se ofrece una descripción detallada de las reglas de acceso que se admiten en la vista previa de acceso condicional de las aplicaciones de Azure.
### Especificación de los usuarios a los que se aplican las reglas de acceso

De forma predeterminada, la directiva se aplicará a todos los usuarios que tengan acceso a la aplicación. Sin embargo, la directiva también se puede restringir a los usuarios que son miembros de los grupos de seguridad especificados. El botón **Agregar grupo** se utiliza para seleccionar uno o varios grupos en el cuadro de diálogo de selección de grupos a los que se aplicará la regla de acceso. Este cuadro de diálogo también sirve para quitar grupos seleccionados. Cuando se seleccionan las reglas que se aplicarán a los grupos, solo se aplicarán las reglas de acceso para los usuarios que pertenezcan a uno de los grupos de seguridad especificados.

Los grupos de seguridad también se pueden excluir explícitamente de la directiva si activa la opción Excepto y especifica uno o más grupos. Los usuarios que sean miembros de un grupo en la lista Excepto no estarán sujetos al requisito de la autenticación multifactor, aunque sean miembros de un grupo al que se aplica la regla de acceso. La regla de acceso que se muestra a continuación requerirá que todos los usuarios del grupo Administradores usen la autenticación multifactor para tener acceso a la aplicación.

![Configuración de reglas de acceso condicional con MFA](./media/active-directory-conditional-access/conditionalaccess-saas-apps.png)

##Reglas de acceso condicional con MFA
Si un usuario se ha configurado con la característica de autenticación multifactor por usuario, esta opción en el usuario tendrá prioridad sobre las reglas de autenticación multifactor de aplicación. Esto significa que un usuario que se ha configurado para la autenticación multifactor por usuario deberá realizarla aunque se haya excluido de las reglas de autenticación multifactor de aplicación. Obtenga más información sobre Multi-Factor Authentication y la configuración por usuario.

###Opciones de reglas de acceso
En la vista previa actual, se admiten las opciones siguientes:

* **Requerir autenticación multifactor**: con esta opción, los usuarios a los que se aplican las reglas de acceso deberán llevar a cabo la autenticación multifactor antes de tener acceso a la aplicación a la que se aplica la directiva.

* **Requerir autenticación multifactor fuera del trabajo**: con esta opción, a los usuarios que proceden de una dirección IP de confianza no se les pedirá que realicen la autenticación multifactor. Los intervalos IP de confianza se pueden configurar en la página de configuración de la autenticación multifactor.

* **Bloquear acceso fuera del trabajo**: con esta opción, se bloqueará a los usuarios que no proceden de una dirección IP de confianza. Los intervalos IP de confianza se pueden configurar en la página de configuración de la autenticación multifactor.

###Configuración del estado de la regla
El estado de la regla de acceso permite la activación o desactivación de las reglas. Cuando las reglas de acceso están desactivadas, el requisito de autenticación multifactor no se aplicará.

### Evaluación de la regla de acceso

Cuando un usuario tiene acceso a una aplicación federada que usa OAuth 2.0, OpenID Connect, SAML o WS-Federation, se evalúan las reglas de acceso. Además, las reglas de acceso se evalúan con OAuth 2.0 y OpenID Connect cuando se usa un token de actualización para adquirir un token de acceso. Si la evaluación de directivas da error cuando se usa un token de actualización, se muestra el error invalid\_grant. Este error indica que el usuario debe volver a autenticarse en el cliente. Configuración de los servicios de federación para proporcionar autenticación multifactor

En el caso de los inquilinos federados, puede que Azure Active Directory o el servidor local de AD FS ejecute Multi-Factor Authentication (MFA).

De forma predeterminada, MFA se producirá en una página hospedada por Azure Active Directory. Para configurar MFA local, la propiedad – SupportsMFA debe establecerse en true en Azure Active Directory usando el módulo de Azure AD para Windows PowerShell.

En el ejemplo siguiente se muestra cómo habilitar MFA local mediante el cmdlet [Set-MsolDomainFederationSettings cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) en el inquilino contoso.com:

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

Además de establecer esta marca, la instancia de AD FS de inquilinos federados debe configurarse para llevar a cabo Multi-Factor Authentication. Siga las instrucciones para implementar Azure Multi-Factor Authentication en modo local.

##Artículos relacionados

- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->