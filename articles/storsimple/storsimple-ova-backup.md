<properties 
   pageTitle="Tutorial de copia de seguridad de la matriz virtual de StorSimple | Microsoft Azure"
   description="Describe cómo realizar copias de seguridad de recursos compartidos y volúmenes de la matriz virtual de StorSimple."
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
   ms.date="03/01/2016"
   ms.author="alkohli" />

# Crear una copia de seguridad de la matriz virtual de StorSimple

## Información general 

Este tutorial se aplica a la matriz virtual de Microsoft Azure StorSimple (también conocida como dispositivo virtual local de StorSimple o dispositivo virtual de StorSimple) que se ejecuta en la versión de disponibilidad general de marzo de 2016.

La matriz virtual de StorSimple es un dispositivo virtual local de almacenamiento en nube híbrida que se puede configurar como servidor de archivos o servidor iSCSI. Puede crear copias de seguridad, restaurar copias de seguridad y realizar la conmutación por error de dispositivos si se necesita recuperación ante desastres. Cuando se configura como servidor de archivos, también permite la recuperación a nivel de elemento. En este tutorial se describe cómo usar el portal clásico de Azure o la interfaz de usuario web de StorSimple para crear copias de seguridad programadas y manuales de la matriz virtual de StorSimple.


## Hacer copias de seguridad de recursos compartidos y volúmenes

Las copias de seguridad proporcionan seguridad a partir de un momento específico y mejoran la capacidad de recuperación, al mismo tiempo que reducen los tiempos de restauración de recursos compartidos y volúmenes. Puede hacer una copia de seguridad de un recurso compartido o de un volumen de su dispositivo StorSimple de dos maneras: **Programada** o **Manual**. En las siguientes secciones se detallan cada uno de los métodos.

> [AZURE.NOTE] En esta versión, se crean copias de seguridad programadas mediante una directiva predeterminada que se ejecuta diariamente a la hora especificada y hace una copia de seguridad de todos los recursos compartidos o volúmenes en el dispositivo. No es posible crear directivas personalizadas para copias de seguridad programadas en este momento.

## Establecer la programación de copias de seguridad

El dispositivo virtual StorSimple tiene una directiva de copia de seguridad predeterminada que se inicia a una hora determinada del día (22:30) y hace una copia de seguridad de todos los recursos compartidos o volúmenes en el dispositivo una vez al día. Puede cambiar la hora a la que se inicia la copia de seguridad, pero no puede cambiar la frecuencia y la retención (que especifica el número de copias de seguridad para conservar). Durante estas copias de seguridad, se copia todo el dispositivo virtual; por lo tanto, recomendamos que programe estas copias de seguridad durante horas de poca actividad.

Siga estos pasos en el [Portal de Azure clásico](https://manage.windowsazure.com/) para cambiar la hora de inicio predeterminada de las copias de seguridad.

#### Para cambiar la hora de inicio para la directiva de copia de seguridad predeterminada

1. Vaya a la pestaña **Configuración** del dispositivo.

2. En la sección **Copia de seguridad**, especifique la hora de inicio de la copia de seguridad diaria.

3. Haga clic en **Guardar**.

### Creación de una copia de seguridad manual

Además de las copias de seguridad programadas, puede crear una copia de seguridad manual (a petición) en cualquier momento.

#### Para crear una copia de seguridad manual (a petición)

1. Vaya a la pestaña **Recursos compartidos** o la pestaña **Volúmenes**.

2. En la parte inferior de la página, haga clic en **Realizar copia de seguridad general**. Se le pedirá que verifique que desea realizar la copia de seguridad ahora. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-ova-backup/image3.png) para continuar con la copia de seguridad.

    ![confirmación de copia de seguridad](./media/storsimple-ova-backup/image4.png)

    Se le notificará que se está iniciando un trabajo de copia de seguridad.

    ![inicio de la copia de seguridad](./media/storsimple-ova-backup/image5.png)

    Recibirá una notificación cuando el trabajo se cree correctamente.

    ![trabajo de copia de seguridad creado](./media/storsimple-ova-backup/image7.png)

3. Para realizar un seguimiento del progreso del trabajo, haga clic en el icono **Ver trabajo**.

4. Una vez finalizado el trabajo de copia de seguridad, vaya a la pestaña **Catálogo de copia de seguridad**. Debería ver la copia de seguridad completada.

    ![Copia de seguridad completada](./media/storsimple-ova-backup/image8.png)

5. Establezca las selecciones de filtro en el dispositivo, la directiva de copia de seguridad y el intervalo de tiempo adecuados y, a continuación, haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-ova-backup/image3.png).

    La copia de seguridad debe aparecer en la lista de conjuntos de copia de seguridad que se muestra en el catálogo.

## Ver copias de seguridad existentes

Siga estos pasos en el Portal de Azure clásico para ver las copias de seguridad existentes.

#### Para ver copias de seguridad existentes

1. En la página del servicio de Administrador de StorSimple, haga clic en la pestaña **Catálogo de copias de seguridad**.

2. Seleccione una copia de seguridad de la siguiente manera:

    1. Seleccione el dispositivo.

    2. En la lista desplegable, seleccione el recurso compartido o el volumen para la copia de seguridad que desea seleccionar.

    3. Especifique el intervalo de tiempo.

    4. Haga clic en el icono de marca de verificación ![](./media/storsimple-ova-backup/image3.png) para ejecutar esta consulta.

    Las copias de seguridad asociadas al recurso compartido o al volumen seleccionado deben aparecer en la lista de conjuntos de copias de seguridad.

![video\_icon](./media/storsimple-ova-backup/video_icon.png) **Vídeo disponible**

Consulte este vídeo para ver cómo puede crear recursos compartidos, realizar copias de seguridad de los recursos compartidos y restaurar datos en una matriz virtual de StorSimple.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## Pasos siguientes

Más información sobre la [administración de la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0302_2016-->