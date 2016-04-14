<properties
   pageTitle="Sincronización de Azure AD Connect: Programador | Microsoft Azure"
   description="En este tema se describe la característica de Programador incorporada en la sincronización de Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/26/2016"
   ms.author="andkjell"/>

# Sincronización de Azure AD Connect: Programador
En este tema se describe el programador incorporado en la sincronización de Azure AD Connect (también denominado motor de sincronización).

Esta característica se introdujo con la compilación 1.1.105.0 (publicada en febrero de 2016).

## Información general
La sincronización de Azure AD Connect sincronizará los cambios que ocurren en su directorio local mediante un programador. Hay dos procesos de programador, uno para la sincronización de contraseñas y otro para la sincronización de objeto o atributo y tareas de mantenimiento. Este tema se tratará el último.

En versiones anteriores el programador para objetos y atributos era externo al motor de sincronización y se utilizaba el programador de tareas de Windows o un servicio independiente de Windows para desencadenar el proceso de sincronización. En las versiones 1.1 el programador viene integrado con el motor de sincronización y permite alguna personalización. La nueva frecuencia de sincronización predeterminada es de 30 minutos.

El programador es responsable de dos tareas:

- **Ciclo de sincronización**. El proceso para importar, sincronizar y exportar los cambios.
- **Tareas de mantenimiento**. Renueve las claves y certificados para el restablecimiento de contraseña y el servicio de registro de dispositivos (DRS). Purgue las entradas antiguas en el registro de operaciones.

El programador en sí, siempre está en ejecución, pero se puede configurar para que ejecute solo una o ninguna de estas tareas. Por ejemplo si necesita tener su propio proceso de ciclo de sincronización, puede deshabilitar esta tarea en el programador pero continuar ejecutando la tarea de mantenimiento.

## Configuración del programador
Para ver la configuración actual, vaya a PowerShell y ejecute `Get-ADSyncScheduler`. Se mostrará algo como esto:

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings.png)

- **AllowedSyncCycleInterval**. En la mayoría de los casos Azure AD permitirá que se produzcan las sincronizaciones. No se puede sincronizar con más frecuencia que esto y mantener la compatibilidad.
- **CurrentlyEffectiveSyncCycleInterval**. La programación en vigor actualmente. Tendrá el mismo valor que CustomizedSyncInterval (si está establecido) si no es más frecuente que AllowedSyncInterval. Si cambia CustomizedSyncCycleInterval, esto surtirá efecto tras el próximo ciclo de sincronización.
- **CustomizedSyncCycleInterval**. Si desea que el programador se ejecute con cualquier otra frecuencia distinta a la del valor predeterminado de 30 minutos, configure este valor. En la imagen anterior, el programador se ha establecido para que se ejecute cada hora. Si se establece en un valor inferior al de AllowedSyncInterval, se utilizará el último.
- **NextSyncCyclePolicyType**. Diferencial o inicial. Define si la siguiente ejecución debe procesar solo cambios diferenciales, o si esta debería hacer una importación y sincronización completas que a su vez volvería a procesar las reglas nuevas o modificadas.
- **NextSyncCycleStartTimeInUTC**. La hora en que el programador iniciará el siguiente ciclo de sincronización.
- **PurgeRunHistoryInterval**. El tiempo que deben mantenerse los registros de operación. Estos se pueden revisar en el Synchronization Service Manager. El valor predeterminado es de 7 días.
- **SyncCycleEnabled**. Indica si el programador está ejecutando los procesos de importación, sincronización y exportación como parte de su funcionamiento.
- **MaintenanceEnabled**. Muestra si está habilitado el proceso de mantenimiento. Actualizará los certificados o claves y purgará el registro de operaciones.
- **IsStagingModeEnabled**. Muestra si el [modo provisional](active-directory-aadconnectsync-operations.md#staging-mode) está habilitado.

Puede modificar estos ajustes mediante `Set-ADSyncScheduler`. El parámetro IsStagingModeEnabled solo puede establecerse mediante el Asistente para instalación.

La configuración del programador se almacena en Azure AD. Si tiene un servidor de almacenamiento provisional, cualquier cambio realizado en el servidor principal también afectará este servidor (a excepción de IsStagingModeEnabled).

## Inicio del programador
De forma predeterminada, el programador se ejecutará cada 30 minutos. En algunos casos puede querer ejecutar un ciclo de sincronización entre los ciclos programados o debe ejecutar un tipo diferente.

**Ciclo de sincronización diferencial** Un ciclo de sincronización diferencial incluye los siguientes pasos:

- Importación diferencial en todos los conectores
- Sincronización diferencial en todos los conectores
- Exportación en todos los conectores

Es posible que haya un cambio urgente que debe sincronizar inmediatamente para lo cual necesita ejecutar manualmente un ciclo. Si necesita ejecutar manualmente un ciclo, ejecute `Start-ADSyncSyncCycle -PolicyType Delta` desde PowerShell.

**Ciclo de sincronización completo** Si ha realizado uno de los siguientes cambios de configuración, debe ejecutar un ciclo de sincronización completa (también conocido como sincronización inicial):

- Agregó más objetos o atributos para su importación desde un directorio de origen
- Realizó cambios en las reglas de sincronización
- Cambió el [filtrado](active-directory-aadconnectsync-configure-filtering.md) para que se incluya un número diferente de objetos

Si ha realizado uno de estos cambios, debe ejecutar un ciclo de sincronización completo, para que el motor de sincronización tenga la oportunidad de volver a consolidar los espacios de conector. Un ciclo de sincronización completo incluye los pasos siguientes:

- Importación completa en todos los conectores
- Sincronización completa en todos los conectores
- Exportación en todos los conectores

Para iniciar un ciclo de sincronización completo, ejecute `Start-ADSyncSyncCycle -PolicyType Initial` desde un símbolo del sistema de PowerShell. Esto iniciará un ciclo de sincronización completo.

## Detención del programador
Si el programador está ejecutando actualmente un ciclo de sincronización puede que necesite detenerlo. Por ejemplo, si inicia el Asistente para instalación y recibe este error:

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Cuando se está ejecutando un ciclo de sincronización, no puede realizar cambios de configuración. Debe esperar hasta que el programador haya terminado el proceso, pero también es posible detenerlo para poder realizar los cambios inmediatamente. Detener el ciclo actual no es perjudicial y los cambios que aún no se hayan procesado se procesarán en la próxima ejecución.

1. Para empezar, indique al programador que detenga el ciclo actual con el cmdlet de PowerShell `Stop-ADSyncSyncCycle`.
2. La detención del programador no hará que el conector en cuestión detenga su tarea actual. Para forzar al conector para que se detenga, tome las medidas siguientes: ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
    - Inicie el **Servicio de sincronización** desde el menú Inicio. Vaya a **Conectores**, seleccione el conector con el estado **En ejecución** y seleccione **Detener** en la lista de acciones.

El programador permanecerá todavía activo y se iniciará de nuevo a la siguiente oportunidad.

## Programador y Asistente para instalación
Si inicia el Asistente para instalación el programador se suspenderá temporalmente. Esto es debido a que se supone que realizará los cambios de configuración y estos no se pueden aplicar si el motor de sincronización se está ejecutando activamente. Por este motivo, no deje el Asistente para instalación abierto ya que impedirá al motor de sincronización realizar ninguna acción.

## Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0302_2016-->