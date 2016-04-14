<properties 
    pageTitle="Configuración del inicio de sesión único en aplicaciones que no están en la Galería de aplicaciones de Azure Active Directory | Microsoft Azure" 
    description="Aprenda cómo conectar de forma autónoma aplicaciones a Azure Active Directory mediante SAML y SSO basado en contraseña" 
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

#Configuración del inicio de sesión único en aplicaciones que no están en la Galería de aplicaciones de Azure Active Directory

En este artículo se trata de una característica que permite a los administradores configurar un inicio de sesión único a aplicaciones que no están presentes en la Galería de aplicaciones de Azure Active Directory *sin escribir código*. Esta característica se ha publicado en Technical Preview el 18 de noviembre de 2015 y se incluye en [Azure Active Directory Premium](active-directory-editions.md). Si por el contrario desea obtener instrucciones para desarrolladores sobre cómo integrar aplicaciones personalizadas con Azure AD a través de código, consulte [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md).

La Galería de aplicaciones de Azure Active Directory proporciona una lista de las aplicaciones que se sabe que admiten un formulario de inicio de sesión único en Azure Active Directory, tal como se describe en [este artículo](active-directory-appssoaccess-whatis.md). Una vez que un especialista en TI o un integrador de sistemas de la organización haya encontrado la aplicación que desea conectar, puede empezar por seguir las instrucciones detalladas que se proporcionan en el Portal de administración de Azure para habilitar el inicio de sesión único.

Los clientes con licencias [Azure Active Directory Premium](active-directory-editions.md) también obtienen estas funcionalidades adicionales:

* Integración de autoservicio de cualquier aplicación que admita proveedores de identidades SAML 2.0
* Integración de autoservicio de cualquier aplicación web que tenga una página de inicio de sesión basada en HTML que use [SSO basado en contraseña](active-directory-appssoaccess-whatis.md/#password-based-single-sign-on)
* Conexión autoservicio de las aplicaciones que usan el protocolo SCIM para el aprovisionamiento de usuarios ([se describe aquí](active-directory-scim-provisioning))
* Capacidad para agregar vínculos a cualquier aplicación del [iniciador de aplicaciones de Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) o del [panel de acceso de Azure AD](active-directory-appssoaccess-whatis.md/#deploying-azure-ad-integrated-applications-to-users)

Aquí puede incluir no solo las aplicaciones SaaS que usa, pero que aún ha integrado en la Galería de aplicaciones de Azure AD, sino también las aplicaciones web de terceros que la organización ha implementado en los servidores que controla, ya sea en la nube o locales.

Estas funcionalidades, también conocidas como *plantillas de integración de aplicaciones*, proporcionan puntos de conexión basados en estándares para aplicaciones que admiten SAML, SCIM o autenticación basada en formularios, e incluyen opciones y configuraciones flexibles para compatibilidad con un amplio número de aplicaciones.

##Adición de una aplicación que no figura en la lista

Para conectar una aplicación con una plantilla de integración de aplicaciones, inicie sesión en el Portal de administración de Azure con su cuenta de administrador de Azure Active Directory y vaya a la sección **Active Directory > [directorio] > Aplicaciones**, seleccione **Agregar** y, a continuación, **Agregar una aplicación de la galería**.

![][1]

En la Galería de aplicaciones, se puede agregar una aplicación que no figura en la lista mediante la categoría **Personalizado** de la izquierda, o bien seleccionando el vínculo **Agregar una aplicación que no figura en la lista** que se muestra en los resultados de búsqueda si no se encontró la aplicación deseada. Después de escribir el nombre de la aplicación, puede configurar las opciones y el comportamiento de inicio de sesión único.

**Sugerencia rápida**: Como procedimiento recomendado, use la función de búsqueda para comprobar si la aplicación ya existe en la Galería de aplicaciones. Si se encuentra la aplicación y su descripción menciona "inicio de sesión único", la aplicación ya es compatible con un inicio de sesión único federado.

![][2]

La adición de una aplicación de esta forma supone una experiencia muy parecida a la disponible para las aplicaciones preintegradas. Para empezar, seleccione **Configurar inicio de sesión único**. La siguiente pantalla presenta las tres opciones siguientes para configurar el inicio de sesión único, que se describen en las secciones siguientes.

![][3]


##Inicio de sesión único de Azure AD

Seleccione esta opción para configurar la autenticación basada en SAML para la aplicación. Esto requiere que la aplicación sea compatible con SAML 2.0 y se debe recopilar información acerca de cómo usar las capacidades de SAML de la aplicación antes de continuar. Después de seleccionar **Siguiente**, se le pedirá que escriba tres direcciones URL diferentes correspondientes a los puntos de conexión SAML de la aplicación.

![][4]

Los iconos de información sobre herramientas del cuadro de diálogo proporcionan detalles acerca de lo que es cada dirección URL y cómo se utiliza. Después de especificar dichas direcciones, haga clic en **Siguiente** para pasar a la pantalla siguiente. Esta pantalla proporciona información sobre lo que es preciso configurar en la propia aplicación para que pueda aceptar un token SAML de Azure AD.

![][5]

Los valores requeridos variarán en función de la aplicación, así que es conveniente que compruebe la documentación de SAML de la aplicación. Las URL de los servicios de **inicio de sesión** y **cierre de sesión** se resuelven en el mismo punto de conexión, que es el punto de conexión de control de solicitudes de SAML de su instancia de Azure AD. La URL del emisor es el valor que aparece como "Emisor" en el token SAML que se emite para la aplicación.

Después de configurar la aplicación, haga clic en el botón **Siguiente** y, a continuación, en **Completado** para cerrar el cuadro de diálogo.

##Asignación de usuarios y grupos a una aplicación de SAML 

Tras configurar la aplicación para que use Azure AD como proveedor de identidades basado en SAML, ya casi está lista para probarla. Como control de seguridad, Azure AD no emitirá un token que les permita iniciar sesión en la aplicación, a menos que se les concediera acceso mediante Azure AD. A los usuarios se les puede conceder acceso directamente o a través del grupo al que pertenecen.

Para asignar un usuario o grupo a una aplicación, haga clic en el botón **Asignar usuarios**. Seleccione el usuario o grupo que desea asignar y, a continuación, haga clic en el botón **Asignar**.

![][6]

La asignación de un usuario permitirá a Azure AD emitir un token para el usuario, así como hacer que aparezca un icono de la aplicación aparece en el panel de acceso del usuario. Si el usuario usa Office 365, también aparecerá un icono de la aplicación en el iniciador de aplicaciones de Office 365.

Para cargar un logotipo de icono de la aplicación, pulse el botón **Cargar logotipo** en la pestaña **Configurar** de la aplicación.

###Personalización de las notificaciones emitidas en el token SAML 

Cuando un usuario se autentique en la aplicación, Azure AD emitirá un token SAML a la aplicación que contiene información (o notificaciones) sobre el usuario que lo identifica de forma única. De forma predeterminada, dicha información incluye el nombre de usuario, la dirección de correo electrónico, el nombre y los apellidos del usuario.

Las notificaciones enviadas en el token SAML a la aplicación se pueden ver o editar en la pestaña **Atributos**.

![][7]

Hay dos posibles razones por las que podría tener la necesidad de editar las notificaciones emitidas en el token SAML: •La aplicación se escribió para requerir un conjunto diferente de identificadores URI de notificaciones o valores de notificaciones •La aplicación se ha implementado de tal forma que requiere que la notificación NameIdentifier no sea el nombre de usuario (conocido también como nombre principal de usuario) almacenado en Azure Active Directory.

Para obtener información acerca de cómo agregar y editar notificaciones de estos escenarios, consulte este [artículo sobre la personalización de notificaciones](active-directory-saml-claims-customization.md).

###Prueba de la aplicación SAML 

Una vez que las direcciones URL de SAML y el certificado se han configurado en Azure AD y en la aplicación, los usuarios o grupos se han asignado a la aplicación en Azure y las notificaciones se han revisado y editado si es necesario, el usuario estará listo para iniciar sesión en la aplicación.

Para realizar la prueba, solo es preciso iniciar sesión en el panel de acceso de Azure AD en https://myapps.microsoft.com con la cuenta de usuario asignada a la aplicación y, a continuación, haga clic en el icono de la aplicación iniciar el proceso de inicio de sesión único. Como alternativa, puede navegar directamente a la URL de inicio de sesión de la aplicación e iniciar sesión desde ahí.

Para ver sugerencias sobre depuración, consulte este [artículo sobre cómo depurar el inicio de sesión único basado en SAML en aplicaciones](active-directory-saml-debugging.md)

##Inicio de sesión único con contraseña

Seleccione esta opción para configurar [el inicio de sesión único basado en contraseña](active-directory-appssoaccess-whatis.md) para una aplicación web que tiene una página de inicio de sesión HTML. El SSO basado en contraseña, también conocido como almacenamiento de contraseñas, permite administrar el acceso y las contraseñas de los usuarios en aplicaciones web que no admiten la federación de identidades. También es útil para escenarios en los que varios usuarios necesitan compartir una sola cuenta, como las cuentas de aplicaciones de redes sociales de la organización.

Después de seleccionar **Siguiente**, se le solicitará que escriba la dirección URL de página de inicio de sesión basada en web de la aplicación. Tenga en cuenta que dicha página debe incluir los campos en que se especifican el nombre de usuario y la contraseña. Una vez especificados, Azure AD inicia un proceso para analizar la página de inicio de sesión para detectar si se han escrito un nombre de usuario y una contraseña. Si el resultado del proceso no es satisfactorio, le guía a través de un proceso alternativo de instalación de una extensión de explorador (requiere Internet Explorer, Chrome o Firefox) que le permitirá capturar manualmente los campos.

Una vez que se captura la página de inicio de sesión, es posible asignar usuarios y grupos, las directivas de credenciales se pueden establecer como [aplicaciones de SSO con contraseña](active-directory-appssoaccess-whatis.md) normales.

Nota: Para cargar un logotipo de icono de la aplicación, pulse el botón **Cargar logotipo** en la pestaña **Configurar** de la aplicación.

##Inicio de sesión único existente

Seleccione esta opción si desea agregar un vínculo a una aplicación en el panel de acceso de Azure AD o en el Portal de Office 365 de la organización. Esto se puede usar para agregar vínculos a aplicaciones web personalizadas que actualmente utilizan los Servicios de federación de Azure Active Directory (u otro servicio de federación), en lugar de Azure AD para la autenticación. O bien, puede agregar vínculos profundos a páginas específicas de SharePoint o a otras páginas web que desee que aparezcan en los paneles de acceso de su usuario.

Después de seleccionar **Siguiente**, se le solicitará que escriba la dirección URL de la aplicación con la que se va a establecer el vínculo. Una vez completada la operación, ya es posible asignar usuarios y grupos a la aplicación, lo que provoca que la aplicación aparezca en el [iniciador de aplicaciones de Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) o en el [panel de acceso de Azure AD](active-directory-appssoaccess-whatis.md/#deploying-azure-ad-integrated-applications-to-users) de dichos usuarios.

Nota: Para cargar un logotipo de icono de la aplicación, pulse el botón **Cargar logotipo** en la pestaña **Configurar** de la aplicación.

## Artículos relacionados

- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
- [Personalización de notificaciones emitidas en el token SAML para aplicaciones previamente integradas en Azure Active Directory](active-directory-saml-claims-customization.md)
- [Cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png

<!---HONumber=AcomDC_0218_2016-->