<properties 
	pageTitle="Cómo elegir parámetros para optimizar los algoritmos de Aprendizaje automático de Azure | Microsoft Azure" 
	description="Explica cómo elegir el parámetro óptimo establecido para un algoritmo de Aprendizaje automático de Azure." 
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
	ms.date="02/28/2016" 
	ms.author="bradsev" />


# Cómo elegir parámetros para optimizar los algoritmos de Aprendizaje automático de Azure

En este tema se describe cómo elegir el hiperparámetro adecuado establecido para un algoritmo en Aprendizaje automático de Azure. La mayoría de algoritmos de aprendizaje automático dependen de varios parámetros. Cuando se entrena un modelo, tenemos que proporcionar valores para esos parámetros. La eficacia del modelo entrenado depende de los parámetros del modelo que se elija. El proceso de encontrar el conjunto óptimo de parámetros se conoce como selección del modelo.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Hay varias maneras de realizar la selección del modelo. En el aprendizaje automático, la validación cruzada es uno de los métodos más utilizados para la selección del modelo y es el mecanismo de selección de modelo predeterminado en Aprendizaje automático de Azure. Debido a que R y Python son compatibles con el Aprendizaje automático de Azure, los usuarios siempre pueden implementar su propio mecanismo de selección de modelo mediante R o Python.

Hay cuatro pasos en el proceso de encontrar el mejor conjunto de parámetros.

1.	**Definir espacio de parámetro**: para el algoritmo, primero decidimos los valores de parámetro exactos que nos gustaría tener en cuenta. 
2.	**Definir la configuración de validación cruzada**: para el conjunto de datos, debemos decidir cómo elegir subconjuntos de validación cruzada. 
3.	**Definir métrica**: a continuación, decidimos qué métrica se va a utilizar para determinar el mejor conjunto de parámetros, por ejemplo, la precisión, el error cuadrático medio raíz, la precisión, la recuperación o la puntuación f. 
4.	**Formar, evaluar y comparar**: para cada combinación única de los valores de parámetro, se lleva a cabo la validación cruzada y se basa en la métrica de error definida por el usuario, y se puede elegir el modelo que mejor rinde.

El siguiente procedimiento muestra cómo se puede lograr esto en Aprendizaje automático de Azure.

![imagen1](./media/machine-learning-algorithm-parameters-optimize/fig1.png)
 
## Definir el espacio de parámetros
El conjunto de parámetros puede definirse en el paso de inicialización del modelo. El panel de parámetros de todos los algoritmos de aprendizaje automático tiene dos modos de instructor: de **único parámetro** y de **rango de parámetros**. Se debe elegir el modo de **rango de parámetros** (figura 1). Este permite introducir varios valores para cada parámetro: se pueden especificar valores separados por comas en el cuadro de texto. También se puede usar **Usar generador de intervalo** para definir los puntos máximos y mínimos de la cuadrícula y el número total de puntos que se va a generar. De forma predeterminada se generan los valores de parámetro en una escala interior. Pero si el cuadro **Escala logarítmica** está activado, los valores se generan en la escala logarítmica (es decir, la relación de los puntos adyacentes es constante en lugar de su diferencia). Para los parámetros de número entero se puede definir un intervalo con un guión "-", p. ej. "1-10", que significa que todos los valores enteros entre 1 y 10 (ambos inclusive) forman el conjunto de parámetros. También se admite un modo mixto, por ejemplo, “1-10, 20, 50”. En este caso, además de los números enteros de 1 a 10, 20 y 50 también se agregarán al conjunto de parámetros.
  
![imagen2](./media/machine-learning-algorithm-parameters-optimize/fig2.png) ![imagen3](./media/machine-learning-algorithm-parameters-optimize/fig3.png)

## Definición de plegamiento de validación cruzada
El módulo de [partición y ejemplo][partition-and-sample] se puede usar para asignar pliegues a los datos de forma aleatoria. En la siguiente ilustración, vemos un ejemplo de configuración para el módulo donde definimos 5 subconjuntos y asignamos aleatoriamente un número de subconjunto a las instancias de ejemplo.

![imagen4](./media/machine-learning-algorithm-parameters-optimize/fig4.png)


## Definir métrica
El módulo [Parámetros de barrido][sweep-parameters] proporciona compatibilidad para elegir empíricamente el mejor conjunto de parámetros para un algoritmo determinado y el conjunto de datos. El panel de propiedades de este módulo incluye, además de otra información referente a entrenar el modelo, la métrica que se utilizará para determinar el mejor conjunto de parámetros. Tiene dos listas desplegables diferentes para los algoritmos de clasificación y regresión, respectivamente. Si el algoritmo en cuestión es un algoritmo de clasificación, se ignorará la métrica de regresión y viceversa. En este ejemplo concreto, elegimos **Precisión** como la métrica.
 
![imagen5](./media/machine-learning-algorithm-parameters-optimize/fig5.png)

## Formar, evaluar y comparar:  
El mismo módulo [Parámetros de barrido][sweep-parameters] entrena todos los modelos correspondientes al conjunto de parámetros, evalúa varias métricas y, a continuación, genera el mejor modelo formado en función de la métrica elegida por el usuario. Este módulo contiene dos entradas obligatorias

* el aprendiz no formado 
* el conjunto de datos 

y una entrada de conjunto de datos opcional. Conectamos el conjunto de datos con información de plegado a la entrada del conjunto de datos obligatorios. Si el conjunto de datos no se asigna a ninguna información de plegado, se ejecuta automáticamente una validación cruzada 10 veces más rápida de forma predeterminada. Si no se realiza la asignación de plegamiento y se proporciona un conjunto de datos de validación en el puerto opcional del conjunto de datos, a continuación, se utiliza un modo de prueba de formación elegido y el primer conjunto de datos para entrenar el modelo para cada combinación de parámetros y, a continuación, se evalúa en el conjunto de datos de validación. El puerto de salida izquierdo del módulo muestra métricas diferentes como función de los valores del parámetro. El puerto de salida derecho proporciona el modelo formado correspondiente al modelo de máximo rendimiento según la métrica elegida por el usuario (precisión en este caso).

![imagen6](./media/machine-learning-algorithm-parameters-optimize/fig6a.png) ![imagen7](./media/machine-learning-algorithm-parameters-optimize/fig6b.png)
 
Podemos ver los parámetros exactos elegidos mediante la visualización del puerto de salida correcto. Este modelo puede utilizarse para determinar la puntuación de un conjunto de pruebas o en un servicio web de operaciones después de guardarlo como un modelo formado.


<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[sweep-parameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
 

<!---HONumber=AcomDC_0302_2016-->