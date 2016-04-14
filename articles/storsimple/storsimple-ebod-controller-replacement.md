<properties 
   pageTitle="Reemplazar un controlador de EBOD de StorSimple | Microsoft Azure"
   description="Explica cómo quitar y reemplazar uno o ambos controladores EBOD en un dispositivo de StorSimple 8600."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/02/2015"
   ms.author="alkohli" />

# Reemplazar un controlador EBOD en el dispositivo StorSimple

## Información general

Este tutorial explica cómo reemplazar un módulo de controladores EBOD defectuoso en el dispositivo StorSimple de Microsoft Azure. Para reemplazar un módulo de controladores EBOD, necesitará:

- Quitar el controlador EBOD defectuoso
- Instalar un nuevo controlador EBOD

Antes de comenzar, tenga en cuenta la siguiente información:

- Los módulos EBOD vacíos deben insertarse en todas las ranuras sin usar. El alojamiento no se refrigerará correctamente si una ranura queda abierta.

- El controlador EBOD es intercambiable en caliente y puede quitarse o reemplazarse. No quite un módulo defectuoso hasta que tenga un reemplazo. Cuando se inicia el proceso de reemplazo, debe finalizarlo en 10 minutos.

>[AZURE.IMPORTANT]Antes de quitar y reemplazar un controlador EBOD, revise la información de seguridad en [Reemplazo de componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

## Quitar un controlador EBOD

Antes de reemplazar el módulo de controladores EBOD defectuoso en el dispositivo StorSimple, asegúrese de que el módulo de controladores EBOD esté activo y en funcionamiento. El procedimiento y la tabla siguientes explican cómo quitar el módulo de controladores EBOD.

#### Para quitar un módulo EBOD

1. Abra el Portal de Azure clásico.

2. Vaya a **Dispositivos** > **Mantenimiento** > **Estado del hardware** y compruebe que el estado del LED para el módulo de controladores EBOD es verde y el LED del módulo de controladores EBOD defectuoso es rojo.

3. Busque el módulo de controladores EBOD defectuoso en la parte posterior del dispositivo.

4. Quite los cables que conectan el módulo de controladores EBOD al controlador antes de extraer el módulo EBOD del sistema.

5. Tome nota del puerto SAS exacto del módulo de controladores EBOD que estaba conectado al controlador. Deberá restaurar el sistema a esta configuración después de reemplazar el módulo EBOD.

    >[AZURE.NOTE]Normalmente, se tratará del Puerto A, que está etiquetada como **Alojar en** en el diagrama siguiente.

    ![Plano anterior del controlador EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)

     **Figura 1** Parte posterior del módulo EBOD

    |Etiqueta|Descripción|
    |:----|:----------|
    |1|LED de error|
    |2|LED de encendido|
    |3|Conectores SAS|
    |4|LED SAS|
    |5|Puertos en serie para uso en fábrica únicamente|
    |6|Puerto A (Alojar en)|
    |7|Puerto B (Alojar en)|
    |8|Puerto C (solo para uso de fábrica)|

## Instalar un nuevo controlador EBOD

El procedimiento y la tabla siguientes explican cómo instalar el módulo de controladores EBOD en el dispositivo StorSimple.

#### Para instalar un controlador EBOD

1. Compruebe el dispositivo EBOD para ver si hay daños, especialmente en el conector de la interfaz. No instale el nuevo controlador EBOD si alguna de las clavijas está doblada.

2. Con los pestillos en la posición abierta, deslice el módulo hacia el gabinete hasta que los pestillos encajen.

    ![Instalación del controlador EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)

    **Figura 2** Instalación del módulo de controladores EBOD

3. Cierre el pestillo. Oirá un clic cuando el pestillo encaje.

    ![Liberación del pestillo EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)

    **Figura 3** Cierre del pestillo del módulo EBOD

4. Vuelva a conectar los cables. Utilice la configuración exacta que estaba presente antes del reemplazo. Vea la tabla y el diagrama siguientes para obtener detalles acerca de cómo conectar los cables.

    ![Cableado del dispositivo 4U para alimentación](./media/storsimple-ebod-controller-replacement/IC770723.png)

    **Figura 4** Reconexión de los cables

    |Etiqueta|Descripción|
    |:----|:----------|
    |1|Receptáculo principal|
    |2|PCM 0|
    |3|PCM 1|
    |4|Controlador 0|
    |5|Controlador 1|
    |6|Controlador EBOD 0|
    |7|Controlador EBOD 1|
    |8|Receptáculo EBOD|
    |9|PDU|

## Pasos siguientes

Obtenga más información sobre el [Reemplazo de los componentes de hardware de StorSimple](storsimple-hardware-component-replacement.md).

<!---HONumber=AcomDC_1203_2015-->