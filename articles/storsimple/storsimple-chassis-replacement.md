<properties 
   pageTitle="Reemplazar el chasis en un dispositivo StorSimple | Microsoft Azure"
   description="Describe cómo quitar y reemplazar el chasis del gabinete EBOD y del gabinete principal de StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/30/2015"
   ms.author="alkohli" />

# Reemplazar el chasis en el dispositivo StorSimple

## Información general

Este tutorial explica cómo quitar y reemplazar un chasis en un dispositivo de la serie 8000 de StorSimple. El modelo StorSimple 8100 es un dispositivo de un solo gabinete (un chasis), mientras que el 8600 es un dispositivo de dos gabinetes (dos chasis). Para un modelo 8600, hay potencialmente dos chasis que pueden producir un error en el dispositivo: el chasis para el gabinete principal o el chasis del gabinete EBOD.

En cualquier caso, el chasis de reemplazo que se distribuye por Microsoft estará vacío. No se incluirán Módulos de alimentación y refrigeración (PCM), módulos de controlador, unidades de disco de estado sólido (SSD), unidades de disco duro (HDD) o módulos EBOD.

>[AZURE.IMPORTANT]Antes de quitar y reemplazar el chasis, revise la información de seguridad en [Reemplazo de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

## Quitar el chasis

Realice los pasos siguientes para quitar el chasis en el dispositivo StorSimple.

#### Para quitar un chasis

1. Asegúrese de que el dispositivo StorSimple esté apagado y desconectado de todas las fuentes de alimentación.

2. Quite todos los cables de red y SAS, si corresponde.

3. Quite la unidad del bastidor.

4. Quite cada una de las unidades de disco y tenga en cuenta las ranuras de las que se quitan. Para obtener más información, consulte [Quitar la unidad de disco](storsimple-disk-drive-replacement.md#remove-the-disk-drive).

5. En el gabinete EBOD (si es el chasis que produjo un error), quite los módulos del controlador EBOD. Para obtener más información, consulte [Quitar un controlador EBOD](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller).

    En el gabinete principal (si es el chasis que produjo un error), quite los controladores y tenga en cuenta las ranuras de las que se quitaron. Para obtener más información, consulte [Quitar un controlador](storsimple-controller-replacement.md#remove-a-controller).

## Instalar el chasis

Realice los pasos siguientes para instalar el chasis en un dispositivo StorSimple de Microsoft Azure.

#### Para instalar un chasis

1. Monte el chasis en el bastidor. Para obtener más información, consulte [Montaje en bastidor de su dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) o [Montaje en bastidor de su dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).

2. Después de que el chasis está montado en el bastidor, instale los módulos de controlador en las mismas posiciones en las que estaban previamente instaladas.

3. Instale las unidades en las mismas posiciones y ranuras en las que estaban previamente instaladas.

    >[AZURE.NOTE]Se recomienda que primero instale las SSD en las ranuras y, luego, instale los discos duros.

2. Con el dispositivo montado en el bastidor y los componentes instalados, conecte el dispositivo a las fuentes de alimentación adecuadas y encienda el dispositivo. Para obtener más información, consulte [Cableado del dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) o [Cableado del dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## Pasos siguientes

Obtenga más información sobre el [Reemplazo de los componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

<!---HONumber=AcomDC_0107_2016-->