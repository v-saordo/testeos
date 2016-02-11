<properties 
	pageTitle="Restablecimiento de contraseña de Azure AD | Microsoft Azure"
	description="Descripción de las capacidades de administración de contraseñas en Azure AD, incluido el restablecimiento de contraseña, el cambio, la creación de informes de administración de contraseñas y la escritura diferida en Active Directory local." 
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
	

# Restablecimiento de contraseña de Azure AD para usuarios y administradores

  >[AZURE.IMPORTANT]¿Está aquí porque quiere restablecer su contraseña de Azure o O365? Si es así, [vaya a esta sección](#users-how-to-manage-your-own-password).
  
El autoservicio ha sido durante mucho tiempo un objetivo clave para los departamentos de TI de todo el mundo como una medida de reducción de costos y ahorro de mano de obra. De hecho, el mercado está atestado de productos que le permiten administrar los grupos locales, las contraseñas o los perfiles de usuario en la nube o a nivel local. Azure AD se distingue de estas ofertas proporcionando algunas de las capacidades de autoservicio más fáciles de usar y más eficaces disponibles hoy día.

**La Administración de contraseñas de Azure AD** es un conjunto de capacidades que permiten a los usuarios administrar cualquier contraseña desde cualquier dispositivo, en cualquier momento, desde cualquier ubicación, manteniendo la conformidad con las directivas de seguridad que defina.

##USUARIOS: cómo administrar su propia contraseña
Si es un usuario (y no un administrador) en una organización que usa Office 365 o Cuentas Microsoft para obtener acceso a recursos de trabajo, haga clic en los vínculos siguientes para obtener información sobre cómo solucionar problemas habituales con su contraseña.

| Tema. | |
| --------- | --------- |
| Quiero registrarme para restablecer la contraseña | [Cómo registrarse para restablecer la contraseña](active-directory-passwords-update-your-own-password.md#how-to-register-for-password-reset) |
| Deseo cambiar mi contraseña de Office O365 | [Cómo cambiar la contraseña de Office365](active-directory-passwords-update-your-own-password.md#how-to-change-your-password-from-o365) |
| Quiero cambiar mi contraseña de myapps.microsoft.com | [Cómo cambiar la contraseña desde el panel de acceso](active-directory-passwords-update-your-own-password.md#how-to-change-your-password-from-the-access-panel) |
| Olvidé mi contraseña y deseo restablecerla | [Cómo restablecer la contraseña](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password) |
| No puedo iniciar sesión y deseo desbloquear mi cuenta | [Cómo desbloquear la cuenta local](active-directory-passwords-update-your-own-password.md#how-to-unlock-your-account) |
| Quiero ayuda para solucionar un restablecimiento de contraseña incorrecto | [Problemas comunes y sus soluciones](active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions) |

##ADMINISTRADORES: obtener información sobre cómo empezar a usar el restablecimiento de contraseña de Azure AD
Si es un administrador que desea habilitar el restablecimiento de contraseña de Azure AD o simplemente quiere obtener más información sobre esto, comience con los siguientes vínculos hasta llegar a lo que le interesa.

| Tema. | |
| --------- | --------- |
| Escenarios admitidos | [¿Qué se puede hacer con el restablecimiento de contraseña de Azure AD?](#what-is-possible-with-azure-ad-password-reset) |
| ¿Por qué usarla? | [¿Por qué usar el restablecimiento de contraseña de Azure AD?](#why-use-azure-ad-password-reset) |
| Precios y disponibilidad | [Precios y disponibilidad](#pricing-and-availability) |
| Habilitar restablecimiento de contraseña | [Habilitación del restablecimiento de contraseña para los usuarios](#enable-password-reset-for-your-users) |
| Personalizar cómo funciona | [Personalización del comportamiento de restablecimiento de contraseña](#customize-password-reset-behavior) |
| Implementarlo en los usuarios | [Configuración de los usuarios para que usen el restablecimiento de contraseña](#configure-your-users-to-use-password-reset) |
| Ver informes | [Visualización de la actividad de restablecimiento de contraseña con informes integrados](#view-password-reset-activity-with-integrated-reports) |
| Restablecimiento de la contraseña del usuario | [Administración de contraseñas de usuarios](#manage-your-users-passwords) |
| Establecer directivas de contraseñas de mi organización | [Establecimiento de directivas de contraseñas](#set-password-policies) |
| Solución de problemas | [Solución de problemas](#troubleshoot-a-problem) |
| P+F | [Lectura de Preguntas más frecuentes](#read-a-faq) |
| Detalles técnicos | [Descripción de los detalles técnicos](#understand-the-technical-details) |
| Características recientes | [Actualizaciones recientes de servicios](#recent-service-updates) |
| Vínculos a otra documentación | [Vínculos a la documentación de restablecimiento de contraseña](#links-to-password-reset-documentation) |

### ¿Qué se puede hacer con el restablecimiento de contraseña de Azure AD?
Estas son algunas de las cosas que puede hacer con las capacidades de administración de contraseñas de Azure AD.

- El **cambio de la contraseña de autoservicio** permite a los usuarios finales o administradores cambiar sus contraseñas expiradas o no expiradas sin llamar a un administrador o departamento de soporte técnico para obtener soporte técnico.
- El **restablecimiento de la contraseña de autoservicio** permite a los usuarios finales o administradores restablecer sus contraseñas automáticamente sin llamar a un administrador o departamento de soporte técnico para obtener soporte técnico. El restablecimiento de la contraseña de autoservicio requiere Azure AD Premium o Básico. Para obtener más información, consulte Ediciones de Azure Active Directory.
- El **restablecimiento de la contraseña iniciado por el administrador** permite a un administrador restablecer la contraseña de un usuario final o de otro administrador desde dentro del [Portal de administración de Azure](https://manage.windowsazure.com).
- Los **informes de actividad de administración de contraseñas** proporcionan a los administradores perspectivas sobre una actividad de registro y restablecimiento de contraseña en su organización. 
- La **escritura diferida de la contraseña** permite la administración de contraseñas locales desde la nube, por lo que todos los escenarios anteriores pueden realizarse por los usuarios sincronizados con contraseña o federados, o en nombre de ellos. La escritura diferida de la contraseña requiere Azure AD Premium. Para obtener más información, consulte Introducción a Azure AD Premium.

### ¿Por qué usar el restablecimiento de contraseña de Azure AD?
Estas son algunas de las razones por las que se debe usar las capacidades de administración de contraseñas de Azure AD.

- **Reducir los costos** : el restablecimiento de contraseñas con asistencia de un servicio de soporte técnico normalmente supone el 20 % del gasto en TI de una organización.
- **Mejorar las experiencias de usuario**: a los usuarios les incomoda llamar al soporte técnico y pasar una hora al teléfono cada vez que olvidan sus contraseñas.
- **Reducir los volúmenes de soporte técnico**: la administración de contraseñas es el motivo único que empuja a la mayoría de organizaciones a recurrir más veces al departamento de soporte técnico.
- **Permitir la movilidad**: los usuarios pueden restablecer sus contraseñas desde cualquier lugar en que se encuentren.

### Precios y disponibilidad
El restablecimiento de contraseña de Azure AD está disponible en tres niveles, en función de la suscripción que tenga:

- **Azure AD gratuito**: los administradores que están solo en la nube pueden restablecer sus propias contraseñas.
- **Azure AD Basic o cualquier suscripción de pago de O365**: los usuarios y administradores que están solo en la nube pueden restablecer sus propias contraseñas.
- **Azure AD Premium** : cualquier usuario o administrador, incluidos los que están solo en la nube, los federados o los usuarios sincronizados con contraseña, pueden restablecer sus contraseñas (requiere que [esté habilitada la escritura diferida de contraseñas](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords))

Para obtener más información sobre los precios de Azure AD Premium o Basic, consulte la página [Detalles de precios de Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

##Habilitación del restablecimiento de contraseña para los usuarios
| Tema. | |
| --------- | --------- |
| ¿Cómo se puede habilitar el restablecimiento de contraseña para usuarios en la nube? | [Habilitación de usuarios para que restablezcan sus contraseñas de Azure Active Directory en la nube](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) |
| ¿Cómo se puede habilitar el restablecimiento y el cambio de contraseña para usuarios locales? | [Habilitación de usuarios para que restablezcan o cambien sus contraseñas de Active Directory local](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) |
| ¿Cómo se puede limitar el ámbito de restablecimiento de contraseña a un conjunto específico de usuarios? | [Restricción del restablecimiento de contraseña a usuarios específicos](active-directory-passwords-customize.md#restrict-access-to-password-reset) |
| ¿Cómo se puede probar el restablecimiento de contraseña en la nube? | [Restablecimiento de contraseña de Azure AD como usuario](active-directory-passwords-getting-started.md#step-3-reset-your-azure-ad-password-as-a-user) |
| ¿Cómo se puede probar el restablecimiento de contraseña local? | [Restablecimiento de contraseña de AD local como usuario](active-directory-passwords-getting-started.md#step-5-reset-your-ad-password-as-a-user) |
| ¿Cómo se puede deshabilitar el restablecimiento de contraseña en un momento posterior? | [Configuración: usuarios habilitados para el restablecimiento de contraseña](active-directory-passwords-customize.md#users-enabled-for-password-reset) |


##Personalización del comportamiento de restablecimiento de contraseña
| Tema. | |
| --------- | --------- |
| ¿Cómo se puede cambiar los métodos de autenticación que se admiten? | [Configuración: métodos de autenticación disponibles para los usuarios](active-directory-passwords-customize.md#authentication-methods-available-to-users) |
| ¿Cómo se puede cambiar el número de métodos de autenticación necesarios? | [Configuración: número de métodos de autenticación necesarios](active-directory-passwords-customize.md#number-of-authentication-methods-required) |
| ¿Cómo se configuran preguntas de seguridad personalizadas? | [Configuración: preguntas de seguridad personalizadas](active-directory-passwords-customize.md#custom-security-questions) |
| ¿Cómo se configuran preguntas de seguridad localizadas predefinidas? | [Configuración: preguntas de seguridad basadas en el conocimiento](active-directory-passwords-customize.md#knowledge-based-security-questions) |
| ¿Cómo se puede cambiar el número de preguntas de seguridad que son necesarias? | [Configuración: número de preguntas de seguridad de registro o restablecimiento](active-directory-passwords-customize.md#number-of-questions-required-to-register) |
| ¿Cómo se puede personalizar el modo en que los usuarios se ponen en contacto con un administrador? | [Configuración: personalización del vínculo "póngase en contacto con su administrador"](active-directory-passwords-customize.md#customize-the-contact-your-administrator-link) |
| ¿Cómo se puede permitir que los usuarios desbloqueen cuentas de AD sin restablecer una contraseña? | [Configuración: permitir que los usuarios desbloqueen sus cuentas de AD sin restablecer una contraseña](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password) |
| ¿Cómo se pueden habilitar las notificaciones de restablecimiento de contraseña para los usuarios? | [Configuración: notificar a los usuarios cuando sus contraseñas se han restablecido](active-directory-passwords-customize.md#notify-users-and-admins-when-their-own-password-has-been-reset) |
| ¿Cómo se pueden habilitar las notificaciones de restablecimiento de contraseña para los administradores? | [Configuración: notificar a otros administradores cuando un administrador restablezca su propia contraseña](active-directory-passwords-customize.md#notify-admins-when-other-admins-reset-their-own-passwords) |
| ¿Cómo se puede personalizar la apariencia del restablecimiento de contraseña? | [Configuración: nombre, marca y logotipo de la empresa ](active-directory-passwords-customize.md#password-managment-look-and-feel) |


##Configuración de los usuarios para que usen el restablecimiento de contraseña
| Tema. | |
| --------- | --------- |
| ¿Cómo se puede saber si una cuenta está configurada para el restablecimiento de contraseña? | [Configuración de una cuenta para el restablecimiento de contraseña](active-directory-passwords-best-practices.md#what-makes-an-account-configured) |
| ¿Cómo se puede configurar a los usuarios para el restablecimiento de contraseña? | [Formas de rellenar los datos de autenticación de restablecimiento de contraseña para los usuarios](active-directory-passwords-best-practices.md#ways-to-populate-authentication-data) |
| ¿Cómo se pueden cargar datos manualmente para los usuarios? | [Carga de datos de restablecimiento de contraseña](active-directory-passwords-best-practices.md#uploading-data-yourself) |
| ¿Cómo se puede usar PowerShell para leer o establecer datos para los usuarios? | [Cómo obtener acceso a los datos de restablecimiento de contraseña de los usuarios](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) |
| ¿Cómo se pueden sincronizar datos de restablecimiento de contraseña desde el entorno local? | [¿Qué datos sirven para restablecer la contraseña?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |
| ¿Cómo se puede usar una campaña de correo electrónico para que los usuarios se registren y utilicen el restablecimiento de contraseña? | [Implementación basada en correo electrónico de restablecimiento de contraseña](active-directory-passwords-best-practices.md#email-based-rollout) |
| ¿Cómo se puede forzar a que los usuarios se registren al iniciar sesión? | [Implementación de restablecimiento de contraseña basado en registro forzoso](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) |
| ¿Cómo se puede forzar a que los usuarios confirmen de nuevo su registro periódicamente? | [Configuración: número de días antes de que los usuarios deban confirmar nuevamente sus datos de autenticación](active-directory-passwords-customize.md#number-of-days-before-users-must-confirm-their-contact-data) |
| ¿Cuáles son los procedimientos recomendados de comunicar el restablecimiento de contraseña a los usuarios finales? | [Creación de su propio portal de contraseñas para que la utilicen los usuarios](active-directory-passwords-best-practices.md#creating-your-own-password-portal) |


##Visualización de la actividad de restablecimiento de contraseña con informes integrados
| Tema. | |
| --------- | --------- |
| ¿Dónde se pueden ver los informes de restablecimiento de contraseña? | [Información general de los informes de administración de contraseñas](active-directory-passwords-get-insights.md#overview-of-password-management-reports) |
| ¿Dónde se puede ver cómo usan los usuarios el restablecimiento de contraseña en mi organización? | [Visualización de la actividad de restablecimiento de contraseña en su organización](active-directory-passwords-get-insights.md#view-password-reset-activity) |
| ¿Dónde se puede ver cuántos usuarios se registran y para qué lo hacen? | [Visualización de la actividad de registro de restablecimiento de contraseña](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) |
| ¿Cómo se pueden obtener informes de restablecimiento de contraseña desde una API? | [Creación de una aplicación de Azure AD para tener acceso a la API de informes](active-directory-reporting-api-getting-started.md#creating-an-azure-ad-application-to-access-the-api) |
| ¿Qué tipo de información de informes de restablecimiento de contraseña está disponible desde una API? | [Eventos de registro y restablecimiento de contraseña disponibles en la API de informes](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview#SsprActivityEvent) |


##Administración de contraseñas de usuarios
| Tema. | |
| --------- | --------- |
| ¿Cómo se puede restablecer la contraseña de un usuario desde el portal de administración de OfficeO365? | [Restablecimiento de una contraseña de usuario en Office 365](https://support.office.com/article/Reset-a-user-s-password-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C) |
| ¿Cómo se puede restablecer una contraseña de usuario mediante PowerShell? | [Restablecimiento de una contraseña de usuario con Set-MsolUserPassword](https://msdn.microsoft.com/library/azure/dn194140.aspx) |


##Establecimiento de directivas de contraseñas
| Tema. | |
| --------- | --------- |
| ¿Cómo se pueden establecer directivas de expiración de contraseñas de organización desde Office 365? | [Establecimiento de directivas de expiración de contraseñas](https://support.office.com/article/Set-a-user-s-password-expiration-policy-0f54736f-eb22-414c-8273-498a0918678f) |
| ¿Cómo se pueden establecer las contraseñas de un usuario específico para que no expiren nunca con PowerShell? | [Establecimiento de una contraseña de usuario individual para que no expire nunca con PowerShell](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466) |
| ¿Cómo se puede saber si una contraseña de usuario está establecida para que no expire nunca con PowerShell? | [Comprobación del estado de expiración de contraseñas de usuario individual con PowerShell](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466#__toc378845827) |


##Solución de problemas
| Tema. | |
| --------- | --------- |
| ¿Qué información se debe proporcionar al soporte técnico si se necesita ayuda? | [Información para incluir si necesita ayuda](active-directory-passwords-troubleshoot.md#information-to-include-when-you-need-help) |
| Cómo se puede corregir un problema con el restablecimiento de contraseña | [Solución de problemas con el portal de restablecimiento de contraseña](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-portal) |
| Cómo se puede corregir un problema con la escritura diferida de contraseñas | [Solución de problemas con la escritura diferida de contraseñas](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Cómo se puede corregir un problema con la conectividad de la escritura diferida de contraseñas | [Solución de problemas con la conectividad de la escritura diferida de contraseñas](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) |
| Cómo se puede corregir un problema con la configuración de restablecimiento de contraseña | [Solución de problemas con la configuración de restablecimiento de contraseña en el Portal de administración de Azure](active-directory-passwords-troubleshoot.md#troubleshoot-password-reset-configuration-in-the-azure-management-portal) |
| Cómo se puede corregir un problema con los informes de restablecimiento de contraseña | [Solución de problemas con los informes de administración de contraseñas en el Portal de administración de Azure](active-directory-passwords-troubleshoot.md#troubleshoot-password-management-reports-in-the-azure-management-portal) |
| Cómo se puede corregir un problema con el registro de restablecimiento de contraseña | [Solución de problemas con el portal de registro de restablecimiento de contraseña](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-registration-portal) |
| Códigos de error de registro de eventos de escritura diferida de contraseñas | [Códigos de error de registro de eventos de escritura diferida de contraseñas](active-directory-passwords-troubleshoot.md#password-writeback-event-log-error-codes) |


##Lectura de Preguntas más frecuentes (P+F)
| Tema. | |
| --------- | --------- |
| P+F acerca del registro de restablecimiento de contraseña | [P+F del registro de restablecimiento de contraseña](active-directory-passwords-faq.md#password-reset-registration) |
| P+F acerca del restablecimiento de contraseña | [P+F del restablecimiento de contraseña](active-directory-passwords-faq.md#password-reset) |
| P+F acerca de informes de restablecimiento de contraseña | [P+F de informes de administración de contraseñas](active-directory-passwords-faq.md#password-management-reports) |
| P+F acerca de la escritura diferida de contraseñas | [P+F de la escritura diferida de contraseñas](active-directory-passwords-faq.md#password-writeback) |


##Descripción de los detalles técnicos

| Tema. | |
| --------- | --------- |
| Información sobre lo que es la escritura diferida de contraseñas | [Información general sobre la escritura diferida de contraseñas](active-directory-passwords-learn-more.md#password-writeback-overview) |
| Información sobre cómo funciona la escritura diferida de contraseñas | [Funcionamiento de la escritura diferida de contraseñas](active-directory-passwords-learn-more.md#how-password-writeback-works) |
| Información sobre los escenarios admitidos por la escritura diferida de contraseñas | [Escenarios admitidos para la escritura diferida de contraseñas](active-directory-passwords-learn-more.md#scenarios-supported-for-password-writeback) |
| Información sobre cómo se protege la escritura diferida de contraseñas | [Modelo de seguridad de la escritura diferida de contraseñas](active-directory-passwords-learn-more.md#password-writeback-security-model) |
| Información sobre cómo funciona el portal de restablecimiento de contraseña | [¿Cómo funciona el portal de restablecimiento de contraseñas?](active-directory-passwords-learn-more.md#how-does-the-password-reset-portal-work) |
| Información sobre qué datos se usan para el restablecimiento de contraseña | [¿Qué datos sirven para restablecer la contraseña?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |

## Actualizaciones recientes de servicios

####Aplicar el registro de restablecimiento de contraseña al iniciar sesión en aplicaciones de Office 365, noviembre de 2015

- Ahora, después de habilitar la característica de [registro exigido](active-directory-passwords-customize.md#require-users-to-register-when-signing-in), los usuarios deberán registrarse desde cualquier lugar que inicien sesión con una cuenta profesional o educativa. Esto aumenta considerablemente el ritmo al que muchas organizaciones pueden incorporarse al restablecimiento de contraseña. Con esta nueva característica, ¡hemos visto la incorporación de grandes organizaciones en tan solo dos semanas!

####Compatibilidad para desbloquear cuentas de Active Directory sin restablecer una contraseña, noviembre de 2015

- Lograr solo el desbloqueo (sin tener que reiniciar) supone un auténtico reto para los departamentos de soporte técnico hoy en día. De hecho, muchas organizaciones dedican hasta un 70 % del presupuesto destinado al restablecimiento de contraseñas al desbloqueo de cuentas. Para satisfacer esta demanda, ahora, con el restablecimiento de contraseña de Azure AD puede habilitar una característica para que los usuarios puedan desbloquear cuentas de AD sin tener que restablecer la contraseña. Compruebe cómo habilitarla aquí: [Configuración: permitir que los usuarios desbloqueen sus cuentas de AD sin restablecer una contraseña](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password).

####Actualizaciones de facilidad de uso para la página de registro, octubre de 2015

- Ahora, cuando un usuario ya tiene datos registrados, solo tiene que hacer clic en "Tiene buen aspecto" para actualizar los datos sin necesidad de volver a enviar el mensaje correo electrónico o una llamada de teléfono.

####Mejor confiabilidad de escritura diferida de contraseñas, septiembre de 2015

- A partir de la versión de septiembre de Azure AD Connect, el agente de escritura diferida de contraseñas intentará ahora conexiones de manera más agresiva y capacidades adicionales de conmutación por error más sólidas.

####API para recuperar los datos de informes de restablecimiento de contraseña, agosto de 2015

- Ahora, los datos que se encuentran detrás de los informes de restablecimiento de contraseña se pueden recuperar directamente de la [API de eventos e informes de Azure AD](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

####Compatibilidad con el restablecimiento de contraseña de Azure AD durante la unión al dominio de nube, agosto de 2015

- Ahora, cualquier usuario de nube puede restablecer su contraseña directamente desde la pantalla de inicio de sesión de Windows 10 durante la experiencia de incorporación de unión de dominio de nube. Tenga en cuenta que esto todavía no está expuesto en la pantalla de inicio de sesión de Windows 10.

####Aplicar el registro de restablecimiento de contraseña al iniciar sesión en Azure y aplicaciones federadas, julio de 2015

- Además de aplicar el registro al iniciar sesión en myapps.microsoft.com, ahora admitimos la aplicación del registro durante inicios de sesión para el Portal de administración de Azure y cualquiera de sus aplicaciones de inicio de sesión único federadas.

####Compatibilidad de localización de preguntas de seguridad, mayo de 2015

- Ahora tiene la opción de seleccionar preguntas de seguridad predefinidas que están localizadas en el conjunto de idiomas completo de O365 al configurar las preguntas de seguridad para el restablecimiento de contraseña.

####Compatibilidad con el desbloqueo de cuentas durante el restablecimiento de contraseña, junio de 2015

- Si está utilizando la escritura diferida de contraseñas y restablece la contraseña cuando la cuenta está bloqueada, desbloquearemos automáticamente su cuenta de Active Directory.

####Registro de SSPR con la marca, abril de 2015

- La página de registro del restablecimiento de contraseña ahora lleva el logotipo de su empresa.

####Preguntas de seguridad, marzo de 2015

- ¡Hemos puesto las preguntas de seguridad a disposición de todos los usuarios!

####Desbloqueo de cuenta, marzo de 2015

- Ahora los usuarios pueden desbloquear sus cuentas cuando se produce el restablecimiento de contraseña.

## Próximamente

A continuación se muestran algunas de las interesantes características en las que estamos trabajando en este momento.

**Compatibilidad para recordar a los usuarios que actualicen sus datos registrados durante el inicio de sesión**, trabajo en curso

- En la actualidad, admitimos recordar a los usuarios que actualicen sus datos registrados al tener acceso a myapps.microsoft.com, pero estamos trabajando en la capacidad para hacerlo para todos los inicios de sesión.

## Vínculos a la documentación de restablecimiento de contraseña
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* [**Restablecimiento de la propia contraseña**](active-directory-passwords-update-your-own-password.md): obtenga información sobre cómo restablecer o cambiar su propia contraseña como usuario del sistema.
* [**Funcionamiento**](active-directory-passwords-how-it-works.md): obtenga información acerca de los seis diferentes componentes del servicio y lo que hace cada uno.
* [**Introducción**](active-directory-passwords-getting-started.md): obtenga información sobre cómo permitir a los usuarios restablecer y cambiar sus contraseñas en la nube o locales.
* [**Personalizar**](active-directory-passwords-customize.md) : obtenga información sobre cómo personalizar la apariencia y el comportamiento del servicio para ajustarse a las necesidades de su organización.
* [**Prácticas recomendadas**](active-directory-passwords-best-practices.md): obtenga información sobre cómo implementar rápidamente y administrar eficazmente las contraseñas de la organización.
* [**Obtener perspectivas**](active-directory-passwords-get-insights.md): obtenga información sobre nuestras capacidades integradas de creación de informes.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): obtenga respuestas a las preguntas más frecuentes.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): obtenga información sobre cómo solucionar rápidamente los problemas del servicio.
* [**Más información**](active-directory-passwords-learn-more.md): profundice en los detalles técnicos del funcionamiento del servicio.

<!---HONumber=AcomDC_1125_2015-->