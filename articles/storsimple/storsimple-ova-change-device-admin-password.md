<properties 
   pageTitle="Cambiar la contraseña del administrador del dispositivo virtual StorSimple | Microsoft Azure"
   description="Describe cómo usar el Portal de Azure clásico o la interfaz de usuario web de la matriz virtual de StorSimple para cambiar la contraseña del administrador de dispositivos."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="01/15/2016"
   ms.author="alkohli" />

# Cambiar la contraseña del administrador de dispositivos de la matriz virtual de StorSimple (versión preliminar)

## Información general

Cuando se usa la interfaz de Windows PowerShell para tener acceso al dispositivo virtual StorSimple, se le pedirá que escriba una contraseña del administrador de dispositivos. Cuando el dispositivo StorSimple se aprovisione e inicie por primera vez, la contraseña predeterminada es *Password1*. Para la seguridad de los datos, la contraseña predeterminada caduca la primera vez que inicia sesión y deberá cambiar esta contraseña.

También puede utilizar la interfaz de usuario web local o el Portal de Azure clásico para cambiar la contraseña del administrador de dispositivos en cualquier momento después de que el dispositivo se implemente en el entorno de producción. En este artículo se describe cada uno de estos procedimientos.

## Utilizar el Portal de Azure clásico para cambiar la contraseña

Realice los pasos siguientes para cambiar la contraseña del administrador de dispositivos a través del Portal de Azure clásico.

#### Para cambiar la contraseña del administrador de dispositivos a través del Portal de Azure clásico

1. En el portal, haga clic en **Dispositivos** > **Configuración** para el dispositivo.

2. Desplácese hacia abajo a la sección **Contraseña de administrador de dispositivos**. Proporcione una contraseña de administrador que tenga entre 8 y 15 caracteres. La contraseña debe ser una combinación de caracteres en mayúsculas, minúsculas, números y caracteres especiales.

3. Confirme la contraseña.

4. Haga clic en **Guardar** en la parte inferior de la página.

Ahora debe actualizarse la contraseña del administrador de dispositivos. Puede usar esta contraseña modificada para tener acceso al dispositivo de forma local.

## Utilizar la interfaz de usuario web de la matriz virtual de StorSimple para cambiar la contraseña

Realice los pasos siguientes para cambiar la contraseña del administrador de dispositivos a través de la interfaz de usuario web local.

#### Para cambiar la contraseña del administrador de dispositivos a través de la interfaz de usuario web local

1. En la interfaz de usuario de web local, haga clic en **Mantenimiento** > **Cambio de contraseña** para el dispositivo.

    ![cambiar password1](./media/storsimple-ova-change-device-admin-password/image40.png)

2. Escriba la **Contraseña actual**.

3. Introduzca una **Nueva contraseña**. La contraseña debe tener al menos 8 caracteres. Debe contener 3 de 4 de los siguientes requisitos: caracteres en mayúsculas, minúsculas, números y caracteres especiales.

    Tenga en cuenta que la contraseña no puede ser la misma que ninguna de las 24 últimas contraseñas.

3. Vuelva a escribir la contraseña para confirmarla.

    ![cambiar password2](./media/storsimple-ova-change-device-admin-password/image41.png)

4. En la parte inferior de la página, haga clic en **Aplicar**. A continuación, se aplicará la nueva contraseña. Si el cambio de contraseña no se realiza correctamente, verá el siguiente error.

    ![error de contraseña](./media/storsimple-ova-change-device-admin-password/image42.png)

    Después de que la contraseña se haya actualizado correctamente, se le notificará. A continuación, puede usar esta contraseña modificada para tener acceso al dispositivo de forma local.

## Pasos siguientes

Obtenga más información sobre la [administración de la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0121_2016-->