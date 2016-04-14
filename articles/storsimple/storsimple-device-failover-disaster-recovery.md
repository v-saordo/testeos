<properties 
   pageTitle="Conmutación por error y recuperación ante desastres de StorSimple | Microsoft Azure"
   description="Aprenda cómo conmutar por error el dispositivo StorSimple a sí mismo, a otro dispositivo físico o a un dispositivo virtual."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/20/2016"
   ms.author="alkohli" />

# Conmutación por error y recuperación ante desastres para el dispositivo StorSimple

## Información general

En este tutorial se describen los pasos necesarios para conmutar por error un dispositivo StorSimple en caso de desastre. La conmutación por error permite migrar los datos desde un dispositivo de origen en el centro de datos a otro dispositivo físico o incluso virtual situado en la misma ubicación geográfica o en otra diferente.

La conmutación por error de un dispositivo se coordina a través de la característica de recuperación ante desastres y se inicia desde la página Dispositivos. Esta página recoge en formato de tabla todos los dispositivos de StorSimple conectados al servicio de Administrador de StorSimple. Para cada dispositivo se muestran el nombre descriptivo, el estado, la capacidad aprovisionada y máxima, el tipo y el modelo.

![Página de dispositivos](./media/storsimple-device-failover-disaster-recovery/IC740972.png)

Las instrucciones de este tutorial se aplican a dispositivos físicos y virtuales de StorSimple en todas las versiones de software.



## Recuperación ante desastres y conmutación por error del dispositivo

En un escenario de recuperación ante desastres, el dispositivo principal deja de funcionar. En esta situación, puede mover los datos en la nube asociados al dispositivo con error a otro dispositivo usando el dispositivo primario como *origen* y especificando otro dispositivo como *destino*. Puede seleccionar uno o varios contenedores de volúmenes para migrar al dispositivo de destino. Este proceso se conoce como *conmutación por error*. Durante la conmutación por error, los contenedores de volúmenes del dispositivo de origen cambian la propiedad y se transfieren al dispositivo de destino.

## Consideraciones para la conmutación por error del dispositivo

En caso de desastre, puede elegir conmutar por error el dispositivo StorSimple:

- A un dispositivo físico 
- A sí mismo
- A un dispositivo virtual

En todos los tipos de conmutación por error del dispositivo, tenga en cuenta lo siguiente:

- Los requisitos previos para la recuperación ante desastres son que todos los volúmenes incluidos en los contenedores de volúmenes estén desconectados y que los contenedores de volúmenes tengan asociada una instantánea en la nube. 
- Los dispositivos de destino disponibles para la recuperación ante desastres son dispositivos con espacio suficiente para alojar los contenedores de volúmenes seleccionados. 
- Los dispositivos que están conectados al servicio pero que no cumplen los criterios de espacio suficiente no estarán disponibles como dispositivos de destino.

#### Conmutación por error del dispositivo a través de las versiones de software

Un servicio del administrador de StorSimple en una implementación puede tener varios dispositivos tanto virtuales como físicos, y todos ellos pueden ejecutar versiones de software diferentes. En función de la versión de software, los tipos de volúmenes de los dispositivos también pueden ser diferentes. Por ejemplo, un dispositivo que ejecuta Update 2 o posterior tendría volúmenes anclados localmente y en capas (de tal forma que los archivos serían un subconjunto de las capas). Por otro lado, un dispositivo con una versión anterior a Update 2 puede tener volúmenes en capas y de archivado.

Utilice la tabla siguiente para determinar si puede conmutar por error a otro dispositivo que ejecuta una versión diferente de software y el comportamiento de los tipos de volumen durante la recuperación ante desastres.

| Conmutación por error a partir de | Permitida para dispositivo físico | Permitida para dispositivo virtual |
|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------|
| Update 2 a versión anterior a Update 1 (lanzamiento, 0.1, 0.2, 0.3) | No | No |
| Update 2 a Update 1 (1, 1.1, 1.2) | Sí <br></br>Si utiliza volúmenes anclados localmente o en capas o una combinación de ambos, la conmutación por error de tales volúmenes siempre se realiza en capas. | Sí<br></br>Si utiliza volúmenes anclados localmente, su conmutación por error se realiza en capas. |
| Update 2 a Update 2 (versión posterior) | Sí<br></br>Si utiliza volúmenes anclados localmente o en capas o una combinación de ambos, la conmutación por error de tales volúmenes se realiza como el tipo de volumen de partida; los volúmenes en capas como en capas y los anclados localmente como anclados localmente. | Sí<br></br>Si utiliza volúmenes anclados localmente, su conmutación por error se realiza en capas. |


#### Conmutación por error parcial entre versiones de software

Siga esta guía si va a realizar una conmutación por error parcial de un dispositivo de origen StorSimple con una versión anterior a la actualización 1 en un destino en que se ejecuta la actualización 1 o una versión posterior.


| Conmutación por error parcial desde | Permitida para dispositivo físico | Permitida para dispositivo virtual |
|----------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------|
|Versión anterior a la actualización 1 (lanzamiento, 0.1, 0.2, 0.3) en la versión de actualización 1 o posterior | Sí, consulte abajo la sugerencia de procedimiento recomendado. | Sí, consulte abajo la sugerencia de procedimiento recomendado. |


>[AZURE.TIP] Se produjo un cambio de formato de datos y metadatos de nube en la versión de actualización 1 y posteriores. Por lo tanto, no se recomienda realizar una conmutación por error parcial desde versiones anteriores a la actualización 1 en la versión de actualización 1 o posteriores. Si necesita realizar una conmutación por error parcial, se recomienda que aplique primero la actualización 1 o posterior en ambos dispositivos (origen y destino) y después lleve a cabo la conmutación por error.

## Conmutar por error a otro dispositivo físico

Siga estos pasos para restaurar el dispositivo a un dispositivo físico de destino.

1. Compruebe que el contenedor de volúmenes que desea conmutar por error tiene asociadas instantáneas en la nube.

1. En la página **Dispositivos**, haga clic en la pestaña **Contenedores de volúmenes**.

1. Seleccione un contenedor de volúmenes que desee conmutar por error a otro dispositivo. Haga clic en el contenedor de volúmenes para mostrar la lista de volúmenes incluidos dentro de este contenedor. Seleccione un volumen y haga clic en **Desconectar** para desconectar el volumen. Repita este proceso para todos los volúmenes incluidos en el contenedor de volúmenes.

1. Repita el paso anterior para todos los contenedores de volúmenes que le gustaría conmutar por error a otro dispositivo.

1. En la página **Dispositivos**, haga clic en **Conmutación por error**.

1. En el asistente que se abre, en **Elegir el contenedor de volúmenes para la conmutación por error**:

	1. En la lista de contenedores de volúmenes, seleccione los contenedores de volúmenes que desea que conmuten por error.

		>[AZURE.NOTE] **Solo se muestran los contenedores de volúmenes con volúmenes desconectados e instantáneas de nube asociadas.**

	1. En **Elegir un dispositivo de destino** para los volúmenes de los contenedores seleccionados, elija un dispositivo de destino en la lista desplegable de dispositivos disponibles. En la lista desplegable solo se muestran los dispositivos con capacidad disponible.

	1. Por último, revise toda la configuración de conmutación por error en **Confirmar conmutación por error**. Haga clic en el icono de marca de verificación ![Icono de marca de verificación](./media/storsimple-device-failover-disaster-recovery/IC740895.png).

1. Se crea un trabajo de conmutación por error que puede supervisarse mediante la página **Trabajos**. Si el contenedor de volúmenes que conmutó por error tiene volúmenes locales, verá los trabajos de restauración individuales de cada volumen local (no de volúmenes en niveles) en el contenedor. Es posible que esos trabajos de restauración tarden bastante tiempo en completarse. Asimismo, es probable que el trabajo de conmutación por error se complete antes. Tenga en cuenta que estos volúmenes solo tendrán garantías locales después de que los trabajos de restauración se completen. Una vez completada la conmutación por error, vaya a la página **Dispositivos**.

	1. Seleccione el dispositivo que se usó como dispositivo de destino para el proceso de conmutación por error.

	1. Vaya a la página **Contenedores de volúmenes**. Deberían aparecer todos los contenedores de volúmenes, junto con los volúmenes del dispositivo antiguo.

## Conmutación por error usando un solo dispositivo

Siga estos pasos si solo dispone de un dispositivo y necesita realizar una conmutación por error.

1. Tome instantáneas en la nube de todos los volúmenes del dispositivo.

1. Restablezca el dispositivo a los valores predeterminados de fábrica. Siga las instrucciones detalladas de [Cómo restablecer un dispositivo StorSimple a los valores predeterminados de fábrica](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

1. Configure el dispositivo y vuelva a registrarlo con el servicio de Administrador de StorSimple.

1. En la página **Dispositivos**, el dispositivo antiguo debe aparecer como **Desconectado**. El dispositivo recién registrado debe aparecer como **Conectado**.

1. Complete primero la configuración mínima del dispositivo en el dispositivo nuevo.
												
	>[AZURE.IMPORTANT] **Si no se completa primero la configuración mínima, se producirá un error en la recuperación ante desastres como resultado de un error en la implementación actual. Este comportamiento se solucionará en una versión posterior.**

1. Seleccione el dispositivo antiguo (estado desconectado) y haga clic en **Conmutación por error**. En el asistente que aparece, conmute por error este dispositivo y especifique el dispositivo de destino como dispositivo recién registrado. Para obtener instrucciones detalladas, consulte [Conmutar por error a otro dispositivo físico](#fail-over-to-another-physical-device).

1. Se creará un trabajo de restauración de dispositivo que puede supervisar desde la página **Trabajos**.

1. Después de que el trabajo se haya completado correctamente, acceda al dispositivo nuevo y navegue hasta la página **Contenedores de volúmenes**. Ahora se deben migrar todos los contenedores de volúmenes del dispositivo antiguo al dispositivo nuevo.

## Conmutar por error a un dispositivo virtual de StorSimple

Debe crear y configurar un dispositivo virtual de StorSimple antes de ejecutar este procedimiento. Si ejecuta Update 2, considere el uso de un dispositivo virtual 8020 para la recuperación ante desastres que tiene 64 TB y utiliza el almacenamiento premium.
 
Siga estos pasos para restaurar el dispositivo a un dispositivo virtual de StorSimple de destino.

1. Compruebe que el contenedor de volúmenes que desea conmutar por error tiene asociadas instantáneas en la nube.

1. En la página **Dispositivos**, haga clic en la pestaña **Contenedores de volúmenes**.

1. Seleccione un contenedor de volúmenes que desee conmutar por error a otro dispositivo. Haga clic en el contenedor de volúmenes para mostrar la lista de volúmenes incluidos dentro de este contenedor. Seleccione un volumen y haga clic en **Desconectar** para desconectar el volumen. Repita este proceso para todos los volúmenes incluidos en el contenedor de volúmenes.

1. Repita el paso anterior para todos los contenedores de volúmenes que le gustaría conmutar por error a otro dispositivo.

1. En la página **Dispositivos**, haga clic en **Conmutación por error**.

1. En el asistente que se abre, en **Elegir el contenedor de volúmenes para la conmutación por error**, siga estos pasos:
													
	a. En la lista de contenedores de volúmenes, seleccione los contenedores de volúmenes que desea que conmuten por error.

	>[AZURE.NOTE] **Solo se muestran los contenedores de volúmenes con volúmenes desconectados e instantáneas de nube asociadas.**

	b. En **Elegir un dispositivo de destino para los volúmenes de los contenedores seleccionados**, seleccione el dispositivo virtual de StorSimple en la lista desplegable de dispositivos disponibles. En la lista desplegable solo se muestran los dispositivos con capacidad suficiente.
	

1. Por último, revise toda la configuración de conmutación por error en **Confirmar conmutación por error**. Haga clic en el icono de marca de verificación ![Icono de marca de verificación](./media/storsimple-device-failover-disaster-recovery/IC740895.png).

1. Una vez completada la conmutación por error, vaya a la página **Dispositivos**.
													
	a. Seleccione el dispositivo virtual de StorSimple que se usó como dispositivo de destino para el proceso de conmutación por error.
	
	b. Vaya a la página **Contenedores de volúmenes**. Deberían aparecer todos los contenedores de volúmenes, junto con los volúmenes del dispositivo antiguo.

![Vídeo disponible](./media/storsimple-device-failover-disaster-recovery/Video_icon.png) **Vídeo disponible**

Para ver un vídeo que muestra cómo se puede restaurar un dispositivo físico con conmutación por error en un dispositivo virtual en la nube, haga clic [aquí](https://azure.microsoft.com/documentation/videos/storsimple-and-disaster-recovery/).

## Recuperación ante desastres y continuidad empresarial (BCDR)

Un escenario de recuperación ante desastres y continuidad empresarial (BCDR) se produce cuando todo el centro de datos de Azure deja de funcionar. Esto puede afectar al servicio de Administrador de StorSimple y a los dispositivos StorSimple asociados.

Si hay dispositivos StorSimple que se registraron justo antes de que ocurra un desastre, es posible que estos dispositivos deban restablecerse a valores de fábrica. Después del desastre, el dispositivo StorSimple se mostrará como desconectado. El dispositivo StorSimple debe eliminarse del portal, restablecerse a valores de fábrica y volver a registrarse.


## Pasos siguientes

- Después de haber realizado una conmutación por error, puede que necesite [desactivar o eliminar el dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

- Para obtener información sobre cómo usar el servicio del administrador de StorSimple, vaya a [Utilizar el servicio de Administrador de StorSimple para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md).
 

<!---HONumber=AcomDC_0302_2016-->