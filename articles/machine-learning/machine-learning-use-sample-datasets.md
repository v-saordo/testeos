<properties
	pageTitle="Uso de los conjuntos de datos de muestra en Estudio de aprendizaje automático | Microsoft Azure"
	description="Descripciones de los conjuntos de datos que se usan en los modelos de muestras incluidos en Estudio de aprendizaje automático. Puede usar estos conjuntos de datos de ejemplo para los experimentos."
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


# Uso de los conjuntos de datos de muestra en Estudio de aprendizaje automático de Azure

[top]: #machine-learning-sample-datasets

Cuando se crea un área de trabajo nueva en Aprendizaje automático de Azure, de manera predeterminada se incluyen diversos conjuntos de datos y experimentos de muestra. Muchos de estos conjuntos de datos de ejemplo se usan en los modelos de ejemplo de la [Galería de análisis de Cortana de Azure](http://gallery.azureml.net/), y otros se incluyen como ejemplos de diversos tipos de datos que se usan normalmente en el aprendizaje automático.

Algunos de estos conjuntos de datos están disponibles en el almacenamiento de blobs de Azure. Para dichos conjuntos de datos, la tabla siguiente proporciona un vínculo directo. Puede usar estos conjuntos de datos en sus experimentos mediante el módulo [Lector][reader].

El resto de estos conjuntos de datos de muestra aparece en **Conjuntos de datos guardados** en la paleta de módulos a la izquierda del lienzo de experimentos al abrir o crear un experimento nuevo en Estudio de aprendizaje automático. Para usar cualquiera de estos conjuntos de datos en su propio experimento, arrástrelo a su lienzo de experimentos.


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<table>

<tr>
  <th align=left>Nombre del conjunto de datos</th>
  <th align=left>Descripción del conjunto de datos</th>
</tr>

<tr>
  <td valign=top>Conjunto de datos de clasificación binaria de ingresos en el censo de adultos</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-adult-census-income](../../includes/machine-learning-sample-dataset-adult-census-income.md)]
  </td>
</tr>

<tr ID=airport-codes-dataset>
  <td valign=top>Conjunto de datos de códigos de aeropuerto</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-airport-codes](../../includes/machine-learning-sample-dataset-airport-codes.md)]
  </td>
</tr>

<tr>
  <td valign=top>Información sobre los precios de los automóviles (datos sin procesar)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-price](../../includes/machine-learning-sample-dataset-automobile-price.md)]
  </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top>Conjunto de datos UCI de alquiler de bicicletas</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]
  </td>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top>Imagen RGB de Bill Gates</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos sobre donación de sangre</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-blood-donation](../../includes/machine-learning-sample-dataset-blood-donation.md)]
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top>Reseñas de libros de Amazon</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos sobre cáncer de mama</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-data](../../includes/machine-learning-sample-dataset-breast-cancer-data.md)]
  </td>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Características de cáncer de mama
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-features](../../includes/machine-learning-sample-dataset-breast-cancer-features.md)]
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Información sobre cáncer de mama</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-info](../../includes/machine-learning-sample-dataset-breast-cancer-info.md)]
  </td>
</tr>

<tr ID=crm-appetency-labels-shared>
  <td valign=top>Etiquetas de apetencia CRM compartidas</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]
  </td>
</tr>

<tr ID=crm-churn-labels-shared>
  <td valign=top>Etiquetas de rotación de clientes de CRM compartidas</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td valign=top>Conjunto de datos CRM compartido</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]
  </td>
</tr>

<tr ID=crm-upselling-labels-shared>
  <td valign=top>Etiquetas de mejora de ventas de CRM compartidas</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos de regresión de eficiencia energética</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-energy-efficiency-regression](../../includes/machine-learning-sample-dataset-energy-efficiency-regression.md)]
  </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Datos de retrasos de vuelos</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-delays-data](../../includes/machine-learning-sample-dataset-flight-delays-data.md)]
</td>
</tr>

<tr>
  <td valign=top>Información sobre puntualidad de vuelos (datos sin procesar)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-on-time-performance](../../includes/machine-learning-sample-dataset-flight-on-time-performance.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos de incendios forestales</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-forest-fires](../../includes/machine-learning-sample-dataset-forest-fires.md)]
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top>Conjunto de datos UCI de tarjeta de crédito alemana</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-german-credit-card-uci-dataset](../../includes/machine-learning-sample-dataset-german-credit-card-uci-dataset.md)]
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>Títulos de películas en IMDB</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-imdb-movie-titles](../../includes/machine-learning-sample-dataset-imdb-movie-titles.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos sobre iris de dos clases</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-iris-two-class](../../includes/machine-learning-sample-dataset-iris-two-class.md)]
  </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Tweets sobre películas</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-movie-tweets](../../includes/machine-learning-sample-dataset-movie-tweets.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos sobre consumo de combustible por distancia recorrida para varios automóviles</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-mpg](../../includes/machine-learning-sample-dataset-automobile-mpg.md)]
  </td>
</tr>

<tr>
  <td valign=top>Conjunto de datos de clasificación binaria sobre diabetes en indios pima</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-pima-indians-diabetes](../../includes/machine-learning-sample-dataset-pima-indians-diabetes.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos de clientes de restaurantes</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-customer-data](../../includes/machine-learning-sample-dataset-restaurant-customer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos de características de restaurantes</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-feature-data](../../includes/machine-learning-sample-dataset-restaurant-feature-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Valoraciones de restaurantes</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-ratings](../../includes/machine-learning-sample-dataset-restaurant-ratings.md)]
  </td>
</tr>

<tr>
  <td valign=top>Conjunto de datos de varias clases de recocido de acero</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-steel-annealing](../../includes/machine-learning-sample-dataset-steel-annealing.md)]
  </td>
</tr>

<tr>
  <td valign=top>Datos de telescopio</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-telescope-data](../../includes/machine-learning-sample-dataset-telescope-data.md)]
  </td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Conjunto de datos del tiempo</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-weather-dataset](../../includes/machine-learning-sample-dataset-weather-dataset.md)]
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top>Conjunto de datos de SP 500 de Wikipedia</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-wikipedia-sp-500](../../includes/machine-learning-sample-dataset-wikipedia-sp-500.md)]
  </td>
</tr>





<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-direct-marketing](../../includes/machine-learning-sample-dataset-direct-marketing.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-test](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-test.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-train](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-train.md)]
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../../includes/machine-learning-sample-dataset-network-intrusion.md)]
  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1 v2.topics.qrels.csv</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-rcv1-v2-topics](../../includes/machine-learning-sample-dataset-rcv1-v2-topics.md)]
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-student-performance](../../includes/machine-learning-sample-dataset-student-performance.md)]
  </td>
</tr>




</table>


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!----HONumber=AcomDC_0204_2016-->