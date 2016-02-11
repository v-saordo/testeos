<properties
	pageTitle="Restricciones y directivas de contraseñas en Azure Active Directory | Microsoft Azure"
	description="Describe las directivas que se aplican a las contraseñas en Azure Active Directory, incluidos los caracteres permitidos, la longitud y la expiración"
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
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="curtand"/>


# Restricciones y directivas de contraseñas en Azure Active Directory

Este artículo describe las directivas de contraseña y los requisitos de complejidad asociados a las cuentas de usuario almacenadas en el directorio de Azure AD.

## Directivas de UserPrincipalName que se aplican a todas las cuentas de usuario

Cada cuenta de usuario que se necesita iniciar sesión en el sistema de autenticación de Azure AD debe tener un valor de atributo de nombre principal de usuario (UPN) único asociado a esa cuenta. En la siguiente tabla se describen las directivas que se aplican tanto a cuentas de usuario de Active Directory locales (sincronizadas con la nube) como a cuentas de usuario solo en la nube.

| Propiedad | Requisitos de UserPrincipalName |
|   ----------------------- |   ----------------------- |
| Caracteres permitidos | <ul> <li>A – Z</li> <li>a -z </li><li>0 – 9</li> <li> . - \_ ! # ^ ~</li></ul> |
| Caracteres no permitidos | <ul> <li>@</li> <li>No puede contener un carácter de punto '.' inmediatamente antes que el símbolo '@'</li></ul> |
| Restricciones de longitud | <ul> <li>La longitud total no debe superar los 113 caracteres</li><li>64 caracteres antes que el símbolo ‘@’</li><li>48 caracteres después del símbolo ‘@’</li></ul>

## Directivas de contraseña que se aplican solo a cuentas de usuario en la nube

En la tabla siguiente se describe la configuración de políticas de contraseña disponible que se puede aplicar a cuentas de usuario creadas y administradas en Azure AD.

| Propiedad | Requisitos |
|   ----------------------- |   ----------------------- |
| Caracteres permitidos | <ul><li>A – Z</li><li>a -z </li><li>0 – 9</li> <li>@ # $ % ^ & * - \_ ! + = [ ] { } | \\ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Caracteres no permitidos | <ul><li>Caracteres Unicode</li><li>Espacios</li><li>espacios</li><li> **Solo contraseñas seguras**: no pueden contener un carácter de punto '.' inmediatamente antes que el símbolo '@'</li></ul> |
| Restricciones de contraseña | <ul><li>8 caracteres como mínimo y 16 caracteres como máximo</li><li>**Solo contraseñas seguras**: requiere 3 de 4 de los siguiente:<ul><li>Caracteres en minúsculas</li><li>Caracteres en mayúsculas</li><li>Números (0-9)</li><li>Símbolos (vea las restricciones de contraseña anteriores)</li></ul></li></ul> |
| Duración de las contraseñas | <ul><li>Valor predeterminado: **90** días </li><li>El valor se puede configurar mediante el cmdlet Set-MsolPasswordPolicy del Módulo Azure Active Directory para Windows PowerShell.</li></ul> |
| Notificación de la expiración de contraseñas | <ul><li>Valor predeterminado: **14** días (antes de que la contraseña expire)</li><li>El valor se puede configurar mediante el cmdlet Set-MsolPasswordPolicy.</li></ul> |
| Expiración de las contraseñas | <ul><li>Valor predeterminado: **false** (indica la expiración de la contraseña está habilitado) </li><li>El valor se puede configurar para cuentas de usuario individuales mediante el cmdlet Set-MsolUser. </li></ul> |
| Historial de contraseñas | No se puede utilizar nuevamente la última contraseña. |
| Duración del historial de contraseñas | Siempre |
| Bloqueo de cuenta | Después de 10 intentos de inicio de sesión incorrectos (contraseña incorrecta), el usuario se bloqueará durante un minuto. Más intentos de inicio de sesión incorrectos bloquearán el usuario para mayores duraciones. |


## Pasos siguientes

* [Administración de contraseñas desde cualquier lugar](active-directory-passwords.md)
* [Funcionamiento de la administración de contraseñas](active-directory-passwords-how-it-works.md)
* [Introducción a la administración de contraseñas](active-directory-passwords-getting-started.md)
* [Personalización de la administración de contraseñas](active-directory-passwords-customize.md)
* [Prácticas recomendadas de administración de contraseñas](active-directory-passwords-best-practices.md)
* [Visión operativa con los informes de administración de contraseñas](active-directory-passwords-get-insights.md)
* [Preguntas más frecuentes sobre la administración de contraseñas](active-directory-passwords-faq.md)
* [Solución de problemas de administración de contraseñas](active-directory-passwords-troubleshoot.md)
* [Más información](active-directory-passwords-learn-more.md)

<!---HONumber=AcomDC_0114_2016-->