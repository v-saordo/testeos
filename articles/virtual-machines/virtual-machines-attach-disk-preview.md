<properties
	pageTitle="Conectar un disco de datos | Microsoft Azure"
	description="Cómo conectar un disco de datos nuevo o existente a una máquina virtual en el Portal de Azure con el modelo de implementación del Administrador de recursos."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# Cómo conectar un disco de datos en el Portal de Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](storage-windows-attach-disk.md).

En este artículo se muestra cómo conectar discos nuevos y existentes a una máquina virtual a través del Portal de Azure. Antes de hacerlo, revise estas sugerencias:

- El tamaño de la máquina virtual controla cuántos discos de datos puede conectar. Para obtener más información, consulte [Tamaños de máquinas virtuales](virtual-machines-size-specs.md).
- Para usar Almacenamiento premium, necesitará una máquina virtual de serie DS o GS. Puede utilizar discos de cuentas de almacenamiento premium y estándar con estas máquinas virtuales. Almacenamiento premium está disponible en determinadas regiones. Para obtener más información, consulte [Almacenamiento Premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquina virtual de Azure](../storage/storage-premium-storage.md)
- Los discos conectados a las máquinas virtuales son en realidad archivos .vhd en una cuenta de almacenamiento de Azure. Para obtener más información, vea [Acerca de los discos y los discos duros virtuales para máquinas virtuales](virtual-machines-disks-vhds.md).
- Para un disco nuevo, no es necesario crearlo en primer lugar porque Azure lo crea cuando lo conecta.
- Para un disco existente, el archivo .vhd debe estar disponible en una cuenta de almacenamiento de Azure. Puede usar uno que ya esté disponible allí, si no está conectado a otra máquina virtual, o cargar su propio archivo .vhd en la cuenta de almacenamiento.

## Búsqueda de la máquina virtual

1. Inicie sesión en el Portal de Azure.

2. En el menú del Centro, haga clic en **Máquinas virtuales**.

3.	Seleccione la máquina virtual en la lista.

4. A la derecha, en **Essentials**, haga clic en **Toda la configuración** y luego haga clic en **Discos**.

	![Abrir configuración de disco](./media/virtual-machines-attach-disk-preview/find-disk-settings.png)

Continúe siguiendo las instrucciones para conectar un nuevo disco o un disco existente.

## Opción 1: Conectar un nuevo disco

1.	En la hoja **Discos**, haga clic en **Adjuntar nuevo**.

2.	Revise la configuración predeterminada, actualice según sea necesario y, después, haga clic en **Aceptar**.

 	![Revisar configuración de disco](./media/virtual-machines-attach-disk-preview/attach-new.png)

3.	Una vez que Azure crea el disco y lo adjunta a la máquina virtual, el nuevo disco aparece en la configuración de disco de la máquina virtual en el apartado **Discos de datos**.

## Opción 2: Conectar un disco existente

1.	En la hoja **Discos**, haga clic en **Adjuntar existente**.

2.	En **Adjuntar un disco existente**, haga clic en **Archivo VHD**.

	![Conectar disco existente](./media/virtual-machines-attach-disk-preview/attach-existing.png)

3.	En **Cuentas de almacenamiento**, seleccione la cuenta y el contenedor que incluyen el archivo .vhd.

	![Buscar ubicación de VHD](./media/virtual-machines-attach-disk-preview/find-storage-container.png)

4.	Seleccione el archivo .vhd.

5.	En **Adjuntar un disco existente**, el archivo que acaba de seleccionar aparece en **Archivo VHD**. Haga clic en **Aceptar**.

6.	Una vez que Azure adjunta el disco a la máquina virtual,este aparece en la configuración del disco de la máquina virtual en el apartado **Discos de datos**.

## Pasos siguientes

Una vez que se agrega el disco, debe prepararlo para su uso en el sistema operativo de la máquina virtual:

- Para Linux, consulte "Inicializar un nuevo disco de datos en Linux", en este [artículo](virtual-machines-linux-how-to-attach-disk.md).
- Para Windows, consulte "Inicializar un nuevo disco de datos en Windows Server", en este [artículo](storage-windows-attach-disk.md).

## Recursos adicionales

[Acerca de las cuentas de almacenamiento de Azure]

<!--Link references-->

[Acerca de las cuentas de almacenamiento de Azure]: ../storage-whatis-account/

<!---HONumber=AcomDC_0302_2016-->