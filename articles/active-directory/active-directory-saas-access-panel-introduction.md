<properties
	pageTitle="Introducción al panel de acceso | Microsoft Azure"
	description="Aprenda a usar las diferentes variantes del Panel de acceso (explorador Web, aplicación Android, aplicación iPhone y iPad) para acceder a las aplicaciones SaaS que tiene asignadas."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="markusvi"/>


# Introducción al Panel de acceso


El Panel de acceso es un portal basado en web que permite que los usuarios finales que tengan cuenta organizativa en Azure Active Directory puedan ver e iniciar aplicaciones basadas en la nube a las que el administrador de Azure AD les haya concedido acceso. Si es un usuario final con ediciones de Azure Active Directory, también puede utilizar las capacidades de autoservicio de administración de grupos a través del Panel de acceso. <br> El Panel de acceso es independiente del Portal de administración de Azure y no requiere que los usuarios tengan una suscripción de Azure.


![Panel de acceso][1]


El Panel de acceso permite a los usuarios editar parte de la configuración de su perfil, incluida la capacidad para:

- Cambiar la contraseña asociada a la cuenta de su organización

- Editar configuración de restablecimiento de contraseña

- Editar la configuración de contacto y preferencias relacionada con Multi-factor Authentication (para las cuentas para la que un administrador ha requerido su utilización)

- Ver detalles de la cuenta, como el identificador de usuario, el correo electrónico alternativo, los números de teléfono de la oficina y de móvil

- Ver e iniciar aplicaciones basadas en la nube a las que el administrador de Azure AD le ha concedido acceso el administrador. Para obtener más información sobre el Panel de acceso desde la perspectiva del usuario final, vea [Utilización del Panel de acceso](https://msdn.microsoft.com/library/azure/dn756411.aspx).

- Autoadministrar grupos. Más concretamente, puede crear y administrar grupos de seguridad y solicitar la pertenencia a grupos de seguridad en Azure AD. Para obtener más información, vea [Administración de grupos de autoservicio para usuarios en Azure AD](active-directory-accessmanagement-self-service-group-management.md) y [Administración de sus grupos](active-directory-manage-groups.md).




## Acceso al Panel de acceso


Los usuarios pueden acceder al Panel de acceso visitando la siguiente dirección URL en un explorador web: <br> ****http://myapps.microsoft.com**

Si tiene la configuración de marca personalizada en su página de inicio de sesión, puede cargar esta personalización de marca de forma predeterminada anexando el dominio de su organización al final de la dirección URL: <br> ****http://myapps.microsoft.com/contosobuild.com**

En este caso, se puede utilizar cualquier nombre de dominio activo o comprobado que se haya configurado en la pestaña Dominios de su directorio en el portal de administración de Azure, tal y como se muestra en la siguiente captura de pantalla.


![Wingtip Toys][2]


Esta dirección URL se debe distribuir a todos los usuarios que vayan a iniciar sesión en las aplicaciones integradas con Azure AD..
 




## Autenticación

Para llegar al Panel de acceso, un usuario debe autenticarse utilizando una cuenta de organización en Azure AD. <br> Un usuario se puede autenticar en Azure AD directamente. <br> Además, si una organización ha configurado la federación usando ADFS u otras tecnologías, Windows Server Active Directory puede autenticar a los usuarios.

Si un usuario tiene una suscripción para Azure u Office 365 y ha estado utilizando el Portal de administración de Azure o una aplicación de Office 365, verá la lista de aplicaciones sin necesidad de volver a iniciar sesión. A los usuarios que no se han autenticado se les solicitará que inicien sesión utilizando el nombre de usuario y la contraseña de su cuenta en Azure AD. Si la organización ha configurado federación, bastará con escribir el nombre de usuario.

Una vez autenticados, los usuarios podrán interactuar con las aplicaciones que el administrador haya integrado con el directorio. Para obtener información sobre cómo integrar las aplicaciones con Azure AD, vea [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
 




## Requisitos del explorador web

Como mínimo, el Panel de acceso requiere un explorador que admita JavaScript y CSS habilitados. Para que el usuario inicie sesión en aplicaciones utilizando SSO basado en contraseña, debe instalarse la extensión del Panel de acceso en el explorador del usuario. Esta extensión del Panel de acceso se descarga automáticamente cuando un usuario selecciona una aplicación que está configurada para SSO basado en contraseña.

En este momento, la extensión del Panel de acceso está disponible para los exploradores Internet Explorer 8 y posteriores, Chrome y Firefox.





## Compatibilidad para aplicación móvil

Para poder iniciar sesión en aplicaciones con SSO basado en contraseña utilizando dispositivos iOS o Android, los usuarios deben instalar la aplicación móvil Mis aplicaciones publicada por el equipo de Azure Active Directory.





### Mis aplicaciones para Android


Puede usar Mis aplicaciones para Android en cualquier dispositivo Android que tenga la versión 4.1 o superior; tiene esta aplicación disponible hoy mismo en [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.myapps).


![Mis aplicaciones][3]






### Mis aplicaciones para iPhone e iPad


Puede utilizar Mis aplicaciones para iOS en cualquier iPhone o iPad que tenga la versión iOS 7 o superior; tiene esta aplicación disponible hoy mismo en Apple App Store.


![Perfil de aplicaciones][4]




> [AZURE.NOTE] Aquellas aplicaciones que admitan federación con Azure AD (como Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 y otras 70 más) pueden iniciar sesión de forma virtual desde cualquier explorador de cualquier dispositivo sin necesidad de instalar complementos o aplicaciones móviles. Tampoco necesitará usar la aplicación móvil Mis aplicaciones en su dispositivo móvil para utilizar el resto del panel de acceso en [https://myapps.microsoft.com](https://myapps.microsoft.com/).
 


 

## Sugerencias para probar la experiencia de usuario final

Si es un administrador de Azure y ha iniciado sesión en el Portal de administración de Azure utilizando una cuenta en el directorio, iniciará sesión automáticamente en el Panel de acceso con su cuenta de administrador actual. En este caso, puede ver todas las aplicaciones que se han asignado a esta cuenta.

**Para probar con una cuenta de usuario diferente:**

1. Haga clic en el menú de usuario en la esquina superior derecha del portal de Azure o del Panel de acceso y seleccione “**Cerrar sesión**”. Se cerrará su sesión de Azure AD.

2. Vaya al Panel de acceso en ****http://myapps.microsoft.com**.

3. En la página de inicio de sesión, escriba el nombre de usuario y la contraseña para la cuenta de su directorio que desea probar.
 
## Inicio de aplicaciones

Pueden aparecer varios tipos de aplicaciones en el Panel de acceso.
 
### Aplicaciones de Office 365

Si una organización utiliza aplicaciones de Office 365 y el usuario dispone de licencia para ellas, dichas aplicaciones aparecerán en el Panel de acceso.

Cuando un usuario hace clic en un título de aplicación para una aplicación de Office 365, se redirige a esa aplicación e inicia sesión automáticamente.

### Aplicaciones de Microsoft y de terceros configuradas con SSO basado en federación

Estas son aplicaciones que el administrador ha agregado en la sección de Active Directory del Portal de administración de Azure con el modo de inicio de sesión único establecido en “*Inicio de sesión único de Azure AD*”. Un usuario solo verá estas aplicaciones si el administrador le ha concedido explícitamente acceso a ellas.

Cuando un usuario hace clic en un título de aplicación para una de estas aplicaciones, se redirigirá a esa aplicación e iniciará sesión automáticamente.

### SSO basado en contraseña sin aprovisionamiento de identidad

Estas son aplicaciones que el administrador ha agregado en la sección de Active Directory del Portal de administración de Azure con el modo de inicio de sesión único establecido en “*Inicio de sesión único basado en contraseña*”. <br> Todos los usuarios del directorio verán todas las aplicaciones que se han configurado en este modo.

La primera vez que un usuario hace clic en un título de aplicación para una de estas aplicaciones, se le solicitará que instale el complemento SSO de contraseña para Internet Explorer o Chrome, lo que pueden requerir el reinicio del explorador web. Cuando vuelva al Panel de acceso y haga clic en el título de aplicación otra vez, se le solicitará un nombre de usuario y contraseña para la aplicación. Después de introducir el nombre de usuario y la contraseña, estas credenciales se almacenarán de forma segura en Azure AD y se vincularán a su cuenta en Azure AD, y el Panel de acceso iniciará la sesión del usuario automáticamente en la aplicación utilizando estas credenciales.

La próxima vez que un usuario haga clic en el título de aplicación, iniciará sesión automáticamente en la aplicación sin necesidad de volver a introducir las credenciales y sin tener que instalar el complemento SSO de contraseña otra vez.

Si las credenciales de un usuario han cambiado en la aplicación de terceros de destino, el usuario también debe actualizar sus credenciales almacenadas en Azure AD. Para actualizar las credenciales, el usuario debe seleccionar el icono en la parte inferior derecha del título de aplicación y seleccionar “actualizar credenciales” para volver a introducir el nombre de usuario y la contraseña para esa aplicación.

### SSO basado en contraseña con aprovisionamiento de identidad

Estas son aplicaciones que el administrador ha agregado en la sección de Active Directory del Portal de administración de Azure con el modo de inicio de sesión único establecido en “*Inicio de sesión único basado en contraseña*” así como el aprovisionamiento de identidad.

La primera vez que un usuario hace clic en un título de aplicación para una de estas aplicaciones, se le solicitará que instale el complemento SSO de contraseña para Internet Explorer o Chrome, lo que pueden requerir el reinicio del explorador web. Cuando vuelva al Panel de acceso y haga clic en la ventana de aplicación otra vez, iniciará sesión automáticamente en la aplicación.

Algunas aplicaciones pueden requerir que los usuarios cambien su contraseña en el primer inicio de sesión. Si las credenciales de un usuario han cambiado en la aplicación de terceros de destino, el usuario también debe actualizar sus credenciales almacenadas en Azure AD. Para actualizar las credenciales, el usuario debe seleccionar el icono en la parte inferior derecha del título de aplicación y seleccionar “actualizar credenciales” para volver a introducir el nombre de usuario y la contraseña para esa aplicación.

### Aplicación con soluciones SSO existentes

Al configurar un inicio de sesión único para una aplicación, el portal de administración de Azure proporciona una tercera opción, el "Inicio de sesión único existente". Esta opción permite al administrador crear un vínculo a una aplicación y colocarlo en el panel de acceso de los usuarios seleccionados. Por ejemplo, si hay una aplicación que está configurada para autenticar a los usuarios mediante Servicios de federación de Active Directory 2.0, el administrador puede utilizar la opción "Inicio de sesión único existente" para crear un vínculo a éste en el panel de acceso. Cuando los usuarios tienen acceso al vínculo, se autentican mediante Servicios de federación de Active Directory 2.0 o cualquier solución de inicio de sesión único existente que proporcione la aplicación.

##Artículos relacionados

- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
- [Lista de tutoriales sobre cómo integrar aplicaciones SaaS](active-directory-saas-tutorial-list.md)
- [Introducción al acceso a la aplicación Inicio de sesión único y administración con Azure Active Directory](active-directory-appssoaccess-whatis.md)
- [Aprovisionamiento y desaprovisionamiento automático de usuarios para aplicaciones SaaS](active-directory-saas-app-provisioning.md)

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/ic767166.png
[2]: ./media/active-directory-saas-access-panel-introduction/ic767167.png
[3]: ./media/active-directory-saas-access-panel-introduction/ic767168.png
[4]: ./media/active-directory-saas-access-panel-introduction/ic767169.png

<!---HONumber=AcomDC_0211_2016-->