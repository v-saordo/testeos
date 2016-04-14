<properties
	pageTitle="Ingeniería de características en el proceso de análisis de Cortana | Microsoft Azure" 
	description="Explica el propósito del diseño de características y ofrece ejemplos de su rol en el proceso de mejora de los datos del aprendizaje automático."
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
	ms.date="02/05/2016"
	ms.author="zhangya;bradsev" />


# Ingeniería de características en el proceso de análisis de Cortana 

La ingeniería de características intenta aumentar la eficacia predictiva de los algoritmos de aprendizaje creando características de los datos sin procesar que facilitan el proceso de aprendizaje.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]
Este **menú** vincula a temas en los que se describe cómo crear características para datos en diversos entornos. Esta tarea es un paso del [proceso de Cortana Analytics (CAP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

##Introducción

En este tema se explica el propósito del diseño de características y ofrece ejemplos de su rol en el proceso de mejora de los datos del aprendizaje automático. Estos ejemplos se extraen de Estudio de aprendizaje automático de Azure.

La ingeniería y la selección de características es una parte del proceso de CAP descrito en [¿Cuál es el proceso de análisis de Cortana?](machine-learning-data-science-the-cortana-analytics-process.md) La selección y la ingeniería de características son partes del paso **Desarrollo de características** del CAP. * **Ingeniería de características**: este proceso intenta crear otras características pertinentes además de las características sin procesar existentes de los datos, y aumentar la eficacia de predicción del algoritmo de aprendizaje. * **Selección de características**: este proceso, selecciona el subconjunto de claves de características de datos originales en un intento de reducir las dimensionalidad del problema de entrenamiento.

Normalmente, la **ingeniería de características** se aplica primero para generar características adicionales y, a continuación, se realiza el paso de **selección de características** para eliminar características irrelevantes, redundantes o altamente correlacionadas.

Los datos de entrenamiento que se usan en el aprendizaje automático a menudo pueden mejorarse gracias a la extracción de características desde los datos sin procesar que se han recopilado. Un ejemplo de una característica diseñada en el contexto de aprender cómo clasificar las imágenes de caracteres manuscritos es la creación de un mapa de densidad de bits construido a partir de los datos de distribución de bits sin procesar. Este mapa puede ayudar a ubicar los bordes de los caracteres de manera más eficiente usando simplemente la distribución sin procesar directamente.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## Creación de características a partir de sus datos: diseño de características

Los datos de entrenamiento constan de una matriz compuesta de ejemplos (registros u observaciones almacenados en filas), cada uno de los cuales cuenta con un conjunto de características (variables o campos almacenados en columnas). Se espera que las características especificadas en el diseño experimental caractericen los patrones de los datos. A pesar de que muchos de los campos de datos sin procesar se pueden incluir directamente en el conjunto de características seleccionado que se usa para entrenar un modelo, a menudo se da el caso de que se requiere construir características adicionales (diseñadas) a partir de las características existentes en los datos sin procesar para generar un conjunto de datos de entrenamiento mejorado.

¿Qué tipo de características se deben crear para mejorar el conjunto de datos cuando se entrena un modelo? Las características diseñadas que mejoran el entrenamiento proporcionan información que ayuda a diferenciar de mejor manera los patrones de los datos. Se espera que las características nuevas proporcionen información adicional que no está claramente capturada o no es fácilmente evidente en el conjunto de características original o existente. Pero este proceso es, en cierto modo, un arte. Las decisiones acertadas y productivas a menudo requieren cierto conocimiento especializado.

Al comenzar con Aprendizaje automático de Azure, es más fácil comprender este proceso de manera concreta si se usan ejemplos proporcionados en Estudio. Aquí se muestran dos ejemplos:

* Un ejemplo de regresión, [Predicción del número de bicicletas alquiladas](../machine-learning-sample-prediction-of-number-of-bike-rentals.md), en un experimento supervisado en el que se conocen los valores de destino
* Un ejemplo de clasificación de minería de texto con [Hash de características](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

### Ejemplo 1: incorporación de características temporales para el modelo de regresión ###

Usemos el experimento "Previsión de demanda de bicicletas" en Estudio de aprendizaje automático de Azure para mostrar cómo diseñar características para una tarea de regresión. El objetivo de este experimento es predecir la demanda de bicicletas, es decir, el número de bicicletas alquiladas dentro de un mes/día/hora específico. El conjunto de datos "Conjunto de datos de UCI de alquiler de bicicletas" se usa como los datos de entrada sin procesar. Este conjunto de datos se basa en datos reales provenientes de la empresa Capital Bikeshare que mantiene una red de alquiler de bicicletas en Washington DC, en Estados Unidos. El conjunto de datos representa el número de bicicletas alquiladas dentro de una hora específica durante un día en el año 2011 y 2012 y contiene 17379 filas y 17 columnas. El conjunto de características sin procesar contiene condiciones climáticas (temperatura/humedad/velocidad del viento) y el tipo de día (festivo/día de semana). El campo para la predicción es "cnt", un contador que representa las bicicletas alquiladas dentro de una hora específica y cuyos intervalos van de 1 a 977.

Con el objetivo de construir características eficaces en los datos de entrenamiento, se crean cuatro modelos de regresión con el mismo algoritmo, pero con cuatro conjuntos de datos de entrenamiento distintos. Los cuatro conjuntos de datos representan los mismos datos de entrada sin procesar, pero con un número creciente del conjunto de características. Estas características se agrupan en cuatro categorías:

1. A = características de clima + festivo + día de semana + fin de semana correspondiente al día de la predicción
2. B = el número de bicicletas alquiladas en cada una de las 12 horas anteriores
3. C = el número de bicicletas alquiladas en cada uno de los 12 días anteriores a la misma hora
4. D = el número de bicicletas arrendadas en cada una de las 12 semanas anteriores a la misma hora y el mismo día

Aparte del conjunto de características A, que ya existe en los datos sin procesar originales, los otros tres conjuntos de características se crean mediante el proceso de diseño de características. El conjunto de características B captura cada demanda de bicicletas reciente. El conjunto de características C captura la demanda de bicicletas en una hora específica. El conjunto de características D captura la demanda de bicicletas en una hora específica y un día de la semana específico. Los conjuntos de datos de entrenamiento incluyen el conjunto de características A, A+B, A+B+C y A+B+C+D, respectivamente.

En el experimento de Aprendizaje automático de Azure, estos cuatro conjuntos de datos de entrenamiento se forman a través de cuatro ramas del conjunto de datos de entrada procesado previamente. Con la excepción de la rama que se encuentra más a la izquierda, cada una de estas ramas contiene un módulo [Ejecutar script de R](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/), en el que un conjunto de características derivadas (conjunto de características B, C y D) se construye y anexa respectivamente al conjunto de datos importado. En la siguiente figura se muestra el script de R que se usa para crear el conjunto de características B en la segunda rama a la izquierda.

![crear funciones](./media/machine-learning-data-science-create-features/addFeature-Rscripts.png)

La comparación de los resultados de rendimiento de los cuatro modelos se resume en la siguiente tabla. Las características A+B+C muestran los mejores resultados. Observe que la tasa de errores disminuye cuando se incluyen conjuntos de datos adicionales en los datos de entrenamiento. Esto comprueba nuestra presunción con respecto a que los conjuntos de características B, C proporcionan información pertinente adicional para la tarea de regresión. Sin embargo, agregar la característica D no parece proporcionar reducción adicional ninguna en lo que respecta a la tasa de errores.

![comparación de resultados](./media/machine-learning-data-science-create-features/result1.png)

### <a name="example2"></a> Ejemplo 2: creación de características en minería de texto  

El diseño de características se aplica ampliamente en las tareas relacionadas con la minería de texto, como la clasificación de documentos y el análisis de opiniones. Por ejemplo, cuando deseamos clasificar documentos en varias categorías, una hipótesis típica es que las palabras/frases incluidas que se encuentran en una categoría de documento tienen menos probabilidades de presentarse en otra categoría de documento. Dicho de otro modo, la frecuencia de la distribución de palabras/frases puede caracterizar distintas categorías de documento. En las aplicaciones de minería de texto, debido a que partes individuales de contenidos de texto normalmente sirven como los datos de entrada, es necesario el proceso de diseño de características para crear las características que implican las frecuencias de palabras/frases.

Para llevar a cabo esta tarea, se aplica una técnica llamada **hash de características** para convertir eficazmente las características arbitrarias de texto en índices. En lugar de asociar cada característica de texto (palabras/frases) a un índice determinado, este método funciona mediante la aplicación de una función de hash a las características y el uso de sus valores de hash como índices directamente.

En Aprendizaje automático de Azure, existe un módulo llamado [Hash de características](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) que crea oportunamente estas características de palabras/frases. La figura siguiente muestra un ejemplo del uso de este módulo. El conjunto de datos de entrada contiene dos columnas: la clasificación de libro, que va de 1 a 5, y el contenido mismo de la reseña. El objetivo de este módulo [Hash de características](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) es recuperar una gran cantidad de características nuevas que muestran la frecuencia de repetición de las palabras/frases correspondientes dentro de la reseña de ese libro en especial. Para usar este módulo, es necesario realizar los siguientes pasos:

* Primero, seleccione la columna que contiene el texto de entrada ("Col2" en este ejemplo).
* Segundo, defina el "Tamaño de bits de hash" en 8,lo que significa que se crearán 2^8=256 características. La palabra/frase en todo el texto tendrá hash en 256 índices. El parámetro "Tamaño de bits de hash" va de 1 a 31. Es menos probable que las palabras/frases tengan hash en el mismo índice si este valor se define para que sea un número mayor.
* Tercero, defina el parámetro "N-gramas" en 2. Con esto se obtiene la frecuencia de repetición de unigramas (una característica para cada palabra única) y bigramas (una características para cada par de palabras adyacentes) a partir del texto de entrada. El parámetro "N-gramas" va de 0 a 10, lo que indica el número máximo de palabras secuenciales que se incluirán en una característica.  

![Módulo "Hash de características"](./media/machine-learning-data-science-create-features/feature-Hashing1.png)

La figura siguiente muestra cómo se ven estas nuevas características.

![Ejemplo de "Hash de características"](./media/machine-learning-data-science-create-features/feature-Hashing2.png)


## Conclusión

Las características diseñadas y seleccionadas aumenta la eficiencia del proceso de entrenamiento, el que intenta extraer la información clave contenida en los datos. También mejoran la eficacia de estos modelos para clasificar los datos de entrada de manera precisa y para predecir resultados de interés de manera más sólida. El diseño y la selección de características también se pueden combinar para que sea posible hacer un mejor seguimiento computacional del aprendizaje. Para ello, mejora y luego reduce el número de características que se necesitan para calibrar o entrenar un modelo. Matemáticamente hablando, las características seleccionadas para entrenar el modelo son un conjunto mínimo de variables independientes que explican los patrones existentes en los datos y, a continuación, predicen correctamente los resultados.

Observe que no siempre es necesario realizar el diseño o la selección de características. Si es necesario o no depende de los datos que se tengan o que se hayan recopilado, del algoritmo que se elija y del objetivo del experimento.
 

<!----HONumber=AcomDC_0211_2016-->