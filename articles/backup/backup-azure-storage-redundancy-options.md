<properties
	pageTitle="Determinación de las opciones de redundancia del almacenamiento de copia de seguridad de Azure | Microsoft Azure"
	description="Comprenda la diferencia entre el almacenamiento con redundancia geográfica y el almacenamiento con redundancia local para determinar la opción de redundancia de almacenamiento de Copia de seguridad de Azure."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="jimpark; markgal"/>


# Opciones de redundancia de almacenamiento de Copia de seguridad de Azure

Las necesidades de la empresa determinarán la opción de redundancia de almacenamiento adecuada para el almacenamiento de Copia de seguridad de Azure. Si está usando Azure como un punto de conexión de almacenamiento de copia de seguridad principal (por ejemplo, realiza una copia de seguridad en Azure desde un servidor de Windows), debe considerar seleccionar la opción de almacenamiento con redundancia geográfica (valor predeterminado). Si está usando Azure como un punto de conexión de almacenamiento de copia de seguridad terciario (por ejemplo, usa SCDPM para tener una copia de seguridad local y usa Azure para sus necesidades de retención a largo plazo), debería considerar elegir el almacenamiento con redundancia local.

## Almacenamiento con redundancia geográfica (GRS)

El almacenamiento con redundancia geográfica mantiene seis copias de los datos. Con GRS, sus datos se replican tres veces dentro de la región primaria, y se replican también tres veces en una región secundaria a cientos de kilómetros de distancia de la región primaria, proporcionando el nivel más alto de durabilidad. Si se produce un error en la región principal, al almacenar datos en GRS, la Copia de seguridad de Azure garantiza que los datos perduren en dos regiones independientes.

## Almacenamiento con redundancia local (LRS)

El almacenamiento con redundancia local mantiene tres copias de sus datos. LRS se replica tres veces dentro de una única instalación de una sola región. LRS protege los datos frente a errores comunes del hardware, pero no frente a errores de una instalación de Azure completa. Esto reduce el costo de almacenamiento de datos en Azure, a la vez que ofrece un menor nivel de durabilidad de los datos, el cual podría ser aceptable para copias terciarias.

Puede seleccionar la opción adecuada para sus necesidades en la sección **Configurar** de su almacén de copia de seguridad.

## Pasos siguientes

- Asegúrese de que su entorno esté [preparado para la copia de seguridad de un servidor de Windows o una máquina cliente](backup-configure-vault.md).
- Si todavía le quedan dudas sin resolver, eche un vistazo a [P+F de servicio de Copia de seguridad de Azure](backup-azure-backup-faq.md).
- Visite el [foro de Copia de seguridad de Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0211_2016-->