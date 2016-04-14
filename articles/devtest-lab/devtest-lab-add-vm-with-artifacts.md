<properties
	pageTitle="Incorporación de una máquina virtual con artefactos a un Laboratorio de desarrollo y pruebas | Microsoft Azure"
	description="Cree una nueva máquina virtual con artefactos en el Laboratorio de desarrollo y pruebas."
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
	ms.date="02/18/2016"
	ms.author="tarcher"/>

# Incorporación de una máquina virtual con artefactos a un Laboratorio de desarrollo y pruebas de Azure

> [AZURE.NOTE] Haga clic en el vínculo siguiente para ver el vídeo que acompaña a este artículo: [Cómo crear VM con artefactos en un laboratorio de desarrollo y pruebas](/documentation/videos/how-to-create-vms-with-artifacts-in-a-devtest-lab)

## Información general

Una máquina virtual se crea en un Laboratorio de desarrollo y pruebas a partir de una imagen base de Azure o con una imagen que se ha cargado en el laboratorio.

Los *artefactos* del Laboratorio de desarrollo y pruebas le permiten especificar las acciones que se realizan cuando se crea la máquina virtual. Las acciones del artefacto pueden realizar procedimientos como la ejecución de comandos de Powershell y Bash y la instalación de software. Los *parámetros* del artefacto le permiten personalizar el artefacto para su escenario en particular.

El laboratorio incluye artefactos procedentes del repositorio de artefactos del Laboratorio de desarrollo y pruebas público, así como los artefactos que se crean y se agregan a su propio repositorio de artefactos.

En este artículo se muestra cómo crear una máquina virtual en el laboratorio usando artefactos.

## Incorporación de una máquina virtual con artefactos

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com).

1. Pulse **Examinar** y luego pulse **Laboratorios de desarrollo y pruebas** en la lista.

1. En la lista de laboratorios, pulse el laboratorio en el que desea crear la nueva máquina virtual.

1. En la hoja del laboratorio, pulse **+ Máquina virtual de laboratorio** tal como se muestra en la ilustración siguiente. ![Hoja principal del Laboratorio de desarrollo y pruebas](./media/devtest-lab-add-vm-with-artifacts/devtestlab-home-blade-add-vm.png)

1. En la hoja **Máquina virtual de laboratorio**, escriba un nombre para la nueva máquina virtual en el cuadro de texto **Nombre de máquina virtual de laboratorio**.

1. Pulse **Base / Configurar los valores obligatorios** y seleccione una imagen base para la máquina virtual.

    ![Configuración de la máquina virtual de laboratorio](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-1.png)

1. Después de seleccionar una imagen base, la hoja **Máquina virtual de laboratorio** se ampliará para incluir los elementos **Nombre de usuario** y **Contraseña**. Escriba un **Nombre de usuario** al que se concederán privilegios de administrador en la máquina virtual.

    ![Hoja de Máquina virtual de laboratorio ampliada](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-lab-vm-blade-2.png)

1. Escriba una **Contraseña**.

1. Pulse **Tamaño de máquina virtual** y seleccione uno de los elementos predefinidos que especifican los núcleos del procesador, el tamaño de RAM y el tamaño de la unidad de disco duro de la máquina virtual que se va a crear.

1. Pulse **Artefactos** y, en la lista de artefactos, seleccione y configure los artefactos que desea agregar a la imagen base. **Nota:** Si no está familiarizado con los Laboratorios de desarrollo y pruebas o la configuración de artefactos, vaya a la sección [Selección y configuración de un artefacto](#configuring-an-artifact) y vuelva aquí cuando haya finalizado.

1. Pulse **Crear** para agregar la máquina virtual especificada al laboratorio.

1. La hoja del laboratorio muestra el estado de creación de la máquina virtual; primero como **En creación** y luego como **En ejecución**, una vez que se haya iniciado la máquina virtual. Para conectarse a la máquina virtual, pulse la máquina virtual y, desde la hoja de esa máquina virtual, pulse **Conectar**.

## Selección y configuración de un artefacto

Al crear una máquina virtual, puede agregar artefactos punteando en **Artefactos** desde la hoja **Máquina virtual de laboratorio**. De este modo aparecerá la hoja **Agregar artefactos**, que permite agregar y configurar los artefactos de la máquina virtual desde el repositorio del Laboratorio de desarrollo y pruebas oficial (**Repositorio oficial**) y los artefactos desde el repositorio del equipo.

![Hoja de Agregar artefactos](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifact-blade.png)

**Incorporación de un artefacto a una máquina virtual**

Siga estos pasos para cada artefacto que desee agregar a su máquina virtual:

1. Pulse el artefacto deseado en la hoja **Agregar artefactos** para ver un cuadro que le permita especificar los parámetros del artefacto.  

2. Escriba los valores de parámetro necesarios y cualquier otro parámetro opcional que necesite.

3. Pulse **Agregar** para agregar el artefacto y vuelva a la hoja **Agregar artefactos**.

**Cambio del orden en que se ejecutan los artefactos**

A medida que agregue artefactos a la máquina virtual y los configure para ella, aparecerá un vínculo que muestra el número actual de artefactos en la parte superior de la hoja **Agregar artefactos**. De forma predeterminada, las acciones de los artefactos se ejecutan en el orden en que se agregan a la máquina virtual. Para cambiar el orden en el que se ejecutan los artefactos, simplemente arrastre y coloque los artefactos en la lista y pulse **Aceptar** cuando haya terminado.

**Visualización o modificación de los artefactos seleccionados**

Siga estos pasos para ver o modificar los parámetros de los artefactos seleccionados:

1. En la parte superior de la hoja **Agregar artefactos**, pulse el vínculo que indica cuántos artefactos se han agregado a la máquina virtual.

    ![](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)

1. Para ver o editar los parámetros de un artefacto específico, pulse ese artefacto en la hoja **Artefactos seleccionados**.

1. Realice los cambios necesarios y pulse **Aceptar** para cerrar la hoja **Agregar artefacto**.

1. Pulse **Aceptar** para cerrar la hoja **Artefactos seleccionados**.

1. Pulse **Aceptar** para cerrar la hoja **Agregar artefactos**.

## Pasos siguientes

- Aprenda cómo [crear artefactos personalizados para la máquina virtual](devtest-lab-artifact-author.md).

<!---HONumber=AcomDC_0224_2016-->