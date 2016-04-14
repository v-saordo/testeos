<properties 
   pageTitle="Implementación del servicio StorSimple Manager en una matriz virtual de StorSimple| Microsoft Azure"
   description="Aquí encontrará información sobre cómo crear y eliminar el servicio StorSimple Manager en el Portal de Azure clásico, así como una descripción acerca de cómo administrar la clave de registro de servicio."
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
   ms.date="02/05/2016"
   ms.author="alkohli" />

# Implementación del servicio StorSimple Manager en una matriz virtual de StorSimple

## Información general

El servicio StorSimple Manager se ejecuta en Microsoft Azure y se conecta a varios dispositivos StorSimple. Después de crear el servicio, puede usarlo para administrar los dispositivos desde el Portal de Microsoft Azure clásico, ejecutándolo en un explorador. Esto permite supervisar todos los dispositivos que están conectados al servicio StorSimple Manager desde una única ubicación central, reduciendo la carga administrativa.

La página de aterrizaje de StorSimple Manager enumera todos los servicios de StorSimple Manager que puede usar para administrar los dispositivos de almacenamiento de StorSimple. Para cada servicio StorSimple Manager, se presenta la siguiente información en la página de StorSimple Manager:

- **Nombre** : el nombre asignado al servicio StorSimple Manager cuando se creó. El nombre del servicio no se puede cambiar una vez creado el servicio.

- **Estado** : estado del servicio, que puede ser **Activo**, **Creando** o **En línea**.

- **Ubicación** : ubicación geográfica en la que se implementará el dispositivo StorSimple.

- **Suscripción** : suscripción de facturación asociada a su servicio.

Las tareas comunes que se pueden realizar a través de la página de StorSimple Manager son:

- Crear un servicio
- Eliminar un servicio
- Obtener la clave de registro del servicio
- Volver a generar la clave de registro de servicio

Este tutorial describe cómo realizar cada una de estas tareas.

## Crear un servicio

Utilice la opción **Creación rápida** para crear un servicio de StorSimple Manager si desea implementar el dispositivo StorSimple. Para crear un servicio, debe tener:

- Una suscripción con un contrato Enterprise
- Una cuenta de Almacenamiento de Microsoft Azure activa.
- La información de facturación que se usa para la administración de acceso

También puede generar una cuenta de almacenamiento predeterminada cuando se crea el servicio.

Un único servicio puede administrar varios dispositivos. Sin embargo, un dispositivo no puede abarcar varios servicios. Una gran empresa puede tener varias instancias de servicio para trabajar con distintas suscripciones, organizaciones o incluso las ubicaciones de implementación.

Realice los siguientes pasos para crear un servicio.

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

## Eliminar un servicio

Antes de eliminar un servicio, asegúrese de que no lo esté utilizando ningún dispositivo conectado. Si se está utilizando el servicio, desactive los dispositivos conectados. La operación de desactivación eliminará la conexión entre el dispositivo y el servicio, pero conservará los datos del dispositivo en la nube.

> [AZURE.IMPORTANT] Después de eliminar un servicio, no se puede revertir la operación.

Realice los siguientes pasos para eliminar un servicio.

### Para eliminar un servicio

1. En la página **Servicio StorSimple Manager**, haga clic en el servicio que desee eliminar.

1. En la parte inferior de la página, haga clic en **Eliminar**.

1. Haga clic en **Sí** en la notificación de confirmación. Es posible que el servicio tarde unos minutos en eliminarse.

## Obtener la clave de registro del servicio

Después de haber creado correctamente un servicio, deberá registrar el dispositivo StorSimple con el servicio. Para registrar el primer dispositivo StorSimple, necesitará la clave de registro del servicio. Para registrar dispositivos adicionales con un servicio existente StorSimple, necesitará la clave de registro y la clave de cifrado de datos de servicio (que se genera en el primer dispositivo durante el registro). Para obtener más información acerca de la clave de cifrado de datos de servicio, consulte [Obtener clave de cifrado de datos de servicio de la interfaz de usuario web local](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key).

Realice los pasos siguientes para obtener la clave de registro del servicio.

[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

Mantenga la clave de registro del servicio en una ubicación segura. Necesitará esta clave, así como la clave de cifrado de datos de servicio para registrar dispositivos adicionales con este servicio. Después de obtener la clave de registro del servicio, deberá configurar el dispositivo a través de la interfaz de Windows PowerShell para StorSimple.

## Volver a generar la clave de registro de servicio

Será necesario volver a generar una clave de registro del servicio si es necesario para realizar la rotación de claves o si ha cambiado la lista de administradores de servicios. Cuando se regenera la clave, la nueva clave se utiliza solo para registrar dispositivos posteriores. Los dispositivos que ya se han registrado no se ven afectados por este proceso.

Realice los pasos siguientes para volver a generar una clave de registro de servicio.

### Para volver a generar la clave de registro de servicio

1. En la página del **servicio de Administrador de StorSimple**, haga clic en **Clave de registro**.

1. En el cuadro de diálogo **Clave de registro del servicio**, haga clic en **Regenerar**.

1. Aparecerá un mensaje de confirmación. Haga clic en **Aceptar** para continuar con la regeneración.

1. Aparecerá una nueva clave de registro de servicio.

1. Copie esta clave y guárdela para registrar los dispositivos nuevos con este servicio.

1. Haga clic en el icono de verificación ![Icono de marca de verificación](./media/storsimple-ova-manage-service/image7.png) para cerrar este cuadro de diálogo.


## Pasos siguientes

- Lea una [introducción](storsimple-ova-deploy1-portal-prep.md) a las matrices virtuales de StorSimple.
	
- Obtenga información sobre cómo [administrar el dispositivo StorSimple](storsimple-ova-web-ui-admin.md).

 

<!---HONumber=AcomDC_0218_2016-->