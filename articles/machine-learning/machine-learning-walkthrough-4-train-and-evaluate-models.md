<properties
	pageTitle="Paso 4: Entrenamiento y evaluación de los modelos de análisis predictivo | Microsoft Azure"
	description="Paso 4 del tutorial Desarrollo de una solución predictiva: entrenamiento, puntuación y evaluación de múltiples modelos en Estudio de aprendizaje automático de Azure."
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
	ms.author="garye"/>


# Paso 4 del tutorial: Entrenamiento y evaluación de los modelos de análisis predictivo

Este es el cuarto paso del tutorial [Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Creación de un área de trabajo de Aprendizaje automático](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Carga de los datos existentes](machine-learning-walkthrough-2-upload-data.md)
3.	[Crear un experimento nuevo](machine-learning-walkthrough-3-create-new-experiment.md)
4.	**Entrenamiento y evaluación de los modelos**
5.	[Implementación del servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Acceso al servicio web](machine-learning-walkthrough-6-access-web-service.md)

----------

Una de las ventajas del uso de Estudio de aprendizaje automático de Azure para crear modelos de aprendizaje automático es la capacidad para probar más de un tipo de modelo a la vez en un experimento y comparar los resultados. Este tipo de experimentación ayuda a encontrar la mejor solución al problema.

En el experimento que vamos a crear en este tutorial, crearemos dos tipos diferentes de modelos y después compararemos los resultados de su puntuación para decidir cuál de ellos utilizaremos en nuestro experimento final.

Podemos elegir entre diversos modelos. Para ver cuáles están disponibles, expanda el nodo **Aprendizaje automático** de la paleta de módulos y, a continuación, expanda **Inicializar modelo** y el nodo que incluye. Teniendo en cuenta el objetivo de este experimento, seleccionaremos la Máquina de vectores de soporte (SVM) y los módulos Árboles de decisión ampliados de dos clases.

> [AZURE.TIP] Para obtener ayuda para decidir qué algoritmo de Aprendizaje automático se adapta mejor al problema concreto que trata de solucionar, vea [Cómo elegir algoritmos para Aprendizaje automático de Microsoft Azure](machine-learning-algorithm-choice.md).

##Entrenamiento de los modelos
En primer lugar, vamos a configurar el modelo del árbol de decisión ampliado:

1.	Busque el módulo [Árbol de decisión ampliado de dos clases][two-class-boosted-decision-tree] en la paleta de módulos y arrástrelo al lienzo.
2.	Busque el módulo [Entrenar modelo][train-model], arrástrelo al lienzo y conecte la salida del módulo del árbol de decisión ampliado al puerto de entrada izquierdo ("Módulo sin entrenar") del módulo [Entrenar modelo][train-model].
    
    El módulo [Árbol de decisión ampliado de dos clases][two-class-boosted-decision-tree] inicializa el modelo genérico, y [Entrenar modelo][train-model] usa los datos de entrenamiento para entrenar el modelo.
     
3.	Conecte la salida izquierda ("Conjunto de datos de resultados") del módulo [Ejecutar script R][execute-r-script] izquierdo al puerto de entrada derecho ("Conjunto de datos") del módulo [Entrenar modelo][train-model].

	> [AZURE.TIP] No necesitamos dos de las entradas y una de las salidas del módulo [Ejecutar script R][execute-r-script] para este experimento, así que las dejaremos desconectadas. Esto es bastante habitual para algunos módulos.

4.	Seleccione el módulo [Entrenar modelo][train-model]. En el panel **Propiedades**, haga clic en **Iniciar el selector de columnas**, seleccione **Incluir** en la primera lista desplegable, seleccione **nombres de columna** en la segunda lista desplegable y escriba "Riesgo de crédito" en el campo de texto (también puede seleccionar **índices de columna** y escribir "21"). Esto identifica la columna 21, el valor de riesgo de crédito y la columna para el modelo que se va a predecir.


Esta parte del experimento tiene ahora un aspecto similar al siguiente:

![Training a model][1]

A continuación, vamos a configurar el modelo SVM.

En primer lugar, una breve explicación sobre SVM. Los árboles de decisión ampliados funcionan bien con características de todo tipo. Sin embargo, dado que el módulo SVM genera un clasificador lineal, el modelo que genera tiene el mejor error de prueba cuando todas las características numéricas tienen la misma escala. Por tanto, para convertir todas las características numéricas a la misma escala, vamos a usar una transformación "Tanh" (con el módulo [Normalizar datos][normalize-data]) que transforma los números en el intervalo [0,1] (las características de cadena las convierte el módulo SVM a características de categorías y luego a características 0/1 binarias, por lo que no es necesario transformar manualmente las características de cadena). Además, no queremos transformar la columna Riesgo de crédito (columna 21): es numérica, pero es el valor sobre cuya predicción estamos entrenando al modelo; por tanto, es necesario dejarla tal cual.

Para configurar el modelo SVM, realice lo siguiente:

1.	Busque el módulo [Máquina de vectores de soporte de dos clases][two-class-support-vector-machine] en la paleta de módulos y arrástrelo al lienzo.
2.	Haga clic con el botón derecho en el módulo [Entrenar modelo][train-model], seleccione **Copiar**, haga clic con el botón derecho en el lienzo y seleccione **Pegar**. Tenga en cuenta que la copia del módulo [Entrenar modelo][train-model] tiene la misma selección de columnas que el original.
3.	Conecte la salida del módulo SVM al puerto de entrada izquierdo ("Modelo sin entrenar") del módulo [Entrenar modelo][train-model].
4.	Busque el módulo [Normalizar datos][normalize-data] y arrástrelo al lienzo.
5.	Conecte la entrada de este módulo a la salida de la izquierda a la izquierda del módulo [Ejecutar script R][execute-r-script] (tenga en cuenta que el puerto de salida de un módulo puede estar conectado a más de un módulo distinto).
6.	Conecte el puerto de salida izquierdo ("Conjunto de datos transformados") del módulo [Normalizar datos][normalize-data] al puerto de entrada derecho ("Conjunto de datos") del módulo [Entrenar modelo][train-model].
7.	En el panel **Propiedades** del módulo [Normalizar datos][normalize-data], seleccione **Tanh** para el parámetro **Método de transformación**.
8.	Haga clic en **Iniciar el selector de columnas**, seleccione "Ninguna columna" en **Empieza por**, **Incluir** en la primera lista desplegable, **Tipo de columna** en la segunda lista desplegable y **Numérico** en la tercera. Esto especifica que todas las columnas numéricas (y solo numéricas) se transformarán.
9.	Haga clic en el signo más (+) a la derecha de esta fila; de esta forma, se crea una nueva fila de listas desplegables. Seleccione **Excluir** en la primera lista desplegable, seleccione **Nombres de columna** en la segunda lista desplegable y escriba “Riesgo de crédito” en el campo de texto (o seleccione **Índices de columna** y escriba "21"). Especifica que se debe ignorar la columna Riesgo de crédito (debemos hacerlo porque se trata de una columna numérica y, de lo contrario, se transformaría).
10.	Haga clic en **Aceptar**.  


El módulo [Normalizar datos][normalize-data] está configurado ahora para realizar una transformación Tanh en todas las columnas numéricas excepto en la columna de riesgo de crédito.

Esta parte de nuestro experimento debería tener ahora un aspecto similar al siguiente:

![Training the second model][2]

##Puntuación y evaluación de modelos
Utilizaremos los datos de prueba que se separaron mediante el módulo [Dividir datos][split] para puntuar nuestros modelos entrenados. A continuación podremos comparar los resultados de los dos modelos para ver cuál de ellos generó mejores resultados.

1.	Busque el módulo [Puntuar modelo][score-model] y arrástrelo al lienzo.
2.	Conecte el puerto de entrada izquierdo de este módulo al modelo del árbol de decisión ampliado (esto es, conéctelo al puerto de salida del módulo [Entrenar modelo][train-model] que está conectado al módulo [Árbol de decisión ampliado de dos clases][two-class-boosted-decision-tree]).
3.	Conecte el puerto de entrada derecho del módulo [Puntuar modelo][score-model] a la salida izquierda del módulo [Ejecutar script R][execute-r-script] derecho.

    El módulo [Puntuar modelo][score-model] ahora puede utilizar la información de crédito de los datos de prueba, ejecutarla a través del modelo y comparar las predicciones que el modelo genera con la columna de riesgo de crédito real de los datos de prueba.

4.	Copie y pegue el módulo [Puntuar modelo][score-model] para crear una segunda copia, o arrastre un nuevo módulo al lienzo.
5.	Conecte el puerto de entrada izquierdo de este módulo al modelo SVM (esto es, conéctelo al puerto de salida del módulo [Entrenar modelo][train-model] que está conectado al módulo [Máquina de vectores de soporte de dos clases][two-class-support-vector-machine]).
6.	En cuanto al modelo SVM, tenemos que realizar la misma transformación en los datos de prueba que la que realizamos con los datos de entrenamiento. Por tanto, copie y pegue el módulo [Normalizar datos][normalize-data] para crear una segunda copia y conéctelo a la salida izquierda del módulo [Ejecutar script R][execute-r-script] derecho.
7.	Conecte el puerto de entrada derecho del módulo [Puntuar modelo][score-model] a la salida izquierda del módulo [Normalizar datos][normalize-data].  

Para evaluar los dos resultados de puntuación, usaremos el módulo [Evaluar modelo][evaluate-model].

1.	Busque el módulo [Evaluar modelo][evaluate-model] y arrástrelo al lienzo.
2.	Conecte el puerto de entrada izquierdo al puerto de salida de [Puntuar modelo][score-model] asociado al modelo del árbol de decisión ampliado.
3.	Conecte el puerto de entrada derecho al otro módulo [Puntuar modelo][score-model].  

Haga clic en el botón **EJECUTAR** bajo el lienzo para ejecutar el experimento. Esto puede tardar unos minutos. Verá un indicador giratorio en cada módulo que indica que está en ejecución; cuando haya acabado, aparecerá una marca de verificación de color verde. Cuando todos los módulos tengan una marca de verificación, habrá finalizado la ejecución del experimento.

El experimento debería tener ahora un aspecto similar al siguiente:

![Evaluating both models][3]

Para consultar los resultados, haga clic en el puerto de salida del módulo [Evaluar modelo][evaluate-model] y seleccione **Visualizar**.

El módulo [Evaluar modelo][evaluate-model] produce un par de curvas y métricas que le permiten comparar los resultados de los dos modelos de puntuación. Puede ver los resultados como curvas de características operativas del receptor (ROC), curvas de precisión/exhaustividad o curvas de elevación. También se muestran otros datos como la matriz de confusión y los valores del área bajo la curva (AUC) acumulados, entre otras métricas. También puede cambiar el valor del umbral moviendo el control deslizante a la izquierda o a la derecha, y comprobar cómo afecta esta acción al conjunto de métricas.

A la derecha del gráfico, haga clic en **Conjunto de datos puntuados** o **Conjunto de datos puntuados para comparar** con el fin de resaltar la curva asociada y mostrar debajo las métricas asociadas. En la leyenda de las curvas, "Conjunto de datos puntuados" corresponde al puerto de entrada izquierdo del módulo [Evaluar modelo][evaluate-model] (en nuestro caso, se trata del modelo del árbol de decisión ampliado). "Conjunto de datos puntuados para comparar" corresponde al puerto de entrada derecho (el modelo SVM en nuestro caso). Al hacer clic en una de estas etiquetas, se destaca la curva de ese modelo y aparecen debajo las métricas correspondientes.

![ROC curves for models][4]

Al examinar estos valores, puede decidir qué modelo se acerca más a los resultados que busca. Puede volver y repetir el experimento cambiando valores en los diferentes nodos.

> [AZURE.TIP] Cada vez que ejecute el experimento, se guardará un registro de esa iteración en el Historial de ejecuciones. Puede ver estas iteraciones y volver a cualquiera de ellas haciendo clic en **VER HISTORIAL DE EJECUCIÓN** bajo el lienzo. También puede hacer clic en **Anterior ejecución** en el panel **Propiedades** para volver a la iteración inmediatamente anterior a la que ha abierto. Puede hacer una copia de cualquier iteración de su experimento si hace clic en **GUARDAR COMO** bajo el lienzo. Utilice las propiedades **Resumen** y **Descripción** para mantener un registro de lo que ha intentado en las iteraciones del experimento.

>  Consulte [Administración de iteraciones de experimentos en Estudio de aprendizaje automático de Azure](machine-learning-manage-experiment-iterations.md) para obtener más detalles.


----------

**A continuación: [Implementación del servicio web](machine-learning-walkthrough-5-publish-web-service.md)**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

<!---HONumber=AcomDC_0211_2016-->