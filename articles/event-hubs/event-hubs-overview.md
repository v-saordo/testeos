<properties 
   pageTitle="Información general de los Centros de eventos de Azure | Microsoft Azure"
   description="Introducción e información general sobre los Centros de eventos de Azure."
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="sethm" />

# Información general de los Centros de eventos de Azure

Muchas soluciones modernas están destinadas a ofrecer experiencias de cliente adaptables mejorar los productos a través de comentarios continuos y telemetría automatizada. Estas soluciones se enfrentan al desafío de cómo procesar de forma segura y confiable grandes cantidades de información de muchos publicadores simultáneos. Centros de eventos de Microsoft Azure es un servicio de plataforma administrada que ofrece una base para el consumo de datos a gran escala en una amplia variedad de escenarios. Algunos ejemplos de estos escenarios son el seguimiento del comportamiento en aplicaciones móviles, la información sobre el tráfico de granjas de servidores web, la captura de eventos en el juego de juegos de consola o los datos de telemetría recopilados de máquinas industriales o vehículos conectados. La función habitual que Centros de eventos desempeña en las arquitecturas de las soluciones es que actúan como la "puerta principal" de una canalización de eventos, conocida a menudo como un *consumidor de eventos*. Un consumidor de eventos es un componente o servicio que se encuentra entre los publicadores de eventos y los consumidores de eventos para desacoplar la producción de un flujo de eventos del consumo de esos eventos.

![Centros de eventos](./media/event-hubs-overview/IC759856.png)

Centros de eventos de Azure es un servicio de procesamiento de eventos que ofrece entrada de telemetría y eventos en la nube a escala masiva, con una latencia baja y una confiabilidad alta. Este servicio, que se usa con otros servicios del flujo de trabajo, es especialmente útil en escenarios de Internet de las cosas, procesamiento del flujo de trabajo o de la experiencia del usuario y en instrumentación de aplicaciones. Centros de eventos ofrece una funcionalidad de control de secuencias de mensajes y, aunque un Centro de eventos es una entidad similar a los temas y las colas, tiene características que son muy diferentes de la mensajería empresarial tradicional. Los escenarios de mensajería empresarial normalmente requieren un número de capacidades sofisticadas, como la secuenciación, las colas de mensajes fallidos, la compatibilidad con las transacciones y las garantías de entrega segura, mientras que la preocupación principal sobre el consumo de eventos es el alto procesamiento y la flexibilidad de procesamiento de los flujos de eventos. Por lo tanto, las capacidades de Centros de eventos de Azure se diferencian de las de los temas del Bus de servicio en que están fuertemente orientadas a escenarios de alto rendimiento y procesamiento de eventos. En este sentido, los Centros de eventos no implementan algunas de las capacidades de mensajería que están disponibles para los temas. Si necesita esas capacidades, los temas siguen siendo la opción óptima.

Un Centro de eventos se crea en el nivel de espacio de nombres en el Bus de servicio, de forma similar a las colas y los temas. Centros de eventos usa HTTP y AMQP como sus interfaces API principales. En el diagrama siguiente se muestra la relación entre los Centros de eventos y el Bus de servicio.

![Centros de eventos](./media/event-hubs-overview/IC741188.png)

## Información general conceptual

Centros de eventos ofrece un flujo de mensajes a través de un patrón de consumidores con particiones. Las colas y los temas usan un modelo de [consumidores paralelos](https://msdn.microsoft.com/library/dn568101.aspx) en el que cada consumidor intenta leer desde la misma cola o el mismo recurso. Esta competición por los recursos resulta finalmente en complejidad y límites de escalabilidad para las aplicaciones de procesamiento de flujos. Centros de eventos usa un patrón de consumidor con particiones en el que cada consumidor lee solo un subconjunto específico o una partición del flujo de mensajes. Este patrón permite un escalado horizontal para el procesamiento de eventos y ofrece otras características centradas en los flujos que no están disponibles en las colas y los temas.

### Particiones

Una partición es una secuencia ordenada de eventos que se mantiene en un Centro de eventos. A medida que llegan eventos más recientes, se agregan al final de esta secuencia. Una partición puede considerarse como un "registro de confirmación".

![Centros de eventos](./media/event-hubs-overview/IC759857.png)

Las particiones retienen datos durante un tiempo de retención configurado que se establece a nivel del Centro de eventos. Esta configuración se aplica en todas las particiones del Centro de eventos. Los eventos expiran en función del tiempo; no se pueden eliminar explícitamente. Un Centro de eventos contiene varias particiones. Cada partición es independiente y contiene su propia secuencia de datos. Como resultado, las particiones a menudo crecen a ritmos distintos.

![Centros de eventos](./media/event-hubs-overview/IC759858.png)

El número de particiones se especifica en el momento de la creación del Centro de eventos y debe estar comprendido entre 2 y 32 (el valor predeterminado es 4). Las particiones son un mecanismo de organización de datos y están más relacionadas con el grado de paralelismo de bajada necesario para consumir las aplicaciones que con el procesamiento de los Centros de eventos. Esto hace que la elección del número de particiones en un Centro de eventos esté directamente relacionada con el número de lectores simultáneos que espera tener. Tras la creación del Centro de eventos, el recuento de particiones no es modificable; debe considerar este número en función de la escala esperada a largo plazo. Puede aumentar el límite de 32 particiones si se pone en contacto con el equipo del Bus de servicio de Azure.

Aunque las particiones son identificables y se pueden enviar directamente a ellas, normalmente es preferible evitar el envío de datos a particiones concretas. En su lugar, puede usar construcciones de nivel superior que se presentan en las secciones [Publicador de eventos](#event-publisher) y [Directiva del publicador](#capacity-and-security).

En el contexto de los Centros de eventos, los mensajes se conocen como *datos de eventos*. Los datos de eventos contienen el cuerpo del evento, un contenedor de propiedades definido por el usuario y diversos metadatos sobre el evento, como su desplazamiento en la partición y su número en el flujo de la secuencia. Las particiones se rellenan con una secuencia de datos de eventos.

## Publicador de eventos

Cualquier entidad que envíe eventos o datos a un Centro de eventos es un *publicador de eventos*. Los publicadores de eventos pueden publicar eventos mediante HTTPS o AMQP 1.0. Los publicadores de eventos usan un token de firma de acceso compartido (SAS) para identificarse en un Centro de eventos y, según los requisitos del escenario, pueden tener una identidad única o usar un token de SAS común.

Para obtener más información sobre el funcionamiento con SAS, consulte [Autenticación con firma de acceso compartido en Bus de servicio](../service-bus/service-bus-shared-access-signature-authentication.md).

### Tareas comunes del publicador

En esta sección se describen tareas comunes de los publicadores de eventos.

#### Adquisición de un token de SAS

La firma de acceso compartido (SAS) es el mecanismo de autenticación de los Centros de eventos. El Bus de servicio ofrece directivas SAS a nivel de Centro de eventos y de espacio de nombres. Un token de SAS se genera a partir de una clave de SAS y es un hash SHA de una dirección URL, codificado en un formato concreto. Con el nombre de la clave (directiva) y el token, el Bus de servicio puede volver a generar el hash y así autenticar al remitente. Normalmente, los tokens de SAS para publicadores de eventos se crean solo con privilegios de **envío** en un Centro de eventos concreto. Este mecanismo de dirección URL del token de SAS es la base para la identificación del publicador introducida en la directiva del publicador. Para obtener más información sobre el funcionamiento con SAS, consulte [Autenticación con firma de acceso compartido en Bus de servicio](../service-bus/service-bus-shared-access-signature-authentication.md).

#### Publicación de un evento

Puede publicar un evento a través de AMQP 1.0 o HTTPS. El Bus de servicio ofrece una clase [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx) para publicar los eventos en un Centro de eventos de clientes .NET. Para otras plataformas y tiempos de ejecución, puede usar cualquier cliente de AMQP 1.0, como [Apache Qpid](http://qpid.apache.org/). Puede publicar eventos individualmente o por lotes. Una sola publicación (instancia de datos de eventos) tiene un límite de 256 KB, independientemente de si es un evento único o un lote. La publicación de eventos mayores producirá un error. Es una práctica recomendada que los publicadores desconozcan las particiones en el Centro de eventos y solo especifiquen una *clave de partición* (que se presenta en la sección siguiente) o su identidad mediante su token de SAS.

La opción de usar AMQP o HTTPS es específica para el escenario de uso. AMQP requiere el establecimiento de un socket bidireccional persistente, además de la seguridad de nivel de transporte (TLS) o SSL/TLS. Esto puede ser una operación costosa en términos de tráfico de red, pero solo se produce al comienzo de una sesión AMQP. HTTPS tiene una sobrecarga inicial menor, pero requiere sobrecarga SSL adicional por cada solicitud. Para los publicadores que publican frecuentemente los eventos, AMQP ofrece un ahorro considerable de rendimiento, latencia y procesamiento.

### Clave de partición

Una clave de partición es un valor que se usa para asignar datos de eventos entrantes a particiones concretas con fines de organización de los datos. La clave de partición es un valor proporcionado por el remitente que se pasa a un Centro de eventos. Se procesa a través de una función hash estática, cuyo resultado crea la asignación de la partición. Si no especifica una clave de partición cuando se publica un evento, se usa una asignación de tipo round robin. Al usar claves de partición, el publicador de eventos solo conoce su clave de partición, no la partición en la que se publican los eventos. Este desacoplamiento de clave y la partición evita al remitente la necesidad de conocer demasiado sobre el procesamiento de bajada y el almacenamiento de eventos. Las claves de partición son importantes para organizar los datos para el procesamiento de bajada, pero no están relacionadas fundamentalmente con las propias particiones. Una identidad única por cada dispositivo o usuario es una buena clave de partición, pero otros atributos como la geografía también pueden usarse para agrupar eventos relacionados en una única partición. En la siguiente imagen se muestran remitentes de eventos que usan claves de partición para anclarse a particiones.

![Centros de eventos](./media/event-hubs-overview/IC759859.png)

Centros de eventos de Azure garantiza que todos los eventos que comparten el mismo valor de clave de partición se entregan por orden y en la misma partición. Lo importante es que, si se usan claves de partición con directivas de publicador (que se describen en la siguiente sección), la identidad del publicador y el valor de la clave de partición deben coincidir. De lo contrario, se produce un error.

### Consumidor de eventos

Cualquier entidad que lea datos de eventos de un Centro de eventos es un consumidor de eventos. Todos los consumidores de eventos leen el flujo de eventos a través de las particiones de un grupo de consumidores. Cada partición debe tener solo un lector activo a la vez. Todos los consumidores de los Centros de eventos se conectan a través de la sesión de AMQP 1.0, en la que los eventos se entregan a medida que están disponibles. El cliente no necesita realizar un sondeo de disponibilidad de los datos.

#### Grupos de consumidores

El mecanismo de publicación y suscripción de los Centros de eventos se habilita a través de los grupos de consumidores. Un grupo de consumidores es una vista (estado, posición o desplazamiento) de un Centro de eventos completo. Los grupos de consumidores habilitan varias aplicaciones consumidoras para que cada una tenga una vista separada del flujo de eventos y para que lean el flujo de forma independiente a su propio ritmo y con sus propios desplazamientos. En una arquitectura de procesamiento de flujos, cada aplicación de bajada se corresponde con un grupo de consumidores. Si quiere escribir datos de eventos para el almacenamiento a largo plazo, esa aplicación de escritura de almacenamiento es un grupo de consumidores. Otro grupo de consumidores independiente realiza el procesamiento de eventos complejos. Solo puede obtener acceso a las particiones a través de un grupo de consumidores. Siempre hay un grupo de consumidores predeterminado en un Centro de eventos y pueden crearse hasta 20 grupos de consumidores para un Centro de eventos de nivel Standard.

A continuación se muestran ejemplos de la convención URI del grupo de consumidores:

	//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
	//<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>

En la siguiente imagen se muestran los consumidores de eventos dentro de los grupos de consumidores.

![Centros de eventos](./media/event-hubs-overview/IC759860.png)

#### Desplazamientos de los flujos

Un desplazamiento es la posición de un evento dentro de una partición. Puede pensar en un desplazamiento como un cursor de lado cliente. El desplazamiento es una numeración de byte del evento. Esto permite que un consumidor de eventos (lector) especifique un punto en el flujo de eventos desde el que quiere empezar a leer los eventos. Puede especificar el desplazamiento como una marca de tiempo o como un valor de desplazamiento. Los consumidores son responsables de almacenar sus propios valores de desplazamiento fuera del servicio de los Centros de eventos.

![Centros de eventos](./media/event-hubs-overview/IC759861.png)

Dentro de una partición, cada evento incluye un desplazamiento. Los consumidores usan este desplazamiento para mostrar la ubicación en la secuencia de eventos de una partición determinada. Los desplazamientos pueden pasarse al Centro de eventos como número o valor de marca de tiempo cuando se conecta un lector.

#### Puntos de control

La *creación de puntos de comprobación* es un proceso en el que los lectores marcan o confirman su posición dentro de la secuencia de eventos de una partición. La creación de puntos de comprobación es responsabilidad del consumidor y se realiza por partición dentro de un grupo de consumidores. Esto significa que por cada grupo de consumidores, cada lector de la partición debe realizar un seguimiento de su posición actual en el flujo del evento y puede informar al servicio cuando considere que el flujo de datos se ha completado. Si se desconecta un lector de una partición, cuando se vuelve a conectar comienza a leer en el punto de comprobación que envió previamente el último lector de esa partición en ese grupo de consumidores. Cuando se conecta, el lector pasa este desplazamiento al Centro de eventos para especificar la ubicación en la que se empieza a leer. De este modo, puede usar puntos de comprobación para marcar eventos como "completados" por las aplicaciones de bajada y para ofrecer resistencia en caso de una conmutación por error entre lectores que se ejecutan en máquinas distintas. Dado que los datos de eventos se conservan durante el intervalo de retención especificado en el momento en que se crea el Centro de eventos, es posible volver a los datos más antiguos si se especifica un desplazamiento inferior de este proceso de puntos de comprobación. Mediante este mecanismo, los puntos de comprobación permiten una resistencia a la conmutación por error y una reproducción controlada del flujo de eventos.

#### Tareas comunes del consumidor

En esta sección se describen tareas comunes para los lectores o consumidores de eventos de los Centros de eventos. Todos los consumidores de los Centros de eventos se conectan a través de AMQP 1.0. AMQP 1.0 es un canal de comunicación bidireccional con estado y sesión. Cada partición tiene una sesión de vínculo AMQP 1.0 que facilita el transporte de eventos que deben separarse por partición.

##### Conexión a una partición

Para consumir eventos de un Centro de eventos, un consumidor debe conectarse a una partición. Como se mencionó anteriormente, siempre se obtiene acceso a las particiones a través de un grupo de consumidores. Como parte del modelo de consumidor con particiones, solo debe estar activo un único lector a la vez en una partición dentro de un grupo de consumidores. Es una práctica habitual al conectarse directamente a particiones usar un mecanismo de concesiones para coordinar las conexiones de lector para particiones concretas. De este modo, es posible que cada partición de un grupo de consumidores solo tenga un lector activo. La administración de la posición en la secuencia de un lector es una tarea importante que se logra mediante los puntos de comprobación. Esta funcionalidad se simplifica mediante el uso de la clase [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) para los clientes de .NET. [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) es un agente de consumidor inteligente que se describe en la sección siguiente.

##### Lectura de eventos

Después de abrir una sesión de AMQP 1.0 y el vínculo de una partición específica, el servicio de Centros de eventos entrega los eventos al cliente de AMQP 1.0. Este mecanismo de entrega permite un mayor procesamiento y una menor latencia que los mecanismos basados en extracción como HTTP GET. Los eventos se envían al cliente, cada instancia de datos de eventos contiene metadatos importantes, como el número de secuencia y el desplazamiento que se usan para facilitar la creación de puntos de comprobación en la secuencia de eventos.

![Centros de eventos](./media/event-hubs-overview/IC759862.png)

Es responsabilidad del usuario la administración de este desplazamiento de la manera que mejor habilite la administración del progreso en el procesamiento del flujo.

## Capacidad y seguridad

Centros de eventos es una arquitectura paralela altamente escalable para la entrada de flujos. Por tanto, hay varios aspectos clave que se deben tener en cuenta al ajustar el tamaño y realizar el escalado de una solución basada en Centros de eventos. El primero de estos controles de capacidad son las *unidades de procesamiento*, que se describen en la sección siguiente.

### Unidades de procesamiento

La capacidad de procesamiento de los Centros de eventos se controla mediante unidades de procesamiento. Las unidades de procesamiento son unidades de capacidad adquiridas previamente. Una unidad de procesamiento individual incluye lo siguiente:

- Entrada: hasta 1 MB por segundo o 1000 eventos por segundo.

- Salida: hasta 2 MB por segundo.

La entrada está limitada a la cantidad de capacidad que ofrece el número de unidades de procesamiento adquiridas. El envío de datos por encima de esta cantidad provoca una excepción de "cuota superada". Esta cantidad es de 1 MB por segundo o 1000 eventos por segundo, lo que ocurra primero. La salida no produce excepciones de limitación, pero está limitada a la cantidad de transferencia de datos que ofrecen las unidades de procesamiento adquiridas: 2 MB por segundo por unidad de procesamiento. Si recibe excepciones de tasa de publicación o espera ver una salida superior, compruebe cuántas unidades de procesamiento adquirió para el espacio de nombres en que se creó el Centro de eventos. Para obtener más unidades de procesamiento, puede ajustar la configuración en la página **Espacios de nombres**, en la pestaña **Escala** del [Portal de Azure clásico][]. También puede cambiar esta configuración mediante las API de Azure.

Aunque las particiones son un concepto de organización de datos, las unidades de procesamiento son puramente un concepto de capacidad. Las unidades de procesamiento se facturan por hora y se adquieren previamente. Cuando se adquieren, las unidades de procesamiento se facturan durante un período mínimo de una hora. Se pueden adquirir hasta 20 unidades de procesamiento para un espacio de nombres del Bus de servicio y hay un límite de cuenta de Azure de 20 unidades de procesamiento. Estas unidades de procesamiento se comparten entre todos los Centros de eventos de un espacio de nombres determinado.

Las unidades de procesamiento se aprovisionan en base al mejor esfuerzo y puede que no siempre estén disponibles para su compra inmediata. Si necesita una capacidad específica, se recomienda que adquiera esas unidades de procesamiento con antelación. Si necesita más de 20 unidades de procesamiento, puede ponerse en contacto con el soporte técnico del Bus de servicio para comprar más unidades de procesamiento por bloques de 20, hasta un total de 100 unidades de procesamiento iniciales. A partir de ahí, también puede adquirir bloques de 100 unidades de procesamiento.

Se recomienda que equilibre cuidadosamente las particiones y las unidades de procesamiento para lograr una escalabilidad óptima con los Centros de eventos. Una sola partición tiene una escala máxima de una unidad de procesamiento. El número de unidades de procesamiento debe ser menor o igual que el número de particiones de un Centro de eventos.

Para obtener información detallada sobre los precios, consulte [Precios de los Centros de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

### Directiva del publicador

Los Centros de eventos permiten un control granular sobre los publicadores de eventos a través de las *directivas de publicador*. Las directivas de publicador son un conjunto de características de tiempo de ejecución diseñadas para facilitar grandes números de publicadores de eventos independientes. Con las directivas de publicador, cada publicador usa su propio identificador único al publicar los eventos en un Centro de eventos mediante el mecanismo siguiente:

	//<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>

No tiene que crear nombres de publicador con antelación, pero deben coincidir con el token de SAS que se usa al publicar un evento, con el fin de garantizar las identidades de publicador independientes. Para obtener más información sobre SAS, consulte [Autenticación con firma de acceso compartido en Bus de servicio](../service-bus/service-bus-shared-access-signature-authentication.md). Al usar directivas de publicador, el valor **PartitionKey** se establece como el nombre del publicador. Para que funcione correctamente, estos valores deben coincidir.

## Resumen

Centros de eventos Azure ofrece un servicio de procesamiento de eventos de gran escala y telemetría que se puede usar para la supervisión del flujo de trabajo de usuarios y aplicaciones comunes a cualquier escala. Con la capacidad para ofrecer capacidades de publicación y suscripción con una latencia baja y a gran escala, los Centros de eventos sirven como una "vía de entrada" para los datos de gran tamaño. Con la identidad basada en el publicador y las listas de revocación, estas capacidades se extienden en escenarios comunes de Internet de las cosas. Para obtener más información sobre cómo desarrollar aplicaciones de Centros de eventos, vea la [Guía de programación de Centros de eventos](event-hubs-programming-guide.md).

## Pasos siguientes

Ahora que ha aprendido conceptos sobre los Centros de eventos, puede continuar con los siguientes escenarios:

- Empezar a trabajar con un [tutorial de Centros de eventos].
- Una [aplicación de ejemplo completa que usa Centros de eventos].
- Una [solución de mensajería en cola] mediante las colas de Bus de servicio.

[Portal de Azure clásico]: http://manage.windowsazure.com
[tutorial de Centros de eventos]: event-hubs-csharp-ephcs-getstarted.md
[aplicación de ejemplo completa que usa Centros de eventos]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[solución de mensajería en cola]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0218_2016-->