<properties
	pageTitle="Paso 2: Carga de datos en un experimento de Aprendizaje automático | Microsoft Azure"
	description="Paso 2 del tutorial Desarrollo de una solución predictiva: carga de datos públicos almacenados en Estudio de aprendizaje automático de Azure."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016" 
	ms.author="garye"/>


# Paso 2 del tutorial: Carga de los datos existentes en un experimento de Aprendizaje automático de Azure

Este es el segundo paso del tutorial [Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Creación de un área de trabajo de Aprendizaje automático](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	**Carga de los datos existentes**
3.	[Crear un experimento nuevo](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Entrenamiento y evaluación de los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Implementación del servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)

----------

Para desarrollar un modelo de predicción de riesgo de crédito, se necesitan datos para entrenar y probar el modelo. Para este tutorial, se usará el conjunto de datos "UCI Statlog (German Credit Data)" del repositorio de Aprendizaje automático de UCI. Puede encontrarlo aquí: <a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Usaremos el archivo llamado **german.data**. Descargue este archivo en la unidad de disco duro local.

Este conjunto de datos contiene filas de 20 variables para 1000 solicitantes de crédito. Estas 20 variables representan el vector de características del conjunto de datos que proporciona características de identificación de cada solicitante de crédito. Una columna adicional en cada fila representa el riesgo de crédito del solicitante, donde 700 solicitantes se identificaron como de bajo riesgo y 300 como de alto riesgo.

El sitio web de UCI ofrece una descripción de los atributos del vector de características que incluyen información financiera, historial de crédito, estado de empleo e información personal. A cada solicitante se le ha dado una calificación binaria para indicar si son de riesgo de crédito alto o bajo.

Estos datos los usaremos para entrenar un modelo de análisis predictivo. Cuando hayamos acabado, nuestro modelo deberá poder aceptar información de nuevos individuos y predecir si representan un riesgo de crédito alto o bajo.

Aquí hay un giro interesante. La descripción del conjunto de datos explica que clasificar erróneamente una persona como de riesgo de crédito bajo cuando en realidad es de riesgo de crédito alto es cinco veces más costoso para la entidad financiera que clasificar erróneamente un riesgo de crédito bajo como alto. Una forma sencilla de tener esto en cuenta en nuestro experimento es duplicar (5 veces) esas entradas que representan a alguien con un riesgo de crédito alto. Luego, si el modelo clasifica erróneamente un riesgo de crédito alto como bajo, lo hará cinco veces, una por cada duplicado. Esto aumentará el coste de este error en los resultados del entrenamiento.

##Conversión del formato del conjunto de datos
El conjunto de datos original utiliza un formato separado por espacios en blanco. Estudio de aprendizaje automático funciona mejor con un archivo de valores delimitados por comas (CSV), así que vamos a convertir el conjunto de datos y reemplazar los espacios por comas.

Hay muchas maneras de convertir los datos. En primer lugar, es posible convertirlos con el siguiente comando de Windows PowerShell:

	cat german.data | %{$_ -replace " ",","} | sc german.csv  

También se puede hacer con el comando sed de Unix:

	sed 's/ /,/g' german.data > german.csv  

En cualquier caso, se creará una versión separada por comas de los datos en un archivo denominado **german.csv** que usaremos en nuestro experimento.

##Carga del conjunto de datos en Estudio de aprendizaje automático

Una vez que los datos se han convertido al formato CSV, debemos cargarlos en el Estudio de aprendizaje automático. Para obtener más información acerca de cómo empezar a utilizar Estudio de aprendizaje automático, consulte [Inicio de Estudio de aprendizaje automático de Microsoft Azure](https://studio.azureml.net/).

1.	Abra Estudio de aprendizaje automático: [https://studio.azureml.net](https://studio.azureml.net). Cuando se le pida que inicie sesión, use la cuenta que especificó como el propietario del área de trabajo.
1.  Haga clic en la pestaña **Estudio** en la parte superior de la página.
1.	Haga clic en **+NUEVO** en la parte inferior de la página.
1.	Seleccione **CONJUNTO DE DATOS**.
1.	Seleccione **DE ARCHIVO LOCAL**.
1.	En el cuadro de diálogo **Cargar un nuevo conjunto de datos**, haga clic en **Examinar** y busque el archivo **german.csv** que creó.
1.	Escriba un nombre para el conjunto de datos. En este tutorial, lo llamaremos "UCI German Credit Card Data".
1.	Para el tipo de datos, seleccione **Archivo CSV genérico sin encabezado (.nh.csv)**.
1.	Agregue una descripción si así lo desea.
1.	Haga clic en **Aceptar**.  

![Carga del conjunto de datos][1]


De esta manera los datos se cargan en un módulo de conjunto de datos que podemos usar en un experimento.

> [AZURE.TIP] Para administrar los conjuntos de datos que cargó en el Estudio, haga clic en la ficha **CONJUNTOS DE DATOS** a la izquierda de la ventana de Estudio.

Para obtener más información acerca de la importación de diversos tipos de datos a un experimento, consulte [Importar datos de entrenamiento a Estudio de aprendizaje automático de Azure](machine-learning-data-science-import-data.md).

**Siguiente: [Crear un experimento nuevo](machine-learning-walkthrough-3-create-new-experiment.md)**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png

<!---HONumber=AcomDC_0211_2016-->