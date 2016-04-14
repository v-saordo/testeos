<properties
    pageTitle="Topologías admitidas de Azure AD Connect | Microsoft Azure"
    description="En este tema se detallan las topologías admitidas y no admitidas de Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="AndKjell"
    manager="stevenpo"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
	ms.topic="get-started-article"
    ms.date="02/12/2016"
    ms.author="andkjell"/>

# Topologías de Azure AD Connect

El objetivo de este tema es describir diferentes topologías locales y de Azure AD con Azure AD Connect Sync como la solución de integración clave. Se describen tanto las configuraciones admitidas como las no admitidas.

Leyenda de imágenes en el documento:

| Descripción | Icono |
|-----|-----|
| Bosque local de Active Directory | ![AD](./media/active-directory-aadconnect-topologies/LegendAD1.png)|
| Active Directory con importación filtrada | ![AD](./media/active-directory-aadconnect-topologies/LegendAD2.png)|
| Servidor de Azure AD Connect Sync | ![Sync](./media/active-directory-aadconnect-topologies/LegendSync1.png)|
| "Modo provisional" del servidor de Azure AD Connect Sync | ![Sync](./media/active-directory-aadconnect-topologies/LegendSync2.png)|
| GALSync con FIM2010 o MIM2016 | ![Sync](./media/active-directory-aadconnect-topologies/LegendSync3.png)|
| Servidor de Azure AD Connect Sync, información detallada |![Sync](./media/active-directory-aadconnect-topologies/LegendSync4.png)|
| Directorio de Azure AD |![AAD](./media/active-directory-aadconnect-topologies/LegendAAD.png)|
| Escenario no admitido | ![No compatible](./media/active-directory-aadconnect-topologies/LegendUnsupported.png)



## Un único bosque, un único directorio de Azure AD
![SingleForestSingleDirectory](./media/active-directory-aadconnect-topologies/SingleForestSingleDirectory.png)

La topología más común es un único bosque local, con uno o varios dominios y un único directorio de Azure AD (también conocido como inquilino). La autenticación de Azure AD se realiza con la sincronización de contraseñas. Esta es la topología compatible con la instalación rápida de Azure AD Connect.

### Bosque único, varios servidores de sincronización con un directorio de Azure AD
![SingleForestFilteredUnsupported](./media/active-directory-aadconnect-topologies/SingleForestFilteredUnsupported.png)

No se permite tener varios servidores de Azure AD Connect Sync conectados al mismo directorio de Azure AD, incluso si están configurados para sincronizar un conjunto de objetos mutuamente excluyentes (a excepción de un [servidor provisional](#staging-server)). Esto podría intentarse cuando un dominio de un bosque no es accesible desde una ubicación de red común o en un intento de distribuir la carga de sincronización entre varios servidores.

## Varios bosques, un único directorio de Azure AD
![MultiForestSingleDirectory](./media/active-directory-aadconnect-topologies/MultiForestSingleDirectory.png)

Muchas organizaciones tienen entornos que incluyen varios bosques de Active Directory local. Existen varias razones para tener implementado más de un bosque de Active Directory local. Los ejemplos típicos son los diseños con bosques de cuenta-recurso, bosques relacionados con fusiones y adquisiciones o bosques utilizados para externalizar los datos.

Cuando hay varios bosques, todos los bosques deben ser accesibles mediante un único servidor de Azure AD Connect Sync. El servidor no tiene que estar unido a un dominio y, si es necesario, puede colocarse en una red DMZ para poder tener acceso a todos los bosques.

El asistente de Azure AD Connect ofrece varias opciones para consolidar a los usuarios, de modo que aunque el mismo usuario se represente varias veces en diferentes bosques, solo se representará una vez en Azure AD. A continuación se describen algunas topologías comunes. Para configurar la topología que tiene, use la ruta de instalación personalizada en el asistente de instalación y seleccione la opción correspondiente en la página "Identificación de forma exclusiva de usuarios". La consolidación solo ocurre para los usuarios. Si los grupos están duplicados, estos no se consolidan con la configuración predeterminada.

Las topologías comunes se describen en las siguientes secciones: [Topologías independientes](#multiple-forests-separate-topologies), [Malla completa](#multiple-forests-full-mesh-with-optional-galsync) y [Cuenta-recurso](#multiple-forests-account-resource-forest).

En la configuración predeterminada proporcionada por Azure AD Connect Sync, se dan por hecho los siguientes supuestos:

1.	Los usuarios tienen solo una cuenta habilitada y el bosque donde se encuentra esta cuenta se usa para la autenticación del usuario. Esta se usa para la sincronización de contraseñas y para la federación; userPrincipalName y sourceAnchor/immutableID proceden de este bosque.
2.	Los usuarios tienen solo un buzón.
3.	El bosque que aloja el buzón de un usuario tiene la mejor calidad de datos para los atributos visibles en la lista global de direcciones (GAL) de Exchange. Si no hay ningún buzón en el usuario, puede usarse cualquiera de los bosques para aportar estos valores de atributo.
4.	Si tiene un buzón vinculado, hay también otra cuenta en un bosque diferente que se usa para el inicio de sesión.

Si su entorno no se ajusta a estos supuestos, ocurrirá lo siguiente:

-	Si tiene más de una cuenta activa o más de un buzón, el motor de sincronización elegirá uno y pasará por alto los demás.
-	Si tiene buzones vinculados pero no hay ninguna otra cuenta, estas cuentas no se exportarán a Azure AD y el usuario no será miembro de ningún grupo. En la sincronización de directorios, un buzón vinculado se representará como un buzón normal, de modo que es intencionadamente un comportamiento diferente para una mejor compatibilidad con escenarios de varios bosques.

### Varios bosques, varios servidores de sincronización con un directorio de Azure AD
![MultiForestMultiSyncUnsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiSyncUnsupported.png)

No se puede para tener más de un servidor de Azure AD Connect Sync conectado a un único directorio de Azure AD (a excepción de un [servidor provisional](#staging-server)).

### Varios bosques: topologías independientes
"Los usuarios solo se representan una vez en todos los directorios".

![MultiForestUsersOnce](./media/active-directory-aadconnect-topologies/MultiForestUsersOnce.png)

![MultiForestSeperateTopologies](./media/active-directory-aadconnect-topologies/MultiForestSeperateTopologies.png)

En este entorno, todos los bosques locales se tratan como entidades independientes y ningún usuario estará presente en ningún otro bosque. Cada bosque tiene su propia organización de Exchange y no hay ninguna GALSync entre los bosques. Esta podría ser la situación después de una fusión o adquisición, o en una organización donde cada unidad de negocio funciona de forma aislada de las demás. En Azure AD estos bosques estarán en la misma organización y aparecerán con una GAL unificada. En esta imagen, cada objeto en cada bosque se representará una vez en el metaverso y se agregará al directorio de Azure AD de destino.

### Varios bosques: coincidencia de usuarios
Común para todos los escenarios de varios bosques donde se selecciona una de las opciones de "Existen identidades de usuario entre varios directorios", es que pueden encontrarse en cada bosque grupos de distribución y seguridad y pueden contener una combinación de usuarios, contactos y FSP (entidades de seguridad externa).

Las FSP se usan en ADDS para representar a miembros de otros bosques en un grupo de seguridad. El motor de sincronización resolverá la FSP en el usuario real y representará el grupo de seguridad en Azure AD con todas las FSP resueltas en el objeto real.

### Varios bosques: malla completa con GALSync opcional
"Existen identidades de usuario entre varios directorios. Coincidencia mediante: atributo Mail"

![MultiForestUsersMail](./media/active-directory-aadconnect-topologies/MultiForestUsersMail.png)

![MultiForestFullMesh](./media/active-directory-aadconnect-topologies/MultiForestFullMesh.png)

Una topología de malla completa permite a los usuarios y los recursos ubicarse en cualquier bosque; normalmente habrá confianzas bidireccionales entre los bosques.

Si Exchange está presente en más de un bosque, podría haber opcionalmente una solución GALSync local que representa a un usuario en un bosque como un contacto en los demás bosques. GALSync se implementa normalmente mediante Forefront Identity Manager 2010 o Microsoft Identity Manager 2016. No se puede usar Azure AD Connect en GALSync local.

En este escenario, los objetos de identidad se combinan mediante el atributo mail. Por lo tanto, un usuario con un buzón en un bosque se combina con los contactos en los otros bosques.

### Varios bosques: bosque de cuenta-recurso
"Existen identidades de usuario entre varios directorios. Coincidencia mediante: atributos ObjectSID y msExchMasterAccountSID"

![MultiForestUsersObjectSID](./media/active-directory-aadconnect-topologies/MultiForestUsersObjectSID.png)

![MultiForestAccountResource](./media/active-directory-aadconnect-topologies/MultiForestAccountResource.png)

En una topología de bosque de cuenta-recurso, tiene uno o más bosques de cuenta con cuentas de usuario activas.

Este escenario incluye un bosque que confía en todos los bosques de cuenta. Normalmente, este bosque tiene un esquema de AD extendido con Exchange y Lync. Todos los servicios de Exchange y Lync, así como otros servicios compartidos, se encuentran en este bosque. Los usuarios tienen una cuenta de usuario deshabilitada en este bosque y el buzón está vinculado al bosque de cuenta.

## Consideraciones sobre la topología y Office 365
Algunas cargas de trabajo de Office 365 presenta ciertas restricciones para las topologías compatibles. Si planea usar cualquiera de ellas, consulte las páginas de topologías admitidas de cada carga de trabajo.

| Carga de trabajo | |
| --------- | --------- |
| Exchange Online |	Si hay más de una organización de Exchange local (es decir, Exchange se ha implementado en más de un bosque), debe usar Exchange 2013 SP1 o posterior. Se pueden encontrar detalles aquí: [Implementaciones híbridas con varios bosques de Active Directory](https://technet.microsoft.com/es-ES/library/jj873754.aspx) |
| Skype Empresarial | Cuando se usan varios bosques locales, solo se admite la topología de bosque cuenta-recurso. Se pueden encontrar detalles sobre las topologías admitidas aquí: [Requisitos del entorno para Skype Empresarial Server 2015](https://technet.microsoft.com/es-ES/library/dn933910.aspx). |

## Servidor provisional
![StagingServer](./media/active-directory-aadconnect-topologies/MultiForestStaging.png)

Azure AD Connect admite la instalación de un segundo servidor en "modo provisional". Un servidor en este modo solo leerá los datos de todos los directorios conectados y, por tanto, tendrá una copia actualizada de los datos de identidad. En caso de un desastre en el que el servidor principal deje de funcionar, es fácil conmutar manualmente por error al segundo servidor mediante el asistente de Azure AD Connect. Este segundo servidor se puede ubicar preferiblemente en un centro de datos diferente dado que no se comparte ninguna infraestructura con el servidor principal. Cualquier cambio de configuración realizado en el servidor principal debe copiarse en el segundo servidor. Y esto debe hacerlo el usuario.

Un servidor provisional puede usarse también si desea probar una nueva configuración personalizada y el efecto que tendrá en los datos. En este sentido, puede obtener una vista previa de los cambios y ajustar la configuración. Cuando esté satisfecho con la nueva configuración, puede hacer del servidor provisional el servidor activo y establecer el antiguo servidor activo en modo provisional.

También puede usar este método si desea reemplazar el servidor de sincronización y preparar el nuevo antes de apagar el servidor actualmente activo.

Es posible tener más de un servidor provisional si desea tener varias copias de seguridad en distintos centros de datos.

## Varios directorios de Azure AD
Microsoft recomienda tener un único directorio en Azure AD para una organización. Antes de considerar el uso de varios directorios de Azure AD, estos temas explican escenarios comunes que le permitirán usar un único directorio.

| Tema. | |
| --------- | --------- |
| Delegación mediante unidades administrativas | [Administración de unidades administrativas en Azure AD.](active-directory-administrative-units-management.md)

![MultiForestMultiDirectory](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectory.png)

Existe una relación 1:1 entre un servidor de Azure AD Connect Sync y un directorio de Azure AD. Para cada directorio de Azure AD, necesitará una instalación de servidor de Azure AD Connect Sync. Por diseño, las instancias de directorio de Azure AD están aisladas y los usuarios de uno no podrán ver a los usuarios del otro directorio. Si esta es la intención, esta es una configuración admitida; en caso contrario, debe usar los modelos de directorio único de Azure AD descritos anteriormente.

### Cada objeto se incluye una sola vez en un directorio de Azure AD
![SingleForestFiltered](./media/active-directory-aadconnect-topologies/SingleForestFiltered.png)

En esta topología, un servidor de AAD Connect Sync está conectado a cada directorio de Azure AD. Los servidores de sincronización de Azure AD Connect deben estar configurados con filtrado para que cada uno tenga un conjunto de objetos mutuamente excluyentes sobre el que operar, por ejemplo, creando un ámbito para cada servidor en una unidad organizativa o un dominio en concreto. Un dominio DNS solo se puede registrar en un único directorio de Azure AD, de modo que los UPN de los usuarios en el entorno local de AD deben usar también espacios de nombres independientes. Por ejemplo, en la imagen anterior tres sufijos UPN independientes se registran en el entorno local de AD: contoso.com, fabrikam.com y wingtiptoys.com. Los usuarios de cada dominio de Active Directory local usan otro espacio de nombres.

En esta topología no hay ninguna "GALsync" entre las instancias de directorio de Azure AD, de modo que la libreta de direcciones de Exchange Online y Skype Empresarial solo mostrarán a los usuarios del mismo directorio.

Con esta topología, solo uno de los directorios de Azure AD puede permitir la implementación híbrida de Exchange con Active Directory local.

El requisito del conjunto de objetos mutuamente excluyente también se aplica a la reescritura. Esto hace que algunas características de reescritura no se admitan con esta topología, ya que estas asumen una sola configuración local. Esto incluye:

-	Escritura diferida de grupos con la configuración predeterminada
-	Escritura diferida de dispositivos

### Cada objeto se incluye varias veces en un directorio de Azure AD
![SingleForestMultiDirectoryUnsupported](./media/active-directory-aadconnect-topologies/SingleForestMultiDirectoryUnsupported.png) ![SingleForestMultiConnectorsUnsupported](./media/active-directory-aadconnect-topologies/SingleForestMultiConnectorsUnsupported.png)

No se admite la sincronización del mismo usuario con varios directorios de Azure AD. Tampoco se admite realizar un cambio de configuración para que los usuarios de un directorio de Azure AD aparezcan como contactos en otro directorio de Azure AD. Asimismo, no se admite la modificación de Azure AD Connect Sync para conectarse a varios directorios de Azure AD.

### GALsync mediante reescritura
![MultiForestMultiDirectoryGALSync1Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync1Unsupported.png) ![MultiForestMultiDirectoryGALSync2Unsupported](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync2Unsupported.png)

Los directorios de Azure AD están aislados por diseño. No se admite el cambio de la configuración de Azure AD Connect Sync para leer datos de otro directorio de Azure AD en un intento por crear una GAL común y unificada entre los directorios. Tampoco se admite la exportación de usuarios como contactos a otro directorio AD local mediante Azure AD Connect Sync.

### GALsync con servidor de sincronización local
![MultiForestMultiDirectoryGALSync](./media/active-directory-aadconnect-topologies/MultiForestMultiDirectoryGALSync.png)

Se admite el uso de FIM2010/MIM2016 local para los usuarios de GALsync entre dos organizaciones de Exchange. Los usuarios de una organización se mostrarán como usuarios o contactos externos en la otra organización. Estos directorios de AD locales diferentes se pueden sincronizar luego con sus propios directorios de Azure AD.


## Pasos siguientes
Para obtener información sobre cómo instalar Azure AD Connect en estos escenarios, vea [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Obtenga más información sobre la configuración de [sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->