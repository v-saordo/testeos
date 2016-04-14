<properties 
	pageTitle="Inicio de trabajos de streaming en Análisis de transmisiones | Microsoft Azure" 
	description="Ejecución de un trabajo de streaming en Análisis de transmisiones de Azure | segmento de ruta de acceso de aprendizaje."
    keywords="Trabajos de streaming"
	documentationCenter=""
	services="stream-analytics"
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

# Ejecución de un trabajo de streaming en Análisis de transmisiones de Azure

Cuando se hayan especificado una entrada, una consulta y una salida de trabajo, podrá iniciar el trabajo de Análisis de transmisiones.

Para iniciar su trabajo:

1.	En el Portal de Azure, en el panel del trabajo, haga clic en **Iniciar** en la parte inferior de la página.

    ![Botón Iniciar trabajo](./media/stream-analytics-run-a-job/1-stream-analytics-run-a-job.png)

    En el Portal de vista previa de Azure, haga clic en **Iniciar** en la parte superior de la página de trabajo.

    ![Portal de vista previa de Azure: Botón Iniciar trabajo](./media/stream-analytics-run-a-job/4-stream-analytics-run-a-job.png)

2.	Especifique un valor en **Iniciar salida** para determinar cuándo comenzará este trabajo a generar una salida. La configuración predeterminada para los trabajos que no se han iniciado anteriormente es **Hora de inicio del trabajo**, lo que significa que el trabajo iniciará inmediatamente el procesamiento de datos. También puede especificar un tiempo **Personalizado** en el pasado (para consumir datos históricos) o en el futuro (para retrasar el procesamiento hasta una fecha futura). En los casos en que un trabajo se ha iniciado y detenido anteriormente, la opción **Hora de la última detención** está disponible para reanudar el trabajo desde la última hora de salida y evitar la pérdida de datos.

    ![Hora de inicio del trabajo de streaming](./media/stream-analytics-run-a-job/2-stream-analytics-run-a-job.png)

    ![Portal de vista previa de Azure: Hora de inicio del trabajo de streaming](./media/stream-analytics-run-a-job/5-stream-analytics-run-a-job.png)

3.	Confirme la selección. El estado del trabajo cambiará a *Iniciando* y, en cuanto se inicie el trabajo, pasará a *En ejecución*. Puede supervisar el progreso de la operación **Iniciar** en el **Centro de notificaciones**:

    ![progreso del trabajo de streaming](./media/stream-analytics-run-a-job/3-stream-analytics-run-a-job.png)

    ![Portal de vista previa de Azure: Progreso del trabajo de streaming](./media/stream-analytics-run-a-job/6-stream-analytics-run-a-job.png)

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0204_2016-->