<properties
	pageTitle="Procedimientos recomendados de cambio de la configuración predeterminada de sincronización de Azure AD Connect | Microsoft Azure"
	description="Proporciona procedimientos recomendados para cambiar la configuración predeterminada de Azure AD Connect Sync."
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
	ms.author="markusvi;andkjell"/>


# Azure AD Connect Sync: procedimientos recomendados de cambio de la configuración predeterminada
El propósito de este tema es describir los cambios admitidos y no admitidos en Azure AD Connect Sync.

La configuración creada por Azure AD Connect funciona "como está" para la mayoría de entornos que sincronizan Active Directory local con Azure AD. Sin embargo, en algunos casos, es necesario aplicar algunos cambios a una configuración para satisfacer un requisito o una necesidad en particular.

## Cambios en la cuenta de servicio
Azure AD Connect Sync se está ejecutando con una cuenta de servicio creada por el asistente para la instalación. Esta cuenta de servicio contiene las claves de cifrado para la base de datos usada en la sincronización. Se crea con una contraseña de 127 caracteres, que se configura para que no caduque.

- **No se admite** cambiar o restablecer la contraseña de la cuenta de servicio. Si lo hace, se destruirán las claves de cifrado y el servicio no podrá tener acceso a la base de datos ni se podrá iniciar.

## Cambios realizados en el programador
Azure AD Connect Sync está configurado para sincronizar los datos de identidad cada tres horas. Durante la instalación se crea una tarea programada que se ejecuta con una cuenta de servicio con permisos para usar el servidor de sincronización.

- **No se admite** realizar cambios en la tarea programada. No se conoce la contraseña de la cuenta de servicio. Consulte [Cambios en la cuenta de servicio](#changes-to-the-service-account)
- **No se admite** realizar sincronizaciones con una frecuencia superior al valor predeterminado de tres horas.
	- Se admiten sincronizaciones puntuales cuando se prueba una nueva configuración. Pero no se deben ejecutar exportaciones a Azure AD con una frecuencia mayor.

## Cambios en las reglas de sincronización
El Asistente para la instalación proporciona una configuración que se supone que funciona en la mayoría de los casos. En caso de que tenga que realizar cambios en la configuración, debe seguir entonces estas reglas para disponer de una configuración admitida.

- Puede [cambiar los flujos de atributo](#change-attribute-flows) si los flujos de atributo directos predeterminados no son adecuados para su organización.
- Si no desea [pasar un atributo](#do-not-flow-an-attribute) ni quitar valores de atributo existentes en Azure AD, deberá crear una regla para ello.
- [Deshabilite una regla de sincronización no deseada](#disable-an-unwanted-sync-rule) en lugar de eliminarla. La regla eliminada se volverá a crear durante una actualización.
- Para [cambiar una regla lista para usar](#change-an-out-of-box-rule), debe realizar una copia de la regla original y deshabilitar la regla lista para usar. El Editor de reglas de sincronización le ayudará con este paso.
- Exporte las reglas de sincronización personalizadas mediante el editor de reglas de sincronización. De este modo, obtiene un script de PowerShell que puede utilizar fácilmente para volver a crearlas en un posible escenario de recuperación ante desastres.

>[AZURE.WARNING] Las reglas de sincronización listas para usar tienen una huella digital. Si realiza un cambio en estas reglas, la huella digital ya no coincidirá y puede que tenga problemas en el futuro cuando intente aplicar una nueva versión de Azure AD Connect. Realiza únicamente cambios cómo se describe en este artículo.

### Cambiar los flujos de atributo
En algunos casos, los flujos de atributo predeterminados no funcionan para una organización.

Debe seguir estas reglas:

- Cree una nueva regla de sincronización con sus flujos de atributo. Al asignarle una prioridad más alta (valor numérico inferior) a las reglas, se invalidarán los flujos de atributo listos para usar.
- No agregue flujos adicionales a una regla lista para usar. Estos cambios se perderán durante la actualización.

En Fabrikam hay un bosque donde se usa el alfabeto local en el nombre, el apellido y el nombre para mostrar dados. La representación de caracteres latinos de estos atributos se almacena en los atributos de extensión. Al generar la lista global de direcciones en Azure AD y Office 365, la organización quiere usar en su lugar las direcciones de esta lista.

Con una configuración predeterminada, un objeto del bosque local tiene el siguiente aspecto:

![Flujo de atributos 1](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp1.png)

Para crear una regla con otros flujos de atributo, haga lo siguiente:

- Inicie el **Editor de reglas de sincronización** en el menú Inicio.
- Con **Entrante** aún seleccionado a la izquierda, haga clic en el botón **Agregar nueva regla**.
- Asigne a la regla un nombre y una descripción. Seleccione Active Directory local y los tipos de objeto correspondientes. En **Tipo de vínculo**, seleccione **Unir**. Para establecer la precedencia, seleccione un número que no se use en otra regla. Las reglas listas para usar comienzan con 100, así que en este ejemplo se puede usar el valor 50. ![Flujo de atributos 2](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp2.png)
- Deje el ámbito vacío (es decir, se debe aplicar a todos los objetos de usuario del bosque).
- Deje las reglas de unión vacías (es decir, permita que la regla lista para usar controle las uniones).
- En Transformaciones, cree los siguientes flujos. ![Flujo de atributos 3](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp3.png)
- Haga clic en **Agregar** para guardar la regla.
- Vaya a **Synchronization Service Manager**. En **Conectores**, seleccione el conector donde agregamos la regla. Seleccione **Ejecutar** y **Sincronización completa**. Una sincronización completa volverá a calcular todos los objetos con las reglas actuales.

Este es el resultado final para el mismo objeto con esta regla personalizada:

![Flujo de atributos 4](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/attributeflowjp4.png)

### No pasar atributos
Existen dos maneras de pasar un atributo. El primero está disponible en el Asistente para la instalación y le permite [quitar atributos seleccionados](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering). Esta opción funciona si nunca ha sincronizado el atributo antes. Sin embargo si ha comenzado a sincronizar este atributo y después lo quita con esta característica, el motor de sincronización dejará de administrar el atributo y los valores existentes se quedarán en Azure AD.

Si quiere quitar el valor de un atributo y asegurarse de no pasarlo en el futuro, debe crear en su lugar una regla personalizada.

En Fabrikam no hemos dado cuenta de que algunos de los atributos sincronizamos con la nube no deberían estar allí. Queremos asegurarse de que estos atributos se quitan de Azure AD.

![Atributos de extensión](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/badextensionattribute.png)

- Cree una nueva regla de sincronización de entrada y rellene la descripción ![Descripciones](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruledescription.png)
- Cree flujos de atributos de tipo **Expression** y con el origen **AuthoritativeNull**. El literal **AuthoritativeNull** indica que el valor debe estar vacío en la máquina virtual incluso si una regla de sincronización de prioridad inferior intenta rellenar el valor. ![Atributos de extensión](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruletransformations.png)
- Guarde la regla de sincronización. Inicie el **Servicio de sincronización**, busque el conector y seleccione **Ejecutar** y **Sincronización completa**. Esta acción hará que se vuelvan a calcular todos los flujos de atributos.
- Compruebe que los cambios deseados están a punto de exportarse; para ello, busque en el espacio de conector. ![Eliminación por fases](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/deletetobeexported.png)

### Deshabilitar una regla de sincronización no deseada
No elimine una regla de sincronización lista para usar; se volverá crear durante la siguiente actualización.

En algunos casos, el asistente de instalación ha generado una configuración que no funciona en su topología. Por ejemplo, si tiene una topología de bosque de cuentas-recursos pero ha ampliado el esquema en el bosque de cuentas con el esquema de Exchange, entonces se crearán reglas para Exchange tanto para el bosque de cuentas como para el bosque de recursos. En este caso, tenemos que deshabilitar la regla de sincronización para Exchange.

![Regla de sincronización deshabilitada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

En la imagen anterior, el Asistente para la instalación ha encontrado un esquema antiguo de Exchange 2003 en el bosque de cuentas. Este se agregó antes de que el bosque de recursos se introdujera en el entorno de Fabrikam. Para asegurarse de que ningún atributo de la implementación antigua de Exchange se sincronice, la regla de sincronización se debe deshabilitar como se muestra.

### Cambiar una regla lista para usar
Si tiene que realizar cambios en una regla lista para usar, debe realizar una copia de ella y deshabilitar la regla original. A continuación, realice los cambios en la regla clonada. El Editor de reglas de sincronización le ayudará con este paso. Cuando se abre una regla lista para usar se presenta este cuadro de diálogo:

![Regla lista para usar de advertencia](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Seleccione **Sí** para crear una copia de la regla. A continuación, se abre la regla clonada.

![Regla clonada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

En esta regla clonada, realice los cambios necesarios en el ámbito, la combinación y las transformaciones.

## Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->