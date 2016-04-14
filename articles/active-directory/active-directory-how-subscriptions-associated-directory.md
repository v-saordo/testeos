<properties
	pageTitle="Asociaciones de las suscripciones de Azure con Azure Active Directory | Microsoft Azure"
	description="El inicio de sesión en Microsoft Azure y temas relacionados, como la relación entre una suscripción de Azure y Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/11/2016"
	ms.author="curtand"/>

# Asociación de las suscripciones de Azure con Azure Active Directory

Este tema contiene información sobre cómo iniciar sesión en Microsoft Azure y temas relacionados, como la relación entre una suscripción de Azure y Azure Active Directory (AD).

## Cuentas que puede utilizar para iniciar sesión
Comencemos con las cuentas que puede utilizar para iniciar sesión. Hay dos tipos: una cuenta Microsoft (anteriormente conocido como Microsoft Live ID) y una cuenta profesional o educativa, que es una cuenta almacenada en Azure AD.

 Cuenta Microsoft | Cuenta de Azure AD
	------------- | -------------
Sistema de identidad de cliente ejecutado por Microsoft | Sistema de identidad empresarial ejecutado por Microsoft
Autenticación en servicios orientados al cliente, como Hotmail y MSN | Autenticación en servicios orientados a la empresa, como Office 365
Los clientes crean sus propias cuentas Microsoft, por ejemplo, cuando se registren para correo electrónico | Las empresas y organizaciones crean y administran sus propias cuentas profesionales y educativas
Las identidades se crean y almacenan en el sistema de cuentas de Microsoft | Las identidades se crean mediante Azure u otro servicio, como Office 365 y se almacenan en una instancia de Azure AD asignada a la organización

Aunque Azure inicialmente permitía el acceso únicamente a los usuarios de cuentas Microsoft, ahora permite acceso a los usuarios de *ambos* sistemas. Esto se hizo haciendo que todas las propiedades de Azure confiaran en Azure AD para la autenticación, haciendo que Azure AD autenticara usuarios de la organización y creando una relación de federación en la que Azure AD confiaba en el sistema de identidad del cliente de cuentas de Microsoft para autenticar usuarios de cliente. Como resultado, Azure AD puede autenticar cuentas Microsoft de tipo "Invitado", así como cuentas de Azure AD "nativas".

Por ejemplo, aquí un usuario con una cuenta Microsoft inicia sesión en el Portal de Azure clásico.

> [AZURE.NOTE]
Para iniciar sesión en el Portal de Azure clásico, msmith@hotmail.com debe tener una suscripción a Azure. La cuenta debe ser un administrador de servicios o un coadministrador de la suscripción.

![][1]

Dado que esta dirección de Hotmail es una cuenta de cliente, se autentica el inicio de sesión en el sistema de identidad de cliente de cuentas Microsoft. El sistema de identidad de Azure AD confía en la autenticación realizada por el sistema de cuentas de Microsoft y emitirá un token de acceso a los servicios de Azure.

## Cómo se relaciona una suscripción de Azure a Azure AD

Cada suscripción de Azure tiene una relación de confianza con una instancia de Azure AD. Esto significa que confía en ese directorio para autenticar usuarios, servicios y dispositivos. Varias suscripciones pueden confiar en el mismo directorio, pero una suscripción confía solo en un único directorio. Puede ver en qué directorio confía su suscripción en la pestaña Configuración. También puede [editar la configuración de suscripción](active-directory-understanding-resource-access.md) para cambiar el directorio en el que confía.

Esta relación de confianza que tiene una suscripción con un directorio es diferente de la relación que tiene una suscripción con todos los demás recursos de Azure (sitios web, bases de datos etc.), que son más parecidos a los recursos secundarios de una suscripción. Si una suscripción expira, el acceso a esos otros recursos asociados a la suscripción también se detiene. Sin embargo, el directorio permanece en Azure y puede asociar otra suscripción a ese directorio y continuar con la administración de los usuarios del directorio.

De forma similar, la extensión de Azure AD que ve en su suscripción no funciona como las demás extensiones del Portal de Azure clásico. Otras extensiones del Portal de Azure clásico tienen como ámbito la suscripción de Azure. Lo que ve en la extensión de Azure AD no varía en función de la suscripción: solo se muestran los directorios según el usuario que inicia la sesión.

Todos los usuarios tienen un único directorio de inicio que los autentica, pero también pueden ser invitados en otros directorios. En la extensión de Azure AD, verá cada directorio del que es miembro su cuenta de usuario. No aparecerá ningún directorio que no sea miembro de la cuenta. Un directorio puede emitir tokens para las cuentas profesionales y educativas en Azure AD o para usuarios de cuenta Microsoft (ya que Azure AD está federado con el sistema de cuentas Microsoft).

Este diagrama muestra una suscripción para Michael Smith después de haber iniciado sesión con una cuenta de trabajo para Contoso.

![][2]

## Administración de una suscripción y un directorio
Los roles administrativos para una suscripción de Azure administran los recursos vinculados a la suscripción de Azure. Estos roles y procedimientos recomendados para administrar la suscripción se tratan en [Asignación de roles de administrador en Azure Active Directory](active-directory-assign-admin-roles.md).

De forma predeterminada, se le asigna el rol de administrador de servicios cuando se registra. Si otras personas necesitan iniciar sesión y obtener acceso a servicios con la misma suscripción, puede agregarlas como coadministradores. El Administrador de servicios y los coadministradores pueden ser cuentas Microsoft o cuentas profesionales o educativas desde el directorio asociado a la suscripción de Azure.

Azure AD tiene un conjunto diferente de roles administrativos para gestionar las características relacionadas con la identidad y el directorio. Por ejemplo, el administrador global de un directorio puede agregar usuarios y grupos al directorio o requerir la autenticación multifactor para los usuarios. Los usuarios que crean un directorio se asignan al rol de administrador global y pueden asignar roles de administrador a otros usuarios.

Al igual que con los administradores de suscripciones, los roles administrativos de Azure AD pueden ser cuentas Microsoft o cuentas profesionales o educativas. Otros servicios, como Office 365 y Microsoft Intune, también usan los roles administrativos de Azure AD. Para obtener más información, consulte [Asignación de roles de administrador](active-directory-assign-admin-roles.md).

Sin embargo, lo importante aquí es que los administradores de suscripciones de Azure y los administradores de directorios de Azure AD son dos conceptos diferentes. Los administradores de suscripciones de Azure pueden administrar recursos de Azure y pueden ver la extensión de Active Directory en el Portal de Azure clásico (porque dicho portal es un recurso de Azure). Los administradores de directorios pueden administrar propiedades en el directorio.

Una persona puede tener ambos roles, pero no es obligatorio. Un usuario puede asignarse al rol de administrador global de directorios, pero no asignarlo como administrador de servicios o coadministrador de una suscripción de Azure. Sin ser un administrador de la suscripción, este usuario no puede iniciar sesión en el Portal de Azure clásico. Sin embargo, el usuario podría realizar tareas de administración de directorio mediante otras herramientas como Azure AD PowerShell o Centro de administración de Office 365.

## ¿Por qué no puedo administrar el directorio con mi cuenta de usuario actual?

A veces, un usuario puede intentar iniciar sesión en el Portal de Azure clásico mediante una cuenta profesional o educativa antes de registrarse para una suscripción de Azure. En este caso, el usuario recibirá un mensaje que indicará que no hay ninguna suscripción para esa cuenta. El mensaje incluirá un vínculo para iniciar una suscripción de prueba gratuita.

Después de registrarse para la evaluación gratuita, el usuario verá el directorio de la organización en el Portal de Azure clásico pero no podrá administrarlo (es decir, no podrá agregar usuarios ni editar las propiedades de usuario existentes) porque el usuario no es un administrador global de directorios. La suscripción permite al usuario usar el Portal de Azure clásico y ver la extensión de Active Directory, pero se necesitan los permisos adicionales de administrador global para administrar el directorio.

## Uso de una cuenta profesional o educativa para administrar una suscripción a Azure que se creó mediante una cuenta Microsoft

Como práctica recomendada, debe [suscribirse a Azure como organización](sign-up-organization.md) y utilizar una cuenta profesional o educativa para administrar recursos en Azure. Se prefieren cuentas profesionales o educativas porque la organización que las haya expedido pueden administrarlas centralmente, tienen más características que las cuentas Microsoft y las autentica directamente Azure AD. La misma cuenta proporciona acceso a otros servicios en línea de Microsoft que se ofrecen a empresas y organizaciones, como Office 365 o Microsoft Intune. Si ya tiene una cuenta que se utiliza con estas otras propiedades, es probable que deba usar la misma cuenta con Azure. También tendrá una instancia de Active Directory que respaldará esas propiedades en las que desee que confíe su suscripción de Azure.

También es posible administrar las cuentas profesionales y educativas de más formas que una cuenta de Microsoft. Por ejemplo, un administrador puede restablecer la contraseña de una cuenta profesional o educativa o requerir una autenticación multifactor para ella.

En algunos casos, puede que desee que un usuario de la organización pueda administrar recursos que están asociados a una suscripción de Azure para una cuenta Microsoft de cliente. Para obtener más información acerca de cómo realizar una transición para tener distintos directorios o suscripciones de gestión de cuentas, consulte [Administración del directorio de su suscripción de Office 365 en Azure](#manage-the-directory-for-your-office-365-subscription-in-azure).


## Inicio de sesión cuando utiliza el correo electrónico profesional para su cuenta Microsoft

Si en algún momento en el pasado ha creado una cuenta Microsoft de cliente utilizando su correo electrónico profesional como un identificador de usuario, verá una página que le solicitará que realice una selección a partir del sistema de la cuenta Microsoft Azure o del sistema de la cuenta Microsoft.

![][3]

Dispone de cuentas de usuario con el mismo nombre, una en Azure AD y otra en el sistema de cuenta de Microsoft de cliente. Debe elegir la cuenta que está asociada a la suscripción de Azure que desee utilizar. Si se produce un error que indica que no existe una suscripción para este usuario, es posible que haya elegido la opción incorrecta. Cierre la sesión y vuelva a intentarlo. Para obtener más información acerca de los errores que pueden impedir el inicio de sesión, vea [Solución de errores: "No podemos encontrar las suscripciones asociadas a su cuenta"](https://social.msdn.microsoft.com/Forums/es-ES/f952f398-f700-41a1-8729-be49599dd7e2/troubleshooting-we-were-unable-to-find-any-subscriptions-associated-with-your-account-errors-in?forum=windowsazuremanagement).

## Administración del directorio para la suscripción de Office 365 en Azure

Supongamos que se ha registrado en Office 365 antes de suscribirse a Azure. Ahora, desea administrar el directorio para la suscripción de Office 365 en el Portal de Azure clásico. Hay dos formas de hacerlo, dependiendo de si se ha registrado en Azure o no.

### No tengo una suscripción de Azure

En este caso, solo necesitará [registrarse en Azure](sign-up-organization.md) usando la misma cuenta profesional o educativa que usó para iniciar sesión en Office 365. Se rellenará previamente la información relevante de la cuenta de Office 365 en el formulario de registro de Azure. Su cuenta se asignará al rol de administrador de servicios de la suscripción.

### Tengo una suscripción de Azure con mi cuenta Microsoft

Si se ha suscrito a Office 365 con una cuenta profesional o educativa y, a continuación, se ha suscrito a Azure con una cuenta Microsoft, tiene dos directorios: uno de tipo profesional o educativo y un directorio predeterminado que se creó cuando se suscribió a Azure.

Para administrar ambos directorios en el Portal de Azure clásico, siga estos pasos.

> [AZURE.NOTE]
Solo se pueden completar estos pasos cuando un usuario inicie sesión con una cuenta Microsoft. Si el usuario ha iniciado sesión con una cuenta profesional o educativa, la opción **Usar directorio existente** no estará disponible porque se puede autenticar una cuenta profesional o educativamente únicamente mediante su directorio particular (es decir, el directorio donde se almacena la cuenta profesional o educativa, y cuyo propietario sea el trabajo o la escuela).

1. Inicie sesión en el Portal de Azure clásico con su cuenta Microsoft.

2. Haga clic en **Nuevo** > **Servicios de aplicaciones** > **Active Directory** > **Directorio** > **Creación personalizada**.

3. Haga clic en **Usar directorio existente** y active **y Estoy listo para cerrar la sesión ahora. ** y haga clic en la marca de verificación para completar la acción.

4. Inicie sesión en el Portal de Azure clásico con una cuenta que tenga derechos de administrador global para el directorio profesional o educativo.

5. Cuando se le pregunte **¿Usar el directorio de Contoso con Azure?**, haga clic en **Continuar**.

6. Haga clic en **Cerrar sesión ahora**.

7. Inicie sesión de nuevo en el Portal de Azure clásico con la cuenta Microsoft. Ambos directorios aparecerán en la extensión de Active Directory.


## Pasos siguientes

- Para más información acerca de cómo cambiar los administradores de una suscripción de Azure, consulte [Incorporación o cambio de roles de administrador de Azure](../billing-add-change-azure-subscription-administrator.md)

- Para más información acerca de cómo se controla el acceso a los recursos en Microsoft Azure, consulte [Descripción de acceso a los recursos de Azure](active-directory-understanding-resource-access.md)

- Para más información sobre cómo asignar roles en Azure AD, consulte [Asignación de roles de administrador en Azure Active Directory (Azure AD)](active-directory-assign-admin-roles.md)

- [Registro en Azure como una organización](sign-up-organization.md)


<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

<!---HONumber=AcomDC_0218_2016-->