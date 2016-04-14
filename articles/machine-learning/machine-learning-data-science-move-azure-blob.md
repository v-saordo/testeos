<properties 
	pageTitle="Mover datos hacia y desde el almacenamiento de blobs de Azure | Microsoft Azure" 
	description="Mover datos hacia y desde el almacenamiento de blobs de Azure" 
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
	ms.author="bradsev;sunliangms;sachouks;mohabib" />

# Mover datos hacia y desde el almacenamiento de blobs de Azure

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

## Introducción

A continuación se ofrecen vínculos de orientación sobre las tecnologías que se usan para mover datos hacia o desde el almacenamiento de blobs de Azure:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

El método más adecuado para usted dependerá de su escenario. El artículo [Escenarios para la Tecnología y procesos de análisis avanzado (ADAPT) en Aprendizaje automático de Azure](../machine-learning-data-science-plan-sample-scenarios.md) le ayudará a determinar los recursos que necesita para una variedad de flujos de trabajo de ciencia de datos utilizados en el proceso de análisis avanzado.

> [AZURE.NOTE]Para ver una introducción completa al almacenamiento de blobs de Azure, consulte [Aspectos básicos del blob de Azure](../storage-dotnet-how-to-use-blobs.md) y [Servicio BLOB de Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

> [AZURE.TIP] Como alternativa, puede usar [Factoría de datos de Azure](https://azure.microsoft.com/services/data-factory/) para crear y programar una canalización que descargará datos desde el almacenamiento de blobs de Azure, los pasará a un servicio web publicado de Aprendizaje automático de Azure, recibirá los resultados de análisis predictivos y cargará los resultados en el almacenamiento. Consulte [Creación de canalizaciones predictivas mediante Factoría de datos de Azure y Aprendizaje automático de Azure](data-factory-azure-ml-batch-execution-activity.md) para obtener más información.

## Requisitos previos

En este documento se supone que tiene una suscripción de Azure y una cuenta de almacenamiento y la clave de almacenamiento correspondiente para dicha cuenta. Antes de cargar o descargar datos, debe conocer su nombre de cuenta de almacenamiento de Azure y la clave de cuenta.

- Para configurar una suscripción de Azure, consulte [Prueba gratuita de un mes](https://azure.microsoft.com/pricing/free-trial/).
- Para obtener instrucciones sobre la creación de una cuenta de almacenamiento y para obtener información de cuentas y claves, vea [Acerca de las cuentas de almacenamiento de Azure](../storage-create-storage-account.md).




 

<!---HONumber=AcomDC_0211_2016-->