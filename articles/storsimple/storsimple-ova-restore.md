<properties
   pageTitle="Restaurar desde una copia de seguridad de la matriz virtual de StorSimple"
   description="Obtenga más información sobre cómo restaurar una copia de seguridad de la matriz virtual de StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/01/2016"
   ms.author="alkohli"/>

# Restaurar desde una copia de seguridad de la matriz virtual de StorSimple

## Información general 

Este artículo se aplica a la matiz virtual de Microsoft Azure StorSimple (también conocida como dispositivo virtual local de StorSimple o dispositivo virtual de StorSimple) que se ejecuta en la versión de disponibilidad general de marzo de 2016. Asimismo, en este artículo se describe paso a paso cómo realizar una restauración a partir de un conjunto de copias de seguridad de los recursos compartidos o de los volúmenes de la matriz virtual de StorSimple. El artículo también describe cómo funciona la recuperación a nivel de elemento en la matriz virtual de StorSimple que se haya configurado como un servidor de archivos.


## Restaurar recursos compartidos a partir de un conjunto de copias de seguridad


Antes de intentar restaurar los recursos compartidos, asegúrese de que tiene suficiente espacio en el dispositivo para completar esta operación. Siga estos pasos en el [Portal de Azure clásico](https://manage.windowsazure.com/) para realizar una restauración a partir de una copia de seguridad.

#### Restaurar un recurso compartido

1.  Vaya al **catálogo de copias de seguridad**. Filtre las opciones para encontrar el dispositivo y el intervalo de tiempo adecuados para buscar las copias de seguridad. Haga clic en el icono de marca de verificación ![](./media/storsimple-ova-restore/image1.png) para ejecutar esta consulta.


1.  En la lista de conjuntos de copias de seguridad que se muestre, haga clic y seleccione una copia de seguridad específica. Expanda la copia de seguridad para ver los distintos recursos compartidos que posee. Haga clic y seleccione el recurso compartido que desee restaurar.

2.  En la parte inferior de la página, haga clic en **Restaurar como nuevo**.

3.  Con esto, iniciará el asistente para **restaurar como un recurso compartido nuevo**. En la página **Especificar nombre y ubicación**:


	1.  Compruebe el nombre del dispositivo de origen. Este debe ser el dispositivo que contiene el recurso compartido que quiera restaurar. La selección del dispositivo está atenuada. Para seleccionar un dispositivo de origen diferente, debe salir del asistente y volver a seleccionar la copia de seguridad de nuevo.

	2.  Proporcione un nombre de recurso compartido. El nombre del recurso compartido debe contener de 3 a 127 caracteres.

	3.  Revise el tamaño, el tipo y los permisos asociados con el recurso compartido que está intentando restaurar. Podrá modificar las propiedades del recurso compartido mediante el Explorador de Windows una vez finalizada la restauración.

	4.  Haga clic en el icono de marca de verificación ![](./media/storsimple-ova-restore/image1.png).

		![](./media/storsimple-ova-restore/image9.png)

1.  Una vez completado el trabajo de restauración, la restauración se iniciará y aparecerá otra notificación. Para supervisar el progreso de la restauración, haga clic en **Ver trabajo**. Esto le llevará a la página **Trabajos**.

2.  Puede supervisar el progreso del trabajo de restauración. Cuando la restauración se haya completado al 100 %, vaya a la página **Recursos compartidos** del dispositivo.

3.  En la lista de recursos compartidos del dispositivo podrá ver el recurso compartido recién restaurado. Asimismo, verá que al realizar la restauración el tipo del recurso compartido sigue siendo el mismo. Esto es, un recurso compartido en niveles se restaurará “en niveles” y un recurso compartido anclado localmente se restaurará como “anclado localmente”.

Ya ha completado la configuración del dispositivo y aprendió a realizar copias de seguridad o a restaurar un recurso compartido.


## Restaurar volúmenes a partir de un conjunto de copias de seguridad


Siga estos pasos en el Portal de Azure clásico para realizar una restauración a partir de una copia de seguridad. La operación de restauración consiste en restaurar la copia de seguridad en un nuevo volumen del mismo dispositivo virtual; no se puede restaurar a un dispositivo diferente.

#### Para restaurar un volumen

1.  Vaya al **catálogo de copias de seguridad**. Filtre las opciones para encontrar el dispositivo y el intervalo de tiempo adecuados para buscar las copias de seguridad. Haga clic en el icono de marca de verificación ![](./media/storsimple-ova-restore/image1.png) para ejecutar esta consulta.

2.  En la lista de conjuntos de copias de seguridad que se muestre, haga clic y seleccione una copia de seguridad específica. Expanda la copia de seguridad para ver los distintos volúmenes que posee. Debe desconectar estos volúmenes del host y del dispositivo para que pueda restaurarlos. Acceda a los volúmenes de la página **Volúmenes** y desconéctelos.

3.  Vuelva a la pestaña **Catálogo de copias de seguridad** y seleccione un conjunto de copias de seguridad.

5.  En la parte inferior de la página, haga clic en **Restaurar como nuevo**. Se iniciará el asistente para **restaurar como nuevo volumen**.

1.  En la página **Especificar nombre y ubicación**:


	1.  Compruebe el nombre del dispositivo de origen. Este debe ser el dispositivo que contiene el volumen que quiera restaurar. La selección del dispositivo no está disponible. Para seleccionar un dispositivo de origen diferente, debe salir del asistente y volver a seleccionar la copia de seguridad de nuevo.

	2.  Proporcione un nombre de volumen. El nombre del volumen debe contener de 3 a 127 caracteres.

	3.  Haga clic en el icono con forma de flecha.

		![](./media/storsimple-ova-restore/image12.png)

1.  En la página **Especificar hosts que pueden usar este volumen**, seleccione los ACR adecuados en la lista desplegable.

	![](./media/storsimple-ova-restore/image13.png)

1.  Haga clic en el icono de marca de verificación ![](./media/storsimple-ova-restore/image1.png). Se iniciará un trabajo de restauración y verá la siguiente notificación indicándole que el trabajo está en curso.

2.  Una vez completado el trabajo de restauración, la restauración se iniciará y aparecerá otra notificación. Para supervisar el progreso de la restauración, haga clic en **Ver trabajo**. Esto le llevará a la página **Trabajos**.

3.  Puede supervisar el progreso del trabajo de restauración. Cuando la restauración se haya completado al 100 %, vuelva a la página **Volúmenes** del dispositivo.

4.  En la lista de volúmenes del dispositivo podrá ver el volumen recién restaurado. Asimismo, verá que, al realizar la restauración, el tipo de volumen sigue siendo el mismo. Esto es, un volumen en niveles se restaurará “en niveles” y un volumen anclado localmente se restaurará como “anclado localmente”.

> [AZURE.IMPORTANT] Si se produce un error en el trabajo de restauración al intentar restaurar un volumen o un recurso compartido desde un conjunto de copia de seguridad, todavía se puede crear en el portal un recurso compartido o un volumen de destino. Es importante eliminar este recurso compartido o este volumen de destino del portal para minimizar los problemas que deriven de este elemento en el futuro.

## Recuperación a nivel de elemento (ILR)

Esta versión incluye la recuperación a nivel de elemento (ILR) en un dispositivo virtual de StorSimple configurado como un servidor de archivos. Gracias a esta característica, podrá realizar una recuperación pormenorizada de los archivos y carpetas mediante una copia de seguridad en la nube de todos los recursos compartidos en el dispositivo StorSimple. Asimismo, los usuarios pueden recuperar archivos eliminados de copias de seguridad recientes mediante un modelo de autoservicio.

Cada recurso compartido tiene una carpeta con formato *.backups* que contiene las copias de seguridad más recientes. El usuario puede ir a la copia de seguridad que desee, copiar los archivos y carpetas pertinentes de la copia de seguridad y restaurarlas. Gracias a ello, no es necesario llamar a los administradores para restaurar archivos a través de copias de seguridad.

1.  Al realizar la ILR, puede ver las copias de seguridad a través del Explorador de Windows. Haga clic en el recurso compartido específico que quiera ver en la copia de seguridad. Verá una carpeta con formato *.backups* creada en el recurso compartido que almacena todas las copias de seguridad. Expanda la carpeta con formato *.backups* para ver las copias de seguridad. A continuación, verá en la carpeta la vista seccionada de toda la jerarquía de copias de seguridad. Normalmente, esta vista se crea a petición y en tan solo un par de segundos.

	De esta manera, se muestran las últimas 5 copias de seguridad. Esto incluye tanto las copias de seguridad programadas de forma predeterminada y las manuales.

	
	-   **Copias de seguridad programadas** denominadas &lt;nombreDe Dispositivo&gt; DailySchedule-AAAAMMDD-HHMMSS-UTC.

	-   **Copias de seguridad manuales** denominadas Ad-hoc-AAAAMMDD-HHMMSS-UTC.
	
		![](./media/storsimple-ova-restore/image14.png)

1.  Identifique la copia de seguridad que contenga la versión más reciente del archivo eliminado. Aunque el nombre de la carpeta contiene una marca de tiempo UTC en cada uno de los casos anteriores, la hora en que se creó la carpeta es la hora real del dispositivo cuando comenzó la copia de seguridad. Use la marca de tiempo de la carpeta para localizar e identificar las copias de seguridad.

2.  Busque la carpeta o el archivo que quiera restaurar en la copia de seguridad que identificó en el paso anterior. Tenga en cuenta que solo podrá ver los archivos o carpetas para los que tenga permiso. Si no puede obtener acceso a determinados archivos o carpetas, debe ponerse en contacto con un administrador de recursos compartidos que pueda usar el Explorador de Windows para editar los permisos de recursos compartidos y así proporcionarle acceso al archivo o carpeta en cuestión. Es recomendable que el administrador de recursos compartidos sea un grupo de usuarios en lugar de un solo usuario.

3.  Copie el archivo o la carpeta en el recurso compartido adecuado del servidor de archivos de StorSimple.

![video\_icon](./media/storsimple-ova-restore/video_icon.png) **Vídeo disponible**

Consulte este vídeo para ver cómo puede crear recursos compartidos, realizar copias de seguridad de los recursos compartidos y restaurar datos en una matriz virtual de StorSimple.

> [AZURE.VIDEO use-the-storsimple-virtual-array]

## Pasos siguientes

Obtenga más información acerca de cómo [administrar la matriz virtual de StorSimple mediante la interfaz de usuario web local](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0302_2016-->