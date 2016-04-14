<properties 
   pageTitle="Administración de directivas de copia de seguridad de StorSimple | Microsoft Azure"
   description="Explica cómo se puede usar el servicio Administrador de StorSimple para crear y administrar copias de seguridad manuales, programaciones de copia de seguridad y retención de copia de seguridad."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/15/2016"
   ms.author="v-sharos"/>

# Usar el servicio de Administrador de StorSimple para administrar directivas de copia de seguridad (Update 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## Información general

Este tutorial explica cómo utilizar la página **Directivas de copia de seguridad** del Servicio de Administrador de StorSimple para controlar los procesos de copia de seguridad y la retención de copias de seguridad de los volúmenes de StorSimple. También describe cómo realizar copias de seguridad manuales.

Cuando realice una copia de un volumen, puede elegir crear una instantánea local o una instantánea de nube. Si está realizando una copia de seguridad de un volumen anclado localmente, se recomienda que especifique una instantánea de nube. La realización de un gran número de instantáneas acopladas locales junto con un conjunto de datos que tenga una gran cantidad actividad dará como resultado una situación en la que podría quedarse rápidamente sin espacio local. Si elige realizar instantáneas locales, se recomienda que realice menos instantáneas diarias para realizar una copia de seguridad del estado más reciente, conservarlas durante un día y eliminarlas.

Al realizar una instantánea en la nube de un volumen anclado localmente, se copian solo los datos modificados en la nube, donde se desduplican y se comprimen.

## Página Directivas de copia de seguridad

La página **Directivas de copia de seguridad** permite administrar las directivas de copia de seguridad y programar instantáneas locales y en la nube. (Las directivas de copia de seguridad se usan para configurar las programaciones de copia de seguridad y la retención de copias de seguridad de una colección de volúmenes). Las directivas de copia de seguridad le permiten tomar una instantánea de varios volúmenes simultáneamente. Esto significa que las copias de seguridad creadas por una directiva de copia de seguridad serán copias preparadas para bloqueos. En la página **Directivas de copia de seguridad** se enumeran las directivas de copia de seguridad, sus tipos, los volúmenes asociados, el número de copias de seguridad retenidas y la opción para habilitar estas directivas.

La página **Directivas de copia de seguridad** también permite filtrar las directivas de copia de seguridad existentes por uno, o varios, de los siguientes campos:

- **Nombre de directiva**: el nombre asociado con la directiva. Los distintos tipos de directivas incluyen:

   - Directivas programadas, que las crea explícitamente el usuario.
   - Directivas automáticas, que se crean al habilitar la copia de seguridad predeterminada de esta opción de volumen en el momento en que se creó dicho volumen. Estas directivas se denominan *VolumeName*\_Default, donde *VolumeName* hace referencia al nombre del volumen de StorSimple que ha configurado el usuario en el Portal de Azure clásico. Las directivas automáticas generan instantáneas diarias en la nube a partir de las 22:30, hora del dispositivo.
   - Directivas importadas, que se crearon originalmente en el Administrador de instantáneas de StorSimple. Dichas directivas tienen una etiqueta que describe el host de Administrador de instantáneas StorSimple del que se importaron las directivas.

- **Volúmenes** : los volúmenes asociados a la directiva. Cuando se crean copias de seguridad se agrupan todos los volúmenes asociados a una directiva de copia de seguridad.

- **Última copia de seguridad correcta**: la fecha y hora de la última copia de seguridad correcta que se ha realizado con esta directiva.

- **Siguiente copia de seguridad**: la fecha y hora de la siguiente copia de seguridad programada que iniciará esta directiva.

- **Programaciones** : el número de programaciones asociadas a la directiva de copia de seguridad.

Estas son las operaciones utilizadas con frecuencia que se pueden realizar desde esta página:

- Incorporación de una directiva de copia de seguridad 
- Incorporación o modificación de una programación 
- Eliminación de una directiva de copia de seguridad 
- Creación de una copia de seguridad manual 
- Creación de una directiva de copia de seguridad personalizada con varios volúmenes y programaciones 

## Incorporación de una directiva de copia de seguridad

Agregue una directiva de copia de seguridad para programar automáticamente sus copias de seguridad. Realice los pasos siguientes en el Portal de Azure clásico para agregar una directiva de copia de seguridad para el dispositivo StorSimple. Después de agregar la directiva, puede definir una programación (vea [Incorporación o modificación de una programación](#add-or-modify-a-schedule)).

[AZURE.INCLUDE [storsimple-add-backup-policy-u2](../../includes/storsimple-add-backup-policy-u2.md)]

![Vídeo disponible](./media/storsimple-manage-backup-policies-u2/Video_icon.png) **Vídeo disponible**

Para ver un vídeo en el que se muestra cómo crear una directiva de copia de seguridad local o en la nube, haga clic [aquí](https://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/).


## Incorporación o modificación de una programación

Es posible agregar o modificar una programación adjunta a una directiva de copia de seguridad de un dispositivo StorSimple. Siga estos pasos en el Portal de Azure clásico para agregar o modificar una programación.

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule-u2.md)]

## Eliminación de una directiva de copia de seguridad

Realice los pasos siguientes en el Portal de Azure clásico para eliminar una directiva de copia de seguridad del dispositivo StorSimple.

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## Creación de una copia de seguridad manual

Siga estos pasos en el Portal de Azure clásico para crear una copia de seguridad a petición (manual) de un único volumen.

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## Creación de una directiva de copia de seguridad personalizada con varios volúmenes y programaciones

Realice los pasos siguientes en el Portal de Azure clásico para crear una directiva de copia de seguridad personalizada que tenga varios volúmenes y programaciones.

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy-u2.md)]


## Pasos siguientes

Obtenga más información sobre el [uso del servicio StorSimple Manager para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0128_2016-->