<properties 
   pageTitle="Restaurar un volumen de StorSimple de una copia de seguridad | Microsoft Azure"
   description="Explica cómo usar la página del catálogo de copias de seguridad del Administrador de StorSimple para restaurar un volumen de StorSimple desde un conjunto de copias de seguridad."
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
   ms.date="12/14/2015"
   ms.author="v-sharos" />

# Restaurar un volumen de StorSimple de un conjunto de copia de seguridad (Update 2)

[AZURE.INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## Información general

La página **Catálogo de copias de seguridad** muestra todos los conjuntos de copia de seguridad que se crean cuando se realizan copias de seguridad manuales o automatizadas. Puede usar esta página para enumerar todas las copias de seguridad para un volumen o una directiva de copia de seguridad, seleccionar o eliminar las copias de seguridad, o usar una copia de seguridad para restaurar o clonar un volumen.

 ![Página del catálogo de copias de seguridad](./media/storsimple-restore-from-backup-set-u2/HCS_BackupCatalog.png)

Este tutorial explica cómo usar la página **Catálogo de copias de seguridad** para restaurar el dispositivo desde un conjunto de copia de seguridad.

Puede restaurar un volumen a partir de una copia de seguridad local o en la nube. En cualquier caso, la operación de restauración pone el volumen en línea inmediatamente mientras los datos se descargan en segundo plano.

Antes de iniciar una operación de restauración, debe ser consciente de las acciones siguientes:

- **Debe desconectar el volumen**: desconecte el volumen en el host y en el dispositivo antes de iniciar la operación de restauración. Aunque la operación de restauración pone el volumen automáticamente en línea en el dispositivo, debe poner el dispositivo en línea manualmente en el host. Puede poner el volumen en línea en el host tan pronto como el volumen está conectado en el dispositivo. (No es necesario esperar a que termine la operación de restauración). Para conocer los procedimientos, vaya a [Desconectar un volumen](storsimple-manage-volumes-u2.md#take-a-volume-offline).

- **El tipo de volumen después de la restauración**: los volúmenes eliminados se restauran en función del tipo de instantánea; es decir, los volúmenes anclados localmente se restauran como volúmenes anclados localmente y los volúmenes en capas, como volúmenes en capas.

    Para los volúmenes existentes, el tipo de uso actual del volumen reemplaza el tipo que se almacena en la instantánea. Por ejemplo, si restaura un volumen a partir de una instantánea realizada cuando el tipo de volumen se ha almacenado en capas y el tipo de volumen ahora está anclado localmente (debido a la realización de una operación de conversión), el volumen se restaurará como un volumen anclado localmente. De forma similar, si un volumen anclado localmente existente se ha expandido y posteriormente se restaura a partir de una instantánea más antigua que se realiza cuando el volumen es más pequeño, el volumen restaurado conservará el tamaño expandido actual.

    No se puede convertir un volumen de un volumen en capas a un volumen anclado localmente, o viceversa, mientras se está restaurando el volumen. Espere hasta que finalice la operación de restauración y después puede convertir el volumen a otro tipo. Para obtener información sobre cómo convertir un volumen, vaya a [Cambiar el tipo de volumen](storsimple-manage-volumes-u2.md#change-the-volume-type).

- **El tamaño del volumen se reflejará en el volumen restaurado**: ésta es una consideración importante si va a restaurar un volumen anclado localmente que se ha eliminado (porque los volúmenes anclados localmente se aprovisionan totalmente). Asegúrese de que dispone de suficiente espacio antes de intentar restaurar un volumen anclado localmente que se haya eliminado anteriormente.

- **No se puede expandir un volumen mientras se está restaurando**: espere a que termine la operación de restauración antes de intentar expandir el volumen. Para obtener información sobre la expansión de un volumen, vaya a [Modificar un volumen](storsimple-manage-volumes-u2.md#modify-a-volume).

- **Puede realizar una copia de seguridad mientras se restaura un volumen local**: para conocer los procedimientos, vaya a [Usar el servicio de Administrador de StorSimple para administrar directivas de copia de seguridad](storsimple-manage-backup-policies.md).

- **Puede cancelar una operación de restauración**: si se cancela el trabajo de restauración, el volumen se revertirá al estado en que estaba antes de iniciar la operación de restauración. Para conocer los procedimientos, vaya a [Cancelar un trabajo](storsimple-manage-jobs-u2.md#cancel-a-job).

## Cómo usar el catálogo de copias de seguridad

La página **Catálogo de copias de seguridad** proporciona una consulta que permite limitar la selección de conjuntos de copias de seguridad. Puede filtrar los conjuntos de copias de seguridad que se recuperan en función de los parámetros siguientes:

- **Dispositivo**: dispositivo en el que se creó el conjunto de copias de seguridad.
- **Directiva de copia de seguridad** o **Volumen**: directiva de copia de seguridad o volumen asociado a este conjunto de copias de seguridad.
- **De** y **A**: el intervalo de fecha y hora en el que se creó el conjunto de copias de seguridad.

A continuación, los conjuntos de copias de seguridad filtrados se presentan en forma de tabla en función de los siguientes atributos:

- **Nombre**: nombre de la directiva de copias de seguridad o del volumen asociado al conjunto de copias de seguridad.
- **Tamaño**: tamaño real del conjunto de copias de seguridad.
- **Creado en**: fecha y hora en que se crearon las copias de seguridad. 
- **Tipo**: los conjuntos de copias de seguridad pueden ser instantáneas locales o instantáneas en la nube. Una instantánea local es una copia de seguridad de todos los datos del volumen que se almacenan localmente en el dispositivo, mientras que una instantánea en la nube hace referencia a la copia de seguridad de los datos del volumen que residen en la nube. Las instantáneas locales proporcionan un acceso más rápido, mientras que las instantáneas en la nube son preferibles para la resistencia de los datos.
- **Iniciada por**: las copias de seguridad se pueden iniciar automáticamente en función de una programación o manualmente por el usuario. (Puede usar una directiva de copia de seguridad para programar copias de seguridad. Como alternativa, puede usar la opción **Realizar copia de seguridad** para realizar una copia de seguridad interactiva).

## Cómo restaurar un volumen de StorSimple de una copia de seguridad.

Puede usar la página **Catálogo de copias de seguridad** para restaurar el volumen StorSimple a partir de una copia de seguridad específica. Sin embargo, debe tener en cuenta que, cuando se restaura un volumen, el volumen volverá al estado en el que se encontraba cuando se realizó la copia de seguridad. Se perderán todos los datos que se agregaron después de la operación de copia de seguridad.

> [AZURE.WARNING] Cuando se realice una restauración a partir de una copia de seguridad, se reemplazarán los volúmenes existentes desde la copia de seguridad. Esto puede provocar la pérdida de los datos que se escribieron después de que se realizase la copia de seguridad.

### Para restaurar el volumen

1. En la página del servicio de Administrador de StorSimple, haga clic en la pestaña **Catálogo de copias de seguridad**.

    ![Catálogo de copias de seguridad](./media/storsimple-restore-from-backup-set-u2/HCS_Restore.png)

2. Seleccione una copia de seguridad de la siguiente manera:
  1. Seleccione el dispositivo adecuado.
  2. En la lista desplegable, seleccione el volumen o la directiva de copia de seguridad para la copia de seguridad que desea seleccionar.
  3. Especifique el intervalo de tiempo.
  4. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) para ejecutar esta consulta.
 
    Las copias de seguridad asociadas al volumen o la directiva de copia de seguridad seleccionados deben aparecer en la lista de conjuntos de copias de seguridad.

3. Expanda el conjunto de copias de seguridad para ver los volúmenes asociados. Estos volúmenes deben desconectarse en el host y en el dispositivo para que pueda restaurarlos. Acceda a los volúmenes de la página **Contenedores de volúmenes** y, a continuación, siga los pasos indicados en [Desconectar un volumen](storsimple-manage-volumes-u2.md#take-a-volume-offline) para desconectarlos.

    > [AZURE.IMPORTANT] Asegúrese de desconectar primero los volúmenes del host y, después, desconectar los volúmenes del dispositivo. Si no establece los volúmenes sin conexión en el host, esto podría causar daños en los datos.

4. Vuelva a la pestaña **Catálogo de copias de seguridad** y seleccione un conjunto de copias de seguridad.

5. Haga clic en **Restaurar** en la parte inferior de la página.

6. Se le pedirá confirmación. Revise la información de restauración y después seleccione la casilla de confirmación.

    ![Página de confirmación](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)

7. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). Se iniciará un trabajo de restauración que se puede ver accediendo a la página **Trabajos**.

8. Una vez completada la restauración, puede comprobar que los volúmenes de la copia de seguridad sustituyeron el contenido de los volúmenes.

![Vídeo disponible](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **Vídeo disponible**

Para ver un vídeo en que se muestra cómo usar las características de clonación y restauración en StorSimple para recuperar archivos eliminados, haga clic [aquí](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## Si se produce algún error en la restauración

Recibirá una alerta si se produce un error en la operación de restauración por algún motivo. En su caso, actualice la lista de copias de seguridad para verificar que la copia de seguridad sigue siendo válida. Si la copia de seguridad es válida y se restaura desde la nube, el problema puede deberse a errores en la conectividad.

Para completar correctamente la operación de restauración, desconecte el volumen en el host y vuelva a intentar la operación de restauración. Tenga en cuenta que las modificaciones realizadas en los datos del volumen durante el proceso de restauración se perderán.

## Pasos siguientes

- Obtenga información sobre cómo [Administrar volúmenes de StorSimple](storsimple-manage-volumes-u2.md).

- Obtenga información sobre cómo [usar el servicio del administrador de StorSimple para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0204_2016-->