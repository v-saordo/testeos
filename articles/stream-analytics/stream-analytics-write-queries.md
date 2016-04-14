<properties 
	pageTitle="Escritura de consultas en Análisis de transmisiones | Microsoft Azure" 
	description="Escritura de consultas en Análisis de transmisiones y datos de consulta | segmento de ruta de aprendizaje."
	keywords="escritura de consultas, datos de consulta, escritura de una consulta, escritura de consultas"
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

# Cómo escribir consultas en Análisis de transmisiones

La escritura de consultas con lógica de procesamiento de transmisiones de Análisis de transmisiones de Azure se implementa como una "consulta permanente" que se define antes de que un trabajo empiece y se ejecuta en los datos conforme llega al trabajo. La transformación de datos se expresa en un lenguaje de consultas similar a SQL, que es en gran medida un subconjunto de T-SQL con algunas extensiones de lenguaje agregadas como [Basado en ventanas](https://msdn.microsoft.com/library/azure/dn835019.aspx) que se usa para expresar la semántica temporal.

## Escritura de consultas: ##

1. En su trabajo de Análisis de transmisiones en el Portal de administración de Azure, haga clic en **Consulta**.

    ![Seleccionar consulta](./media/stream-analytics-write-queries/1-stream-analytics-write-queries.png)

    En el Portal de vista previa de Azure, haga clic en **Consulta**.

    ![Vista previa de selección de consulta](./media/stream-analytics-write-queries/query-preview-portal.png)

2.	Los trabajos nuevos tienen una plantilla de consulta para ayudarle a empezar. La plantilla de consulta realiza una consulta de "paso a través" que proyecta todos los campos de los eventos de entrada a la salida.

    - Si ha definido al menos una entrada y una salida para el trabajo, puede reemplazar los campos de marcador de posición "[SuAliasSalida]" y "[SuAliasEntrada]" por los alias de la entrada y la salida que quiere usar en primer lugar. Además, todavía puede crear y probar la consulta en el portal de Azure sin definir las entradas y salidas en el trabajo.
    - Si quiere realizar un procesamiento más que un simple paso a través, puede editar la definición de la consulta. Para empezar con la creación de consultas, observe algunos patrones de consultas comunes que se capturan [aquí](stream-analytics-stream-analytics-query-patterns.md).  
  
    ![Ventana de datos de consulta](./media/stream-analytics-write-queries/2-stream-analytics-write-queries.png)

## Para comprobar que los datos de consulta funcionan: ##

Puede probar que la consulta se comporta según lo esperado si la ejecuta en el explorador a través de uno o más archivos JSON locales que contienen los datos de prueba. Esto no iniciará el trabajo ni tendrá implicaciones de facturación.

> [AZURE.NOTE] Actualmente no se admiten pruebas de consultas en el explorador en el Portal de vista previa de Azure.

1.	Asegúrese de que no hay errores en la consulta (en caso contrario, se deshabilitará el botón Probar) y luego haga clic en el botón Probar.  

    ![Prueba de datos de consulta](./media/stream-analytics-write-queries/3-stream-analytics-write-queries.png)

2.	Se le pedirá que especifique archivos para cada una de las entradas a las que se hace referencia en la consulta. En este ejemplo, la consulta de la plantilla se deja tal cual, por lo que el cuadro de diálogo solicita una entrada denominada "sualiasentrada".

    ![Consulta de datos de prueba](./media/stream-analytics-write-queries/4-stream-analytics-write-queries.png)

3.	Vaya hasta un archivo de prueba. Hay varios archivos de ejemplo disponibles en [github] (datos de https://github.com/Azure/azure-stream-analytics/tree/master/Sample) y también puede recuperar datos de ejemplo de sus propias entradas de flujo de datos a través de la función de datos de ejemplo de la pestaña de entradas.

    ![Entrada de consulta](./media/stream-analytics-write-queries/5-stream-analytics-write-queries.png)

4.	Después de cerrar el cuadro de diálogo, se ejecutará su consulta para los datos de prueba y verá los resultados en la parte inferior de la página de consulta.

    ![Resumen de la consulta](./media/stream-analytics-write-queries/6-stream-analytics-write-queries.png)

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0204_2016-->