<properties
    pageTitle="Creación de una imagen de Azure RemoteApp basada en una máquina virtual de Azure | Microsoft Azure"
    description="Obtenga información sobre cómo crear una imagen para Azure RemoteApp comenzando con una máquina virtual de Azure."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/05/2016" 
    ms.author="elizapo" />



# Creación de una imagen de Azure RemoteApp basada en una máquina virtual de Azure

Puede crear imágenes de Azure RemoteApp (que incluyan las aplicaciones que comparte en la colección) desde una máquina virtual de Azure. También puede elegir usar una imagen de máquina virtual que agregamos a la galería de imágenes de máquina virtual de Azure que cumple todos los requisitos de imagen de Azure RemoteApp, y usarla como punto de partida para su propia máquina virtual, si lo desea. Solo busque la imagen "Host de sesión de Escritorio remoto de Windows Server" en la biblioteca.

Existen dos pasos para crear su propia imagen basada en una máquina virtual de Azure: crear la imagen y luego cargarla desde la biblioteca de máquinas virtuales de Azure en Azure RemoteApp.

## Creación de una imagen personalizada basada en una máquina virtual de Azure

Utilice estos pasos para crear una imagen basada en una máquina virtual de Azure.

1. Cree una máquina virtual de Azure. Puede usar la imagen "Host de sesión de Escritorio remoto de Windows Server" o "Host de sesión de Escritorio remoto de Windows Server con Microsoft Office 365 ProPlus" de la galería de imágenes de máquina virtual de Azure. Esta imagen cumple con todos los requisitos de imagen de plantilla de Azure RemoteApp.

	Para obtener información detallada, vea [Creación de una máquina virtual que ejecuta Windows](../virtual-machines/virtual-machines-windows-tutorial.md).

2. Conéctese a la máquina virtual e instale y configure las aplicaciones que desea compartir a través de RemoteApp. Asegúrese de realizar cualquier configuración adicional de Windows que requieran sus aplicaciones.

	Para obtener información detallada, consulte [Cómo iniciar sesión en una máquina virtual que ejecuta Windows Server](../virtual-machines/virtual-machines-log-on-windows-server.md).

3. Si usa una de las imágenes de Host de sesión de Escritorio remoto de Windows Server, hay incluido un script de validación que garantizará que la máquina virtual cumple los requisitos previos de RemoteApp. Para ejecutar el script, haga doble clic en **ValidateRemoteAppImage** en el escritorio. Asegúrese de que todos los errores que el script informó estén corregidos antes de continuar al paso siguiente.

4. SYSPREP generalice y capture la imagen. Vea [Cómo capturar una máquina virtual Windows para usarla como plantilla#](../virtual-machines/virtual-machines-capture-image-windows-server.md) para obtener instrucciones.



## Importación de la imagen en la biblioteca de imágenes de Azure RemoteApp

Use estos pasos para importar la imagen nueva en Azure RemoteApp:

1. En la pestaña **Imágenes de plantilla**:
	- Si no dispone de imágenes existentes, haga clic en **Carga o importación de una imagen de plantilla**.
	- Si ya tiene al menos una imagen, haga clic en **+** para agregar otra imagen.

2. Seleccione **Importar una imagen de la biblioteca de máquinas virtuales** y haga clic en **Siguiente**.

3. En la página siguiente, seleccione la imagen personalizada de la lista y confirme que siguió los pasos enumerados al momento de crear la imagen. Haga clic en **Siguiente**.
4. Escriba un nombre para la imagen nueva de RemoteApp y elija la ubicación; a continuación, haga clic en la marca de verificación para comenzar el proceso de importación.

> [AZURE.NOTE] Puede importar imágenes desde cualquier ubicación de Azure compatible con máquinas virtuales de Azure a cualquier ubicación de Azure compatible con Azure RemoteApp. La importación puede demorar hasta 25 minutos, dependiendo de las ubicaciones.

Ahora está listo para crear su colección, ya sea una colección [en la nube](remoteapp-create-cloud-deployment.md) o una colección [híbrida](remoteapp-create-hybrid-deployment.md), según sus necesidades.

<!---HONumber=AcomDC_0211_2016-->