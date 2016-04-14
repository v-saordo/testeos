<properties 
	pageTitle="Mover datos hacia y desde el almacenamiento de blobs de Azure con el Explorador de almacenamiento de Azure | Microsoft Azure" 
	description="Mover datos hacia y desde el almacenamiento de blobs de Azure con el Explorador de almacenamiento de Azure" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/08/2016" 
	ms.author="bradsev" />

# Mover datos hacia y desde el almacenamiento de blobs de Azure con el Explorador de almacenamiento de Azure

## Introducción 

El Explorador de almacenamiento de Azure es una herramienta gratuita basada en Windows para inspeccionar y modificar datos de una cuenta de almacenamiento de Azure. En este tema se describe cómo usarlo para cargar y descargar datos del Almacenamiento de blobs de Azure. Se puede descargar en el [Explorador de almacenamiento de Azure](http://azurestorageexplorer.codeplex.com/).

A continuación se ofrecen vínculos de orientación sobre las tecnologías que se usan para mover datos hacia o desde el almacenamiento de blobs de Azure:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]


> [AZURE.NOTE] Si está usando la máquina virtual que se configuró con los scripts ofrecidos por [Máquinas virtuales de ciencia de datos en Azure](machine-learning-data-science-virtual-machines.md),el Explorador de almacenamiento de Azure ya está instalado en la máquina virtual.

> [AZURE.NOTE] Para ver una introducción completa al almacenamiento de blobs de Azure, consulte [Aspectos básicos del blob de Azure](../storage-dotnet-how-to-use-blobs.md) y [Servicio BLOB de Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Requisitos previos

En este documento se supone que tiene una suscripción de Azure y una cuenta de almacenamiento y la clave de almacenamiento correspondiente para dicha cuenta. Antes de cargar o descargar datos, debe conocer su nombre de cuenta de almacenamiento de Azure y la clave de cuenta.

- Para configurar una suscripción de Azure, consulte [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
- Para obtener instrucciones sobre la creación de una cuenta de almacenamiento y para obtener información de cuentas y claves, vea [Acerca de las cuentas de almacenamiento de Azure](../storage-create-storage-account.md).


<a id="explorer"></a>
## Usar el Explorador de almacenamiento de Azure 

En los pasos siguientes se describe cómo cargar y descargar datos mediante el Explorador de almacenamiento de Azure.

1.  Iniciar el Explorador de almacenamiento de Azure 
2.  Si la cuenta de almacenamiento a la que desea acceder no se ha agregado al Explorador de almacenamiento de Azure, haga clic en el botón "Agregar cuenta" para agregar la cuenta. Si ya se ha agregado, seleccione la cuenta en la lista desplegable "Seleccione una cuenta de almacenamiento". ![Creación del espacio de trabajo][1] <br>
3. Escriba el nombre de la cuenta de almacenamiento y la clave de la cuenta de almacenamiento y, a continuación, haga clic en Agregar cuenta de almacenamiento. Puede agregar varias cuentas de almacenamiento y cada cuenta se mostrará en una pestaña. Los contenedores de esta cuenta de almacenamiento se muestran en el panel izquierdo. Seleccione un contenedor para ver los blobs del contenedor en el panel derecho. ![Creación del espacio de trabajo][2] <br> ![Creación del espacio de trabajo][3] <br>
4. Cargue datos haciendo clic en el botón "Cargar". Seleccione uno o varios archivos para cargarlos desde el sistema de archivos y haga clic en "Abrir" para empezar a cargarlos.
5. Para descargar datos seleccione el blob en el contenedor correspondiente y haga clic en el botón "Descargar".

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png

<!---HONumber=AcomDC_0211_2016-->