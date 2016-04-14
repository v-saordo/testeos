<properties
	pageTitle="Agrega entradas de datos a trabajos de Análisis de transmisiones | Microsoft Azure"
	description="Obtenga información acerca de cómo enlazar un origen de datos al trabajo de Análisis de transmisiones como entrada de datos streaming desde los Centros de eventos, o bien como datos de referencia desde el almacenamiento de blogs."
	keywords="entrada de datos, datos de streaming"
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="02/04/2016"
	ms.author="jeffstok"
/>


# Adición de entradas de datos streaming o datos de referencia a trabajos de Análisis de transmisiones

Obtenga información acerca de cómo enlazar un origen de datos al trabajo de Análisis de transmisiones como entrada de datos streaming desde los Centros de eventos, o bien como datos de referencia desde el almacenamiento de blogs.

Los trabajos de Análisis de transmisiones de Azure pueden estar conectados a una entrada de datos, donde cada una define una conexión a un origen de datos existente. A medida que los datos se envían a ese origen de datos, el trabajo de Análisis de transmisiones los consume y los procesa en tiempo real como datos de streaming. Análisis de transmisiones cuenta con integración de primera clase con [Centro de eventos de orígenes de Azure](https://azure.microsoft.com/services/event-hubs/) y [Almacenamiento de blobs de Azure](./storage/storage-dotnet-how-to-use-blobs.md) desde dentro y fuera de la suscripción del trabajo.

Este artículo es un paso de la [ruta de aprendizaje de Análisis de transmisiones](/documentation/learning-paths/stream-analytics/).

## Entrada de datos: datos streaming y datos de referencia

Hay dos tipos distintos de entradas en Análisis de transmisiones: flujos de datos y datos de referencia.

- **Secuencias de datos**: los trabajos de Análisis de transmisiones deben incluir, al menos, una entrada de secuencia de datos para que el trabajo la consuma y transforme. Almacenamiento de blobs de Azure y Centros de eventos de Azure se admiten como orígenes de entrada de flujos de datos. Los Centros de eventos de Azure se usan para recopilar transmisiones de eventos desde dispositivos conectados, servicios y aplicaciones. El almacenamiento de blobs de Azure puede usarse como origen de entrada para la ingesta de datos en masa como secuencia.  
- **Datos de referencia**: Análisis de transmisiones admite un segundo tipo de entrada auxiliar denominada datos de referencia. A diferencia de los datos en movimiento, estos datos son estáticos o están desacelerando los cambios. Normalmente se usa para realizar búsquedas y correlaciones con secuencias de datos para crear un conjunto de datos más amplio. Almacenamiento de blobs de Azure es el único origen de entrada admitido actualmente para los datos de referencia.  

Para agregar una entrada a su trabajo de Análisis de transmisiones:

1. En el portal de Azure, haga clic en **Entradas** y luego en **Agregar una entrada** en el trabajo de Análisis de transmisiones.

    ![Portal de Azure clásico: agregar entrada.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)

    En el Portal de Azure, haga clic en el icono **Entradas** en el trabajo de Análisis de transmisiones.

    ![Portal de Azure: agregar entrada.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)

2. Especifique el tipo de la entrada: **Flujo de datos** o **Datos de referencia**.

    ![Adición de la entrada correcta de datos, de streaming o de referencia](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)

    ![Adición de la entrada correcta de datos, de streaming o de referencia](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)

3. Si crea una entrada de Flujo de datos, especifique el tipo de origen para la entrada. Este paso se omite durante la creación de Datos de referencia ya que, en este momento, solo se admite el almacenamiento de blobs.

    ![Adición de entrada de datos de flujo de datos](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)

    ![Adición de entrada de datos de flujo de datos](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)

4. Asigne un nombre descriptivo para esta entrada en el cuadro Alias de entrada. Este nombre se usará en la consulta de su trabajo más adelante para hacer referencia a la entrada.

    Rellene el resto de las propiedades de conexión necesarias para conectarse a su origen de datos. Estos campos varían según el tipo de entrada y de origen y se definen en detalle [aquí](stream-analytics-create-a-job.md).

    ![Adición de entrada de datos de Centro de eventos](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)

5. Especifique la configuración de serialización para los datos de entrada:
	- Para asegurarse de que las consultas funcionan según lo previsto, especifique el **Formato de serialización de eventos** de los datos entrantes. Los formatos de serialización compatibles son JSON, CSV y Avro.
	- Compruebe el valor de **Codificación** para los datos. Por el momento, UTF-8 es el único formato de codificación compatible.

    ![Configuración de la serialización de datos para la entrada de datos](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)

    ![Configuración de la serialización de datos para la entrada de datos](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)

6. Después de completar la creación de la entrada, el Análisis de transmisiones comprobará que se puede conectar al origen de la entrada. Puede ver el estado de la operación de prueba de conexión en el centro de notificaciones.

    ![Prueba de la conexión de la entrada de datos de streaming](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)

    ![Prueba de la conexión de la entrada de datos de streaming](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)

## Obtener ayuda con las entradas de datos streaming
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0211_2016-->