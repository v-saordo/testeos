<properties
	pageTitle="Capturar una imagen de una máquina virtual de Windows de Azure | Microsoft Azure"
	description="Capture una imagen de una máquina virtual Windows de Azure creada con el modelo de implementación clásica."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/05/2015"
	ms.author="cynthn"/>

#Capture una imagen de una máquina virtual Windows de Azure creada con el modelo de implementación clásica.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo del Administrador de recursos.


En este artículo se muestra cómo puede capturar una máquina virtual de Azure con Windows para usarla como imagen en la creación de otras máquinas virtuales. Esta imagen incluye el disco del sistema operativo y los discos de datos que están conectados a la máquina virtual. No incluye configuraciones de red, por lo que deberá configurarlas usted mismo cuando cree las otras máquinas virtuales que usen la imagen.

Azure almacena la imagen en **Mis imágenes**. Este es el mismo lugar donde se almacenan las imágenes que se han cargado. Para obtener más información acerca de las imágenes, vea [Acerca de las imágenes para las máquinas virtuales](virtual-machines-images.md).

##Antes de empezar##

Para seguir estos pasos se supone que ya ha creado un máquina virtual Azure y ha configurado el sistema operativo, incluida la anexión de cualquier disco de datos. Si no ha realizado esto todavía, siga estas instrucciones:

- [Cree una máquina virtual desde una imagen](virtual-machines-create-custom.md)
- [Acoplamiento de un disco de datos a una máquina virtual](storage-windows-attach-disk.md)

> [AZURE.WARNING] Este proceso elimina la máquina virtual original después de capturarla.

Antes de capturar una imagen de una máquina virtual de Azure, se recomienda hacer una copia de seguridad de la máquina virtual de destino. Esto se puede hacer mediante Copia de seguridad de Azure. Para obtener más información, consulte [Copia de seguridad de máquinas virtuales de Azure](../backup/backup-azure-vms.md). Hay otras soluciones disponibles de socios certificados. Para averiguar lo que está actualmente disponible, busque Azure Marketplace.


##Captura de la máquina virtual

1. En el [Portal de Azure clásico](http://manage.windowsazure.com), **Conecte** con la máquina virtual. Para obtener instrucciones, consulte [Inicio de sesión en una máquina virtual con Windows Server][].

2.	Abra una ventana de símbolo del sistema como administrador.

3.	Cambie el directorio a `%windir%\system32\sysprep` y después ejecute sysprep.exe.

4. 	Aparecerá el cuadro de diálogo **Herramienta de preparación del sistema**. Haga lo siguiente:

	- En **Acción de limpieza del sistema**, seleccione **Iniciar la Configuración rápida (OOBE)** y asegúrese de que la opción **Generalizar** está marcada. Para obtener más información acerca del uso de Sysprep, consulte [Uso de Sysprep: Introducción][].

	- En **Opciones de apagado**, seleccione **Apagar**.

	- Haga clic en **Aceptar**.

	![Ejecute Sysprep](./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png)

7.	Sysprep apaga la máquina virtual, lo que cambia su estado en el Portal de Azure clásico a **Detenido**.

8.	En el Portal de Azure clásico, haga clic en **Máquinas virtuales** y seleccione la máquina virtual que desee capturar.

9.	En la barra de comandos, haga clic en **Capturar**.

	![Capture la máquina virtual](./media/virtual-machines-capture-image-windows-server/CaptureVM.png)

	Aparece el cuadro de diálogo **Capturar la máquina virtual**.

10.	En **Nombre de la imagen**, escriba un nombre para la nueva imagen.

11.	Antes de agregar una imagen de Windows Server al conjunto de imágenes personalizadas, ejecute Sysprep para generalizarla siguiendo las instrucciones de los pasos anteriores. Haga clic en **He ejecutado Sysprep en la máquina virtual** para indicar que lo ha hecho.

12.	Haga clic en la marca de verificación para capturar la imagen. La nueva imagen ahora está disponible en **Imágenes**.

 	![Captura correcta de la imagen](./media/virtual-machines-capture-image-windows-server/VMCapturedImageAvailable.png)

##Pasos siguientes

La imagen está lista para usarse para crear máquinas virtuales. Para ello, creará una máquina virtual mediante el elemento del menú **Desde la galería** y seleccionando la imagen que acaba de crear. Para obtener instrucciones, consulte [Creación de una máquina virtual desde una imagen](virtual-machines-create-custom.md).



[Inicio de sesión en una máquina virtual con Windows Server]: virtual-machines-log-on-windows-server.md
[Uso de Sysprep: Introducción]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/virtual-machines-capture-image-windows-server/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/virtual-machines-capture-image-windows-server/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png

<!---HONumber=AcomDC_0128_2016-->