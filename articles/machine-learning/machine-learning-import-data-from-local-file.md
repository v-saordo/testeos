<properties
	pageTitle="Importación de datos en Estudio de aprendizaje automático desde un archivo local | Microsoft Azure"
	description="Cómo importar datos de aprendizaje en Estudio de aprendizaje automático de Azure desde un archivo local."
	keywords="importar datos, formato de datos, tipos de datos, orígenes de datos, datos de entrenamiento"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="garye;bradsev" />


# Importación de datos de aprendizaje en Estudio de aprendizaje automático de Azure desde un archivo local

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Para usar sus propios datos en Estudio de aprendizaje automático, puede cargar un archivo de datos por adelantado desde el disco duro local para crear un módulo de conjunto de datos en el área de trabajo.


## Importación de datos desde un archivo local

Para importar datos desde un disco duro local, siga estos pasos:

1. Haga clic en **+NUEVO** en la parte inferior de la ventana de Estudio de aprendizaje automático.
2. Seleccione **CONJUNTO DE DATOS** y **DESDE ARCHIVO LOCAL**.
3. En el cuadro de diálogo **Cargar un nuevo conjunto de datos**, vaya al archivo que desea cargar.
4. Escriba un nombre, identifique el tipo de datos y, si lo desea, escriba una descripción. Se recomienda incluir una descripción: le permite registrar cualquier característica acerca de los datos que querrá recordar cuando use los datos en el futuro.
5. La casilla **Esta es la versión nueva de un conjunto de datos existente** le permite actualizar una base de datos existente con datos nuevos. Haga clic en esta casilla y, a continuación, escriba el nombre de un conjunto de datos existente.

Durante la carga, verá un mensaje que le indica que se está cargando el archivo. El tiempo de la carga dependerá del tamaño de los datos y de la velocidad de conexión con el servicio. Si sabe que el archivo tardará mucho tiempo en cargarse, puede realizar otras operaciones en Estudio de aprendizaje automático mientras espera. Sin embargo, si cierra el explorador, la carga de los datos generará un error.

Una vez que los datos estén cargados, se almacenan en un módulo de conjunto de datos y se encontrarán disponibles para cualquier experimento que se realice en su área de trabajo. Puede encontrar la base de datos, junto con todos los conjuntos de datos de muestra cargados previamente, en la lista de **conjuntos de datos guardados** en la paleta de módulos cuando edita un experimento. Puede arrastrar y colocar el conjunto de datos en el lienzo de experimento cuando desee usarlo para profundizar en su análisis o para el aprendizaje automático.

<!---HONumber=AcomDC_0218_2016-->