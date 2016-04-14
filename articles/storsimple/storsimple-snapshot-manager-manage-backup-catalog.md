<properties 
   pageTitle="Catálogo de copias de seguridad de Administrador de instantáneas StorSimple | Microsoft Azure"
   description="Describe cómo usar el complemento MMC del Administrador de instantáneas StorSimple para ver y administrar el catálogo de copia de seguridad."
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

# Uso de Administrador de instantáneas StorSimple para administrar el catálogo de copia de seguridad

## Información general

La función principal de Administrador de instantáneas StorSimple es permitirle crear copias de seguridad coherentes con las aplicaciones de los volúmenes de Azure StorSimple en el formato de instantáneas. Las instantáneas se enumeran en un archivo XML llamado *catálogo de copia de seguridad*. El catálogo de copia de seguridad organiza las instantáneas por grupo de volúmenes y, luego, por instantáneas locales o instantánea de nube.

Puede ver el catálogo de copia de seguridad expandiendo el nodo **Catálogo de copia de seguridad** en el panel **Ámbito** y luego el grupo de volúmenes.

- Si hace clic en el nombre del grupo de volúmenes, el panel **Resultados** muestra el número de instantáneas locales y las instantáneas de nube disponibles para el grupo de volúmenes. 

- Si hace clic en **Instantánea Local** o **Instantánea de nube** el panel **Resultados** muestra la siguiente información sobre cada instantánea de copia de seguridad (dependiendo de la configuración de **Vista**):

    - **Nombre**: el momento en el que se tomó la instantánea. 

    - **Tipo**: si se trata de una instantánea local o una instantánea de nube.

    - **Propietario**: el propietario del contenido.

    - **Disponible**: si la instantánea está disponible actualmente. True indica que la instantánea esté disponible y se pueden restaurar; False indica que la instantánea ya no está disponible.

    - **Importado**: si se importó la copia de seguridad. **True** indica que la copia de seguridad se importó desde el servicio StorSimple Manager en el momento en el que se configuró el dispositivo en Administrador de instantáneas StorSimple; **False** indica que no se importó, pero se creó con Administrador de instantáneas StorSimple. (Se puede identificar fácilmente un grupo de volúmenes importado porque se agrega un sufijo que identifica el dispositivo desde el que se ha importado el grupo de volúmenes.)

    ![Catálogo de copias de seguridad](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)

- Si expande **Instantánea local** o **Instantánea de nube** y, a continuación, hace clic en un nombre de instantánea individual, el panel **Resultados** muestra la siguiente información acerca de la instantánea seleccionada:

    - **Nombre**: el volumen identificado por la letra de unidad. 

    - **Nombre local**: el nombre local de la unidad (si está disponible).

    - **Dispositivo**: el nombre del dispositivo en el que reside el volumen.

    - **Disponible**: si la instantánea está disponible actualmente. **True** indica que la instantánea esté disponible y se pueden restaurar; **False** indica que la instantánea ya no está disponible.

Este tutorial describe cómo se puede usar el nodo **Catálogo de copia de seguridad** para completar las tareas siguientes:

- Restauración de un volumen 
- Clonación de un volumen o un grupo de volúmenes 
- Eliminación de una copia de seguridad 
- Recuperación de un archivo
- Restauración de la base de datos de Administrador de instantáneas Storsimple

## Restauración de un volumen

Utilice el siguiente procedimiento para restaurar un volumen a partir de la copia de seguridad.

#### Requisitos previos

Si aún no lo ha hecho, cree un volumen y un grupo de volúmenes y, a continuación, elimine el volumen. De forma predeterminada, Administrador de instantáneas StorSimple realiza una copia de un volumen antes de permitir que se elimine. Esta precaución puede evitar la pérdida de datos si se elimina accidentalmente el volumen o si los datos deben recuperarse por cualquier motivo.

Administrador de instantáneas StorSimple muestra el siguiente mensaje mientras crea la copia de seguridad preventiva.

![Mensaje de instantánea automático](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png)

>[AZURE.IMPORTANT]No se puede eliminar un volumen que forma parte de un grupo de volúmenes. La opción Eliminar no está disponible.<br>

#### Para restaurar un volumen

1. Haga clic en el icono del escritorio para iniciar Administrador de instantáneas StorSimple. 

2. En el panel **Ámbito**, expanda el nodo **Catálogo de copia de seguridad**, expanda un grupo de volúmenes y, a continuación, haga clic en **Instantáneas locales** o **Instantáneas de nube**. Aparece una lista de las instantáneas de copia de seguridad en el panel **Resultados**.

3. Busque la copia de seguridad que desea restaurar, haga clic con el botón derecho y a continuación, haga clic en **Restaurar**.

    ![Restauración del catálogo de copia de seguridad](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png)

4. En la página de confirmación, revise los detalles, escriba **Confirmar** y, a continuación, haga clic en **Aceptar**. Administrador de instantáneas StorSimple usa la copia de seguridad para restaurar el volumen.

    ![Mensaje de confirmación de restauración](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png)

5. Puede supervisar la acción de restauración mientras se ejecuta. En el panel **Ámbito**, expanda el nodo **Trabajos** y, a continuación, haga clic en **En ejecución**. Los detalles del trabajo aparecen en el panel **Resultados**. Cuando finalice el trabajo de restauración, los detalles del mismo se transfieren a la lista **Últimas 24 horas**.

## Clonación de un volumen o un grupo de volúmenes

Utilice el procedimiento siguiente para crear un duplicado (clon) de un volumen o un grupo de volúmenes.

#### Para clonar un volumen o un grupo de volúmenes

1. Haga clic en el icono del escritorio para iniciar Administrador de instantáneas StorSimple.

2. En el panel **Ámbito**, expanda el nodo **Catálogo de copia de seguridad**, expanda un grupo de volúmenes y, a continuación, haga clic en **Instantáneas de nube**. Aparecerá una lista de copias de seguridad en el panel **Resultados**.

3. Encuentre el volumen o el grupo de volúmenes que desea clonar, haga clic con el botón derecho en el volumen o el nombre del grupo de volúmenes y haga clic en **Clonar**. Aparece el cuadro de diálogo **Clonar instantánea de nube**.

    ![Clonación de una instantánea de nube](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png)

4. Completar el cuadro de diálogo **Clonar instantánea de nube** de la forma siguiente:

    1. En el cuadro de texto **Nombre**, escriba un nombre para el volumen clonado. Este nombre aparecerá en el nodo **Volúmenes**. 

    2. (Opcional) seleccione **Unidad** y, a continuación, seleccione una letra de unidad de la lista desplegable.

    3. (Opcional) seleccione **Carpeta (NTFS)** y escriba una ruta de acceso a la carpeta o haga clic en Examinar y seleccione una ubicación para la carpeta.

    4. Haga clic en **Crear**.

5. Cuando finalice el proceso de clonación, tiene que inicializar el volumen clonado. Inicie el Administrador del servidor y, a continuación, inicie Administración de discos. Para obtener instrucciones detalladas, consulte [Montar volúmenes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Una vez inicializado, el volumen aparecerá en la lista en el nodo **Volúmenes** en el panel **Ámbito**. Si el volumen no aparece, actualice la lista de volúmenes (haga clic con el botón derecho en el nodo **Volúmenes** y, a continuación, haga clic en **Actualizar**).

## Eliminación de una copia de seguridad

Utilice el procedimiento siguiente para eliminar una instantánea del catálogo de copia de seguridad.

>[AZURE.NOTE]Si se elimina una instantánea, se eliminan también los datos de copia de seguridad asociados a la instantánea. De todas formas, el proceso de limpieza de datos de la nube puede tardar algún tiempo.<br>
 
#### Para eliminar una copia de seguridad

1. Haga clic en el icono del escritorio para iniciar Administrador de instantáneas StorSimple.

2. En el panel **Ámbito**, expanda el nodo **Catálogo de copia de seguridad**, expanda un grupo de volúmenes y, a continuación, haga clic en **Instantáneas locales** o **Instantáneas de nube**. Aparecerá una lista de instantáneas en el panel **Resultados**.

3. Haga clic con el botón derecho en la instantánea que desea eliminar y, a continuación, haga clic en **Eliminar**.

4. Cuando aparezca el mensaje de confirmación, haga clic en **Aceptar**.

## Recuperación de un archivo

Si un archivo se elimina accidentalmente de un volumen, puede recuperar el archivo a través de la recuperación de una instantánea con fecha anterior a la eliminación, usando la instantánea para crear un clon del volumen y, a continuación, copiando el archivo del volumen clonado en el volumen original.

#### Requisitos previos

Antes de comenzar, asegúrese de que tiene una copia de seguridad actual del grupo de volúmenes. A continuación, elimine un archivo almacenado en uno de los volúmenes de ese grupo de volúmenes. Por último, siga los pasos a continuación para restaurar el archivo eliminado desde la copia de seguridad.

#### Para recuperar un archivo eliminado

1. Haga clic en el icono Administrador de instantáneas StorSimple en su escritorio. Aparece la ventana de la consola de Administrador de instantáneas StorSimple. 

2. En el panel **Ámbito**, expanda el nodo **Catálogo de copia de seguridad** y vaya a una instantánea que contenga el archivo eliminado. Normalmente, debe seleccionar una instantánea que se haya creado antes de la eliminación.

3. Busque el volumen que desea clonar, haga clic con el botón derecho y a continuación, haga clic en **Clonar**. Aparece el cuadro de diálogo **Clonar instantánea de nube**.

    ![Clonación de una instantánea de nube](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png)

4. Completar el cuadro de diálogo **Clonar instantánea de nube** de la forma siguiente:

   1. En el cuadro de texto **Nombre**, escriba un nombre para el volumen clonado. Este nombre aparecerá en el nodo **Volúmenes**.

   2. (Opcional) Seleccione **Unidad** y, a continuación, seleccione una letra de unidad de la lista desplegable.

   3. (Opcional) seleccione **Carpeta (NTFS)** y escriba una ruta de acceso a la carpeta o haga clic en **Examinar** y seleccione una ubicación para la carpeta.

   4. Haga clic en **Crear**.

5. Cuando finalice el proceso de clonación, tiene que inicializar el volumen clonado. Inicie el Administrador del servidor y, a continuación, inicie Administración de discos. Para obtener instrucciones detalladas, consulte [Montar volúmenes](storsimple-snapshot-manager-manage-volumes.md#mount-volumes). Una vez inicializado, el volumen aparecerá en la lista en el nodo **Volúmenes** en el panel **Ámbito**.

    Si el volumen no aparece, actualice la lista de volúmenes (haga clic con el botón derecho en el nodo **Volúmenes** y, a continuación, haga clic en **Actualizar**).

6. Abra la carpeta NTFS que contiene el volumen clonado, expanda el nodo **Volúmenes** y después abra el volumen clonado. Busque el archivo que desea recuperar y cópielo en el volumen principal.

7. Después de restaurar el archivo, puede eliminar la carpeta NTFS que contiene el volumen clonado.

## Restauración de la base de datos de Administrador de instantáneas StorSimple

Debe hacer de forma regular copias de seguridad de la base de datos de Administrador de instantáneas StorSimple en el equipo host. Si se produce un desastre o se produce un error en el equipo host por cualquier motivo, se puede realizar una restauración desde la copia de seguridad. La creación de la copia de seguridad de la base de datos es un proceso manual.

#### Para copiar y restaurar la base de datos

1. Detenga el servicio de administración de Microsoft StorSimple:

    1. Inicie el Administrador del servidor.

    2. En el panel Administrador del servidor, en el menú **Herramientas**, seleccione **Servicios**.

    3. En la ventana **Servicios**, seleccione el **servicio de administración de Microsoft StorSimple**.

    4. En el panel derecho, en **Servicio de administración de Microsoft StorSimple**, haga clic en **Detener el servicio**.

2. En el equipo host, vaya a C:\\ProgramData\\Microsoft\\StorSimple\\BACatalog.

    >[AZURE.NOTE]ProgramData es una carpeta oculta.
 
3. Busque el archivo XML de catálogo, copie el archivo y almacene la copia en una ubicación segura o en la nube. Si se produce un error en el host, puede usar este archivo de copia de seguridad para ayudar a recuperar las directivas de copia de seguridad que creó en Administrador de instantáneas StorSimple.

    ![Archivo de catálogo de copia de seguridad de Azure StorSimple](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)

4. Reinicie el servicio de administración de Microsoft StorSimple:

    1. En el panel Administrador del servidor, en el menú **Herramientas**, seleccione **Servicios**.
    
    2. En la ventana **Servicios**, seleccione el **servicio de administración de Microsoft StorSimple**.

    3. En el panel derecho, en **Servicio de administración de Microsoft StorSimple**, haga clic en **Reiniciar el servicio**.

5. En el equipo host, vaya a C:\\ProgramData\\Microsoft\\StorSimple\\BACatalog.

6. Elimine el archivo XML de catálogo y reemplácelo por la versión de copia de seguridad que creó.

7. Haga clic en el icono de escritorio de Administrador de instantáneas StorSimple para iniciar Administrador de instantáneas StorSimple.

## Pasos siguientes

- Obtenga más información sobre el [uso de Snapshot Manager de StorSimple para administrar la solución de StorSimple](storsimple-snapshot-manager-admin.md).
- Obtenga más información sobre las [tareas y flujos de trabajo de Snapshot Manager de StorSimple](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows).

<!---HONumber=AcomDC_0107_2016-->