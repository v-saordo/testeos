<properties
   pageTitle="Administración de volúmenes de StorSimple | Microsoft Azure"
   description="Explica cómo agregar, modificar, supervisar y eliminar volúmenes de StorSimple y cómo desconectarlos en caso necesario."
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
   ms.date="01/15/2016"
   ms.author="v-sharos" />

# Usar el servicio de Administrador de StorSimple para administrar volúmenes

[AZURE.INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## Información general

Este tutorial explica cómo usar el servicio de Administrador de StorSimple para crear y administrar volúmenes en el dispositivo StorSimple y en el dispositivo virtual de StorSimple.

El servicio de Administrador de StorSimple es una extensión del Portal de Azure clásico que le permite administrar la solución de StorSimple desde una interfaz web única. Además de administrar volúmenes, puede usar el servicio de Administrador de StorSimple para crear y administrar servicios de StorSimple, ver y administrar dispositivos, ver alertas, y ver y administrar las directivas de copia de seguridad y el catálogo de copias de seguridad.

> [AZURE.NOTE] Azure StorSimple solo puede crear volúmenes con aprovisionamiento fino. No es posible crear volúmenes total o parcialmente aprovisionados en un sistema de Azure StorSimple.
>
> El aprovisionamiento fino es una tecnología de virtualización en que el almacenamiento disponible parece superar los recursos físicos. En lugar de reservar almacenamiento suficiente por adelantado, Azure StorSimple usa el aprovisionamiento fino para asignar solo el espacio suficiente para cumplir con los requisitos actuales. La naturaleza elástica del almacenamiento en la nube facilita este enfoque porque Azure StorSimple puede aumentar o disminuir el almacenamiento en la nube para cumplir con las exigencias cambiantes.

## La página Volúmenes

La página **Volúmenes** permite administrar los volúmenes de almacenamiento que se aprovisionaron en el dispositivo de Microsoft Azure StorSimple para los iniciadores (servidores). Muestra la lista de volúmenes del dispositivo StorSimple.

 ![Página de volúmenes](./media/storsimple-manage-volumes/HCS_VolumesPage.png)

Un volumen se compone de una serie de atributos:

- **Nombre**: nombre descriptivo que debe ser único y que ayuda a identificar el volumen. Este nombre también se usa en los informes de supervisión cuando se filtra por un volumen específico.

- **Estado**: puede estar conectado o desconectado. Si un volumen está desconectado, no es visible para los iniciadores (servidores) que tienen acceso para usar el volumen.

- **Capacidad**: especifica el tamaño del volumen, tal como lo percibe el iniciador (servidor). La capacidad especifica la cantidad total de datos que puede almacenar el iniciador (servidor). Los volúmenes tienen aprovisionamiento fino y los datos están desduplicados. Esto implica que el dispositivo ya no asigna previamente la capacidad de almacenamiento físico de forma interna o en la nube en función de la capacidad configurada del volumen. La capacidad del volumen se asigna y se consume a petición.

- **Acceso**: especifica los iniciadores (servidores) que pueden tener acceso a este volumen. Los iniciadores que no son miembros del registro de control de acceso (ACR) asociado al volumen no podrán ver el volumen.

- **Supervisión**: especifica si se está supervisando un volumen. Un volumen tendrá la supervisión habilitada de forma predeterminada cuando se crea. Sin embargo, la supervisión estará deshabilitada para un clon del volumen. Para habilitar la supervisión de un volumen, siga las instrucciones indicadas en Supervisar un volumen.

Las tareas más comunes asociadas a un volumen son:

- Agregar un volumen 
- Modificar un volumen 
- Eliminar un volumen 
- Desconectar un volumen 
- Supervisar un volumen 

## Agregar un volumen

Ya [creó un volumen](storsimple-deployment-walkthrough-u1.md#step-6-create-a-volume) durante la implementación de la solución de StorSimple. El procedimiento para agregar un volumen es similar.

### Para agregar un volumen

1. En la página **Dispositivos**, seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volúmenes**.

2. Seleccione un contenedor de volúmenes y haga clic en la flecha situada en la fila correspondiente para acceder a los volúmenes asociados al contenedor.

3. Haga clic en **Agregar** en la parte inferior de la página. Se iniciará el Asistente para agregar volúmenes.

     ![Agregar configuración básica del asistente de volumen](./media/storsimple-manage-volumes/AddVolume1.png)

4. En el Asistente para agregar volúmenes, en **Configuración básica**, haga lo siguiente:

  1. Proporcione un **Nombre** para el volumen.
  2. Especifique la **Capacidad aprovisionada** para el volumen en GB o TB. La capacidad debe estar entre 1 y 64 TB para un dispositivo físico. La capacidad máxima que se puede aprovisionar para un volumen en un dispositivo virtual de StorSimple es de 30 TB.
  3. En la lista desplegable, seleccione el **Tipo de uso** para el volumen. Si está usando este volumen para datos de archivo, active la casilla **Usar este volumen para los datos de archivo a los que accede con menos frecuencia**. Para los demás casos de uso, seleccione simplemente **Volumen por niveles**. (Los volúmenes en niveles se denominaban anteriormente volúmenes primarios).
  5. Haga clic en el icono de flecha ![Icono de flecha](./media/storsimple-manage-volumes/HCS_ArrowIcon.png) para ir a la página **Configuración adicional**.

        ![Add Volume wizard Additional Settings](./media/storsimple-manage-volumes/AddVolume2.png)
   
5. En **Configuración adicional**, agregue un nuevo registro de control de acceso (ACR):
  
  1. Seleccione un registro de control de acceso (ACR) de la lista desplegable. También puede agregar un nuevo ACR. Los ACR determinan qué hosts pueden acceder a los volúmenes haciendo coincidir el IQN del host con el que aparece en el registro.
  2. Le recomendamos que, para habilitar una copia de seguridad predeterminada, active la casilla **Habilitar una copia de seguridad predeterminada para este volumen**.
   3. Haga clic en el icono de marca de verificación ![Icono de marca de verificación](./media/storsimple-manage-volumes/HCS_CheckIcon.png) para crear el volumen con la configuración especificada.

El nuevo volumen ya está listo para usarse.

## Modificar un volumen

Modifique un volumen cuando necesite expandirlo o cambiar los hosts que tienen acceso al volumen.

> [AZURE.IMPORTANT] 
>
> - Si modifica el tamaño del volumen en el dispositivo, también deberá cambiar el tamaño del volumen en el host. 
> - Los pasos del lado host que se describen aquí son para Windows Server 2012 (2012R2). Los procedimientos para Linux u otros sistemas operativos de host serán diferentes. Consulte las instrucciones del sistema operativo del host cuando modifique el volumen del host que esté ejecutando otro sistema operativo. 

### Para modificar un volumen

1. En la página **Dispositivos**, seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedor de volúmenes**. Esta página muestra en un formato tabular todos los contenedores de volúmenes que están asociados al dispositivo.

2. Seleccione un contenedor de volúmenes y haga clic en él para mostrar la lista de todos los volúmenes dentro del contenedor.

3. En la página **Volúmenes**, seleccione un volumen y haga clic en **Modificar**.

4. En el Asistente para modificar volúmenes, en **Configuración básica**, puede hacer lo siguiente:

  - Editar el **Nombre** y el **Tipo de aplicación**.
  - Aumentar la **Capacidad aprovisionada**. La **Capacidad aprovisionada** solo puede aumentarse. No se puede reducir un volumen después de crearlo.

    > [AZURE.NOTE] No se puede cambiar el contenedor de volúmenes después de asignarlo a un volumen.

5. En **Configuración adicional**, puede hacer lo siguiente:

  - Modificar los ACR, siempre que el volumen esté desconectado. Si el volumen está conectado, primero deberá desconectarlo. Consulte los pasos indicados en [Desconectar un volumen](#take-a-volume-offline) antes de modificar el ACR.
  - Modificar la lista de ACR después de desconectar el volumen.
 
    > [AZURE.NOTE] En cuanto al volumen, no puede cambiar la opción **Habilitar una copia de seguridad predeterminada para este volumen**.

6. Guarde los cambios haciendo clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-manage-volumes/HCS_CheckIcon.png). El Portal de Azure clásico mostrará un mensaje de actualización del volumen. Presentará en la pantalla un mensaje de confirmación cuando el volumen se haya actualizado correctamente.

7. Si se va a expandir un volumen, realice los pasos siguientes en el equipo host Windows:

   1. Vaya a **Administración de equipos** -> **Administración de discos**.
   2. Haga clic con el botón derecho en **Administración de discos** y seleccione **Volver a examinar los discos**.
   3. En la lista de discos, seleccione el volumen que actualizó, haga clic en él con el botón derecho y seleccione **Extender volumen**. Se iniciará el Asistente para extender volúmenes. Haga clic en **Siguiente**.
   4. Complete el asistente aceptando los valores predeterminados que se proporcionan. Cuando el asistente haya finalizado, el volumen debería mostrar el aumento de tamaño.

![Vídeo disponible](./media/storsimple-manage-volumes/Video_icon.png) **Vídeo disponible**

Para ver un vídeo que muestra cómo expandir un volumen, haga clic [aquí](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## Desconectar un volumen

Puede que necesite desconectar un volumen si tiene previsto modificarlo o eliminarlo. Cuando un volumen está desconectado, no está disponible para el acceso de lectura y escritura. Deberá desconectar el volumen en el host, así como en el dispositivo. Realice los pasos siguientes para desconectar un volumen.

### Para desconectar un volumen

1. Asegúrese de que el volumen en cuestión no está en uso antes de desconectarlo.

2. Desconecte el volumen primero en el host. Esto elimina cualquier riesgo potencial de dañar los datos del volumen. Para conocer los pasos específicos, consulte las instrucciones del sistema operativo del host.

3. Una vez desconectado el host, desconecte el volumen en el dispositivo siguiendo estos pasos:

  1. En la página **Dispositivos**, seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volúmenes**. La pestaña **Contenedores de volúmenes** muestra en formato tabular todos los contenedores de volúmenes que están asociados al dispositivo.
  2. Seleccione un contenedor de volúmenes y haga clic en él para mostrar la lista de todos los volúmenes dentro del contenedor.
  3. Seleccione un volumen y haga clic en **Desconectar**.
  4. Cuando se le pida confirmación, haga clic en **Sí**. El volumen debería desconectarse.

    Una vez desconectado un volumen, pasa a estar disponible la opción **Conectar**.

> [AZURE.NOTE] El comando **Desconectar** envía una solicitud al dispositivo para desconectar el volumen. Si los hosts todavía están usando el volumen las conexiones se interrumpirán, pero la desconexión del volumen no producirá un error.

## Eliminar un volumen

> [AZURE.IMPORTANT] Puede eliminar un volumen solo si está desconectado.

Siga estos pasos para eliminar un volumen.

### Para eliminar un volumen

1. En la página **Dispositivos**, seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volúmenes**.

2. Seleccione el contenedor de volúmenes que tiene el volumen que desea eliminar. Haga clic en el contenedor de volúmenes para acceder a la página **Volúmenes**.

3. Todos los volúmenes asociados a este contenedor se muestran en formato tabular. Compruebe el estado del volumen que desea eliminar. Si el volumen que desea eliminar no está desconectado, desconéctelo primero siguiendo los pasos indicados en [Desconectar un volumen](#take-a-volume-offline).

4. Una vez desconectado el volumen, haga clic en **Eliminar** en la parte inferior de la página.

5. Cuando se le pida confirmación, haga clic en **Sí**. El volumen se eliminará y la página **Volúmenes** mostrará la lista actualizada de volúmenes dentro del contenedor.

## Supervisar un volumen

La supervisión de volúmenes permite recopilar estadísticas relacionados con la E/S de un volumen. La supervisión está habilitada de forma predeterminada para los 32 primeros volúmenes que cree. La supervisión de volúmenes adicionales está deshabilitada de forma predeterminada. La supervisión de volúmenes clonados también está deshabilitada de forma predeterminada.

Siga estos pasos para habilitar o deshabilitar la supervisión de un volumen.

### Para habilitar o deshabilitar la supervisión de volúmenes

1. En la página **Dispositivos**, seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volúmenes**.

2. Seleccione el contenedor de volúmenes en el que reside el volumen y, a continuación, haga clic en el contenedor de volúmenes para acceder a la página **Volúmenes**.

3. Todos los volúmenes asociados a este contenedor se muestran en presentación tabular. Haga clic para seleccionar el volumen o el clon del volumen.

4. En la parte inferior de la página, haga clic en **Modificar**.

5. En el Asistente para modificar volúmenes, en **Configuración básica**, seleccione **Habilitar** o **Deshabilitar** en la lista desplegable de **Supervisión**.

    ![Modificar la configuración básica de un volumen](./media/storsimple-manage-volumes/HCS_MonitorVolumeM.png)


## Pasos siguientes

- Aprenda cómo [clonar un volumen de StorSimple](storsimple-clone-volume.md).

- Obtenga información sobre cómo [usar el servicio StorSimple Manager para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).

 

<!---HONumber=AcomDC_0128_2016-->