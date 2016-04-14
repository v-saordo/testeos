<properties 
   pageTitle="Implementación del dispositivo StorSimple (Actualización 2) | Microsoft Azure"
   description="Describe los pasos y procedimientos recomendados para implementar el servicio y el dispositivo de la actualización 2 de StorSimple."
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
   ms.workload="NA"
   ms.date="02/22/2016"
   ms.author="alkohli" />

# Implementar el dispositivo StorSimple local (Actualización 2)

> [AZURE.SELECTOR]
- [Update 2](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [Update 1](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [GA Release](../articles/storsimple/storsimple-deployment-walkthrough.md)

## Información general

Bienvenido a la implementación del dispositivo StorSimple de Microsoft Azure. Estos tutoriales de implementación se aplican a la actualización 2 de la serie StorSimple 8000. En esta serie de tutoriales se describe se incluye una lista de comprobación de configuración, requisitos previos de configuración y los pasos de configuración detallados para su dispositivo StorSimple.

La información de estos tutoriales da por supuesto que revisó las precauciones de seguridad, y que desempaquetó, montó y cableó el dispositivo StorSimple. Si todavía necesita realizar dichas tareas, empiece por revisar las [precauciones de seguridad](storsimple-safety.md). Siga las instrucciones específicas del dispositivo para desempaquetar, montar en bastidor y cablear el dispositivo.

- [Desempaquetado, montaje en bastidor y cableado del 8100](storsimple-8100-hardware-installation.md)
- [Desempaquetado, montaje en bastidor y cableado del 8600](storsimple-8600-hardware-installation.md)

Necesitará privilegios de administrador para completar el proceso de instalación y configuración. Se recomienda que revise la lista de comprobación de configuración antes de comenzar. El proceso de implementación y configuración puede tardar algún tiempo en completarse.

> [AZURE.NOTE] La información de implementación de StorSimple publicada en el sitio web de Microsoft Azure se aplica solo a los dispositivos StorSimple de la serie 8000. Para obtener información completa sobre los dispositivos de la serie 7000, vaya a: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Para obtener información sobre la implementación de la serie 7000, vea la [Guía de inicio rápido del sistema StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).

## Pasos de implementación

Siga estos pasos obligatorios para configurar el dispositivo StorSimple y conectarlo al servicio StorSimple Manager: Además de los pasos obligatorios, hay pasos y procedimientos opcionales que puede que necesite llevar a cabo durante la implementación. Las instrucciones detalladas de implementación indican cuándo debe realizar cada uno de estos pasos opcionales.


| Paso | Descripción |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **REQUISITOS PREVIOS** | Se deben llevar a cabo como preparación para la próxima implementación. |
| [Lista de comprobación de la configuración de implementación](#deployment-configuration-checklist) | Use esta lista de comprobación para recopilar y registrar información antes y durante la implementación. |
| [Requisitos previos de implementación](#deployment-prerequisites) | Validan que el entorno está listo para la implementación. |
| | |
| **IMPLEMENTACIÓN PASO A PASO** | Estos pasos son obligatorios para implementar el dispositivo StorSimple en producción. |
| [Paso 1: Crear un nuevo servicio](#step-1-create-a-new-service) | Configure la administración y el almacenamiento en la nube para el dispositivo StorSimple. *Omita este paso si tiene un servicio existente para otros dispositivos StorSimple*. |
| [Paso 2: Obtener la clave de registro del servicio](#step-2-get-the-service-registration-key) | Esta clave se usa para registrar y conectar el dispositivo StorSimple con el servicio de administración. |
| [Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) | Conecte el dispositivo a la red y regístrelo con Azure para completar la instalación mediante el servicio de administración. |
| [Paso 4: Completar el programa de instalación mínima del dispositivo](#step-4-complete-minimum-device-setupd)</br>[Opcional: actualización del dispositivo StorSimple](#scan-for-and-apply-updates) | Use el servicio de administración para realizar la instalación del dispositivo y habilitarlo para proporcionar almacenamiento. |
| [Paso 5: Crear un contenedor de volúmenes](#step-5-create-a-volume-container) | Cree un contenedor para aprovisionar los volúmenes. Un contenedor de volúmenes tiene la configuración de la cuenta de almacenamiento, el ancho de banda y el cifrado de todos los volúmenes que contiene. |
| [Paso 6: Crear un volumen](#step-6-create-a-volume) | Aprovisione volúmenes de almacenamiento en el dispositivo StorSimple para los servidores. |
| [Paso 7: Montar, inicializar y formatear un volumen](#step-7-mount-initialize-and-format-a-volume)</br>[Opcional: configurar MPIO](storsimple-configure-mpio-windows-server.md) | Conecte los servidores al almacenamiento iSCSI proporcionado por el dispositivo. Opcionalmente, configure MPIO para asegurarse de que los servidores pueden tolerar errores de vínculo, red e interfaz. |
| [Paso 8: Realizar una copia de seguridad](#step-8-take-a-backup) | Configure la directiva de copia de seguridad para proteger los datos. |
| | |
| **OTROS PROCEDIMIENTOS** | Puede que necesite hacer referencia a estos procedimientos mientras implementa la solución. |
| [Configurar una nueva cuenta de almacenamiento para el servicio](#configure-a-new-storage-account-for-the-service) | |
| [Usar PuTTY para conectarse a la consola serie del dispositivo](#use-putty-to-connect-to-the-device-serial-console) | |
| [Obtener el IQN de un host de Windows Server](#get-the-iqn-of-a-windows-server-host) | |
| [Crear una copia de seguridad manual](#create-a-manual-backup) | 


## Lista de comprobación de la configuración de implementación

Antes de implementar el dispositivo, debe recopilar información para configurar el software en el dispositivo StorSimple. Si prepara alguna de esta información con antelación, simplificará el proceso de implementar el dispositivo StorSimple en el entorno. Descargue y use esta lista de comprobación para anotar los detalles de configuración cuando implemente el dispositivo.

- [Descargar la lista de comprobación de la configuración de implementación de StorSimple](http://www.microsoft.com/download/details.aspx?id=49159)


## Requisitos previos de implementación

Las siguientes secciones explican los requisitos previos de configuración para el servicio de Administrador de StorSimple y el dispositivo StorSimple.

### Para el servicio de Administrador de StorSimple

Antes de comenzar, asegúrese de que:

- Tiene una cuenta Microsoft con credenciales de acceso.

- Tiene una cuenta de almacenamiento de Microsoft Azure con credenciales de acceso.

- Su suscripción de Microsoft Azure está habilitada para el servicio de Administrador de StorSimple. Debe adquirir la suscripción a través del [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Tiene acceso a software de emulación de terminales, como PuTTY.

### Para el dispositivo en el centro de datos

Antes de configurar el dispositivo, asegúrese de que está completamente desempaquetado, montado en un bastidor y totalmente cableado para alimentación, red y acceso serie, tal como se describe en:

-  [Desempaquetado, montaje en bastidor y cableado del dispositivo 8100](storsimple-8100-hardware-installation.md)
-  [Desempaquetado, montaje en bastidor y cableado del dispositivo 8600](storsimple-8600-hardware-installation.md)


### Para la red del centro de datos

Antes de comenzar, asegúrese de que:

- Se abren los puertos del firewall del centro de datos para permitir el tráfico de iSCSI y de la nube, como se describe en [Requisitos de red para el dispositivo StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).


## Implementación paso a paso

Use las siguientes instrucciones paso a paso para implementar el dispositivo StorSimple en el centro de datos.

## Paso 1: Crear un nuevo servicio

El servicio de Administrador de StorSimple puede administrar varios dispositivos de StorSimple. Siga estos pasos para crear una nueva instancia del servicio de Administrador de StorSimple.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] Si no habilitó la creación automática de una cuenta de almacenamiento con el servicio, debe crear al menos una cuenta de almacenamiento después de crear correctamente un servicio. Dicha cuenta de almacenamiento se usará al crear un contenedor de volúmenes.
>
> * Si no creó automáticamente una cuenta de almacenamiento, vaya a [Configurar una nueva cuenta de almacenamiento para el servicio](#configure-a-new-storage-account-for-the-service) para obtener instrucciones detalladas. 
> * Si habilitó la creación automática de una cuenta de almacenamiento, vaya al [Paso 2: Obtener la clave de registro del servicio](#step-2-get-the-service-registration-key).

## Paso 2: Obtener la clave de registro del servicio

Una vez esté en funcionamiento el servicio de Administrador de StorSimple, necesitará la clave de registro del servicio. Esta clave se usa para registrar y conectar el dispositivo StorSimple con el servicio.

Siga estos pasos en el Portal de administración.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## Paso 3: Configurar y registrar el dispositivo a través de Windows PowerShell para StorSimple

Use Windows PowerShell para StorSimple para completar la configuración inicial del dispositivo StorSimple, tal como se explica en el procedimiento siguiente. Deberá usar software de emulación de terminales para completar este paso. Para obtener más información, consulte [Uso de PuTTY para conectarse a la consola serie del dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## Paso 4: Completar la instalación mínima del dispositivo

Para proceder a la configuración mínima del dispositivo StorSimple, debe hacer lo siguiente:

- Configurar el servidor DNS secundario.
- Habilitar iSCSI en al menos una interfaz de red.
- Asignar direcciones IP fijas a ambos controladores.

Siga estos pasos en el Portal de administración para completar la instalación mínima del dispositivo.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## Paso 5: Crear un contenedor de volúmenes

Un contenedor de volúmenes tiene la configuración de la cuenta de almacenamiento, el ancho de banda y el cifrado de todos los volúmenes que contiene. Deberá crear un contenedor de volúmenes para poder empezar a aprovisionar volúmenes en el dispositivo StorSimple.

Siga estos pasos en el Portal de administración para crear un contenedor de volúmenes.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## Paso 6: Crear un volumen

Después de crear un contenedor de volúmenes, puede aprovisionar un volumen de almacenamiento en el dispositivo StorSimple para los servidores. Siga estos pasos en el Portal de administración para crear un volumen.

> [AZURE.IMPORTANT] StorSimple Manager puede crear tanto volúmenes con aprovisionamiento fino como completo. Sin embargo, no se pueden crear volúmenes aprovisionados parcialmente.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume-u2.md)]

## Paso 7: Montar, inicializar y formatear un volumen

Los siguientes pasos se realizan en el host de Windows Server.


> [AZURE.IMPORTANT]

> - Para obtener la alta disponibilidad de la solución de StorSimple, se recomienda que configure MPIO en los servidores host (opcional) antes de configurar iSCSI. La configuración MPIO en los servidores host garantizará que los servidores pueden tolerar errores de vínculos, redes o interfaces.

> - Para instrucciones de instalación y configuración de MPIO e iSCSI en el host Windows Server, vaya a [Configurar MPIO para el dispositivo StorSimple](storsimple-configure-mpio-windows-server.md). También se incluyen los pasos para montar, inicializar y formatear volúmenes StorSimple.

> - Para instrucciones de instalación y configuración de MPIO e iSCSI en un host Linux, vaya a [Configurar MPIO para el host Linux de StorSimple](storsimple-configure-mpio-on-linux.md).

Si decide no configurar MPIO, realice los pasos siguientes para montar, inicializar y formatear los volúmenes StorSimple en un host Windows Server.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## Paso 8: Realizar una copia de seguridad

Las copias de seguridad proporcionan seguridad para los volúmenes a partir de un momento específico y mejoran la capacidad de recuperación al mismo tiempo que reducen los tiempos de restauración. Puede realizar dos tipos de copia de seguridad en el dispositivo StorSimple: instantáneas locales e instantáneas en la nube. Cada uno de estos tipos de copia de seguridad puede ser **Programada** o **Manual**.

Siga estos pasos en el Portal de administración para crear una copia de seguridad programada.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Puede realizar una copia de seguridad manual en cualquier momento. Para conocer los procedimientos, vaya a [Crear una copia de seguridad manual](#create-a-manual-backup).

## Configurar una nueva cuenta de almacenamiento para el servicio

Se trata de un paso opcional que debe llevar a cabo únicamente si no habilitó la creación automática de una cuenta de almacenamiento con su servicio. Se requiere una cuenta de almacenamiento de Microsoft Azure para crear un contenedor de volúmenes de StorSimple.

Si necesita crear una cuenta de almacenamiento de Azure en una región distinta, vea [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md) para obtener instrucciones paso a paso.

Siga estos pasos en el Portal de administración, en la página **Servicio de Administrador de StorSimple**.

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## Usar PuTTY para conectarse a la consola serie del dispositivo

Para conectarse a Windows PowerShell para StorSimple, deberá usar software de emulación de terminales, como PuTTY. Puede usar PuTTY cuando acceda al dispositivo directamente a través de la consola serie o abriendo una sesión de Telnet desde un equipo remoto.

[AZURE.INCLUDE [Usar PuTTY para conectarse a la consola serie del dispositivo](../../includes/storsimple-use-putty.md)]


## Búsqueda y aplicación de actualizaciones

La actualización del dispositivo puede tardar varias horas. Realice los pasos siguientes para detectar y aplicar las actualizaciones en el dispositivo.<!--can take 1-4 hours-->

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### Para actualizar su dispositivo

1.	En la página **Inicio rápido** del dispositivo, haga clic en **Dispositivos**. Seleccione el dispositivo físico, haga clic en **Mantenimiento** y luego en **Buscar actualizaciones**.  

2.	Se crea un trabajo para buscar las actualizaciones disponibles. Si hay actualizaciones disponibles, la opción **Buscar actualizaciones** cambia a **Instalar actualizaciones**. Haga clic en **Instalar actualizaciones**.

3.	Se creará un trabajo de actualización. Vaya a **Trabajos** para supervisar el estado de la actualización.

	> [AZURE.NOTE] Cuando se inicia el trabajo de actualización, se muestra inmediatamente el estado como 50 por ciento. El estado cambia al 100 por cien una vez completado el trabajo de actualización. No hay ningún estado en tiempo real para el proceso de actualizaciones.

4.	Después de que el dispositivo se actualiza correctamente, habilite las interfaces de red Data 2 y Data 3 si estaban deshabilitadas.

<!-- In step 2, you may be requested to disable Data 2 and Data 3 prior to installing the updates. You must disable these network interfaces or the updates may fail.-->

## Obtener el IQN de un host de Windows Server

Siga estos pasos para obtener el nombre completo del iSCSI (IQN) de un host de Windows que ejecute Windows Server® 2012.

[AZURE.INCLUDE [Crear una copia de seguridad manual](../../includes/storsimple-get-iqn.md)]

## Crear una copia de seguridad manual

Siga estos pasos en el Portal de administración para crear una copia de seguridad manual a petición para un único volumen en el dispositivo StorSimple.

[AZURE.INCLUDE [Crear una copia de seguridad manual](../../includes/storsimple-create-manual-backup.md)]


## Pasos siguientes

- Configure un [dispositivo virtual](storsimple-virtual-device.md).

- Use el [servicio de Administrador de StorSimple](storsimple-manager-service-administration.md) para administrar el dispositivo StorSimple.
 

<!---HONumber=AcomDC_0224_2016-->