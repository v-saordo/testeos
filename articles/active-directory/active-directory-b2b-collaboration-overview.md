<properties
   pageTitle="Colaboración de Azure Active Directory B2B | Microsoft Azure"
   description="La colaboración de Azure Active Directory B2B permite a los socios comerciales tener acceso a sus aplicaciones corporativas, con cada uno de sus usuarios representados por una cuenta de Azure AD única."
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/09/2016"
   ms.author="curtand"/>

# Colaboración de Azure Active Directory (Azure AD) B2B

La colaboración de Azure AD B2B le permite habilitar el acceso a sus aplicaciones corporativas desde identidades administradas por socios. Puede crear relaciones entre empresas invitando y autorizando a los usuarios de empresas asociadas para tener acceso a su recursos. La complejidad se reduce debido a que cada empresa federa una vez con Azure Active Directory y cada usuario está representado por una sola cuenta de Azure AD. La seguridad aumenta porque se revoca el acceso cuando se eliminan los usuarios de socios de sus organizaciones y se impide el acceso no deseado mediante la pertenencia en directorios internos. Para los socios comerciales que ya no tienen Azure AD, la colaboración de B2B tiene una experiencia de registro simplificada para ofrecer las cuentas de Azure AD a sus socios comerciales.

-   Los socios comerciales usan sus propias credenciales de inicio de sesión, lo que le libera de la administración de un directorio de socios externos y de la necesidad de quitar el acceso cuando los usuarios dejen la organización asociada.

-   Administre el acceso a sus aplicaciones de manera independiente de su ciclo de vida de la cuenta de su socio comercial. Esto significa, por ejemplo, que puede revocar el acceso sin tener que solicitar al departamento de TI de su socio comercial que haga nada.

## Capacidades

La colaboración de B2B simplifica la administración y mejora la seguridad del acceso del socio a recursos corporativos, incluidas las aplicaciones SaaS, como Office 365, Salesforce, servicios de Azure y todas las aplicaciones para notificaciones móviles, de nube y locales. La colaboración de B2B permite a los socios administrar sus propias cuentas y las empresas pueden aplicar directivas de seguridad para el acceso de los socios.

La colaboración de Azure Active Directory B2B es sencilla de configurar con registro simplificado para socios de todos los tamaños incluso si no disponen de su propio Azure Active Directory mediante un proceso comprobado por correo electrónico. También es sencillo de mantener sin directorios externos o configuraciones de federación por socio.

El proceso:

1. La colaboración de Azure AD B2B permite a un administrador de compañía invitar y autorizar a un conjunto de usuarios externos cargando un archivo de valores separados por comas (CSV) de no más de 2000 líneas en el portal de colaboración de B2B.

  ![Diálogo de carga de archivos CSV](./media/active-directory-b2b-collaboration-overview/upload-csv.png)

2. El portal enviará invitaciones por correo electrónico a estos usuarios externos.

3. El usuario invitado iniciará sesión en una cuenta profesional existente con Microsoft (administrada en Azure AD) u obtendrá una nueva cuenta profesional en Azure AD.

4. Cuando haya iniciado sesión, se le redirigirá al usuario a la aplicación que se compartió con él.

No se admiten actualmente las invitaciones a las direcciones de correo electrónico de consumidor (por ejemplo, gmail o [*comcast.net*](http://comcast.net/)).

Para obtener más información sobre el funcionamiento de la colaboración de B2B, eche un vistazo a [este vídeo](http://aka.ms/aadshowb2b).

## Formato de archivo CSV

El archivo CSV sigue el siguiente formato. Agregue toda las comas necesarias aunque no especifique una o más opciones.

**Email:** dirección de correo electrónico para el usuario invitado.<br/> **DisplayName:** el nombre para mostrar para el usuario invitado (normalmente, nombre y apellidos).<br/> **InviteAppID:** el id. de la aplicación que se usará para la personalización de marca de la invitación por correo electrónico y las páginas de aceptación.<br/> **InviteReplyURL:** dirección URL a la que se dirigirá un usuario invitado después de la aceptación de la invitación. Debería ser una URL específica de compañía (como [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Si no se especifica este campo opcional, se generará la dirección URL del Panel de acceso de la compañía que realiza la invitación (esta dirección URL tiene el formato `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`).<br/> **InviteAppResources:** AppID a los que las aplicaciones pueden asignar usuarios. Los AppID son recuperables mediante una llamada a `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/> **InviteGroupResources:** los ObjectID para que los grupos agregarán el usuario. Los ObjectID son recuperables mediante una llamada a `Get-MsolGroup | fl DisplayName, ObjectId`<br/> **InviteContactUsUrl:** la URL de contacto que se incluirá en las invitaciones por correo electrónico en caso de que el usuario invitado quiera ponerse en contacto con su organización.<br/>

## Archivo CSV de ejemplo
Este es un CSV de ejemplo que puede modificar para sus objetivos. Guárdelo con cualquier nombre de archivo que prefiera, pero asegúrese de que tiene una extensión de archivo 'csv'.

```
Email,DisplayName,InviteAppID,InviteReplyUrl,InviteAppResources,InviteGroupResources,InviteContactUsUrl
wharp@contoso.com,Walter Harp,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
jsmith@contoso.com,Jeff Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
bsmith@contoso.com,Ben Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
```
## Pasos siguientes
Examine nuestros otros artículos sobre la colaboración de Azure B2B

- [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Cómo funciona](active-directory-b2b-how-it-works.md)
- [Tutorial detallado](active-directory-b2b-detailed-walkthrough.md)
- [Referencia de formato de archivo CSV](active-directory-b2b-references-csv-file-format.md)
- [Formato de token de usuario externo](active-directory-b2b-references-external-user-token-format.md)
- [Cambios de atributo de objeto de usuario externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitaciones de la vista previa actual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->