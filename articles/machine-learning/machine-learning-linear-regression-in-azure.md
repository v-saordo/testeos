<properties 
	pageTitle="Uso de la regresión lineal en el aprendizaje automático | Microsoft Azure" 
	description="Una comparación de los modelos de regresión lineal en Excel y en Estudio de aprendizaje automático de Azure" 
	metaKeywords="" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/11/2015" 
	ms.author="kbaroni;garye" />

# Uso de regresión lineal en Aprendizaje automático de Azure

> *Kate Baroni* y *Ben Boatman* son arquitectos de soluciones para empresas del Centro de excelencia de Data Insights de Microsoft. En este artículo, se describe su experiencia al migrar un conjunto existente de análisis de regresión a una solución basada en la nube mediante Aprendizaje automático (ML) de Azure.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Objetivo

Nuestro proyecto se inició con dos objetivos en mente:

1. Usar el análisis predictivo para mejorar la exactitud de las proyecciones de ingresos mensuales de la organización.  
2. Usar Aprendizaje automático de Azure para confirmar, optimizar, aumentar la velocidad y escalar los resultados.  

Como muchas empresas, nuestra organización pasa por un proceso de previsión de ingresos mensuales. Nuestro pequeño equipo de analistas de negocios se encarga de usar Aprendizaje automático para respaldar el proceso y mejorar la precisión de la previsión. El equipo dedicó varios meses a recopilar los datos de varios orígenes y ejecutar los atributos de datos a través de análisis estadísticos a fin de identificar los atributos claves relevantes para la previsión de ventas de servicios. Los pasos siguientes consistían en comenzar el prototipo de los modelos de regresión estadística de los datos en Excel. En unas semanas, tuvimos un modelo de regresión de Excel que superaba los procesos actuales de previsión de finanzas y de campo. Esto se convirtió en el resultado de la predicción de la línea de base.


A continuación, el siguiente paso consistió en pasar nuestro análisis predictivo a Aprendizaje automático de Azure para averiguar cómo se podría mejorar el rendimiento de las predicciones con Aprendizaje automático de Azure.


## Consecución de la paridad en el rendimiento predictivo

Nuestra prioridad era lograr la paridad entre los modelos de regresión de Excel y de Aprendizaje automático de Azure. Teniendo en cuenta los mismos datos y la misma división de datos de prueba (test) y entrenamiento (train), queríamos conseguir la paridad de rendimiento predictivo entre Excel y Aprendizaje automático de Azure. Al principio no lo conseguimos. El modelo de Excel superaba al de Aprendizaje automático de Azure. El error se debía a una falta de conocimiento de la configuración de la herramienta base en Aprendizaje automático de Azure. Después de una sincronización con el equipo de producto de Aprendizaje automático de Azure, se consiguió una mejor comprensión de la configuración base necesaria para nuestros conjuntos de datos, y se logró la paridad entre los dos modelos.

### Creación de un modelo de regresión en Excel
La regresión de Excel utilizaba el modelo de regresión lineal estándar de Excel Analysis ToolPak.

Calculamos el *porcentaje de error medio absoluto* y se utilizó como medida de rendimiento para el modelo. Tardamos 3 meses en conseguir un modelo operativo con Excel. Aplicamos gran parte de lo aprendido al experimento con Aprendizaje automático de Azure, lo que, en última instancia, era una ventaja a la hora de conocer los requisitos.

### Creación de un experimento comparable en Aprendizaje automático de Azure  
Seguimos estos pasos para crear nuestro experimento en Aprendizaje automático de Azure:

1.	Carga del conjunto de datos como un archivo csv en Aprendizaje automático de Azure (archivo muy pequeño).
2.	Creación de un nuevo experimento y uso del módulo [Columnas del proyecto][project-columns] para seleccionar las mismas características de datos que se usaron en Excel.   
3.	Uso del módulo [División][split] (con el modo *Expresión relativa*) para dividir los datos en conjuntos de aprendizaje exactamente iguales, tal y como se ha realizado en Excel.  
4.	Realizamos experimentos con el módulo [Regresión lineal][linear-regression] (solo opciones predeterminadas), documentamos el proceso y comparamos los resultados con el modelo de regresión de Excel.

### Revisión de los resultados iniciales
Al principio, el modelo de Excel superaba claramente al de Aprendizaje automático de Azure:

| |Excel|Aprendizaje automático de Azure|
|---|:---:|:---:|
|Rendimiento| | |
|<ul style="list-style-type: none;"><li>R cuadrado ajustado</li></ul>| 0,96 |N/D|
|<ul style="list-style-type: none;"><li>Coeficiente de <br />determinación</li></ul>|N/D|	0,78<br />(baja precisión)|
|Error medio absoluto |	9,5 millones de $|	19,4 millones de $|
|Error medio absoluto (%)|	6,03 %|	12,2 %

Cuando ejecutamos el proceso y los resultados para los desarrolladores y científicos de datos del equipo de Aprendizaje automático de Azure, rápidamente nos proporcionaron algunas sugerencias útiles.

* Cuando se usa el módulo [Regresión lineal][linear-regression] en Aprendizaje automático de Azure, se proporcionan dos métodos:
	*  Descenso de gradiente en línea: pueden resultar más adecuado para los problemas a mayor escala.
	*  Ordinaria de mínimos cuadrados: este es el método en el que se suele pensar cuando se habla de la regresión lineal. Para los conjuntos de datos más pequeños, la regresión ordinaria de mínimos cuadrados puede ser una opción más adecuada.
*  Considere la posibilidad de ajustar el parámetro Peso de regularización L2 para mejorar el rendimiento. Está establecido en 0,001 de forma predeterminada y, para nuestro pequeño conjunto de datos, lo establecimos en 0,005 para mejorar el rendimiento.    

### ¡Misterio resuelto!
Al aplicar las recomendaciones, logramos el mismo rendimiento de línea de base en Aprendizaje automático de Azure que con Excel:   

|| Excel|ML de Azure (Inicial)|ML de Azure con mínimos cuadrados|
|---|:---:|:---:|:---:|
|Valor etiquetado |Datos reales (numéricos)|igual|igual|
|Aprendiz |Excel -> Análisis de datos -> Regresión|Regresión lineal|Regresión lineal|
|Opciones del aprendiz|N/A|Predeterminado|Ordinaria de mínimos cuadrados<br />L2 = 0,005|
|Conjunto de datos|26 filas, 3 características, 1 etiqueta Todo numérico|igual|igual|
|División: entrenamiento|Entrenamiento de Excel en las primeras 18 filas, probado en las últimas 8 filas|igual|igual|
|División: prueba|Fórmula de regresión de Excel aplicada a las últimas 8 filas|igual|igual|
|**Rendimiento**||||
|R cuadrado ajustado|0,96|N/A||
|Coeficiente de determinación|N/A|0,78|0,952049|
|Error medio absoluto|9,5 millones de $|19,4 millones de $|9,5 millones de $|
|Error medio absoluto (%)|<span style="background-color: 00FF00;"> 6,03 %</span>|12,2 %|<span style="background-color: 00FF00;"> 6,03 %</span>|

Además, los coeficientes de Excel son muy similares a los pesos de la característica en el modelo de entrenamiento de Azure:

||Coeficientes de Excel |Pesos de las características de Azure |
|---|:---:|:---:|
|Intercepción/desviación|19470209,88|19328500|
|Característica A|0,832653063|0,834156|
|Característica B|11071967,08|11007300|
|Característica C|25383318,09|25140800|

## Pasos siguientes

Queríamos consumir el servicio web de Aprendizaje automático de Azure en Excel. Nuestros analistas de negocios se basan en Excel y necesitábamos una manera de llamar al servicio web de Aprendizaje automático de Azure con una fila de datos de Excel y obtener el valor esperado para Excel.

También queríamos optimizar nuestro modelo utilizando las opciones y los algoritmos disponibles en ML de Azure.

### Integración con Excel
Nuestra solución fue instrumentar nuestro modelo de regresión de Aprendizaje automático de Azure mediante la creación de un servicio web desde el modelo entrenado. En unos minutos, se creó el servicio web y pudimos llamarlo directamente desde Excel para obtener el valor de ingresos previstos.

La sección *Panel de servicios web* incluye un libro de Excel descargable. El libro contiene información predefinida sobre el esquema y la API de servicio web incrustada. Al hacer clic en *Descargar el libro de Excel*, se abre y puede guardarlo en el equipo local.

![][1]
 
Con el libro abierto, copie los parámetros predefinidos en la sección de parámetros de color azul, como se muestra a continuación. Una vez que se especifican los parámetros, Excel llama al servicio web AzureML y las etiquetas puntuadas previstas se mostrarán en la sección de valores de predicción de color verde. El libro continuará creando predicciones para los parámetros basándose en el modelo entrenado para todos los elementos de fila especificados en los parámetros. Para obtener más información sobre cómo usar esta característica, consulte [Consumo de un servicio web de Aprendizaje automático de Azure de Excel](machine-learning-consuming-from-excel.md).

![][2]
 
### Optimización y otros experimentos
Ahora que teníamos una línea de base con nuestro modelo de Excel, dimos un paso más para optimizar nuestro modelo de regresión lineal de Aprendizaje automático de Azure. Usamos el módulo [Selección de características basada en filtros][filter-based-feature-selection] para mejorar nuestra selección de datos iniciales de elementos. Ello nos ayudó a lograr una mejora del rendimiento del 4,6 % en el error medio absoluto. Para proyectos futuros, utilizaremos esta característica que nos permitirá ahorrar semanas de iteración en los atributos de los datos para buscar el conjunto correcto de características que se utilizará para el modelado.

A continuación, tenemos previsto incluir algoritmos adicionales como [bayesianos][bayesian-linear-regression] o [árboles de decisiones incrementados][boosted-decision-tree-regression] en nuestro experimento para comparar el rendimiento.

Si desea experimentar con regresión, un buen conjunto de datos para probar es el conjunto de datos de ejemplo de Energy Efficiency Regression, que tiene muchos atributos numéricos. El conjunto de datos se proporciona como parte de los conjuntos de datos de muestra en Estudio de Aprendizaje automático. Puede usar diversos módulos de entrenamiento para predecir la carga de calefacción o refrigeración. En el gráfico siguiente, se muestra una comparación de distintos entrenamientos de regresión efectuados con el conjunto de datos Energy Efficiency para predecir una variable de destino sobre la carga de refrigeración:

|Modelo|Error medio absoluto|Error cuadrático medio|Error absoluto relativo|Error cuadrático relativo|Coeficiente de determinación
|---|---|---|---|---|---
|Árbol de decisiones incrementados|0,930113|1,4239|0,106647|0,021662|0,978338
|Regresión lineal (descenso de gradiente)|2,035693|2,98006|0,233414|0,094881|0,905119
|Regresión de red neuronal|1,548195|2,114617|0,177517|0,047774|0,952226
|Regresión lineal (ordinaria de mínimos cuadrados)|1,428273|1,984461|0,163767|0,042074|0,957926  

## Puntos clave 

Hemos aprendido mucho al ejecutar experimentos de regresión en Excel y en Aprendizaje automático de Azure de forma paralela. El hecho de crear un modelo de línea de base en Excel y compararlo con modelos usando la [regresión lineal][linear-regression] de Aprendizaje automático de Azure nos permitió aprender sobre Aprendizaje automático de Azure. Además, descubrimos oportunidades para mejorar la selección de datos y el modelo de rendimiento.

También descubrimos que es aconsejable utilizar la [Selección de características basada en filtros][filter-based-feature-selection] para acelerar los proyectos futuros de predicción. Al aplicar la selección de características a los datos, se puede crear un modelo mejorado en Aprendizaje automático de Azure con un mejor rendimiento general.

La capacidad de transferir sistemáticamente la predicción analítica previsión desde Aprendizaje automático de Azure hasta Excel permite aumentar significativamente la capacidad de proporcionar resultados correctos a una extensa audiencia de usuarios empresariales.


## Recursos
A continuación, encontrará algunos recursos que le ayudarán a trabajar con la regresión:

* Regresión en Excel. Si nunca ha intentado realizar la regresión en Excel, este tutorial le facilitará el trabajo: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Regresión frente a previsión. Tyler Chessman escribió un artículo de blog que explica cómo realizar una serie de previsiones de tiempo en Excel. Incluye una buena descripción para aquellos que se inician en la regresión lineal: [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts)  
* 	Regresión lineal ordinaria con mínimos cuadrados: errores, problemas y riesgos. Introducción y análisis sobre la regresión: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

[1]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 

<!-----HONumber=AcomDC_1210_2015-->
