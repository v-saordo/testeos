<properties 
	pageTitle="Aplicación de aprendizaje automático: servicio de detección de anomalías | Microsoft Azure" 
	description="La API de detección de anomalías es un ejemplo integrado en Aprendizaje automático de Microsoft Azure que detecta anomalías en los datos de serie temporales con valores numéricos espaciados de manera uniforme en el tiempo." 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm"
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="reference" 
	ms.tgt_pltfrm="na" 
	ms.workload="multiple" 
	ms.date="12/08/2015" 
	ms.author="pingf"/>


# Servicio de detección de anomalías de aprendizaje automático#

##Información general

La API de detección de anomalías es un ejemplo integrado en Aprendizaje automático de Azure que detecta anomalías en los datos de serie temporales con valores numéricos espaciados de manera uniforme en el tiempo.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Este servicio de detección de anomalías puede detectar los siguientes tipos diferentes de anomalías en los datos de series temporales:

1. Tendencias positivas y negativas: al supervisar el uso de la memoria en informática, por ejemplo, una tendencia al alza es indicativa de una pérdida de memoria.

2. Aumento en el intervalo de valores dinámico: por ejemplo, al supervisar las excepciones producidas por un servicio, cualquier aumento en el intervalo de valores dinámico podría indicar la inestabilidad en el estado del servicio.

3. Por ejemplo, al supervisar el número de errores de inicio de sesión en un servicio o el número de finalizaciones de compras en un sitio de comercio electrónico, cambios abruptos e interrupciones, podrían indicar un comportamiento anormal.


Estos detectores realizan un seguimiento de los cambios en los valores con el tiempo e informan sobre cambios continuos en sus valores. No requieren el ajuste del umbral ad hoc y sus puntuaciones pueden utilizarse para controlar el índice de falsos positivos. La API de detección de anomalías es útil en escenarios como la supervisión del servicio mediante el seguimiento de KPI en el tiempo, las métricas de uso como el número de búsquedas, el número de clics, los contadores de rendimiento como la memoria, la cpu, las lecturas de archivos, etc., en el tiempo.

##Definición de la API

El servicio proporciona API basadas en REST a través de HTTPS que se pueden consumir de varias formas, como una aplicación web o móvil, R, Python, Excel, etc. Tenemos un [aplicación web de Azure](http://anomalydetection-aml.azurewebsites.net/) que le ayuda a ejecutar el servicio web de detección de anomalías en los datos y a mostrar los resultados.

También puede enviar los datos de la serie de tiempo a este servicio a través de una llamada a la API de REST, y ejecuta una combinación de los tres tipos de anomalías descritos anteriormente. El servicio se ejecuta en la plataforma de Aprendizaje automático de AzureML, que se adapta a las necesidades de su empresa sin problemas y proporciona SLA del 99,95%.

La siguiente ilustración muestra un ejemplo de anomalías detectadas una serie de tiempos mediante el marco anterior. La serie temporal tiene 2 cambios de nivel distintos y 3 picos. Los puntos rojos muestran la hora en que se detecta el cambio del nivel, mientras que las flechas hacia arriba rojas muestran los picos detectados.


![][1]

##Entrada

La API toma dos parámetros de entrada

1. "datos" representa la serie de hora de entrada en el formato: t1=v1;t2=v2;... 

 
	Datos de ejemplo:
		
		"9/21/2014 11:05:00 AM=3;9/21/2014 11:10:00 AM=9.09;9/21/2014 11:15:00 AM=0;"

2. "params" establecido en "SpikeDetector.TukeyThresh=3; SpikeDetector.ZscoreThresh=3 "que controla la sensibilidad de los detectores de pico. Los valores más altos capturarán picos superiores y viceversa.

	URL de muestra con parámetros de entrada mencionados arriba:

		https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v1/Score?data=%279%2F21%2F2014%2011%3A05%3A00%20AM%3D3%3B9%2F21%2F2014%2011%3A10%3A00%20AM%3D9.09%3B9%2F21%2F2014%2011%3A15%3A00%20AM%3D0%3B%27&params=%27SpikeDetector.TukeyThresh%3D3%3B%20SpikeDetector.ZscoreThresh%3D3%27



###Salida###

La API ejecuta estos detectores en los datos de serie temporal y devuelve las puntuaciones de anomalías en cada punto en el tiempo. Esta salida se puede analizar mediante un analizador sencillo, como se muestra en [https://adresultparser.codeplex.com/](https://adresultparser.codeplex.com/). Esto proporciona un código de ejemplo que muestra cómo conectarse a la API y analizar la salida. Las alertas generadas se pueden visualizar en un panel y/o pasan a usuarios expertos que pueden tomar medidas correctivas.

Ejemplo de salida para la entrada de ejemplo anterior:

	"Time,Data,TSpike,ZSpike,Martingale values,Alert indicator,Martingale values(2),Alert indicator(2),;9/21/2014 11:05:00 AM,3,0,0,-0.687952590518378,0,-0.687952590518378,0,;9/21/2014 11:10:00 AM,9.09,0,0,-1.07030497733224,0,-0.884548154298423,0,;9/21/2014 11:15:00 AM,0,0,0,-1.05186237440962,0,-1.173800281031,0,;"

que es una representación de la tabla siguiente:

Hora|Datos|Tspike|Zspike|Valores de martingala|Indicador de alerta|Valores de martingala (2)|Indicador de alerta (2)
---|---|---|---|---|---|---|---
21/9/2014 11:05|3|0|0|-0.687952591|0|-0.687952591|0|   
9/21/2014 11:10|9\.09|0|0|-1.070304977|0|-0.884548154|0|    
9/21/2014 11:15|0|0|0|-1.051862374|0|-1.1738002814|0|   
   

[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection.jpg

 

 

<!---HONumber=AcomDC_1210_2015-->