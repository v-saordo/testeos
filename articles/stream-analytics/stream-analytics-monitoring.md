<properties 
	pageTitle="Descripción de la supervisión del trabajo de Análisis de transmisiones | Microsoft Azure" 
	description="Descripción de la supervisión del trabajo de Análisis de transmisiones" 
	keywords="supervisión de consultas"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/04/2016" 
	ms.author="jeffstok"/>

# Descripción de la supervisión del trabajo de Análisis de transmisiones y cómo supervisar consultas

## Introducción: Página de supervisión

Tanto el Portal de administración de Azure como el Portal de vista previa de Azure exponen métricas de rendimiento clave que se pueden utilizar para supervisar y solucionar problemas de rendimiento del trabajo y de consultas.

En el Portal de administración de Azure, haga clic en la pestaña **Supervisión** de un trabajo de Análisis de transmisiones en ejecución para ver estas métricas. Hay un retraso de como máximo 1 minuto en las métricas de rendimiento que se muestran en la página Supervisión.

  ![Panel de supervisión de trabajos](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)

En el Portal de vista previa de Azure, vaya al trabajo de Análisis de transmisiones en el que está interesado en ver las métricas y vea la sección **Supervisión**.

  ![Portal de vista previa de Azure: Panel de supervisión de trabajos](./media/stream-analytics-monitoring/06-stream-analytics-monitoring.png)

La primera vez que se crea un trabajo de Análisis de transmisiones en una región, deberá configurar los diagnósticos para esa región. Para ello, haga clic en cualquier lugar de la sección **Supervisión**; aparecerá la hoja **Diagnósticos**. Aquí puede habilitar diagnósticos y especificar una cuenta de almacenamiento para datos de supervisión.

  ![Portal de vista previa de Azure: Configuración de diagnósticos de consulta](./media/stream-analytics-monitoring/07-stream-analytics-monitoring.png)

## Métricas disponibles para Análisis de transmisiones


| Métrica | Definición |
|--------|-------------|
| SU % uso | El uso de las unidades de streaming asignadas a un trabajo en la pestaña Escala del trabajo. Si este indicador llega o supera el 80 %, existe una gran probabilidad de que el procesamiento de eventos se retrase o deje de avanzar. |
| Eventos de entrada | Cantidad de datos recibidos por el trabajo de Análisis de transmisiones, en términos de recuento de eventos. Puede usarse para validar que los eventos que se envían al origen de entrada. |
| Bytes del evento de entrada | Cantidad de datos recibidos por el trabajo de Análisis de transmisiones, en términos de rendimiento en bytes. |
| Eventos de salida | Cantidad de datos enviados por el trabajo de Análisis de transmisiones al destino de salida, en términos de recuento de eventos. |
| Eventos que no funcionan | Número de eventos recibidos fuera de orden que se eliminan o se les asigna una marca de tiempo ajustada, según la Directiva de ordenación de eventos. Puede verse afectado por la configuración del ajuste de Período de tolerancia de fuera de servicio. |
| Errores de conversión de datos | Número de errores de conversión de datos que produce un trabajo de Análisis de transmisiones. |
| Errores de tiempo de ejecución | Número de errores que se producen durante la ejecución de un trabajo de Análisis de transmisiones. |
| Eventos de entrada retrasada | Número de eventos que llegan tarde del origen y que se han eliminado o cuya marca de tiempo se ha ajustado, en función de la configuración de la Directiva de ordenación de eventos del ajuste del Período de tolerancia de fuera de servicio. |

## Personalización de la supervisión en el Portal de administración de Azure ##

Se pueden mostrar hasta 6 métricas en un gráfico.

Para alternar entre los valores relativos de visualización (solo el valor final de cada métrica) y los valores absolutos (se muestra el eje Y), seleccione Relative o Absolute en la parte superior del gráfico.

  ![Supervisión de consultas: Absoluta o Relativa](./media/stream-analytics-monitoring/02-stream-analytics-monitoring.png)

Las métricas se pueden ver en el gráfico de supervisión en las agregaciones de 1 hora, 12 horas, 24 horas o 7 días.

Para cambiar el intervalo de tiempo que se muestra en el gráfico de métricas, seleccione 1 hora, 24 horas o 7 días en la parte superior del gráfico.

  ![Escala de tiempo de supervisión de consultas](./media/stream-analytics-monitoring/03-stream-analytics-monitoring.png)

Puede establecer reglas que pueden enviarle una notificación por correo electrónico en caso de que el trabajo supere un umbral definido.

## Personalización de la supervisión en el Portal de vista previa de Azure ##

Puede ajustar el tipo de gráfico, las métricas que se muestran y el intervalo de tiempo en la configuración de Editar gráfico. Para obtener detalles, vea [Personalización de la supervisión](./azure-portal/insights-how-to-customize-monitoring.md).

  ![Portal de vista previa de Azure: Escala de tiempo de supervisión de consultas](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)

## Estado del trabajo

El estado de los trabajos de Análisis de transmisiones se puede ver en el Portal de Azure cuando ve una lista de trabajos. Para ver la lista de trabajos, haga clic en el icono de Análisis de transmisiones en el Portal de Azure.

| Estado | Definición |
|--------|------------|
| Creado | Se ha creado un trabajo, aunque no se ha iniciado. |
| Iniciando | Un usuario ha hecho clic en Iniciar el trabajo, que se está iniciando. |
| Ejecución | El trabajo está asignado, está procesando la entrada o está a la espera de procesar la entrada. Si el trabajo muestra un estado En ejecución sin generar salida, es probable que la ventana de tiempo de procesamiento de datos sea grande o la lógica de consulta sea complicada. Otra razón puede ser que actualmente no haya ningún dato que se esté enviándose al trabajo. |
| Deteniéndose | El usuario ha hecho clic en Detener el trabajo, que se detiene. |
| Stopped | El trabajo se ha detenido. |
| Degradado | Este estado indica que un trabajo de Análisis de transmisiones está encontrando errores transitorios (por ejemplo, errores de entrada/salida, errores de procesamiento o errores de conversión, entre otros). El trabajo se sigue ejecutando; sin embargo, se están generando muchos de errores. Este trabajo requiere la atención de cliente y el cliente puede ver los registros de operaciones para consultar los errores. |
| Con error | Indica que el trabajo ha fallado debido a errores, y se ha detenido el procesamiento. El cliente debe buscar en los registros de operaciones para depurar los errores. |
| Eliminando | Indica que se está eliminando el trabajo. |

## Diagnóstico

En el Portal de administración de Azure, el panel del trabajo proporciona información sobre dónde deberá buscar el diagnóstico, es decir, entradas, salidas y el registro de operaciones. Puede hacer clic en el vínculo para dirigirse a la ubicación adecuada para consultar el diagnóstico.

  ![Error de supervisión de consultas](./media/stream-analytics-monitoring/04-stream-analytics-monitoring.png)

Al hacer clic en el recurso de entrada o de salida, se proporciona información de diagnóstico detallada. Se actualiza con la información de diagnóstico más reciente mientras se ejecuta el trabajo.

  ![Diagnóstico de consultas](./media/stream-analytics-monitoring/05-stream-analytics-monitoring.png)

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0204_2016-->