<properties
   pageTitle="Sincronización de Azure AD Connect: descripción de la configuración predeterminada | Microsoft Azure"
   description="En este artículo se describe la configuración predeterminada de sincronización de Azure AD Connect."
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
   ms.date="01/21/2016"
   ms.author="andkjell"/>

# Sincronización de Azure AD Connect: descripción de la configuración predeterminada

En este artículo se explican las reglas de configuración rápida. Se documentan las reglas y cómo afectan a la configuración. También le sirve de guía en la configuración predeterminada de Azure AD Connect Sync. El objetivo es que el lector comprenda cómo funciona el modelo de configuración, denominado aprovisionamiento declarativo, en un ejemplo del mundo real. En este artículo se supone que ya instaló y configuró Azure AD Connect Sync mediante el asistente para instalación.

## Reglas integradas entre el entorno local y Azure AD

Las expresiones siguientes se pueden encontrar en la configuración rápida.

Las reglas se expresan como reglas que deben cumplirse y como objetos que se deben filtrar (si se cumple la regla, **no** sincronizar).

### Reglas integradas de usuario

Estas reglas también se aplican al tipo de objeto iNetOrgPerson.

Un objeto de usuario debe cumplir los siguientes requisitos para que se sincronice:

- Debe tener un valor de sourceAnchor.
- Después de crear el objeto en Azure AD, no se puede cambiar el valor de sourceAnchor. Si el valor se modifica en el entorno local, el objeto dejará de sincronizarse hasta que se vuelva a cambiar el valor de sourceAnchor a su valor anterior.
- Debe tener rellenado el atributo accountEnabled (userAccountControl). Con una instancia de Active Directory local, este atributo siempre estará presente y rellenado.

Los siguientes objetos de usuario **no** se sincronizan con Azure AD:

- `IsPresent([isCriticalSystemObject])`. Asegúrese de que muchos objetos incluidos en Active Directory, como la cuenta de administrador integrada, no estén sincronizados.
- `IsPresent([sAMAccountName]) = False`. Asegúrese de que los objetos de usuario sin el atributo sAMAccountName no estén sincronizados. Esto sucedería prácticamente solo en un dominio actualizado desde NT4.
- `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. No sincronice la cuenta de servicio que usa Azure AD Connect Sync y sus versiones anteriores.
- No sincronice las cuentas de Exchange que no funcionan en Exchange Online.
    - `[sAMAccountName] = "SUPPORT_388945a0"`
    - `Left([mailNickname], 14) = "SystemMailbox{"`
    - `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
    - `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
- No sincronice los objetos que no vayan a funcionar en Exchange Online. `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))` Esta máscara de bits (& H21C07000) filtraría los objetos siguientes:
    - Carpeta pública habilitada para correo
    - Buzón del operador del sistema
    - Buzón de la base de datos de buzones (buzón del sistema)
    - Grupo de seguridad universal (no se aplicaría a un usuario, pero existe por motivos de herencia)
    - Grupo no universal (no se aplicaría a un usuario, pero existe por motivos de herencia)
    - Plan de buzón
    - Buzón de detección
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. No sincronice ningún objeto víctima de replicación.

Se aplican las siguientes reglas de atributo:

- `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. El atributo sourceAnchor no procederá de un buzón vinculado. Se supone que si se ha encontrado un buzón vinculado, la cuenta real se unirá más tarde.
- Los atributos relacionados con Exchange solo se sincronizan si el atributo **mailNickName** tiene un valor.
- Cuando hay varios bosques, los atributos se consumen en el orden siguiente:
    - Los atributos relacionados con el inicio de sesión (por ejemplo, userPrincipalName) procederán del bosque con una cuenta habilitada.
    - El atributo que se encuentra en una GAL de Exchange (lista Global de direcciones) procederá del bosque con un buzón de Exchange.
    - Si no se encuentra ningún buzón, estos atributos pueden provenir de cualquier bosque.
    - Los atributos relacionados con Exchange proceden del bosque donde `mailNickname ISNOTNULL`.
    - Si hay varios bosques que podrían satisfacer una de estas reglas, se utilizará el orden de creación (fecha y hora) de los conectores (bosques) para determinar qué bosque contribuirá a los atributos.

### Reglas integradas de contacto

Un objeto de contacto debe cumplir los siguientes requisitos para sincronizarse:

- El contacto debe estar habilitado para correo. Se comprueba con las reglas siguientes:
    - `IsPresent([proxyAddresses]) = True)`. El atributo proxyAddresses debe estar rellenado.
    - Se puede encontrar una dirección de correo electrónico principal en el atributo proxyAddresses o en el atributo de correo. La precedencia de una @ se usa para comprobar que el contenido es una dirección de correo electrónico. Uno de estos dos atributos debe evaluarse como True.
        - `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. ¿Hay una entrada con "SMTP:" y, si la hay, se puede encontrar una @ en la cadena?
        - `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. ¿Está rellenado el atributo mail y, si lo está, se puede encontrar una @ en la cadena?

Los siguientes objetos de contacto **no** se sincronizan con Azure AD:

- `IsPresent([isCriticalSystemObject])`. Asegúrese de que ningún objeto marcado como crítico se sincronice. No debería ser ninguno con una configuración predeterminada.
- `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
- `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Estos no funcionaran en Exchange Online.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. No sincronice ningún objeto víctima de replicación.

### Reglas integradas de grupo

Un objeto de grupo debe cumplir los siguientes requisitos para sincronizarse:

- Debe tener menos de 50.000 miembros. Estos se cuentan como el número de miembros del grupo local.
    - Si tiene más miembros antes de que la sincronización se inicie por primera vez, el grupo no se sincronizará.
    - Si el número de miembros crece desde que se creara inicialmente, cuando alcance 50.000 miembros dejará de sincronizarse hasta que el número de miembros sea de nuevo inferior a 50.000.
    - Nota: Azure AD también aplicará el número de miembros de 50.000. No podrá sincronizar los grupos con más miembros incluso si modifica o elimina esta regla.
- Si el grupo es un **grupo de distribución**, entonces también deberá estar habilitado para correo. Consulte [Reglas integradas de contacto](#contact-out-of-box-rules) para aplicar esta regla.

Los siguientes objetos de grupo **no** se sincronizan con Azure AD:

- `IsPresent([isCriticalSystemObject])`. Asegúrese de muchos objetos incluidos en Active Directory, como el grupo de administradores integrado, no estén sincronizados.
- `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Grupo heredado utilizado por DirSync.
- `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Grupo de roles.
- `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. No sincronice ningún objeto víctima de replicación.

### Reglas integradas de ForeignSecurityPrincipal

Los FSP se unen a "cualquier" (*) objeto en el metaverso. Esto solo sucederá en realidad para usuarios y grupos de seguridad. De este modo se garantiza que los miembros de otro bosque se resolverán y representarán correctamente en Azure AD.

### Reglas integradas de equipo

Un objeto de equipo debe cumplir los siguientes requisitos para sincronizarse:

- `userCertificate ISNOTNULL`. Solo los objetos de equipo Windows 10 rellenarán este atributo. Todos los objetos de equipo con un valor en este atributo se sincronizan.

## Descripción del escenario de reglas integradas

En este ejemplo usamos una implementación con un bosque de cuentas (A), un bosque de recursos (R) y un directorio de Azure AD.

![escenario](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

En esta configuración se supone que ya hay una cuenta habilitada en el bosque de cuentas y una cuenta deshabilitada en el bosque de recursos con un buzón vinculado.

Nuestro objetivo con la configuración predeterminada es el siguiente:

- La información de los atributos relacionada con el inicio de sesión se sincronizará desde el bosque con la cuenta habilitada.
- Los atributos que se encuentran en la LGD (lista global de direcciones) se sincronizarán desde el bosque con el buzón. Si no se encuentra ningún buzón, se usará cualquier otro bosque.
- Si hay un buzón vinculado, se debe encontrar la cuenta habilitada vinculada para poder exportar el objeto a Azure AD.

### Editor de reglas de sincronización

Se puede ver y cambiar la configuración con la herramienta Editor de reglas de sincronización (SRE), y hay un acceso directo a ella en el menú Inicio.

![Editor de reglas de sincronización](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

El Editor de reglas de sincronización es una herramienta del kit de recursos y se instala con la sincronización de Azure AD Connect. Para poder iniciarlo debe ser miembro del grupo ADSyncAdmins. Cuando se inicie, verá algo parecido a lo siguiente:

![Reglas de sincronización entrantes](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

En este panel verá todas las reglas de sincronización creadas para su configuración. Cada línea de la tabla es una regla de sincronización. A la izquierda, en Tipos de regla, se muestran los dos tipos diferentes: entrantes y salientes. La perspectiva entrante y saliente corresponde al punto de vista del metaverso. Nos centraremos, principalmente, en las reglas entrantes en esta información general. La lista real de reglas de sincronización dependerá del esquema detectado en AD. En la imagen anterior, el bosque de cuentas (fabrikamonline.com) no dispone de ningún servicio, como Exchange y Lync, y no se crearon reglas de sincronización para estos servicios. Sin embargo, en el bosque de recursos (res.fabrikamonline.com) encontraremos reglas de sincronización para estos servicios. El contenido de las reglas será diferente en función de la versión detectada. Por ejemplo, en una implementación de Exchange 2013 tendremos configurados más flujos de atributo que en Exchange 2010 y Exchange 2007.

### Regla de sincronización

Una regla de sincronización es un objeto de configuración con un conjunto de atributos que se pasan cuando se cumple una condición. También se utiliza para describir cómo se relaciona un objeto en un espacio de conector con un objeto en el metaverso, proceso conocido como **unión** o **coincidencia**. Las reglas de sincronización tienen un valor de precedencia que indica cómo se relacionan entre sí. Una regla de sincronización con un valor numérico inferior en precedencia tiene una precedencia superior y, en caso de conflicto del flujo de atributo, la precedencia superior prevalecerá en su resolución.

Nos fijaremos, por ejemplo, en la regla de sincronización **In from AD – User AccountEnabled**. Marcaremos esta línea en el Editor de reglas de sincronización y seleccionaremos **Editar**.

Puesto que se trata de una regla integrada, vamos a ver una advertencia al abrir la regla. No debería realizar ningún [cambio en las reglas integradas](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) por lo que se le pregunta cuáles son sus intenciones. En este caso, solo desea ver la regla, así que seleccione **No**.

![Reglas de sincronización entrantes](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

Una regla de sincronización tiene cuatro secciones de configuración: Descripción, Filtro de ámbito, Reglas de unión y Transformaciones.

#### Descripción

La primera sección proporciona información básica como el nombre y la descripción.

![Editar regla de sincronización entrante](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

También encontramos información acerca del sistema conectado con el que se relaciona esta regla, el tipo de objeto del sistema conectado al que se aplica y el tipo de objeto del metaverso. El tipo de objeto del metaverso siempre es persona, independientemente de si el tipo de objeto de origen es usuario, iNetOrgPerson o contacto. El tipo de objeto del metaverso nunca debe cambiar, por lo que se crea como un tipo genérico. El tipo de vínculo se puede establecer en Unión, Unión permanente o Aprovisionamiento. Esta configuración funciona junto con la sección Reglas de unión, que abordaremos más adelante.

Puede ver igualmente que esta regla de sincronización se usa para la sincronización de contraseñas. Si un usuario está en el ámbito de esta regla de sincronización, la contraseña se sincronizará desde local a la nube (suponiendo que esté habilitada la característica de sincronización de contraseñas).

#### Filtro de ámbito

La sección Filtro de ámbito se utiliza para configurar cuándo se debe aplicar una regla de sincronización. Como el nombre de la regla de sincronización que estamos examinando indica que solo debe aplicarse para los usuarios habilitados, el ámbito se configura de modo que el atributo **userAccountControl** de AD no deba tener establecido el bit 2. Cuando encontremos un usuario en AD, aplicaremos esta regla de sincronización si **userAccountControl** está establecido en el valor decimal 512 (usuario normal habilitado) pero no se aplicará si el usuario que encontremos tiene **userAccountControl** establecido en 514 (usuario normal deshabilitado).

![Editar regla de sincronización entrante](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

El filtro de ámbito tiene grupos y las cláusulas que se pueden anidar. Deben cumplirse todas las cláusulas dentro de un grupo para que se aplique una regla de sincronización. Cuando se definen varios grupos, se debe cumplir al menos uno para que la regla se aplique. Por ejemplo, un OR lógico se evalúa entre grupos y un AND lógico se evalúa dentro de un grupo. Un ejemplo de esto se puede encontrar en la regla de sincronización saliente **Out to AAD – Group Join**, que se muestra a continuación. Hay varios grupos de filtros de sincronización, por ejemplo, uno para los grupos de seguridad (securityEnabled EQUAL True) y otro para los grupos de distribución (securityEnabled EQUAL False).

![Editar regla de sincronización saliente](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Esta regla se usa para definir qué grupos se deben aprovisionar a ADD. Los grupos de distribución deben tener el correo habilitado para sincronizarse con ADD, pero esto no es necesario para los grupos de seguridad. Como se puede ver además, también se evalúan bastantes atributos más.

#### Reglas de unión

La tercera sección se usa para configurar cómo se relacionan los objetos del espacio conector con los objetos del metaverso. La regla que hemos examinado antes no tiene ninguna configuración para Reglas de unión, por lo que vamos a examinar en su lugar la regla **In from AD – User Join**.

![Editar regla de sincronización entrante](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

El contenido de las reglas de unión dependerá de la opción de coincidencia seleccionada en el asistente para instalación. Para una regla entrante, la evaluación se inicia con un objeto en el espacio conector de origen y cada grupo de las reglas de unión se evalúa en secuencia. Si se evalúa si un objeto de origen coincide exactamente con un objeto del metaverso usando una de las reglas de unión, los objetos se unen. Si se han evaluado todas las reglas y no hay coincidencia, se usa el tipo de vínculo de la página de descripción. Si este valor se establece en Provision, se crea un nuevo objeto en el destino, el metaverso. Aprovisionar un nuevo objeto en el metaverso también se denomina proyectar un objeto en el metaverso.

Las reglas de unión solo se evalúan una vez. Cuando un objeto del espacio conector y un objeto del metaverso se unen, permanecerán unidos mientras el ámbito de la regla de sincronización se siga cumpliendo.

Cuando se evalúan reglas de sincronización, solo debe haber en el ámbito una regla de sincronización con reglas de unión definidas. Si se encuentran varias reglas de sincronización con reglas de unión para un objeto, se emite un error. Por este motivo, lo mejor es tener solo una regla de sincronización con una unión definida cuando haya varias reglas de sincronización en el ámbito para un objeto. En la configuración integrada para Azure Active Directory, estas reglas se pueden encontrar consultando el nombre y buscando las que tienen la palabra Join al final del mismo. Una regla de sincronización sin ninguna regla de unión definida aplicará los flujos de atributo si otra regla de sincronización ha unido los objetos o ha aprovisionado un nuevo objeto en el destino.

Si observa la imagen anterior, puede ver que la regla está intentando unirse a **objectSID** con **msExchMasterAccountSid** (Exchange) y **msRTCSIP-OriginatorSid** (Lync), que es lo que esperamos de una topología de bosque de recursos y de cuentas. Encontramos la misma regla en todos los bosques, es decir, suponemos que cada bosque puede ser un bosque de cuentas o de recursos. Esto también funciona si dispone de cuentas que residen en un solo bosque y no tienen que unirse.

#### Transformaciones

La sección sobre las transformaciones define todos los flujos de atributo que se aplicarán al objeto de destino cuando se unan los objetos y se cumpla el filtro de ámbito. Si volvemos a nuestra regla de sincronización **In from AD – User AccountEnabled**, encontraremos las transformaciones siguientes:

![Editar regla de sincronización entrante](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Para ponerlo en contexto, en una implementación de bosque cuenta-recurso esperamos encontrar una cuenta habilitada en el bosque de cuenta y una cuenta deshabilitada en el bosque de recurso con la configuración de Exchange y Lync. La regla de sincronización que estamos examinando contiene los atributos necesarios para el inicio de sesión y queremos que fluyan desde el bosque en el que hemos encontrado una cuenta habilitada. Todos estos flujos de atributo se combinan en una regla de sincronización.

Una transformación puede tener tipos diferentes: Constante, Directa y Expresión.

- En un flujo de tipo constante siempre fluirá un valor en particular, en el caso anterior estableceremos siempre el valor True en el atributo del metaverso denominado accountEnabled.
- En un flujo de tipo directo fluirá el valor del atributo del origen hacia el atributo de destino.
- El tercer flujo es de tipo expresión y permite configuraciones más avanzadas.

El lenguaje de la expresión es VBA (Visual Basic para Aplicaciones), por lo que un usuario con experiencia en Microsoft Office o VBScript reconocerá el formato Los atributos se especifican entre corchetes, [nombreAtributo]. Los nombres de atributo y los nombres de función distinguen entre mayúsculas y minúsculas, pero el Editor de reglas de sincronización evaluará las expresiones y proporcionará una advertencia si la expresión no es válida. Todas las expresiones se expresan en una única línea con funciones anidadas. Para mostrar la eficacia del lenguaje de configuración, encontrará a continuación el flujo para pwdLastSet, pero con comentarios adicionales insertados:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by AAD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

El tema de transformación es amplio y proporciona una gran parte de la configuración personalizada posible con sincronización de Azure AD Connect. Puede encontrar más información sobre esto en los otros artículos de la sincronización de Azure AD Connect.

### Precedencia

Hemos examinado algunas reglas de sincronización individuales pero las reglas se usan combinadas en la configuración. En algunos casos, se aporta un valor de atributo desde varias reglas de sincronización al mismo atributo de destino. En este caso, se usa la precedencia de atributos para determinar qué atributo prevalecerá. Por ejemplo, examinemos el atributo sourceAnchor. Este atributo es importante para poder iniciar sesión en Azure AD. Podemos encontrar un flujo de atributo para este atributo en dos reglas de sincronización diferentes, **In from AD – User AccountEnabled** y **In from AD – User Common**. Debido a la precedencia de las reglas de sincronización, el atributo sourceAnchor se aportará desde el bosque con una cuenta habilitada primero si hay varios objetos unidos al objeto del metaverso. Si no hay cuentas habilitadas, usaremos la regla de sincronización para capturar todo **In from AD – User Common**. Esto asegurará que proporcionamos un sourceAnchor incluso para las cuentas que están deshabilitadas.

![Reglas de sincronización entrantes](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

El asistente para instalación establece la precedencia para las reglas de sincronización en grupos. En un grupo de reglas, todas tienen el mismo nombre pero se conectan a diferentes directorios conectados. El asistente para instalación dará a la regla **In from AD – User Join** la precedencia más alta e iterará sobre todos los directorios AD conectados. Después continuará con los grupos de reglas siguientes en un orden predefinido. Dentro de un grupo, las reglas se agregan en el orden en que los conectores se agregaron en el asistente. Si se agrega otro conector mediante el asistente, las reglas de sincronización se reordenarán y las reglas del nuevo conector se insertarán al final de cada grupo.

### Resumen

Ahora conocemos lo suficiente de las reglas de sincronización para poder comprender cómo funciona la configuración con las distintas reglas de sincronización. Si tomamos un usuario y los atributos que se aportan al metaverso, las reglas se aplican en el orden siguiente:

| Nombre | Comentario |
| :------------- | :------------- |
| In from AD – User Join | Regla para unir objetos del espacio del conector con el metaverso. |
| In from AD – UserAccount Enabled | Atributos necesarios para iniciar sesión en Azure AD y Office 365. Estos atributos deben ser de la cuenta habilitada. |
| In from AD – User Common from Exchange | Atributos encontrados en la lista global de direcciones Suponemos que la calidad de los datos es mejor en el bosque donde encontramos el buzón del usuario. |
| In from AD – User Common | Atributos encontrados en la lista global de direcciones En caso de que no encontremos un buzón, cualquier otro objeto unido puede aportar el valor de atributo. |
| In from AD – User Exchange | Solo existirá si se detecta Exchange. Hará fluir todos los atributos de Exchange de la infraestructura. |
| In from AD – User Lync | Solo existirá si se detecta Lync. Hará fluir todos los atributos de Lync de la infraestructura. |

## Recursos adicionales

* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0128_2016-->