<properties
	pageTitle="¿Qué es acceso a aplicaciones y el inicio de sesión único con Azure Active Directory? | Microsoft Azure"
	description="Utilice Azure Active Directory para habilitar el inicio de sesión único para todas las aplicaciones web y SaaS que necesita para la empresa."
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="asmalser-msft"/>

#¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?

Inicio de sesión único significa poder tener acceso a todas las aplicaciones y los recursos que necesita para hacer negocios, iniciando iniciar sesión una sola vez usando una única cuenta de usuario. Una vez que ha iniciado sesión, puede tener acceso a todas las aplicaciones que necesite sin tener que autenticarse (por ejemplo, escribiendo una contraseña) una segunda vez.

Muchas organizaciones confían en el modelo de software como servicio (SaaS), como Office 365, Box y Salesforce para la productividad del usuario final. Históricamente, el personal de TI ha tenido que crear y actualizar individualmente cuentas de usuario en cada aplicación SaaS, y los usuarios tienen que recordar una contraseña para cada aplicación SaaS.

Azure Active Directory extiende Active Directory local a la nube, permitiendo a los usuarios usar su cuenta profesional principal no solo para iniciar sesión en sus dispositivos unidos a dominios y recursos de la empresa, sino también en todas las aplicaciones web y SaaS necesarias para su trabajo.

Por lo tanto, los usuarios no tienen que administrar varios conjuntos de nombres de usuario y contraseñas, el acceso a sus aplicaciones se puede aprovisionar o anular su aprovisionado en función de sus miembros de grupo de organización y también su estado como empleados. Azure Active Directory introduce controles de seguridad y acceso que permiten administrar de forma centralizada el acceso de los usuarios a través de las aplicaciones SaaS.

Azure AD permite una integración fácil con muchas de las aplicaciones SaaS populares de la actualidad; proporciona administración de identidades y acceso ,y permite a los usuarios realizar inicio de sesión único a aplicaciones directamente, o descubrirlas y ejecutarlas desde un portal como Office 365 o en el panel de acceso de Azure AD.

La arquitectura de la integración consta de los siguientes cuatro bloques principales:

* Inicio de sesión único permite a los usuarios tener acceso a sus aplicaciones SaaS basándose en sus cuentas profesionales de Azure AD. Inicio de sesión único es lo que permite a los usuarios autenticarse en una aplicación mediante su cuenta organizativa única.

* El aprovisionamiento de usuarios permite el aprovisionamiento y el desaprovisionamiento de usuarios en el SaaS de destino basándose en los cambios realizados en Windows Server Active Directory y Azure AD. Una cuenta aprovisionada es lo que permite que un usuario tenga autorización para usar una aplicación, después de haberse autenticado a través de un inicio de sesión único.

* El acceso centralizado a las aplicaciones en el Portal de administración de Azure permite tener un punto único de acceso a las aplicaciones SaaS y su administración, con la capacidad de delegar en cualquier persona la toma de decisiones y la aprobación de acceso a las aplicaciones

* Creación de informes y supervisión unificada en Azure AD

##¿Cómo funciona el inicio de sesión único con Azure Active Directory?

Cuando un usuario "inicia sesión" en una aplicación, pasa por un proceso de autenticación en el que debe demostrar que es quien dice ser. Sin inicio de sesión único, por lo general, esto se hace especificando una contraseña que se almacena en la aplicación y el usuario debe conocer esta contraseña.

Azure AD ofrece tres métodos diferentes para iniciar sesión en aplicaciones:

*	**Inicio de sesión único federado**, que permite a las aplicaciones redirigir a Azure AD las solicitudes de autenticación de usuario en lugar de solicitar su propia contraseña. Este sistema se admite para las aplicaciones compatibles con protocolos como SAML 2.0, WS-Federation u OpenID Connect, y es el modo más enriquecido de inicio de sesión único.

*	**Inicio de sesión único con contraseña**, que permite el almacenamiento seguro de contraseñas de las aplicaciones y reproducirlo usando una extensión de explorador web o aplicación móvil. Aprovecha el proceso de inicio de sesión existente proporcionado por la aplicación, pero permite que un administrador administre las contraseñas y no requiere que el usuario conozca la contraseña.

*	**Inicio de sesión único existente**, que permite a Azure AD aprovechar cualquier inicio de sesión único existente que se haya configurado para la aplicación, pero también permite que estas aplicaciones se vinculen a los portales de panel de acceso a Office 365 o Azure AD, además de preparar informes en Azure AD cuando se lanzan desde allí las aplicaciones.

Una vez que un usuario se ha autenticado en una aplicación, también debe tener aprovisionado un registro de cuenta en la aplicación que le diga dónde están los permisos y el nivel de acceso dentro de la aplicación. El aprovisionamiento de este registro de cuenta puede producirse automáticamente o bien lo puede realizar manualmente un administrador para que el usuario disponga de acceso de inicio de sesión único.

 A continuación se ofrecen más detalles sobre estos modos de inicio de sesión único y sobre el aprovisionamiento.

###Inicio de sesión único federado

El Inicio de sesión único federado permite a los usuarios de su organización iniciar la sesión automáticamente en una aplicación SaaS de terceros en Azure AD utilizando la información de la cuenta del usuario en Azure AD.

En este escenario, cuando ya haya se haya registrado en Azure AD y desee obtener acceso a los recursos controlados por una aplicación SaaS de terceros, la federación elimina la necesidad de que los usuarios vuelvan a autenticarse.

Azure AD puede proporcionar el inicio de sesión único federado con aplicaciones que admitan los protocolos WS-Federation, SAML 2.0 u OpenID.

Consulte también: [Administración de certificados para inicio de sesión único federado](active-directory-sso-certs.md)

###Inicio de sesión único con contraseña

Configurar el inicio de sesión único con contraseña permite a los usuarios de su organización iniciar sesión automáticamente en una aplicación de SaaS de terceros porque Azure AD utiliza la información de cuenta de usuario de la aplicación de SaaS de terceros. Cuando se habilita esta característica, Azure AD recopila y almacena de forma segura la información de cuenta del usuario y la contraseña correspondiente.

Azure AD admite el inicio de sesión único con contraseña para cualquier aplicación basada en la nube cuya página de inicio de sesión esté basada en HTML. Mediante el uso de un complemento de explorador personalizado, AAD automatiza el proceso de inicio de sesión del usuario recuperando de forma segura las credenciales de la aplicación, como el nombre de usuario y la contraseña desde el directorio, y proporciona en nombre del usuario estas credenciales en la página de inicio de sesión de la aplicación. Hay dos posibles casos de uso:

1.	**El administrador administra las credenciales**: los administradores pueden crear y administrar las credenciales de la aplicación, y asignar esas credenciales a los usuarios o grupos que necesitan tener acceso a la aplicación. En estos casos, el usuario final no necesita conocer las credenciales, pero sigue teniendo acceso a la aplicación con inicio de sesión único, simplemente haciendo clic en ella en su panel de acceso o a través de un vínculo que se le proporcione. Esto permite una doble ventaja: la administración del ciclo de vida de las credenciales por el administrador, y la comodidad para los usuarios finales, porque no tienen que recordar ni administrar contraseñas específicas de las aplicaciones. Las credenciales se ocultan al usuario final durante el proceso de inicio de sesión automático; sin embargo, son técnicamente reconocibles por el usuario mediante herramientas de depuración web, y los usuarios y administradores deben seguir las mismas directivas de seguridad como si las presentara directamente el usuario. Las credenciales proporcionadas por el administrador son muy útiles, al proporcionar acceso de cuenta que comparten muchos usuarios, como aplicaciones de medios sociales o de uso compartido de documentos.

2.	**El usuario administra las credenciales**: los administradores pueden asignar aplicaciones a los usuarios finales o grupos y permitir que los usuarios finales escriban sus propias credenciales directamente al tener acceso a la aplicación por primera vez desde su panel de acceso. Esto crea una comodidad para los usuarios finales porque no es necesario que escriban continuamente las contraseñas específicas de las aplicaciones cada vez que quieran tener acceso a ellas. Este caso de uso también puede utilizarse como un paso firme hacia la administración de las credenciales, mediante la cual el administrador puede establecer nuevas credenciales para la aplicación en el futuro sin cambiar la experiencia de acceso a la aplicación del usuario final.

En ambos casos, las credenciales se almacenan en estado cifrado en el directorio y sólo se pasan a través de HTTPS durante el proceso de inicio de sesión automático. Usando el inicio de sesión único con contraseña, Azure AD ofrece una cómoda solución de administración de identidad y acceso para las aplicaciones que no admiten el uso de protocolos de federación.

El SSO con contraseña depende de la extensión del navegador para recuperar información específica de la aplicación y el usuario desde Azure AD de manera segura y aplicarla al servicio. La mayoría de las aplicaciones de SaaS de terceros que son compatibles con Azure AD admite esta característica.

Para el SSO basado en contraseña, los exploradores del usuario final pueden ser:

- Internet Explorer 8, 9 y 10, en Windows 7 o posterior (consulte también la [Guía de implementación de extensión de IE](active-directory-saas-ie-group-policy.md))
- Chrome (en Windows 7 o posterior y en Mac OS X o posterior)
- Firefox 26.0 o posterior (en Windows XP SP2 o posterior y en Mac OS X 10.6 o posterior)

**Nota:** la extensión del SSO con contraseña estará disponible para Edge en Windows 10 cuando Edge admita las extensiones del explorador.

###Inicio de sesión único existente

Al configurar un inicio de sesión único para una aplicación, el portal de administración de Azure proporciona una tercera opción, el "Inicio de sesión único existente". Esta opción permite al administrador crear un vínculo a una aplicación y colocarlo en el panel de acceso de los usuarios seleccionados.

Por ejemplo, si hay una aplicación que está configurada para autenticar a los usuarios mediante Servicios de federación de Active Directory 2.0, el administrador puede utilizar la opción "Inicio de sesión único existente" para crear un vínculo a éste en el panel de acceso. Cuando los usuarios tienen acceso al vínculo, se autentican mediante Servicios de federación de Active Directory 2.0 o cualquier solución de inicio de sesión único existente que proporcione la aplicación.

###Aprovisionamiento de usuarios

Para determinadas aplicaciones, Azure AD permite el aprovisionamiento automático de usuarios y el desaprovisionamiento de cuentas en aplicaciones de SaaS de terceros desde el Portal de administración de Azure, utilizando la información de identidad de Windows Server Active Directory o Azure AD. Cuando un usuario tiene permisos en Azure AD para una de estas aplicaciones, se puede crear (aprovisionar) automáticamente una cuenta en la aplicación SaaS de destino.

Cuando se elimina un usuario o cambia su información en Azure AD, estos cambios también se reflejan en la aplicación SaaS. Esto significa configurar la administración automatizada del ciclo de vida de identidades, lo que permite a los administradores controlar y proporcionar aprovisionamiento y desaprovisionamiento automatizados del aprovisionamiento de aplicaciones SaaS. En Azure AD, esta automatización de la administración del ciclo de vida de identidad está habilitada por el aprovisionamiento de usuarios.

Para obtener más información, consulte [Aprovisionamiento y desaprovisionamiento automático de usuarios para aplicaciones SaaS](active-directory-saas-app-provisioning.md)

##Introducción a la Galería de aplicaciones de Azure AD

¿Ya está listo para comenzar? Para implementar el inicio de sesión único entre Azure AD y las aplicaciones de SaaS que usa la organización, siga estas instrucciones.

###Uso de la Galería de aplicaciones de Azure AD

La [Galería de aplicaciones de Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/) proporciona una lista de las aplicaciones que se sabe que admiten un formulario de inicio de sesión único con Azure Active Directory.

![][1]

Algunas sugerencias para buscar aplicaciones por las funciones que admiten:

*	Azure AD admite el aprovisionamiento automático y el desaprovisionamiento para todas las aplicaciones de tipo "Destacada" en la [Galería de aplicaciones de Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/).

*	[Aquí](http://social.technet.microsoft.com/wiki/contents/articles/20235.azure-active-directory-application-gallery-federated-saas-apps.aspx) se puede consultar una lista de aplicaciones federadas que admiten específicamente el inicio de sesión único federado usando protocolos como SAML, WS-Federation u OpenID Connect.

Una vez que haya encontrado la aplicación, puede comenzar por seguir las instrucciones paso a paso presentadas en la Galería de aplicaciones y en el portal de administración de Azure para habilitar el inicio de sesión único.

###¿La aplicación no está en la Galería?

Si la aplicación no se encuentra en la Galería de aplicaciones de Azure AD, tienes estas opciones:

*	**Agregar una aplicación que no aparezca en la lista**: use la categoría Personalizada de la Galería de aplicaciones del Portal de administración de Azure para conectar una aplicación que usa su organización pero que no está incluida en la lista. Puede agregar cualquier aplicación que admita SAML 2.0 como aplicación federada o cualquier aplicación que tenga una página de inicio de sesión basada en HTML para usar SSO con contraseña. Para obtener más información, consulte este artículo en [Agregar su propia aplicación](active-directory-saas-custom-apps.md).


*	**Agregar una aplicación propia que está desarrollando**: si ha desarrollado la aplicación usted mismo, siga las instrucciones de la documentación para desarrolladores de Azure AD para implementar un inicio de sesión único federado o el aprovisionamiento mediante la API Graph de Azure AD. Para obtener más información, vea estos recursos:
  * [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md)
  * [https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet](https://github.com/AzureADSamples/WebApp-WebAPI-MultiTenant-OpenIdConnect-DotNet)
  * [https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore](https://github.com/AzureADSamples/NativeClient-WebAPI-MultiTenant-WindowsStore)

*	**Solicitar una integración de aplicaciones**: solicite soporte técnico que necesite para la aplicación en el [foro de comentarios de Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory/).

###Mediante el Portal de administración de Azure

Puede utilizar la extensión de Active Directory en el Portal de administración de Azure para configurar el inicio de sesión único en la aplicación. Como primer paso, debe seleccionar un directorio en la sección de Active Directory del portal:

![][2]

Para administrar las aplicaciones SaaS de terceros, puede pasar a la ficha Aplicaciones del directorio seleccionado. Esta vista permite a los administradores:

* Agregar nuevas aplicaciones desde la Galería de Azure AD, así como aplicaciones que se esté desarrollando
* Eliminar aplicaciones integradas
* Administrar las aplicaciones que ya están integradas

Tareas administrativas habituales para una aplicación de SaaS de terceros son:

* Habilitar el inicio de sesión único con Azure AD, mediante SSO con contraseña o, si el SaaS de destino lo permite, el SSO federado
* También tiene la opción de habilitar el aprovisionamiento de usuarios para el aprovisionamiento y el desaprovisionamiento de usuarios (administración del ciclo de vida de identidad)
* Para las aplicaciones que tienen habilitado el aprovisionamiento de usuarios, seleccione los usuarios que tienen acceso a esa aplicación

Para las aplicaciones de la Galería que admiten el inicio de sesión único federado, la configuración normalmente requiere que proporcione valores de configuración adicionales, como certificados y metadatos para crear una confianza federada entre la aplicación de terceros y Azure AD. El Asistente para configuración le guía por los detalles y proporciona fácil acceso a los datos específicos de la aplicación de SaaS e instrucciones.

Para las aplicaciones de la galería que admiten el aprovisionamiento automático de usuarios, esto requiere que otorgue permisos de Azure AD para administrar sus cuentas en la aplicación SaaS. Como mínimo, es necesario que proporcione credenciales que debe usar Azure AD al autenticar a través de la aplicación de destino. Que sea necesario proporcionar opciones de configuración adicionales depende de los requisitos de la aplicación.

##Implementación de aplicaciones integradas en Azure AD a los usuarios

Azure AD proporciona varias maneras personalizables para implementar aplicaciones para los usuarios finales de su organización:

* Panel de acceso de Azure AD
* Iniciador de aplicaciones de Office 365
* Inicio de sesión directo en aplicaciones federadas
* Vínculos profundos a aplicaciones federadas, con contraseña o existentes

Los métodos que elija implementar en su organización son criterio suyo.

###Panel de acceso de Azure AD

El Panel de acceso de https://myapps.microsoft.com es un portal basado en web que permite que los usuarios finales que tengan una cuenta organizativa en Azure Active Directory puedan ver e iniciar aplicaciones basadas en la nube a las que el administrador de Azure AD les haya concedido acceso. Si usted es un usuario final con [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/), también puede utilizar las capacidades de autoservicio de administración de grupos a través del Panel de acceso.

![][3]

El Panel de acceso es independiente del Portal de administración de Azure y no requiere que los usuarios tengan una suscripción de Azure u Office 365.

Para obtener más información sobre el Panel de acceso de Azure AD, consulte la [Introducción al panel de acceso](active-directory-saas-access-panel-introduction.md).

###Iniciador de aplicaciones de Office 365

Para las organizaciones que han implementado Office 365, también aparecerán las aplicaciones asignadas a los usuarios mediante Azure AD en el Portal de Office 365 en https://portal.office.com/myapps. Esto hace que resulte fácil y cómodo para los usuarios de una organización iniciar sus aplicaciones sin tener que utilizar un segundo portal y es la solución de inicio de aplicaciones recomendada para las organizaciones que usan Office 365.

![][4]

Para obtener más información sobre el iniciador de aplicaciones de Office 365, consulte [Hacer que su aplicación aparezca en el iniciador de aplicaciones de Office 365](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher).

###Inicio de sesión directo en aplicaciones federadas

La mayoría de las aplicaciones federadas que admiten SAML 2.0, WS-Federation u OpenID Connect también tienen la capacidad de que los usuarios inicien la sesión en la aplicación y, después, lo hagan a través Azure AD, ya sea mediante el redireccionamiento automático o haciendo clic en un vínculo para iniciar la sesión. Esto se conoce como inicio de sesión por el proveedor de servicios y la mayoría de las aplicaciones federadas incluidas en la Galería de aplicaciones de Azure AD lo admiten (consulte la documentación vinculada desde el Asistente para la configuración de inicio de sesión único de la aplicación en el portal de administración de Azure para obtener más detalles).

![][5]

###Vínculos de inicio de sesión directos para aplicaciones federadas, con contraseña o existentes

Azure AD también admite vínculos directos de inicio de sesión único a las aplicaciones individuales que admiten el inicio de sesión con contraseña, existente y cualquier forma de inicio de sesión único federado.

Estos vínculos son direcciones URL especialmente diseñadas que envían a los usuarios al proceso de inicio de sesión de Azure AD para una aplicación específica sin necesidad de que el usuario la inicie desde el panel de acceso de Azure AD o de Office 365. Estas direcciones URL de inicio de sesión único se pueden encontrar en la ficha Panel de cualquier aplicación previamente integrada en la sección Active Directory del portal de administración de Azure, como se puede ver en la captura de pantalla siguiente.

![][6]

Estos vínculos se pueden copiar y pegar en cualquier sitio donde que desee proporcionar un vínculo de inicio de sesión a la aplicación seleccionada. Podría ser en un mensaje de correo electrónico o en cualquier portal personalizado basado en web que haya configurado para el acceso de los usuarios a la aplicación. Este es un ejemplo de una AD de Azure único inicio de sesión URL directa de Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Similar a direcciones URL específicas de la organización para el panel de acceso, se puede personalizar aún más esta dirección URL mediante la adición de uno de los dominios de activos o comprobados para su directorio tras el dominio myapps.microsoft.com. Esto garantiza que cualquier personalización de marca corporativa se carga inmediatamente en la página de inicio de sesión sin que el usuario tenga que escribir primero su ID de usuario:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Cuando un usuario autorizado hace clic en uno de estos vínculos específicos de aplicaciones, primero ve su página de inicio de sesión organizativa (suponiendo que no ha iniciado todavía la sesión) y después de iniciarla se lo redirigen a su aplicación sin pararse primero en el panel de acceso. Si el usuario no cumple los requisitos previos para tener acceso a la aplicación, como la extensión de explorador para el inicio de sesión único con contraseña, el vínculo le pedirá al usuario que instale la extensión que le falta. La dirección URL del vínculo también permanece constante si cambia la configuración de inicio de sesión única para la aplicación.

Estos vínculos utilizan los mismos mecanismos de control de acceso que el panel de acceso y Office 365, y solo los usuarios o grupos que se han asignado a la aplicación en el portal de administración de Azure podrán autenticarse correctamente. Sin embargo, cualquier usuario que no tenga autorización, verá un mensaje que explica que no se le ha concedido acceso y se proporcionan un vínculo para cargar el panel de acceso y ver las aplicaciones disponibles a las que tienen acceso.

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-appssoaccess-whatis/onlineappgallery.png
[2]: ./media/active-directory-appssoaccess-whatis/azuremgmtportal.png
[3]: ./media/active-directory-appssoaccess-whatis/accesspanel.png
[4]: ./media/active-directory-appssoaccess-whatis/officeapphub.png
[5]: ./media/active-directory-appssoaccess-whatis/workdaymobile.png
[6]: ./media/active-directory-appssoaccess-whatis/deeplink.png

<!---HONumber=AcomDC_0128_2016-->