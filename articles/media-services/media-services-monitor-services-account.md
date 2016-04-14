<properties 
	pageTitle="Supervisión de una cuenta de Servicios multimedia" 
	description="Describe cómo configurar la supervisión para la cuenta de Servicios multimedia en Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>

#<a id="monitormediaservicesaccount"></a>Supervisión de una cuenta de Servicios multimedia

El panel Servicios multimedia de Azure presenta las métricas de uso y la información de la cuenta que se pueden utilizar para administrar la cuenta de Servicios multimedia.

Puede supervisar el número de trabajos de codificación en cola, tareas de codificación con error, trabajos de codificación activos representados por los datos de entrada y salida del codificador, así como el uso de almacenamiento de blobs asociados a la cuenta de Servicios multimedia. Además, si está realizando streaming de contenido a los clientes, puede recuperar también varias métricas de streaming. Puede elegir supervisar los datos durante las últimas 6 horas, 24 horas o 7 días.
 
>[AZURE.NOTE] Los costes adicionales están asociados a la supervisión de los datos de almacenamiento en el Portal de Azure clásico. Para obtener más información, consulte[ Facturación y análisis de almacenamiento](http://go.microsoft.com/fwlink/?LinkId=256667).

##<a id="configuremonitoring"></a>Supervisión de una cuenta de Servicios multimedia

1. En el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=256666), haga clic en **Servicios multimedia** y haga clic en el nombre de la cuenta de Servicios multimedia para abrir el panel. 

	![Panel\_ServiciosMultimedia][dashboard]

2. Para supervisar los datos o trabajos de codificación, comience a enviar los trabajos de codificación a Servicios multimedia, o comience a realizar streaming de contenido a los clientes a través de streaming a petición de Servicios multimedia de Azure. Debería empezar a ver los datos de supervisión en el panel al cabo de una hora aproximadamente.

##<a id="configuringstorage"></a>Supervisión del uso de almacenamiento de blobs (opcional)
1. Haga clic en el nombre de la **CUENTA DE ALMACENAMIENTO** en la sección de **vista rápida**.
2. En la página de la cuenta de almacenamiento, haga clic en el vínculo de la **página de configuración** y desplácese hacia abajo hasta la configuración de **supervisión** para los servicios Blob, Tabla y Cola, mostrados a continuación.

	>[AZURE.NOTE] Los blobs son el único tipo de almacenamiento admitido en Servicios multimedia.

	![OpcionesAlmacenamiento][storage_options_scoped]

3. En **supervisión**, configure el nivel de supervisión y la directiva de retención de datos para los blobs:

-  Para configurar el nivel de supervisión, seleccione una de las opciones siguientes:

      **Minimal**: recopila métricas como entrada/salida, disponibilidad, latencia y porcentajes de éxito, que se agregan a los servicios Blob, Tabla y Cola.

      **Verbose**: además de las métricas mínimas, recopila el mismo conjunto de métricas para cada operación de almacenamiento en la API del Servicio de almacenamiento de Azure. Las métricas detalladas facilitan el análisis preciso de los problemas que se producen durante las operaciones de las aplicaciones.

      **Off**: desactiva la supervisión. Los datos de supervisión existentes permanecen disponibles hasta el final del periodo de retención.

- Para configurar la directiva de retención de datos, en **Retención (en días)**, escriba el número de días que se deben retener los datos, entre 1 y 365. Si no desea configurar una directiva de retención, escriba un cero. Si no existe una directiva de retención, es posible eliminar los datos de supervisión. Recomendamos que configure una directiva de retención basada en el tiempo que desee retener los datos de análisis de almacenamiento de su cuenta, de modo que el sistema pueda eliminar sin coste los datos de análisis antiguos o no usados.

4. Al finalizar la configuración de la supervisión, haga clic en **Guardar**. De manera similar a las métricas de Servicios multimedia, debería empezar a ver los datos de supervisión en el panel al cabo de una hora aproximadamente. Las métricas se almacenan en la cuenta de almacenamiento en cuatro tablas denominadas $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue y $MetricsCapacityBlob. Para obtener más información, consulte [Métricas del análisis de almacenamiento](http://go.microsoft.com/fwlink/?LinkId=256668).



##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]flujo de trabajo de streaming a petición](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


<!-- Images -->
[dashboard]: ./media/media-services-monitor-services-account/media-services-dashboard.png
[storage_options_scoped]: ./media/media-services-monitor-services-account/storagemonitoringoptions_scoped.png

 

<!---HONumber=AcomDC_0211_2016-->