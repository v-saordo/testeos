<properties
   pageTitle="Conceptos de diseño de Azure AD Connect | Microsoft Azure"
   description="En este tema se detallan algunas áreas de diseño de implementación"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Identity"
   ms.date="02/16/2016"
   ms.author="andkjell"/>

# Azure AD Connect: conceptos de diseño
El propósito de este tema es describir las áreas que se deben tener en cuenta durante el diseño de implementación de Azure AD Connect. Se trata de una profundización en determinadas áreas, y estos conceptos se describen también brevemente en otros temas.

## sourceAnchor
El atributo sourceAnchor se define como *un atributo inmutable durante la vigencia de un objeto*. Identifica de forma única que un objeto es el mismo objeto en el entorno local y en Azure AD. El atributo también se denomina **immutableId** y los dos nombres se usan indistintamente.

La palabra inmutable, es decir, no se puede cambiar, es importante en este tema. Puesto que el valor de este atributo no se puede cambiar después de que se haya establecido, es importante elegir un diseño que respalde su caso.

El atributo se usa en las situaciones siguientes:

- Cuando un nuevo servidor de motor de sincronización se genera, o regenera, después de una situación de recuperación ante desastres, este atributo vinculará los objetos existentes en Azure AD con los objetos locales.
- Si se mueve de una identidad solo de nube a un modelo de identidad sincronizada, este atributo permitirá que los objetos existentes en Azure AD coincidan exactamente con los objetos locales.
- Si usa federación, este atributo junto con el **userPrincipalName** se usan en la notificación para identificar de forma exclusiva un usuario.

En este tema solo se habla de sourceAnchor puesto que está relacionado con los usuarios. Las mismas reglas se aplican a todos los tipos de objeto, pero es solo este atributo el que supone un problema normalmente para los usuarios.

### Selección de un buen atributo sourceAnchor
El valor de atributo debe seguir las reglas siguientes:

- Debe tener una longitud de menos de 60 caracteres.
- No debe contener caracteres especiales: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " @ \_
- Debe ser único globalmente.
- Debe ser una cadena, un entero o un número binario.
- No se debe basar en el nombre del usuario, */*estos cambios
- No debe distinguir mayúsculas y minúsculas; evite valores que pueden variar según el caso
- Debe asignarse cuando se crea el objeto.

Si el atributo sourceAnchor seleccionado no es de tipo cadena, Azure AD Connect aplicará Base64Encode al valor de atributo para asegurarse de que no se muestre ningún carácter especial. Si usa otro servidor de federación distinto de ADFS, asegúrese de que también tenga la capacidad para aplicar Base64Encode al atributo.

El atributo sourceAnchor distingue mayúsculas de minúsculas. El valor "JohnDoe" no es igual que "johndoe".

Si tiene un solo bosque local, el atributo que debe usar es **objectGUID**. También es el atributo que se usa con la configuración rápida en Azure AD Connect y el atributo de sincronización de directorios.

Si tiene varios bosques y no mueve usuarios entre bosques y dominios del mismo bosque, **objectGUID** es un buen atributo para usar incluso en este caso.

Si mueve usuarios entre bosques y dominios, debe buscar un atributo que no cambie o se puede mover con los usuarios durante el movimiento. Un enfoque recomendado es introducir un atributo sintético. Un atributo que pueda contener algo parecido a un GUID sería adecuado. Durante la creación del objeto se crea un nuevo GUID y se marca en el usuario. Puede crear una regla personalizada en el servidor del motor de sincronización para crear este valor según el **objectGUID** y actualizar el atributo seleccionado en ADDS. Al mover el objeto, asegúrese de copiar también el contenido de este valor.

Otra solución consiste en seleccionar un atributo existente que sepa que no cambiará. Uno de los atributos de uso más común es **employeeID**. Si está considerando el uso de un atributo que contenga letras, asegúrese de que no haya ninguna posibilidad de que la distinción de mayúsculas y minúsculas pueda cambiar el valor del atributo. Entre los atributos incorrectos que no deben usarse se incluyen aquellos con el nombre del usuario. En un matrimonio o divorcio se espera que cambie el nombre, lo que no está permitido para este atributo. Este también es uno de los motivos de que atributos como **userPrincipalName**, **mail** y **targetAddress** no se puedan seleccionar en el asistente de instalación de Azure AD Connect. Esos atributos también contendrán el carácter @, que no está permitido en sourceAnchor.

### Cambio del atributo sourceAnchor
No se puede cambiar el valor del atributo sourceAnchor después de que el objeto se ha creado en Azure AD y la identidad se ha sincronizado.

Por este motivo, se aplican las restricciones siguientes a Azure AD Connect:

- El atributo sourceAnchor solo puede establecerse durante la instalación inicial. Si vuelve a ejecutar el asistente de instalación, esta opción es de solo lectura. Si necesita cambiar esto, debe desinstalar y reinstalar la aplicación.
- Si instala otro servidor de Azure AD Connect, debe seleccionar el mismo atributo sourceAnchor usado anteriormente. Si anteriormente ha estado usando la sincronización de directorios y se pasa a Azure AD Connect, debe usar **objectGUID** ya que es el atributo de sincronización de directorios.
- Si se cambia el valor de sourceAnchor después de que el objeto se ha exportado a Azure AD, la sincronización de Azure AD Connect producirá un error y no permitirá más cambios en ese objeto antes de que el problema se haya corregido y el atributo sourceAnchor cambie de nuevo en el directorio de origen.

## Pasos siguientes
Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->