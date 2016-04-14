
<properties
    pageTitle="Carga de una imagen personalizada en Azure RemoteApp | Microsoft Azure"
    description="Obtenga información sobre cómo cargar una imagen personalizada para RemoteApp de Azure"
    services="remoteapp"
    documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/02/2016"
    ms.author="ericor" />



# Carga de una imagen personalizada para RemoteApp de Azure

Ahora que ha creado la imagen de plantilla personalizada o que la actualizado con cambios, es necesario cargar esa imagen a la biblioteca de imágenes de Azure RemoteApp. Siga estos pasos.


## Antes de comenzar

1.      Compruebe que la imagen personalizada cumple con los [requisitos de imagen](remoteapp-imagereqs.md) y los [requisitos de aplicación](remoteapp-appreqs.md).
2.      Instale el [módulo de Azure PowerShell](../powershell-install-configure.md).

## Procedimiento paso a paso para cargar una imagen personalizada

1.      Abra el Portal de administración de Azure y navegue a la página RemoteApp.
2.      En la pestaña **Imágenes de plantilla**, haga clic en **Cargar** en la parte inferior de la página.
4.      Escriba un nombre descriptivo para la imagen y especifique la ubicación de la cuenta de almacenamiento. Asegúrese de que la ubicación es la misma que para la colección de RemoteApp o una ubicación donde desea crear una.
5.      Cuando se le solicite, descargue el script a su equipo local.
6.      Copie los parámetros de comando del cuadro de texto en el portapapeles.
7.      Abra una ventana de Windows PowerShell con privilegios elevados.
8.      Desde la ventana de Windows PowerShell con privilegios elevados, navegue al mismo directorio al que descargó el script.
9.      Pegue el comando copiado y presione **Entrar**.

	Comenzará el proceso de carga y la duración podría depender de muchos factores, incluida la velocidad de la red y el tamaño de la imagen.

11.    Si la carga no se realiza correctamente debido a una interrupción de la red o algún factor similar, siempre puede reanudar el proceso de carga que comenzó. Para reanudar una carga, ejecute nuevamente el script con la misma línea de comando.

> [AZURE.WARNING] No modifique nunca el script de carga. Se han implementado comprobaciones específicas para garantizar que la imagen cumple con los requisitos de imagen y los requisitos de aplicación.

## Problemas comunes

- Asegúrese de que usa Windows PowerShell y no Azure PowerShell. Debe instalar el módulo Azure PowerShell porque se necesitan ciertos módulos durante el proceso de carga.
- Nunca modifique el script: las validaciones existen para su comodidad.
- Si el archivo vhd se bloquea durante la carga, cópielo o muévalo a una nueva ubicación e intente volver a cargarlo. Es posible que algunos procesos de Windows impidan la carga.  

<!---HONumber=AcomDC_0211_2016-->