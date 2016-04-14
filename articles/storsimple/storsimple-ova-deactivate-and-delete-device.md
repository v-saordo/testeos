<properties 
   pageTitle="Desactivación y eliminación de una matriz virtual de StorSimple | Microsoft Azure"
   description="Describe cómo desactivar y eliminar en primer lugar el dispositivo de StorSimple para quitarlo del servicio."
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
   ms.date="02/08/2016"
   ms.author="alkohli" />

# Desactivación y eliminación de una matriz virtual de StorSimple

## Información general

La desactivación de una matriz virtual de StorSimple interrumpe la conexión entre el dispositivo y el servicio StorSimple Manager correspondiente. La desactivación es una operación permanente y no se puede deshacer. Un dispositivo desactivado no se puede registrar en el servicio StorSimple Manager de nuevo.

Puede que tenga que desactivar y eliminar un dispositivo virtual de StorSimple en los escenarios siguientes:


- El dispositivo está en línea y planea realizar la conmutación por error en este dispositivo. Puede que tenga que hacer esto si piensa actualizar a un dispositivo de mayor tamaño. Después de que se transfieran los datos del dispositivo y se complete la conmutación por error, puede eliminar el dispositivo.

- El dispositivo está desconectado y planea realizar la conmutación por error en este dispositivo. Esto puede ocurrir si se produce un desastre donde, debido a una interrupción del centro de datos, el dispositivo principal está inactivo. Planea realizar la conmutación por error del dispositivo en un dispositivo secundario. Después de que se transfieran los datos del dispositivo y se complete la conmutación por error, puede eliminar el dispositivo.

- Desea retirar el dispositivo y, a continuación, eliminarlo.
 

Al desactivar un dispositivo, no podrá obtener acceso a los datos almacenados localmente. Solo se pueden recuperar los datos almacenados en la nube. Si piensa mantener los datos del dispositivo tras la desactivación, debe realizar una instantánea en la nube de todos los datos antes de desactivar un dispositivo. Esto le permitirá recuperar todos los datos más adelante.


Este tutorial explica cómo realizar lo siguiente:

- Desactivación de un dispositivo 
- Eliminación de un dispositivo desactivado


## Desactivación de un dispositivo

Realice los pasos siguientes para desactivar el dispositivo.

#### Para desactivar el dispositivo   

1. Vaya a la página **Dispositivos**. Seleccione el dispositivo que desee desactivar.

	![Selección del dispositivo que desactivar](./media/storsimple-ova-deactivate-and-delete-device/deactivate1m.png)

3. Haga clic en **Desactivar** en la parte inferior de la página.

	![Clic en Desactivar](./media/storsimple-ova-deactivate-and-delete-device/deactivate2m.png)

4. Aparecerá un mensaje de confirmación. Haga clic en **Sí** para continuar.

	![Confirmación de la desactivación](./media/storsimple-ova-deactivate-and-delete-device/deactivate3m.png)

	El proceso de desactivación se iniciará y tardará unos minutos en completarse.

	![Desactivación en curso](./media/storsimple-ova-deactivate-and-delete-device/deactivate4m.png)

3. Tras la desactivación, se actualizará la lista de dispositivos.

	![Desactivación completa](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)

	Ahora puede eliminar este dispositivo.

## Eliminación del dispositivo

El dispositivo debe desactivarse primero para poder eliminarlo. Al eliminar un dispositivo, se quita de la lista de dispositivos conectados al servicio. El servicio ya no podrá administrar el dispositivo eliminado. Sin embargo, los datos asociados al dispositivo permanecerán en la nube. Tenga en cuenta que estos datos generarán cargos.

Siga estos pasos para eliminar el dispositivo:

#### Para eliminar el dispositivo 

 1. En el servicio StorSimple Manager, en la página **Dispositivos**, seleccione un dispositivo desactivado que desea eliminar.

	![Selección del dispositivo que eliminar](./media/storsimple-ova-deactivate-and-delete-device/deactivate5m.png)

 2. En la parte inferior de la página, haga clic en **Eliminar**.
 
	![Clic en Eliminar](./media/storsimple-ova-deactivate-and-delete-device/deactivate6m.png)

 3. Se le pedirá confirmación. Escriba el nombre del dispositivo para confirmar la eliminación del dispositivo. Tenga en cuenta que al eliminar el dispositivo no se eliminarán los datos en la nube asociados al dispositivo. Haga clic en el icono de marca de verificación para continuar.
 
	![Confirmar eliminación](./media/storsimple-ova-deactivate-and-delete-device/deactivate7m.png)

 5. Es posible que el servicio tarde unos minutos en eliminarse.

	![Eliminación en curso](./media/storsimple-ova-deactivate-and-delete-device/deactivate8m.png)

 	Después de que se elimine el dispositivo, se actualizará la lista de dispositivos.

	![Eliminación completa](./media/storsimple-ova-deactivate-and-delete-device/deactivate9m.png)


## Pasos siguientes

- Para obtener información sobre cómo usar el servicio StorSimple Manager, vaya a [Uso del servicio StorSimple Manager para administrar la matriz virtual de StorSimple](storsimple-ova-manager-service-administration.md). 

<!---HONumber=AcomDC_0218_2016-->