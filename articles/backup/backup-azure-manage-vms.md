
<properties
	pageTitle="Administración y supervisión de copias de seguridad de máquinas virtuales de Azure | Microsoft Azure"
	description="Aprenda a administrar y supervisar las copias de seguridad de máquinas virtuales de Azure"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="trinadhk; jimpark; markgal;"/>

# Administración y supervisión de copias de seguridad de máquinas virtuales de Azure

## Administrar máquinas virtuales protegidas

Para administrar máquinas virtuales protegidas:

1. Para ver y administrar la configuración de copia de seguridad para una máquina virtual, haga clic en la pestaña **Elementos protegidos**.

2. Haga clic en el nombre de un elemento protegido para ver la pestaña **Detalles de copia de seguridad**, que muestra información acerca de la última copia de seguridad.

    ![Copia de seguridad de máquina virtual](./media/backup-azure-manage-vms/backup-vmdetails.png)

3. Para ver y administrar la configuración de directiva de copia de seguridad para una máquina virtual, haga clic en la pestaña **Directivas**.

    ![Directiva de máquina virtual](./media/backup-azure-manage-vms/manage-policy-settings.png)

    La pestaña **Directivas de copia de seguridad** muestra la directiva existente. Se puede modificar según sea necesario. Si necesita crear una nueva directiva, haga clic en **Crear** en la página **Directivas**. Tenga en cuenta que si desea quitar una directiva no debería tener ninguna máquina virtual asociada a esta.

    ![Directiva de máquina virtual](./media/backup-azure-manage-vms/backup-vmpolicy.png)

4. Puede obtener más información sobre las acciones o el estado de una máquina virtual en la página **Trabajos**. Haga clic en un trabajo en la lista para obtener más detalles, o filtre los trabajos de una máquina virtual específica.

    ![Trabajos](./media/backup-azure-manage-vms/backup-job.png)

## Copia de seguridad a petición de una máquina virtual
Puede realizar una copia de seguridad a petición de una máquina virtual una vez que esta esté configurada para la protección. Si está pendiente la copia de seguridad inicial para la máquina virtual, la copia de seguridad a petición creará una copia completa de la máquina virtual en el almacén de copia de seguridad de Azure. Si se ha realizado la primera copia de seguridad, la copia de seguridad a petición solo enviará al almacén de copia de seguridad de Azure los cambios que se hayan realizado después de la copia de seguridad anterior.

Para realizar una copia de seguridad a petición de una máquina virtual:

1. Vaya a la página **Elementos protegidos** y elija **Máquina virtual de Azure** como valor del campo **Tipo** (si no está ya seleccionado) y haga clic en el botón **Seleccionar**.

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/vm-type.png)

2. Seleccione la máquina virtual de la que desea realizar copia de seguridad a petición. A continuación, haga clic en el botón **Copia de seguridad ahora**, situado en la parte inferior de la página.

    ![Copia de seguridad ahora](./media/backup-azure-manage-vms/backup-now.png)

    Esto creará un trabajo de copia de seguridad en la máquina virtual seleccionada. El periodo de retención del punto de recuperación creado con este trabajo será el mismo que el especificado en la directiva asociada a la máquina virtual.

    ![Creación de trabajo de copia de seguridad](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE] Para ver la directiva asociada a una máquina virtual, acceda a la máquina virtual en la página **Elementos protegidos** y vaya a la pestaña de la directiva de la copia de seguridad.

3. Una vez que se crea el trabajo, puede hacer clic en el botón **Ver trabajo** de la barra de notificación del sistema para ver el trabajo correspondiente en la página de los trabajos.

    ![Trabajo de copia de seguridad creado](./media/backup-azure-manage-vms/created-job.png)

4. Tras finalizar correctamente el trabajo, se creará un punto de recuperación que puede utilizar para restaurar la máquina virtual. Esto también incrementará el valor de la columna del punto de recuperación en 1 unidad en la página **Elementos protegidos**.

## Detener la protección de máquinas virtuales
Puede detener las futuras copias de seguridad de una máquina virtual con las siguientes opciones:

- Conservar los datos de copia de seguridad asociados a la máquina virtual en el almacén de copia de seguridad de Azure
- Eliminación de datos de copia de seguridad asociados a la máquina virtual

Si ha seleccionado la opción de conservar los datos de copia de seguridad asociados a la máquina virtual, puede usar estos datos para restaurar la máquina virtual. Para obtener más detalles sobre el precio de estas máquinas virtuales, haga clic [aquí](https://azure.microsoft.com/pricing/details/backup/).

Para detener la protección de una máquina virtual:

1. Vaya a la página **Elementos protegidos** y elija **Máquina virtual de Azure** como tipo de filtro (si no está ya seleccionado) y haga clic en el botón **Seleccionar**.

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/vm-type.png)

2. Elija la máquina virtual y haga clic en **Detener protección**, en la parte inferior de la página.

    ![Detener protección](./media/backup-azure-manage-vms/stop-protection.png)

3. De forma predeterminada, la copia de seguridad de Azure no elimina los datos de copia de seguridad asociados a la máquina virtual.

    ![Confirmación de la detención de la protección](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    Si desea eliminar los datos de copia de seguridad, active la casilla de verificación.

    ![Casilla de verificación](./media/backup-azure-manage-vms/checkbox.png)

    Especifique un motivo para detener la copia de seguridad. Aunque esto es opcional, proporcionar un motivo ayudará a Copia de seguridad de Azure a trabajar en los comentarios y dar prioridad a los escenarios del cliente.

4. Haga clic en el botón **Enviar** para enviar el trabajo **Detener protección**. Haga clic en **Ver trabajo** para ver el trabajo correspondiente en la página **Trabajos**.

    ![Detener protección](./media/backup-azure-manage-vms/stop-protect-success.png)

    Si no ha elegido la opción **Eliminar datos de copia de seguridad asociados** en el asistente **Detener protección**, publique los cambios del estado de protección y la finalización del trabajo en **Protección detenida**. Los datos permanecen en Copia de seguridad de Azure hasta que se eliminen explícitamente. Para eliminar los datos, seleccione la máquina virtual en la página **Elementos protegidos** y haga clic en **Eliminar**.

    ![Protección detenida](./media/backup-azure-manage-vms/protection-stopped-status.png)

    Si ha elegido la opción **Eliminar datos de copia de seguridad asociados**, la máquina virtual no formará parte de la página **Elementos protegidos**.

## Volver a proteger la máquina virtual
Si no ha elegido la opción **Eliminar datos de copia de seguridad asociados** en **Detener protección**, puede volver a proteger la máquina virtual siguiendo unos pasos similares a los de la copia de seguridad de las máquinas virtuales registradas. Una vez protegida, esta máquina virtual conservará los datos de copia de seguridad antes de detener la protección, así como los puntos de recuperación creados después de aplicar de nuevo la protección.

Después de aplicar de nuevo la protección, el estado de protección de la máquina virtual cambiará a **Protegida** si hay puntos de recuperación anteriores a la **detención de la protección**.

  ![Máquina virtual protegida de nuevo](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE] Al volver a proteger la máquina virtual, puede elegir una directiva diferente de la directiva con la que estaba protegida inicialmente la máquina virtual.

## Anular registro de máquinas virtuales

Si desea quitar la máquina virtual del almacén de copia de seguridad:

1. Haga clic en el botón **ANULAR REGISTRO** en la parte inferior de la página.

    ![Deshabilitar protección](./media/backup-azure-manage-vms/unregister-button.png)

    Aparecerá una notificación en la parte inferior de la pantalla de confirmación de solicitud. Haga clic en **Sí** para continuar.

    ![Deshabilitar protección](./media/backup-azure-manage-vms/confirm-unregister.png)

## Eliminación de datos de copia de seguridad
Los datos de copia de seguridad asociados a una máquina virtual se pueden eliminar en los siguientes momentos:

- Durante el trabajo de detención de la protección
- Después de realizar un trabajo de detención de la protección en una máquina virtual

Para eliminar los datos de copia de seguridad de una máquina virtual que se encuentre en estado de *Protección detenida* después de haber realizado correctamente un trabajo **Detener copia de seguridad**:

1. Vaya a la página **Elementos protegidos**, elija **Máquina virtual de Azure** como *tipo* y haga clic en el botón **Seleccionar**.

    ![Tipo de máquina virtual](./media/backup-azure-manage-vms/vm-type.png)

2. Seleccione la máquina virtual. La máquina virtual estará en el estado **Protección detenida**.

    ![Detención de la protección](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. Haga clic en el botón **ELIMINAR**, situado en la parte inferior de la página.

    ![Eliminación de copia de seguridad](./media/backup-azure-manage-vms/delete-backup.png)

4. En el asistente **Eliminar datos de copia de seguridad**, elija un motivo para eliminar los datos de copia de seguridad (muy recomendable) y haga clic en **Enviar**.

    ![Eliminación de datos de copia de seguridad](./media/backup-azure-manage-vms/delete-backup-data.png)

5. Esto creará un trabajo para eliminar los datos de copia de seguridad de la máquina virtual seleccionada. Haga clic en **Ver trabajo** para ver el trabajo correspondiente en la página Trabajos.

    ![Eliminación de datos correcta](./media/backup-azure-manage-vms/delete-data-success.png)

    Una vez completado el trabajo, se eliminará la entrada correspondiente a la máquina virtual de la página **Elementos protegidos**.

## Panel
En la página **Panel**, puede revisar la información sobre las máquinas virtuales de Azure, su almacenamiento y los trabajos asociados a ellas durante las últimas 24 horas. Puede ver el estado de copia de seguridad y los errores de copia de seguridad asociados.

![Panel](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

>[AZURE.NOTE] Los valores indicados en el panel se actualizan cada 24 horas.

## Operaciones de auditoría
Copia de seguridad de Azure proporciona la revisión de los "registros de operación" de las operaciones de copia de seguridad desencadenadas por el cliente, lo que facilita ver exactamente qué operaciones de administración se realizaron en el almacén de copia de seguridad. Los registros de operaciones permiten excelentes análisis finales y soportes técnicos de auditoría para las operaciones de copia de seguridad.

Las siguientes operaciones se registran en los registros de operaciones:

- Registro
- Anulación del registro
- Configuración de protección
- Copia de seguridad (tanto programada como a petición mediante BackupNow)
- Restauración
- Detener protección
- Eliminación de datos de copia de seguridad
- Add policy
- Eliminación de directiva
- Actualización de directiva
- Cancelar trabajo

Para ver los registros de operación correspondientes a un almacén de copia de seguridad:

1. Vaya a **Servicios de administración** en el portal de Azure y, a continuación, haga clic en la pestaña **Registros de operaciones**.

    ![Registros de operaciones](./media/backup-azure-manage-vms/ops-logs.png)

2. En los filtros, seleccione **Copia de seguridad** como *Tipo*, especifique el nombre del almacén de copia de seguridad en *Nombre de servicio* y haga clic en **Enviar**.

    ![Filtro de registros de operaciones](./media/backup-azure-manage-vms/ops-logs-filter.png)

3. En los registros de operaciones, seleccione cualquier operación y haga clic en **Detalles** para ver los detalles correspondientes a una operación.

    ![Detalles de obtención de registros de operaciones](./media/backup-azure-manage-vms/ops-logs-details.png)

    El **asistente para detalles** contiene información sobre la operación desencadenada, Id. de trabajo, recurso en el que se desencadena esta operación y hora de inicio de la operación.

    ![Detalles de la operación](./media/backup-azure-manage-vms/ops-logs-details-window.png)

## Notificaciones de alerta
Puede obtener notificaciones de alerta personalizadas para los trabajos del portal. Esto se consigue mediante la definición de reglas de alerta basadas en PowerShell en los eventos de registros operativos.

Las alertas basadas en eventos funcionan en el modo de recursos de Azure. Cambie al modo de recursos de Azure mediante la ejecución del siguiente cmdlet en el modo de comandos con privilegios elevados:

```
PS C:\> Switch-AzureMode AzureResourceManager
```

Para definir una notificación personalizada que avise de errores de copia de seguridad, puede ver un comando de ejemplo parecido al siguiente:

```
PS C:\> Add-AlertRule -Operator GreaterThanOrEqual -Threshold 1 -ResourceId '/subscriptions/86eeac34-eth9a-4de3-84db-7a27d121967e/resourceGroups/RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US/providers/microsoft.backupbvtd2/BackupVault/trinadhVault' -EventName Backup  -EventSource Administrative -Level Error -OperationName 'Microsoft.Backup/backupVault/Backup' -ResourceProvider Microsoft.Backup -Status Failed  -SubStatus Failed -RuleType Event -Location eastus -ResourceGroup RecoveryServices-DP2RCXUGWS3MLJF4LKPI3A3OMJ2DI4SRJK6HIJH22HFIHZVVELRQ-East-US -Name Backup-Failed -Description 'Backup failed for one of the VMs in vault trinadhkVault' -CustomEmails 'contoso@microsoft.com' -SendToServiceOwners
```

**ResourceId**: puede obtenerlo en la ventana emergente de registros de operaciones tal como se describe en la sección anterior. ResourceUri en una ventana emergente de detalles de una operación es el ResourceId que se debe indicar para este cmdlet.

**EventName**: para alertas acerca de copias de seguridad de máquinas virtuales de IaaS, los valores admitidos son: Register, Unregister, ConfigureProtection, Backup, Restore, StopProtection, DeleteBackupData, CreateProtectionPolicy, DeleteProtectionPolicy, UpdateProtectionPolicy

**Level**: los valores admitidos son: Informational, Error. Para alertas acerca de errores en acción, use Error y para alertas sobre trabajos realizados correctamente, use Informational.

**OperationName**: tendrá el formato "Microsoft.Backup/backupvault/<EventName>", donde EventName es tal como se describió anteriormente.

**Status**: los valores admitidos son: Started, Succeeded y Failed. Es conveniente mantener Informational como nivel para un estado de correcto.

**SubStatus**: igual que estado para las operaciones de copia de seguridad

**RuleType**: manténgalo como *Event* ya que las alertas de copia de seguridad están basadas en eventos.

**ResourceGroup**: ResourceGroup del recurso en el que se desencadena la operación. Puede obtenerlo del valor de ResourceId. El valor entre los campos */resourceGroups/* y */providers/* en el valor de ResourceId es el valor de ResourceGroup.

**Name**: nombre de la regla de alerta.

**Description**: descripción de la regla de alerta.

**CustomEmails**: especifique la dirección de correo electrónico personalizada a la que desea enviar la notificación de alerta

**SendToServiceOwners**: esta opción envía la notificación de alerta a todos los administradores y coadministradores de la suscripción.

Un mensaje de alerta de ejemplo se parecerá a lo siguiente:

Encabezado de ejemplo:

![Encabezado de la alerta](./media/backup-azure-manage-vms/alert-header.png)

Cuerpo de ejemplo del mensaje de alerta:

![Cuerpo de la alerta](./media/backup-azure-manage-vms/alert-body.png)

### Limitaciones de las alertas
Las alertas basadas en eventos están sometidas a las siguientes limitaciones:

1. Las alertas se activan en todas las máquinas virtuales del almacén de copia de seguridad. No es posible personalizar esto para obtener alertas para un conjunto específico de máquinas virtuales de un almacén de copia de seguridad.
2. Las alertas se resuelven automáticamente si no hay ningún evento coincidente de alerta desencadenado a lo largo de la duración de la alerta siguiente. Use el parámetro *WindowSize* del cmdlet Add-AlertRule para establecer la duración de desencadenamiento de alerta.

## Pasos siguientes

- [Restauración de máquinas virtuales de Azure](backup-azure-restore-vms.md)

<!---HONumber=AcomDC_0128_2016-->