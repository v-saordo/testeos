<properties 
	pageTitle="Interpretación de los resultados del modelo de Aprendizaje automático | Microsoft Azure" 
	description="Cómo elegir el conjunto de parámetros óptimo para un algoritmo que use y visualice resultados del modelo de puntuación." 
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
	ms.date="12/04/2015" 
	ms.author="bradsev" />


# Cómo interpretar los resultados del modelo de aprendizaje automático de Azure 
 
**Descripción y visualización del resultado del 'Modelo de puntuación'** En este tema se explica cómo ver e interpretar los resultados de predicción en el estudio de aprendizaje automático de Azure. Después de entrenar un modelo y realizar predicciones sobre él ("puntuar el modelo"), deberá comprender e interpretar el resultado de predicción que ha obtenido.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Hay cuatro tipos de modelos de aprendizaje automático principales en Aprendizaje automático de Azure:

* clasificación
* agrupación en clústeres
* regresión
* sistemas de recomendación

Los módulos usados para realizar una predicción de dichos módulos, lo cual se denomina "puntuación", dados algunos datos de prueba, son los siguientes:

* módulo [Modelo de puntuación][score-model] para la clasificación y regresión; 
* módulo [Asignar a clústeres][assign-to-clusters] para la agrupación en clústeres; 
* [Score Matchbox Recommender][score-matchbox-recommender] para sistemas de recomendación. 
 
Este documento explica cómo interpretar los resultados de predicción para cada uno de estos módulos. Para obtener información general sobre estos tipos de modelos, vea [Cómo elegir parámetros para optimizar los algoritmos de Aprendizaje automático de Azure](machine-learning-algorithm-parameters-optimize.md).

Este tema aborda la interpretación de predicción, pero no la evaluación de modelos. Para información acerca de cómo evaluar su modelo, vea [Cómo evaluar el rendimiento del modelo en Aprendizaje automático de Azure](machine-learning-evaluate-model-performance.md).

Si no está familiarizado con Aprendizaje automático de Azure y necesita ayuda para crear un experimento simple para comenzar, vea [Crear un experimento simple en Estudio de aprendizaje automático de Azure](machine-learning-create-experiment.md) en el Estudio de aprendizaje de automático de Azure.

##Clasificación
Existen dos subcategorías de problemas de clasificación:

* problemas con solo dos clases (clasificación de dos clases o binaria) 
* problemas con más de dos clases (clase de clasificación multiclase) 

Aprendizaje automático de Azure tiene distintos módulos para cada uno de estos tipos de clasificación. No obstante, las formas de interpretar los resultados de predicción son muy similares. Hablaremos acerca de los problemas de clasificación de dos clases primero y, a continuación, abordaremos los problemas de clasificación multiclase.

###Clasificación multiclase
**Experimento de ejemplo**

Un ejemplo de problema de clasificación de dos clases puede ser la clasificación de flores Iris: la tarea consiste en clasificar flores Iris en función de sus características. Tenga en cuenta que el conjunto de datos de Iris proporcionado en Aprendizaje automático de Azure es un subconjunto del [conjunto de datos de Iris](http://en.wikipedia.org/wiki/Iris_flower_data_set) popular, que contiene instancias de solo 2 especies de flor (clases 0 y 1). Existen cuatro características para cada flor (longitud del sépalo, ancho del sépalo, longitud del pétalo y ancho del pétalo).

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/1.png)

Figura 1: Experimento del problema de clasificación de dos clases de Iris

Como se muestra en la figura 1, se ha realizado un experimento para solucionar este problema. Se ha entrenado y puntuado un modelo de árbol de decisión de dos clases. Ahora podemos visualizar los resultados de predicción del módulo [Modelo de puntuación][score-model] módulo haciendo clic en el puerto de salida del módulo [Modelo de puntuación][score-model] y, a continuación, haciendo clic en **Visualizar** en el menú que aparece. Esto mostrará los resultados de puntuación, como se muestra en la figura 2.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/1_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/2.png)

Figura 2 Visualizar el resultado del modelo de puntuación en la clasificación de dos clases

**Interpretación de resultados**

Hay seis columnas en la tabla de resultados. Las cuatro columnas de la izquierda son las cuatro características. Las dos columnas de la derecha, Etiquetas puntuadas y Probabilidades puntuadas, son los resultados de predicción. La columna Probabilidades puntuadas muestra la probabilidad de que una flor pertenece a la clase positiva (clase 1). Por ejemplo, el primer número 0,028571 de la columna significa que hay una probabilidad de 0,028571 de que la primera flor pertenezca a la clase 1. La columna Etiquetas puntuadas muestra la clase prevista de cada flor. Esto se basa en la columna Probabilidades de puntuación. Si la probabilidad puntuada de una flor es mayor que 0,5, se prevé que sea la clase 1; en caso contrario, se prevé que sea la clase 0.

**Publicación de servicios web**

Una vez comprendidos los resultados de la predicción, el experimento puede publicarse como un servicio web, de forma que se pueda implementar en diversas aplicaciones y se le pueda llamar para que obtenga las predicciones de clase en cualquier flor de iris nueva. Para conocer el procedimiento de cómo cambiar un experimento de entrenamiento a un experimento de puntuación y publicarlo como un servicio web, vea [Publicar el servicio web de Aprendizaje automático de Azure](machine-learning-walkthrough-5-publish-web-service.md). Este procedimiento proporciona un experimento de puntuación, como muestra la figura 3.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/3.png)

Figura 3: Experimento de puntuación del problema de clasificación de dos clases de Iris

Ahora debemos establecer la entrada y salida del servicio web. Obviamente, la entrada es el puerto de entrada derecho del [Módulo de puntuación][score-model], que es la entrada de las características de la flor Iris. La elección del resultado depende de si estamos interesados en la clase de predicción (etiqueta puntuada), en la probabilidad puntuada o en ambas. En este caso, se supone que estamos interesados en ambas. Para seleccionar las columnas de salida deseadas, debemos usar un módulo [Columnas de proyecto][project-columns]. Haga clic en el módulo [Columnas de proyecto][project-columns], haga clic en **Selector de columna de inicio** en el panel derecho y seleccione **Etiquetas puntuadas** y **Probabilidades puntuadas**. Después de configurar el puerto de salida del módulo [Columnas de proyecto][project-columns] y ejecutarlo de nuevo, deberíamos estar preparados para publicar el experimento de puntuación como un servicio web haciendo clic en **PUBLICAR SERVICIO WEB** en la parte inferior. El experimento final es similar a la figura 4.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/4.png)

Figura 4: Experimento de puntuación final del problema de clasificación de dos clases de Iris

Después de la ejecución del servicio web y de escribir algunos valores de las características de una instancia de prueba, el resultado devuelve dos números. El primer número es la etiqueta puntuada, y el segundo es la probabilidad puntuada. Se prevé que esta flor sea de clase 1 con probabilidad de 0,9655.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/4_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/5.png)

Figura 5: Resultado de servicio web de la clasificación de dos clases de Iris

###Clasificación multiclase
**Experimento de ejemplo**

En este experimento se llevará a cabo una tarea de reconocimiento de letras como un ejemplo de clasificación multiclase. El clasificador intentará predecir una letra determinada (clase), dados algunos valores de atributo escritos a mano extraídos de las imágenes realizadas a mano. En los datos de entrenamiento, hay dieciséis características extraídas de imágenes de letras escritas a mano. Las veintiséis letras forman nuestras veintiséis clases. Un experimento se ha configurado para entrenar un modelo de clasificación multiclase para el reconocimiento de letras y predecir en el mismo conjunto de características de un conjunto de datos de prueba, como se muestra en la figura 6.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/5_1.png)
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/6.png)

Figura 6: Experimento del problema de clasificación multiclase de reconocimiento de letras

Al visualizar los resultados del módulo [Modelo de puntuación][score-model] haciendo clic con el botón principal o secundario en el puerto de salida del módulo [Modelo de puntuación][score-model] y, a continuación, haciendo clic en **Visualizar**, verá una ventana como la que se muestra en la figura 7.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/7.png)

Figura 7: Visualizar el resultado del modelo de puntuación en la clasificación multiclase

**Interpretación de resultados**

Las dieciséis columnas de la izquierda representan los valores de características del conjunto de pruebas. Las columnas con nombres Probabilidades puntuadas de la clase "XX" son iguales que la columna Probabilidades de puntuación en el caso de dos clases. Muestran la probabilidad de que la entrada correspondiente se encuentre en una clase determinada. Por ejemplo, para la primera entrada, hay una probabilidad de 0,003571 de que sea una "A", una probabilidad de 0,000451 de que sea una "B", etc. Las última columna de Etiquetas puntuadas es la misma que la de Etiquetas puntuadas del caso de dos clases. Selecciona la clase con la mayor probabilidad puntuada como la clase de predicción de la entrada correspondiente. Por ejemplo, para la primera entrada, la etiqueta puntuada es "F", ya que tiene la mayor probabilidad de que sea una "F" (0,916995).

**Publicación de servicios web**

Esta vez, en lugar de usar las [Columnas de proyecto][project-columns] para seleccionar algunas columnas como el resultado de nuestro servicio web, nos gustaría obtener la etiqueta puntuada para cada entrada y la probabilidad de la etiqueta puntuada. La lógica básica es encontrar la probabilidad más alta entre todas las probabilidades puntuadas. Para ello, debemos usar el módulo [Ejecutar script R][execute-r-script]. El código de R se muestra en la figura 8, y el experimento es como la figura 9.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/8.png)

Figura 8: Código R para extraer etiquetas puntuadas y las probabilidades asociadas de las etiquetas
  
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/9.png)

Figura 9: Experimento de puntuación final del problema de clasificación multiclase de reconocimiento de letras

Después de publicar y ejecutar el servicio web y especificar algunos valores de características de entrada, el resultado devuelto será como el mostrado en la figura 10. Se prevé que esta letra escrita a mano, con sus dieciséis características extraídas, sea una "T", con una probabilidad de 0,9715.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/9_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/10.png)

Figura 10: Resultado de servicio web de la clasificación de dos clases de Iris

##Regresión

Los problemas de regresión son diferentes de los problemas de clasificación. En un problema de clasificación, intentamos predecir clases discretas, como a qué clase pertenece una flor Iris. Pero en un problema de regresión, intentamos predecir una variable continua, como el precio de un automóvil, como puede ver en el ejemplo siguiente.

**Experimento de ejemplo**

Usaremos la predicción del precio de automóviles como nuestro ejemplo de regresión. Intentaremos predecir el precio de un automóvil en función de sus características, como la fabricación, el tipo de combustible, el tipo de carrocería, el volante, etc. El experimento se muestra en la figura 11.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/11.png)

Figura 11: Experimento de problema de regresión de precios de automóviles

Al ver el módulo [Modelo de puntuación][score-model], el resultado es parecido al de la figura 12.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/12.png)

Figura 12: Visualización de los resultados de puntuación del problema de predicción de precios de automóviles

**Interpretación de resultados**

En el resultado de esta puntuación, Etiquetas puntuadas es la columna de resultados. Los números son el precio de predicción de cada uno.

**Publicación de servicios web**

Podemos publicar el experimento de regresión en un servicio web y llamarlo para la predicción de precios de automóviles de la misma forma que con el caso de uso de clasificación de dos clases.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/13.png)

Figura 13: Experimento de puntuación de problema de regresión de precios de automóviles

Al ejecutar el servicio web, el resultado devuelto es similar al de la figura 14. El precio de predicción para este automóvil es de 15085,52.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/13_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/14.png)

Figura 14: Resultado del servicio web del problema de regresión de precios de automóviles

##Agrupación en clústeres

**Experimento de ejemplo**

Usemos de nuevo el conjunto de datos de Iris para generar un experimento de agrupación en clústeres. Aquí, filtramos las etiquetas de clase del conjunto de datos, de forma que solo tenga características y pueda usarse para la agrupación en clústeres. En este caso de uso de Iris, especificaremos el número de clústeres en 2 durante el proceso de entrenamiento, lo que significa que nos gustaría agrupar las flores en dos clases. El experimento se muestra en la figura 15.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/15.png)

Figura 15: Experimento del problema de agrupación en clústeres de Iris

La agrupación en clústeres difiere de clasificación en que el conjunto de datos de entrenamiento no tiene etiquetas de realidad por sí mismo. En su lugar, nos interesa cómo agrupar las instancias de conjunto de datos de entrenamiento en clústeres distintos. Durante el proceso de entrenamiento, el modelo etiqueta las entradas al conocer las diferencias entre sus características. Después, el modelo de aprendizaje se puede usar para clasificar entradas futuras. Hay dos partes del resultado que nos interesan en un problema de agrupación en clústeres. La primera parte es cómo etiquetar el conjunto de datos de entrenamiento; la segunda parte es cómo clasificar un nuevo conjunto de datos con el modelo entrenado.

La primera parte del resultado se puede visualizar haciendo clic en el puerto de salida izquierdo del módulo [Entrenar modelo de agrupación en clústeres][train-clustering-model] y haciendo clic en **Visualizar** posteriormente. La ventana de visualización se muestra en la figura 16.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/16.png)

Figura 16: Visualización de resultado de agrupación en clústeres para el conjunto de datos de entrenamiento

El resultado de la segunda parte, agrupación en clústeres nuevas entradas con el modelo de agrupación en clústeres de aprendizaje, se muestra en la figura 17.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/17.png)

Figura 17: Visualización de resultado de agrupación en clústeres en el nuevo conjunto de datos

**Interpretación de resultados**

Aunque los resultados de las dos partes se derivan de diferentes fases del experimento, tienen exactamente el mismo aspecto y se interpretan de la misma manera. Las primeras cuatro columnas son características. La última columna, Asignaciones, es el resultado de predicción. Se prevé que las entradas asignadas al mismo número estén en el mismo clúster, es decir, que compartan similitudes de alguna manera (hemos utilizado la métrica de distancia euclidiana predeterminada en este experimento). Recuerde que hemos especificado el número de clústeres como 2. Por tanto, en la columna Asignaciones, las entradas se etiquetan como 0 o como 1.

**Publicación de servicios web**

Podemos publicar el experimento de agrupación en clústeres en un servicio web y llamarlo para las predicciones de agrupación en clústeres de de la misma forma que con el caso de uso de clasificación de dos clases.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/18.png)

Figura 18: Experimento de puntuación del problema de agrupación en clústeres de Iris

Tras ejecutar el servicio web, el resultado devuelto es similar al de la figura 19. Se prevé que esta flor esté en el clúster 0.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/18_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/19.png)

Figura 19: Resultado de servicio web de la clasificación de dos clases de Iris

##Sistema de recomendación
**Experimento de ejemplo**

Para los sistemas de recomendación, usaremos el problema de la recomendación de restaurante como ejemplo: para recomendar restaurantes a los clientes basándose en su historial de valoración. Los datos de entrada constan de tres partes:

* valoraciones de restaurantes de los clientes; 
* datos de características de los clientes; 
* datos de características de restaurantes. 

Hay varias cosas que podemos hacer con el módulo integrado [Train Matchbox Recommender][train-matchbox-recommender] del Aprendizaje automático de Azure:

- predecir las valoraciones para un usuario determinado y un elemento;
- recomendar elementos a un usuario determinado;
- buscar usuarios relacionados con un usuario determinado;
- buscar elementos relacionados con un elemento determinado.

Podemos elegir lo que queremos hacer mediante la selección de las cuatro opciones en el menú **Tipo de predicción de recomendación** en el panel derecho. En este caso, le mostraremos los cuatro escenarios. Un experimento de Aprendizaje automático de Azure típico para el sistema de recomendación es similar al de la figura 20. Para información sobre cómo usar los módulos del sistema de recomendación, vea la página de ayuda [Train Matchbox Recommender][train-matchbox-recommender] y [Score Matchbox Recommender][score-matchbox-recommender].
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/19_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/20.png)

Figura 20: Experimento del sistema de recomendación

**Interpretación de resultados**

*Predecir las valoraciones para un usuario determinado y un elemento*

Al seleccionar la predicción de valoración en el menú **Tipo de predicción de recomendación**, pedimos al sistema de recomendación que prediga la valoración de un usuario y un elemento determinados. La visualización del resultado del [Score Matchbox Recommender][score-matchbox-recommender] es similar a la de la figura 21.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/21.png)

Figura 21: Visualización del resultado de puntuación del sistema de recomendación: valoración de predicción

Hay tres columnas. Las dos primeras columnas son los pares de elemento-usuario proporcionados por los datos de entrada. La tercera columna es la valoración de predicción de un usuario para un elemento determinado. Por ejemplo, en la primera fila, se prevé que el cliente U1048 valore el restaurante 135026 como 2.

*Recomendar elementos a un usuario determinado*

Al seleccionar **Recomendación de elementos** en el menú **Tipo de predicción de recomendación**, pedimos al sistema de recomendación que recomiende elementos a un usuario determinado. Hay un parámetro más que necesitamos elegir en este escenario, la selección de elementos recomendados. La opción **De elementos valorados (para la evaluación de modelos)** es principalmente para la evaluación de modelos durante el proceso de entrenamiento. Para esta fase de predicción, elegiremos **De todos los elementos**. La visualización del resultado del Score Matchbox Recommender es similar a la de la figura 22.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/22.png)

Figura 22: Visualización del resultado de puntuación del sistema de recomendación: Recomendación de elementos

Hay seis columnas. La primera columna representa los id. de usuario determinados para los que recomendar elementos, proporcionados por los datos de entrada. Las cinco columnas restantes representan los elementos que se recomiendan para el usuario, en orden descendente en términos de relevancia. Por ejemplo, en la primera fila, el restaurante más recomendado para el cliente U1048 es 134986, seguido de 135018, 134975, 135021 y 132862.

*Buscar usuarios relacionados con un usuario determinado*

Al seleccionar usuarios relacionados en el menú “Tipo de predicción de recomendación”, pedimos al sistema de recomendación que busque usuarios relacionados a un usuario determinado. Los usuarios relacionados son los usuarios que tienen preferencias similares. Hay un parámetro más que necesitamos elegir en este escenario, la selección de usuarios relacionados. La opción “De elementos valorados (para la evaluación de modelos)” es principalmente para la evaluación de modelos durante el proceso de entrenamiento. Para esta fase de predicción, elegiremos “De todos los usuarios”. La visualización del resultado del Score Matchbox Recommender es similar a la de la figura 23.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/23.png)

Figura 23: Visualización del resultado de puntuación del sistema de recomendación: usuarios relacionados

Hay seis columnas. La primera columna son los identificadores de usuario dados para buscar usuarios relacionados, proporcionados por los datos de entrada. Las cinco columnas restantes tienen los usuarios relacionados del usuario, en orden descendente en términos de relevancia. Por ejemplo, en la primera fila, el cliente más relevante para el cliente U1048 es U1051, seguido de U1066, U1044, U1017 y U1072.

**Buscar elementos relacionados con un elemento determinado**

Al seleccionar **Elementos relacionados** en el menú **Tipo de predicción de recomendación**, pedimos al sistema de recomendación que busque usuarios relacionados a un usuario determinado. Los elementos relacionados son los que tienen mayor probabilidad de estar vinculados al mismo usuario. Hay un parámetro más que necesitamos elegir en este escenario, la selección de usuarios relacionados. La opción **De elementos valorados (para la evaluación de modelos)** es principalmente para la evaluación de modelos durante el proceso de entrenamiento. Para esta fase de predicción, elegiremos **De todos los elementos**. La visualización del resultado del Score Matchbox Recommender es similar a la de la figura 24.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/24.png)

Figura 24: Visualización del resultado de puntuación del sistema de recomendación: elementos relacionados

Hay seis columnas. La primera columna representa los id. de elementos determinados para los que buscar elementos relacionados, proporcionados por los datos de entrada. Las cinco columnas restantes tienen los elementos relacionados del elemento, en orden descendente en términos de relevancia. Por ejemplo, en la primera fila, el elemento más relevante para el elemento 135026 es 135074, seguido de 135035, 132875, 135055 y 134992. Publicación de servicios web

El proceso de publicación de estos experimentos como servicios web para obtener predicciones es similar para cada uno de los cuatro escenarios. Aquí veremos el segundo escenario, el de recomendar elementos a un usuario determinado, por ejemplo. Puede seguir el mismo procedimiento con los otros tres.

Al guardar el sistema entrenado de recomendación como un modelo entrenado y filtrar los datos de entrada a una columna de identificador de usuario único como se solicitó, podemos enlazar el experimento como se muestra en la figura 25 y publicarlo como un servicio web.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/25.png)
 
Figura 25: Experimento de puntuación del problema de recomendación del restaurante

Al ejecutar el servicio web, el resultado devuelto es similar al de la figura 14. Los cinco restaurantes recomendados para usuario U1048 son 134986, 135018, 134975, 135021 y 132862.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/25_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/26.png)

Figura 26: Resultado de servicio web del problema de recomendación del restaurante


<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
 

<!---HONumber=AcomDC_1210_2015-->