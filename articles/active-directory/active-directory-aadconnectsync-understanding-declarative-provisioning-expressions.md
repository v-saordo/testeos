<properties
	pageTitle="Sincronización de Azure AD Connect: conocimiento de expresiones de aprovisionamiento declarativo | Microsoft Azure"
	description="Explica las expresiones declarativas de aprovisionamiento."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="markusvi;andkjell"/>


# Sincronización de Azure AD Connect: conocimiento de expresiones de aprovisionamiento declarativo
Azure AD Connect Sync se basa en el aprovisionamiento declarativo presentado por primera vez en Forefront Identity Manager 2010 para permitirle implementar la lógica empresarial completa de integración de identidades sin necesidad de escribir código compilado.

Una parte esencial del aprovisionamiento declarativo es el lenguaje de expresiones que se usa en flujos de atributos. El lenguaje usado es un subconjunto de Microsoft® Visual Basic® para Aplicaciones. Este lenguaje se usa en Microsoft Office, y los usuarios con experiencia en VBScript también lo reconocerán. El lenguaje de expresiones de aprovisionamiento declarativo solo utiliza funciones y no es un lenguaje estructurado; no hay métodos ni instrucciones. Las funciones se anidarán en su lugar en el flujo de programa rápido.

Para más información, vea [Referencia del lenguaje VBA para Office 2013](https://msdn.microsoft.com/library/gg264383.aspx).

Los atributos están fuertemente tipados. Una función solo aceptará los atributos del tipo correcto. También distingue mayúsculas de minúsculas. Los nombres de función y los nombres de atributo deben tener la grafía correcta o se producirá un error

## Identificadores y definiciones de idioma

- Las funciones tienen un nombre seguido de argumentos entre paréntesis: FunctionName(argument 1,argument N).
- Los atributos se especifican entre corchetes: [attributeName]
- Los parámetros se identifican por signos de porcentaje: %ParameterName%
- Las constantes de cadenas están entre comillas: por ejemplo, "Contoso" (Nota: se deben usar comillas rectas "" y no tipográficas "")
- Los valores numéricos se expresan sin comillas y se espera que sean decimales. Los valores hexadecimales van precedidos de &H. Por ejemplo, 98052, &HFF
- Los valores booleanos se expresan con constantes: True, False.
- Las constantes integradas se expresan solo con su nombre: NULL, CRLF, IgnoreThisFlow

### Funciones
El aprovisionamiento declarativo usa muchas funciones que ofrecen la posibilidad de transformar los valores de atributo. Estas se pueden anidar de manera que el resultado de una función se pasa a otra función.

Las funciones también se pueden operar sobre un atributo de varios valores. En este caso la función se opera sobre cada valor individual y se aplica la misma función a cada valor. Por ejemplo `Trim([proxyAddresses])` haría un recorte de cada valor en el atributo proxyAddress.

La lista completa de funciones se puede encontrar en la [referencia de funciones](active-directory-aadconnectsync-functions-reference.md).

### Parámetros

Un parámetro se define mediante un conector o por medio de un administrador que usa PowerShell para definirlo. Los parámetros contendrán normalmente valores que serán diferentes entre sistemas, por ejemplo, el nombre del dominio en el que está ubicado el usuario. Pueden usarse en flujos de atributos.

El conector de Active Directory proporcionó los siguientes parámetros para las reglas de sincronización entrantes:

| Nombre de parámetro | Comentario |
| --- | --- |
| Domain.Netbios | El formato Netbios del dominio importado actualmente, por ejemplo, FABRIKAMSALES |
| Domain.FQDN | El formato FQDN del dominio importado actualmente, por ejemplo, sales.fabrikam.com |
| Domain.LDAP | El formato LDAP del dominio importado actualmente, por ejemplo, DC=ventas,DC=fabrikam,DC=com |
| Forest.Netbios | El formato Netbios del nombre de bosque importado actualmente, por ejemplo, FABRIKAMCORP |
| Forest.FQDN | El formato FQDN del nombre de bosque importado actualmente, por ejemplo, fabrikam.com |
| Forest.LDAP | Formato LDAP del nombre de bosque importado actualmente, por ejemplo, DC=fabrikam,DC=com |

El sistema proporciona el parámetro siguiente, que se usa para obtener el identificador del conector que se está ejecutando actualmente:

`Connector.ID`

Un ejemplo que llenará el dominio del atributo de metaverso con el nombre netbios del dominio en el que se encuentra el usuario:

`domain` <- `%Domain.Netbios%`

### Operadores

Pueden utilizarse los siguientes operadores:

- **Comparación**: <, <=, <>, =, >, >=
- **Matemáticos**: +, -, *, -
- **Cadena**: & (concatenar)
- **Lógico**: && (and), || (or)
- **Orden de evaluación**: ( )

Los operadores se evalúan de izquierda a derecha y tienen la misma prioridad de evaluación. Es decir, la multiplicación (\*) no se evalúa antes que la resta (-). 2\*(5+3) no es lo mismo que 2\*5+3. Los corchetes () se usan para cambiar el orden de evaluación cuando la evaluación de izquierda a derecha no es adecuada.

## Escenarios comunes

### Longitud de los atributos

Los atributos de cadena se establecen de forma predeterminada para que puedan indexarse y tengan una longitud máxima de 448 caracteres. Si está trabajando con atributos de cadena que podrían contener más, asegúrese de incluir lo siguiente en el flujo de atributos:

`attributeName` <- `Left([attributeName],448)`

### Cambio de userPrincipalSuffix

Los usuarios no siempre conocen el atributo userPrincipalName de Active Directory y podría no resultar adecuado como identificador de inicio de sesión. El Asistente para la instalación de Azure AD Connect Sync permite la selección de un atributo diferente, por ejemplo, correo. Sin embargo, en algunos casos, debe calcularse el atributo. Por ejemplo, la empresa Contoso tiene dos directorios de Azure AD, uno para producción y otro para pruebas. Quieren que los usuarios de su inquilino de prueba solo cambien el sufijo del identificador de inicio de sesión.

`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

En esta expresión tomamos todo de la izquierda en el primer @-sign (Word) y se concatena con una cadena fija.

### Conversión de varios valores en un valor único

Algunos atributos de Active Directory tienen varios valores en el esquema aunque parezca que tienen un valor en Usuarios y equipos de Active Directory. Un ejemplo es el atributo description.

`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

En esta expresión, en caso de que el atributo tenga un valor, tomamos el primer elemento (Elemento) en el atributo, quitamos los espacios iniciales y finales (Recorte) y, a continuación, conservamos los primeros 448 caracteres (Izquierda) en la cadena.

## Conceptos avanzados

### NULL frente a IgnoreThisFlow

Para las reglas de sincronización entrantes, debe usarse siempre la constante **NULL**. Esto indica que el flujo no tiene ningún valor para contribuir y otra regla puede aportar un valor. Si no hay ninguna regla que aporte un valor, se quita el atributo metaverse.

Existen dos constantes distintas que utilizar par alas reglas de sincronización salientes: NULL e IgnoreThisFlow. Ambas indican que el flujo de atributos no tiene nada a lo que contribuir, pero la diferencia es lo que ocurre cuando ninguna otra regla tiene nada que aportar. Si hay un valor existente en el directorio conectado, se llevará a cabo una eliminación del valor NULL en el atributo quitándolo, mientras que IgnoreThisFlow conservará el valor existente.

### ImportedValue

La función ImportedValues es diferente de todas las demás funciones, ya que el nombre del atributo debe incluirse entre comillas, en lugar de corchetes: ImportedValue(“proxyAddresses”).

Normalmente, un atributo usará el valor esperado durante la sincronización, incluso si no se ha exportado todavía o se recibió un error durante la exportación ("parte superior de la torre"). Una sincronización entrante supondrá que un atributo que todavía no haya llegado a un directorio conectado lo alcanzará en algún momento. En algunos casos es importante sincronizar únicamente un valor confirmado por el directorio conectado y, en este caso, se usa la función ImportedValue ("torre de importación delta y holograma").

Encontrará un ejemplo de esto en la regla de sincronización lista para su aplicación en AD – User Common desde Exchange, donde en Hybrid Exchange el valor agregado por Exchange Online solo debe sincronizarse si se ha confirmado que el valor se ha exportado correctamente:

`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValues("proxyAddresses")))`

Para una lista completa de funciones, vea [Azure AD Connect Sync: referencia de funciones](active-directory-aadconnectsync-functions-reference.md).


## Recursos adicionales

* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->

<!---HONumber=AcomDC_0218_2016-->