## Creación de un almacén de copia de seguridad para una máquina virtual

Un almacén de copia de seguridad es una entidad que almacena todas las copias de seguridad y los puntos de recuperación creados con el tiempo. El almacén de copia de seguridad contiene también las directivas de copia de seguridad que se aplicarán a las máquinas virtuales cuya copia de seguridad se está realizando.

La siguiente imagen muestra las relaciones entre las diversas entidades de Copia de seguridad de Azure: ![Relaciones y entidades de Copia de seguridad de Azure](./media/backup-create-vault-for-vms/vault-policy-vm.png)

Para crear un almacén de copia de seguridad:

1. Inicie sesión en el [Portal de Azure](http://manage.windowsazure.com/).

2. En el Portal de Azure, haga clic en **Nuevo** > **Integración híbrida** > **Copia de seguridad**. Al hacer clic en **Copia de seguridad**, cambiará automáticamente al portal clásico (que aparece después de la nota).

    ![Portal de Ibiza](./media/backup-create-vault-for-vms/Ibiza-portal-backup01.png)

    >[AZURE.NOTE] Si la suscripción se usó por última vez en el portal clásico, es posible que la suscripción se abra en el portal clásico. En este caso, para crear un almacén de copia de seguridad, haga clic en **Nuevo** > **Servicios de datos** > **Servicios de recuperación** > **Almacén de copia de seguridad** > **Creación rápida** (consulte la ilustración siguiente).

    ![Crear un almacén de copia de seguridad](./media/backup-create-vault-for-vms/backup_vaultcreate.png)

3. En **Nombre**, escriba un nombre descriptivo para identificar el almacén. El nombre debe ser único para la suscripción de Azure. Escriba un nombre que tenga entre 2 y 50 caracteres. Debe comenzar por una letra y solo puede contener letras, números y guiones.

4. En **Región**, seleccione la región geográfica del almacén. El almacén debe estar en la misma región que las máquinas virtuales que desea proteger. Si tiene máquinas virtuales en varias regiones, debe crear un almacén de copia de seguridad en cada región. No es necesario especificar cuentas de almacenamiento para almacenar los datos de copia de seguridad: el almacén de copia de seguridad y el servicio Copia de seguridad de Azure lo controlarán automáticamente.

5. En **Suscripción**, seleccione la suscripción que desea asociar al almacén de copia de seguridad. Solo habrá varias opciones si la cuenta de su organización está asociada a varias suscripciones de Azure.

5. Haga clic en **Crear almacén**. La creación del almacén de credenciales de copia de seguridad puede tardar unos minutos. Supervise las notificaciones de estado en la parte inferior del portal.

    ![Crear la notificación del sistema del almacén](./media/backup-create-vault-for-vms/creating-vault.png)

6. Un mensaje confirmará que el almacén se creó correctamente. Aparecerá en la página **Servicios de recuperación** como **Activo**. Asegúrese de elegir la opción de redundancia de almacenamiento apropiada justo después de crear el almacén. Lea más sobre cómo [establecer la opción de redundancia de almacenamiento en el almacén de copia de seguridad](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Lista de copias de seguridad](./media/backup-create-vault-for-vms/backup_vaultslist.png)

7. Haga clic en el almacén de copia de seguridad para ir a la página **Inicio rápido**, donde se muestran las instrucciones para realizar la copia de seguridad de las máquinas virtuales de Azure.

    ![Instrucciones de copia de seguridad de máquina virtual en la página del Panel](./media/backup-create-vault-for-vms/vmbackup-instructions.png)

<!---HONumber=AcomDC_0302_2016-->