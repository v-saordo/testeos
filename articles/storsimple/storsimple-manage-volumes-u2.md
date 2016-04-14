<properties
   pageTitle="Administración de volúmenes de StorSimple (U2) | Microsoft Azure"
   description="Explica cómo agregar, modificar, supervisar y eliminar volúmenes de StorSimple y cómo desconectarlos en caso necesario."
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
   ms.workload="NA"
   ms.date="02/25/2016"
   ms.author="v-sharos" />

# Usar el servicio de Administrador de StorSimple para administrar volúmenes (Update 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## Información general

En este tutorial se explica cómo usar el servicio StorSimple Manager para crear y administrar volúmenes en el dispositivo StorSimple y en el dispositivo virtual StorSimple con una instancia de Update 2 instalada.

El servicio de Administrador de StorSimple es una extensión del Portal de Azure clásico que le permite administrar la solución de StorSimple desde una interfaz web única. Además de administrar volúmenes, puede usar el servicio de Administrador de StorSimple para crear y administrar servicios de StorSimple, ver y administrar dispositivos, ver alertas, y ver y administrar las directivas de copia de seguridad y el catálogo de copias de seguridad.

## Tipos de volúmenes

Los volúmenes de StorSimple pueden ser:

- **Volúmenes anclados localmente**: los datos de estos volúmenes se mantiene en el dispositivo StorSimple local en todo momento.
- **Volúmenes en capas**: los datos de estos volúmenes pueden escribirse en la nube.

Un volumen de archivo es un tipo de volumen en capas. El tamaño del fragmento de desduplicación más grande utilizado para los volúmenes de archivo permite que el dispositivo transfiera a la nube segmentos más grandes de datos.

Si es necesario, puede cambiar un volumen local por uno en capas y viceversa. Para más información, consulte [Cambiar el tipo de volumen](#change-the-volume-type).

### Volúmenes anclados localmente

Los volúmenes anclados localmente son volúmenes totalmente aprovisionados, sin capas de datos en la nube, lo que garantiza la conectividad local de los datos principales independiente de la nube. Los datos de los volúmenes anclados localmente no se desduplican ni comprimen, pero las instantáneas de dichos volúmenes se desduplican.

Los volúmenes anclados localmente están totalmente aprovisionados, por lo que debe haber espacio suficiente en el dispositivo cuando los crea. Puede aprovisionar volúmenes anclados localmente hasta un tamaño máximo de 9 TB en el dispositivo StorSimple 8100 y de 24 TB en el dispositivo 8600. StorSimple reserva el resto del espacio local en el dispositivo para las instantáneas, los metadatos y el procesamiento de los datos. Puede aumentar el tamaño de un volumen anclado localmente hasta el espacio máximo disponible, pero no puede reducir el tamaño de un volumen una vez creado.

Cuando se crea un volumen anclado localmente, el espacio disponible para la creación de los volúmenes en capas se reduce. Y al contrario, si tiene volúmenes en capas existentes, el espacio disponible para crear volúmenes anclados localmente será inferior a los límites máximos indicados anteriormente.

### Volúmenes en capas

Los volúmenes en capas son volúmenes con aprovisionamiento reducido en los que los datos a los que se accede con frecuencia permanecen locales en el dispositivo y los datos de uso menos frecuente se apilan automáticamente en la nube. El aprovisionamiento fino es una tecnología de virtualización en que el almacenamiento disponible parece superar los recursos físicos. En lugar de reservar almacenamiento suficiente por adelantado, StorSimple utiliza el aprovisionamiento fino para asignar solo el espacio suficiente para cumplir con los requisitos actuales. La naturaleza elástica del almacenamiento en la nube facilita este enfoque porque StorSimple puede aumentar o disminuir el almacenamiento en la nube para cumplir con las exigencias cambiantes.

Si usa el volumen en capas para los datos de archivo, seleccione la casilla de verificación **Usar este volumen para los datos de archivo a los que accede con menos frecuencia** cambia el tamaño del fragmento de desduplicación del volumen a 512 KB. Si no selecciona esta opción, el volumen correspondiente en capas usará un tamaño del fragmento de 64 KB. Un mayor tamaño de fragmento de desduplicación permite que el dispositivo acelere la transferencia de datos de archivado de gran tamaño a la nube.

>[AZURE.NOTE] Los volúmenes de archivo creados con una versión anterior a Update 2 de StorSimple se importarán como volúmenes en capas con la casilla de archivo seleccionada.

### Capacidad aprovisionada

Para conocer la capacidad máxima aprovisionada de cada dispositivo y tipo de volumen, consulte la siguiente tabla. (Tenga en cuenta que los volúmenes anclados localmente no están disponibles en un dispositivo virtual).

| | Tamaño máximo del volumen en capas | Tamaño máximo del volumen anclado localmente |
|-------------|----------------------------|------------------------------------|
| **Dispositivos físicos** | | |
| 8100 | 64 TB | 9 TB |
| 8600 | 64 TB | 24 TB |
| **Dispositivos virtuales** | | |
| 8010 | 30 TB | N/D |
| 8020 | 64 TB | N/D | 

## La página Volúmenes

La página **Volúmenes** permite administrar los volúmenes de almacenamiento que se aprovisionaron en el dispositivo de Microsoft Azure StorSimple para los iniciadores (servidores). Muestra la lista de volúmenes del dispositivo StorSimple.

 ![Página de volúmenes](./media/storsimple-manage-volumes-u2/VolumePage.png)

Un volumen se compone de una serie de atributos:

- **Nombre**: nombre descriptivo que debe ser único y que ayuda a identificar el volumen. Este nombre también se usa en los informes de supervisión cuando se filtra por un volumen específico.

- **Estado**: puede estar conectado o desconectado. Si un volumen está desconectado, no es visible para los iniciadores (servidores) que tienen acceso para usar el volumen.

- **Capacidad**: especifica la cantidad total de datos que puede almacenar el iniciador (servidor). Los volúmenes anclados localmente están completamente aprovisionados y residen en el dispositivo StorSimple. Los volúmenes en capas tienen aprovisionamiento reducido y los datos están desduplicados. Esto implica que el dispositivo ya no asigna previamente la capacidad física de almacenamiento de forma interna o en la nube en función de la capacidad configurada del volumen. La capacidad del volumen se asigna y se consume a petición.

- **Tipo**: indica si el tipo del volumen es **En capas** (predeterminado) o **Anclado localmente**.

- **Copia de seguridad**: indica si existe una directiva de copia de seguridad predeterminada para el volumen.

- **Acceso**: especifica los iniciadores (servidores) que pueden tener acceso a este volumen. Los iniciadores que no son miembros del registro de control de acceso (ACR) asociado al volumen no podrán ver el volumen.

- **Supervisión**: especifica si se está supervisando un volumen. Un volumen tendrá la supervisión habilitada de forma predeterminada cuando se crea. Sin embargo, la supervisión estará deshabilitada para un clon del volumen. Para habilitar la supervisión de un volumen, siga las instrucciones indicadas en [Supervisar un volumen](#monitor-a-volume).

Siga las instrucciones de este tutorial para realizar las siguientes tareas:

- Agregar un volumen 
- Modificar un volumen 
- Cambiar el tipo de volumen
- Eliminar un volumen 
- Desconectar un volumen 
- Supervisar un volumen 

## Agregar un volumen

Ya [creó un volumen](storsimple-deployment-walkthrough-u2.md#step-6-create-a-volume) durante la implementación de la solución de StorSimple. El procedimiento para agregar un volumen es similar.

#### Para agregar un volumen

1. En la página **Dispositivos**, seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volúmenes**.

2. Seleccione un contenedor de volúmenes de la lista y haga doble clic en él para tener acceso a los volúmenes asociados al contenedor.

3. Haga clic en **Agregar** en la parte inferior de la página. Se iniciará el Asistente para agregar volúmenes.

     ![Agregar configuración básica del asistente de volumen](./media/storsimple-manage-volumes-u2/TieredVolEx.png)

4. En el Asistente para agregar volúmenes, en **Configuración básica**, haga lo siguiente:

  1. Proporcione un **Nombre** para el volumen.
  2. Seleccione un **Tipo de uso** en la lista desplegable. Para cargas de trabajo que requieren que los datos estén disponibles localmente en el dispositivo en todo momento, seleccione **Anclado localmente**. Para todos los demás tipos de datos, seleccione **En capas**. (**En capas** es el valor predeterminado).
  3. Si seleccionó **En capas** en el paso 2, puede activar la casilla **Usar este volumen para los datos de archivo a los que accede con menos frecuencia** para configurar un volumen de archivo.
  4. Especifique un valor para **Capacidad aprovisionada** para el volumen en GB o TB. Para conocer los tamaños máximos de cada dispositivo y tipo de volumen, consulte [Capacidad aprovisionada](#provisioned-capacity). Para determinar cuánto almacenamiento hay realmente disponible en el dispositivo, consulte **Capacidad disponible**.

5. Haga clic en el icono de flecha![Icono de flecha](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png). Si va a configurar un volumen anclado localmente, verá el mensaje siguiente.

    ![Cambiar el mensaje de tipo de volumen](./media/storsimple-manage-volumes-u2/LocalVolEx.png)
   
5. Vuelva a hacer clic en el icono de flecha ![Icono de flecha](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png)para ir a la página **Configuración adicional**.

    ![Agregar configuración adicional del asistente de volumen](./media/storsimple-manage-volumes-u2/AddVolume2.png)<br>

6. En **Configuración adicional**, agregue un nuevo registro de control de acceso (ACR):
  
  1. Seleccione un registro de control de acceso (ACR) de la lista desplegable. También puede agregar un nuevo ACR. Los ACR determinan qué hosts pueden acceder a los volúmenes haciendo coincidir el IQN del host con el que aparece en el registro. Si no especifica un ACR, verá el siguiente mensaje.

        ![Specify ACR](./media/storsimple-manage-volumes-u2/SpecifyACR.png)

  2. Le recomendamos que active la casilla **Habilitar una copia de seguridad predeterminada para este volumen**.
  3. Haga clic en el icono de marca de verificación ![Icono de marca de verificación](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) para crear el volumen con la configuración especificada.

El nuevo volumen ya está listo para usarse.

>[AZURE.NOTE] Si crea un volumen anclado localmente e inmediatamente después crea otro, los trabajos de creación de volúmenes se ejecutan de manera secuencial. El primer trabajo de creación de volumen debe finalizar para que el siguiente pueda comenzar.

## Modificar un volumen

Modifique un volumen cuando necesite expandirlo o cambiar los hosts que tienen acceso al volumen.

> [AZURE.IMPORTANT] 
>
> - Si modifica el tamaño del volumen en el dispositivo, también deberá cambiar el tamaño del volumen en el host. 
> - Los pasos del lado host que se describen aquí son para Windows Server 2012 (2012R2). Los procedimientos para Linux u otros sistemas operativos de host serán diferentes. Consulte las instrucciones del sistema operativo del host cuando modifique el volumen del host que esté ejecutando otro sistema operativo. 

#### Para modificar un volumen

1. En la página **Dispositivos**, seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volúmenes**.

2. Seleccione un contenedor de volúmenes de la lista y haga doble clic en él para ver los volúmenes asociados.

3. Seleccione un volumen y, en la parte inferior de la página, haga clic en **Modificar**. Se inicia el Asistente para modificar volúmenes.

4. En el Asistente para modificar volúmenes, en **Configuración básica**, puede hacer lo siguiente:

  - Edite el valor de **Nombre**.
  - Convierta **Tipo de uso** de anclado localmente a en capas o viceversa (consulte [Cambiar el tipo de volumen](#change-the-volume-type) para más información).
  - Aumentar la **Capacidad aprovisionada**. La **Capacidad aprovisionada** solo puede aumentarse. No se puede reducir un volumen después de crearlo.

5. En **Configuración adicional**, puede modificar el ACR, siempre que el volumen esté sin conexión. Si el volumen está conectado, primero deberá desconectarlo. Consulte los pasos indicados en [Desconectar un volumen](#take-a-volume-offline) antes de modificar el ACR.

    > [AZURE.NOTE] No se puede cambiar la opción **Habilitar una copia de seguridad predeterminada** para el volumen.

6. Guarde los cambios haciendo clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png). El Portal de Azure clásico mostrará un mensaje de actualización del volumen. Presentará en la pantalla un mensaje de confirmación cuando el volumen se haya actualizado correctamente.

7. Si se va a expandir un volumen, realice los pasos siguientes en el equipo host Windows:

   1. Vaya a **Administración de equipos** -> **Administración de discos**.
   2. Haga clic con el botón derecho en **Administración de discos** y seleccione **Volver a examinar los discos**.
   3. En la lista de discos, seleccione el volumen que actualizó, haga clic en él con el botón derecho y seleccione **Extender volumen**. Se iniciará el Asistente para extender volúmenes. Haga clic en **Siguiente**.
   4. Complete el asistente aceptando los valores predeterminados que se proporcionan. Cuando el asistente haya finalizado, el volumen debería mostrar el aumento de tamaño.

    >[AZURE.NOTE] Si expande un volumen anclado localmente e inmediatamente después expande otro, los trabajos de expansión de volúmenes se ejecutan de manera secuencial. El primer trabajo de expansión de volumen debe finalizar para que el siguiente pueda comenzar.

![Vídeo disponible](./media/storsimple-manage-volumes-u2/Video_icon.png) **Vídeo disponible**

Para ver un vídeo que muestra cómo expandir un volumen, haga clic [aquí](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## Cambiar el tipo de volumen

Puede cambiar el tipo de volumen en capas por uno anclado localmente y viceversa. Sin embargo, esta conversión no debería tener lugar con frecuencia. Algunas de las razones para convertir un volumen en capas por uno volumen anclado localmente son:

- Garantías locales respecto a la disponibilidad de datos y el rendimiento.
- Eliminación de problemas de conectividad y latencias en la nube.

Normalmente, son volúmenes existentes de pequeño tamaño a los que quiere acceder con frecuencia. Un volumen anclado localmente está aprovisionado por completo cuando se crea. Si va a convertir un volumen en capas en uno anclado localmente, StorSimple comprueba que haya espacio suficiente en el dispositivo antes de iniciar la conversión. Si no tiene suficiente espacio, recibirá un error y se cancelará la operación.

> [AZURE.NOTE] Antes de comenzar la conversión de un volumen en capas en uno anclado localmente, asegúrese de tener en cuenta los requisitos de espacio de las otras cargas de trabajo.

Puede que desee cambiar un volumen anclado localmente por uno en capas si necesita espacio adicional para aprovisionar otros volúmenes. Al convertir el volumen anclado localmente en un volumen en capas, la capacidad disponible en el dispositivo aumenta en proporción al tamaño de la capacidad liberada. Si los problemas de conectividad impiden la conversión de un volumen local en un volumen en capas, el volumen local exhibirá propiedades de un volumen en capas hasta que finalice la conversión. Esto se debe a que algunos datos podrían haberse escrito en la nube. Estos datos escritos seguirán ocupando espacio local en el dispositivo que no se podrá liberar hasta que la operación se reinicie y finalice.

>[AZURE.NOTE] La conversión de un volumen puede tardar algún tiempo y una conversión no se puede cancelar una vez que se ha iniciado. El volumen permanece en línea durante la conversión y puede realizar copias de seguridad, pero no puede expandir ni restaurar el volumen mientras dura la operación.

La conversión de un volumen en capas en uno anclado localmente puede afectar de forma negativa al rendimiento del dispositivo. Además, los siguientes factores podrían aumentar el tiempo que se tarda en completar la conversión:

- No hay suficiente ancho de banda.
- El dispositivo está lleno y ya está escribiendo en la nube.
- No hay ninguna copia de seguridad actual.

Para minimizar los efectos de estos factores:

- Revise su directivas de límite de ancho de banda y asegúrese de que haya disponible un ancho de banda dedicado de 40 Mbps.
- Programe la conversión para las horas de poca actividad.
- Realice una copia de seguridad antes de comenzar la conversión.

Si va a convertir varios volúmenes (compatibles con diferentes cargas de trabajo), debe dar precedencia a la conversión de los volúmenes de mayor prioridad. Por ejemplo, debe convertir los volúmenes que hospedan máquinas virtuales o volúmenes con cargas de trabajo SQL antes que los volúmenes con cargas de trabajo de recursos compartidos de archivos.

#### Para cambiar el tipo de volumen

1. En la página **Dispositivos**, seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volúmenes**.

2. Seleccione un contenedor de volúmenes de la lista y haga doble clic en él para ver los volúmenes asociados.

3. Seleccione un volumen y, en la parte inferior de la página, haga clic en **Modificar**. Se inicia el Asistente para modificar volúmenes.

4. En la página **Configuración básica**, cambie el tipo de uso; para ello, seleccione el nuevo tipo en la lista desplegable **Tipo de uso**.

    - Si va a cambiar el tipo a **Anclado localmente**, StorSimple comprobará si hay capacidad suficiente.
    - Si va a cambiar el tipo a **En capas** y este volumen se va a usar para los datos de archivo, active la casilla **Usar este volumen para los datos de archivo a los que accede con menos frecuencia**.

        ![Casilla de verificación de archivo](./media/storsimple-manage-volumes-u2/ModifyTieredVolEx.png)

5. Haga clic en el icono de flecha ![Icono de flecha](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) para ir a la página **Configuración adicional**. Si configura un volumen anclado localmente, aparecerá el siguiente mensaje.

    ![Cambiar el mensaje de tipo de volumen](./media/storsimple-manage-volumes-u2/ModifyLocalVolEx.png)

6. Haga clic de nuevo en el icono de flecha ![icono de flecha](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) para continuar.

7. Haga clic en el icono de marca de verificación ![Icono de marca de verificación](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) para iniciar el proceso de conversión. El Portal de Azure mostrará un mensaje de actualización del volumen. Presentará en la pantalla un mensaje de confirmación cuando el volumen se haya actualizado correctamente.

## Desconectar un volumen

Puede que necesite desconectar un volumen si tiene previsto modificarlo o eliminarlo. Cuando un volumen está desconectado, no está disponible para el acceso de lectura y escritura. Deberá desconectar el volumen en el host, así como en el dispositivo.

#### Para desconectar un volumen

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

#### Para eliminar un volumen

1. En la página **Dispositivos**, seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volúmenes**.

2. Seleccione el contenedor de volúmenes que tiene el volumen que desea eliminar. Haga clic en el contenedor de volúmenes para acceder a la página **Volúmenes**.

3. Todos los volúmenes asociados a este contenedor se muestran en formato tabular. Compruebe el estado del volumen que desea eliminar. Si el volumen que desea eliminar no está desconectado, desconéctelo primero siguiendo los pasos indicados en [Desconectar un volumen](#take-a-volume-offline).

4. Una vez desconectado el volumen, haga clic en **Eliminar** en la parte inferior de la página.

5. Cuando se le pida confirmación, haga clic en **Sí**. El volumen se eliminará y la página **Volúmenes** mostrará la lista actualizada de volúmenes dentro del contenedor.

    >[AZURE.NOTE] Si elimina un volumen anclado localmente e inmediatamente después elimina otro, los trabajos de eliminación de volúmenes se ejecutan de manera secuencial. El primer trabajo de eliminación de volumen debe finalizar para que el siguiente pueda comenzar.
 
## Supervisar un volumen

La supervisión de volúmenes permite recopilar estadísticas relacionados con la E/S de un volumen. La supervisión está habilitada de forma predeterminada para los 32 primeros volúmenes que cree. La supervisión de volúmenes adicionales está deshabilitada de forma predeterminada. La supervisión de volúmenes clonados también está deshabilitada de forma predeterminada.

Siga estos pasos para habilitar o deshabilitar la supervisión de un volumen.

#### Para habilitar o deshabilitar la supervisión de volúmenes

1. En la página **Dispositivos**, seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volúmenes**.

2. Seleccione el contenedor de volúmenes en el que reside el volumen y, a continuación, haga clic en el contenedor de volúmenes para acceder a la página **Volúmenes**.

3. Todos los volúmenes asociados a este contenedor se muestran en presentación tabular. Haga clic para seleccionar el volumen o el clon del volumen.

4. En la parte inferior de la página, haga clic en **Modificar**.

5. En el Asistente para modificar volúmenes, en **Configuración básica**, seleccione **Habilitar** o **Deshabilitar** en la lista desplegable de **Supervisión**.

## Pasos siguientes

- Aprenda cómo [clonar un volumen de StorSimple](storsimple-clone-volume.md).

- Aprenda a [usar el servicio StorSimple Manager para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).

 

<!---HONumber=AcomDC_0302_2016-->