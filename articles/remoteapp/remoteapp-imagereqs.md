
<properties
    pageTitle="Requisitos de imagen de Azure RemoteApp | Microsoft Azure"
    description="Obtenga información sobre los requisitos para crear imágenes que se usarán con Azure RemoteApp"
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
    ms.date="12/05/2015"
    ms.author="elizapo" />



# Requisitos para las imágenes Azure RemoteApp
Azure RemoteApp usa una imagen de Windows Server 2012 R2 para hospedar todos los programas que desea compartir con los usuarios. Para crear una imagen personalizada, puede comenzar con una imagen existente o [crear una nueva](remoteapp-create-custom-image.md).

> [AZURE.TIP]¿Sabía que la suscripción de Azure RemoteApp le permite acceder a una imagen de Windows Server 2012 R2 en la galería de máquinas virtuales de Azure, y que puede usarla para crear su propia imagen de plantilla? [Compruébelo](remoteapp-image-on-azurevm.md).


Los requisitos para la imagen que se pueden cargar para usarse con RemoteApp de Azure son:


- Las aplicaciones personalizadas no almacenan datos de manera local en la imagen. Estas imágenes no tienen estado y solo deben contener aplicaciones.
- La imagen no contiene datos que se puedan perder.
- El tamaño de la imagen debe ser un múltiplo de MB. Si se intenta cargar una imagen que no es un múltiplo exacto, la carga no se realizará.
- El tamaño de imagen debe ser de 127 GB o menos.
- Debe estar en un archivo VHD (los archivos VHDX no se admiten actualmente).
- El disco duro virtual no debe ser una máquina virtual de generación 2.
- El archivo VHD puede ser de tamaño fijo o expandirse dinámicamente. Se recomienda un archivo VHD que se expanda dinámicamente porque tarda menos tiempo en cargarse en Azure que un archivo VHD de tamaño fijo.
- El disco se debe inicializar usando el estilo de particiones Registro de arranque maestro (MBR, Master Boot Record). El estilo de particiones de tabla de particiones GUID (GPT) no se admite.
- El archivo VHD debe contener una sola instalación de Windows Server 2012 R2. Puede contener varios volúmenes, pero uno de ellos con una instalación de Windows.
- El rol Host de sesión de Escritorio remoto (RDSH, Remote Desktop Session Host) y la característica Experiencia de escritorio deben estar instalados.
- El rol Agente de conexión a Escritorio remoto *no* debe instalarse.
- El Sistema de cifrado de archivos (EFS, Encrypting File System) debe estar instalado.
- La imagen debe estar preparada con sysprep con los parámetros **/oobe /generalize /shutdown** (No USE el parámetro **/mode:vm**).
- No se admite la carga de su VHD desde una cadena de instantáneas.

Consulte [Creación de una imagen de Azure RemoteApp](remoteapp-imageoptions.md) para obtener más información sobre la creación de imágenes para Azure RemoteApp.

<!---HONumber=AcomDC_1210_2015-->