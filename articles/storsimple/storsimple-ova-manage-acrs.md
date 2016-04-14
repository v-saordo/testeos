<properties 
   pageTitle="Administración de los registros de control de acceso en la matriz virtual de StorSimple | Microsoft Azure"
   description="Describe cómo administrar los registros de control de acceso (ACR) para determinar qué hosts pueden conectarse a un volumen en la matriz virtual de StorSimple."
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
   ms.date="02/18/2016"
   ms.author="v-sharos" />

# Uso del servicio de Administrador de StorSimple para administrar registros de control de acceso para la matriz virtual de StorSimple (vista previa)

## Información general

Los registros de control de acceso (ACR) le permiten especificar qué hosts pueden conectarse a un volumen de la matriz virtual de StorSimple (también conocido como dispositivo virtual local de StorSimple). Los ACR se establecen en un volumen específico y contienen los nombres calificados iSCSI (IQNs) de los hosts. Cuando un host intenta conectarse a un volumen, el dispositivo comprueba el ACR asociado a ese volumen para el nombre IQN y, a continuación, si hay una coincidencia, se establece la conexión. La sección de **registros de control de acceso** de la página **Configuración** muestra todos los registros de control de acceso con los IQN correspondientes de los hosts.

Este tutorial explica las siguientes tareas comunes relacionadas con los ACR comunes:

- Obtención del IQN
- Agregar un registro de control de acceso 
- Editar un registro de control de acceso 
- Eliminar un registro de control de acceso 

> [AZURE.IMPORTANT] 
> 
> - Al asignar un ACR a un volumen, tenga cuidado de que no accedan al mismo tiempo al volumen más de un host no agrupado porque esto podría dañar el volumen. 
> - Al eliminar un ACR de un volumen, asegúrese de que el host correspondiente no tiene acceso al volumen porque la eliminación podría dar lugar a una interrupción de lectura y escritura.

## Obtención del IQN

Siga estos pasos para obtener el nombre completo del IQN de un host de Windows que ejecute Windows Server 2012.

[AZURE.INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## Adición de ACR

Use la página **Configuración** del servicio Administrador de StorSimple para agregar ACR. Normalmente, se asociará un ACR a un volumen.

Para obtener información sobre la asociación de un ACR a un volumen, vaya a [Usar el servicio de Administrador de StorSimple para administrar volúmenes](storsimple-ova-manage-volumes).

>[AZURE.IMPORTANT] 
> 
>Al asignar un ACR a un volumen, tenga cuidado de que no accedan al mismo tiempo al volumen más de un host no agrupado porque esto podría dañar el volumen.
 
Realice los pasos siguientes para agregar un ACR.

#### Para agregar un ACR

1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en la pestaña **Configuración**.

    ![pestaña Configuración](./media/storsimple-ova-manage-acrs/acr1.png)

2. En la lista tabular de **Registros de control de acceso**, proporcione un **Nombre** para el ACR.

3. Proporcione el nombre IQN del host de Windows en **Nombre del iniciador iSCSI**.

4. Para guardar el ACR recién creado, haga clic en **Guardar** en la parte inferior de la página. Verá el siguiente mensaje de confirmación.

    ![mensaje de confirmación](./media/storsimple-ova-manage-acrs/acr2.png)

5. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-ova-manage-acrs/check-icon.png). La lista tabular se actualizará para reflejar esta adición.

## Edición de un ACR

Use la página **Configuración** del Portal de Azure clásico para editar ACR.

> [AZURE.NOTE] Debe modificar solo los ACR que no están actualmente en uso. Para editar un ACR asociado a un volumen que está actualmente en uso, el volumen debe estar desconectado.

Realice los pasos siguientes para editar un ACR.

#### Para editar un ACR

1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en la pestaña **Configuración**.

2. En la lista tabular de los registros de control de acceso, pase el ratón sobre el ACR que desea modificar.

3. Proporcione un nombre nuevo y/o el IQN del ACR.

4. Haga clic en **Guardar** en la parte inferior de la página para guardar el ACR modificado. Aparecerá un mensaje de confirmación.

5. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-ova-manage-acrs/check-icon.png). La lista tabular se actualizará para reflejar este cambio.

## Eliminar un registro de control de acceso

Utilice la página **Configuración** del Portal de Azure clásico para eliminar ACR.

> [AZURE.NOTE] 
> 
> - Debe eliminar solo esos ACR que no están actualmente en uso. Para eliminar un ACR asociado a un volumen que está actualmente en uso, el volumen debe estar desconectado.
> - Al eliminar un ACR de un volumen, asegúrese de que el host correspondiente no tiene acceso al volumen porque la eliminación podría dar lugar a una interrupción de lectura y escritura.

Realice los pasos siguientes para eliminar un registro de control de acceso.

#### Para eliminar un registro de control de acceso

1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en la pestaña **Configuración**.

2. En la lista tabular de los registros de control de acceso (ACR), pase el ratón sobre el ACR que desea eliminar.

3. Aparecerá un icono de eliminación (**x**) en la columna en el extremo derecho para el ACR que seleccione. Haga clic en el icono **x** para eliminar el ACR. Verá el siguiente mensaje de confirmación.

    ![mensaje de confirmación](./media/storsimple-ova-manage-acrs/acr3.png)

5. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-ova-manage-acrs/check-icon.png). La lista tabular se actualizará para reflejar la eliminación.

## Pasos siguientes

- Obtenga más información sobre la [adición de volúmenes y la configuración de ACR](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).

<!---HONumber=AcomDC_0224_2016-->