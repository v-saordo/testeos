<properties
	pageTitle="Vista previa de Servicios de dominio de Azure Active Directory: Guía de solución de problemas | Microsoft Azure"
	description="Guía de solución de problemas Servicios de dominio de Azure AD"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Servicios de dominio de Azure AD *(vista previa)*: Guía de solución de problemas
En este artículo se ofrecen sugerencias de solución de problemas para los problemas que puede encontrar al configurar o administrar Servicios de dominio de Azure Active Directory (AD).


### No puede habilitar los Servicios de dominio de Azure AD para su directorio de Azure AD
Si se da una situación en la que intenta habilitar los Servicios de dominio de Azure AD para su directorio, pero se produce un error o se vuelve a cambiar a 'Deshabilitado', realice los siguientes pasos:

- Asegúrese de que no tenga un dominio existente con el mismo nombre de dominio disponible en esa red virtual. Por ejemplo, supongamos que tiene un dominio llamado 'contoso.com' ya disponible en la red virtual seleccionada. Posteriormente, intenta habilitar un dominio administrado de Servicios de dominio de Azure AD con el mismo nombre de dominio (es decir, "contoso.com") en esa red virtual. Al intentar habilitar los Servicios de dominio de Azure AD, encontrará un error. Esto se debe a los conflictos de nombre en el nombre de dominio de esa red virtual. En esta situación, debe utilizar un nombre diferente para configurar el dominio administrado de los Servicios de dominio de Azure AD. Como alternativa, puede aprovisionar el dominio existente y luego proceder a habilitar los Servicios de dominio de Azure AD.

- Vea si tiene una aplicación con el nombre 'Sincronización de Servicios de dominio de Azure AD' en el directorio de Azure AD. Si existe, deberá eliminarla y luego volver a habilitar los Servicios de dominio de Azure AD. Realice los pasos siguientes para comprobar la presencia de la aplicación y eliminarla, en caso de que exista:

  1. Navegue al **Portal de administración de Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. En el panel izquierdo, seleccione **Active Directory**.
  3. Seleccione al inquilino (directorio) de Azure AD para el que quiere habilitar los Servicios de dominio de Azure AD.
  4. Vaya a la pestaña **Aplicaciones**.
  5. Seleccione la opción **Aplicaciones que tiene mi compañía** en la lista desplegable.
  6. Busque una aplicación llamada **Sincronización de Servicios de dominio de Azure AD**. Si la aplicación existe, proceda a eliminarla.
  7. Cuando haya eliminado la aplicación, intente volver a habilitar los Servicios de dominio de Azure AD una vez más.


### Los usuarios no pueden iniciar sesión en el dominio administrado de los Servicios de dominio de Azure AD
Si encuentra una situación en la que uno o más usuarios de su inquilino de Azure AD no pueden iniciar sesión en el dominio administrado creado recientemente, lleve a cabo los siguientes pasos de solución de problemas:

- Asegúrese de que ha [habilitado la sincronización de contraseñas](active-directory-ds-getting-started-password-sync.md) según los pasos que se describen en la Guía de introducción.

- Asegúrese de que la cuenta de usuario afectada no es una cuenta externa en el inquilino de Azure AD. Entre los ejemplos de las cuentas externas se incluyen las cuentas de Microsoft (por ejemplo, 'joe@live.com') o las cuentas de usuario de un directorio de Azure AD externo. Puesto que los Servicios de dominio de Azure AD no tienen las credenciales de dichas cuentas de usuario, estos usuarios no pueden iniciar sesión el dominio administrado.

- Asegúrese de que el prefijo del UPN de la cuenta de usuario afectada (es decir, la primera parte del UPN) del inquilino de Azure AD tiene menos de 20 caracteres de longitud. Por ejemplo, para el UPN 'joereallylongnameuser@contoso.com', el prefijo ('joereallylongnameuser') supera los 20 caracteres y esta cuenta no estará disponible en el dominio administrado de Servicios de dominio de Azure AD.

- **Cuentas sincronizadas:** si las cuentas de usuario afectadas se sincronizan desde un directorio local, compruebe lo siguiente:
    - Ha implementado la [versión más reciente recomendada de Azure AD Connect](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect) o se ha actualización a dicha versión.
    - Ha configurado Azure AD Connect para [realizar una sincronización completa](active-directory-ds-getting-started-password-sync.md).
    - En función del tamaño de su directorio, se puede tardar algo en que las cuentas de usuario y los hash de credenciales estén disponibles en Servicios de dominio de Azure AD. Asegúrese de esperar el tiempo suficiente antes de volver a intentar la autenticación (depende del tamaño de su directorio, desde unas horas a un día o dos, para directorios grandes).

- **Cuentas de solo en la nube**: si la cuenta de usuario afectada es una cuenta de usuario de solo en la nube, asegúrese de que el usuario ha cambiado su contraseña después de habilitar Servicios de dominio de Azure AD. Este paso hace que se generen los hash de credenciales necesarios para los Servicios de dominio de Azure AD


### Ponerse en contacto con nosotros
Si tiene problemas con su dominio administrado, compruebe si los pasos descritos en esta guía de solución de problemas resuelven el problema. Si sigue teniendo problemas, no dude en ponerse en contacto con nosotros en:

- **Correo electrónico:** puede enviarnos un correo electrónico a la sección de [comentarios sobre los Servicios de dominio de Azure AD](mailto:aaddsfb@microsoft.com). Asegúrese de incluir el id. de inquilino de su directorio de Azure AD y el nombre de dominio que ha configurado para los Servicios de dominio de AAD, para que podemos investigar el problema.
- **[Canal de voz del usuario de Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/):** asegúrese de escribir delante de su pregunta **'AADDS'** para ponerse en contacto con nosotros.

<!---HONumber=AcomDC_0128_2016-->