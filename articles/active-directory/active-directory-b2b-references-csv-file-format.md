<properties
   pageTitle="Formato de archivo CSV para la vista previa de colaboración de Azure Active Directory B2B | Microsoft Azure"
   description="Azure Active Directory B2B posibilita las relaciones entre empresas al permitir que los asociados empresariales accedan de forma selectiva a las aplicaciones corporativas."
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="02/09/2016"
   ms.author="viviali"/>

# Vista previa de la colaboración B2B de Azure AD: formato de archivo CSV

La versión de vista previa de la colaboración de Azure AD B2B requiere un archivo CSV en el que se especifique la información del usuario del asociado. Dicho archivo CSV debe cargarse mediante el Portal de Azure AD. El archivo CSV debe incluir las siguientes etiquetas obligatorias, así como determinados campos opcionales según sea necesario. Modifique el archivo CSV de ejemplo (abajo) sin cambiar el texto de las etiquetas de la primera fila ni reordenar las columnas.

>[AZURE.NOTE] Las etiquetas de la primera fila (por ejemplo, Email, DisplayName, etc.) son necesarias para que el archivo CSV se analice correctamente. El nombre de las etiquetas debe coincidir exactamente con los campos que se especifican a continuación. Estas etiquetas identifican el contenido de las columnas. En el caso de los campos opcionales que no son obligatorios, las etiquetas se pueden eliminar del archivo CSV. La columna entera puede dejarse vacía.

## Campos obligatorios: <br/>
**Email:** dirección de correo electrónico del usuario invitado. <br/> **DisplayName:** el nombre para mostrar del usuario invitado (normalmente, el nombre y los apellidos).<br/> **InviteContactUsUrl:** URL que se debe incluir en las invitaciones que se mandan por correo electrónico por si el usuario invitado quiere ponerse en contacto con su organización.<br/>

## Campos opcionales: <br/>
**InviteAppID:** el Id. que debe usar la aplicación para personalizar la marca en la invitación por correo electrónico y las páginas de aceptación.<br/> **InviteAppResources:** AppID corporativos que se deben asignar a los usuarios. Los AppID se pueden recuperar en PowerShell haciendo una llamada a `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId`<br/> **InviteGroupResources:** ObjectIDs a los que los grupos pueden agregar usuarios. Los ObjectID se pueden recuperar en PowerShell efectuando una llamada a `Get-MsolGroup | fl DisplayName, ObjectId`<br/>. **InviteReplyURL:** URL para dirigir a un usuario invitado después de aceptar la invitación. Debería ser una URL específica de la compañía (por ejemplo, [*contoso.my.salesforce.com*](http://contoso.my.salesforce.com/)). Si no se especifica este campo opcional, el usuario invitado se dirige al panel de acceso a las aplicaciones desde el que puede acceder a las aplicaciones corporativas seleccionadas. La URL del panel de acceso a la aplicación tiene el formato `https://account.activedirectory.windowsazure.com/applications/default.aspx?tenantId=<TenantID>`.<br/> **Language:** idioma del correo electrónico de invitación y la experiencia de canje. Si no se especifica otro valor, se usa el inglés. Los otros 10 códigos de idioma admitidos son:<br/> 1. de: alemán<br/> 2. es: español<br/> 3. fr: francés<br/> 4. it: italiano<br/> 5. ja: japonés<br/> 6. ko: coreano<br/> 7. pt-BR: portugués (Brasil)<br/> 8. ru: ruso<br/> 9. zh-HANS: chino simplificado<br/> 10. zh-HANT: chino tradicional<br/>

## Archivo CSV de ejemplo
Este es un archivo CSV de ejemplo que puede modificar.

>[AZURE.NOTE] Copie y pegue esto en el Bloc de notas y guárdelo con una extensión de archivo .csv. A continuación, realice ediciones en Excel. Se estructurará en una tabla con las etiquetas en la primera fila.

>[AZURE.NOTE] Puede agregar más campos opcionales en Excel especificando la etiqueta y rellenando la columna situada debajo.

```
Email,DisplayName,InviteAppID,InviteReplyUrl,InviteAppResources,InviteGroupResources,InviteContactUsUrl
wharp@contoso.com,Walter Harp,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
jsmith@contoso.com,Jeff Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
bsmith@contoso.com,Ben Smith,cd3ed3de-93ee-400b-8b19-b61ef44a0f29,http://azure.microsoft.com/services/active-directory/,,,http://azure.microsoft.com/services/active-directory/
```

## Artículos relacionados
Examine nuestros otros artículos sobre la colaboración de Azure B2B:

- [¿Qué es la colaboración de Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Cómo funciona](active-directory-b2b-how-it-works.md)
- [Tutorial detallado](active-directory-b2b-detailed-walkthrough.md)
- [Formato de token de usuario externo](active-directory-b2b-references-external-user-token-format.md)
- [Cambios de atributo de objeto de usuario externo](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Limitaciones de la vista previa actual](active-directory-b2b-current-preview-limitations.md)
- [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0211_2016-->