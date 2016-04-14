<properties
   pageTitle="Implementar una matriz virtual de StorSimple 1: Preparación del portal"
   description="Primer tutorial para implementar la matriz virtual de StorSimple trata sobre la preparación del portal"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/01/2016"
   ms.author="alkohli"/>

# Implementación de la matriz virtual de StorSimple: preparación del portal

![](./media/storsimple-ova-deploy1-portal-prep/getstarted4.png)

## Información general 

Este artículo se aplica a la matriz virtual de Microsoft Azure StorSimple (también conocida como dispositivo virtual local de StorSimple o dispositivo virtual de StorSimple) que se ejecuta en la versión de disponibilidad general de marzo de 2016. Este es el primer artículo de una serie de tutoriales de implementación necesarios para implementar completamente la matriz virtual como servidor de archivos o servidor iSCSI. En este artículo se describe la preparación necesaria para crear y configurar el servicio de StorSimple Manager antes de aprovisionar una matriz virtual. Este artículo también presenta vínculos a una lista de comprobación de configuración de implementación, así como a los requisitos previos de configuración.

Necesitará privilegios de administrador para completar el proceso de instalación y configuración. Se recomienda que revise la lista de comprobación de configuración antes de comenzar. La preparación del portal le tomará menos de 10 minutos.

La información de la implementación de StorSimple publicada en este artículo solo se aplica a las matrices virtual de StorSimple.

### Primeros pasos

El flujo de trabajo de implementación consta de preparar el portal, aprovisionar una matriz virtual en su entorno virtualizado y completar la instalación. Para comenzar con la implementación de la matriz virtual de StorSimple como servidor de archivos o servidor iSCSI, debe hacer referencia a los siguientes recursos tabulados (artículos y vídeos).

#### Artículos de implementación

Consulte los artículos siguientes en la secuencia recomendada para implementar la matriz virtual de StorSimple.

| **de streaming** | **En este paso** | **Hará esto…** | **Use estos documentos.**|
|------|-------------------------------------------|--------------------------------------------------------------------------------|------------------------|
|1\. | **Configurar el Portal de Azure clásico** | Cree y configure el servicio de StorSimple Manager antes de aprovisionar un dispositivo virtual StorSimple. |[Preparar el portal](storsimple-ova-deploy1-portal-prep.md)| 
|2\. | **Aprovisionar la matriz virtual** | Para Hyper-V, aprovisione y conéctese a un dispositivo virtual StorSimple en un sistema host que ejecute Hyper-V 2008 R2, Hyper-V 2012 o Hyper-V 2012 R2. <br></br> <br></br> Para VMware, aprovisione y conéctese a un dispositivo virtual local de StorSimple en un sistema host que ejecute VMware ESXi 5.5 o versiones posteriores.<br></br>| [Implementar una matriz virtual de StorSimple: Aprovisionar una matriz virtual en Hyper-V (versión preliminar)](storsimple-ova-deploy2-provision-hyperv.md) <br></br> <br></br> [Implementar una matriz virtual de StorSimple: Aprovisionar una matriz virtual en VMware (versión preliminar)](storsimple-ova-deploy2-provision-vmware.md)|
|3\. | **Configurar la matriz virtual** | Para el servidor de archivos, realice la instalación inicial, registre el servidor de archivos de StorSimple y complete la instalación del dispositivo. A continuación, puede aprovisionar los recursos compartidos de SMB. <br></br> <br></br> Para el servidor iSCSI, realice la configuración inicial, registre el servidor iSCSI de StorSimple y complete la configuración del dispositivo. A continuación, puede aprovisionar los volúmenes iSCSI.| [Implementar una matriz virtual de StorSimple: Configurar un servidor de archivos (versión preliminar)](storsimple-ova-deploy3-fs-setup.md)<br></br> <br></br>[Implementar una matriz virtual de StorSimple: Configurar el dispositivo virtual como servidor iSCSI (versión preliminar)](storsimple-ova-deploy3-iscsi-setup.md)|

#### Vídeos de implementación

| **Para completar este paso…** | **Vea este vídeo.**|
|----------------|-------------|
| Instrucciones paso a paso para empezar a trabajar con la matriz virtual de StorSimple. | [Introducción a la matriz virtual de StorSimple](https://azure.microsoft.com/documentation/videos/get-started-with-the-storsimple-virtual-array/)|
| Instrucciones paso a paso para aprovisionar la matriz virtual de StorSimple en Hyper-V.|[Crear una matriz virtual de StorSimple](https://azure.microsoft.com/documentation/videos/create-a-storsimple-virtual-array/) |
|Instrucciones paso a paso para configurar y registrar una matriz virtual de StorSimple.|[Configurar una matriz virtual de StorSimple](https://azure.microsoft.com/documentation/videos/configure-a-storsimple-virtual-array/)|
|Instrucciones paso a paso para crear recursos compartidos, realizar copias de seguridad de recursos compartidos y restaurar datos en una matriz virtual de StorSimple configurada como servidor de archivos.|[Utilizar una matriz virtual de StorSimple](https://azure.microsoft.com/documentation/videos/use-the-storsimple-virtual-array/)|
|Instrucciones paso a paso para la recuperación ante desastres y conmutación por error de una matriz virtual de StorSimple.|[Recuperación ante desastres de una matriz virtual de StorSimple](https://azure.microsoft.com/documentation/videos/storsimple-virtual-array-disaster-recovery/)

Ahora puede empezar a configurar el Portal de Azure clásico.

## Lista de comprobación de la configuración

La lista de comprobación de configuración describe la información que necesita recopilar antes de configurar el software en el dispositivo StorSimple. Si prepara esta información con antelación, simplificará el proceso de implementar el dispositivo StorSimple en el entorno. Dependiendo de si el dispositivo virtual StorSimple se va a implementar como servidor de archivos o como servidor iSCSI, necesitará una de las siguientes listas de comprobación.

-   Descargue la [lista de comprobación de configuración del servidor de archivos de la matriz virtual de StorSimple](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).

-   Descargue la [lista de comprobación de configuración del servidor iSCSI de la matriz virtual de StorSimple](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## Requisitos previos

Aquí encontrará los requisitos previos de configuración del servicio StorSimple Manager, el dispositivo virtual StorSimple y la red del centro de datos.

### Para el servicio de Administrador de StorSimple

Antes de comenzar, asegúrese de que:

-   Tiene una cuenta Microsoft con credenciales de acceso.

-   Tiene una cuenta de almacenamiento de Microsoft Azure con credenciales de acceso.

-   Su suscripción de Microsoft Azure debe estar habilitada para el servicio de StorSimple Manager.

### Para el dispositivo virtual StorSimple 

Antes de implementar un dispositivo virtual, asegúrese de que:

-   Tiene acceso a un sistema host que ejecuta Hyper-V (2008 R2 o superior) o VMware (ESXi 5.5 o superior) que pueda utilizarse para aprovisionar un dispositivo.

-   El sistema host es capaz de utilizar los recursos siguientes para aprovisionar el dispositivo virtual:
	
	-   Un mínimo de 4 núcleos.
	
	-   Al menos 8 GB de RAM.
	
	-   Una interfaz de red.
	
	-   Un disco virtual de 500 GB para datos del sistema.

### Para la red del centro de datos 

Antes de comenzar, asegúrese de que:

-   La red en su centro de datos esté configurada según los requisitos de red para el dispositivo StorSimple. Para más información, consulte [Requisitos del sistema de la matriz virtual de StorSimple](storsimple-ova-system-requirements.md).

-   El dispositivo virtual StorSimple tenga un ancho de banda de Internet de 5 Mbps (o más) disponible en todo momento. Este ancho de banda no debe compartirse con otras aplicaciones.

## Preparación de paso a paso

Utilice las siguientes instrucciones paso a paso para preparar su portal para el servicio de StorSimple Manager.

## Paso 1: Crear un nuevo servicio

Una única instancia del servicio de StorSimple Manager puede administrar 1200 dispositivos StorSimple. Siga estos pasos para crear una nueva instancia del servicio de Administrador de StorSimple. Si tiene un servicio StorSimple Manager existente para administrar los 1200 dispositivos, omita este paso y vaya al [Paso 2: Obtener la clave de registro del servicio](#step-2-get-the-service-registration-key).

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

> [AZURE.IMPORTANT]
> 
> Si no habilitó la creación automática de una cuenta de almacenamiento con el servicio, debe crear al menos una cuenta de almacenamiento después de crear correctamente un servicio.
> 

> - Si no creó automáticamente una cuenta de almacenamiento, vaya a [Configurar una nueva cuenta de almacenamiento para el servicio](#optional-step-configure-a-new-storage-account-for-the-service) para obtener instrucciones detalladas.
> 

> - Si habilitó la creación automática de una cuenta de almacenamiento, vaya al [Paso 2: Obtener la clave de registro del servicio](#step-2-get-the-service-registration-key).


## Paso 2: Obtener la clave de registro del servicio


Una vez esté en funcionamiento el servicio de Administrador de StorSimple, necesitará la clave de registro del servicio. Esta clave se usa para registrar y conectar el dispositivo StorSimple con el servicio.

Siga estos pasos en el [Portal de Azure clásico](https://manage.windowsazure.com/).


[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

> [AZURE.NOTE]
> 
> La clave de registro del servicio se usa para registrar todos los dispositivos StorSimple Manager que deben registrarse en el servicio StorSimple Manager.

## Paso 3: Descargar la imagen del dispositivo virtual

Una vez que tiene la clave de registro del servicio, debe descargar la imagen apropiada del dispositivo virtual para aprovisionar un dispositivo virtual en el sistema host. Las imágenes de dispositivos virtuales son específicas del sistema operativo y pueden descargarse desde la página Inicio rápido en el Portal de Azure clásico.

> [AZURE.IMPORTANT] El software que se ejecuta en la matriz virtual de StorSimple solo puede usarse junto con el servicio StorSimple Manager.


Siga estos pasos en el [Portal de Azure clásico](https://manage.windowsazure.com/).

#### Para obtener la imagen del dispositivo virtual

1.  En la página **Servicio de Administrador de StorSimple**, haga clic en el servicio que creó. Esto le llevará a la página **Inicio rápido**. (Puede hacer clic en el icono de inicio rápido ![](./media/storsimple-ova-deploy1-portal-prep/image8.png) para acceder a la página **Inicio rápido** en cualquier momento).


1.  Descargue el VHD o VHDX apropiado en un recurso compartido de red en su centro de datos. Hay imágenes independientes disponibles para:

	-   Hyper-V 2012 y versiones posteriores
	
	-   Hyper-V 2008 R2 y versiones posteriores

	-   VMWare ESXi 5.5 y versiones posteriores

	> [AZURE.IMPORTANT] El software que se ejecuta en la matriz virtual de StorSimple solo puede usarse junto con el servicio StorSimple Manager.


1.  Haga clic en la imagen para el sistema operativo host que usará para aprovisionar el dispositivo virtual. Esto le llevará al Centro de descarga de Microsoft.

1.  Si usa Hyper-V, descargue el VHDX para Hyper-V 2012 o el VHD para Hyper-V 2008 R2 y versiones posteriores. Si utiliza VMware, descargue el VMDK. El VHDX es un archivo comprimido de 4,77 GB, al igual que el VHD, y el VMDK es un archivo de 4,75 GB. El tiempo para descargar el archivo depende de la conexión a Internet.

2.  Descomprima el archivo y tome nota de la ubicación sin comprimir en la unidad local.

![icono de vídeo](./media/storsimple-ova-deploy1-portal-prep/video_icon.png) **Vídeo disponible**

Mire el vídeo para obtener instrucciones paso a paso para empezar a trabajar con la matriz virtual de StorSimple.

> [AZURE.VIDEO get-started-with-the-storsimple-virtual-array]



## Paso opcional: Configurar una cuenta de almacenamiento nueva para el servicio

Se trata de un paso opcional que debe llevar a cabo únicamente si no habilitó la creación automática de una cuenta de almacenamiento con su servicio.

Si necesita crear una cuenta de almacenamiento de Azure en una región distinta, consulte [Crear una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account) para instrucciones detalladas.

Siga estos pasos en el [Portal de Azure clásico](https://manage.windowsazure.com/) en la página del servicio StorSimple Manager para agregar una cuenta de almacenamiento de Microsoft Azure existente.

#### Para agregar una cuenta de almacenamiento

1.  En la página de aterrizaje del servicio de Administrador de StorSimple, seleccione el servicio y haga doble clic en él. Esto le llevará a la página **Inicio rápido**. Seleccione la página **Configurar**.

2.  Haga clic en **Agregar/editar cuenta de almacenamiento**. En el cuadro de diálogo **Agregar/editar cuenta de almacenamiento**, haga lo siguiente:

	1.  Haga clic en **Agregar nuevo**.

	1.  Proporcione un nombre para la cuenta de almacenamiento.

	1.  Proporcione la **Clave de acceso** principal para la cuenta de almacenamiento de Microsoft Azure.

	1.  Seleccione **Habilitar modo SSL** para crear un canal seguro para la comunicación de red entre su dispositivo y la nube. Desactive la casilla **Habilitar modo SSL** solo si está trabajando dentro de una nube privada.

	1.  Haga clic en el icono de marca de verificación ![](./media/storsimple-ova-deploy1-portal-prep/image7.png). Recibirá una notificación cuando la cuenta de almacenamiento se cree correctamente.

		![](./media/storsimple-ova-deploy1-portal-prep/image11.png)

1.  La cuenta de almacenamiento recién creada se mostrará en la página **Configurar** bajo **Cuentas de almacenamiento**. Haga clic en **Guardar** para guardar la cuenta de almacenamiento recién creada. Haga clic en **Aceptar** cuando se le pida confirmación.


## Paso siguiente

El siguiente paso es aprovisionar una máquina virtual para el dispositivo virtual StorSimple. Según el sistema operativo host, consulte las instrucciones detalladas en:

-   [Aprovisionamiento de la matriz virtual de StorSimple en Hyper-V](storsimple-ova-deploy2-provision-hyperv.md)

-   [Aprovisionamiento de la matriz virtual de StorSimple en VMware](storsimple-ova-deploy2-provision-vmware.md)

<!---HONumber=AcomDC_0302_2016-->