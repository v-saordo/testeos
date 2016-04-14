<properties
	pageTitle="Introducción a los Centros de eventos en C y C# | Microsoft Azure"
	description="Siga este tutorial para empezar a usar Centros de eventos de Azure, a enviar eventos con C y a recibirlos en C# mediante EventProcessorHost."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="c"
	ms.devlang="csharp"
	ms.topic="article"
	ms.date="12/09/2015"
	ms.author="sethm"/>

# Introducción a los Centros de eventos

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introducción

Centros de eventos es un sistema de recopilación de alta escalabilidad que puede recibir millones de eventos por segundo, habilitando una aplicación para procesar y analizar las grandes cantidades de datos generados por las aplicaciones y los dispositivos conectados. Una vez recopilados en los Centros de eventos, puede transformar y almacenar los datos usando cualquier proveedor de análisis en tiempo real o clúster de almacenamiento.

Para obtener más información, consulte [Información general de los Centros de eventos de Azure][].

En este tutorial, aprenderá a recopilar mensajes en un Centro de eventos mediante una aplicación de consola en C y a recuperarlos en paralelo con la biblioteca [Event Processor Host][] de C#.

Para completar este tutorial, necesitará lo siguiente:

+ Un entorno de desarrollo de C. Para este tutorial, consideraremos la pila de gcc en una [VM Linux de Azure](../virtual-machines/virtual-machines-linux-tutorial.md) con Ubuntu 14.04. En los vínculos externos se proporcionarán instrucciones para otros entornos.

+ Microsoft Visual Studio Express 2013 para Windows.

+ Una cuenta de Azure activa. <br/>En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Evaluación gratuita de Azure</a>.

## Creación de un Centro de eventos

1. Inicie sesión en el [Portal de Azure clásico][], y haga clic en **NUEVO** en la parte inferior de la pantalla.

2. Haga clic en **Servicios de aplicaciones**, **Bus de servicio**, **Centro de eventos** y, finalmente, en **Creación rápida**.

   	![][1]

3. Escriba un nombre para el centro de eventos, seleccione la región deseada y, a continuación, haga clic en **Crear un centro de eventos**.

   	![][2]

4. Haga clic en el espacio de nombres que acaba de crear (normalmente ***nombre del centro de eventos*-ns**).

   	![][3]

5. Haga clic en la pestaña **Centros de eventos** en la parte superior de la página y, a continuación, haga clic en el centro de eventos que acaba de crear.

   	![][4]

6. Haga clic en la pestaña **Configurar** en la parte superior de la página, agregue una regla denominada **SendRule** con derechos de *envío*, agregue otra regla denominada **ReceiveRule** con derechos de *administración, envío y escucha*, y luego haga clic en **Guardar**.

   	![][5]

7. En la misma página, anote las claves generadas para **SendRule**.

   	![][6b]

8. Haga clic en la pestaña **Panel** en la parte superior de la página y, a continuación, haga clic en **Información de conexión**. Anote las dos cadenas de conexión.

   	![][6]

Ya se ha creado Centro de eventos y cuenta con las cadenas de conexión que necesita para enviar y recibir eventos.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-c](../../includes/service-bus-event-hubs-get-started-send-c.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Ejecución de las aplicaciones

Ya está preparado para ejecutar las aplicaciones.

1.	Ejecute el proyecto **Receiver** desde Visual Studio y espere a que inicie los receptores para todas las particiones.

   	![][21]

2.	Ejecute el programa **sender** y verá que los eventos aparecen en la ventana del receptor.

   	![][24]

## Pasos siguientes

Ahora que ha creado una aplicación de trabajo que crea un centro de eventos y envía y recibe datos, puede pasar a los siguientes escenarios:

- Una [aplicación de ejemplo completa que usa Centros de eventos][].
- El ejemplo [Escala horizontal del procesamiento de eventos con Centros de eventos][].
- Una [solución de mensajería en cola][] mediante las colas de Bus de servicio.
- [Información general de los Centros de eventos][]

<!-- Images. -->
[1]: ./media/event-hubs-c-ephcs-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-c-ephcs-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-c-ephcs-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-c-ephcs-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-c-ephcs-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-c-ephcs-getstarted/create-event-hub6.png
[6b]: ./media/event-hubs-c-ephcs-getstarted/create-event-hub6b.png


[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Portal de Azure clásico]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Información general de los Centros de eventos]: event-hubs-overview.md
[Información general de los Centros de eventos de Azure]: event-hubs-overview.md
[aplicación de ejemplo completa que usa Centros de eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escala horizontal del procesamiento de eventos con Centros de eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[solución de mensajería en cola]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

<!---HONumber=AcomDC_1217_2015-->