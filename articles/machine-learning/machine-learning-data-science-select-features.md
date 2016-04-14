<properties
	pageTitle="Selección de características en el proceso de análisis de Cortana | Microsoft Azure" 
	description="Aquí se explica el propósito de la selección de características y ofrece ejemplos de su rol en el proceso de mejora de los datos del aprendizaje automático."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="zhangya;bradsev" />


# Selección de características en el proceso de análisis de Cortana

En este tema se explican los propósitos de la selección de características y ofrece ejemplos de su rol en el proceso de mejora de los datos del aprendizaje automático. Estos ejemplos se extraen de Estudio de aprendizaje automático de Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Introducción

En este tema se explica el propósito de la selección de características y ofrece ejemplos de su rol en el proceso de mejora de los datos del aprendizaje automático. Estos ejemplos se extraen de Estudio de aprendizaje automático de Azure.

La ingeniería y la selección de características es una parte del proceso de CAP descrito en [¿Cuál es el proceso de análisis de Cortana?](machine-learning-data-science-the-cortana-analytics-process.md) La selección y la ingeniería de características son partes del paso **Desarrollo de características** del CAP. * **Ingeniería de características**: este proceso intenta crear otras características pertinentes de las características sin procesar existentes de los datos, y aumentar la eficacia de predicción para el algoritmo de aprendizaje. * **Selección de características**: este proceso, selecciona el subconjunto de claves de características de datos originales en un intento de reducir las dimensionalidad del problema de entrenamiento.

Normalmente, la **ingeniería de características** se aplica primero para generar características adicionales y, a continuación, se realiza el paso de **selección de características** para eliminar características irrelevantes, redundantes o altamente correlacionadas.


## Filtrado de características desde sus datos: selección de características 

La selección de características es un proceso que normalmente se aplica para la construcción de conjuntos de datos de entrenamiento para tareas de modelado predictivo, como las tareas de clasificación o de regresión. El objetivo es seleccionar un subconjunto de las características a partir del conjunto de datos original que reduce sus dimensiones al usar un conjunto de características mínimo para que represente la cantidad máxima de varianza en los datos. De este modo, este subconjunto de características son las únicas características que se incluirán para entrenar el modelo. La selección de características tiene dos propósitos principales.

* En primer lugar, la selección de características a menudo aumenta la precisión de la clasificación a través de la eliminación de características irrelevantes, redundantes o altamente correlacionadas.
* En segundo lugar, disminuye la cantidad de características, lo que hace que el proceso de entrenamiento del modelo sea más eficiente. Esto resulta especialmente importante cuando se trata de sistemas aprendices que son costosos de entrenar, como las máquinas de vectores de soporte.

A pesar de que la selección de características sí busca disminuir la cantidad de características en el conjunto de datos que se usa para entrenar el modelo, no es frecuente referirse a ella con el término "reducción de la dimensionalidad". Los métodos de selección de características extraen un subconjunto de las características originales de los datos sin cambiarlas. Los métodos de reducción de dimensionalidad emplean características diseñadas que pueden transformar las características originales y, de ese modo, modificarlas. Algunos ejemplos de los métodos de reducción de dimensionalidad incluyen el análisis del componente principal, el análisis de correlación canónica y la descomposición en valores singulares.

Entre otros aspectos, una categoría ampliamente aplicada de los métodos de selección de categorías en un contexto supervisado se llama "selección de características basada en filtro". Mediante la evaluación de la correlación entre cada característica y el atributo de destino, estos métodos aplican una medida estadística para asignar una puntuación a cada característica. A continuación, las características se clasifican según la puntuación, lo que se puede usar para definir el umbral para conservar o eliminar una característica específica. Algunos ejemplos de las medidas estadísticas que se usan en estos métodos incluyen la correlación de Pearson, la información mutua y la prueba de Chi-cuadrado.

En Estudio de aprendizaje automático de Azure, estos son los módulos proporcionados para la selección de características. Tal como aparece en la figura siguiente, estos módulos incluyen [Selección de características basada en filtro][filter-based-feature-selection] y [Análisis discriminante lineal de Fisher][fisher-linear-discriminant-analysis].

![Ejemplo de selección de características](./media/machine-learning-data-science-select-features/feature-Selection.png)


Por ejemplo, considere el uso del módulo [Selección de características basada en filtro][filter-based-feature-selection]. Para mayor comodidad, seguiremos usando el ejemplo de minería de texto descrito anteriormente. Suponga que deseamos crear un modelo de regresión una vez creado un conjunto de 256 características mediante el módulo [Hash de características][feature-hashing] y que la variable de respuesta es la "Col1" y representa una clasificación de las reseñas de un libro, que van desde 1 a 5. Defina el "Método de puntuación de características" en "Correlación de Pearson", la "Columna de destino" en "Col1" y el "Número de características deseadas" en 50. A continuación, el módulo [Selección de características basada en filtro][filter-based-feature-selection] generará un conjunto de datos con 50 características, junto con el atributo de destino "Col1". La figura siguiente muestra el flujo de este experimento y los parámetros de entrada que acabamos de describir.

![Ejemplo de selección de características](./media/machine-learning-data-science-select-features/feature-Selection1.png)

La figura siguiente muestra los conjuntos de datos resultantes. Cada características recibe una puntuación según la correlación de Pearson entre sí misma y el atributo de destino "Col1". Las características con las mayores puntuaciones se conservan.

![Ejemplo de selección de características](./media/machine-learning-data-science-select-features/feature-Selection2.png)

La figura siguiente muestra las puntuaciones correspondientes de las características seleccionadas.

![Ejemplo de selección de características](./media/machine-learning-data-science-select-features/feature-Selection3.png)

A través de la aplicación de este módulo [Selección de características basada en filtro][filter-based-feature-selection], se seleccionan 50 de las 256 características, debido a que tienen las características más correlacionadas con la variable de destino "Col1", según el método de puntuación "Correlación de Pearson".

## Conclusión
La selección de características y la ingeniería de características son dos características diseñadas y seleccionadas frecuentemente que aumentan la eficiencia del proceso de entrenamiento en el que se intenta extraer la información clave que contienen los datos. También mejoran la eficacia de estos modelos para clasificar los datos de entrada de manera precisa y para predecir resultados de interés de manera más sólida. El diseño y la selección de características también se pueden combinar para que sea posible hacer un mejor seguimiento computacional del aprendizaje. Para ello, mejora y luego reduce el número de características que se necesitan para calibrar o entrenar un modelo. Matemáticamente hablando, las características seleccionadas para entrenar el modelo son un conjunto mínimo de variables independientes que explican los patrones existentes en los datos y, a continuación, predicen correctamente los resultados.

Observe que no siempre es necesario realizar el diseño o la selección de características. Si es necesario o no depende de los datos que se tengan o que se hayan recopilado, del algoritmo que se elija y del objetivo del experimento.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
 

<!---HONumber=AcomDC_0211_2016-->