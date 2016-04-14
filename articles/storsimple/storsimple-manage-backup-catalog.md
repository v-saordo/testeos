<properties 
   pageTitle="Administración del catálogo de copias de seguridad de StorSimple | Microsoft Azure"
   description="Explica cómo usar la página del catálogo de copias de seguridad del servicio de Administrador de StorSimple para enumerar, seleccionar y eliminar conjuntos de copias de seguridad de un volumen."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/30/2015"
   ms.author="v-sharos" />

# Usar el servicio de Administrador de StorSimple para administrar su catálogo de copia de seguridad

## Información general

En la página **Catálogo de copias de seguridad** del servicio StorSimple Manager se muestran todos los conjuntos de copia de seguridad que se crean cuando se realizan copias de seguridad manuales o programadas. Puede usar esta página para enumerar todas las copias de seguridad para un volumen o una directiva de copia de seguridad, seleccionar o eliminar las copias de seguridad, o usar una copia de seguridad para restaurar o clonar un volumen.

En este tutorial se explica cómo enumerar, seleccionar y eliminar un conjunto de copia de seguridad. Para obtener información sobre cómo restaurar su dispositivo de copia de seguridad, vaya a [Restaurar el dispositivo desde un conjunto de copia de seguridad](storsimple-restore-from-backup-set.md). Para aprender cómo clonar un volumen, vaya a [Clonar un volumen de StorSimple](storsimple-clone-volume.md).

![Catálogo de copias de seguridad](./media/storsimple-manage-backup-catalog/HCS_BackupCatalog.png)

La página **Catálogo de copias de seguridad** proporciona una consulta para restringir la selección de conjuntos de copias de seguridad. Puede filtrar los conjuntos de copias de seguridad que se recuperan en función de los parámetros siguientes:

- **Dispositivo**: dispositivo en el que se creó el conjunto de copias de seguridad.

- **Directiva de copia de seguridad o Volumen**: directiva de copia de seguridad o volumen asociado a este conjunto de copia de seguridad.

- **De y A**: el intervalo de fecha y hora en el que se creó el conjunto de copia de seguridad.

A continuación, los conjuntos de copias de seguridad filtrados se presentan en forma de tabla en función de los siguientes atributos:

- **Nombre**: nombre de la directiva de copias de seguridad o del volumen asociado al conjunto de copias de seguridad.

- **Tamaño**: tamaño real del conjunto de copias de seguridad.

- **Creada en**: Fecha y hora en que se crearon las copias de seguridad.

- **Tipo**: los conjuntos de copias de seguridad pueden ser instantáneas locales o instantáneas en la nube. Una instantánea local es una copia de seguridad de todos los datos del volumen que se almacenan localmente en el dispositivo, mientras que una instantánea en la nube hace referencia a la copia de seguridad de los datos del volumen que residen en la nube. Las instantáneas locales proporcionan un acceso más rápido, mientras que las instantáneas en la nube son preferibles para la resistencia de los datos.

- **Iniciada por**: las copias de seguridad se pueden iniciar automáticamente por una programación o manualmente por el usuario. Puede usar una directiva de copia de seguridad para programar copias de seguridad. Como alternativa, puede usar la opción **Realizar copia de seguridad** para crear una copia de seguridad manual.

## Mostrar conjuntos de copia de seguridad para un volumen
 
Complete los pasos siguientes para enumerar todas las copias de seguridad para un volumen.

#### Para enumerar los conjuntos de copia de seguridad

1. En la página del servicio de Administrador de StorSimple, haga clic en la pestaña **Catálogo de copias de seguridad**.

2. Filtre las selecciones de la siguiente manera:

    1. Seleccione el dispositivo adecuado.

    2. En la lista desplegable, elija un volumen para ver las copias de seguridad correspondientes.

    3. Especifique el intervalo de tiempo.

    4. Haga clic en el icono de marca de verificación ![Icono de marca de verificación](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) para ejecutar esta consulta.
 
    Las copias de seguridad asociadas al volumen seleccionado deben aparecer en la lista de conjuntos de copias de seguridad.

## Seleccionar un conjunto de copia de seguridad

Complete los pasos siguientes para seleccionar un conjunto de copias de seguridad para un volumen o una directiva de copia de seguridad.

#### Para seleccionar un conjunto de copia de seguridad

1. En la página del servicio de Administrador de StorSimple, haga clic en la pestaña **Catálogo de copias de seguridad**.

2. Filtre las selecciones de la siguiente manera:

    1. Seleccione el dispositivo adecuado.

    2. En la lista desplegable, seleccione el volumen o la directiva de copia de seguridad para la copia de seguridad que desea seleccionar.

    3. Especifique el intervalo de tiempo.

    4. Haga clic en el icono de marca de verificación ![Icono de marca de verificación](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) para ejecutar esta consulta.

    Las copias de seguridad asociadas al volumen o la directiva de copia de seguridad seleccionados deben aparecer en la lista de conjuntos de copias de seguridad.

3. Selecciona y expanda un conjunto de copia de seguridad. Las opciones **Restaurar** y **Eliminar** aparecen en la parte inferior de la página. Puede realizar cualquiera de estas acciones en el conjunto de copia de seguridad que haya seleccionado.

## Eliminación de un conjunto de copia de seguridad

Elimine una copia de seguridad cuando ya no desee conservar los datos asociados a ella. Realice los siguientes pasos para eliminar un conjunto de copia de seguridad.

#### Para eliminar un conjunto de copia de seguridad

1. En la página del servicio de Administrador de StorSimple, haga clic en la pestaña **Catálogo de copias de seguridad**.

2. Filtre las selecciones de la siguiente manera:

    1. Seleccione el dispositivo adecuado.

    2. En la lista desplegable, seleccione el volumen o la directiva de copia de seguridad para la copia de seguridad que desea seleccionar.

    3. Especifique el intervalo de tiempo.

    4. Haga clic en el icono de marca de verificación ![Icono de marca de verificación](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) para ejecutar esta consulta.

    Las copias de seguridad asociadas al volumen o la directiva de copia de seguridad seleccionados deben aparecer en la lista de conjuntos de copias de seguridad.

3. Selecciona y expanda un conjunto de copia de seguridad. Las opciones **Restaurar** y **Eliminar** aparecen en la parte inferior de la página. Hacer clic en **Eliminar**.

4. Se le notificará cuando la eliminación esté en curso y cuando haya finalizado correctamente. Cuando finalice la eliminación, actualice la consulta en esta página. El conjunto de copia de seguridad eliminado ya no aparecerá en la lista de conjuntos de copia de seguridad.

## Pasos siguientes

- Obtenga información sobre cómo usar el catálogo de copias de seguridad para [ restaurar el dispositivo desde un conjunto de copias de seguridad](storsimple-restore-from-backup-set.md).

- Obtenga información sobre cómo [usar el servicio StorSimple Manager para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0107_2016-->