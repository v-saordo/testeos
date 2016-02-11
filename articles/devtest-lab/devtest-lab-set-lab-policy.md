<properties
pageTitle="Definición de directivas de laboratorio | Microsoft Azure"
description="Obtenga información acerca de cómo definir directivas de laboratorio como tamaños de máquina virtual, máximo de máquinas virtuales por usuario y automatización de apagado."
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

# Definición de directivas de laboratorio

## Información general

El Laboratorio de desarrollo y pruebas permite especificar directivas de clave que rigen cómo se usan el laboratorio y sus máquinas virtuales. Estas directivas incluyen los umbrales de costo, los tamaños de máquina virtual permitidos, el número máximo de máquinas virtuales por usuario y un apagado automático para las máquinas virtuales del laboratorio.

## Acceso a las directivas de un laboratorio

Para ver (y cambiar) las directivas para un laboratorio, siga estos pasos:

1. Inicie sesión en el [Portal de vista previa de Azure](https://portal.azure.com).

1. Pulse **Examinar** y luego pulse **Laboratorios de desarrollo y pruebas** en la lista.

1. En la lista de laboratorios, pulse el laboratorio que desee.

1. Pulse **Configuración**.

	![Settings](./media/devtest-lab-set-lab-policy/lab-blade-settings.png)

1. En la hoja **Configuración**, hay un grupo de valores denominados **Directivas**.

	![Settings](./media/devtest-lab-set-lab-policy/policies.png)

	Pulse la directiva que quiera de la lista siguiente para obtener más información acerca de su configuración:

	- Umbrales de costo: esta directiva no se admite actualmente.

	- [Tamaños de máquina virtual permitidos](#set-allowed-vm-sizes): seleccione la lista de tamaños de máquinas virtuales que se permiten en el laboratorio. Un usuario puede crear máquinas virtuales solo desde esta lista.

	- [Máximo de máquinas virtuales](#set-maximum-vms): especifique el número máximo de máquinas virtuales que se pueden crear para un laboratorio, así como el número máximo de máquinas virtuales que puede crear un usuario.

	- [Apagado automático](#set-auto-shutdown): especifique la hora a la que se deben apagar las máquinas virtuales del laboratorio actual.

## Establecimiento de los tamaños de máquina virtual permitidos

La directiva para establecer los tamaños permitidos de la máquina virtual ayuda a minimizar la pérdida del laboratorio al permitirle especificar los tamaños de máquina virtual que se permiten en este. Si se activa esta directiva, los tamaños de máquina virtual de esta lista son los únicos que pueden utilizarse en la creación de tales máquinas.

1. En la hoja **Configuración** del laboratorio, en **Directivas**, pulse **Tamaños de máquina virtual permitidos**.

	![Settings](./media/devtest-lab-set-lab-policy/allowed-vm-sizes-policy.png)
 
1. Pulse **Activado** para habilitar esta directiva, y en **Desactivado** para deshabilitarla.

1. Si habilitó esta directiva, pulse uno o varios tamaños de máquina virtual que se pueden crear en el laboratorio.

1. Pulse **Guardar**.

## Establecimiento del número máximo de máquinas virtuales

La directiva para el máximo de máquinas virtuales permite especificar el número máximo de máquinas virtuales que se pueden crear para el laboratorio actual, así como el número máximo de máquinas virtuales que puede crear un usuario. Si un usuario intenta crear una nueva máquina virtual cuando se ha alcanzado el límite del usuario o el límite del laboratorio, un mensaje de error indicará que no se puede crear la máquina virtual.

1. En la hoja **Configuración** del laboratorio, en **Directivas**, pulse **Máximo de máquinas virtuales**.

	![Settings](./media/devtest-lab-set-lab-policy/max-vms-policies.png)

1. En la sección **Directiva por usuario**:
 
	1. Pulse **Activado** para habilitar esta directiva, y en **Desactivado** para deshabilitarla.
	
	1. Si habilita esta directiva, en el cuadro de texto **Máximo de máquinas virtuales permitidas por usuario**, escriba un valor numérico que indique el número máximo de máquinas virtuales que puede crear un usuario. Si escribe un número que no sea válido, la interfaz de usuario mostrará el número máximo permitido para este campo.

1. En la sección **Directiva por laboratorio**:
 
	1. Pulse **Activado** para habilitar esta directiva, y en **Desactivado** para deshabilitarla.
	
	1. Si habilitó esta directiva, en el cuadro de texto **Máximo de máquinas virtuales permitidas por laboratorio**, escriba un valor numérico que indique el número máximo de máquinas virtuales que se pueden crear para el laboratorio actual. Si escribe un número que no sea válido, la interfaz de usuario mostrará el número máximo permitido para este campo.

1. Pulse **Guardar**.

## Establecimiento del apagado automático

La directiva de apagado automático ayuda a minimizar la pérdida del laboratorio, ya que permite especificar la hora de apagado de la máquina virtual de este laboratorio.

1. En la hoja **Configuración** del laboratorio, en **Directivas**, pulse **Apagado automático**.

	![Settings](./media/devtest-lab-set-lab-policy/auto-shutdown-policy.png)

1. Pulse **Activado** para habilitar esta directiva, y en **Desactivado** para deshabilitarla.

1. Si habilitó esta directiva, especifique una hora para apagar todas las máquinas virtuales en el laboratorio actual.

1. Pulse **Guardar**.

<!---HONumber=AcomDC_0128_2016-->