<properties
	pageTitle="Introducción a Análisis de transmisiones de Azure para el procesamiento de datos desde dispositivos de IoT | Análisis de transmisiones"
	description="Flujos de datos y SensorTags de IoT con análisis de transmisiones y procesamiento de datos en tiempo real"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/11/2016"
	ms.author="jeffstok"
/>

# Introducción a Análisis de transmisiones de Azure para el procesamiento de datos desde dispositivos de IoT

En este tutorial, aprenderá a crear una lógica de procesamiento de transmisiones para recopilar datos desde dispositivos de Internet de las cosas (IoT). Usaremos un caso de uso real de Internet de las cosas para mostrar cómo puede crear una solución de forma rápida y económica.

## Requisitos previos

-   [Suscripción de Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Archivos de datos y consultas de ejemplo que se pueden descargar desde [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/GettingStarted)

## Escenario

Contoso es una empresa manufacturera en el sector de la automatización industrial que automatizó completamente su proceso de fabricación. La maquinaria de esta planta cuenta con sensores que emiten flujos de datos en tiempo real. En este escenario, un administrador del piso de producción desea tener información en tiempo real de los datos provenientes de los sensores para buscar patrones y llevar a cabo las acciones que sean necesarias. Usaremos el lenguaje de consulta de Análisis de transmisiones (SAQL) sobre los datos de los sensores para encontrar patrones interesantes en los flujos de datos entrantes.

Estos datos provienen de un dispositivo SensorTag de Texas Instruments.

![SensorTag de Texas Instruments](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

La carga de los datos está en formato JSON y tiene un aspecto similar al siguiente:

    
	{
    	"time": "2016-01-26T20:47:53.0000000",  
	    "dspl": "sensorE",  
    	"temp": 123,  
	    "hmdt": 34  
	}  
    
En un escenario real, habrá cientos de estos sensores generando eventos como una transmisión. Idealmente, habría un dispositivo de puerta de enlace que ejecute cierto código para insertar estos eventos en el [Centros de eventos de Azure](https://azure.microsoft.com/services/event-hubs/). El trabajo de Análisis de transmisiones podría usar estos eventos de Centros de eventos y ejecutar un análisis en tiempo real, expresado como consultas, y enviar los resultados a las salidas deseadas.

En esta guía de introducción, se proporciona un archivo de datos de ejemplo, capturado a partir de dispositivos SensorTag reales, sobre el cual puede ejecutar distintas consultas y ver sus resultados. En tutoriales subsiguientes, aprenderá a conectar el trabajo a las entradas y salidas y a implementarlas en el servicio de Azure.

## Creación de un Análisis de transmisiones

Para crear un nuevo trabajo de análisis, vaya al [Portal de Azure](http://manage.windowsazure.com), abra Análisis de transmisiones y haga clic en **"Nuevo"** en la esquina inferior izquierda de la página.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

Haga clic en "**Creación rápida**".

Para la configuración de la **"Cuenta de almacenamiento de supervisión regional"**, seleccione **"Crear nueva cuenta de almacenamiento"** y asígnele un nombre único. Análisis de transmisiones de Azure usará esta cuenta para almacenar la información de supervisión de todos los futuros trabajos.

> [AZURE.NOTE] Deberá crear esta cuenta de almacenamiento solo una vez por región y este almacenamiento se compartirá a todos los trabajos de Análisis de transmisiones de esa región.

Haga clic en "**Crear el trabajo de Análisis de transmisiones**" en la parte inferior de la página.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.jpg)

## Consulta de Análisis de transmisiones de Azure

Haga clic en la pestaña Consulta para ir al Editor de consultas. La ficha Consulta contiene una consulta SQL que realiza la transformación de los datos entrantes.

## Archivado de los datos sin procesar

La forma más sencilla de una consulta es una consulta de paso a través, la que archivará todos los datos de entrada a la salida designada.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

Ahora, descargue el archivo de datos de ejemplo desde [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/GettingStarted) en una ubicación de su equipo. Copie y pegue la consulta desde el archivo **PassThrough.txt**. Haga clic en el botón Test (Probar) que aparece abajo y seleccione el archivo de datos denominado **HelloWorldASA-InputStream.json** en la ubicación de la descarga.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

Puede ver los resultados de la consulta en el explorador que aparece a continuación.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

## Limpieza de los datos según una condición

Intentemos filtrar los resultados según una condición. Nos gustaría mostrar los resultados solo para los eventos que provienen desde el "SensorA". La consulta se encuentra en el archivo **Filtering.txt**.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Observe que aquí comparamos un valor de cadena y que distingue mayúsculas de minúsculas. Haga clic en el botón **Rerun** (Volver a ejecutar) para ejecutar la consulta. La consulta solo devolverá 389 filas de 1860 eventos.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

## Alertas para desencadenar el flujo de trabajo de negocio

Ahora, hagamos que nuestra consulta sea un poco más interesante. Para cada tipo de sensor, si deseamos supervisar la temperatura promedio durante 30 segundos y mostrar los resultados solo si la temperatura promedio supera los 100 grados, escribiremos la consulta a continuación y, luego, haremos clic en Rerun (Volver a ejecutar) para ver los resultados. La consulta se encuentra en el archivo **ThresholdAlerting.txt**.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Puede ver que ahora los resultados contienen solo 245 filas con los sensores donde la temperatura promedio superó los 100 grados. En esta consulta, agrupamos la transmisión de eventos por dspl, que es el nombre del sensor, y con respecto a una **ventana de saltos de tamaño constante** de 30 segundos. Cuando realizamos consultas temporales similares, resulta fundamental establecer cómo deseamos hacer avanzar el tiempo. Mediante la cláusula **TIMESTAMP BY**, especificamos la columna "time" ("tiempo") como una forma de hacer avanzar el tiempo para todos los cálculos temporales. Si desea obtener información detallada, lea los temas de MSDN sobre [Administración del tiempo](https://msdn.microsoft.com/library/azure/mt582045.aspx) y [Basado en ventanas](https://msdn.microsoft.com/library/azure/dn835019.aspx).

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

## Cómo encontrar una falta de patrones

¿Cómo se puede escribir una consulta para encontrar una falta de patrones? Por ejemplo, averigüemos la última vez que un sensor envió datos y luego no envió ningún evento durante el próximo minuto. La consulta se encuentra en el archivo **AbsenseOfEvent.txt**.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-12.png)

Aquí usamos una **LEFT OUTER JOIN** (COMBINACIÓN EXTERNA IZQUIERDA) en el mismo flujo de datos (autocombinación). Para una combinación interna, solo se devuelve un resultado cuando se encuentra una coincidencia. Pero, en el caso de una combinación **LEFT OUTER** (EXTERNA IZQUIERDA), si un evento proveniente del lado izquierdo de la combinación no tiene coincidencia, se devolverá una fila con un valor NULL en todas las columnas de la derecha. Esta técnica resulta muy útil para encontrar la ausencia de eventos. Si desea obtener más detalles sobre [JOIN](https://msdn.microsoft.com/library/azure/dn835026.aspx) (COMBINACIÓN), consulte la documentación de MSDN.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-13.png)

## Conclusión

Este tutorial es, básicamente, una introducción sobre cómo escribir distintas consultas SAQL y ver los resultados en el explorador sobre distintos patrones en los datos. Sin embargo, se trata solo de una introducción. Es mucho más lo que puede hacer con Análisis de transmisiones. A continuación, aprenderá a conectar el trabajo de Análisis de transmisiones a las entradas y salidas e implementarlo en Azure. Puede comenzar a explorar más información sobre Análisis de transmisiones con nuestra guía [Mapa de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/). Además, si desea obtener más información sobre cómo escribir consultas, lea el artículo [Patrones de consultas comunes](./stream-analytics-stream-analytics-query-patterns.md#query-example-detect-the-absence-of-events).

<!---HONumber=AcomDC_0224_2016-->