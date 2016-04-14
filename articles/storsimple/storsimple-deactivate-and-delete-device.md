<properties 
   pageTitle="Desactivación y eliminación de un dispositivo de StorSimple | Microsoft Azure"
   description="Describe cómo desactivar y eliminar en primer lugar el dispositivo de StorSimple para quitarlo del servicio."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/29/2016"
   ms.author="v-sharos" />

# Desactivación y eliminación de un dispositivo de StorSimple

## Información general

Puede que desee eliminar un dispositivo de StorSimple (por ejemplo, si va a reemplazar o actualizar el dispositivo o si ya no usa StorSimple). En su caso, debe desactivar el dispositivo para poder eliminarlo. La desactivación interrumpe la conexión entre el dispositivo y el servicio StorSimple Manager correspondiente. En este tutorial se explica cómo eliminar un dispositivo de StorSimple desactivándolo primero y eliminándolo después.

Al desactivar un dispositivo, ya no se podrá acceder a los datos que se almacenan localmente en el mismo. Solo se pueden recuperar los datos asociados con el dispositivo que se almacenaron en la nube.

>[AZURE.WARNING] La desactivación es una operación permanente y no se puede deshacer. Un dispositivo desactivado no se puede registrar en el servicio StorSimple Manager a menos que se realice un restablecimiento de fábrica.
>
>El restablecimiento de fábrica elimina todos los datos almacenados localmente en el dispositivo. Por lo tanto, es esencial que realice una instantánea en la nube de todos los datos antes de desactivar un dispositivo. Esto le permitirá recuperar todos los datos más adelante.

Este tutorial explica cómo realizar lo siguiente:

- Desactivar un dispositivo y eliminar los datos
- Desactivar un dispositivo y conservar los datos

También se explica cómo funcionan la desactivación y eliminación en un dispositivo virtual de StorSimple.

>[AZURE.NOTE] Antes de desactivar un dispositivo virtual o físico de StorSimple, asegúrese de detener o eliminar los clientes y hosts que dependen de ese dispositivo.

## Desactivación y eliminación de datos

Si está interesado en eliminar completamente el dispositivo y no desea conservar los datos del mismo, siga estos pasos.

#### Para desactivar un dispositivo y eliminar los datos  

1. Antes de desactivar un dispositivo, debe eliminar todos los contenedores de volúmenes (y los volúmenes) asociados con el dispositivo. Puede eliminar los contenedores de volúmenes solo después de eliminar las copias de seguridad asociadas.

2. Desactive el dispositivo como sigue:

    1. En el servicio StorSimple Manager, en la página **Dispositivos**, seleccione el dispositivo que desea desactivar y, en la parte inferior de la página, haga clic en **Desactivar**.

    2. Aparecerá un mensaje de confirmación. Haga clic en **Sí** para continuar. El proceso de desactivación se iniciará y tardará unos minutos en completarse.

3. Tras la desactivación, puede eliminar completamente el dispositivo. Al eliminar un dispositivo, se quita de la lista de dispositivos conectados al servicio. El servicio ya no podrá administrar el dispositivo eliminado. Siga estos pasos para eliminar el dispositivo:

    1. En el servicio StorSimple Manager, en la página **Dispositivos**, seleccione un dispositivo desactivado que desea eliminar.

    2. En la parte inferior de la página, haga clic en **Eliminar**.

    3. Se le pedirá confirmación. Haga clic en **Sí** para continuar.

    Es posible que el servicio tarde unos minutos en eliminarse.

## Desactivación y conservación de los datos

Si está interesado en eliminar el dispositivo pero desea conservar los datos del mismo, siga estos pasos.

####Para desactivar un dispositivo y conservar los datos 

1. Desactive el dispositivo. Todos los contenedores de volúmenes y las instantáneas del dispositivo se conservarán.

    1. En el servicio StorSimple Manager, en la página **Dispositivos**, seleccione el dispositivo que desea desactivar y, en la parte inferior de la página, haga clic en **Desactivar**.

    2. Aparecerá un mensaje de confirmación. Haga clic en **Sí** para continuar. El proceso de desactivación se iniciará y tardará unos minutos en completarse.

2. Ahora puede conmutar los contenedores de volúmenes y las instantáneas asociadas. Para ver los procedimientos, vaya a [Conmutación por error y recuperación ante desastres para el dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md)

3. Después de la desactivación y la conmutación por error, puede eliminar completamente el dispositivo. Al eliminar un dispositivo, se quita de la lista de dispositivos conectados al servicio. El servicio ya no podrá administrar el dispositivo eliminado. Siga estos pasos para eliminar el dispositivo:
 
    1. En el servicio StorSimple Manager, en la página **Dispositivos**, seleccione un dispositivo desactivado que desea eliminar.

    2. En la parte inferior de la página, haga clic en **Eliminar**.

    3. Se le pedirá confirmación. Haga clic en **Sí** para continuar.

    Es posible que el servicio tarde unos minutos en eliminarse.

## Desactivación y eliminación de un dispositivo virtual

Para un dispositivo virtual de StorSimple, la desactivación desasigna la máquina virtual. A continuación, puede eliminar la máquina virtual y los recursos creados en su aprovisionamiento. Después de desactivar el dispositivo virtual, no se puede restaurar a su estado anterior.

Los resultados de la desactivación de las siguientes acciones:

- Se quita el dispositivo virtual de StorSimple.

- Se quitan los OSDisk y los discos de datos creados para el dispositivo virtual StorSimple.

- Se mantienen el servicio hospedado y la red virtual creados durante el aprovisionamiento. Si no usa estas entidades, debe eliminarlas manualmente.

- Se conservan las instantáneas en la nube creadas por el dispositivo virtual de StorSimple.

## Pasos siguientes
- Para restaurar los valores de fábrica del dispositivo desactivado, vaya a [Restablecer el dispositivo a los valores de fábrica](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

- Para obtener asistencia técnica, [póngase en contacto con el soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).

- Para obtener información sobre cómo usar el servicio StorSimple Manager, vaya a [Uso del servicio StorSimple Manager para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_0224_2016-->