<properties
	pageTitle="Importación de datos en Estudio de aprendizaje automático desde otro experimento | Microsoft Azure"
	description="Cómo guardar los datos de aprendizaje en Estudio de aprendizaje automático de Azure y usarlo en otro experimento."
	keywords="importar datos, datos, orígenes de datos, datos de entrenamiento"
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


# Importación de datos en Estudio de aprendizaje automático de Azure desde otro experimento

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Habrá ocasiones en las que querrá tomar un resultado intermedio de un experimento y usarlo como parte de otro experimento. Para ello, guarde el módulo como un conjunto de datos:

1. Haga clic con el botón secundario en la salida del módulo que desea guardar como conjunto de datos.

2. Haga clic en **Guardar como conjunto de datos**.

3. Cuando se le solicite, escriba un nombre y una descripción que le permitan identificar fácilmente el conjunto de datos.

4. Haga clic en la marca de verificación **Aceptar**.

Cuando termine de guardar, el conjunto de datos estará disponible para usarlo dentro de cualquier experimento de su área de trabajo. Puede encontrarlo en la lista **Conjuntos de datos guardados** de la paleta de módulos.

<!---HONumber=AcomDC_0204_2016-->