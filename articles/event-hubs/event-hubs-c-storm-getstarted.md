<properties
	pageTitle="Introducción a los Centros de eventos con C y Apache Storm | Microsoft Azure"
	description="Siga este tutorial para empezar a usar Centros de eventos de Azure; a enviar eventos en C y a recibirlos en un clúster de Apache Storm."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="c"
	ms.devlang="java"
	ms.topic="article" 
	ms.date="12/09/2015"
	ms.author="sethm"/>

# Introducción a los Centros de eventos

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introducción

Centros de eventos es un sistema de recopilación de alta escalabilidad que puede recibir millones de eventos por segundo, habilitando una aplicación para procesar y analizar las grandes cantidades de datos generados por las aplicaciones y los dispositivos conectados. Una vez recopilados en los Centros de eventos, puede transformar y almacenar los datos usando cualquier proveedor de análisis en tiempo real o clúster de almacenamiento.

Para obtener más información, consulte [Información general de los centros de eventos].

En este tutorial, obtendrá información sobre cómo insertar mensajes en un Centro de eventos mediante una aplicación de consola en C y cómo recuperarlos en paralelo mediante Apache Storm.

Para completar este tutorial, necesitará lo siguiente:

+ Un entorno de desarrollo de C. Para este tutorial, consideraremos la pila de gcc en una [VM Linux de Azure](../virtual-machines/virtual-machines-linux-tutorial.md) con Ubuntu 14.04. En los vínculos externos se proporcionarán instrucciones para otros entornos.

+ Un entorno de desarrollo de Java configurado para ejecutar [Maven](http://maven.apache.org/). Para este tutorial, asumimos que vamos a trabajar con [Eclipse](https://www.eclipse.org/).

+ Una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

## Creación de un Centro de eventos

1. Inicie sesión en el [Portal de Azure clásico], y haga clic en **NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, **Service Bus**, **Centro de eventos** y, finalmente, en **Creación rápida**.

	![][1]

3. Escriba un nombre para el centro de eventos, seleccione la región deseada y, a continuación, haga clic en **Crear un centro de eventos**.

	![][2]

4. Haga clic en el espacio de nombres que acaba de crear (normalmente ***nombre del centro de eventos*-ns**).

	![][3]

5. Haga clic en la pestaña **Centros de eventos** en la parte superior de la página y, a continuación, haga clic en el centro de eventos que acaba de crear.

	![][4]

6. Haga clic en la pestaña **Configurar** en la parte superior de la página, agregue una regla llamada **SendRule** con derechos *Enviar*, agregue otra regla llamada **ReceiveRule** con derechos *Escuchar* y, a continuación, haga clic en **Guardar**.

	![][5]

7. En la misma página, anote las claves generadas para **SendRule** y **ReceiveRule**.

	![][6c]

Ya se ha creado Centro de eventos y cuenta con las cadenas de conexión que necesita para enviar y recibir eventos.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-storm](../../includes/service-bus-event-hubs-get-started-receive-storm.md)]

## Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1.	Ejecute la clase **LogTopology** desde Eclipse y espere a que inicie los receptores para todas las particiones.

2.	Ejecute el programa **sender** y verá que los eventos aparecen en la ventana del receptor.

	![][23]

> [AZURE.NOTE]Solo en este tutorial, use Storm en modo local para fines de desarrollo. Consulte [Introducción a Storm en HDInsight] y la documentación oficial de [Apache Storm] para obtener más información sobre los patrones y las implementaciones de Storm.

## Pasos siguientes

Los siguientes recursos están disponibles para desarrollar aplicaciones que integran Centros de eventos y Storm.

- [Análisis de datos de sensores con Apache Storm, Centro de eventos y HBase en HDInsight][] es un tutorial de escenario integral donde se usan Centros de eventos, Storm y HBase para introducir datos de sensores en un clúster de Hadoop.
- [Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio][] es un tutorial donde se describe cómo escribir procesos de Storm con C#.

<!-- Images. -->
[1]: ./media/event-hubs-c-storm-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-c-storm-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-c-storm-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-c-storm-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-c-storm-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-getstarted/create-event-hub6.png
[6c]: ./media/event-hubs-c-storm-getstarted/create-event-hub6c.png

[23]: ./media/event-hubs-c-storm-getstarted/receive-storm3.png

<!-- Links -->
[Portal de Azure clásico]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Información general de los centros de eventos]: event-hubs-overview.md

[Apache Storm]: https://storm.incubator.apache.org
[Introducción a Storm en HDInsight]: ../hdinsight/hdinsight-storm-overview.md/
[Análisis de datos de sensores con Apache Storm, Centro de eventos y HBase en HDInsight]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md
[Desarrollo de topologías de C# para Apache Storm en HDInsight con Visual Studio]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
 

<!---HONumber=AcomDC_1217_2015-->