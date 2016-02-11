<properties 
	pageTitle="Obtener información sobre los informes de administración de contraseñas de Azure AD | Microsoft Azure" 
	description="En este artículo se describe cómo usar los informes para obtener información sobre las operaciones de administración de contraseñas en su organización." 
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

# Visión operativa con los informes de la administración de contraseñas
En esta sección se describe cómo puede usar informes de administración de contraseñas de Azure Active Directory para ver cómo utilizan los usuarios el restablecimiento y el cambio de contraseña en su organización.

- [**Información general de los informes de administración de contraseñas**](#overview-of-password-management-reports)
- [**Visualización de los informes de administración de contraseñas**](#how-to-view-password-management-reports)
- [**Visualización de la actividad de registro de restablecimiento de contraseña en su organización**](#view-password-reset-registration-activity)
- [**Visualización de la actividad de restablecimiento de contraseña en su organización**](#view-password-reset-activity)

## Información general de los informes de administración de contraseñas
Después de implementar el restablecimiento de contraseña, uno de los siguientes pasos más comunes es ver cómo se usa en su organización. Por ejemplo, puede que desee saber cómo se registran los usuarios para el restablecimiento de contraseña o cuántos restablecimientos de contraseña se han realizado en los últimos días. Estas son algunas de las preguntas comunes que podrá responder con los informes de administración de contraseñas que existen actualmente en el [Portal de administración de Azure](https://manage.windowsazure.com):

- ¿Cuántas personas se han registrado para el restablecimiento de contraseña?
- ¿Quién se ha registrado para el restablecimiento de contraseña?
- ¿Qué datos está registrando la gente?
- ¿Cuántas personas restablecieron sus contraseñas en los últimos 7 días?
- ¿Cuáles son los métodos más comunes que utilizan los usuarios o administradores para restablecer sus contraseñas?
- ¿Cuáles son los problemas comunes que encuentran los usuarios o administradores al intentar utilizar el restablecimiento de contraseña?
- ¿Qué administradores restablecen sus propias contraseñas con frecuencia?
- ¿Hay alguna actividad sospechosa en relación con el restablecimiento de contraseña?


## Visualización de los informes de administración de contraseñas
Para buscar los informes de administración de contraseñas, siga estos pasos:

1.	Haga clic en la extensión de **Active Directory** en el [Portal de administración de Azure](https://manage.windowsazure.com).
2.	Seleccione el directorio de la lista que aparece en el portal.
3.	Haga clic en la pestaña **Informes**.
4.	Busque en la sección **Registros de actividad**.
5.	Seleccione el informe **Actividad de restablecimiento de contraseña** o el informe **Actividad de registro de restablecimiento de contraseña**.

    ![][001]

## Cómo obtener acceso a los informes de administración de contraseñas desde una API
A partir de agosto de 2015, los informes y eventos de Azure AD admiten ahora la recuperación de toda la información incluida en los informes de restablecimiento de contraseña y del registro de restablecimiento de contraseña.

Para obtener acceso a estos datos, deberá escribir una pequeña aplicación o un script para recuperarlos de nuestros servidores. [Obtenga información sobre cómo empezar con la API de informes de Azure AD](active-directory-reporting-api-getting-started.md).

Cuando tenga un script de trabajo, querrá examinar los eventos de registro y el restablecimiento de contraseña que puede recuperar para cumplir con sus escenarios.

- [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): muestra las columnas disponibles para los eventos de restablecimiento de contraseña.
- [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): muestra las columnas disponibles para los eventos de registro de restablecimiento de contraseña.

## Visualización de la actividad de registro de restablecimiento de contraseña en su organización

El informe de actividad de registro de restablecimiento de contraseña muestra todos los registros de restablecimiento de contraseña que se han producido en su organización. En este informe, se muestra un registro de restablecimiento de contraseña para cualquier usuario que haya registrado correctamente la información de autenticación en el portal de registro de restablecimiento de contraseña ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)).

- **Intervalo de tiempo máximo**: 1 mes
- **Número máximo de filas**: ilimitado
- **Se puede descargar**: Sí, en un archivo CSV

    ![][002]

### Descripción de las columnas del informe
La siguiente lista explica en detalle cada una de las columnas del informe:

- **Usuario**: usuario que intentó una operación de registro de restablecimiento de contraseña.
- **Rol**: rol del usuario en el directorio.
- **Fecha y hora**: fecha y hora del intento.
- **Datos registrados**: datos de autenticación que el usuario proporcionó durante el registro de restablecimiento de contraseña.

### Descripción de los valores del informe
En la tabla siguiente se describen los distintos valores permitidos para cada columna:

Columna|Valores permitidos y su significado
---|---
Datos registrados| **Correo electrónico alternativo**: el usuario utilizó un correo electrónico alternativo o un correo electrónico de autenticación para autenticarse<p><p>**Teléfono del trabajo**: el usuario utilizó el teléfono de la oficina para autenticarse<p>**Teléfono móvil**: el usuario utilizó un teléfono móvil o un teléfono de autenticación para autenticarse<p>**Preguntas de seguridad**: el usuario utilizó preguntas de seguridad para autenticarse<p>**Cualquier combinación de los valores anteriores (por ejemplo, correo electrónico alternativo + teléfono móvil)**: tiene lugar cuando se especifica una directiva de 2 puertas y muestra los dos métodos que el usuario utilizó para autenticar su solicitud de restablecimiento de contraseña.

## Visualización de la actividad de restablecimiento de contraseña en su organización

Este informe muestra todos los intentos de restablecimiento de contraseña que se han producido en su organización.

- **Intervalo de tiempo máximo**: 1 mes
- **Número máximo de filas**: ilimitado
- **Se puede descargar**: Sí, en un archivo CSV

    ![][003]

### Descripción de las columnas del informe
La siguiente lista explica en detalle cada una de las columnas del informe:

1. **Usuario**: usuario que intentó una operación de restablecimiento de contraseña (basado en el campo Id. de usuario cuando el usuario intenta restablecer una contraseña).
2. **Rol**: rol del usuario en el directorio.
3. **Fecha y hora**: fecha y hora del intento.
4. **Métodos usados**: métodos de autenticación que utiliza el usuario para esta operación de restablecimiento.
5. **Resultado**: resultado final de la operación de restablecimiento de contraseña.
6. **Detalles**: detalles de por qué el restablecimiento de contraseña dio ese resultado. También incluye los pasos de mitigación que podría seguir para resolver un error inesperado.

### Descripción de los valores del informe
En la tabla siguiente se describen los distintos valores permitidos para cada columna:

Columna|Valores permitidos y su significado
---|---
Métodos usados|**Correo electrónico alternativo**: el usuario utilizó un correo electrónico alternativo o un correo electrónico de autenticación para autenticarse<p>**Teléfono del trabajo**: el usuario utilizó el teléfono de la oficina para autenticarse<p>**Teléfono móvil**: el usuario utilizó un teléfono móvil o un teléfono de autenticación para autenticarse<p>**Preguntas de seguridad**: el usuario utilizó preguntas de seguridad para autenticarse<p>**Cualquier combinación de los valores anteriores (por ejemplo, correo electrónico alternativo + teléfono móvil)**: tiene lugar cuando se especifica una directiva de 2 puertas y muestra los dos métodos que el usuario utilizó para autenticar su solicitud de restablecimiento de contraseña.
Resultado|**Abandonado**: el usuario inició un restablecimiento de contraseña, pero lo interrumpió antes de que finalizara<p>**Bloqueado**: se impidió que la cuenta de usuario usara el restablecimiento de contraseña debido a un intento de utilizar la página de restablecimiento de contraseña o una sola puerta de restablecimiento de contraseña demasiadas veces en un período de 24 horas<p>**Cancelado**: el usuario inició un restablecimiento de contraseña, pero después hizo clic en el botón Cancelar para cancelar la sesión en mitad del proceso <p>**Administrador contactado**: el usuario tuvo un problema durante su sesión que no pudo resolver, por lo que hizo clic en el vínculo "Póngase en contacto con el administrador" en lugar de finalizar el flujo de restablecimiento de contraseña<p>**Con error**: el usuario no pudo restablecer una contraseña, probablemente porque el usuario no estaba configurado para usar esta característica (por ejemplo, no tiene licencia, falta información de autenticación, la contraseña se administra en el entorno local pero la escritura diferida está desactivada).<p>**Correcto**: la contraseña se restableció correctamente.
Detalles|Consulte la tabla siguiente:

### Valores permitidos para la columna Detalles
A continuación se muestra la lista de los tipos de resultados que puede esperar cuando usa el informe de actividad de restablecimiento de contraseña:

Detalles | Tipo de resultado
----|----
El usuario abandonó tras completar la opción de comprobación de correo electrónico. | Abandonado
El usuario abandonó tras completar la opción de comprobación por SMS en el teléfono móvil.|Abandonado
El usuario abandonó tras completar la opción de comprobación por llamada de voz al teléfono móvil. | Abandonado
El usuario abandonó tras completar la opción de comprobación por llamada de voz a la oficina. | Abandonado
El usuario abandonó tras completar la opción de preguntas de seguridad.|Abandonado
El usuario abandonó después de escribir su identificador de usuario.| Abandonado
El usuario abandonó tras iniciar la opción de comprobación de correo electrónico.|Abandonado
El usuario abandonó tras iniciar la opción de comprobación por SMS en el teléfono móvil.|Abandonado
El usuario abandonó tras iniciar la opción de comprobación por llamada de voz al teléfono móvil.|Abandonado
El usuario abandonó tras iniciar la opción de comprobación por llamada de voz a la oficina.|Abandonado
El usuario abandonó tras iniciar la opción de preguntas de seguridad.| Abandonado
El usuario abandonó antes de seleccionar una nueva contraseña.| Abandonado
El usuario abandonó mientras seleccionaba una nueva contraseña.| Abandonado
El usuario escribió demasiados códigos de comprobación por SMS no válidos y se ha bloqueado durante 24 horas.|Bloqueado
El usuario intentó la comprobación por llamada de voz al teléfono móvil demasiadas veces y se ha bloqueado durante 24 horas.|Bloqueado
El usuario intentó la comprobación por llamada de voz al teléfono de la oficina demasiadas veces y se ha bloqueado durante 24 horas. |Bloqueado
El usuario intentó responder las preguntas de seguridad demasiadas veces y se ha bloqueado durante 24 horas.| Bloqueado
El usuario intentó comprobar un número de teléfono demasiadas veces y se ha bloqueado durante 24 horas.|Bloqueado
El usuario lo canceló antes de pasar a los métodos de autenticación requeridos.|Cancelado
El usuario lo canceló antes de enviar una contraseña nueva.|Cancelado
El usuario se puso en contacto con un administrador después de intentar la opción de comprobación de correo electrónico. |Administrador contactado
El usuario se puso en contacto con un administrador después de intentar la opción de comprobación por SMS en el teléfono móvil.|Administrador contactado
El usuario se puso en contacto con un administrador después de intentar la opción de comprobación por llamada de voz al teléfono móvil.|Administrador contactado
El usuario se puso en contacto con un administrador después de intentar la opción de comprobación por llamada de voz a la oficina. |Administrador contactado
El usuario se puso en contacto con un administrador después de intentar la opción de comprobación con preguntas de seguridad.|Administrador contactado
El restablecimiento de contraseña no está habilitado para este usuario. Habilite el restablecimiento de contraseña en la pestaña Configurar para resolver este problema.| Con error
El usuario no tiene una licencia. Puede agregar una licencia al usuario para resolver este problema.|Con error
El usuario intentó el restablecimiento desde un dispositivo sin las cookies habilitadas.| Con error
La cuenta del usuario no tiene definidos suficientes métodos de autenticación. Agregue información de autenticación para resolver este problema.|Con error
La contraseña del usuario se administra en el entorno local. Puede habilitar la escritura diferida de contraseña para resolver este problema.|Con error
No pudimos obtener acceso a su servicio de restablecimiento de contraseña local. Compruebe el registro de eventos de su máquina de sincronización.|Con error
Se encontró un problema durante el restablecimiento de la contraseña local del usuario. Compruebe el registro de eventos de su máquina de sincronización. | Con error
Este usuario no es miembro del grupo de usuarios de restablecimiento de contraseña. Agregue este usuario a ese grupo para resolver este problema.|Con error
El restablecimiento de contraseña se ha deshabilitado por completo para este inquilino. Consulte [aquí](http://aka.ms/ssprtroubleshoot) para resolver este problema. | Con error
El usuario restableció la contraseña correctamente.|Succeeded

## Vínculos a la documentación de restablecimiento de la contraseña
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* [**Restablecimiento de la propia contraseña**](active-directory-passwords-update-your-own-password.md): obtenga información sobre cómo restablecer o cambiar su propia contraseña como usuario del sistema.
* [**Funcionamiento**](active-directory-passwords-how-it-works.md): obtenga información acerca de los seis diferentes componentes del servicio y lo que hace cada uno.
* [**Introducción**](active-directory-passwords-getting-started.md): obtenga información sobre cómo permitir a los usuarios restablecer y cambiar sus contraseñas en la nube o locales.
* [**Personalizar**](active-directory-passwords-customize.md) : obtenga información sobre cómo personalizar la apariencia y el comportamiento del servicio para ajustarse a las necesidades de su organización.
* [**Prácticas recomendadas**](active-directory-passwords-best-practices.md): obtenga información sobre cómo implementar rápidamente y administrar eficazmente las contraseñas de la organización.
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): obtenga respuestas a las preguntas más frecuentes.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): obtenga información sobre cómo solucionar rápidamente los problemas del servicio.
* [**Más información**](active-directory-passwords-learn-more.md): profundice en los detalles técnicos del funcionamiento del servicio.



[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"

<!---HONumber=AcomDC_1125_2015-->