<properties 
   pageTitle="Reemplazar un disco duro en un dispositivo StorSimple | Microsoft Azure"
   description="Explica cómo reemplazar una unidad de disco en un dispositivo principal de StorSimple o un gabinete EBOD."
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
   ms.date="12/02/2015"
   ms.author="alkohli" />

# Reemplazar un disco duro en el dispositivo StorSimple

## Información general

Este tutorial explica cómo quitar y reemplazar una unidad de disco duro que no funciona correctamente o defectuosa en un dispositivo StorSimple de Microsoft Azure. Para reemplazar una unidad de disco, necesitará:

- Desactivar el bloqueo antisabotaje

- Quitar la unidad de disco

- Instalar la unidad de disco de reemplazo

>[AZURE.IMPORTANT] Antes de quitar y reemplazar una unidad de disco, revise la información de seguridad en [Reemplazo de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

## Desactivar el bloqueo antisabotaje

Este procedimiento explica cómo pueden activarse o desactivarse los bloqueos antisabotaje del dispositivo StorSimple al reemplazar las unidades de disco. Los bloqueos antisabotaje se instalan en las asas transportadoras de la unidad, y se accede a ellos a través de un pequeño orificio en la sección del pestillo del asa. Las unidades se suministran con los bloqueos establecidos en la posición bloqueada.

#### Para desbloquear el bloqueo antisabotaje

1. Inserte cuidadosamente la clave de bloqueo (un destornillador T10 "inviolable" proporcionado por Microsoft) en el orificio del asa y en la toma. 

    >[AZURE.NOTE] Si el bloqueo antisabotaje está activado, el indicador rojo está visible en el orificio.

    ![Unidad de disco bloqueada](./media/storsimple-disk-drive-replacement/IC741056.png)

    **Figura 1** Bloqueo antisabotaje activado

    |Etiqueta|Descripción|
    |:----|:----------|
    |1|Orificio indicador|
    |2|Bloqueo antisabotaje|

2. Gire la clave hacia la derecha hasta que el indicador rojo no esté visible en el orificio por encima de la clave.

3. Quite la llave.

    ![Unidad de disco desbloqueada](./media/storsimple-disk-drive-replacement/IC741057.png)

    **Figura 2** Unidad de disco desbloqueada

4. Ahora se puede quitar la unidad de disco.

Siga los pasos en orden inverso para activar el bloqueo.

## Quitar la unidad de disco

El dispositivo StorSimple es compatible con una configuración de espacios de almacenamiento similares a RAID 10. Esto implica que puede funcionar normalmente con un disco, unidad de estado sólido (SSD), o unidad de disco duro (HDD) defectuoso.

>[AZURE.IMPORTANT]
>
>- Si el sistema tiene más de un disco defectuoso, no quite más de un SSD o unidad de disco duro del sistema en un determinado momento. Esto puede provocar la pérdida de datos.
>
>- Asegúrese de colocar un SSD de reemplazo en una ranura que previamente contenía un SSD. Asegúrese de colocar un HDD de reemplazo en una ranura que previamente contenía un HDD.
>
>- En el Portal de Azure clásico, las ranuras se numeran de 0 a 11. Por lo tanto, si el portal muestra que ha fallado un disco en la ranura 2, en el dispositivo, busque el disco defectuoso en la tercera ranura de la parte superior izquierda.

Las unidades de disco se pueden quitar y reemplazar mientras el sistema está en funcionamiento.

#### Para quitar una unidad

1. Para identificar el disco defectuoso, en el Portal de Azure clásico, vaya a **Dispositivos** > **Mantenimiento** > **Estado del hardware**. Dado que se puede producir un error en un disco en el gabinete principal o en el gabinete EBOD (si está utilizando un modelo 8600), examine el estado de los discos en **Componentes compartidos** y en **Componentes compartidos del gabinete EBOD**. Un disco defectuoso en cualquier gabinete se mostrará con un estado rojo.

2. Busque las unidades en la parte frontal del gabinete principal o del gabinete EBOD. Para el disco defectuoso, el LED ámbar estará encendido.

3. Si el disco está desbloqueado, continúe con el paso siguiente. Si el disco está bloqueado, desbloquéelo siguiendo el procedimiento descrito en [Desactivar el bloqueo antisabotaje](#disengage-the-antitamper-lock).

4. Presione el pestillo negro en el módulo transportador de unidades y tire del asa transportadora de la unidad hacia afuera y lejos de la parte frontal del chasis.

    ![Liberar el asa de la unidad de disco](./media/storsimple-disk-drive-replacement/IC741051.png)

    **Figura 3** Liberar el asa de la unidad

5. Cuando el asa transportadora de la unidad está totalmente extendida, deslice el transportador de la unidad fuera del chasis.

    ![Deslizamiento del disco fuera de la unidad de disco](./media/storsimple-disk-drive-replacement/IC741052.png)
    
    **Figura 4** Deslizamiento de la unidad de disco fuera del transportador

## Instalar la unidad de disco de reemplazo

Después de que se ha producido un error en la unidad de su dispositivo StorSimple de Microsoft Azure y que la haya quitado, siga este procedimiento para reemplazarla con una nueva unidad.

#### Para insertar una unidad

1. Asegúrese de que el asa transportadora de la unidad esté totalmente extendida, como se muestra en la siguiente imagen.

    ![Unidad de disco con asa extendida](./media/storsimple-disk-drive-replacement/IC741044.png)

    **Figura 5** Unidad con asa extendida

2. Deslice el transportador de la unidad por completo hacia el chasis.

    ![Deslizamiento del disco hacia el transportador de la unidad de disco](./media/storsimple-disk-drive-replacement/IC741045.png)

    **Figura 6** Deslizamiento del transportador de la unidad hacia el chasis

3. Con el transportador de la unidad insertado, cierre el asa transportadora de la unidad mientras continúa empujando el transportador de la unidad hacia el chasis, hasta que el asa transportadora de la unidad encaje en una posición de bloqueo.

4. Utilice la llave de bloqueo proporcionada por Microsoft (destornillador Torx inviolable) para fijar el asa transportadora en su lugar girando el destornillador de bloqueo un cuarto de vuelta hacia la derecha.

5. Compruebe que el reemplazo se realizó correctamente y que la unidad funcione. Para ello, acceda al Portal de Azure clásico y vaya a **Mantenimiento** > **Estado del hardware**. En **Componentes compartidos** o **Componentes compartidos del gabinete EBOD**, el estado de la unidad debe ser verde, que indica que está en buenas condiciones.

    >[AZURE.NOTE] Puede tardar varias horas para que el estado del disco cambie a verde, después de la sustitución.

## Pasos siguientes

Obtenga más información sobre el [Reemplazo de los componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

<!---HONumber=AcomDC_0224_2016-->