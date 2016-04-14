<properties 
   pageTitle="Visualización y administración de trabajos de StorSimple | Microsoft Azure"
   description="Describe la página Trabajos del servicio Administrador de StorSimple y cómo usarla para realizar un seguimiento de los trabajos de copia de seguridad programados, actuales y recientes."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/15/2016"
   ms.author="alkohli" />

# Usar el servicio de Administrador de StorSimple para ver y administrar trabajos de StorSimple

[AZURE.INCLUDE [storsimple-version-selector-manage-jobs](../../includes/storsimple-version-selector-manage-jobs.md)]

## Información general

En la página **Trabajos** se ofrece un único portal central para ver y administrar trabajos iniciados en dispositivos conectados a su servicio StorSimple Manager. Puede ver los trabajos programados, en ejecución, completados y con error de varios dispositivos. Los resultados se presentan en un formato tabular.

![Página de trabajos](./media/storsimple-manage-jobs/HCS_JobsPage.png)

Puede buscar rápidamente los trabajos que le interesan filtrando por campos como:

- **Estado**: los trabajos pueden estar en ejecución, programados, con errores, completados, cancelándose o cancelados.

- **Tipo**: los trabajos pueden crearse como resultado de una copia de seguridad programada o a petición (**Realizar copia de seguridad**), una clonación, una restauración de dispositivo o una operación de actualización.

- **Dispositivos**: los trabajos se inician en un determinado dispositivo conectado a su servicio.

- **Desde y Hasta**: los trabajos se pueden filtrar según el intervalo de fecha y hora.

A continuación, los trabajos filtrados se tabulan en función de los siguientes atributos:

- **Tipo**: copia de seguridad, clonación, restauración, conmutación por error o actualización.

- **Estado**: en ejecución, programado, con error, completado, cancelándose o cancelado.

- **Entidad**: los trabajos se pueden asociados a un volumen, una directiva de copia de seguridad o un dispositivo. Un trabajo de clonación se asocia a un volumen, mientras que un trabajo de copia de seguridad programado está asociado a una directiva de copia de seguridad. Se crea un trabajo de dispositivo como resultado de una recuperación ante desastres (DR) o una operación de restauración.

- **Dispositivo**: el nombre del dispositivo en el que se inició el trabajo.

- **Hora de inicio**: la hora a la que se inició el trabajo.

- **Progreso**: el porcentaje de finalización de un trabajo en ejecución. Para un trabajo completado, este debe equivaler siempre al 100%.

La lista de trabajos se actualiza cada 30 segundos.

Puede realizar las siguientes acciones relacionadas con el trabajo en esta página:

- Ver detalles del trabajo

- Cancelación de un trabajo

## Ver detalles del trabajo

Realice los pasos siguientes para ver los detalles de cualquier trabajo.

#### Para ver los detalles del trabajo

1. En la página **Trabajos**, para mostrar los trabajos en los que esté interesado, ejecute una consulta con los filtros adecuados. Puede buscar trabajos completados, en ejecución o cancelados.

2. Seleccione un trabajo.

3. En la parte inferior de la página, haga clic en **Detalles**.

4. En el cuadro de diálogo **Detalles del trabajo de copia de seguridad**, puede ver el estado, los detalles, así como las estadísticas de tiempo y de los datos.

## Cancelación de un trabajo

Realice los pasos siguientes para cancelar un trabajo en ejecución.

### Para cancelar un trabajo

1. En la página **Trabajos**, para mostrar los trabajos en curso que quiere cancelar, ejecute una consulta con los filtros adecuados.

1. Seleccione el trabajo.

1. En la parte inferior de la página, haga clic en **Cancelar**.

1. Cuando se le pida confirmación, haga clic en **Sí**.

Ahora se cancelará este trabajo.

## Pasos siguientes

- Obtenga información sobre cómo [administrar las directivas de copia de seguridad de StorSimple](storsimple-manage-backup-policies.md).

- Obtenga información sobre cómo [usar el servicio StorSimple Manager para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0121_2016-->