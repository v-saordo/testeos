<properties 
   pageTitle="Disponibilidad y el soporte técnico de los Centros de eventos | Microsoft Azure"
   description="Preguntas más frecuentes sobre la disponibilidad y el soporte técnico de los Centros de eventos"
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="sethm" />

# Preguntas más frecuentes sobre la disponibilidad y el soporte técnico de los Centros de eventos

Los Centros de eventos ofrecen consumo a gran escala, persistencia y procesamiento de eventos de datos de orígenes de datos de alto rendimiento o millones de dispositivos. Cuando se emparejan con temas y colas de Bus de servicio, los Centros de eventos permiten comandos persistentes e implementaciones de control para los escenarios de Internet de las cosas.

En esta sección se proporciona información sobre disponibilidad y respuestas a algunas preguntas frecuentes.

## Información de precios

Para obtener una completa información sobre los precios de los Centros de eventos, consulte los [detalles de precios de los Centros de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

## ¿Cómo se calculan los eventos de entrada de los Centros de eventos?

Cada evento enviado a un Centro de eventos cuenta como un mensaje facturable. Un *evento de entrada* se define como una unidad de datos que es menor o igual que 64 KB. Cualquier evento que tenga un tamaño menor o igual que 64 KB se considera un evento facturable. Si el evento es mayor de 64 KB, el número de eventos facturables se calcula según el tamaño del evento, en múltiplos de 64 KB. Por ejemplo, un evento de 8 KB enviado al Centro de eventos se factura como un evento, pero un mensaje de 96 KB enviado al Centro de eventos se factura como dos eventos.

Los eventos consumidos de un Centro de eventos, así como las operaciones de administración y las llamadas de control como los puntos de comprobación, no se cuentan como eventos de entrada facturables, pero se acumulan hasta la asignación de unidad de procesamiento.

## ¿Qué son las unidades de procesamiento de los Centros de eventos?

Las unidades de procesamiento de los Centros de eventos las selecciona explícitamente el usuario, a través del portal de Azure clásico o de la API de administración de Centros de eventos. Las unidades de procesamiento que se aplican a todos los Centros de eventos de un espacio de nombres del Bus de servicio y cada unidad de procesamiento da derecho al espacio de nombres a las siguientes capacidades:

- Hasta 1 MB por segundo de eventos de entrada (eventos enviados a un Centro de eventos), pero no más de 1000 eventos de entrada, operaciones de administración o llamadas a la API de control por segundo.

- Hasta 2 MB por segundo de eventos de salida (eventos consumidos de un Centro de eventos).

- Hasta 84 GB de almacenamiento de eventos (suficiente para el período de retención predeterminado de 24 horas).

Las unidades de procesamiento de los Centros de eventos se facturan por horas, según el número máximo de unidades seleccionadas durante la hora en cuestión.

## ¿Cómo se aplican los límites de unidades de rendimiento de los Centros de eventos?

Si el procesamiento de entrada total o la tasa de eventos de entrada total en todos los Centros de eventos en un espacio de nombres superan las unidades de procesamiento totales permitidas, los remitentes se limitarán y recibirán errores que indican que se superó la cuota de entrada.

Si el procesamiento de salida total o la tasa de eventos de salida total en todos los Centros de eventos en un espacio de nombres superan las unidades de procesamiento totales permitidas, los receptores se limitarán y recibirán errores que indican que se superó la cuota de salida. Las cuotas de entrada y de salida se aplican por separado, por lo que ningún remitente puede provocar que se ralentice el consumo de eventos ni ningún receptor puede impedir que los eventos se envíen a un Centro de eventos.

Tenga en cuenta que la selección de la unidad de procesamiento es independiente del número de particiones de los Centros de eventos. Aunque cada partición ofrece un procesamiento máximo de 1 MB por segundo de entrada (con un máximo de 1000 eventos por segundo) y 2 MB por segundo de salida, no hay ningún cargo fijo por las propias particiones. El cargo corresponde a las unidades de procesamiento agregadas en todos los Centros de eventos en un espacio de nombres del Bus de servicio. Con este patrón, puede crear particiones suficientes para admitir la carga máxima anticipada de sus sistemas, sin incurrir en cargos por unidades de procesamiento hasta que la carga de eventos en el sistema requiera realmente mayores cifras de procesamiento, sin tener que cambiar la estructura y la arquitectura de los sistemas a medida que la carga del sistema aumente.

## ¿Hay un límite en el número de unidades de procesamiento que se pueden seleccionar?

Hay una cuota predeterminada de 20 unidades de procesamiento por espacio de nombres. Puede solicitar una cuota de unidades de procesamiento mayor, si rellena una incidencia de soporte técnico. Más allá del límite de 20 unidades de procesamiento, hay disponibles paquetes de 20 y 100 unidades de procesamiento. Tenga en cuenta que al usar más de 20 unidades de procesamiento, se quita la capacidad de cambiar el número de unidades de procesamiento sin rellenar una incidencia de soporte técnico.

## ¿Hay un cargo por retener eventos de los Centros de eventos durante más de 24 horas?

El nivel Standard de los Centros de eventos permiten períodos de retención de mensajes superiores a 24 horas, hasta un máximo de 30 días. Si el tamaño de la cantidad total de eventos almacenados supera la asignación de almacenamiento para el número de unidades de procesamiento seleccionadas (84 GB por unidad de procesamiento), el tamaño que supere la asignación se cargará con la tarifa publicada de almacenamiento de blobs de Azure. La asignación de almacenamiento en cada unidad de procesamiento cubre todos los costos de almacenamiento de los períodos de retención de 24 horas (valor predeterminado), incluso aunque la unidad de procesamiento se consuma hasta la asignación de entrada máxima.

## ¿Cuál es el período de retención máximo?

El nivel Standard de los Centros de eventos admite actualmente un período de retención máximo de 7 días. Tenga en cuenta que los Centros de eventos no están concebidos como un almacén de datos permanentes. Los períodos de retención superiores a 24 horas están pensados para escenarios en los que es útil volver a reproducir un flujo de eventos en los mismos sistemas; por ejemplo, para entrenar o comprobar un nuevo modelo de aprendizaje automático con datos existentes.

## ¿Cómo se calcula y se cobra el tamaño de almacenamiento de los Centros de eventos?

El tamaño total de todos los eventos almacenados, incluida la sobrecarga interna de encabezados de eventos o las estructuras de almacenamiento en disco de todos los Centros de eventos, se mide a lo largo del día. Al final del día, se calcula el tamaño máximo de almacenamiento. La asignación de almacenamiento diario se calcula basándose en el número mínimo de unidades de procesamiento seleccionadas durante el día (cada unidad de procesamiento ofrece una asignación de 84 GB). Si el tamaño total supera la asignación de almacenamiento diaria calculada, el exceso de almacenamiento se factura con las tarifas de almacenamiento de blobs de Azure (con la tarifa de **almacenamiento con redundancia local**).

## ¿Puedo usar una conexión AMQP única para enviar y recibir desde los Centros de eventos y los temas y colas del Bus de servicio?

Sí, siempre y cuando todos los Centros de eventos, colas y temas se encuentren en el mismo espacio de nombres del Bus de servicio. Como tal, puede implementar una conectividad desacoplada e indirecta a muchos dispositivos, con latencias de fracciones de segundos, de manera rentable y altamente escalable.

## ¿Los cargos por conexión desacoplada se aplican a los Centros de eventos?

Para los remitentes, los cargos de conexión se aplican solo cuando se usa el protocolo AMQP. No hay ningún cargo de conexión por el envío de eventos mediante HTTP, independientemente del número de sistemas o dispositivos emisores. Si tiene previsto usar AMQP (por ejemplo, para conseguir un flujo de eventos más eficiente o para habilitar la comunicación bidireccional en escenarios de comando y control de Internet de las cosas), consulte la página de [información de precios del Bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/) para obtener información sobre lo que constituye una conexión desacoplada y cómo se mide.

## ¿Cuál es la diferencia entre los niveles Basic y Standard de los Centros de eventos?

El nivel Standard de los Centros de eventos ofrece características que van más allá de lo que está disponible en Basic, así como en algunos sistemas competitivos. Estas características incluyen períodos de retención superiores a 24 horas y la capacidad para usar una conexión AMQP única para enviar comandos a un gran número de dispositivos con latencias de fracciones de segundos, así como para enviar telemetría desde estos dispositivos a Centros de eventos. Para obtener la lista de características, vea los [Detalles de precios de Centros de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

## Disponibilidad geográfica

Los Centros de eventos están disponibles en las siguientes regiones:

|Geoárea|Regiones|
|---|---|
|Estados Unidos|Centro de EE. UU., Este de EE.UU., Este de EE.UU. 2, Centro y sur de EE.UU. y Oeste de EE.UU.|
|Europa|Norte de Europa y Oeste de Europa|
|Asia Pacífico|Este de Asia y Sudeste de Asia|
|Japón|Este de Japón, Oeste de Japón|
|Brasil|Sur de Brasil|
|Australia|Este de Australia, Sudeste de Australia|

## SLA y soporte técnico

El soporte técnico para los Centros de eventos está disponible a través de los [foros de la comunidad](https://social.msdn.microsoft.com/forums/azure/home). Se ofrece de forma gratuita soporte técnico para la administración de suscripciones y la facturación.

Para obtener más información sobre nuestro SLA, visite la página [Acuerdos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/).

## Pasos siguientes

Para obtener más información sobre los Centros de eventos, consulte los siguientes artículos:

- [Información general de los Centros de eventos]
- Una [aplicación de ejemplo completa que usa Centros de eventos].
- Una [solución de mensajería en cola] mediante las colas de Bus de servicio.

[Información general de los Centros de eventos]: event-hubs-overview.md
[aplicación de ejemplo completa que usa Centros de eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[solución de mensajería en cola]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

<!---HONumber=AcomDC_0128_2016-->