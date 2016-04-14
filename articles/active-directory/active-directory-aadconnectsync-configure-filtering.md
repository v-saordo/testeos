<properties
	pageTitle="Configuración de filtrado de sincronización de Azure AD Connect | Microsoft Azure"
	description="Explica cómo configurar el filtrado en Azure AD Connect Sync."
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
	ms.author="andkjell;markusvi"/>


# Azure AD Connect Sync: configuración del filtrado
Con el filtrado, puede controlar qué objetos deben aparecer en Azure AD desde el directorio local. La configuración predeterminada aceptará todos los objetos en todos los dominios de los bosques configurados. Por lo general, esta configuración es la recomendada. Por ejemplo, los usuarios finales con cargas de trabajo de Office 365, como Exchange Online y Skype Empresarial, se beneficiarán de una lista global de direcciones completa para poder enviar correo electrónico y llamar a todos los integrantes. Con la configuración predeterminada, obtendrían la misma experiencia que con una implementación local de Exchange o Lync.

En algunos casos, es necesario realizar algunos cambios en la configuración predeterminada. A continuación se muestran algunos ejemplos:

- Quiere usar la [topología de varios directorios de Azure AD](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-directory). Después, debe aplicar un filtro para controlar qué objeto se debe sincronizar en un directorio determinado de Azure AD.
- Va a ejecutar un piloto para Azure u Office 365 y desea solo un subconjunto de usuarios en Azure AD. En el pequeño proyecto piloto, no es importante tener una lista global de direcciones completa para demostrar la funcionalidad.
- Tiene muchas cuentas de servicio y otras de carácter no personal que no desea que estén presentes en Azure AD.
- Por motivos de cumplimiento, no elimina ninguna cuenta de usuario local, sino que solo las deshabilita. Pero en Azure AD solo quiere que haya cuentas activas presentes.

En este artículo, se trata cómo configurar los distintos métodos de filtrado.

> [AZURE.IMPORTANT]Microsoft no admite la modificación ni el funcionamiento de Azure AD Connect Sync que no se limite a estas acciones documentadas formalmente. Cualquiera de estas acciones puede dar lugar a un estado incoherente o no admitido de Azure AD Connect Sync y, como resultado, Microsoft no ofrece soporte técnico para las implementaciones de este tipo.

## Conceptos básicos y notas importantes
En Azure AD Connect Sync, puede habilitar el filtrado en cualquier momento. Si comienza con una configuración predeterminada de la sincronización de directorios y después configura el filtrado, los objetos que se filtren ya no se sincronizan con Azure AD. Esto provoca que los objetos en Azure AD que se sincronizaron previamente pero que se filtraron después se eliminen en Azure AD.

Antes de empezar a realizar cambios en el filtrado, asegúrese de [deshabilitar la tarea programada](#disable-scheduled-task) para no exportar accidentalmente cambios cuya corrección aún no se ha comprobado.

Como el filtrado puede quitar muchos objetos al mismo tiempo, querrá asegurarse de que los nuevos filtros sean correctos antes de exportar los cambios a Azure AD. Después de completar los pasos de configuración, se recomienda encarecidamente que siga los [pasos de comprobación](#apply-and-verify-changes) antes de exportar y realizar cambios en Azure AD.

Para impedir la eliminación de muchos objetos por accidente, la característica para [evitar eliminaciones accidentales](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) está activada de forma predeterminada. Si elimina muchos objetos debido al filtrado (de forma predeterminada, 500), debe seguir los pasos descritos en este artículo para permitir que las eliminaciones se reflejen en Azure AD.

Si usa una compilación anterior a noviembre de 2015 ([1\.0.9125](active-directory-aadconnect-version-history.md#1091250)), realiza un cambio en la configuración de filtro y usa la sincronización de contraseñas, deberá desencadenar una sincronización completa de todas las contraseñas después de haber completado la configuración. Para información sobre cómo desencadenar una sincronización completa de las contraseñas, consulte [Desencadenamiento de una sincronización completa de todas las contraseñas](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords). Si usa la compilación 1.0.9125 o una posterior, la acción de **sincronización completa** normal también calculará si se deben sincronizar las contraseñas, por lo que no se requiere este paso adicional.

Si los objetos de **usuario** se eliminaron involuntariamente en Azure AD por un error de filtrado, puede volver a crearlos en Azure AD quitando las configuraciones de filtrado y después sincronizar de nuevo los directorios. Así se restaurarán los usuarios de la papelera de reciclaje en Azure AD. Sin embargo, no se pueden recuperar otros tipos de objeto. Por ejemplo, si elimina accidentalmente un grupo de seguridad que se usó para incluir un recurso en una ACL, no se pueden recuperar ni el grupo ni sus ACL.

Azure AD Connect solo eliminará objetos que alguna vez haya considerado dentro del ámbito. Si hay objetos en Azure AD que se crearon con otro motor de sincronización y que no están dentro del ámbito, no se quitarán después de agregar el filtrado. Por ejemplo, si comienza con un servidor DirSync que creó una copia completa de todo el directorio en Azure AD e instala un nuevo servidor de Azure AD Connect Sync en paralelo con el filtrado habilitado desde el principio, no se quitarán los objetos adicionales creados por DirSync.

Se conservarán las configuraciones de filtrado cuando instale o actualice a una versión más reciente de Azure AD Connect. Es siempre una práctica recomendada comprobar que la configuración no se haya cambiado involuntariamente después de una actualización a una nueva versión antes de ejecutar el primer ciclo de sincronización.

Si tiene más de un bosque, las configuraciones de filtrado descritas en este tema deben aplicarse a cada bosque (siempre que quiera la misma configuración para todos).

### Deshabilitación de la tarea programada
Para deshabilitar la tarea programada que desencadenará un ciclo de sincronización cada 3 horas, siga estos pasos:

1. Abra el **Programador de tareas** en el menú Inicio.
2. Directamente bajo **Biblioteca del Programador de tareas**, busque la tarea llamada **Programador de Sincronización de Azure AD**, haga clic con el botón derecho en ella y seleccione **Deshabilitar**. ![Programador de tareas](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. Ahora puede realizar cambios de configuración y ejecutar el motor de sincronización manualmente desde la consola de **Synchronization Service Manager**.

Después de completar todos los cambios de filtrado, no olvide volver a seleccionar de nuevo **Habilitar** para la tarea.

## Opciones de filtrado
Los tipos de configuración de filtrado siguientes pueden aplicarse a la herramienta Sincronización de directorios:

- [**Basado en grupo**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups): el filtrado basado en un único grupo solo se puede configurar durante la instalación inicial con el Asistente para la instalación. No se profundiza más en este tema.

- [**Basado en dominios**](#domain-based-filtering): esta opción le permite seleccionar qué dominios se sincronizarán con Azure AD. También permite agregar y quitar dominios de la configuración del motor de sincronización si realiza cambios en la infraestructura local después de instalar Azure AD Connect Sync.

- [**Basado en unidades organizativas**](#organizational-unitbased-filtering): esta opción de filtrado permite seleccionar qué unidades organizativas se sincronizarán con Azure AD. Esta opción estará en todos los tipos de objeto de las unidades organizativas seleccionadas.

- [**Basado en atributos**](#attribute-based-filtering): esta opción permite filtrar objetos en función de los valores de sus atributos. También puede tener filtros diferentes para distintos tipos de objetos.

Puede usar varias opciones de filtrado al mismo tiempo. Por ejemplo, puede utilizar el filtrado basado en unidades organizativas para incluir solo los objetos de una unidad organizativa y, al mismo tiempo, el filtrado basado en atributos para filtrar aún más los objetos. Cuando se usan varios métodos de filtrado, los filtros utilizan un operador AND lógico entre los filtros.

## Filtrado basado en dominios
En esta sección se proporcionan los pasos necesarios para configurar el filtro de dominios. Si ha agregado o quitado dominios en el bosque después de instalar Azure AD Connect, también tendrá que actualizar la configuración del filtrado.

La mejor manera de cambiar el filtrado basado en dominio es mediante la ejecución del Asistente para instalación y el cambio del [filtrado por dominios y unidades organizativas](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). El Asistente para instalación está automatizando todas las tareas que se documentan en este tema.

Solo debe seguir estos pasos si por alguna razón no se puede ejecutar el Asistente para instalación.

La configuración del filtrado basado en dominios consta de estos pasos:

- [Seleccione los dominios](#select-domains-to-be-synchronized) que deben incluirse en la sincronización.
- Para cada dominio agregado o quitado, ajuste los [perfiles de ejecución](#update-run-profiles).
- [Aplicación y comprobación de los cambios](#apply-and-verify-changes).

### Selección de los dominios que se sincronizarán
**Para configurar el filtro de dominio, realice los pasos siguientes:**

1. Inicie sesión en el servidor donde se ejecuta Azure AD Connect Sync con una cuenta que forme parte del grupo de seguridad **ADSyncAdmins**.
2. Inicie el **Servicio de sincronización** desde el menú Inicio.
3. Seleccione **Conectores** y, en la lista **Conectores**, elija el conector de tipo **Servicios de dominio de Active Directory**. En **Acciones**, seleccione **Propiedades**. ![Propiedades del conector](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Haga clic en **Configurar particiones de directorio**.
5. En la lista **Seleccionar particiones de directorio**, seleccione o anule la selección de los dominios según sea necesario. Compruebe que solo estén seleccionadas las particiones que desee sincronizar. ![Particiones](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png) Si ha cambiado la infraestructura de AD local y ha agregado o quitado dominios en el bosque, haga clic en el botón **Actualizar** para obtener una lista actualizada. Al actualizar, se le pedirán las credenciales; proporcione credenciales con acceso de lectura al entorno de Active Directory local. No tiene por qué ser el usuario que esté rellenado en el cuadro de diálogo. ![Actualización necesaria](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. Cuando termine, haga clic en el botón **Aceptar** para cerrar el cuadro de diálogo **Propiedades**. Si quitó dominios del bosque, aparecerá un mensaje para indicar que se eliminó un dominio y se borrará esa configuración.
7. Pase a ajustar los [perfiles de ejecución](#update-run-profiles).

### Actualización de los perfiles de ejecución
Si ha actualizado el filtro de dominio, también debe actualizar los perfiles de ejecución.

1. En la lista **Conectores**, asegúrese de que está seleccionado el conector que cambió en el paso anterior. En **Acciones**, seleccione **Configurar perfiles de ejecución**. ![Perfiles de ejecución de conector](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  

Debe ajustar los siguientes perfiles:

- Importación completa
- Sincronización completa
- Importación delta
- Sincronización delta
- Exportación

Para cada uno de los cinco perfiles, siga estos pasos para cada dominio **agregado**:

1. Seleccione el perfil de ejecución y haga clic en **Nuevo paso**.
2. En la página **Configurar paso**, en la lista desplegable **Tipo**, seleccione el tipo de paso que comparta nombre con el perfil que esté configurando. Después, haga clic en **Siguiente**. ![Perfiles de ejecución de conector](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
3. En la página **Configuración de conector**, en la lista desplegable **Partición**, seleccione el nombre del dominio que ha agregado al filtro de dominio. ![Perfiles de ejecución de conector](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
4. Para cerrar el cuadro de diálogo **Configurar perfil de ejecución**, haga clic en **Finalizar**.

Para cada uno de los cinco perfiles, siga estos pasos para cada dominio **quitado**:

1. Seleccione el perfil de ejecución.
2. Si el campo **Valor** del atributo **Partición** es un GUID, seleccione el paso de ejecución y haga clic en **Eliminar paso**. ![Perfiles de ejecución de conector](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  

Como resultado final, cada dominio que desee sincronizar debe aparecer como paso en cada perfil de ejecución.

Para cerrar el cuadro de diálogo **Configurar perfiles de ejecución**, haga clic en **Aceptar**.

- Para completar la configuración, [aplique y compruebe los cambios](#apply-and-verify-changes).

## Filtrado basado en unidades organizativas
La mejor manera de cambiar el filtrado basado en unidades organizativas es mediante la ejecución del Asistente para instalación y el cambio del [filtrado por dominios y unidades organizativas](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). El Asistente para instalación está automatizando todas las tareas que se documentan en este tema.

Solo debe seguir estos pasos si por alguna razón no se puede ejecutar el Asistente para instalación.

**Para configurar el filtrado basado en unidad organizativa, realice los pasos siguientes:**

1. Inicie sesión en el servidor donde se ejecuta Azure AD Connect Sync con una cuenta que forme parte del grupo de seguridad **ADSyncAdmins**.
2. Inicie el **Servicio de sincronización** desde el menú Inicio.
3. Seleccione **Conectores** y, en la lista **Conectores**, elija el conector de tipo **Servicios de dominio de Active Directory**. En **Acciones**, seleccione **Propiedades**. ![Propiedades del conector](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Haga clic en **Configurar particiones de directorio**, seleccione el dominio que desee configurar y,luego, haga clic en **Contenedores**.
5. Cuando se le solicite, proporcione las credenciales con acceso de lectura al entorno de Active Directory local. No tiene por qué ser el usuario que esté rellenado en el cuadro de diálogo.
6. En el cuadro de diálogo **Seleccionar contenedores**, borre las unidades organizativas que no desee sincronizar con el directorio en la nube y luego haga clic en **Aceptar**. ![Unidad organizativa](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
  - El contenedor **Computers** debe estar seleccionado para que los equipos con Windows 10 se sincronicen correctamente con Azure AD. Si los equipos unidos a un dominio se encuentran en otras unidades organizativas, asegúrese de que estén seleccionadas.
  - Se debe seleccionar el contenedor **ForeignSecurityPrincipals** si tiene varios bosques con confianzas. Esto permitirá que se resuelva la pertenencia a grupos de seguridad de otros bosques.
  - La unidad organizativa **RegisteredDevices** debe estar seleccionada si ha habilitado la característica de reescritura de dispositivos. Si usa otra característica de reescritura, como la de grupos, asegúrese de que estas ubicaciones estén seleccionadas.
  - Seleccione cualquier otra unidad organizativa donde se encuentren usuarios, objetos InetOrgPerson, grupos, contactos y equipos. En la imagen anterior, se encuentran en la unidad organizativa ManagedObjects.
7. Cuando termine, haga clic en el botón **Aceptar** para cerrar el cuadro de diálogo **Propiedades**.
8. Para completar la configuración, [aplique y compruebe los cambios](#apply-and-verify-changes).

## Filtrado basado en atributos
Asegúrese de usar la compilación de noviembre de 2015 ([1\.0.9125](active-directory-aadconnect-version-history.md#1091250)) o una posterior para que estos pasos funcionen.

El filtrado basado en atributos es la manera más flexible de filtrar objetos. Puede usar la potencia del [aprovisionamiento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) para controlar prácticamente todos los aspectos de cuándo se debe sincronizar un objeto con Azure AD.

El filtrado se puede aplicar tanto en la [entrada](#inbound-filtering) desde Active Directory hacia el metaverso como en la [salida](#outbound-filtering) desde el metaverso hacia Azure AD. Se recomienda aplicar el filtrado entrante, ya que es el más fácil de mantener. El filtrado saliente solo debe usarse si es necesario para unir objetos de más de un bosque antes de poder realizar la evaluación.

### Filtrado entrante
El filtrado entrante aprovecha la configuración predeterminada en la que los objetos que se dirigen a AAD no tienen el atributo de metaverso cloudFiltered establecido en ningún valor que se vaya a sincronizar. Si el valor de este atributo está establecido en **True**, el objeto no se sincronizará. No debería estar establecido en **False** por diseño. Para asegurarse de que otras reglas tengan la capacidad de aportar un valor, este atributo solo debería tener los valores **True** o **NULL** (ausente).

En el filtrado entrante, se utilizará la potencia del **ámbito** para determinar qué objetos se deben sincronizar y cuáles no. Aquí realizará ajustes para satisfacer los requisitos de su organización. El módulo de ámbito cuenta con **grupos** y **cláusulas** para determinar si una regla de sincronización debe estar dentro del ámbito. Un **grupo** contendrá una o varias **cláusulas**. Existe un operador AND lógico entre varias cláusulas y un operador OR lógico entre varios grupos.

Veamos un ejemplo: ![Scope](./media/active-directory-aadconnectsync-configure-filtering/scope.png) se debe leer como **(departamento = TI) O (departamento = Ventas Y c = Estados Unidos)**.

En los ejemplos y los pasos siguientes, usaremos el objeto de usuario como ejemplo, pero puede usarlo para todo tipo de objetos.

En los ejemplos siguientes, los valores de precedencia usados comienzan por 500, lo que garantiza que se evalúen después de las reglas integradas (a menor precedencia, mayor valor numérico).

#### Filtrado negativo ("no sincronizar estos")
En el siguiente ejemplo, filtraremos (no sincronizaremos) todos los usuarios en los que **extensionAttribute15** tenga el valor **NoSync**.

1. Inicie sesión en el servidor donde se ejecuta Azure AD Connect Sync con una cuenta que forme parte del grupo de seguridad **ADSyncAdmins**.
2. Abra el **Editor de reglas de sincronización** en el menú Inicio.
3. Asegúrese de que **Entrante** esté seleccionado y haga clic en **Agregar nueva regla**.
4. Asigne a la regla un nombre descriptivo, como "*Entrante desde AD – Usuario FiltroNoSincronizar*". Seleccione el bosque correcto, **Usuario** para **Tipo de objeto de sistema conectado** y **Persona** para **Tipo de objeto de metaverso**. Como **Tipo de vínculo**, seleccione **Unirse** y, en el tipo de precedencia, un valor que no esté usando ninguna otra regla de sincronización (por ejemplo: 500) y después haga clic en **Siguiente**. ![Descripción de entrante 1](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. En **Filtro de ámbito**, haga clic en **Agregar grupo**, luego en **Agregar cláusula** y, en Atributo, seleccione **ExtensionAttribute15**. Asegúrese de que el operador esté configurado en **EQUAL** y escriba el valor **NoSincronizar** en el cuadro Valor. Haga clic en **Siguiente**. ![Ámbito de entrante 2](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. Deje las reglas de **unión** vacías y, a continuación, haga clic en **Siguiente**.
7. Haga clic en **Agregar transformación**, seleccione **Constante** en **Tipo de flujo**, seleccione el atributo de destino **cloudFiltered** y, en el cuadro de texto Origen, escriba **True**. Haga clic en **Agregar** para guardar la regla. ![Transformación de entrante 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. Para completar la configuración, [aplique y compruebe los cambios](#apply-and-verify-changes).

#### Filtrado positivo ("solo sincronizar estos")
Expresar el filtrado positivo puede ser más complicado, ya que se deben considerar también objetos que no es obvio que se vayan a sincronizar, como las salas de conferencias.

La opción de filtrado positivo requiere dos reglas de sincronización. Una (o varias) con el ámbito correcto de los objetos que se van a sincronizar y una segunda de comodín que filtrará todos los objetos que aún no se hayan identificado como objetos que se deban sincronizar.

En el ejemplo siguiente, solo sincronizaremos los objetos de usuario en los que el atributo departamento tenga el valor **Ventas**.

1. Inicie sesión en el servidor donde se ejecuta Azure AD Connect Sync con una cuenta que forme parte del grupo de seguridad **ADSyncAdmins**.
2. Abra el **Editor de reglas de sincronización** en el menú Inicio.
3. Asegúrese de que **Entrante** esté seleccionado y haga clic en **Agregar nueva regla**.
4. Asigne a la regla un nombre descriptivo, como "*Entrante desde AD – Usuario Ventas sincronizar*". Seleccione el bosque correcto, **Usuario** para **Tipo de objeto de sistema conectado** y **Persona** para **Tipo de objeto de metaverso**. Como **Tipo de vínculo**, seleccione **Unirse** y, en el tipo de precedencia, un valor que no esté usando ninguna otra regla de sincronización (por ejemplo: 501) y después haga clic en **Siguiente**. ![Descripción de entrante 4](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. En **Filtro de ámbito**, haga clic en **Agregar grupo**, luego en **Agregar cláusula** y, en Atributo, seleccione **departamento**. Asegúrese de que el operador esté configurado en **EQUAL** y escriba el valor **Ventas** en el cuadro Valor. Haga clic en **Siguiente**. ![Ámbito de entrante 5](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. Deje las reglas de **unión** vacías y, a continuación, haga clic en **Siguiente**.
7. Haga clic en **Agregar transformación**, seleccione **Constante** en **Tipo de flujo**, seleccione el atributo de destino **cloudFiltered** y, en el cuadro de texto Origen, escriba **False**. Haga clic en **Agregar** para guardar la regla. ![Transformación de entrante 6](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png) Esto es un caso especial en el que estableceremos cloudFiltered explícitamente en False.

	Ahora tenemos que crear la regla de sincronización de comodín.

8. Asigne a la regla un nombre descriptivo, como "*Entrante desde AD – Usuario Filtro de comodín*". Seleccione el bosque correcto, **Usuario** para **Tipo de objeto de sistema conectado** y **Persona** para **Tipo de objeto de metaverso**. Como **Tipo de vínculo**, seleccione **Unirse** y, en tipo de precedencia, un valor que no esté usando ninguna otra regla de sincronización (por ejemplo, 600). Hemos seleccionado un valor de precedencia más alto (precedencia inferior) que la anterior regla de sincronización, pero además hemos dejado espacio para agregar más reglas de filtrado de sincronización más adelante cuando queramos empezar a sincronizar otros departamentos. Haga clic en **Siguiente**. ![Descripción de entrante 7](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)
9. Deje **Filtro de ámbito** vacío y haga clic en **Siguiente**. Un filtro vacío indica que se debe aplicar la regla a todos los objetos.
10. Deje las reglas de **unión** vacías y, a continuación, haga clic en **Siguiente**.
11. Haga clic en **Agregar transformación**, seleccione **Constante** en **Tipo de flujo**, seleccione el atributo de destino **cloudFiltered** y, en el cuadro de texto Origen, escriba **True**. Haga clic en **Agregar** para guardar la regla. ![Transformación de entrante 3](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. Para completar la configuración, [aplique y compruebe los cambios](#apply-and-verify-changes).

Si es necesario, podemos crear más reglas del primer tipo en las que se incluyan más objetos en la sincronización.

### Filtrado saliente
En algunos casos es necesario realizar el filtrado solo después de que los objetos se hayan unido en metaverso. Por ejemplo, podría ser necesario observar el atributo mail del bosque de recursos y el atributo userPrincipalName del bosque de cuentas para determinar si debe sincronizarse un objeto. En estos casos, crearemos el filtrado en la regla de salida.

En este ejemplo, cambiaremos el filtrado de manera que se sincronicen solo los usuarios en los que mail y userPrincipalName terminen en @contoso.com:

1. Inicie sesión en el servidor donde se ejecuta Azure AD Connect Sync con una cuenta que forme parte del grupo de seguridad **ADSyncAdmins**.
2. Abra el **Editor de reglas de sincronización** en el menú Inicio.
3. En Tipo de reglas, haga clic en **Saliente**.
4. Busque la regla con el nombre **Saliente hacia AAD – Usuario Unión SOAInAD**. Haga clic en **Editar**.
5. En el cuadro emergente, responda **Sí** para crear una copia de la regla.
6. En la página **Descripción**, cambie la precedencia a un valor no usado, como 50.
7. Haga clic en **Filtro de ámbito** en el panel de navegación izquierdo. Haga clic en **Agregar cláusula** y, en Atributo, seleccione **mail**; en Operador, seleccione **ENDSWITH** y en Valor, escriba **@contoso.com**. Haga clic en **Agregar cláusula** y, en Atributo, seleccione **userPrincipalName**; en Operador, seleccione **ENDSWITH** y en Valor, escriba **@contoso.com**.
8. Haga clic en **Guardar**.
9. Para completar la configuración, [aplique y compruebe los cambios](#apply-and-verify-changes).

## Aplicación y comprobación de los cambios
Una vez realizados los cambios de configuración, se deben aplicar a los objetos que ya están presentes en el sistema. También es posible que se deban procesar objetos que no estén en el motor de sincronización y que debamos volver a leer el sistema de origen para comprobar su contenido.

Si cambió la configuración mediante el filtrado basado en **dominios** o en **unidades organizativas**, deberá realizar una **importación completa** seguida de una **sincronización diferencial**.

Si cambió la configuración mediante el filtrado de **atributos**, deberá llevar a cabo una **sincronización completa**.

Siga estos pasos.

1. Inicie el **Servicio de sincronización** desde el menú Inicio.
2. Seleccione **Conectores** y, en la lista **Conectores**, elija el conector en el que realizó un cambio de configuración antes. En **Acciones**, seleccione **Ejecutar**. ![Ejecución de conector](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. En **Perfiles de ejecución**, seleccione la operación que se menciona en la sección anterior. Si necesita ejecutar dos acciones, ejecute la segunda una vez completada la primera (la columna **Estado** muestra **Inactivo** para el conector seleccionado).

Después de la sincronización, todos los cambios se almacenan provisionalmente para exportarlos. Antes de realizar los cambios en Azure AD, queremos comprobar que todos sean correctos.

1. Inicie un símbolo del sistema y vaya a `%Program Files%\Microsoft Azure AD Sync\bin`.
2. Ejecute: `csexport "Name of Connector" %temp%\export.xml /f:x` El nombre del conector puede encontrarse en Servicio de sincronización. Tendrá un nombre similar a “contoso.com – AAD” para Azure AD.
3. Ejecute: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Ahora tiene un archivo en %temp% denominado export.csv que se puede examinar en Microsoft Excel. Este archivo contiene todos los cambios que se van a exportar.
5. Realice los cambios necesarios en los datos o la configuración y repita estos pasos (importación, sincronización y comprobación) hasta que se esperen los cambios que se van a exportar en breve.

Cuando esté satisfecho, exporte los cambios a Azure AD.

1. Seleccione **Conectores** y, en la lista **Conectores**, seleccione el conector de Azure AD. En **Acciones**, seleccione **Ejecutar**.
2. En **Perfiles de ejecución**, seleccione **Exportar**.
3. Si los cambios de configuración van a eliminar muchos objetos, verá un error al exportar si el número es mayor que el umbral configurado (de forma predeterminada, 500). Si aparece, deberá deshabilitar temporalmente la característica para [evitar eliminaciones accidentales](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md).

Ahora es el momento de volver a habilitar el programador.

1. Abra el **Programador de tareas** en el menú Inicio.
2. Directamente en **Biblioteca del Programador de tareas**, busque la tarea llamada **Programador de Sincronización de Azure AD**, haga clic con el botón derecho en ella y seleccione **Habilitar**.

## Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->