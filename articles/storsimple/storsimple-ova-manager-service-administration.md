<properties 
   pageTitle="Administración de matrices virtuales de StorSimple Manager | Microsoft Azure"
   description="Obtenga información sobre cómo administrar la matriz virtual local de StorSimple mediante el servicio StorSimple Manager en el Portal de Azure clásico."
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
   ms.date="03/01/2016"
   ms.author="alkohli" />

# Uso del servicio StorSimple Manager para administrar la matriz virtual de StorSimple

![flujo del proceso de instalación](./media/storsimple-ova-manager-service-administration/manage4.png)

## Información general

En este artículo se describe la interfaz de servicio StorSimple Manager, incluida la forma de conectarse a ella y las diversas opciones disponibles; además, se proporcionan vínculos a los flujos de trabajo específicos que se pueden realizar a través de esta interfaz de usuario.

Después de leer este artículo, aprenderá a:

- Conexión al servicio StorSimple Manager
- Navegar la interfaz de usuario del Administrador de StorSimple
- Administrar la matriz virtual de StorSimple mediante el servicio StorSimple Manager.

> [AZURE.NOTE] Si desea ver las opciones de administración disponibles para el dispositivo StorSimple de la serie 8000, vaya a [Uso del servicio StorSimple Manager para administrar el dispositivo StorSimple](storsimple-manager-service-administration.md).

## Conexión al servicio StorSimple Manager

El servicio StorSimple Manager se ejecuta en Microsoft Azure y se conecta a varias matrices virtuales de StorSimple. Use un Portal de Microsoft Azure clásico central que se ejecute en un navegador para administrar estos dispositivos. Para conectarse al servicio de Administrador de StorSimple, haga lo siguiente.

#### Para conectarse al servicio

1. Vaya a [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

2. Con las credenciales de su cuenta Microsoft, inicie sesión en el Portal de Microsoft Azure clásico (situado en la parte superior derecha del panel).

3. Desplácese hacia abajo en el panel de navegación izquierdo para obtener acceso al servicio de Administrador de StorSimple.

    ![desplazarse al servicio](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## Navegación de la interfaz de usuario del servicio StorSimple Manager

En la tabla siguiente se muestra la jerarquía de navegación de la IU del servicio de Administrador de StorSimple.

- La página de aterrizaje de **StorSimple Manager** lleva a las páginas de nivel de servicio de la interfaz de usuario que se aplican a todas las matrices virtuales dentro de un servicio.

- La página **Dispositivos** lleva a las páginas de la interfaz de usuario de nivel de dispositivo que se aplican a una matriz virtual específica.

#### Jerarquía de navegación del servicio de Administrador de StorSimple

|Página de aterrizaje|Páginas de nivel de servicio|Páginas de nivel de dispositivo|
|---|---|---|
|Servicio StorSimple Manager|Panel (servicio)|Panel (dispositivo)|
||Dispositivos →|Supervisión|
||Catálogo de copias de seguridad|Recursos compartidos (servidor de archivos) o </br>volúmenes (servidor iSCSI)|
||Configurar (servicio)|Configurar (dispositivo)|
||Trabajos|Mantenimiento|
||Alertas|

## Uso del servicio StorSimple Manager para realizar tareas de administración

En la siguiente tabla se muestra un resumen de todas las tareas comunes de administración y flujos de trabajo complejos que pueden llevarse a cabo en la interfaz de usuario del servicio de Administrador de StorSimple. Estas tareas se organizan en función de las páginas de la IU en que se inician.

Para obtener más información sobre cada flujo de trabajo, haga clic en el procedimiento adecuado en la tabla.

#### Flujos de trabajo del servicio de Administrador de StorSimple

|Si desea hacer esto...|Vaya a esta página de la UI...|Use este procedimiento|
|---|---|---|
|Crear un servicio</br>Eliminar un servicio</br>Obtener la clave de registro del servicio</br>Volver a generar la clave de registro del servicio|Servicio StorSimple Manager|[Implementar el servicio StorSimple Manager](storsimple-ova-manage-service.md)|
|Cambiar la clave de cifrado de datos del servicio</br>Ver los registros de operaciones|Servicio de Administrador de StorSimple → Panel|[Uso del panel de servicios de StorSimple](storsimple-ova-service-dashboard.md)|
|Desactivar una matriz virtual</br>Eliminar una matriz virtual|Servicio de Administrador de StorSimple → Dispositivos|[Desactivar o eliminar una matriz virtual](storsimple-ova-deactivate-and-delete-device.md)|
|Recuperación ante desastres y conmutación por error de dispositivos</br>Requisitos de la conmutación por error</br>Conmutación por error en un dispositivo virtual</br>Recuperación ante desastres y continuidad empresarial (BCDR)</br>Errores durante la recuperación ante desastres|Servicio de Administrador de StorSimple → Dispositivos|[Recuperación ante desastres y conmutación por error de dispositivos para la matriz virtual de StorSimple](storsimple-ova-failover-dr.md)|
|Crear copias de seguridad de los recursos compartidos y volúmenes</br>Crear una copia de seguridad manual</br>Establecer la programación de copias de seguridad</br>Ver copias de seguridad existentes|Servicio StorSimple Manager → Catálogo de copias de seguridad|[Crear una copia de seguridad de la matriz virtual de StorSimple](storsimple-ova-backup.md)|
|Restaurar recursos compartidos a partir de un conjunto de copias de seguridad</br>Restaurar volúmenes a partir de un conjunto de copias de seguridad</br>Recuperación a nivel de elemento (solo servidor de archivos)|Servicio de Administrador de StorSimple → Catálogo de copias de seguridad|[Restaurar desde una copia de seguridad de la matriz virtual de StorSimple](storsimple-ova-restore.md)|
|Acerca de las cuentas de almacenamiento</br>Agregar una cuenta de almacenamiento</br>Editar una cuenta de almacenamiento</br>Eliminar una cuenta de almacenamiento|Servicio de Administrador de StorSimple → Configurar|[Administrar cuentas de almacenamiento para la matriz virtual de StorSimple](storsimple-ova-manage-storage-accounts.md)|
|Acerca de los registros de control de acceso</br>Agregar o modificar un registro de control de acceso </br>Eliminar un registro de control de acceso|Servicio de Administrador de StorSimple → Configurar|[Administrar registros de control de acceso para la matriz virtual de StorSimple](storsimple-ova-manage-acrs.md)|
|Ver detalles del trabajo|Servicio StorSimple Manager → Trabajos| [Administrar trabajos de matriz virtual de StorSimple](storsimple-ova-manage-jobs.md)|
|Configurar alertas</br>Recibir notificaciones de alerta</br>Administrar alertas</br>Revisar alertas|Servicio de Administrador de StorSimple → Alertas|[Ver y administrar alertas para la matriz virtual de StorSimple](storsimple-ova-manage-alerts.md)|
|Modificar la contraseña del administrador de dispositivos|Servicio de Administrador de StorSimple → Dispositivos → Configurar|[Cambiar la contraseña del administrador de dispositivos de la matriz virtual de StorSimple](storsimple-ova-change-device-admin-password.md)|
|Instalación de actualizaciones de software|Servicio de Administrador de StorSimple → Dispositivos → Mantenimiento|[Actualizar la matriz virtual](storsimple-ova-update.md)|

>[AZURE.NOTE] Debe usar la [interfaz de usuario web local](storsimple-ova-web-ui-admin.md) para realizar las tareas siguientes:
>
>- [Recuperar la clave de cifrado de datos de servicio.](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
>- [Crear un paquete de soporte](storsimple-ova-web-ui-admin.md#generate-a-log-package)
>- [Detener y reiniciar una matriz virtual.](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)

##Pasos siguientes
Si desea obtener información sobre la interfaz de usuario web y cómo usarla, vaya a [Uso de la interfaz de usuario web de StorSimple para administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

<!---HONumber=AcomDC_0302_2016-->