<properties 
   pageTitle="Administración de los registros de control de acceso en StorSimple | Microsoft Azure"
   description="Describe cómo utilizar los registros de control de acceso (ACR) para determinar qué hosts pueden conectarse a un volumen en el dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="alkohli" />

# Usar el servicio de Administrador de StorSimple para administrar registros de control de acceso

## Información general

Los registros de control de acceso (ACR) le permiten especificar qué hosts pueden conectarse a un volumen del dispositivo StorSimple. Los ACR se establecen en un volumen específico y contienen los nombres calificados iSCSI (IQNs) de los hosts. Cuando un host intenta conectarse a un volumen, el dispositivo comprueba el ACR asociado a ese volumen para el nombre IQN y, a continuación, si hay una coincidencia, se establece la conexión. La sección de registros de control de acceso de la página **Configurar** muestra todos los registros de control de acceso con los IQN correspondientes de los hosts.

Este tutorial explica las siguientes tareas comunes relacionadas con los ACR comunes:

- Agregar un registro de control de acceso 
- Editar un registro de control de acceso 
- Eliminar un registro de control de acceso 

> [AZURE.IMPORTANT] 
> 
> - Al asignar un ACR a un volumen, tenga cuidado de que no accedan al mismo tiempo al volumen más de un host no agrupado porque esto podría dañar el volumen. 
> - Al eliminar un ACR de un volumen, asegúrese de que el host correspondiente no tiene acceso al volumen porque la eliminación podría dar lugar a una interrupción de lectura y escritura.

## Agregar un registro de control de acceso

Use la página **Configurar** del servicio Administrador de StorSimple para agregar ACR. Normalmente, se asociará un ACR a un volumen.

Realice los pasos siguientes para agregar un ACR.

#### Para agregar un registro de control de acceso

1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en la pestaña **Configurar**.

2. En la lista tabular de **Registros de control de acceso**, proporcione un **Nombre** para el ACR.

3. Proporcione el nombre IQN del host de Windows en **Nombre del iniciador iSCSI**. Para obtener el IQN del host de Windows Server, haga lo siguiente:

   - Inicie el iniciador iSCSI de Microsoft en el host de Windows.
   - En la ventana **Propiedades del iniciador iSCSI**, en la pestaña **Configuración**, seleccione y copie la cadena desde el campo **Nombre de iniciador**.
   - Pegue esta cadena en el campo **Nombre del iniciador iSCSI** de la tabla de ACR en el Portal de Azure clásico.

4. Haga clic en **Guardar** para guardar el ACR recién creado. La lista tabular se actualizará para reflejar esta adición.

## Editar un registro de control de acceso

Use la página **Configurar** del Portal de Azure clásico para editar ACR.

> [AZURE.NOTE] Puede modificar solo esos ACR que no están actualmente en uso. Para editar un ACR asociado a un volumen que está actualmente en uso, primero debe establecer el volumen como sin conexión.

Realice los pasos siguientes para editar un ACR.

#### Para editar un registro de control de acceso

1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en la pestaña **Configurar**.

2. En la lista tabular de los registros de control de acceso, pase el ratón sobre el ACR que desea modificar.

3. Proporcione un nombre nuevo y/o el IQN del ACR.

4. Haga clic en **Guardar** para guardar el ACR modificado. La lista tabular se actualizará para reflejar este cambio.

## Eliminar un registro de control de acceso

Use la página **Configurar** del Portal de Azure clásico para eliminar ACR.

> [AZURE.NOTE] Solo puede eliminar esos ACR que no están actualmente en uso. Para eliminar un ACR asociado a un volumen que está actualmente en uso, primero debe establecer el volumen como sin conexión.

Realice los pasos siguientes para eliminar un registro de control de acceso.

#### Para eliminar un registro de control de acceso

1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en la pestaña **Configurar**.

2. En la lista tabular de los registros de control de acceso (ACR), pase el ratón sobre el ACR que desea eliminar.

3. Aparecerá un icono de eliminación (**x**) en la columna en el extremo derecho para el ACR que seleccione. Haga clic en el icono **x** para eliminar el ACR.

4. Cuando se le pida confirmación, haga clic en **SÍ** para continuar con la eliminación. La lista tabular se actualizará para reflejar la eliminación.

## Pasos siguientes

- Obtenga más información sobre la [administración de volúmenes de StorSimple](storsimple-manage-volumes.md).

- Obtenga más información sobre el [uso del servicio StorSimple Manager para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md).
 

<!---HONumber=AcomDC_0218_2016-->