    <properties
	pageTitle="Create VM templates | Microsoft Azure"
	description="Learn how to create VM templates from VHD images"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# Crear plantillas de máquina virtual

## Información general

Una vez que haya [creado un laboratorio](devtest-lab-create-lab.md), puede [agregar máquinas virtuales a ese laboratorio](devtest-lab-add-vm-with-artifacts.md) desde una lista de plantillas de máquina virtual. En este artículo obtendrá información sobre cómo cargar y configurar un archivo de imagen de disco duro Virtual (VHD) como una plantilla desde la que creará sus máquinas virtuales. Si no está familiarizado con las imágenes de disco duro virtual, consulte el artículo [Creación y carga de un VHD de Windows Server en Azure](../virtual-machines-create-upload-vhd-windows-server.md) para información sobre cómo crear una imagen de VHD. Una vez que haya creado u obtenido acceso a una imagen de VHD, este artículo le guiará sobre cómo cargarla y crear una plantilla a partir de ella.

## Creación de una plantilla de VM

1. Inicie sesión en el [portal de vista previa de Azure](https://portal.azure.com).

1. Pulse **Examinar** y luego pulse **Laboratorios de desarrollo y pruebas** en la lista.

1. En la lista de laboratorios, pulse en el laboratorio que quiera.

1. En la hoja del laboratorio, pulse en **Configuración**.

    ![Configuración del laboratorio](./media/devtest-lab-create-template/lab-blade-settings.png)

1. En la hoja **Configuración** del laboratorio, pulse en **Plantillas**.

    ![Opción de plantillas](./media/devtest-lab-create-template/lab-blade-settings-templates.png)

1. En la hoja **Plantillas**, pulse en **+ Plantilla**.

    ![Agregar plantilla](./media/devtest-lab-create-template/add-template.png)

1. En la hoja **Agregar plantilla**:

	1. Escriba el nombre de la plantilla. Este nombre se muestra en la lista de plantillas al crear una nueva máquina virtual.

	1. Escriba la descripción de la plantilla. Esta descripción se muestra en la lista de plantillas al crear una nueva máquina virtual.

	1. Pulse en **Imagen**.

	1. Si la imagen que quiere no aparece en la lista y quiere agregarla, vaya a la sección [Agregar una nueva imagen de plantilla](#add-a-new-template-image) y vuelva aquí cuando haya terminado.

	1. Seleccione la imagen que quiera.

	1. Pulse en **Aceptar** para cerrar la hoja **Agregar plantilla**.

1. Pulse en **Configuración del sistema operativo**.

1. En la pestaña **Configuración del sistema operativo**, seleccione **Windows** o **Linux**.

1. Si se selecciona **Windows**, especifique a través de la casilla si se ha ejecutado *Sysprep* en el equipo.

1. Escriba un **Nombre de usuario** para la máquina.

1. Escriba una **Contraseña** para la máquina. **Nota:** la contraseña aparecerá en texto no cifrado.

1. Pulse en **Aceptar** para cerrar la hoja **Configuración del sistema operativo**.

1. Especifique la **Ubicación**.

1. Pulse **Aceptar** para crear la plantilla.

##Agregar una nueva imagen de plantilla

Para agregar una nueva imagen de plantilla, deberá tener acceso a un archivo de imagen VHD.

1. En la hoja **Agregar imagen de plantilla**, pulse en **Cargar una imagen con PowerShell**.

    ![Cargar imagen](./media/devtest-lab-create-template/upload-image-using-psh.png)

1. En la siguiente hoja se muestran instrucciones para modificar y ejecutar un script de PowerShell que carga en su suscripción de Azure un archivo de imagen de disco duro virtual. **Nota:** este proceso puede durar bastante tiempo, en función del tamaño del archivo de imagen y de la velocidad de conexión.

##Pasos siguientes

Una vez que haya agregado una plantilla de máquina virtual para usarla al crear una máquina virtual, el siguiente paso consiste en [agregar una máquina virtual a su laboratorio de DevTest](devtest-lab-add-vm-with-artifacts).

<!---HONumber=AcomDC_0128_2016-->