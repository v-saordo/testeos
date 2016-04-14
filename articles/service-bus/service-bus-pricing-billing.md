<properties 
   pageTitle="Precios y facturación de Bus de servicio | Microsoft Azure"
   description="Información general de la estructura de precios de Bus de servicio."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/28/2015"
   ms.author="sethm" />

# Precios y facturación de Bus de servicio

Bus de servicio está disponible en los niveles Básico, Estándar y [Premium](service-bus-premium-messaging.md). Puede elegir un nivel de servicio para cada espacio de nombres del servicio Bus de servicio que cree y esta selección de nivel se aplica a todas las colas, temas/suscripciones, retransmisiones y Centros de eventos creados dentro de ese espacio de nombres.

>[AZURE.NOTE]Para más información sobre los precios actuales de Bus de servicio, consulte las [P+F de precios de Bus de servicio](service-bus-pricing-faq.md).

Bus de servicio usa los siguientes dos medidores para colas y temas/suscripciones:

1. **Operaciones de mensajería**: definidas como llamadas de API contra extremos de servicio de temas/suscripciones o cola. Este medidor reemplazará los mensajes enviados o recibidos como unidad primaria de uso facturable para las colas y los temas/suscripciones.

2. **Conexiones asíncronas**: definidas como el número máximo de conexiones persistentes abiertas contra colas, temas/suscripciones o Centros de eventos durante un periodo de prueba de una hora determinado. Este medidor solo se aplica en el nivel Estándar, en el que puede abrir conexiones adicionales (anteriormente, las conexiones estaban limitadas a 100 por cola/tema/suscripción) por un cargo por conexión simbólico.

El nivel **Estándar** presenta precios escalonados para operaciones con colas y temas/suscripciones, ofreciendo descuentos basados en volumen de hasta el 80 % para los niveles de uso más elevados. También hay un cargo base para el nivel Estándar de 10 $ al mes, que le permite realizar hasta 12,5 millones de operaciones al mes sin ningún costo adicional.

El nivel **Premium** proporciona aislamiento de recursos en el nivel de CPU y memoria para que cada carga de trabajo de cliente se ejecute de forma aislada. Este contenedor de recursos se llama *unidad de mensajería*. A cada espacio de nombres premium se le asigna al menos una unidad de mensajería. Puede comprar 1, 2 o 4 unidades de mensajería para cada espacio de nombres Premium del Bus de servicio. Una sola carga de trabajo o entidad puede abarcar varias unidades de mensajería y el número de unidades de mensajería puede cambiarse a voluntad, aunque la facturación se realiza con base en una tarificación diaria o de 24 horas. El resultado es un rendimiento predecible y repetible para su solución basada en el Bus de servicio. Este rendimiento no es solo más predecible y presenta mayor disponibilidad, sino que también es más rápido. La mensajería Premium de Bus de servicio de Azure se basa en el motor de almacenamiento presentado en los Centros de eventos de Azure. Con la mensajería Premium, obtener el máximo rendimiento es mucho más rápido que en el nivel Estándar.

Tenga en cuenta que el cargo base estándar de la base se cobra solo una vez al mes por suscripción de Azure. Esto significa que cuando cree un espacio de nombres de Bus de servicio de nivel Estándar o Premium, podrá crear tantos espacios de nombre de nivel Estándar o Premium como quiera dentro de esa misma suscripción de Azure, sin incurrir en cargos base adicionales.

Todos los espacios de nombres de Bus de servicio existentes creados antes del 1 de noviembre de 2014 pasarán automáticamente a formar parte del nivel Estándar. Esto garantiza que continuará teniendo acceso a todas las características disponibles actualmente con Bus de servicio. Posteriormente, puede usar el [Portal de Azure clásico][] para cambiar al nivel Básico, si lo desea.

En la tabla siguiente se resumen las diferencias funcionales entre los niveles Básico, Estándar y Premium.

|Capacidad|Básica|Estándar/Premium|
|---|---|---|
|Centros de eventos|Sí|Sí|
|Colas|Sí|Sí|
|Mensajes programados|Sí|Sí|
|Temas/suscripciones|No|Sí|
|Relés|No|Sí|
|Transacciones|No|Sí|
|Desduplicación|No|Sí|
|Sesiones|No|Sí|
|Mensajes grandes|No|Sí|
|ForwardTo|No|Sí|
|SendVia|No|Sí|
|Conexiones asíncronas (incluidas)|100 por cada espacio de nombres de Bus de servicio|1\.000 por cada suscripción de Azure|
|Conexiones asíncronas (se permite el uso por encima del límite)|No|SÍ (facturable)|

## Operaciones de mensajería

Como parte del nuevo modelo de precios, facturación de las colas y los temas/suscripciones está cambiando. Estas entidades están pasando de una facturación por mensaje a una facturación por operación. Una "operación" se refiere a cualquier llamada de API que se realiza contra un extremo de servicios de cola o temas/suscripciones. Esto incluye las operaciones de administración, envío/recepción y estado de la sesión.

|Tipo de operación|Descripción|
|---|---|
|Administración|Creación, lectura, actualización, eliminación (CRUD) contra colas o temas/suscripciones.|
|Mensajería|Envío y recepción de mensajes con colas o con temas/suscripciones.|
|Estado de la sesión|Recuperación o establecimiento del estado de la sesión en una cola o un tema/una suscripción.|

Los siguientes precios están vigentes desde el 1 de noviembre de 2014:

|Básica|Coste|
|---|---|
|Operaciones|0,05 $ por cada millón de operaciones|

|Estándar|Coste|
|---|---|
|Cargo base|10 $/mes|
|Los 12,5 millones de operaciones primeras al mes|Se incluye|
|De 12,5 a 100 millones de operaciones en un mes|0,80 $ por cada millón de operaciones|
|De 100 a 2.500 millones de operaciones en un mes|0,50 $ por cada millón de operaciones|
|Por encima de 2.500 millones de operaciones en un mes|0,20 $ por cada millón de operaciones|

>[AZURE.NOTE]El nivel Premium se encuentra actualmente en vista previa y el precio siguiente refleja un descuento del 50 % en la versión de vista previa.

|Premium|Coste|
|---|---|
|Diario|11,13 $ de tarifa fija por unidad de mensaje|

## Conexiones asincrónicas

Las *conexiones asincrónicas* se adaptan a los patrones de uso de los clientes que implican una gran cantidad de remitentes/receptores "conectados de forma persistente" contra colas, temas/suscripciones o Centros de eventos. Los remitentes/receptores conectados de forma persistente son aquellos que se conectan mediante AMQP o HTTP con un tiempo de expiración de recepción distinto a cero (por ejemplo, HTTP long polling). Los remitentes y receptores con un tiempo de expiración inmediato no generarán conexiones asíncronas.

Anteriormente, las colas y los temas/las suscripciones tenían un límite de 100 conexiones concurrentes por URL. El esquema de facturación actual quita el límite por URL de las colas y los temas/las suscripciones, e implementa cuotas y mediciones en las conexiones asíncronas en los niveles de suscripción de Azure y el espacio de nombres de Bus de servicio.

El nivel Básico incluye 100 conexiones asíncronas (y está estrictamente limitado a ellas) por cada espacio de nombres de Bus de servicio. Las conexiones por encima de este número se rechazarán en el nivel Básico. El nivel Estándar elimina el límite por espacio de nombres y cuenta el uso por conexión asíncrona total en toda la suscripción de Azure. En el nivel Estándar, se permiten 1.000 conexiones asíncronas por cada suscripción de Azure sin costo adicional (más allá del cargo base). Si se usa un total de más de 1.000 conexiones asíncronas a lo largo de los espacios de nombres de Bus de servicio de nivel Estándar en una suscripción de Azure, se facturará de forma escalonada, como se muestra en la siguiente tabla.

|Conexiones asíncronas (nivel Estándar)|Coste|
|---|---|
|Primeras 1.000 del mes|Incluidas con el cargo base|
|De 1.000 a 100.000 en un mes|0,03 $ por conexión y mes|
|De 100.000 a 500.000 en un mes|0,025 $ por conexión y mes|
|Más de 500.000 en un mes|0,015 $ por conexión y mes|

>[AZURE.NOTE]Se incluyen 1.000 conexiones asíncronas con el nivel de mensajería Estándar (en el cargo base) y se pueden compartir entre todas las colas, los temas/las suscripciones y los Centros de eventos dentro de la suscripción de Azure asociada.

>[AZURE.NOTE]La facturación se basa en el número máximo de conexiones concurrentes y se prorratea por horas, basándose en 744 horas al mes.

|Nivel Premium
|---|
|No se cobran las conexiones asíncronas en el nivel Premium.|

Para obtener más información sobre las conexiones asincrónicas, consulte la sección [Preguntas más frecuentes](#FAQ) más adelante en este tema.

## Retransmisión

Las retransmisiones solo están disponibles en los espacios de nombres de nivel Estándar. En cualquier otro caso, el precio y las cuotas de conexión de las retransmisiones permanecen igual. Esto significa que las retransmisiones seguirán cobrándose según el número de mensajes (y no de operaciones) y las horas de retransmisión.

|Precios de retransmisión|Coste|
|---|---|
|Horas de retransmisión|0,10 $ por cada 100 horas de retransmisión|
|error de Hadoop|0,01 $ por cada 10.000 mensajes|

## P+F

### ¿Cómo se calcula el contador de horas de retransmisión?

Consulte [este tema](service-bus-pricing-faq.md#How-is-the-Relay-Hours-meter-calculated?).

### ¿Qué son las conexiones asíncronas y cómo se cobran?

Una conexión asíncrona se define como una de estas dos posibilidades:

1. Una conexión AMQP desde un cliente a un tema/suscripción, cola o un Centro de eventos de Bus de servicio.

2. Una llamada HTTP para recibir un mensaje desde un tema o cola de Bus de servicio que tiene un valor para el tiempo de expiración de recepción mayor que cero.

Bus de servicio cobra el número máximo de conexiones asíncronas concurrentes que superen la cantidad incluida (1.000 en el nivel Estándar). Los valores máximos se miden cada hora, que se prorratea dividiéndolos por 744 horas en un mes y sumándolos al periodo de facturación mensual. La cantidad incluida (1.000 conexiones asíncronas al mes) se aplica al final del periodo de facturación contra la suma de los máximos de horas prorrateados.

Por ejemplo:

1. 10\.000 dispositivos se conectan mediante una conexión AMQP única y reciben comandos desde un tema de Bus de servicio. Los dispositivos envían eventos de telemetría a un Centro de eventos. Si todos los dispositivos se conectan durante 12 horas cada día, se aplican los siguientes cargos por conexión (sumados a cualquier otro cargo de temas de Bus de servicio): 10.000 conexiones * 12 horas * 31 días / 744 = 5.000 conexiones asíncronas. Después de permitir la conexión mensual de 1.000 conexiones asíncronas, se le cobrarían 4.000 conexiones asíncronas, con un importe de 0,03 $ por cada conexión asíncrona, que daría un total de 120 $.

2. 10\.000 dispositivos reciben mensajes desde una cola de Bus de servicio mediante HTTP, especificando un tiempo de expiración distinto a cero. Si todos los dispositivos se conectan durante 12 horas cada día, se aplican los siguientes cargos por conexión (sumados a cualquier otro cargo de Bus de servicio): 10.000 conexiones de recepción HTTP * 12 horas al día * 31 días / 744 horas = 5.000 conexiones asíncronas.

### ¿Se aplican los cargos por conexión asíncrona a las colas y los temas/las suscripciones?

Sí. No hay ningún cargo de conexión por el envío de eventos mediante HTTP, independientemente del número de sistemas o dispositivos emisores. La recepción de eventos con HTTP con un tiempo de expiración superior a cero, que a menudo se conoce como "long polling", genera cargos por conexión asíncrona. Las conexiones AMQP generan cargos por conexiones asíncronas, sin importar si las conexiones se usan para enviar o para recibir. Tenga en cuenta que se permiten 100 conexiones asíncronas sin cargo en un espacio de nombres Básico. Este es también el número máximo de conexiones asíncronas permitidas para la suscripción de Azure. Las primeras 1.000 conexiones asíncronas a lo largo de todos los espacios de nombre Estándar en una suscripción de Azure se incluyen sin ningún cargo adicional (más allá del cargo base). Dado que estos números son suficientes para cubrir una gran cantidad de escenarios de mensajería entre servicios, los cargos por conexión asíncrona normalmente solo son relevantes si planea usar AMQP o HTTP long-polling con un gran número de clientes; por ejemplo, para conseguir una mayor eficiencia en el streaming de eventos o para habilitar la comunicación bidireccional con muchos dispositivos o instancias de aplicación.

## Pasos siguientes

Para más información sobre los precios de Bus de servicio, consulte las [P+F de precios de Bus de servicio](service-bus-pricing-faq.md).

[Portal de Azure clásico]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0107_2016-->