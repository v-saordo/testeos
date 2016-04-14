<properties
   pageTitle="Clonación del volumen de StorSimple | Microsoft Azure"
   description="Describe los diferentes tipos de clon y cuándo usarlos, y explica cómo se puede usar un conjunto de copia de seguridad para clonar un volumen individual."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/14/2015"
   ms.author="alkohli" />

# Usar el servicio StorSimple Manager para clonar un volumen

[AZURE.INCLUDE [storsimple-version-selector-clone-volume](../../includes/storsimple-version-selector-clone-volume.md)]

## Información general

En la página **Catálogo de copias de seguridad** del servicio StorSimple Manager se muestran todos los conjuntos de copia de seguridad que se crean cuando se realizan copias de seguridad manuales o automatizadas. Puede usar esta página para enumerar todas las copias de seguridad para un volumen o una directiva de copia de seguridad, seleccionar o eliminar las copias de seguridad, o usar una copia de seguridad para restaurar o clonar un volumen.

![Página del catálogo de copias de seguridad](./media/storsimple-clone-volume/HCS_BackupCatalog.png)

Este tutorial describe cómo puede usar una copia de seguridad para clonar un volumen individual. También explica la diferencia entre clones *transitorios* y *permanente*.

## Crear un clon de un volumen

Puede crear un clon en el mismo dispositivo, en otro dispositivo o incluso en una máquina virtual mediante el uso de una instantánea local o en la nube.

#### Para clonar un volumen

1. En la página del servicio de Administrador de StorSimple, haga clic en la pestaña **Catálogo de copia de seguridad** y seleccione un conjunto de copia de seguridad.

2. Expanda el conjunto de copias de seguridad para ver los volúmenes asociados. Haga clic y seleccione un volumen del conjunto de copia de seguridad.

     ![Clonar un volumen](./media/storsimple-clone-volume/HCS_Clone.png)

3. Haga clic en **Clonar** para empezar a clonar el volumen seleccionado.

4. En el Asistente para clonar volúmenes, en **Especificar el nombre y la ubicación**:

  1. Identifique un dispositivo de destino. Esta es la ubicación donde se creará el clon. Puede elegir el mismo dispositivo o especificar otro dispositivo. Si elige un volumen asociado a otros proveedores de servicios en la nube (no Azure), la lista desplegable del dispositivo de destino solo mostrará dispositivos físicos. No se puede clonar un volumen asociado a otros proveedores de servicios en la nube en un dispositivo virtual.

        >  [AZURE.NOTE] Asegúrese de que la capacidad necesaria para el clon es inferior a la capacidad disponible en el dispositivo de destino. 
  2. Especifique un nombre de volumen único para el clon. El nombre debe tener entre 3 y 127 caracteres.
  3. Haga clic en el icono con forma de flecha ![icono de flecha](./media/storsimple-clone-volume/HCS_ArrowIcon.png) para ir a la página siguiente.

5. En **Especificar los hosts que pueden usar este volumen**:

  1. Especifique un registro de control de acceso (ACR) para el clon. Puede agregar un ACR nuevo o elegir uno de la lista.
  2. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-clone-volume/HCS_CheckIcon.png) para completar la operación.

6. Se iniciará un trabajo de clonación y se le notificará cuando se haya creado correctamente el clon. Haga clic en **Ver trabajo** para supervisar el trabajo de clonación en la página **Trabajos**.

7. Una vez completado el trabajo de clonación:

  1. Vaya a la página **Dispositivos** y seleccione la pestaña **Contenedores de volúmenes**.
  2. Seleccione el contenedor de volúmenes que está asociado con el volumen de origen que clonó. En la lista de volúmenes, debería ver el clon recién creado.

>[AZURE.NOTE] La copia de seguridad predeterminada y la supervisión se deshabilitan automáticamente en un volumen clonado.

Los clones que se creen de esta forma son clones transitorios. Para obtener más información acerca de los tipos de clon, consulte [Clones transitorios frente a clones permanentes](#transient-vs.-permanent-clones).

Este clon es ahora un volumen normal, y todas las operaciones que sean posibles en un volumen estarán disponibles para el clon. Deberá configurar el volumen para las copias de seguridad.

## Clones transitorios frente a clones permanentes

Se puede clonar un volumen específico de un conjunto de copia de seguridad. Los clones que se creen de esta forma son clones *transitorios*. El clon transitorio tendrá referencias al volumen original y usará ese volumen para leer mientras escribe de forma local. Esto podría conllevar un rendimiento lento, especialmente si el volumen clonado es grande.

Después de tomar una instantánea en la nube de un clon transitorio, el clon resultante será un clon *permanente*. El clon permanente es independiente y no tiene ninguna referencia al volumen original desde el que se clonó. Para agilizar el rendimiento, se recomienda crear clones permanentes.

## Escenarios para clones transitorios y permanentes

Las secciones siguientes describen situaciones de ejemplo en las que pueden usarse clones transitorios y permanentes.

### Recuperación de nivel de elemento con un clon transitorio

Necesita recuperar un archivo de presentación de Microsoft PowerPoint de un año de antigüedad. El administrador de TI identifica la copia de seguridad específica de ese período de tiempo y, a continuación, filtra el volumen. El administrador clona el volumen, busca el archivo que necesita y se lo proporciona. En este escenario, se usa un clon transitorio.
 
![Vídeo disponible](./media/storsimple-clone-volume/Video_icon.png) **Vídeo disponible**

Para ver un vídeo en el que se muestra cómo puede usar el clon y restaurar las características de StorSimple para recuperar archivos eliminados, haga clic [aquí](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

### Pruebas en el entorno de producción con un clon permanente

Necesita comprobar un error de prueba en el entorno de producción. Crear un clon del volumen en el entorno de producción. Para aumentar el rendimiento, deberá tomar una instantánea en la nube de este clon. El volumen clonado es ahora independiente, lo que conlleva un rendimiento más rápido. En este escenario, se usa un clon permanente.

## Pasos siguientes
- Obtenga información sobre cómo [restaurar un volumen de StorSimple de un conjunto de copia de seguridad](storsimple-restore-from-backup-set.md).

- Obtenga información sobre cómo [usar el servicio StorSimple Manager para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).

 

<!---HONumber=AcomDC_0224_2016-->