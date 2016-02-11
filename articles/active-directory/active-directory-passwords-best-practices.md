<properties 
	pageTitle="Procedimientos recomendados: Administración de contraseñas de Azure AD | Microsoft Azure" 
	description="Procedimientos recomendados de implementación y uso, ejemplo de documentación de usuario final y guías de aprendizaje para la administración de contraseñas en Azure Active Directory." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="asteen"/>

# Implementación de la Administración de contraseñas y formación a los usuarios para que la utilicen
Después de habilitar el restablecimiento de contraseña, la siguiente tarea es conseguir que los usuarios de su organización utilicen este servicio. Para ello, deberá asegurarse de que los usuarios están configurados para usar el servicio correctamente y también de que los usuarios tienen la formación que necesitan para administrar correctamente sus propias contraseñas. En este artículo se explican los conceptos siguientes:

* [**Configuración de los usuarios para la Administración de contraseñas**](#how-to-get-users-configured-for-password-reset)
  * [Configuración de una cuenta para el restablecimiento de la contraseña](#what-makes-an-account-configured)
  * [Maneras de rellenar los datos de autenticación personalmente](#ways-to-populate-authentication-data)
* [**Las mejores maneras de aplicar el restablecimiento de contraseña por toda su organización**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [Implementación basada en correo electrónico + ejemplo de comunicaciones por correo electrónico](#email-based-rollout)
  * [Creación de su propio portal personalizado de administración de contraseñas para los usuarios](#creating-your-own-password-portal)
  * [Uso del registro exigido para obligar a los usuarios a registrarse en el inicio de sesión](#using-enforced-registration)
  * [Carga de datos de autenticación para cuentas de usuario](#uploading-data-yourself)
* [**Ejemplo de usuario y materiales de formación de soporte técnico (disponible próximamente)**](#sample-training-materials)

## Configuración de los usuarios para el restablecimiento de contraseña
En esta sección se describen diversos métodos por los que se garantiza que todos los usuarios de su organización pueden utilizar el autoservicio de restablecimiento de contraseña de forma eficaz en caso de que olviden su contraseña.

### Configuración de una cuenta
Antes de que un usuario pueda utilizar el restablecimiento de contraseña, deben cumplirse **todas** las siguientes condiciones:

1.	El restablecimiento de la contraseña debe estar habilitado en el directorio. Aprenda a habilitar el restablecimiento de contraseña en [Permitir a los usuarios restablecer sus contraseñas de Azure AD](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) o [Permitir a los usuarios restablecer o cambiar sus contraseñas de AD](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords).
2.	El usuario debe tener licencia.
 - En el caso de los usuarios en la nube, el usuario debe tener asignada **cualquier licencia de Office 365 de pago** o una licencia de nivel **Básico** o **Premium de AAD**.
 - En el caso de los usuarios locales (federados o sincronizados mediante hash), el usuario **debe tener asignada una licencia Premium de AAD**.
3.	El usuario debe tener el **conjunto mínimo de datos de autenticación definidos** de conformidad con la directiva actual de restablecimiento de contraseña.
 - Los datos de autenticación se consideran definidos si el campo correspondiente del directorio contiene datos con formato correcto.
 - Un conjunto mínimo de datos de autenticación se define como **al menos una** de las opciones de autenticación habilitadas si se configura una directiva de una puerta, o **al menos dos** de las opciones autenticación habilitadas si se configura una directiva de dos puertas.
4.	Si el usuario está utilizando una cuenta local, debe habilitarse y activarse la [escritura diferida de contraseñas](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords).

### Maneras de rellenar los datos de autenticación
Tiene varias opciones para especificar los datos de los usuarios de su organización que se usarán para restablecer la contraseña.

- Editar usuarios en el [Portal de administración de Azure](https://manage.windowsazure.com) o en el [Portal de administración de Office 365](https://portal.microsoftonline.com)
- Usar Sincronización de Azure AD para sincronizar las propiedades de usuario en Azure AD desde un dominio de Active Directory local
- [Siga los pasos indicados aquí](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) para usar Windows PowerShell para editar las propiedades de usuario.
- Permitir a los usuarios que registren sus propios datos guiándolos al portal de registro en [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
- Requerir a los usuarios que se registren para el restablecimiento de contraseña al iniciar sesión en su cuenta de Azure AD mediante el establecimiento de la opción de configuración [**¿Desea exigir a los usuarios que se registren al iniciar sesión?**](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) en **Sí**.

No es necesario que los usuarios se registren en el restablecimiento de contraseña para que el sistema funcione. Por ejemplo, si ya tiene números de teléfono móvil o de oficina en el directorio local, puede sincronizarlos en Azure AD y de este modo se utilizarán para restablecer la contraseña automáticamente.

También puede obtener más información sobre [cómo usa los datos el restablecimiento de la contraseña](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) y [cómo puede rellenar los campos individuales de autenticación con PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).

## ¿Cuál es la mejor forma de aplicar el restablecimiento de contraseña para los usuarios?
Estos son los pasos de implementación generales para restablecer la contraseña:

1.	Habilite el restablecimiento de contraseña; para ello, vaya a la pestaña **Configurar** en el [Portal de administración de Azure](https://manage.windowsazure.com) y seleccione **Sí** en la opción **Usuarios habilitados para restablecer la contraseña**.
2.	Asigne las licencias correspondientes a cada uno de los usuarios a los que desea conceder el restablecimiento de contraseña; para ello, vaya a la pestaña **Licencias** del [Portal de administración de Azure](https://manage.windowsazure.com).
3.	Tiene la opción de restringir el restablecimiento de contraseña a un grupo de usuarios para aplicar la característica lentamente con el tiempo; para ello, establezca el botón de alternancia **Restringir el acceso al restablecimiento de contraseña** en **Sí** y seleccione un grupo de seguridad para habilitar el restablecimiento de contraseña (tenga en cuenta que todos estos usuarios deben tener licencias asignadas).
4.	Indique a los usuarios que utilicen el restablecimiento de contraseña enviándoles un correo electrónico en el que se les pida que se registren, habilitando el registro exigido en el panel de control o cargando usted mismo los datos de autenticación apropiados para esos usuarios a través de la sincronización de directorios, PowerShell o el [Portal de administración de Azure](https://manage.windowsazure.com). A continuación se proporcionan más detalles al respecto.
5.	Con el tiempo, revise los usuarios que se han registrado; para ello, desplácese hasta la pestaña Informes y consulte el informe [**Actividad de registro de restablecimiento de contraseña**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity).
6.	Una vez que se haya registrado un buen número de usuarios, compruebe cómo hacen uso del restablecimiento de contraseña; para ello, desplácese hasta la pestaña Informes y consulte el informe [**Actividad de restablecimiento de contraseña**](active-directory-passwords-get-insights.md#view-password-reset-activity).

Hay varias maneras de informar a los usuarios de que pueden registrarse en el restablecimiento de contraseña y utilizar este servicio en su organización. Se explican a continuación.

### Implementación basada en correo electrónico
Quizás el enfoque más sencillo para informar a los usuarios sobre el registro en el restablecimiento de contraseña o el uso de este servicio es enviarles un correo electrónico pidiéndoles que lo hagan. A continuación se muestra una plantilla que puede utilizar para ello. Reemplace los colores y logotipos por otros de su elección para personalizarla de manera que se ajuste a sus necesidades.

  ![][001]

Puede [ descargar la plantilla de correo electrónico aquí](http://1drv.ms/1xWFtQM).

### Creación de su propio portal de contraseñas
Una estrategia que funciona bien con grandes clientes que implementan funcionalidades de administración de contraseñas es crear un único "portal de contraseñas" que los usuarios pueden usar para administrar todos los aspectos relacionados con sus contraseñas en un solo lugar.

Muchos de nuestros clientes más grandes eligen crear una entrada DNS raíz, como https://passwords.contoso.com con vínculos al portal de restablecimiento de contraseñas de Azure AD, el portal de registro de restablecimiento de contraseñas y las páginas de cambio de contraseña. De este modo, en las comunicaciones por correo electrónico o los folletos que envía, puede incluir una única dirección URL fácil de recordar que los usuarios pueden visitar cuando tienen un momento para empezar a usar el servicio.

Para comenzar aquí, hemos creado una página sencilla que usa los últimos paradigmas de diseño UI que funcionan, y que sirve para todos los exploradores y dispositivos móviles.

  ![][007]
  
Puede [descargar la plantilla del sitio web aquí](https://github.com/kenhoff/password-reset-page). Se recomienda personalizar el logotipo y los colores de acuerdo con las necesidades de su organización.

### Uso del registro exigido
Si desea que sean los usuarios quienes se registren para el restablecimiento de contraseña, también puede obligarles a registrarse al iniciar sesión en el panel de acceso, en [http://myapps.microsoft.com](http://myapps.microsoft.com). Puede habilitar esta opción desde la pestaña **Configurar** del directorio habilitando la opción **Requerir que los usuarios se registren al iniciar sesión en el panel de acceso** .

Opcionalmente, también puede definir si se les pedirá o no que se vuelvan a registrar transcurrido un período de tiempo configurable mediante la modificación de la opción **Antelación con la que los usuarios deben confirmar sus datos de contacto, en días** en un valor distinto de cero. Consulte [Personalización del comportamiento de la administración de contraseñas de usuario](active-directory-passwords-customize.md#password-management-behavior) para obtener más información.

  ![][002]

Después de habilitar esta opción, cuando los usuarios inicien sesión en el panel de acceso, verán un menú emergente que les informa de que su administrador ha requerido que verifiquen su información de contacto. Pueden usarlo para restablecer su contraseña si alguna vez pierden acceso a su cuenta.

  ![][003]

Al hacer clic en **Comprobar ahora**, el usuario llegará al **portal de registro de restablecimiento de contraseña** en [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) y se le pedirá que se registre. El registro mediante este método se puede descartar haciendo clic en el botón **Cancelar** o cerrando la ventana, pero los usuarios seguirán viendo este recordatorio cada vez que inicien sesión en tanto no se registren.

  ![][004]

### Carga de los datos personalmente
Si desea cargar los datos de autenticación personalmente, no es necesario que los usuarios se registren en el restablecimiento de contraseña para poder restablecer sus contraseñas. Siempre que los datos de autenticación definidos en la cuenta de los usuarios se correspondan con la directiva de restablecimiento de contraseña, estos usuarios podrán restablecer sus contraseñas.

Para obtener información sobre las propiedades que puede configurar a través de AAD Connect o Windows PowerShell, consulte [Qué datos se utilizan por el restablecimiento de contraseña](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

Puede cargar los datos de autenticación a través del [Portal de administración de Azure](https://manage.windowsazure.com) siguiendo estos pasos:

1.	Diríjase a su directorio en la **extensión de Active Directory** en el [Portal de administración de Azure](https://manage.windowsazure.com).
2.	Haga clic en la pestaña **Usuarios**.
3.	Seleccione en la lista el usuario que le interese.
4.	En la primera pestaña, verá **Correo electrónico alternativo**, que puede utilizarse como una propiedad para habilitar el restablecimiento de contraseña. 

    ![][005]

5.	Haga clic en la pestaña **Información laboral**.
6.	En esta página, encontrará el **Teléfono de la oficina**, el **Teléfono móvil**, el **Teléfono de autenticación** y el **Correo electrónico de autenticación**. Estas propiedades también se pueden establecer para permitir a un usuario restablecer su contraseña. 

    ![][006]

Consulte [Qué datos se utilizan por el restablecimiento de contraseña](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) para ver cómo se puede utilizar cada una de estas propiedades.

Consulte [Cómo tener acceso a los datos de restablecimiento de la contraseña de los usuarios desde PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) para ver cómo se puede leer y establecer estos datos con PowerShell.

## Ejemplo de materiales de formación
Estamos trabajando en materiales de formación de ejemplo que puede utilizar para hacer que su organización de TI y sus usuarios se pongan al día rápidamente sobre cómo implementar y usar el restablecimiento de contraseña. Permanezca atento.


<br/> <br/> <br/>

## Vínculos a la documentación de restablecimiento de la contraseña
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* [**Restablecimiento de la propia contraseña**](active-directory-passwords-update-your-own-password.md): obtenga información sobre cómo restablecer o cambiar su propia contraseña como usuario del sistema.
* [**Funcionamiento**](active-directory-passwords-how-it-works.md): obtenga información acerca de los seis diferentes componentes del servicio y lo que hace cada uno.
* [**Introducción**](active-directory-passwords-getting-started.md): obtenga información sobre cómo permitir a los usuarios restablecer y cambiar sus contraseñas en la nube o locales.
* [**Personalizar**](active-directory-passwords-customize.md) : obtenga información sobre cómo personalizar la apariencia y el comportamiento del servicio para ajustarse a las necesidades de su organización.
* [**Obtener perspectivas**](active-directory-passwords-get-insights.md): obtenga información sobre nuestras capacidades integradas de creación de informes.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): obtenga respuestas a las preguntas más frecuentes.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): obtenga información sobre cómo solucionar rápidamente los problemas del servicio.
* [**Más información**](active-directory-passwords-learn-more.md): profundice en los detalles técnicos del funcionamiento del servicio.



[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-best-practices/007.jpg "Image_007.jpg"

<!---HONumber=AcomDC_1125_2015-->