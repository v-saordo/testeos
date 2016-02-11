<properties
	pageTitle="Acceso a aplicaciones de autoservicio y administración delegada con Azure Active Directory | Microsoft Azure"
	description="En este artículo se describe cómo habilitar el acceso a aplicaciones de autoservicio y administración delegada con Azure Active Directory"
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="asmalser"/>

#Acceso a aplicaciones de autoservicio y administración delegada con Azure Active Directory

Habilitar las funcionalidades de autoservicio para los usuarios finales es un escenario común para TI empresarial. Muchos usuarios, muchas aplicaciones y es posible que la persona mejor informada para tomar decisiones de concesión de acceso no sea el administrador de directorio. A menudo la persona más adecuada para decidir quién puede tener acceso a una aplicación es un responsable de equipo u otro administrador delegado. Pero al final, es el usuario el que utiliza la aplicación y sabe lo que necesitan para poder hacer su trabajo.

El acceso a la aplicación de autoservicio es una característica de [Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/) que permite a los administradores de directorio:

* Permitir a los usuarios solicitar acceso a aplicaciones mediante un icono "Obtener más aplicaciones" en el [Panel de acceso de Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)
* Establecer qué usuarios de las aplicaciones pueden solicitar acceso
* Establecer si los usuarios necesitan aprobación para poder autoasignarse acceso a una aplicación
* Establecer quién debe aprobar las solicitudes y administrar el acceso para cada aplicación

Hoy en día se admite esta funcionalidad para todas las aplicaciones preintegradas y personalizadas que admiten el inicio de sesión único basado en contraseña en la [galería de aplicaciones de Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/), incluidas aplicaciones como Salesforce, Dropbox, Google Apps y mucho más. En este artículo se describe cómo:

* Configurar el acceso a aplicaciones de autoservicio para usuarios finales, incluida la configuración de un flujo de trabajo de aprobación opcional. 
* Delegar la administración del acceso para aplicaciones específicas a las personas más adecuadas de su organización y permitirles utilizar el panel de acceso de Azure AD para aprobar las solicitudes de acceso, asignar el acceso directamente a usuarios seleccionados u, opcionalmente, establecer credenciales para el acceso a aplicaciones cuando está configurado el inicio de sesión único basado en contraseña.


##Configuración del acceso de la aplicación de autoservicio

Para habilitar el acceso a aplicaciones de autoservicio y configurar las aplicaciones que pueden agregar o solicitar los usuarios finales, siga estas instrucciones.

**1:** Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).

**2:** En la sección **Active Directory**, seleccione el directorio y, a continuación, seleccione la pestaña **Aplicaciones**.

**3:** Seleccione el botón **Agregar** y use la opción de galería para seleccionar y agregar una aplicación.

**4:** Una vez agregada la aplicación, aparecerá la página Inicio rápido de la aplicación. Haga clic en **Configurar inicio de sesión único**, seleccione el modo de inicio de sesión único deseado y guarde la configuración.

**5:** A continuación, seleccione la pestaña **Configurar**. Para que los usuarios puedan solicitar acceso a esta aplicación desde el panel de acceso de Azure AD, establezca **Permitir el acceso de autoservicio a las aplicaciones** a **Sí**.

![][1]

**6:** Para configurar opcionalmente un flujo de trabajo de aprobación de las solicitudes de acceso, establezca **Exigir aprobación antes de conceder acceso** a **Sí**. A continuación, se pueden seleccionar uno o más aprobadores mediante el botón **Aprobadores**.

Un aprobador puede ser cualquier usuario de la organización con una cuenta de Azure AD y podría ser responsable del aprovisionamiento de cuentas, las licencias o cualquier otro proceso de negocio que necesite su organización antes de conceder acceso a una aplicación. El aprobador incluso podría ser el propietario del grupo de uno o más grupos de cuentas compartidos y pueden asignar los usuarios a uno de estos grupos para concederles acceso a través de una cuenta compartida.

Si no se requiere ninguna aprobación, los usuarios obtienen instantáneamente la aplicación agregada a su panel de acceso de Azure AD. Esto es adecuado si la aplicación se ha configurado para el [aprovisionamiento automático de usuarios](active-directory-saas-app-provisioning.md), o bien se ha configurado el [modo SSO con contraseña "administrada por el usuario"](active-directory-appssoaccess-whatis.md#password-based-single-sign-on), en el que el usuario ya tiene una cuenta de usuario y conoce la contraseña.

**7:** Si la aplicación se ha configurado para utilizar el inicio de sesión único basado en contraseña, también hay disponible una opción para permitir que el aprobador establezca las credenciales del SSO para cada usuario. Consulte la sección siguiente sobre la administración de acceso delegado para más información.

**8:** Por último, el **Grupo para usuarios que se asignaron a sí mismos** muestra el nombre del grupo que se utilizará para almacenar los usuarios a los que se ha concedido o asignado acceso a la aplicación. El aprobador de acceso se convierte en el propietario de este grupo. Si el nombre del grupo mostrado no existe, se creará automáticamente. Opcionalmente, el nombre del grupo puede establecerse en el nombre de un grupo existente.

**9:** Haga clic en **Guardar** en la parte inferior de la pantalla para guardar la configuración. Ahora los usuarios podrán solicitar acceso a esta aplicación desde el panel de acceso.

**10:** Para probar la experiencia del usuario final, inicie sesión en el panel de acceso de Azure AD de la organización, en https://myapps.microsoft.com, preferiblemente mediante una cuenta que no sea un aprobador de aplicaciones.

**11:** En la pestaña **Aplicaciones**, haga clic en el icono **Obtener más aplicaciones**. Esto muestra una galería de todas las aplicaciones que se han habilitado para el acceso a la aplicación de autoservicio en el directorio, con la posibilidad de buscar y filtrar por categoría de aplicaciones de la izquierda.

**12:** Al hacer clic en una aplicación se inicia el proceso de solicitud. Si no se requiere ningún proceso de aprobación, la aplicación se agregará inmediatamente en la pestaña **Aplicaciones** tras una breve confirmación. Si es necesaria la aprobación, verá un cuadro de diálogo que lo indica y se enviará un correo electrónico a los aprobadores. (Nota rápida: Deberá iniciar sesión en el panel de acceso como no aprobador para ver este proceso de solicitud).

**13:** El correo electrónico proporciona instrucciones al aprobador para iniciar sesión en el panel de acceso de Azure AD y aprobar la solicitud. Una vez que se ha aprobado la solicitud (y el aprobador ha realizado los procesos especiales definidos), el usuario verá la aplicación en su pestaña **Aplicaciones**, dese donde puede iniciar sesión en ella.

##Administración de acceso a aplicaciones delegadas

Un aprobador de acceso a la aplicación puede ser cualquier usuario de la organización que sea la persona más adecuada para aprobar o denegar el acceso a esa aplicación. Este usuario podría ser responsable del aprovisionamiento de cuentas, las licencias o cualquier otro proceso de negocio que necesite su organización antes de conceder acceso a una aplicación.
 
Al configurar el acceso a la aplicación de autoservicio descrito antes, cualquier aprobador de la aplicación asignado verá un icono **Administrar aplicaciones** adicional en el panel de acceso de Azure AD, que muestra las aplicaciones para las que son administrador de acceso. Al hacer clic en una aplicación, se muestra una pantalla con varias opciones.

![][2]

###Aprobar solicitudes

El icono **Aprobar solicitudes** permite que los aprobadores vean todas las aprobaciones pendientes específicas de esa aplicación y redirige a la pestaña Aprobaciones, donde se pueden confirmar o denegar las solicitudes. Tenga en cuenta que el aprobador también recibe mensajes de correo electrónico automatizados siempre que se crea una solicitud que les indica qué hacer.

###Agregar usuarios

El icono **Agregar usuarios** permite a los aprobadores conceder directamente acceso a la aplicación a los usuarios seleccionados. Al hacer clic en este icono, el aprobador verá un cuadro de diálogo que le permite ver y buscar usuarios en su directorio. Al agregar un usuario, la aplicación se muestra en los paneles de acceso de esos usuarios de Azure AD u Office 365. Si es necesario un proceso de aprovisionamiento del usuario manual en la aplicación antes de que el usuario podrá iniciar sesión, el aprobador debe realizarlo antes de asignar el acceso.

###Administrar usuarios

El icono **Administrar usuarios** permite a los aprobadores actualizar o quitar directamente los usuarios que tienen acceso a la aplicación.

###Configuración de credenciales de SSO con contraseña (si corresponde)

El icono **Configurar** solo se muestra si el administrador de TI ha configurado la aplicación para que use el inicio de sesión único basado en contraseña y el administrador ha concedido al aprobador la capacidad de establecer las credenciales de SSO con contraseña como se describió anteriormente. Cuando se selecciona, se presentan varias opciones al aprobador para establecer cómo se propagarán las credenciales a los usuarios asignados:

![][3]

* **Los usuarios inician sesión con sus propias contraseñas**: en este modo, los usuarios asignados saben sus nombres de usuario y contraseñas para la aplicación, y se les pide que los escriban la primera vez que inician sesión en la aplicación. Corresponde al caso de SSO con contraseña, en el que los [usuarios administran las credenciales](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

* **Los usuarios inician sesión automáticamente con cuentas independientes que administro yo**: en este modo, los usuarios asignados no tendrán que escribir o conocer sus credenciales específicas de la aplicación al iniciar sesión en la aplicación. En lugar de ello, el aprobador establece las credenciales para cada usuario después de asignar el acceso mediante el icono **Agregar usuario**. Cuando el usuario hace clic en la aplicación en su panel de acceso u Office 365, iniciará sesión automáticamente con las credenciales establecidas por el aprobador. Corresponde al caso de SSO con contraseña, en el que los [administradores administran las credenciales](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

* **Los usuarios inician sesión automáticamente con una única cuenta que administro yo**: este caso es especial y es adecuado usarlo cuando es necesario conceder acceso a todos los usuarios asignados mediante una sola cuenta compartida. El caso más habitual para usarlo son las aplicaciones de redes sociales, donde una organización tiene una única cuenta de "compañía" y varios usuarios deben realizar actualizaciones en esa cuenta. También corresponde al caso de SSO con contraseña, en el que los [administradores administran las credenciales](active-directory-appssoaccess-whatis.md#password-based-single-sign-on). Sin embargo, después de seleccionar esta opción, se pedirá al aprobador que escriba el nombre de usuario y la contraseña para la única cuenta compartida. Una vez completado, todos los usuarios asignados iniciarán sesión con esta cuenta al hacer clic en la aplicación en sus paneles de acceso de Azure AD u Office 365.

<!--Image references-->
[1]: ./media/active-directory-self-service-application-access/ssaa_admin.PNG
[2]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app.PNG
[3]: ./media/active-directory-self-service-application-access/ssaa_ap_manage_app_config.PNG

<!---HONumber=AcomDC_0128_2016-->