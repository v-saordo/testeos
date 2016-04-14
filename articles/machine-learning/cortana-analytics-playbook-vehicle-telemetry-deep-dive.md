<properties 
	pageTitle="Cuaderno de estrategias de soluciones de análisis de telemetría de vehículo: profundización en la solución | Microsoft Azure" 
	description="Use las funcionalidades de Cortana Analytics para obtener información predictiva y en tiempo real del estado del vehículo y los hábitos de conducción." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="bradsev" />


# Cuaderno de estrategias de soluciones de análisis de telemetría de vehículo: profundización en la solución

Este **menú** vincula a las secciones de este cuaderno de estrategias.

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Esta sección profundiza en los detalles de cada una de las fases que se describen en la sección de arquitectura de la solución, con instrucciones e indicaciones para la personalización.

## Orígenes de datos

Esta solución usa dos orígenes de datos diferentes:

- **conjunto de datos de señales de vehículo y diagnósticos simulados** y 
- **catálogo de vehículo**

Como parte de esta solución se incluye un simulador telemático de vehículo. Este simulador emite información de diagnóstico y señales correspondientes al estado del vehículo y al patrón de conducción en un momento dado en el tiempo. Haga clic en el [simulador telemático de vehículo](http://go.microsoft.com/fwlink/?LinkId=717075) para descargar la **solución de simulador telemático de vehículo de Visual Studio** y personalizarla de acuerdo a sus necesidades. El catálogo de vehículo contiene un conjunto de datos de referencia con una asignación de número de identificación VIN a modelo.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig2-vehicle-telematics-simulator.png)

*Ilustración 2: Simulador telemático de vehículo*

Es un conjunto de datos de formato JSON y contiene el siguiente esquema.

Columna | Descripción | Valores   
 ------- | ----------- | ---------  
VIN | Número de identificación del vehículo generado de forma aleatoria | Se obtiene a partir de una lista maestra de 10.000 números de identificación de vehículo generados de forma aleatoria
Outside temperature | La temperatura exterior del entorno en el que se está conduciendo el vehículo | Número generado al azar de 0 a 100
Engine temperature | La temperatura del motor del vehículo | Número generado al azar de 0 a 500
Velocidad | La velocidad del motor del vehículo en conducción | Número generado al azar de 0 a 100
Fuel | El nivel de combustible del vehículo | Número generado al azar de 0 a 100 (indica el porcentaje del nivel de combustible)
EngineOil | El nivel de aceite del motor del vehículo | Número generado al azar de 0 a 100 (indica el porcentaje del nivel de aceite del motor)
Tirepressure | La presión de los neumáticos del vehículo | Número generado al azar de 0 a 50 (indica el porcentaje del nivel de presión de los neumáticos)
Odometer | La lectura del cuentakilómetros del vehículo | Número generado al azar de 0 a 200000
Accelerator\_pedal\_position | La posición del pedal del acelerador del vehículo | Número generado al azar de 0 a 100 (indica el porcentaje del nivel del acelerador)
Parking\_brake\_status | Indica si el vehículo está aparcado o no | Verdadero o falso
Headlamp\_status | Indica si los faros están encendidos o no | Verdadero o falso
Brake\_pedal\_status | Indica si el pedal de freno está pisado o no | Verdadero o falso
Transmission\_gear\_position | La posición del cambio de marcha del vehículo | Estados: primera, segunda, tercera y cuarta, quinta, sexta, séptima, octava
Ignition\_status | Indica si el vehículo está en marcha o detenido | Verdadero o falso
Windshield\_wiper\_status | Indica si el limpiaparabrisas está activado o no | Verdadero o falso
ABS | Indica si el ABS está activado o no | Verdadero o falso
Timestamp | La marca de tiempo del momento en el que se crea el punto de datos | Date
City | La ubicación del vehículo | En esta solución tiene la opción de 4 ciudades: Bellevue, Redmond, Sammamish, Seattle


El conjunto de datos de referencia de modelo del vehículo contiene una asignación de VIN al modelo.

VIN | Modelo |
--------------|------------------
FHL3O1SA4IEHB4WU1 | Sedán |
8J0U8XCPRGW4Z3NQE | Híbrido |
WORG68Z2PLTNZDBI7 | Berlina familiar |
JTHMYHQTEPP4WBMRN | Sedán |
W9FTHG27LZN1YWO0Y | Híbrido |
MHTP9N792PHK08WJM | Berlina familiar |
EI4QXI2AXVQQING4I | Sedán |
5KKR2VB4WHQH97PF8 | Híbrido |
W9NSZ423XZHAONYXB | Berlina familiar |
26WJSGHX4MA5ROHNL | Descapotable |
GHLUB6ONKMOSI7E77 | Familiar |
9C2RHVRVLMEJDBXLP | Compacto |
BRNHVMZOUJ6EOCP32 | SUV pequeño |
VCYVW0WUZNBTM594J | Deportivo |
HNVCE6YFZSA5M82NY | SUV mediano |
4R30FOR7NUOBL05GJ | Familiar |
WYNIIY42VKV6OQS1J | SUV grande |
8Y5QKG27QET1RBK7I | SUV grande |
DF6OX2WSRA6511BVG | Cupé |
Z2EOZWZBXAEW3E60T | Sedán |
M4TV6IEALD5QDS3IR | Híbrido |
VHRA1Y2TGTA84F00H | Berlina familiar |
R0JAUHT1L1R3BIKI0 | Sedán |
9230C202Z60XX84AU | Híbrido |
T8DNDN5UDCWL7M72H | Berlina familiar |
4WPYRUZII5YV7YA42 | Sedán |
D1ZVY26UV2BFGHZNO | Híbrido |
XUF99EW9OIQOMV7Q7 | Berlina familiar
8OMCL3LGI7XNCC21U | Descapotable |
……. | |


### Para generar datos simulados
1.	Haga clic en la flecha situada en la esquina superior derecha del nodo del simulador telemático de vehículo para descargar el paquete de datos del simulador. Guarde y extraiga los archivos localmente en su equipo. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig3-vehicle-telemetry-blueprint.png) *Ilustración 3: Plano técnico de la solución de análisis de telemetría de vehículo*

2.	En el equipo local, vaya a la carpeta en la que extrajo el paquete del simulador telemático de vehículo. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-simulator-folder.png) *Ilustración 4: Carpeta del simulador telemático de vehículo*

3.	Ejecute la aplicación **CarEventGenerator.exe**.

### Referencias

[Solución de simulador telemático de vehículo de Visual Studio](http://go.microsoft.com/fwlink/?LinkId=717075)

[Centro de eventos de Azure](https://azure.microsoft.com/services/event-hubs/)

[Factoría de datos de Azure](https://azure.microsoft.com/documentation/learning-paths/data-factory/)


## Ingesta de datos
Se aprovecha una combinación de los Centros de eventos, Análisis de transmisiones y Factoría de datos de Azure para introducir las señales de vehículo y los eventos de diagnóstico así como los análisis en tiempo real y de lotes. Todos estos componentes se crean y se configuran como parte de la implementación de la solución.

### Análisis en tiempo real
Los eventos generados por el simulador telemático de vehículo se publican en el Centro de eventos mediante el SDK del Centro de eventos. El trabajo de Análisis de transmisiones recopila estos eventos desde el Centro de eventos y procesa los datos en tiempo real para analizar el estado del vehículo.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-event-hub-dashboard.png)

*Ilustración 5: Panel del Centro de eventos*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-processing-data.png)

*Ilustración 6: Procesamiento de datos del trabajo de Análisis de transmisiones*

El trabajo de Análisis de transmisiones recopila datos a partir del Centro de eventos, realiza una combinación con los datos de referencia para asignar el VIN del vehículo al modelo correspondiente, y también los conserva en el Almacenamiento de blobs de Azure para el análisis exhaustivo por lotes. La consulta de Análisis de transmisiones a continuación se usa para conservar los datos en el Almacenamiento de blobs de Azure.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png)

*Ilustración 7: Consulta de trabajo de Análisis de transmisiones para la ingesta de datos*

### Análisis por lotes
También se genera un volumen adicional de las señales y el conjunto de datos de diagnóstico del vehículo simulado para un análisis por lotes más completo. Esto es necesario para garantizar un volumen de datos representativo para el procesamiento por lotes. Con este propósito, usamos una canalización llamada "PrepareSampleDataPipeline" en el flujo de trabajo de Factoría de datos, para generar un conjunto de datos de diagnóstico y de señales de vehículo simulado para un año completo. Haga clic en la [actividad personalizada de la Factoría de datos](http://go.microsoft.com/fwlink/?LinkId=717077) para descargar la solución de Visual Studio de actividad DotNet personalizada de Factoría de datos para realizar personalizaciones que se ajusten a sus necesidades.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-for-batch-processing.png)

*Ilustración 8: Preparar los datos de muestra para el flujo de trabajo de procesamiento por lotes*

La canalización consta de una actividad .net personalizada ADF .Net que se muestra a continuación:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline.png)

*Ilustración 9: PrepareSampleDataPipeline*

Una vez que la canalización se ejecuta correctamente y se marca el conjunto de datos "RawCarEventsTable" como "Ready" se creará un año entero de datos de señales y diagnóstico de vehículo simulado. Verá la siguiente carpeta y archivo creado en la cuenta de almacenamiento en el contenedor "connectedcar"

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-prepare-sample-data-pipeline-output.png)

*Ilustración 10: Resultado de PrepareSampleDataPipeline*

### Referencias

[SDK del Centro de eventos de Azure para ingesta de transmisiones](event-hubs-csharp-ephcs-getstarted.md)

[Funcionalidades de movimiento de datos de Factoría de datos de Azure](data-factory-data-movement-activities.md) [Actividad DotNet de Factoría de datos de Azure](data-factory-use-custom-activities.md)

[Solución de Visual Studio para la actividad DotNet de Factoría de datos de Azure para preparar datos de ejemplo](http://go.microsoft.com/fwlink/?LinkId=717077)


## Preparación
>[AZURE.ALERT] Este paso de la solución solo es aplicable al procesamiento por lotes.

El conjunto de datos semiestructurado sin procesar de señales y diagnóstico de vehículo se particiona en el paso de preparación de datos en un formato de año y mes para una consulta eficiente y un almacenamiento escalable a largo plazo (es decir, permite una conmutación por error de una cuenta de blob a la siguiente a medida que se llena la primera). Los datos de salida (etiquetados *PartitionedCarEventsTable*) se guardarán durante un largo período como formulario de datos fundacionales (sin procesamiento alguno) en el "Data Lake" del cliente. Normalmente se podrían descartar los datos de entrada para esta canalización, ya que los datos de salida mantienen la máxima fidelidad a la entrada, simplemente se almacenan (se particionan) para su uso posterior.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-workflow.png)

*Ilustración 11: Flujo de trabajo de eventos de automóvil de partición*

Los datos sin procesar se particionan mediante una actividad de HDInsight Hive en "PartitionCarEventsPipeline". Se crean particiones por año y mes de los datos de muestra de un año entero generados en el paso 1 para generar particiones de datos de señales y diagnóstico de vehículo correspondientes a cada mes (total de 12 particiones) en un año.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partition-car-events-pipeline.png)

*Ilustración 12: PartitionCarEventsPipeline*

El script de Hive que se muestra a continuación, con el nombre "partitioncarevents.hql", se usa para crear particiones y se encuentra en la carpeta "\\demo\\src\\connectedcar\\scripts" del archivo zip descargado.

	SET hive.exec.dynamic.partition=true;
	SET hive.exec.dynamic.partition.mode = nonstrict;
	set hive.cli.print.header=true;

	DROP TABLE IF EXISTS RawCarEvents; 
	CREATE EXTERNAL TABLE RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
	) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

	DROP TABLE IF EXISTS Stage_RawCarEvents; 
	CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string,
				YearNo 							int,
				MonthNo 						int) 
	ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

	INSERT OVERWRITE TABLE Stage_RawCarEvents
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		Year(gendate),
		Month(gendate)

	FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

	INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		YearNo,
		MonthNo
	FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

*Ilustración 13: Script de Hive PartitionConnectedCarEvents*

Una vez que se ejecute correctamente la canalización, verá las siguientes particiones generadas en la cuenta de almacenamiento en el contenedor "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-partitioned-output.png)

*Ilustración 14: Salida con particiones*

Los datos se han optimizado, ahora son más fáciles de administrar y están listos para su posterior procesamiento con el fin de obtener información de lote completa.

## Análisis de datos

En esta sección, verá cómo usamos la combinación de Análisis de transmisiones de Azure, Aprendizaje automático de Azure, Factoría de datos de Azure y HDInsight de Azure para realizar un completo análisis avanzado sobre el mantenimiento del vehículo y los hábitos de conducción. Hay 3 subsecciones:

1.	**Aprendizaje automático**: esta subsección contiene información sobre el experimento de detección de anomalías que hemos usado en esta solución para predecir los vehículos que requieren mantenimiento y vehículos que precisan una retirada por problemas de seguridad
2.	**Análisis en tiempo real**: esta subsección contiene información relacionada con el análisis en tiempo real a través del lenguaje de consulta de Análisis de transmisiones y poniendo en funcionamiento el experimento de aprendizaje automático en tiempo real usando una aplicación personalizada
3.	**Análisis de lotes**: esta subsección contiene información acerca de la transformación y el procesamiento de los datos del lote con HDInsight de Azure y Aprendizaje automático de Azure aplicado a través de Factoría de datos de Azure

### Aprendizaje automático

Nuestro objetivo es predecir los vehículos que requieren mantenimiento o retirada según ciertas estadísticas de estado. Se realizan las suposiciones siguientes

- Los vehículos requieren **servicio de mantenimiento** cuando se cumple una de las tres condiciones siguientes:
	- Presión de los neumáticos es baja
	- Nivel de aceite del motor es bajo
	- Temperatura del motor es alta

- Vehículos pueden tener un **problema de seguridad** y requieren **retirada** cuando se cumple una de las siguientes condiciones:
	- Temperatura del motor es alta, pero la temperatura exterior es baja
	- Temperatura del motor es baja, pero la temperatura exterior es alta

En función de los requisitos anteriores, hemos creado dos modelos independientes para detectar anomalías, uno para la detección de mantenimiento del vehículo y otro para la detección de retirada del vehículo. En ambos de estos modelos, se usa el algoritmo integrado de análisis de componentes principales (PCA) para la detección de anomalías.

**Modelo de detección de mantenimiento**: en el modelo de detección de mantenimiento, el modelo informa sobre una anomalía si uno de los tres indicadores (presión de los neumáticos, aceite del motor o temperatura del motor) cumple su condición respectiva. Como resultado, solo necesitamos tener en cuenta estas tres variables en la creación del modelo. En nuestro experimento en Aprendizaje automático de Azure, primero usamos el módulo **Columnas del proyecto** para extraer estas tres variables. A continuación, usamos el módulo de detección de anomalías basado en PCA para generar el modelo de detección de anomalías.

El análisis de componentes principales (PCA) es una técnica establecida en aprendizaje automático que se puede aplicar a la selección de características, clasificación y detección de anomalías. PCA convierte un conjunto de casos con variables posiblemente correlacionadas, en un conjunto de valores denominados componentes principales. La idea clave de modelado basado en PCA es proyectar los datos en un espacio dimensional inferior, para que las características y las anomalías se puedan identificar más fácilmente.
 
En el caso de la detección de anomalías, para cada entrada nueva, el detector de anomalías calcula su proyección de los vectores propios en primer lugar y, a continuación, calcula el error de reconstrucción normalizado. Este error normalizado es la puntuación de anomalías. Cuanto mayor sea el error, más errónea es la instancia.

En el problema de detección de mantenimiento, cada registro se puede considerar como un punto en un espacio de 3 dimensiones definido por las coordenadas de presión de los neumáticos, aceite del motor y temperatura del motor. Para capturar estas anomalías, podemos proyectar los datos originales en un espacio de 3 dimensiones o de 2 dimensiones con PCA. Por lo tanto, establecemos el parámetro Número de componentes para usar en PCA con el valor 2. Este parámetro desempeña un rol importante en la aplicación de la detección de anomalías basada en PCA. Después de proyectar datos con PCA, podemos identificar más fácilmente estas anomalías.

**Modelo de detección de anomalías de retirada**: en el modelo de detección de anomalías de retirada, usamos los módulos Columnas del proyecto y de detección de anomalías basado en PCA de forma similar. En concreto, en primer lugar extraemos tres variables: temperatura del motor, temperatura exterior y velocidad, usando el módulo **Columnas de proyecto**. También incluimos la variable de velocidad ya que la temperatura del motor normalmente está correlacionada con la velocidad. A continuación, usamos el módulo de detección de anomalías basado en PCA para proyectar los datos desde el espacio de 3 dimensiones en un espacio tridimensional de 2. Se cumplen los criterios de retirada, por lo que se requiere la retirada del vehículo cuando existe una correlación negativa muy alta entre la temperatura del motor y la temperatura exterior. A través del algoritmo de detección de anomalías basado en PCA, podemos capturar las anomalías después de realizar el PCA.

Tenga en cuenta que durante el entrenamiento de ambos modelos, es necesario usar datos normales que no requieran mantenimiento o retirada para los datos de entrada de aprendizaje del modelo de detección de anomalías basado en PCA. En el experimento de puntuación, usamos el modelo de detección de anomalías entrenado para detectar si el vehículo requiere mantenimiento o retirada.


### Análisis en tiempo real

La siguiente consulta SQL de Análisis de transmisiones se usa para obtener el promedio de todos los parámetros importantes del vehículo, como velocidad del vehículo, nivel de combustible, temperatura del motor, cuentakilómetros, presión de los neumáticos, nivel de aceite del motor, etc. con el fin de detectar anomalías, emitir alertas y determinar las condiciones de estado general de los vehículos usados en una región específica y correlacionarlo con los datos demográficos.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig15-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Ilustración 15: Consulta de análisis de transmisiones para el procesamiento en tiempo real

Todos los promedios se calculan sobre una TumblingWindow de 3 segundos. En este caso estamos usando TubmlingWindow puesto que necesitamos que los intervalos de tiempo no se superpongan y sean contiguos.

Para más información sobre todas las capacidades basadas en ventanas de Análisis de transmisiones de Azure, haga clic en [Capacidades basadas en ventanas (Análisis de transmisiones de Azure)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

**Predicción en tiempo real**

Como parte de la solución se incluye una aplicación que ponga en funcionamiento el modelo de aprendizaje automático en tiempo real. Esta aplicación denominada "RealTimeDashboardApp" se crea y configura como parte de la implementación de la solución. Esta aplicación hace lo siguiente:

1.	Escucha a una instancia de Centro de eventos donde Análisis de transmisiones publica los eventos con un patrón de forma continua. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-stream-analytics-query-for-publishing.png)*Ilustración 16: Consulta de Análisis de transmisiones para la publicación de los datos a una salida de instancia de Centro de eventos* 

2.	Para cada evento que recibe esta aplicación:

	- Procesa los datos con el punto de conexión de puntuación de solicitud-respuesta de Aprendizaje automático (RRS). El punto de conexión RRS se publica automáticamente como parte de la implementación.
	- La salida RRS se publica en un conjunto de datos de Power BI mediante las API de inserción.

Este patrón también es aplicable a escenarios donde desea integrar una aplicación de línea de negocio con el flujo de análisis en tiempo real para escenarios como las alertas, notificaciones, mensajerías, etc.

Haga clic en la [descarga de RealtimeDashboardApp](http://go.microsoft.com/fwlink/?LinkId=717078) para descargar la solución de Visual Studio de RealtimeDashboardApp para las personalizaciones.

**** Para ejecutar la aplicación de panel en tiempo real **

1.	CNAMEs are displayed directly below this graphHaga clic en el nodo PowerBI en la vista de diagrama y luego en el vínculo Descargar aplicación del panel en tiempo real, en el panel de propiedades. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17-vehicle-telematics-powerbi-dashboard-setup.png) *Ilustración 17: Instrucciones de configuración del panel de Power BI*
2.	Extraer y guardar localmente ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-realtimedashboardapp-folder.png) *Ilustración 18: Carpeta RealtimeDashboardApp*
3.	Ejecute la aplicación RealtimeDashboardApp.exe.
4.	Proporcione credenciales válidas de Power BI, inicie sesión y haga clic en Aceptar ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png). 

*Ilustración 19: RealtimeDashboardApp: Inicio de sesión en Power BI*

>[AZURE.NOTE] Nota: Si desea vaciar el conjunto de datos Power BI, ejecute RealtimeDashboardApp con el parámetro "flushdata":

	RealtimeDashboardApp.exe -flushdata

### Análisis por lotes

El objetivo aquí es mostrar cómo Contoso Motors usa las capacidades de proceso de Azure para aprovechar los macrodatos, con el fin de obtener información valiosa sobre patrones de conducción, estado de vehículos y comportamientos de uso para:

- Mejorar la experiencia del cliente y abaratar los costos de la misma, proporcionando información sobre hábitos de conducción y formas de conducir que hagan un uso más eficiente del combustible
- Aprender de forma proactiva acerca de los clientes y sus patrones de conducción para encauzar la toma de decisiones empresariales y proporcionar servicios y productos inmejorables

En esta solución, nuestro destino son las siguientes métricas:

1.	**Comportamiento agresivo al volante**: identifica la tendencia de los modelos, ubicaciones, condiciones de circulación y época del año para obtener información sobre un patrón de comportamiento agresivo al volante que permita a Contoso Motors usarlo para campañas de marketing, dando lugar a seguros con nuevas características y prestaciones personalizadas y basados en el uso.
2.	**Conducción para un consumo eficiente de combustible**: identifica la tendencia de los modelos, ubicaciones, condiciones de circulación y época del año para obtener información sobre un patrón de conducción que consuma combustible de forma más eficiente, y que permita a Contoso Motors usarlo para campañas de marketing, dando lugar a nuevas prestaciones e información proactiva a los conductores sobre hábitos de conducción más económicos y ecológicos. 
3.	**Modelos de retirada**: identifica modelos que precisan ser retirados, poniendo en funcionamiento los experimentos de aprendizaje automático de detección de anomalías

Veamos cada una de estas métricas en detalle


**Patrón de comportamiento agresivo al volante**

Los datos de diagnóstico y señales de vehículo se procesan en la canalización con el nombre "AggresiveDrivingPatternPipeline" mediante Hive para determinar los modelos, ubicación, vehículo y las condiciones de conducción que exhibe el patrón de comportamiento agresivo al volante.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-aggressive-driving-pattern.png) *Ilustración 20: Flujo de trabajo del patrón de comportamiento agresivo al volante*

El script de Hive denominado "aggresivedriving.hql" se usa para analizar el patrón de comportamiento agresivo al volante se encuentra en la carpeta "\\demo\\src\\connectedcar\\scripts" en el archivo zip descargado.

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS CarEventsAggresive; 
	CREATE EXTERNAL TABLE CarEventsAggresive
	(
               	vin 						string, 
				model						string,
                timestamp					string,
				city						string,
				speed 			 			string,
				transmission_gear_position	string,
				brake_pedal_status			string,
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



	INSERT OVERWRITE TABLE CarEventsAggresive
	select
	vin,
	model,
	timestamp,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'

*Ilustración 21: Consulta de Hive para el patrón de comportamiento agresivo al volante*

Usa la combinación de la posición del cambio de marcha del vehículo, estado del pedal de freno y la velocidad para detectar comportamientos de conducción temeraria o agresiva basándose en el patrón de frenado a alta velocidad.

Una vez que se ejecute correctamente la canalización, verá las siguientes particiones generadas en la cuenta de almacenamiento en el contenedor "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aggressive-driving-pattern-output.png)

*Ilustración 22: Resultado AggressiveDrivingPatternPipeline*


**Patrón de conducción con consumo eficiente de combustible**

Los datos de diagnóstico y señales de vehículo se procesan en la canalización con el nombre "FuelEfficientDrivingPatternPipeline" mediante Hive para determinar los modelos, ubicación, vehículo y condiciones de conducción etc. que exhibe el patrón de conducción con consumo eficiente de combustible.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-fuel-efficient-driving-pattern.png)

*Ilustración 23: Flujo de trabajo del patrón de conducción con consumo eficiente de combustible*

El script de Hive denominado "fuelefficientdriving.hql" se usa para analizar el patrón de comportamiento agresivo al volante se encuentra en la carpeta "\\demo\\src\\connectedcar\\scripts" en el archivo zip descargado.

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS FuelEfficientDriving; 
	CREATE EXTERNAL TABLE FuelEfficientDriving
	(
               	vin 						string, 
				model						string,
               	city						string,
				speed 			 			string,
				transmission_gear_position	string,                
				brake_pedal_status			string,            
				accelerator_pedal_position	string,                             
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



	INSERT OVERWRITE TABLE FuelEfficientDriving
	select
	vin,
	model,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	accelerator_pedal_position,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


*Ilustración 24: Consulta de Hive para el patrón de conducción con consumo eficiente de combustible*

Usa la combinación de la posición del cambio de marcha del vehículo, estado del pedal de freno, velocidad y posición del pedal de acelerador para detectar comportamientos de conducción con consumo eficiente de combustible basándose en patrones de aceleración, frenada y velocidad.

Una vez que se ejecute correctamente la canalización, verá las siguientes particiones generadas en la cuenta de almacenamiento en el contenedor "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-fuel-efficient-driving-pattern-output.png)

*Ilustración 25: Resultados de FuelEfficientDrivingPatternPipeline*


**Predicciones de retirada**

El experimento de aprendizaje automático se proporciona y publica como un servicio web como parte de la implementación de la solución. Se aprovecha el punto de conexión de la puntuación del lote de este flujo de trabajo a través del registro como un servicio vinculado a Factoría de datos, y puesto en funcionamiento a través del uso de la actividad de puntuación de lote de Factoría de datos.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-machine-learning-endpoint.png)

*Ilustración 26: Punto de conexión de aprendizaje automático registrado como un servicio vinculado en Factoría de datos*

El servicio vinculado registrado se usa en DetectAnomalyPipeline para puntuar los datos usando el modelo de detección de anomalías.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-aml-batch-scoring.png)

*Ilustración 27: Actividad de puntuación por lotes de Aprendizaje automático de Azure en Factoría de datos*

Hay algunos pasos que se realizan en esta canalización para la preparación de los datos para que puedan usarse con el servicio web de puntuación de lote.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-pipeline-predicting-recalls.png)

*Ilustración 28: DetectAnomalyPipeline para predecir la necesidad de retirada de vehículos*

Una vez completada la puntuación, se usa una actividad de HDInsight para procesar y agregar los datos que están clasificados como anomalías por el modelo con una puntuación de probabilidad de 0,60 o superior.

	DROP TABLE IF EXISTS CarEventsAnomaly; 
	CREATE EXTERNAL TABLE CarEventsAnomaly 
	(
            	vin							string,
				model						string,
				gendate						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
				fuel						string,
				engineoil					string,
				tirepressure				string,
				odometer					string,
				city						string,
				accelerator_pedal_position	string,
				parking_brake_status		string,
				headlamp_status				string,
				brake_pedal_status			string,
				transmission_gear_position	string,
				ignition_status				string,
				windshield_wiper_status		string,
				abs  						string,
				maintenanceLabel			string,
				maintenanceProbability		string,
				RecallLabel					string,
				RecallProbability			string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

	DROP TABLE IF EXISTS RecallModel; 
	CREATE EXTERNAL TABLE RecallModel 
	(

				vin							string,
				model						string,
				city						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
            	Year						string,
				Month						string				
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

	INSERT OVERWRITE TABLE RecallModel
	select
	vin,
	model,
	city,
	outsidetemperature,
	enginetemperature,
	speed,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from CarEventsAnomaly
	where RecallLabel = '1' AND RecallProbability >= '0.60'

*Ilustración 29: Consulta de Hive para la agregación de retirada*

Una vez que se ejecute correctamente la canalización, verá las siguientes particiones generadas en la cuenta de almacenamiento en el contenedor "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-detect-anamoly-pipeline-output.png)

*Ilustración 30: Resultados de DetectAnomalyPipeline*


## Publicar

### Análisis en tiempo real

Una de las consultas en el trabajo de Análisis de transmisiones publica los eventos en una instancia de Centro de eventos de resultados.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig31-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*Ilustración 31: Trabajo de Análisis de transmisiones publica en una instancia de Centro de eventos de resultados*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig32-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*Ilustración 32: Consulta de Análisis de transmisiones para publicar en la instancia de Centro de eventos de resultados*

Esta transmisión de eventos la consume RealTimeDashboardApp que se incluye en la solución. Esta aplicación aprovecha el servicio web de solicitud-respuesta de Aprendizaje automático para puntuar en tiempo real y publica los datos resultantes en un conjunto de datos de Power BI para su consumo.

### Análisis por lotes

Los resultados del procesamiento por lote y en tiempo real se publican en las tablas de Base de datos SQL de Azure para su consumo. El servidor y Base de datos SQL de Azure y las tablas se crean automáticamente como parte del script de instalación.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig33-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*Ilustración 33: Copia de los resultados de procesamiento por lotes en el flujo de trabajo de puestos de datos*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig34-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*Ilustración 34: Trabajo de Análisis de transmisiones se publica en puestos de datos.*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig35-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*Ilustración 35: Ajuste de puesto de datos en el trabajo de Análisis de transmisiones*


## Consumo

Power BI ofrece a esta solución un panel completo para datos en tiempo real y visualizaciones de análisis predictivo.

Haga clic aquí para obtener instrucciones detalladas sobre cómo configurar los informes de Power BI y el panel. El panel final tiene este aspecto:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig36-vehicle-telematics-powerbi-dashboard.png)

*Ilustración 36: Panel de Power BI*

## Resumen

Este documento contiene un desglose detallado de la solución de análisis de telemetría de vehículos. Se presenta un patrón de arquitectura lambda para análisis en tiempo real y de procesamiento por lotes con predicciones y acciones. Este patrón se aplica a una amplia gama de casos de uso que requieren análisis con ruta de acceso activa (en tiempo real) y la ruta de acceso frío (lote).

<!----HONumber=AcomDC_0211_2016-->