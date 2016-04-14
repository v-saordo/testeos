<properties 
   pageTitle="Administración de la cuenta de almacenamiento de StorSimple | Microsoft Azure"
   description="Se explica cómo usar la página de configuración de StorSimple Manager para agregar, editar, eliminar o rotar las claves de seguridad de una cuenta de almacenamiento asociada con StorSimple Virtual Array."
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
   ms.date="02/05/2016"
   ms.author="alkohli" />

# Uso del servicio StorSimple Manager para administrar cuentas de almacenamiento (vista previa)

## Información general

En la página **Configurar** se presentan los parámetros de servicio globales que se pueden crear en el servicio StorSimple Manager. Estos parámetros se pueden aplicar a todos los dispositivos conectados al servicio e incluyen:

- Cuentas de almacenamiento 
- Registros de control de acceso 

En este tutorial se explica cómo usar la página **Configurar** para agregar, editar o eliminar cuentas de almacenamiento para StorSimple Virtual Array. La información de este tutorial solo se aplica a la matriz StorSimple Virtual Array que ejecuta software de vista previa pública.

 ![Página Configurar](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)

Las cuentas de almacenamiento contienen las credenciales que usa el dispositivo para acceder a su cuenta de almacenamiento con el proveedor de servicios en la nube. En el caso de las cuentas de almacenamiento de Microsoft Azure, se trata de credenciales como el nombre de cuenta y la clave de acceso primaria.

En la página **Configurar**, todas las cuentas de almacenamiento que se crean para la suscripción de facturación se muestran en formato tabular que contiene la información siguiente:

- **Nombre**: nombre único asignado a la cuenta cuando se creó.
- **SSL habilitado**: indica si el SSL está habilitado y la comunicación de dispositivo a la nube se realiza a través del canal seguro.

Las tareas más comunes relacionadas con las cuentas de almacenamiento que se pueden realizar en la página **Configurar** son:

- Agregar una cuenta de almacenamiento 
- Editar una cuenta de almacenamiento 
- Eliminar una cuenta de almacenamiento 


## Tipos de cuentas de almacenamiento

Hay tres tipos de cuentas de almacenamiento que se pueden usar con el dispositivo StorSimple.

- **Cuentas de almacenamiento generadas automáticamente**: como sugiere su nombre, este tipo de cuenta de almacenamiento se genera automáticamente cuando se crea el servicio. Para obtener más información sobre cómo se crea esta cuenta de almacenamiento, vea [Crear un nuevo servicio](storsimple-ova-manage-service.md#create-a-service). 
- **Cuentas de almacenamiento en la suscripción al servicio**: se trata de cuentas de almacenamiento de Azure que están asociadas a la misma suscripción que la del servicio. Para obtener más información sobre cómo se crean estas cuentas de almacenamiento, vea [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md). 
- **Cuentas de almacenamiento fuera de la suscripción al servicio**: son las cuentas de almacenamiento de Azure no asociadas al servicio que probablemente existían antes de que se crease el servicio.

## Agregar una cuenta de almacenamiento

Puede agregar una cuenta de almacenamiento a la configuración del servicio StorSimple Manager proporcionando un nombre descriptivo único y las credenciales de acceso vinculadas a la cuenta de almacenamiento. También tiene la opción de habilitar el modo de Capa de sockets seguros (SSL) para crear un canal seguro para la comunicación de red entre su dispositivo y la nube.

Puede crear varias cuentas para un proveedor de servicios en la nube determinado. Mientras se guarda la cuenta de almacenamiento, el servicio intenta comunicarse con el proveedor de servicios en la nube. Las credenciales y el material de acceso que proporcionó se autenticarán en este momento. Solo si la autenticación se realiza correctamente se crea una cuenta de almacenamiento. Si se produce un error en la autenticación, se mostrará un mensaje de error adecuado.

A continuación se detalla el procedimiento para agregar una cuenta de almacenamiento.

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## Editar una cuenta de almacenamiento

Puede editar una cuenta de almacenamiento utilizada por su dispositivo. Si edita una cuenta de almacenamiento que está actualmente en uso, los campos disponibles para modificar son la clave de acceso y el modo SSL para la cuenta de almacenamiento. Puede proporcionar la nueva clave de acceso de almacenamiento o modificar la selección de **Habilitar modo SSL** y guardar la configuración actualizada.

#### Para editar una cuenta de almacenamiento

1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en **Configurar**.

2. Haga clic en **Agregar/editar cuentas de almacenamiento**.

3. En el cuadro de diálogo **Agregar/editar cuentas de almacenamiento**:

  1. En la lista desplegable de **Cuentas de almacenamiento**, elija una cuenta existente que desea modificar.
  2. Si es necesario, puede modificar la selección **Habilitar modo SSL**.
  3. Puede optar por regenerar las claves de acceso de la cuenta de almacenamiento. Para más información, vea [Regeneración de las claves de la cuenta de almacenamiento](storage-create-storage-account.md#manage-your-storage-access-keys). Proporcione la nueva clave de la cuenta de almacenamiento. Para una cuenta de almacenamiento de Azure, esta es la clave de acceso principal. 
  4. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-ova-manage-storage-accounts/checkicon.png) para guardar la configuración. La configuración se actualiza en la página **Configurar**. 
  5. En la parte inferior de la página, haga clic en **Guardar** para guardar la configuración recién actualizada. 

     ![Editar una cuenta de almacenamiento](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)
  
## Eliminar una cuenta de almacenamiento

> [AZURE.IMPORTANT] Puede eliminar una cuenta de almacenamiento solo si no la usa. Si una cuenta de almacenamiento está en uso, se le notificará.

#### Para eliminar una cuenta de almacenamiento

1. En la página de aterrizaje del servicio de Administrador de StorSimple, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en **Configurar**.

2. En la lista tabular de cuentas de almacenamiento, desplace el mouse sobre la cuenta que desea eliminar.

3. Aparecerá un icono de eliminación (**x**) en la columna más a la derecha para esa cuenta de almacenamiento. Haga clic en el icono **x** para eliminar las credenciales.

4. Cuando se le pida confirmación, haga clic en **Sí** para continuar con la eliminación. La lista tabular se actualizará para reflejar los cambios.

5. En la parte inferior de la página, haga clic en **Guardar** para guardar la configuración recién actualizada.


## Pasos siguientes

- Obtenga más información para [administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0211_2016-->