<properties 
   pageTitle="Administración de la cuenta de almacenamiento de StorSimple | Microsoft Azure"
   description="Explica cómo usar la página Configurar el Administrador de StorSimple para agregar, editar, eliminar o rotar las claves de seguridad de una cuenta de almacenamiento."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/01/2015"
   ms.author="v-sharos" />

# Usar el servicio de Administrador de StorSimple para administrar su cuenta de almacenamiento

## Información general

La página **Configurar** presenta todos los parámetros de servicio globales que se pueden crear en el servicio de Administrador de StorSimple. Estos parámetros se pueden aplicar a todos los dispositivos conectados al servicio e incluyen:

- Cuentas de almacenamiento 
- Plantillas de ancho de banda 
- Registros de control de acceso 

Este tutorial explica cómo usar la página **Configurar** para agregar, editar o eliminar cuentas de almacenamiento, o bien para rotar las claves de seguridad para una cuenta de almacenamiento.

 ![Página Configurar](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)

Las cuentas de almacenamiento contienen las credenciales que usa el dispositivo para acceder a su cuenta de almacenamiento con el proveedor de servicios en la nube. En el caso de las cuentas de almacenamiento de Microsoft Azure, se trata de credenciales como el nombre de cuenta y la clave de acceso primaria.

En la página **Configurar**, todas las cuentas de almacenamiento que se crean para la suscripción de facturación se muestran en formato tabular que contiene la información siguiente:

- **Nombre**: nombre único asignado a la cuenta cuando se creó.
- **SSL habilitado**: indica si el SSL está habilitado y la comunicación de dispositivo a la nube se realiza a través del canal seguro.
- **Usado por**: número de volúmenes que usan la cuenta de almacenamiento.

Las tareas más comunes relacionadas con las cuentas de almacenamiento que se pueden realizar en la página **Configurar** son:

- Agregar una cuenta de almacenamiento 
- Editar una cuenta de almacenamiento 
- Eliminar una cuenta de almacenamiento 
- Rotación de claves de cuentas de almacenamiento 

## Tipos de cuentas de almacenamiento

Hay tres tipos de cuentas de almacenamiento que se pueden usar con el dispositivo StorSimple.

- **Cuentas de almacenamiento generadas automáticamente**: como sugiere su nombre, este tipo de cuenta de almacenamiento se genera automáticamente cuando se crea el servicio. Para obtener más información sobre cómo se crea esta cuenta de almacenamiento, consulte el [Paso 1: Crear un nuevo servicio](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service), en [Implementar el dispositivo StorSimple local](storsimple-deployment-walkthrough.md). 
- **Cuentas de almacenamiento en la suscripción al servicio**: se trata de cuentas de almacenamiento de Azure que están asociadas a la misma suscripción que la del servicio. Para obtener más información sobre cómo se crean estas cuentas de almacenamiento, vea [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md). 
- **Cuentas de almacenamiento fuera de la suscripción al servicio**: son las cuentas de almacenamiento de Azure no asociadas al servicio que probablemente existían antes de que se crease el servicio.

## Agregar una cuenta de almacenamiento

Puede agregar una cuenta de almacenamiento proporcionando un nombre descriptivo único y las credenciales de acceso vinculadas a la cuenta de almacenamiento (con el proveedor de servicios en la nube especificado). También tiene la opción de habilitar el modo de Capa de sockets seguros (SSL) para crear un canal seguro para la comunicación de red entre su dispositivo y la nube.

Puede crear varias cuentas para un proveedor de servicios en la nube determinado. No obstante, tenga en cuenta que, después de crear una cuenta de almacenamiento, no puede cambiar el proveedor de servicios en la nube.

Mientras se guarda la cuenta de almacenamiento, el servicio intenta comunicarse con el proveedor de servicios en la nube. Las credenciales y el material de acceso que proporcionó se autenticarán en este momento. Solo si la autenticación se realiza correctamente se crea una cuenta de almacenamiento. Si se produce un error en la autenticación, se mostrará un mensaje de error adecuado.

> [AZURE.NOTE]El procedimiento para agregar una cuenta de almacenamiento varía en función de la versión de software de StorSimple que está usando. Asegúrese de seguir el procedimiento correcto para su versión de StorSimple.

[AZURE.INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## Editar una cuenta de almacenamiento

Puede editar una cuenta de almacenamiento usada por un contenedor de volúmenes. Si edita una cuenta de almacenamiento que está actualmente en uso, el único campo que puede modificar es la clave de acceso de la cuenta de almacenamiento. Puede proporcionar la nueva clave de acceso de almacenamiento y guardar la configuración actualizada.

#### Para editar una cuenta de almacenamiento

1. En la página de aterrizaje del servicio, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en **Configurar**.

2. Haga clic en **Agregar/editar cuentas de almacenamiento**.

3. En el cuadro de diálogo **Agregar/editar cuentas de almacenamiento**:

  1. En la lista desplegable de **Cuentas de almacenamiento**, elija una cuenta existente que desea modificar. Esto puede incluir también las cuentas de almacenamiento que se generaron automáticamente cuando se creó el servicio.
  2. Si es necesario, puede modificar la selección **Habilitar modo SSL**.
  3. Puede rotar las claves de acceso de la cuenta de almacenamiento. Consulte [Rotación de claves de cuentas de almacenamiento](#key-rotation-of-storage-accounts) para obtener más información sobre cómo realizar la rotación de claves.
  4. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png) para guardar la configuración. La configuración se actualiza en la página **Configurar**. Haga clic en **Guardar** para guardar la configuración recién actualizada.

     ![Editar una cuenta de almacenamiento](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)
  
## Eliminar una cuenta de almacenamiento

> [AZURE.IMPORTANT]Puede eliminar una cuenta de almacenamiento solo si no la usa un contenedor de volúmenes. Si un contenedor de volúmenes está usando una cuenta de almacenamiento, primero elimine el contenedor de volúmenes y, a continuación, elimine la cuenta de almacenamiento asociada.

#### Para eliminar una cuenta de almacenamiento

1. En la página de aterrizaje del servicio de Administrador de StorSimple, seleccione el servicio, haga doble clic en el nombre del servicio y, a continuación, haga clic en **Configurar**.

2. En la lista tabular de cuentas de almacenamiento, desplace el mouse sobre la cuenta que desea eliminar.

3. Aparecerá un icono de eliminación (**x**) en la columna en el extremo derecho para esa cuenta de almacenamiento. Haga clic en el icono **x** para eliminar las credenciales.

4. Cuando se le pida confirmación, haga clic en **Sí** para continuar con la eliminación. La lista tabular se actualizará para reflejar los cambios.

## Rotación de claves de cuentas de almacenamiento

Por motivos de seguridad, la rotación de claves suele ser un requisito en los centros de datos.

> [AZURE.NOTE]La siguiente información de rotación de claves y el procedimiento de rotación se aplican solo a las cuentas de almacenamiento de Microsoft Azure. Si usa otro proveedor de servicios en la nube, puede administrar claves de cuenta de almacenamiento a través del panel de ese proveedor.
 
Cada suscripción de Microsoft Azure puede tener una o más cuentas de almacenamiento asociadas. El acceso a estas cuentas se controla mediante la suscripción y las teclas de acceso de cada cuenta de almacenamiento.

Cuando se crea una cuenta de almacenamiento, Microsoft Azure genera dos claves de acceso de almacenamiento de 512 bits que se usan para la autenticación cuando se accede a la cuenta de almacenamiento. El hecho de tener dos claves de acceso de almacenamiento permite volver a generar las claves sin interrupción en su servicio de almacenamiento, o bien tener acceso a ese servicio. La clave que está actualmente en uso es la clave *principal* y la clave de la copia de seguridad es la clave *secundaria*. Una de estas dos claves debe suministrarse cuando el dispositivo de Microsoft Azure StorSimple tiene acceso a su proveedor de servicios de almacenamiento en la nube.

## ¿Qué es la rotación de claves?

Normalmente, las aplicaciones usan solo una de las claves para acceder a los datos. Después de un período de tiempo, puede hacer que sus aplicaciones pasen a usar la clave secundaria. Después de hacer que sus aplicaciones cambien a la clave secundaria, puede retirar la primera clave y, a continuación, generar una nueva clave. Al usar las dos claves de esta forma permite que las aplicaciones accedan a los datos sin incurrir en tiempo de inactividad.

Las claves de la cuenta de almacenamiento siempre se almacenan en el servicio en un formato cifrado. Sin embargo, pueden restablecerse mediante el servicio de Administrador de StorSimple. El servicio puede obtener la clave principal y secundaria de todas las cuentas de almacenamiento de la misma suscripción, incluidas las cuentas creadas en el servicio de almacenamiento, así como las cuentas de almacenamiento predeterminadas que se generaron cuando se creó el servicio de Administrador de StorSimple. El servicio de StorSimple Manager siempre obtendrá estas claves del Portal de Azure clásico y, a continuación, las almacenará de forma cifrada.

## Flujo de trabajo de rotación

Un administrador de Microsoft Azure puede volver a generar o cambiar la clave principal o secundaria accediendo directamente a la cuenta de almacenamiento (mediante el servicio de almacenamiento de Microsoft Azure). El servicio de Administrador de StorSimple no verá este cambio automáticamente.

Para informar al servicio de Administrador de StorSimple del cambio, tendrá que acceder a dicho servicio, entrar en la cuenta de almacenamiento y sincronizar la clave principal o secundaria (en función de cuál se haya cambiado). A continuación, el servicio obtiene la clave más reciente, cifra las claves y envía la clave cifrada al dispositivo.

#### Para sincronizar las claves de cuentas de almacenamiento de la misma suscripción que el servicio (solo Azure)

1. En la página **Servicios**, haga clic en la pestaña **Configurar**.

2. Haga clic en **Agregar/editar cuentas de almacenamiento**.

3. En el cuadro de diálogo, haga lo siguiente:

  1. Seleccione la cuenta de almacenamiento con la clave que desea sincronizar. Las claves de la cuenta de almacenamiento se muestran cifradas.
  2. En el servicio de Administrador de StorSimple, deberá actualizar la clave que se cambió anteriormente en el servicio de almacenamiento de Microsoft Azure. Si la clave de acceso principal se cambió (se volvió a generar), haga clic en **Sincronizar clave principal**. Si se cambió la clave secundaria, haga clic en **Sincronizar clave secundaria**.

    ![sincronizar claves](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### Para sincronizar las claves de cuentas de almacenamiento fuera de la suscripción al servicio

1. En la página **Servicios**, haga clic en la pestaña **Configurar**.

2. Haga clic en **Agregar/editar cuentas de almacenamiento**.

3. En el cuadro de diálogo, haga lo siguiente:

  1. Seleccione la cuenta de almacenamiento con la clave de acceso que desea actualizar.
  2. Deberá actualizar la clave de acceso de almacenamiento en el servicio de Administrador de StorSimple. En este caso, puede ver la clave de acceso de almacenamiento. Escriba la nueva clave en el cuadro **Clave de acceso de la cuenta de almacenamiento**. 
  3. Guarde los cambios. Ahora debería estar actualizada la clave de acceso de la cuenta de almacenamiento.

## Pasos siguientes

- Obtenga más información acerca de la [Seguridad de StorSimple](storsimple-security.md).
- Obtenga más información sobre el [uso del servicio StorSimple Manager para administrar su dispositivo StorSimple](storsimple-manager-service-administration.md).

<!---HONumber=AcomDC_1203_2015-->