<properties 
	pageTitle="Funcionamiento de la administración de contraseñas de Azure AD | Microsoft Azure" 
	description="Obtenga información sobre los distintos componentes de la administración de contraseñas de Azure AD, entre ellos, dónde los usuarios registran, restablecen y cambian sus contraseñas y dónde los administradores configurar y habilitan la administración de contraseñas de Active Directory locales, además de generar informes sobre ellas." 
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

# Funcionamiento de la administración de contraseñas
La administración de contraseñas en Azure Active Directory consta de varios componentes lógicos que se describen a continuación. Haga clic en cada vínculo para obtener más información sobre ese componente.

- [**Portal de configuración de administración de contraseñas**](#password-management-configuration-portal): los administradores pueden controlar distintas facetas de cómo se administran las contraseñas en sus inquilinos. Para ello, vaya a la pestaña Configurar de su directorio en el [Portal de administración de Azure](https://manage.windowsazure.com).
- [**Portal de registro de usuario**](#user-registration-portal): los usuarios pueden registrarse automáticamente para restablecer la contraseña a través de este portal web.
- [**Portal de restablecimiento de contraseña del usuario**](#user-password-reset-portal): los usuarios pueden restablecer sus propias contraseñas mediante el uso de varios desafíos distintos de acuerdo con la directiva de restablecimiento de contraseña controlada por el administrador.
- [**Portal de cambio de contraseñas de usuario**](#user-password-change-portal): los usuarios pueden cambiar sus propias contraseñas en cualquier momento si escriben su contraseña anterior y seleccionan una contraseña nueva en este portal web.
- [**Informes de administración de contraseñas**](#password-management-reports): los administradores pueden ver y analizar la actividad de registro y restablecimiento de contraseña en su inquilino. Para ello, vaya a la sección "Informes de actividad" de la pestaña "Informes" del [Portal de administración de Azure](https://manage.windowsazure.com).
- [**Componente de escritura diferida de contraseñas de Azure AD Connect**](#password-writeback-component-of-azure-ad-connect): los administradores tienen la opción de habilitar la característica de escritura diferida de contraseñas cuando se instala Azure AD Connect para habilitar la administración de contraseñas de usuarios federados o con contraseña sincronizada desde la nube.

## Portal de configuración de administración de contraseñas
Puede configurar directivas de administración de contraseñas para un directorio específico mediante el [Portal de administración de Azure](https://manage.windowsazure.com), en la sección **Directiva de restablecimiento de contraseña del usuario** en la pestaña **Configurar** del directorio. Desde esta página de configuración, puede controlar muchos aspectos de la administración de contraseñas en su organización, entre ellos:

- Habilitar y deshabilitar el restablecimiento de contraseña para todos los usuarios de un directorio.
- Establecer la cantidad de desafíos (uno o dos) que debe superar el usuario para restablecer su contraseña.
- Establecer los tipos específicos de desafíos que desea habilitar para los usuarios de su organización a partir de las siguientes opciones:
 - Teléfono móvil (código de comprobación mediante texto o llamada de voz)
 - Teléfono de la oficina (llamada de voz)
 - Correo electrónico alternativo (código de comprobación mediante correo electrónico)
 - Preguntas de seguridad (autenticación basada en conocimiento)
- Establecer el número de preguntas que un usuario debe registrar para usar el método de autenticación de preguntas de seguridad (solo visible si están habilitadas las preguntas de seguridad).
- Establecer el número de preguntas que un usuario debe proporcionar durante el restablecimiento para usar el método de autenticación de preguntas de seguridad (solo visible si están habilitadas las preguntas de seguridad).
- Usar preguntas de seguridad predefinidas y adaptadas que un usuario pueda elegir usar al registrarse para el restablecimiento de contraseña (solo es visible si están habilitadas las preguntas de seguridad)
- Definir preguntas de seguridad personalizadas que un usuario pueda elegir usar al registrarse para el restablecimiento de contraseña (solo es visible si están habilitadas las preguntas de seguridad)
- Pedir a los usuarios que se registren para el restablecimiento de contraseñas cuando van al panel de acceso de la aplicación en [http://myapps.microsoft.com](http://myapps.microsoft.com).
- Pedir a los usuarios que vuelvan a confirmar los datos registrados anteriormente después de un número de días configurable (solo visible si está habilitado el registro obligatorio).
- Proporcionar una dirección URL o un correo electrónico de soporte técnico personalizados que se mostrarán a los usuarios en caso de que experimenten algún problema al restablecer sus contraseñas.
- Habilitar o deshabilitar la función de escritura diferida de contraseñas (cuando se ha implementado la escritura diferida de contraseñas con AAD Connect).
- Ver el estado del agente de escritura diferida de contraseñas (cuando se ha implementado la escritura diferida de contraseñas con AAD Connect).
- Habilitar las notificaciones por correo electrónico a los usuarios cuando se ha restablecido su contraseña (se encuentra en la sección **Notificaciones** del [Portal de administración de Azure](https://manage.windowsazure.com)).
- Habilitar las notificaciones por correo electrónico a los administradores cuando otros administradores han restablecido sus propias contraseñas (se encuentra en la sección **Notificaciones** del [Portal de administración de Azure](https://manage.windowsazure.com)).
- Personalizar el portal y los correos electrónicos de restablecimiento de contraseña de usuario con el logotipo y el nombre de su organización mediante la característica de personalización de la marca del inquilino (se encuentra en la sección **Propiedades del directorio** del [Portal de administración de Azure](https://manage.windowsazure.com)).

Para obtener más información acerca de la configuración de la administración de contraseñas en su organización, consulte [Introducción a la administración de contraseñas en Azure AD](active-directory-passwords-getting-started.md).

##Portal de registro de usuario
Para que los usuarios puedan utilizar el restablecimiento de contraseña, las cuentas de usuario en la nube deben estar actualizadas con los datos de autenticación correctos a fin de garantizar que puedan superar el número correspondiente de desafíos definidos por el administrador para restablecer la contraseña. Los administradores también pueden definir esta información de autenticación en nombre del usuario en los portales web de Azure y Office, con DirSync/Azure AD Connect o con Windows PowerShell.

No obstante, si prefiere que sean los usuarios los que registren sus propios datos, también proporcionamos una página web a la que los usuarios pueden ir para brindar esta información. Esta página permite a los usuarios especificar la información de autenticación de acuerdo con las directivas de restablecimiento de contraseña que se hayan habilitado en su organización. Una vez que se comprueban estos datos, se almacenan en la cuenta del usuario en la nube para poder recuperar la cuenta más adelante. El portal de registro tiene el siguiente aspecto:

  ![][001]

Para obtener más información, consulte [Introducción a la administración de contraseñas en Azure AD](active-directory-passwords-getting-started.md) y [Prácticas recomendadas para la administración de contraseñas de Azure AD](active-directory-passwords-best-practices.md).

##Portal de restablecimiento de contraseñas
Una vez que haya habilitado el restablecimiento de contraseña de autoservicio, haya configurado la directiva de restablecimiento de contraseña de autoservicio de su organización y se haya asegurado de que los usuarios tienen los datos de contacto apropiados en el directorio, los usuarios de la organización podrán restablecer sus propias contraseñas de manera automática desde cualquier página web en la que se utilice una cuenta educativa o profesional para iniciar sesión (por ejemplo, [portal.microsoftonline.com](https://portal.microsoftonline.com)). En las páginas de este tipo, los usuarios verán el vínculo **¿No puede obtener acceso a su cuenta?**

  ![][002]

Al hacer clic en este vínculo se inicia un asistente para el restablecimiento de contraseña de autoservicio.

  ![][003]

Para obtener más información acerca de cómo los usuarios pueden restablecer sus contraseñas, consulte [Introducción a la administración de contraseñas en Azure AD](active-directory-passwords-getting-started.md).

##Portal de cambio de contraseñas de usuario
Si los usuarios desean cambiar sus contraseñas, pueden hacerlo en el portal de cambio de contraseña en cualquier momento. Los usuarios pueden tener acceso al portal de cambio de contraseña a través de la página de perfil del panel de acceso o mediante un clic en el vínculo "Cambiar contraseña" en las aplicaciones de Office 365. En el caso de que las contraseñas expiren, también se pedirá a los usuarios que las cambien automáticamente al iniciar sesión.

  ![][004]

En ambos casos, si se ha habilitado la escritura diferida de contraseñas y el usuario es federado o tiene la contraseña sincronizada, las contraseñas cambiadas se escriben en diferido en su instancia local de Active Directory. El portal de cambio de contraseña tiene el siguiente aspecto:

  ![][005]

Para obtener más información sobre cómo los usuarios pueden cambiar sus propias contraseñas de Active Directory local, consulte [Introducción a la administración de contraseñas en Azure AD](active-directory-passwords-getting-started.md).

##Informes de administración de contraseñas
Si navega a la pestaña **Informes** y mira la sección **Registros de actividad**, verá dos informes de administración de contraseñas: **Actividad de restablecimiento de contraseña** y **Actividad de registro de restablecimiento de contraseña**. Con estos dos informes, puede obtener una vista de los usuarios que se registran y usan restablecimiento de contraseña en su organización. Este es el aspecto de estos informes en el [Portal de administración de Azure](https://manage.windowsazure.com):

  ![][006]

Para obtener más información, consulte [Obtener información sobre los informes de administración de contraseñas de Azure AD](active-directory-passwords-get-insights.md).

##Componente de escritura diferida de contraseñas de Azure AD Connect
Si las contraseñas de los usuarios de su organización proceden de su entorno local (mediante federación o sincronización de contraseña), puede instalar la versión más reciente de Azure AD Connect para habilitar la actualización de esas contraseñas directamente desde la nube. Esto significa que, cuando los usuarios olvidan su contraseña de AD o desean cambiarla, pueden hacerlo directamente desde la web. Aquí es donde puede encontrar la opción de escritura diferida de contraseñas en el asistente para la instalación de AD Connect:

  ![][007]

Para obtener más información sobre Azure AD Connect, consulte [Introducción a Azure AD Connect](active-directory-aadconnect.md). Para obtener más información acerca de la escritura diferida de contraseñas, consulte [Introducción a la administración de contraseñas en Azure AD](active-directory-passwords-getting-started.md).


<br/> <br/> <br/>

## Vínculos a la documentación de restablecimiento de la contraseña
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* [**Restablecimiento de la propia contraseña**](active-directory-passwords-update-your-own-password.md): obtenga información sobre cómo restablecer o cambiar su propia contraseña como usuario del sistema.
* [**Introducción**](active-directory-passwords-getting-started.md): obtenga información sobre cómo permitir a los usuarios restablecer y cambiar sus contraseñas en la nube o locales.
* [**Personalizar**](active-directory-passwords-customize.md) : obtenga información sobre cómo personalizar la apariencia y el comportamiento del servicio para ajustarse a las necesidades de su organización.
* [**Prácticas recomendadas**](active-directory-passwords-best-practices.md): obtenga información sobre cómo implementar rápidamente y administrar eficazmente las contraseñas de la organización.
* [**Obtener perspectivas**](active-directory-passwords-get-insights.md): obtenga información sobre nuestras capacidades integradas de creación de informes.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): obtenga respuestas a las preguntas más frecuentes.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): obtenga información sobre cómo solucionar rápidamente los problemas del servicio.
* [**Más información**](active-directory-passwords-learn-more.md): profundice en los detalles técnicos del funcionamiento del servicio.



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"

<!---HONumber=AcomDC_1125_2015-->