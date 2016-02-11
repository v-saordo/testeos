<properties
   pageTitle="Eventos del informe de auditoría de Azure Active Directory | Microsoft Azure"
   description="Eventos auditados que están disponibles para ver y descargar desde Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/07/2015"
   ms.author="kenhoff"/>

# Eventos del Informe de auditoría de Azure Active Directory

*Esta documentación forma parte de la [guía de informes de Azure Active Directory](active-directory-reporting-guide.md).*

El Informe de auditoría de Azure Active Directory ayuda a los clientes a identificar las acciones con privilegios que se produjeron en su Azure Active Directory. Las acciones con privilegios incluyen cambios de elevación (por ejemplo, creación de roles o restablecimientos de contraseña), cambios de configuraciones de directiva (por ejemplo, directivas de contraseña) o cambios de configuración de directorio (por ejemplo, cambios en la configuración de la federación de dominio). Los informes proporcionan el registro de auditoría para el nombre del evento, el actor que realizó la acción, el recurso de destino que se ve afectado por el cambio y la fecha y hora (en UTC). Los clientes pueden recuperar la lista de eventos de auditoría para su Azure Active Directory a través del [Portal de administración de Azure](https://manage.windowsazure.com/), como se describe en [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md).


## Lista de eventos del informe de auditoría
<!--- audit event descriptions should be in the past tense --->

Eventos | Descripción del evento
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Eventos de usuario** |
Agregar usuario | Agregó un usuario al directorio.
Eliminar usuario | Eliminó un usuario del directorio.
Establecer propiedades de la licencia | Estableció las propiedades de la licencia para un usuario en el directorio.
Restablecer contraseña de usuario | Restableció la contraseña de un usuario en el directorio.
Cambiar la contraseña de usuario | Cambió la contraseña de un usuario en el directorio.
Cambiar la licencia de usuario | Cambió la licencia asignada a un usuario en el directorio. Para ver qué licencias se han actualizado, examine el evento "Actualizar usuario" inmediatamente antes o después de este evento.
Actualizar usuario | Actualizó un usuario en el directorio. [Consulte a continuación](#quotupdate-userquot-attributes) los atributos que se pueden actualizar.
Establecer el cambio forzado de la contraseña de usuario | Estableció la propiedad que fuerza a un usuario a cambiar su contraseña en el inicio de sesión.
**Eventos de grupo** |
Agregar grupo | Se crea un grupo en el directorio.
Actualizar grupo | Se actualiza un grupo del directorio.
Eliminar grupo | Se elimina un grupo del directorio.
Agregar miembro a grupo | Se agrega un miembro a un grupo del directorio.
Quitar miembro de grupo | Se quita un miembro de un grupo del directorio.
**Eventos de aplicación** |
Agregar entidad de servicio | Agregó una entidad de servicio al directorio.
Quitar entidad de servicio | Quitó una entidad de servicio del directorio.
Agregar credenciales de la entidad de servicio | Agregó credenciales a una entidad de servicio.
Quitar credenciales de la entidad de servicio | Quitó las credenciales de una entidad de servicio.
Agregar entrada de delegación | Se crea [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) en el directorio.
Establecer entrada de delegación | Se actualiza [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) en el directorio.
Quitar entrada de delegación | Se elimina [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) en el directorio.
**Eventos de rol** |
Agregar miembro de rol a Rol | Agregó un usuario a un rol del directorio.
Quitar miembro de rol de Rol | Quitó un usuario de un rol del directorio.
Establecer la información de contacto de la compañía | Establecer preferencias de contacto a nivel de compañía. Incluye direcciones de correo electrónico para marketing, así como notificaciones técnicas sobre Microsoft Online Services.
**Eventos B2B** |
Invitaciones por lotes cargadas. | Un administrador ha cargado un archivo que contiene invitaciones para enviar a usuarios asociados.
Invitaciones por lotes procesadas. | Se ha procesado un archivo que contiene invitaciones a usuarios asociados.
Invitar a usuario externo. | Se ha sido invitado a un usuario externo al directorio.
Canjear invitación a usuario externo. | Un usuario externo ha canjeado su invitación al directorio.
Agregar usuario externo al grupo. | Se ha asignado una suscripción a un usuario externo a un grupo del directorio.
Asignar usuario externo a la aplicación. | Se ha asignado a un usuario externo acceso directo a una aplicación.
Creación de inquilinos viral. | Se ha creado un nuevo inquilino en Azure AD mediante el canje de una invitación.
Creación de usuarios viral. | Se ha creado un usuario en un inquilino existente en Azure AD mediante el canje de una invitación.
**Eventos de directorio** |
Agregar asociado a la compañía | Agregó un asociado al directorio.
Quitar asociado de la compañía | Quitó un asociado del directorio.
Agregar dominio a la compañía | Agregó un dominio al directorio.
Quitar dominio de la compañía | Quitó un dominio del directorio.
Actualizar dominio | Actualizó un dominio en el directorio.
Establecer la autenticación de dominio | Se cambió la configuración de dominio predeterminada para la compañía.
Establecer la configuración de la federación en el dominio | Actualizó la configuración de la federación para un dominio.
Comprobar dominio | Comprobó un dominio en el directorio.
Comprobar dominio verificado por correo electrónico | Comprobó un dominio en el directorio mediante la verificación por correo electrónico.
Establecer la marca DirSyncEnabled en la compañía | Estableció la propiedad que habilita un directorio para Sincronización de Azure AD.
Establecer directiva de contraseñas | Estableció las restricciones de longitud y caracteres para las contraseñas de usuario.
Establecer información sobre la compañía | Actualizó la información a nivel de la compañía. Consulte el cmdlet [Get-MsolCompanyInformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) de PowerShell para obtener más información.

<!---

List of events that still need descriptions:

Restore Application
Set String Auth Policy
Promote tenant to partner

--->

## Retención de informes de auditoría
Los eventos del informe de auditoría de Azure AD se conservan durante 180 días. Para obtener más información acerca de la retención de los informes, consulte [Directivas de retención de informes de Azure Active Directory](active-directory-reporting-retention.md).

Para los clientes interesados en almacenar eventos de auditoría durante períodos de retención más largos, se puede utilizar la API de informes para extraer periódicamente los eventos de auditoría en un almacén de datos independiente. Consulte [Introducción a la API de informes](active-directory-reporting-api-getting-started.md) para obtener más información.

## Propiedades que se incluyen con cada evento de auditoría

Propiedad | Descripción
------------- | --------------------------------------------------------------
Fecha y hora | La fecha y la hora en que se produjo el evento de auditoría
Actor | El usuario o la entidad de servicio que realizó la acción
Acción | La acción que se realizó
Destino | El usuario o la entidad de servicio en que se realizó la acción


## Atributos de "Actualizar usuario"
El evento de auditoría "Actualizar usuario" incluye información adicional sobre los atributos de usuario que se actualizaron. Para cada atributo, se incluye el valor anterior y el valor nuevo.

Atributo | Descripción
------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | El usuario puede iniciar sesión.
AssignedLicense | Todas las licencias que se han asignado al usuario.
AssignedPlan | Planes de servicio que se derivan de las licencias asignadas al usuario.
LicenseAssignmentDetail | Detalles sobre las licencias asignadas al usuario. Por ejemplo, si se incluyeran licencias basadas en un grupo, esto incluiría a todo el grupo que concedió la licencia.
Móvil | Teléfono móvil del usuario.
OtherMail | Dirección de correo electrónico alternativa del usuario.
OtherMobile | Teléfono móvil alternativo del usuario.
StrongAuthenticationMethod | Una lista de métodos de comprobación configurados por el usuario para Multi-Factor Authentication, como la llamada de voz, los SMS o el código de comprobación de una aplicación móvil.
StrongAuthenticationRequirement | Si Multi-Factor Authentication se exige, habilita o deshabilita para este usuario.
StrongAuthenticationUserDetails | Número de teléfono, número de teléfono alternativo y dirección de correo electrónico del usuario que se usan para Multi-Factor Authentication y para la comprobación de restablecimiento de contraseña.
TelephoneNumber | Número de teléfono del usuario.

Los registros de auditoría son un control necesario para muchas regulaciones de conformidad. Para que los clientes que usan el Informe de auditoría de Azure Active Directory cumplan las regulaciones de conformidad, se recomienda que el cliente envíe una copia de este tema de ayuda con la copia del informe de auditoría exportado del cliente para ayudar a explicar los detalles del informe. Si el auditor desea conocer las regulaciones de conformidad que cumple actualmente Azure, diríjalo a la [página Conformidad](https://azure.microsoft.com/support/trust-center/compliance/) del Centro de confianza de Microsoft Azure.

<!---HONumber=AcomDC_0128_2016-->