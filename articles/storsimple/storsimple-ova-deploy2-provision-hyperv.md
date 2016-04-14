<properties
   pageTitle="Implementar una matriz virtual de StorSimple: Aprovisionamiento en Hyper-V"
   description="En este segundo tutorial de implementación de matrices virtuales de StorSimple, se trata el aprovisionamiento de un dispositivo virtual en Hyper-V."
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

# Implementación de una matriz virtual de StorSimple: aprovisionamiento de una matriz virtual en Hyper-V

![](./media/storsimple-ova-deploy2-provision-hyperv/hyperv4.png)

## Información general 

Este tutorial de aprovisionamiento se aplica a las matrices virtuales de Microsoft Azure StorSimple (también conocidas como dispositivos virtuales locales de StorSimple o dispositivos virtuales de StorSimple) que se ejecutan en la versión de disponibilidad general de marzo de 2016. En este tutorial se describe cómo aprovisionar una matriz virtual de StorSimple en un sistema host que ejecuta Hyper-V 2008 R2, Hyper-V 2012 o Hyper-V 2012 R2.

Se necesitan privilegios de administrador para aprovisionar y configurar un dispositivo virtual. El aprovisionamiento y la instalación inicial pueden tardar unos 10 minutos en completarse.


## Requisitos previos de aprovisionamiento

Aquí encontrará los requisitos previos para aprovisionar un dispositivo virtual en un sistema host que ejecuta Hyper-V 2008 R2, Hyper-V 2012 o Hyper-V 2012 R2.

### Para el servicio de Administrador de StorSimple

Antes de comenzar, asegúrese de que:

-   Ha completado todos los pasos de [Preparar el portal para la matriz virtual de StorSimple](storsimple-ova-deploy1-portal-prep.md).

-   Ha descargado la imagen del dispositivo virtual para Hyper-V desde el Portal de Azure. Para más información, consulte [Paso 3: Descargar la imagen del dispositivo virtual](storsimple-ova-deploy1-portal-prep.md#step-3-download-the-virtual-device-image).
	
	> [AZURE.IMPORTANT] El software que se ejecuta en la matriz virtual de StorSimple solo puede usarse junto con el servicio StorSimple Manager.

### Para el dispositivo virtual StorSimple 

Antes de implementar un dispositivo virtual, asegúrese de que:

-   Tiene acceso a un sistema host que ejecuta Hyper-V (2008 R2 o superior) que pueda utilizarse para aprovisionar un dispositivo.

-   El sistema host es capaz de utilizar los recursos siguientes para aprovisionar el dispositivo virtual:

	-   Un mínimo de 4 núcleos.
	
	-   Al menos 8 GB de RAM.
	
	-   Una interfaz de red.
	
	-   Un disco virtual de 500 GB para datos del sistema.

### Para la red del centro de datos 

Antes de comenzar, asegúrese de que:

-   Ha revisado los requisitos de red para implementar un dispositivo virtual StorSimple y ha configurado la red del centro de datos según los requisitos. Para más información, consulte Requisitos de red en [Requisitos del sistema de la matriz virtual de StorSimple](storsimple-ova-system-requirements.md#networking-requirements).

## Aprovisionamiento paso a paso 

Para aprovisionar y conectarse a un dispositivo virtual, debe realizar los pasos siguientes:

1.  Asegúrese de que el sistema host tiene recursos suficientes para cumplir los requisitos mínimos del dispositivo virtual.

2.  Aprovisione un dispositivo virtual en el hipervisor.

3.  Inicie el dispositivo virtual y obtenga la dirección IP.

En las siguientes secciones se explican cada uno de estos pasos.

## Paso 1: Asegurarse de que el sistema host cumple los requisitos mínimos del dispositivo virtual

Para crear un dispositivo virtual, necesitará:

-   Hyper-V 2008 R2 SP1, Hyper-V 2012 o Hyper-V 2012 R2 en el sistema host de Windows Server 2008 R2 SP1, Windows Server 2012 o Windows Server 2012 R2.

-   Administrador de Hyper-V de Microsoft en un cliente Microsoft Windows conectado al host.

Asegúrese de que el hardware subyacente (sistema de host) en el que está creando el dispositivo virtual pueda dedicar los siguientes recursos al dispositivo virtual:

- Un mínimo de 4 núcleos.
- Al menos 8 GB de RAM.
- Una interfaz de red.
- Un disco virtual de 500 GB para datos del sistema.

## Paso 2: Aprovisionar un dispositivo virtual en el hipervisor

Realice los pasos siguientes para aprovisionar un dispositivo en el hipervisor.

#### Para aprovisionar un dispositivo virtual

1.  En el host de Windows Server, copie la imagen del dispositivo virtual en la unidad local. Esta es la imagen (VHD o VHDX) que ha descargado mediante el Portal de Azure. Anote la ubicación donde haya copiado la imagen, ya que la va a utilizar más adelante en el procedimiento.

2.  Abra **Administrador del servidor**. En la esquina superior derecha, haga clic en **Herramientas** y seleccione **Administrador de Hyper-V**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image1.png)

	Si está ejecutando Hyper-V 2008 R2, abra el Administrador de Hyper-V. En el Administrador del servidor, haga clic en **Roles > Hyper-V > Administrador de Hyper-V**.

1.  En el **Administrador de Hyper-V**, en el panel de ámbito, haga clic con el botón derecho en el nodo del sistema para abrir el menú contextual. Seleccione **Nuevo** y elija **Máquina virtual**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image2.png)

1.  En la página **Antes de comenzar**, haga clic en **Siguiente**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image3.png)

1.  En la página **Especificar nombre y ubicación**, proporcione un **Nombre** para el dispositivo virtual. Haga clic en **Siguiente**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image4.png)

1.  En la página **Especificar generación**, si usa un VHD, elija **Generación 1**. Si usa un VHDX (para Windows Server 2012 o posterior), elija **Generación 2**. Haga clic en **Siguiente**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image5.png)

	Esta pantalla no aparecerá si ejecuta Hyper-V 2008 R2.

1.  En la página **Asignar memoria**:

    a. Especifique una **Memoria de inicio** de 8192 MB o superior. El requisito de memoria mínima para un dispositivo virtual StorSimple es de 8 GB o superior. No active la opción **Usar la memoria dinámica para esta máquina virtual**.

    b. Haga clic en **Siguiente**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image6.png)

1.  En la página **Configurar funciones de red**:

    a. En la lista desplegable para **Conexión**, seleccione un conmutador virtual. Debe seleccionar un conmutador virtual que esté conectado a Internet.

    b. Haga clic en **Siguiente**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image7.png)

1.  En la página **Conectar disco duro virtual**:

    a. Seleccione la opción de **Utilizar un disco duro virtual existente**. Seleccione el VHD que se descargó en el sistema host.

    b. Haga clic en **Siguiente**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image8.png)

1.  Revise el **Resumen** que se le presenta. Haga clic en **Finalizar** para crear la máquina virtual.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image9.png)

1.  Para cumplir los requisitos mínimos, necesitará 4 núcleos. Para agregar 4 procesadores virtuales, con el sistema host seleccionado en la ventana **Administrador de Hyper-V**, en el panel de la derecha bajo la lista de **Máquinas virtuales**, ubique la máquina virtual que acaba de crear. Seleccione el nombre del equipo, haga clic con el botón secundario y seleccione **Configuración**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image10.png)

1.  En la página **Configuración**, en el panel izquierdo, haga clic en **Procesador**. En el panel derecho, establezca **Número de procesadores virtuales** en 4 (o más). Haga clic en **Apply**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image11.png)

1.  Para cumplir los requisitos mínimos, también debe agregar un disco virtual de datos de 500 GB. En la página **Configuración**:

    1.  En el panel izquierdo, seleccione **Controlador SCSI**. 
    2.  En el panel derecho, seleccione **Unidad de disco duro** y haga clic en **Agregar**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image12.png)

1.  En la página **Unidad de disco duro**, seleccione la opción **Disco duro virtual** y haga clic en **Nuevo**. Esto iniciará el **Asistente para crear nuevo disco duro virtual**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image13.png)

1.  En la página **Antes de comenzar**, haga clic en **Siguiente**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image14.png)

1.  En la página **Elegir formato de disco**, acepte la opción predeterminada de formato **VHDX**. Haga clic en **Siguiente**. Esta pantalla no aparecerá si ejecuta Hyper-V 2008 R2.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image15.png)

1.  En la página **Elegir tipo de disco**, establezca el tipo de disco duro virtual como **Expansión dinámica** (recomendado). Si elige un disco de **Tamaño fijo**, también funcionará, pero puede que necesite esperar mucho tiempo. Se recomienda que no utilice la opción **Diferenciación**. Haga clic en **Siguiente**. Tenga en cuenta que **Expansión dinámica** es el valor predeterminado en Hyper-V 2012 e Hyper-V 2012 R2. En Hyper-V 2008 R2, el valor predeterminado es **Tamaño fijo**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image16.png)

1.  En la página **Especificar nombre y ubicación**, proporcione un **Nombre**, así como una **Ubicación** (puede dirigirse a una) para el disco de datos. Haga clic en **Siguiente**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image17.png)

1.  En la página **Configurar disco**, seleccione la opción **Crear un nuevo disco duro virtual en blanco** y especifique el tamaño como **500 GB** (o más). Haga clic en **Siguiente**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image18.png)

1.  En la página **Resumen**, revise los detalles del disco virtual de datos y, si está satisfecho, haga clic en **Finalizar** para crear el disco. El asistente se cerrará y se agregará un disco duro virtual a su equipo.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image19.png)

1.  Volverá a la página **Configuración**. Realice este paso únicamente si está usando un VHDX. Si usa un VHD y una máquina virtual de generación 1, omita este paso y vaya al siguiente. Ahora debe deshabilitar el arranque seguro en la máquina virtual. El arranque seguro se habilita de forma predeterminada cuando se crea una nueva máquina virtual de generación 2. En la página **Configuración** de la máquina virtual de generación 2, seleccione **Firmware** en **Hardware** y después desactive la casilla **Habilitar arranque seguro**.


2.  Volverá a la página **Configuración**. Haga clic en **Aceptar** para cerrar la página **Configuración** y volver a la ventana Administrador de Hyper-V.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image20.png)

## Paso 3: Iniciar el dispositivo virtual y obtener la dirección IP

Realice los pasos siguientes para iniciar el dispositivo virtual y conectarse a él.

#### Para iniciar el dispositivo virtual

1.  Inicie el dispositivo virtual.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image21.png)

1.  Cuando el dispositivo se esté ejecutando, selecciónelo, haga clic con el botón derecho y seleccione **Conectar**.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image22.png)

1.  Es posible que tenga que esperar entre 5 y 10 minutos para que el dispositivo esté listo. En la consola se muestra un mensaje de estado para indicar el progreso. Una vez listo el dispositivo, vaya a **Acción**. Presione `Ctrl + Alt + Delete` para iniciar sesión en el dispositivo virtual. El usuario predeterminado es *StorSimpleAdmin* y la contraseña predeterminada es *Password1*.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image23.png)

1.  Por motivos de seguridad, la contraseña del administrador de dispositivos expirará en el primer inicio de sesión. Se le pedirá que cambie la contraseña.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image24.png)

	Escriba una contraseña que contenga al menos 8 caracteres. La contraseña debe cumplir al menos 3 de los siguientes 4 requisitos: caracteres en mayúsculas, minúsculas, numéricos y especiales. Vuelva a escribir la contraseña para confirmarla. Se le notificará que la contraseña ha cambiado.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image25.png)

1.  Tras cambiar la contraseña correctamente, puede reiniciar el dispositivo virtual. Espere a que el dispositivo se inicie.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image26.png)

 	La consola de Windows PowerShell del dispositivo se mostrará junto a una barra de progreso.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image27.png)

1.  Los pasos 6 a 8 solo se aplican durante el arranque en un entorno sin DHCP. Si se encuentra en un entorno de DHCP, omita estos pasos y vaya al paso 9. Si arranca el dispositivo en un entorno sin DHCP, verá la siguiente pantalla.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image28.png)

 	Ahora debe configurar la red.

1.  Use el comando `Get-HcsIpAddress` para enumerar las interfaces de red habilitadas en el dispositivo virtual. Si el dispositivo tiene una única interfaz de red habilitada, el nombre predeterminado asignado a esta interfaz es `Ethernet`.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image29.png)

1.  Utilice el cmdlet `Set-HcsIpAddress` para configurar la red. A continuación se muestra un ejemplo:

 	`Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

 	![](./media/storsimple-ova-deploy2-provision-hyperv/image30.png)

1.  Una vez que haya finalizado la instalación inicial y el dispositivo haya arrancado, verá el texto de titular del dispositivo. Anote la dirección IP y la dirección URL que se muestran en el texto del titular para administrar el dispositivo. Utilizará esta dirección IP para conectarse a la interfaz de usuario web del dispositivo virtual y completar la instalación local y el registro.

	![](./media/storsimple-ova-deploy2-provision-hyperv/image31.png)

	Si el dispositivo no cumple los requisitos mínimos de configuración, verá un error en el texto del titular (se muestra a continuación). Debe modificar la configuración del dispositivo para que tenga los recursos adecuados para cumplir los requisitos mínimos. A continuación, puede reiniciar y conectarse al dispositivo. Consulte los requisitos mínimos de configuración en [Paso 1: Asegurarse de que el sistema host cumple los requisitos mínimos del dispositivo virtual](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

	![](./media/storsimple-ova-deploy2-provision-hyperv/image32.png)

Si encuentra cualquier otro error durante la configuración inicial mediante la interfaz de usuario web local, consulte los siguientes flujos de trabajo en [Usar la interfaz de usuario web para administrar la matriz virtual de StorSimple](storsimple-ova-web-ui-admin.md).

-   Ejecute pruebas de diagnóstico para [solucionar problemas de configuración de la interfaz de usuario web](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).

-   [Genere el paquete de registro y vea los archivos del registro](storsimple-ova-web-ui-admin.md#generate-a-log-package).

![icono de vídeo](./media/storsimple-ova-deploy2-provision-hyperv/video_icon.png) **Vídeo disponible**

Mire el vídeo para ver cómo puede aprovisionar una matriz virtual de StorSimple en Hyper-V.

> [AZURE.VIDEO create-a-storsimple-virtual-array]

## Pasos siguientes

-   [Configurar la matriz virtual de StorSimple como servidor de archivos](storsimple-ova-deploy3-fs-setup.md)

-   [Configurar la matriz virtual de StorSimple como servidor iSCSI](storsimple-ova-deploy3-iscsi-setup.md)

<!---HONumber=AcomDC_0302_2016-->