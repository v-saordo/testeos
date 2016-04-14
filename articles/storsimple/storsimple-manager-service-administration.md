<properties 
   pageTitle="Administración del servicio Administrador de StorSimple | Microsoft Azure"
   description="Obtenga información sobre cómo administrar su dispositivo StorSimple mediante el servicio Administrador de StorSimple en el Portal de Azure clásico."
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
   ms.date="01/27/2016"
   ms.author="alkohli" />

# Utilizar el servicio de Administrador de StorSimple para administrar su dispositivo StorSimple

## Información general

En este artículo se describe la interfaz del servicio de Administrador de StorSimple, incluyendo cómo conectarse a la misma, las diversas opciones disponibles y los vínculos a los flujos de trabajo específicos que se pueden realizar a través de esta interfaz de usuario. Esta guía es aplicable tanto al dispositivo StorSimple físico como al virtual.

Después de leer este artículo, aprenderá a:

- Conectarse al servicio de Administrador de StorSimple
- Navegar la interfaz de usuario del Administrador de StorSimple
- Administrar su dispositivo StorSimple mediante el servicio de Administrador de StorSimple


## Conectarse al servicio de Administrador de StorSimple

El servicio StorSimple Manager se ejecuta en Microsoft Azure y se conecta a varios dispositivos StorSimple. Use un Portal de Microsoft Azure clásico central que se ejecute en un navegador para administrar estos dispositivos. Para conectarse al servicio de Administrador de StorSimple, haga lo siguiente.

#### Para conectarse al servicio

1. Vaya a [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

1. Con las credenciales de su cuenta Microsoft, inicie sesión en el Portal de Microsoft Azure clásico (situado en la parte superior derecha del panel).

1. Desplácese hacia abajo en el panel de navegación izquierdo para obtener acceso al servicio de Administrador de StorSimple.


## Navegar por la UI del servicio de Administrador de StorSimple

En la tabla siguiente se muestra la jerarquía de navegación de la IU del servicio de Administrador de StorSimple.

- La página de aterrizaje del **Administrador de StorSimple** lleva a las páginas de nivel de servicio de la IU aplicables a todos los dispositivos dentro de un servicio.

- La página **Dispositivos** lleva a las páginas de la IU de nivel de dispositivo aplicables a un dispositivo específico.

- La página **Contenedores de volúmenes** lleva a la página de volúmenes que muestra todos los volúmenes asociados con un dispositivo.


#### Jerarquía de navegación del servicio de Administrador de StorSimple

|Página de aterrizaje|Páginas de nivel de servicio|Páginas de nivel de dispositivo|Páginas de nivel de dispositivo|
|---|---|---|---|
|Servicio StorSimple Manager|Panel del servicio|Panel del dispositivo||
|Dispositivos →|Supervisión|
|Catálogo de copias de seguridad|Contenedores de volúmenes→|Volúmenes|
|Configurar (servicio)|Directivas de copia de seguridad||
|Trabajos|Configurar (dispositivo)|
|Alertas|Mantenimiento|

![Vídeo disponible](./media/storsimple-manager-service-administration/Video_icon.png) **Vídeo disponible**

Para ver un vídeo que le guiará a través de la interfaz de usuario del servicio StorSimple Manager, haga clic [aquí](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/).

## Administrar el dispositivo StorSimple mediante el servicio de Administrador de StorSimple

En la siguiente tabla se muestra un resumen de todas las tareas comunes de administración y flujos de trabajo complejos que pueden llevarse a cabo en la interfaz de usuario del servicio de Administrador de StorSimple. Estas tareas se organizan en función de las páginas de la IU en que se inician.

Para obtener más información sobre cada flujo de trabajo, haga clic en el procedimiento adecuado en la tabla.

#### Flujos de trabajo del servicio de Administrador de StorSimple

|Si desea hacer esto...|Vaya a esta página de la UI...|Utilice este procedimiento.|
|---|---|---|
|Crear un servicio</br>Eliminar un servicio</br>Obtener la clave de registro del servicio</br>Regenerar la clave de registro del servicio regenerar|Servicio StorSimple Manager|[Implementar un servicio del Administrador de StorSimple](storsimple-manage-service.md)
|Cambiar la clave de cifrado de datos del servicio</br>Ver los registros de operaciones|Servicio de Administrador de StorSimple → Panel|[Uso del panel del servicio StorSimple Manager](storsimple-service-dashboard.md)|
|Desactivar un dispositivo</br>Eliminar un dispositivo|Servicio de Administrador de StorSimple → Dispositivos|[Desactivar o eliminar un dispositivo](storsimple-deactivate-and-delete-device.md)|
|Obtener información sobre recuperación ante desastres y conmutación por error</br>Conmutación por error a un dispositivo físico</br>Conmutación por error a un dispositivo virtual</br>Recuperación ante desastres y continuidad empresarial (BCDR)|Servicio de Administrador de StorSimple → Dispositivos|[Conmutación por error y recuperación ante desastres para el dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md)|
|Enumerar copias de seguridad para un volumen</br>Seleccionar un conjunto de copias de seguridad</br>Eliminar un conjunto de copias de seguridad|Servicio de Administrador de StorSimple → Catálogo de copias de seguridad|[Administrar copias de seguridad](storsimple-manage-backup-catalog.md)|
|Clonar un volumen|Servicio de Administrador de StorSimple → Catálogo de copias de seguridad|[Clonar un volumen](storsimple-clone-volume.md)|
|Restaurar un conjunto de copias de seguridad|Servicio de Administrador de StorSimple → Catálogo de copias de seguridad|[Restaurar un conjunto de copias de seguridad](storsimple-restore-from-backup-set.md)|
|Acerca de las cuentas de almacenamiento</br>Agregar una cuenta de almacenamiento</br>Editar una cuenta de almacenamiento</br>Eliminar una cuenta de almacenamiento</br>Rotación de claves de las cuentas de almacenamiento|Servicio de Administrador de StorSimple → Configurar|[Administrar cuentas de almacenamiento](storsimple-manage-storage-accounts.md)|
|Acerca de las plantillas de ancho de banda</br>Agregar una plantilla de ancho de banda</br>Editar una plantilla de ancho de banda</br>Eliminar una plantilla de ancho de banda</br>Utilizar una plantilla de ancho de banda predeterminada</br>Crear una plantilla de ancho de banda para todo el día que comienza a una hora especificada|Servicio de Administrador de StorSimple → Configurar|[Administrar plantillas de ancho de banda](storsimple-manage-bandwidth-templates.md)|
|Acerca de los registros de control de acceso</br>Crear un registro de control de acceso</br>Editar un registro de control de acceso</br>Eliminar un registro de control de acceso|Servicio de Administrador de StorSimple → Configurar|[Administrar registros de control de acceso](storsimple-manage-acrs.md)|
|Ver detalles del trabajo</br>Cancelar un trabajo|Servicio StorSimple Manager → Trabajos|[Trabajos de administración](storsimple-manage-jobs.md)
|Recibir notificaciones de alerta</br>Administrar alertas</br>Revisar alertas|Servicio de Administrador de StorSimple → Alertas|[Ver y administrar alertas de StorSimple](storsimple-manage-alerts.md)
|Ver iniciadores conectados</br>Buscar el número de serie del dispositivo</br>Encontrar el IQN de destino|Servicio de Administrador de StorSimple → Dispositivos → Panel|[Utilizar el panel del dispositivo StorSimple](storsimple-device-dashboard.md)|
|Gráficos de supervisión de dispositivos|Servicio de Administrador de StorSimple → Dispositivos → Supervisión|[Supervisar su dispositivo StorSimple](storsimple-monitor-device.md)|
|Agregar un contenedor de volúmenes</br>Modificar un contenedor de volúmenes</br>Eliminar un contenedor de volúmenes|Servicio de Administrador de StorSimple → Dispositivos → Contenedores de volúmenes|[Administrar contenedores de volúmenes](storsimple-manage-volume-containers.md)|
|Agregar un volumen</br>Modificar un volumen</br>Desconectar un volumen</br>Eliminar un volumen</br>Supervisar un volumen|Servicio de Administrador de StorSimple → Dispositivos → Contenedores de volúmenes → Volúmenes|[Administrar volúmenes](storsimple-manage-volumes.md)|
|Modificar la configuración de dispositivo</br>Modificar la configuración de tiempo</br>Modificar la configuración DNS.md</br>Configurar interfaces de red|Servicio de Administrador de StorSimple → Dispositivos → Configurar|[Modificar la configuración de dispositivo de su dispositivo StorSimple device](storsimple-modify-device-config.md)|
|Ver la configuración de proxy web|Servicio de Administrador de StorSimple → Dispositivos → Configurar|[Configurar el proxy web para el dispositivo](storsimple-configure-web-proxy.md)|
|Modificar la contraseña del administrador de dispositivos</br>Modificar la contraseña de Snapshot Manager de StorSimple|Servicio de Administrador de StorSimple → Dispositivos → Configurar|[Cambiar las contraseñas de StorSimple](storsimple-change-passwords.md)|
|Configuración de la administración remota|Servicio de Administrador de StorSimple → Dispositivos → Configurar|[Conectarse de forma remota al dispositivo StorSimple](storsimple-remote-connect.md)|
|Configurar alertas|Servicio de Administrador de StorSimple → Dispositivos → Configurar|[Ver y administrar alertas de StorSimple](storsimple-manage-alerts.md)|
|Configurar CHAP para el dispositivo StorSimple|Servicio de Administrador de StorSimple → Dispositivos → Configurar|[Configurar CHAP para el dispositivo StorSimple](storsimple-configure-chap.md)|
|Agregar una directiva de copia de seguridad</br>Agregar o modificar una programación</br>Eliminar una directiva de copia de seguridad</br>Realizar una copia de seguridad manual</br>Crear una directiva de copia de seguridad personalizada con varios programas y volúmenes|Servicio de Administrador de StorSimple → Dispositivos → Directivas de copia de seguridad|[Administrar directivas de copia de seguridad](storsimple-manage-backup-policies.md)|
|Detener los controladores de dispositivo</br>Reiniciar los controladores de dispositivo</br>Apagar los controladores de dispositivo</br>Restablecer el dispositivo a los valores predeterminados de fábrica</br>(Lo anterior es solo para dispositivos locales)|Servicio de Administrador de StorSimple → Dispositivos → Mantenimiento|[Administrar controladores de dispositivo StorSimple](storsimple-manage-device-controller.md)|
|Obtener información sobre los componentes de hardware de StorSimple</br>Supervisar el estado del hardware</br>(Lo anterior es solo para dispositivos locales)|Servicio de Administrador de StorSimple → Dispositivos → Mantenimiento|[Supervisar componentes de hardware](storsimple-monitor-hardware-status.md)|
|Crear un paquete de soporte|Servicio de Administrador de StorSimple → Dispositivos → Mantenimiento|[Crear y administrar paquetes de soporte técnico](storsimple-create-manage-support-package.md)|
|Instalación de actualizaciones de software|Servicio de Administrador de StorSimple → Dispositivos → Mantenimiento|[Actualizar su dispositivo](storsimple-update-device.md)|


##Pasos siguientes
Si tiene algún problema con la operación diaria de su dispositivo StorSimple o con cualquiera de sus componentes de hardware, consulte:

- [Solución de problemas de un dispositivo de StorSimple operativo](storsimple-troubleshoot-operational-device.md)
- [Utilizar los LED Indicadores de supervisión de StorSimple](storsimple-monitoring-indicators.md)

Si no puede resolver los problemas y necesita crear una solicitud de servicio, póngase en contacto con el [Servicio de soporte técnico de Microsoft](storsimple-contact-microsoft-support.md).

<!---HONumber=AcomDC_0204_2016-->