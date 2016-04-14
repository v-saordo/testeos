<properties 
   pageTitle="Trabajos de copia de seguridad de Administrador de instantáneas StorSimple | Microsoft Azure"
   description="Describe cómo usar el complemento MMC de Administrador de instantáneas StorSimple para ver y administrar trabajos de copia de seguridad programados, actualmente en ejecución y completados."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/28/2015"
   ms.author="v-sharos" />


# Uso de Administrador de instantáneas StorSimple para ver y administrar trabajos de copia de seguridad

## Información general

El nodo **Trabajos** en el panel **Ámbito** muestra las tareas de copia de seguridad **Programada**, **Últimas 24 horas** y **En ejecución** que se han iniciado de forma interactiva o mediante una directiva configurada.

Este tutorial explica cómo se puede usar el nodo **trabajos** para mostrar información acerca de los trabajos de copia de seguridad programados, recientes y actualmente en ejecución. (La lista de trabajos y la información correspondiente aparece en el panel **Resultados**.) Además, puede es posible hacer clic en un trabajo de la lista y ver un menú contextual que enumera las acciones disponibles.

## Visualización de los trabajos programados

Use el procedimiento siguiente para ver los trabajos de copia de seguridad programados.

#### Para ver los trabajos programados

1. Haga clic en el icono del escritorio para iniciar Administrador de instantáneas StorSimple. 

2. En el panel **Ámbito**, expanda el nodo **Trabajos** y, a continuación, haga clic en **Programado**. La siguiente información aparece en el panel **Resultados**:

    - **Nombre**: el nombre de la instantánea programada

    - **Siguiente ejecución**: la fecha y hora de la siguiente instantánea programada

    - **Última ejecución**: la fecha y hora de la instantánea programada más reciente

    >[AZURE.NOTE]En las instantáneas que se realizan una sola vez, los valores de **Siguiente ejecución** y **Última ejecución** serán iguales.
 
    ![Trabajos de copia de seguridad programados](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_scheduled.png)
 
3. Para realizar acciones adicionales en un trabajo específico, haga clic con el botón derecho en el nombre del trabajo en el panel **Resultados** y realice su selección entre las opciones de menú.

## Visualización de los trabajos recientes

Utilice el procedimiento siguiente para ver la copia de seguridad y restaurar trabajos que se completaron en las últimas 24 horas.

#### Para ver los trabajos recientes

1. Haga clic en el icono del escritorio para iniciar Administrador de instantáneas StorSimple.

2. En el panel **Ámbito**, expanda el nodo **Trabajos** y, a continuación, haga clic en **Últimas 24 horas**. El panel **Resultados** muestra los trabajos de copia de seguridad de las últimas 24 horas (hasta un máximo de 64 trabajos). La siguiente información aparece en el panel **Resultados**, según las opciones de **Vista** que se haya especificado:

    - **Nombre**: el nombre de la instantánea programada.
 
    - **Iniciado**: fecha y hora del comienzo de la instantánea.

    - **Detenido**: fecha y hora de la finalización o la interrupción de la instantánea.

    - **Transcurrido**: la cantidad de tiempo entre los horarios de **Iniciado** y **Detenido**.

    - **Estado**: el estado del trabajo completado recientemente. **Éxito** indica que la copia de seguridad se creó correctamente. **Error** indica que el trabajo no se ejecutó correctamente.

    - **Información**: el motivo del error.

    - **Bytes procesados (MB)**: la cantidad de datos del grupo de volúmenes que se procesó (en MB).

    ![Trabajos ejecutados en las últimas 24 horas](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_Last_24_hours.png)

3. Para realizar acciones adicionales en un trabajo específico, haga clic con el botón derecho en el nombre del trabajo en el panel **Resultados** y realice su selección entre las opciones de menú.

    ![Eliminación de un trabajo](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Delete_backup.png)
     
## Visualización de los trabajos en ejecución

Utilice el procedimiento siguiente para ver los trabajos que se están ejecutando en el momento presente.

#### Para ver los trabajos en ejecución

1. Haga clic en el icono del escritorio para iniciar Administrador de instantáneas StorSimple.

2. En el panel **Ámbito**, expanda el nodo **Trabajos** y haga clic en **En ejecución**. Dependiendo de las opciones de **Vista** que se especifiquen, la siguiente información aparece en el panel **Resultados**:

    - **Nombre**: el nombre de la instantánea programada.

    - **Iniciado**: fecha y hora del comienzo de la instantánea.

    - **Punto de control**: la acción actual de la copia de seguridad.

    - **Estado**: el porcentaje de finalización.
    
    - **Transcurrido**: la cantidad de tiempo que ha transcurrido desde que comenzó la copia de seguridad.

    - **Rendimiento medio (MB)**: la cantidad media de datos entregados, expresada en megabytes (MB).

    - **Bytes procesados (MB)**: la cantidad de datos del grupo de volúmenes que se procesó (en MB).

    - **Bytes escritos (MB)**: la cantidad de datos que se escriben en la copia de seguridad (en MB).

    ![Trabajos en ejecución](./media/storsimple-snapshot-manager-manage-backup-jobs/HCS_SSM_Jobs_running.png)

3. Para realizar acciones adicionales en un trabajo específico, haga clic con el botón derecho en el nombre del trabajo en el panel **Resultados** y realice su selección entre las opciones de menú.

## Pasos siguientes

- Obtenga más información sobre el [uso de Snapshot Manager de StorSimple para administrar la solución de StorSimple](storsimple-snapshot-manager-admin.md).
- Obtenga más información sobre el [uso de Snapshot Manager de StorSimple para administrar el catálogo de copias de seguridad](storsimple-snapshot-manager-manage-backup-catalog.md).















            


 

<!---HONumber=AcomDC_0107_2016-->