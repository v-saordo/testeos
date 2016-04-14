<properties
	pageTitle="Acoplamiento de un disco a una máquina virtual | Microsoft Azure"
	description="Conecte un disco de datos a una máquina virtual de Windows creada con el modelo de implementación clásica e inicialícelo."
	services="virtual-machines, storage"
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
	ms.date="02/03/2016"
	ms.author="cynthn"/>

# Conecte un disco de datos a una máquina virtual de Windows creada con el modelo de implementación clásica

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-attach-disk-preview.md).

Si necesita un disco de datos adicional, puede conectar un disco vacío o un disco existente con datos a una máquina virtual. En ambos casos, se trata de archivos .vhd que residen en una cuenta de almacenamiento de Azure. En el caso de un disco nuevo, una vez que lo conecta, también deberá inicializarlo para que esté listo para su uso por parte de una máquina virtual de Windows.

Es recomendable utilizar uno o varios discos independientes para almacenar los datos de una máquina virtual. Al crear una máquina virtual de Azure, esta cuenta con un disco para el sistema operativo asignado a la unidad C y un disco temporal asignado a la unidad D. **No use el disco temporal para almacenar datos**. Como su nombre así lo indica, el disco temporal solo ofrece almacenamiento temporal. No ofrece redundancia ni copias de seguridad porque no reside en Almacenamiento de Azure.

## Tutorial en vídeo

A continuación se facilita una guía detallada de los pasos de este tutorial.

[AZURE.VIDEO attaching-a-data-disk-to-a-windows-vm]

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a id="initializeinWS"></a>Inicialización de un nuevo disco de datos en Windows Server

1. Conexión a una máquina virtual. Para obtener instrucciones, consulte [Inicio de sesión en una máquina virtual que ejecuta Windows Server][logon].

2. Después de iniciar sesión en la máquina virtual, abra el **Administrador del servidor**. En el panel izquierdo, seleccione **Servicios de archivos y almacenamiento**.

	![Abrir Administrador de servidores](./media/storage-windows-attach-disk/fileandstorageservices.png)

3. Expanda el menú y seleccione **Discos**.

4. En la sección **Discos** aparecen todos los discos. En la mayoría de los casos, habrá un disco 0, un disco 1 y un disco 2. El disco 0 es el disco del sistema operativo, el disco 1 es el disco temporal y el disco 2 es el disco de datos que acaba de conectar a la VM. El nuevo disco de datos mostrará la partición como **Desconocida**. Haga clic con el botón derecho en el disco y seleccione **Inicializar**.

5.	Se le notificará que se borrarán todos los datos cuando se inicializa el disco. Haga clic en **Sí** para confirmar la advertencia e inicializar el disco. Una vez que se complete el proceso, la partición aparecerá como **GPT**. Vuelva a hacer clic con el botón derecho en el disco y seleccione **Nuevo volumen**.

6.	Complete el asistente usando los valores predeterminados que se proporcionan. Cuando haya finalizado el asistente, la sección **Volúmenes** mostrará el nuevo volumen. El disco está ahora conectado y listo para almacenar los datos.

	![Volumen inicializado correctamente](./media/storage-windows-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] El tamaño de la VM determina el número de discos que le puede asociar. Para obtener más información, vea [Tamaños de máquinas virtuales](virtual-machines-size-specs.md).

## Recursos adicionales

[Desacoplamiento de un disco de una máquina virtual de Windows](storage-windows-detach-disk.md)

[Acerca de los discos y los discos duros virtuales para máquinas virtuales](virtual-machines-disks-vhds.md)

[logon]: virtual-machines-log-on-windows-server.md

<!---HONumber=AcomDC_0211_2016-->