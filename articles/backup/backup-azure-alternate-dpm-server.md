<properties
	pageTitle="Recuperación de datos de otro servidor DPM en el almacén de copia de seguridad | Microsoft Azure"
	description="Recupere los datos que protegió en un almacén de Copia de seguridad de Azure desde cualquier servidor DPM registrado en ese almacén."
	services="backup"
	documentationCenter=""
	authors="giridharreddy"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/10/2015"
	ms.author="giridham;jimpark"/>

# Recuperación de datos de otro servidor DPM en el almacén de copia de seguridad
Ahora puede recuperar los datos que protegió en un almacén de Copia de seguridad de Azure desde cualquier servidor DPM registrado en ese almacén. El proceso para hacerlo está completamente integrado en la consola de administración de DPM y es similar a los otros flujos de trabajo de recuperación.

Para recuperar datos de otro servidor DPM en el almacén de copia de seguridad necesitará [System Center Data Protection Manager UR7](https://support.microsoft.com/es-ES/kb/3065246) y el [agente de Copia de seguridad de Azure más reciente](http://aka.ms/azurebackup_agent).

## Recuperación de datos de otro servidor DPM
Para recuperar datos de otro servidor DPM:

1. En la pestaña **Recuperación** de la consola de administración de DPM, haga clic en **'Agregar DPM externo'** (en la parte superior izquierda de la pantalla).

    ![DPM externo de AD](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)

2. Descargue nuevas **credenciales del almacén** asociado al **servidor DPM** en donde se recuperan los datos, elija el servidor DPM en la lista de servidores DPM registrados con el almacén de copia de seguridad y especifique la **frase de contraseña de cifrado** asociada al servidor DPM cuyos datos se recuperan.

    ![Credenciales externas de DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

    >[AZURE.NOTE]Solo los servidores DPM asociados al mismo almacén de registro pueden recuperar datos unos de otros.

    Una vez agregado correctamente el servidor DPM externo, puede examinar los datos del servidor DPM externo y el servidor DPM local en la pestaña **Recuperación**.

3. Vaya a la lista disponible de servidores de producción protegidos por el servidor DPM externo y seleccione el origen de datos adecuado.

    ![Ir al servidor DPM externo](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)

4. Seleccione **el mes y año** en la lista desplegable **Puntos de recuperación**, seleccione la **Fecha de recuperación** necesaria correspondiente a cuando se creó el punto de recuperación y seleccione la **Hora de recuperación**.

    Aparecerá una lista de archivos y carpetas en el panel inferior que se puede examinar y recuperar en cualquier ubicación.

    ![Puntos de recuperación del servidor DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)

5. Haga clic con el botón derecho en el elemento apropiado y haga clic en **Recuperar**.

    ![Recuperación del DPM externo](./media/backup-azure-alternate-dpm-server/recover.png)

6. Revise la **Selección de recuperación**. Compruebe los datos y la hora de la copia de seguridad que se va a recuperar, así como el origen desde el que se creó la copia de seguridad. Si la selección no es correcta, haga clic en **Cancelar** a fin de volver a la pestaña de recuperación para seleccionar el punto de recuperación adecuado. Si la selección es correcta, haga clic en **Siguiente**.

    ![Resumen de recuperación del DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)

7. Seleccione **Recuperar en una ubicación alternativa**. Use **Examinar** para ir a la ubicación correcta para la recuperación.

    ![Ubicación alternativa de la recuperación del DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)

8. Elija la opción relacionada con **Crear copia**, **Omitir** o **Sobrescribir**.
    - **Crear copia** creará una copia del archivo en caso de que haya un conflicto de nombres.
    - **Omitir** omitirá la recuperación del archivo en caso de que haya un conflicto de nombres.
    - **Sobrescribir** sobrescribirá la copia existente en la ubicación especificada en caso de que haya un conflicto de nombres.

    Elija la opción adecuada para **Restaurar seguridad**. Puede aplicar la configuración de seguridad del equipo de destino donde se están recuperando los datos o la configuración de seguridad aplicable al producto en el momento en que se creó el punto de recuperación.

    Identifique si se enviará una **notificación** una vez que se complete la recuperación correctamente.

    ![Notificaciones de recuperación del DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)

9. En la pantalla **Resumen** se muestran las opciones elegidas hasta el momento. Al hacer clic en **'Recuperar'**, se recuperarán los datos en la ubicación local adecuada.

    ![Resumen de las opciones de recuperación del DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

    >[AZURE.NOTE]El trabajo de recuperación se puede supervisar en la pestaña **Supervisión** del servidor DPM.

    ![Supervisión de la recuperación](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)

10. Puede hacer clic en **Borrar DPM externo** en la pestaña **Recuperación** del servidor DPM para quitar la vista del servidor DPM externo.

    ![Borrar DPM externo](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## Solución de mensajes de error
|N.º |	Mensaje de error |	Pasos para solucionar problemas |
| :-------------: |:-------------| :-----|
|1\.|		Este servidor no está registrado en el almacén especificado por las credenciales del almacén.|	**Causa:** Este error aparece cuando el archivo de credenciales del almacén seleccionado no pertenece al almacén de copia de seguridad asociado al servidor DPM en el que se intenta la recuperación. <br> **Resolución:** Descargue el archivo de credenciales del almacén del almacén de copia de seguridad en el que está registrado el servidor DPM.|
|2\.|		Los datos recuperables no están disponibles o el servidor seleccionado no es un servidor DPM.|	**Causa:** No hay ningún otro servidor DPM con DPM 2012 R2 UR7 registrado en el almacén de copia de seguridad, los servidores DPM con DPM 2012 R2 UR7 aún no han cargado los metadatos o el servidor seleccionado no es el servidor DPM (también llamado Windows Server o cliente Windows). <br> **Resolución:** Si hay otros servidores DPM registrados en el almacén de copia de seguridad, asegúrese de que SCDPM 2012 R2 UR7 y el agente de Copia de seguridad de Azure más reciente están instalados. <br>Si hay otros servidores DPM registrados en el almacén de copia de seguridad con DPM 2012 R2 UR7, espere un día después de la instalación de UR7 para iniciar el proceso de recuperación. La tarea nocturna cargará los metadatos para las copias de seguridad protegidas anteriormente en la nube. Los datos estarán disponibles para la recuperación.|
|3\.|		Ningún otro servidor DPM está registrado en este almacén.|	**Causa:** No hay ningún otro servidor DPM con DPM 2012 R2 UR7 o superior registrado en el almacén desde el que se intenta la recuperación.<br>** Resolución: ** Si hay otros servidores DPM registrados en el almacén de copia de seguridad, asegúrese de que SCDPM 2012 R2 UR7 y el agente de Copia de seguridad de Azure más reciente están instalados.<br>Si hay otros servidores DPM registrados en el almacén de copia de seguridad con DPM 2012 R2 UR7, espere un día después de instalar UR7 para iniciar el proceso de recuperación. La tarea nocturna cargará los metadatos para las copias de seguridad protegidas anteriormente en la nube. Los datos estarán disponibles para la recuperación.|
|4\.|		La frase de contraseña de cifrado especificada no coincide con la frase de contraseña asociada al siguiente servidor: **<server name>**|	**Causa:** La frase de contraseña de cifrado que se usa en el proceso de cifrado de los datos del servidor DPM que se recupera no coincide con la frase de contraseña de cifrado proporcionada. El agente no puede descifrar los datos. Por lo tanto, se produce un error en la recuperación.<br>** Resolución: ** Especifique la contraseña de cifrado exacta asociada al servidor DPM cuyos datos se recuperan.|

## Preguntas más frecuentes:
1. **¿Por qué no puedo agregar un servidor DPM externo desde otro servidor DPM después de instalar UR7 y el agente de copia de seguridad de Azure más reciente?**

    A) En el caso de los servidores DPM existentes con orígenes de datos protegidos en la nube (con un paquete acumulativo anterior al Paquete acumulativo de actualizaciones 7), tendrá que esperar al menos un día después de instalar UR7 y el agente de Copia de seguridad de Azure más reciente para empezar a *agregar el servidor DPM externo*. Esto es necesario para cargar los metadatos de los grupos de protección de DPM en Azure. Esto se produce la primera vez durante una tarea nocturna.

2. **¿Cuál es la versión mínima del agente de copia de seguridad de Azure necesario?**

    A) la versión mínima del agente de copia de seguridad de Azure para habilitar esta característica es 2.0.8719.0. Se puede comprobar la versión del agente de Copia de seguridad de Azure; para ello, vaya a Panel de control **>** Todos los elementos del Panel de control **>** Programas y características **>** Agente de Servicios de recuperación de Microsoft Azure. Si la versión es anterior a la 2.0.8719.0, descargue el [agente de Copia de seguridad de Azure más reciente](https://go.microsoft.com/fwLink/?LinkID=288905) e instálelo.

    ![Borrar DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## Pasos siguientes:
[Preguntas más frecuentes sobre Copia de seguridad de Azure](backup-azure-backup-faq.md)

<!---HONumber=AcomDC_1217_2015-->