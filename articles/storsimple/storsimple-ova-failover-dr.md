<properties
   pageTitle="Recuperación ante desastres y conmutación por error de dispositivos para la matriz virtual de StorSimple"
   description="Obtenga más información sobre cómo conmutar por error la matriz virtual de StorSimple."
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

# Recuperación ante desastres y conmutación por error de dispositivos para la matriz virtual de StorSimple


## Información general

En este artículo se describe la recuperación ante desastres para la matriz virtual de Microsoft Azure StorSimple (también conocido como dispositivo virtual local StorSimple), incluidos los pasos detallados necesarios para conmutar por error a otro dispositivo virtual en caso de desastre. La conmutación por error permite migrar los datos desde un dispositivo de *origen* en el centro de datos a otro dispositivo de *destino* situado en la misma ubicación geográfica o en otra diferente. La conmutación por error del dispositivo es para todo el dispositivo. Durante la conmutación por error, los datos de la nube para el dispositivo de origen cambian la propiedad a la del dispositivo de destino.

La conmutación por error de un dispositivo se coordina a través de la característica de recuperación ante desastres y se inicia desde la página **Dispositivos**. Esta página recoge en formato de tabla todos los dispositivos de StorSimple conectados al servicio de Administrador de StorSimple. Para cada dispositivo, se muestran el nombre descriptivo, el estado, la capacidad aprovisionada y máxima, el tipo y el modelo.

![](./media/storsimple-ova-failover-dr/image16.png)

Este artículo se aplica únicamente a matrices virtuales de StorSimple. Para conmutar por error un dispositivo de la serie 8000, vaya a [Conmutación por error y recuperación ante desastres para el dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).


## ¿Qué es la recuperación ante desastres?

En un escenario de recuperación ante desastres, el dispositivo principal deja de funcionar. En esta situación, puede mover los datos en la nube asociados al dispositivo con error a otro dispositivo usando el dispositivo primario como *origen* y especificando otro dispositivo como *destino*. Este proceso se conoce como *conmutación por error*. Durante la conmutación por error, todos los volúmenes o los recursos compartidos del dispositivo de origen cambian la propiedad y se transfieren al dispositivo de destino. No se permite ningún filtrado de los datos.

La recuperación ante desastres se modela como una restauración total del dispositivo mediante la organización en capas basada en mapas térmico y el seguimiento. Un mapa térmico se define mediante la asignación de un valor término a los datos según los patrones de lectura y escritura. A continuación, este mapa térmico asigna los niveles de los fragmentos de datos de calor más bajo a la nube primero y mantiene los fragmentos de datos de mayor calor (más usados) en el nivel local. Durante una recuperación ante desastres, se utiliza el mapa térmico para restaurar y rehidratar los datos desde la nube. El dispositivo captura todos los volúmenes o recursos compartidos de la copia de seguridad más reciente (según se determine internamente) y realiza una restauración a partir de la copia de seguridad. El dispositivo organiza todo el proceso de recuperación ante desastres.


## Requisitos previos para la conmutación por error de un dispositivo


### Requisitos previos

Para la conmutación por error de cualquier dispositivo, deben cumplirse los siguientes requisitos previos:

- El dispositivo de origen debe estar en un estado **Desactivado**.

- El dispositivo de destino debe aparecer como **Activo** en el Portal de Azure clásico. Debe aprovisionar un dispositivo virtual de destino con una capacidad igual o mayor. A continuación, debe usar la interfaz de usuario web local para configurar y registrar correctamente el dispositivo virtual.

	> [AZURE.IMPORTANT]
	> 
	> No intente configurar el dispositivo virtual registrado a través del servicio haciendo clic en **completar configuración del dispositivo**. No debe realizarse ninguna configuración del dispositivo a través del servicio.

- El dispositivo de origen y de destino deben ser del mismo tipo. Solo puede conmutar por error un dispositivo virtual configurado como servidor de archivos a otro servidor de archivos. Lo mismo es cierto para un servidor iSCSI.

- Para la recuperación ante desastres de un servidor de archivos, se recomienda unir el dispositivo de destino al mismo dominio que el del origen para que los permisos de uso compartido se resuelvan automáticamente. En esta versión, se admite solo la conmutación por error en un dispositivo de destino del mismo dominio.

### Otras consideraciones

- Se recomienda que deje sin conexión todos los volúmenes o recursos compartidos en el dispositivo de origen.

- Si se trata de una conmutación por error planeada, se recomienda que realice una copia de seguridad del dispositivo y, a continuación, continúe con la conmutación por error para minimizar la pérdida de datos. Si es una conmutación por error no planeada, se utilizará la copia de seguridad más reciente para restaurar el dispositivo.

- Los dispositivos de destino disponibles para la recuperación ante desastres son dispositivos que tienen una capacidad igual o superior en comparación con el dispositivo de origen. Los dispositivos que están conectados al servicio pero que no cumplen los criterios de espacio suficiente no estarán disponibles como dispositivos de destino.

### Comprobaciones previas para la recuperación ante desastres

Antes de comenzar la recuperación ante desastres, se realizan comprobaciones previas en el dispositivo. Estas comprobaciones ayudan a garantizar que no se producirán errores cuando comience la recuperación ante desastres. Las comprobaciones previas incluyen:

- Validar una cuenta de almacenamiento

- Comprobar la conectividad de la nube en Azure

- Comprobar el espacio disponible en el dispositivo de destino

- Comprobar si un dispositivo de origen del servidor iSCSI tiene nombres de registro de control de acceso (ACR) válidos, nombres IQN (que no superen los 220 caracteres de longitud) y contraseña CHAP (12 y 16 caracteres de longitud) asociados con los volúmenes

Si se produce un error en cualquiera de las comprobaciones previas anteriores, no puede continuar con la recuperación ante desastres. Debe resolver esos problemas y luego volver a intentar la recuperación ante desastres.

Una vez que haya completado correctamente la recuperación ante desastres, la propiedad de los datos de la nube en el dispositivo de origen se transfiere al dispositivo de destino. A continuación, el dispositivo de origen ya no estará disponible en el portal. El acceso a todos los volúmenes o recursos compartidos en el dispositivo de origen se bloquea y se activa el dispositivo de destino.

> [AZURE.IMPORTANT]
> 
> Aunque el dispositivo ya no está disponible, la máquina virtual que se ha aprovisionado en el sistema host sigue consumiendo recursos. Una vez que la recuperación ante desastres se haya completado correctamente, podrá eliminar esta máquina virtual del sistema host.

## Conmutar por error a un dispositivo virtual

Se recomienda que tenga un dispositivo virtual StorSimple aprovisionado, configurado a través de la interfaz de usuario web local y registrado en el servicio StorSimple Manager antes de ejecutar este procedimiento.


> [AZURE.IMPORTANT]
> 
> No se permite conmutar por error desde un dispositivo StorSimple de la serie 8000 hacia un dispositivo virtual.

Siga estos pasos para restaurar el dispositivo a un dispositivo virtual de StorSimple de destino.

1. Desconecte los volúmenes o recursos compartidos en el host. Consulte las instrucciones específicas del sistema operativo en el host para desconectar los volúmenes o recursos compartidos. Si aún no está sin conexión, para desconectar todos los volúmenes o recursos compartidos en el dispositivo debe ir a **Dispositivos > Recursos compartidos** (o **Dispositivos > Volúmenes**). Seleccione un recurso compartido o volumen y haga clic en **Desconectar** en la parte inferior de la página. Cuando se le pida confirmación, haga clic en **Sí**. Repita este proceso para todos los recursos compartidos o volúmenes en el dispositivo.

2. En la página **Dispositivos**, seleccione el dispositivo de origen para la conmutación por error y haga clic en **Desactivar**. Se le pedirá confirmación. La desactivación de un dispositivo es un proceso permanente que no se puede deshacer. También se le recordará desconectar los recursos compartidos o volúmenes en el host.

	![](./media/storsimple-ova-failover-dr/image18.png)

3. Tras la confirmación, se iniciará la desactivación. Después de que la desactivación se haya completado correctamente, recibirá una notificación.

	![](./media/storsimple-ova-failover-dr/image19.png)

4. En la página **Dispositivos**, el estado del dispositivo cambiará a **Desactivado**.

	![](./media/storsimple-ova-failover-dr/image20.png)

5. Seleccione el dispositivo desactivado y, en la parte inferior de la página, haga clic en **Conmutación por error**.

6. En el Asistente de confirmación de conmutación por error que se abre, haga lo siguiente:

    1. En la lista desplegable de dispositivos disponibles, elija un **Dispositivo de destino.** En la lista desplegable solo se muestran los dispositivos con capacidad suficiente.

    2. Revise los detalles asociados con el dispositivo de origen, como el nombre de dispositivo, la capacidad total y los nombres de los recursos compartidos que se conmutarán por error.

		![](./media/storsimple-ova-failover-dr/image21.png)

7. Marque **Acepto que la conmutación por error es una operación permanente y que una vez completada, el dispositivo de origen se eliminará**.

8. Haga clic en el icono de marca de verificación ![](./media/storsimple-ova-failover-dr/image1.png).


9. Se iniciará un trabajo de conmutación por error y se le notificará. Haga clic en **Ver trabajo** para supervisar la conmutación por error.

	![](./media/storsimple-ova-failover-dr/image22.png)

10. En la página **Trabajos**, verá un trabajo de conmutación por error creado para el dispositivo de origen. Este trabajo realiza las comprobaciones previas de recuperación ante desastres.

	![](./media/storsimple-ova-failover-dr/image23.png)

 	Después de que las comprobaciones previas de recuperación ante desastres se realizan correctamente, el trabajo de conmutación por error generará los trabajos de restauración para cada recurso compartido o volumen que exista en el dispositivo de origen.

	![](./media/storsimple-ova-failover-dr/image24.png)

11. Una vez completada la conmutación por error, vaya a la página **Dispositivos**.

	a. Seleccione el dispositivo virtual de StorSimple que se usó como dispositivo de destino para el proceso de conmutación por error.

	b. Vaya a la página **Recursos compartidos** (o **Volúmenes** si se trata de un servidor iSCSI). Todos los recursos compartidos (volúmenes) del dispositivo antiguo deberían aparecer ahora.
 	
	![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png) **Vídeo disponible**

En este vídeo se muestra cómo puede conmutar por error un dispositivo virtual StorSimple local a otro dispositivo virtual.

> [AZURE.VIDEO storsimple-virtual-array-disaster-recovery]

## Recuperación ante desastres y continuidad empresarial (BCDR)

Un escenario de recuperación ante desastres y continuidad empresarial (BCDR) se produce cuando todo el centro de datos de Azure deja de funcionar. Esto puede afectar al servicio de Administrador de StorSimple y a los dispositivos StorSimple asociados.

Si hay dispositivos StorSimple que se registraron justo antes de que ocurriera un desastre, es posible que estos dispositivos StorSimple deban eliminarse. Después del desastre, puede volver a crear y configurar esos dispositivos.

## Errores durante la recuperación ante desastres

**Interrupción de la conectividad de la nube durante la recuperación ante desastres**

Si se interrumpe la conectividad de la nube después de haberse iniciado la recuperación ante desastres y antes de completar la restauración del dispositivo, se producirá un error en la recuperación ante desastres y se le notificará. El dispositivo de destino que se utilizó para la recuperación ante desastres luego se marca como *inutilizable*. El mismo dispositivo de destino no se puede utilizar luego para futuras recuperaciones ante desastres.

**No hay dispositivos de destino compatibles**

Si los dispositivos de destino disponibles no tienen espacio suficiente, verá un error que le indicará que no hay ningún dispositivo de destino compatible.

**Errores de las comprobaciones previas**

Si no se cumple una de las comprobaciones previas, verá errores de las comprobaciones previas.

## Pasos siguientes

Obtenga más información acerca de cómo [administrar la matriz virtual de StorSimple mediante la interfaz de usuario web local](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0302_2016-->