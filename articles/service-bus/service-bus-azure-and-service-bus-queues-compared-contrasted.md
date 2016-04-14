<properties 
   pageTitle="Colas de Bus de servicio y Colas de Azure: comparación y diferencias | Microsoft Azure"
   description="Analiza las diferencias y similitudes entre dos tipos de colas que se ofrecen en Azure."
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
   ms.workload="tbd"
   ms.date="11/18/2015"
   ms.author="sethm" />

# Colas de Service Bus y colas de Azure: comparación y diferencias

En este artículo se analizan las diferencias y similitudes entre los dos tipos de colas que ofrece Microsoft Azure en la actualidad: colas de Azure y colas de Bus de servicio. Con esta información, puede comparar y contrastar las tecnologías respectivas y puede tomar una decisión más fundamentada sobre la solución que satisfaga mejor sus necesidades.

## Introducción

Microsoft Azure admite dos tipos de mecanismos de cola: **colas de Azure** y **colas de Service Bus**.

**Colas de Azure**, que forman parte de la infraestructura de [almacenamiento de Azure](https://azure.microsoft.com/services/storage/), ofrecen una interfaz de Get/Put/Peek sencilla basada en REST, que ofrece una mensajería confiable y persistente dentro de los servicios y entre ellos.

Las **colas de Service Bus** forman parte de una infraestructura de [mensajería de Azure](https://azure.microsoft.com/services/service-bus/) más amplia que admite la puesta en cola, así como la publicación/suscripción, la comunicación remota de servicio web y los patrones de integración. Para obtener más información sobre las colas de Bus de servicio, temas/suscripciones y retransmisiones, consulte [Introducción de la mensajería del Bus de servicio](service-bus-messaging-overview.md).

Aunque ambas tecnologías de cola existen de manera simultánea, las colas de Azure se presentaron en primer lugar, como un mecanismo de almacenamiento de cola dedicado creado a partir de los servicios de almacenamiento de Azure. Las colas de Service Bus se generan además de la infraestructura de "mensajería asíncrona" más amplia diseñada para integrar aplicaciones o componentes de aplicación que pueden abarcar varios protocolos de comunicación, contratos de datos, dominios de confianza y/o entornos de red.

En este artículo se comparan las tecnologías de dos colas ofrecidas por Azure analizando las diferencias en el comportamiento y la implementación de las características proporcionadas por cada una de ellas. En el artículo también se ofrecen instrucciones para elegir las características que pueden satisfacer mejor sus necesidades de desarrollo de aplicaciones.

## Consideraciones de selección de tecnología

Tanto las colas de Azure como las colas de Service Bus son implementaciones del servicio de cola de mensajes ofrecido actualmente en Microsoft Azure. Cada una tiene un conjunto de características ligeramente diferente, lo que significa que puede elegir una u otra, o usar ambas, según las necesidades de su solución concreta o problema empresarial o técnico que va a resolver.

Al determinar qué tecnología de cola se ajusta al propósito de una solución determinada, los desarrolladores y arquitectos de soluciones deben considerar las siguientes recomendaciones. Para obtener información detallada vea la siguiente sección.

Como arquitecto o desarrollador de soluciones, **debe considerar el uso de colas de Azure** cuando:

- Su aplicación debe almacenar más de 80 GB de mensajes en una cola, donde los mensajes tienen una duración inferior a 7 días.

- Su aplicación desea realizar un seguimiento del progreso para procesar un mensaje dentro de la cola. Esto es útil si se bloquea el trabajador que procesa un mensaje. Un trabajador posterior puede usar esa información para continuar desde donde se marchó el trabajador anterior.

- Necesita registros de lado de servidor de todas las transacciones ejecutadas con las colas.

Como arquitecto o desarrollador de soluciones, **debe considerar el uso de colas de Service Bus** cuando:

- Su solución debe poder recibir mensajes sin tener que sondear la cola. Con Service Bus, esto se logra mediante el uso de la operación de recepción de sondeo largo mediante los protocolos basados en TCP que admite Service Bus.

- Su solución requiere que la cola ofrezca una entrega ordenada por primero en entrar es el primero en salir (FIFO).

- Quiere una experiencia simétrica en Azure y en Windows Server (nube privada). Para obtener más información, vea [Service Bus para Windows Server](https://msdn.microsoft.com/library/dn282144.aspx).

- Su solución debe ser capaz de admitir la detección automática de duplicados.

- Quiere que su aplicación procese mensajes como secuencias de larga ejecución en paralelo (los mensajes están asociados a una secuencia mediante la propiedad [SessionId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) del mensaje). En este modelo, cada nodo de la aplicación de consumo compite por secuencias, en lugar de los mensajes. Cuando se proporciona una secuencia a un nodo de consumo, el nodo puede examinar el estado de la secuencia de aplicación mediante transacciones.

- Su solución requiere un comportamiento transaccional y atomicidad al enviar o recibir varios mensajes desde una cola.

- La característica de período de vida (TTL) de la carga de trabajo específica de la aplicación puede superar el período de 7 días.

- Su aplicación administra mensajes que pueden superar los 64 KB pero probablemente no alcanzarán el límite de 256 KB.

- Aborda un requisito para ofrecer un modelo de acceso basado en roles a las colas y diferentes derechos o permisos para los remitentes y receptores.

- El tamaño de la cola no aumentará a más de 80 GB.

- Quiere usar el agente de mensajería basado en estándares AMQP 1.0. Para obtener más información sobre AMQP, vea [Introducción al AMQP de Service Bus](service-bus-amqp-overview.md).

- Puede idear una migración eventual desde la comunicación punto a punto basada en cola a un patrón de intercambio de mensajes que permite la integración perfecta de receptores adicionales (suscriptores), cada uno de los cuales recibe copias independientes de algunos o todos los mensajes enviados a la cola. El segundo se refiere a la capacidad de publicación o suscripción ofrecida por Service Bus de forma nativa.

- La solución de mensajería debe ser capaz de admitir la garantía de entrega "Una vez como máximo" sin necesidad de que compile los componentes de infraestructura adicionales.

- Le gustaría poder publicar y consumir lotes de mensajes.

- Requiere la integración completa con la pila de comunicación de Windows Communication Foundation (WCF) en .NET Framework.

## Comparación de las colas de Azure y colas de Service Bus

En las tablas de las secciones siguientes se ofrece una agrupación lógica de las características de cola y se permite comparar, de un vistazo, las capacidades disponibles tanto en las colas de Azure como en las colas de Service Bus.

## Capacidades fundamentales

En esta sección se comparan algunas de las capacidades de puesta en cola fundamentales ofrecidas por las colas de Azure y las colas de Service Bus.

|Criterios de comparación|Colas de Azure|Colas del Bus de servicio|
|---|---|---|
|Garantía de ordenación|**No** <br/><br>Para obtener más información, vea la primera nota de la sección "Información adicional".</br>|**Sí: primero en entrar, primero en salir (FIFO)**<br/><br>(mediante el uso de las sesiones de mensajería)|
|Garantía de entrega|**Al menos una vez**|**Al menos una vez**<br/><br/>**Una vez como máximo**|
|Compatibilidad con transacciones|**No**|**Sí**<br/><br/>(mediante el uso de transacciones locales)|
|Comportamiento de recepción|**Sin bloqueo**<br/><br/>(se completa inmediatamente si no se encuentra ningún mensaje nuevo)|**Bloque con/sin tiempo de espera**<br/><br/>(ofrece sondeo largo o la ["Técnica de cometa"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Sin bloque**<br/><br/>(solo mediante el uso de la API administrada de .NET)|
|API de estilo de inserción|**No**|**Sí**<br/><br/>[OnMessage](https://msdn.microsoft.com/library/azure/jj908682.aspx) y API. de NET de **sesiones OnMessage**.|
|Modo de recepción|**Ojear y alquilar**|**Ojear y bloquear**<br/><br/>**Recibir y eliminar**|
|Modo de acceso exclusivo|**Basado en concesión**|**Basado en bloqueo**|
|Duración de concesión/bloqueo|**30 segundos (valor predeterminado)**<br/><br/>**7 días (máximo)** (puede renovar o liberar una concesión de mensaje mediante la API de [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx).)|**60 segundos (valor predeterminado)**<br/><br/>Puede renovar un bloqueo de mensaje mediante la API de [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx).|
|Precisión de concesión/bloqueo|**Nivel de mensaje**<br/><br/>(cada mensaje puede tener un valor de tiempo de espera diferente, que luego puede actualizar según sea necesario al procesar el mensaje, mediante el uso de la API de [UpdateMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage.aspx))|**Nivel de cola**<br/><br/>(cada cola tiene una precisión de bloqueo que se aplica a todos sus mensajes, pero puede renovar el bloqueo mediante la API de [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx).)|
|Recepción por lotes|**Sí**<br/><br/>(especificando explícitamente el número de mensajes al recuperar mensajes, hasta un máximo de 32 mensajes)|**Sí**<br/><br/>(habilitando implícitamente una propiedad de captura previa o explícitamente mediante el uso de transacciones)|
|Envío por lotes|**No**|**Sí**<br/><br/>(mediante el uso de transacciones o de procesamiento por lotes del lado cliente)|

### Información adicional

- Los mensajes de las colas de Azure son normalmente primero en entrar es el primero en salir, pero a veces pueden estar ordenados; por ejemplo, cuando expira la duración de tiempo de espera de visibilidad de un mensaje (por ejemplo, como resultado de una aplicación de cliente que se bloquea durante el proceso). Cuando expira el tiempo de espera de visibilidad, el mensaje se vuelve visible en la cola para que otro trabajador lo quite de la cola. En ese momento, el mensaje recién visible se puede colocar en la cola (para quitarlo después) después de un mensaje que se colocó originalmente en cola después de él.

- Si ya está usando las tablas o blobs de almacenamiento de blobs de Azure y empieza a usar colas, se le garantiza una disponibilidad del 99,9 %. Si usa blobs o tablas con las colas de Service Bus, tendrá una disponibilidad menor.

- El patrón de FIFO garantizado en las colas de Service Bus requiere el uso de sesiones de mensajería. En caso de que la aplicación se bloquee al procesar un mensaje recibido en el modo **Ojear y bloquear**, la próxima vez que un receptor de la cola acepte una sesión de mensajería, empezará con el mensaje de error después de que expire su período de vida (TTL).

- Las colas de Azure están diseñadas para admitir escenarios de cola estándar, como componentes de aplicación de desacoplamiento para aumentar la escalabilidad y tolerancia a errores, nivelación de carga y creación de flujos de trabajo de proceso.

- Las colas de Service Bus admiten la garantía de entrega *Al menos una vez*. Además, la semántica de *Una vez como máximo* se puede admitir mediante el estado de la sesión para almacenar el estado de la aplicación y mediante transacciones para recibir mensajes y actualizar el estado de la sesión. El servicio de flujo de trabajo de Azure usa esta técnica para garantizar la entrega de Una vez como máximo.

- Las colas de Azure ofrecen un modelo de programación coherente y uniforme en las colas, tablas y blobs, tanto para desarrolladores como para los equipos de operaciones.

- Las colas de Service Bus ofrecen compatibilidad con transacciones locales en el contexto de una sola cola.

- El modo *Recibir y eliminar* compatible con el Service Bus ofrece la capacidad de reducir el número de operaciones de mensajería (y el costo asociado) a cambio de una garantía de entrega menor.

- Las colas de Azure ofrecen concesiones con la capacidad de ampliar las concesiones para mensajes. Esto permite que los trabajadores mantengan breves concesiones en los mensajes. Por lo tanto, si se bloquea a un trabajador, el mensaje se puede volver a procesar rápidamente por otro trabajador. Además, un trabajador puede ampliar la concesión de un mensaje si necesita procesarlo durante más tiempo que el tiempo de concesión actual.

- Las colas de Azure ofrecen un tiempo de espera de visibilidad que puede establecer al poner en cola un mensaje o quitarlo de la cola. Además, puede actualizar un mensaje con distintos valores de concesión en tiempo de ejecución y actualizar distintos valores entre mensajes de la misma cola. Los tiempos de espera de bloqueo de Service Bus lock se definen en los metadatos de la cola; sin embargo, puede renovar el bloqueo mediante una llamada al método [RenewLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.renewlock.aspx).

- El tiempo de espera máximo para una operación de recepción de bloqueo en las colas de Service Bus es de 24 días. Sin embargo, los tiempos de espera basados en REST tienen un valor máximo de 55 segundos.

- El procesamiento por lotes del cliente ofrecido por el Service Bus permite a un cliente de cola procesar varios mensajes por lotes en una sola operación de envío. El procesamiento por lotes solo está disponible para las operaciones de envío asincrónicas.

- Características como el límite de 200 TB de colas de Azure (más cuando se virtualizan las cuentas) y las colas ilimitadas la convierten en la plataforma idónea para los proveedores de SaaS.

- Las colas de Azure ofrecen un mecanismo de control de acceso delegado de rendimiento y flexible.

## Capacidades avanzadas

En esta sección se comparan algunas de las capacidades avanzadas ofrecidas por las colas de Azure y las colas de Service Bus.

|Criterios de comparación|Colas de Azure|Colas del Bus de servicio|
|---|---|---|
|Entrega programada|**Sí**|**Sí**|
|Mensajes fallidos automáticos|**No**|**Sí**|
|Aumentar el valor de período de vida de cola|**Sí**<br/><br/>(a través de la actualización local de tiempo de espera de visibilidad)|**Sí**<br/><br/>(proporcionado mediante una función de API dedicada)|
|Compatibilidad con mensajes dudosos|**Sí**|**Sí**|
|Actualización local|**Sí**|**Sí**|
|Registro de transacciones del servidor|**Sí**|**No**|
|Métricas de almacenamiento|**Sí**<br/><br/>**Métricas de minuto**: ofrece métricas en tiempo real para disponibilidad, TPS, recuentos de llamadas de API, recuentos de errores y mucho más, todo en tiempo de real (agregado por minuto y notificado en unos minutos a partir de lo que acaba de ocurrir en producción. Para obtener más información, vea [Acerca de las métricas del análisis de almacenamiento](https://msdn.microsoft.com/library/azure/hh343258.aspx).|**Sí**<br/><br/>(consultas masivas llamando a [GetQueues](https://msdn.microsoft.com/library/azure/hh293128.aspx))|
|Administración de estados|**No**|**Sí**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.entitystatus.aspx)|
|Reenvío automático de mensajes|**No**|**Sí**|
|Purgar la función de cola|**Sí**|**No**|
|Grupos de mensajes|**No**|**Sí**<br/><br/>(mediante el uso de sesiones de mensajería)|
|Estado de la aplicación por grupo de mensajes|**No**|**Sí**|
|Detección de duplicados|**No**|**Sí**<br/><br/>(configurable en el lado del remitente)|
|Integración con WCF|**No**|**Sí**<br/><br/>(ofrece enlaces de WCF listos para usar)|
|Integración de WF|**Personalizada**<br/><br/>(requiere la creación de una actividad de WF personalizada)|**Nativa**<br/><br/>(ofrece actividades de WF listas para usar)|
|Exploración de grupos de mensaje|**No**|**Sí**|
|Captura de sesiones de mensajes por id.|**No**|**Sí**|

### Información adicional

- Ambas tecnologías de cola permiten que se programe un mensaje para su entrega posteriormente.

- El reenvío automático de cola permite el reenvío automático de miles de colas de sus mensajes a una única cola, desde la que la aplicación receptora consume el mensaje. Puede usar este mecanismo para lograr seguridad, flujo de control y aislar el almacenamiento aislado entre cada publicador de mensajes.

- Las colas de Azure ofrecen compatibilidad para actualizar el contenido del mensaje. Puede usar esta funcionalidad para conservar información de estado y actualizaciones incrementales de progreso en el mensaje para que se pueda procesar desde el último punto de comprobación conocido, en lugar de hacerlo desde el principio. Con las colas de Service Bus, puede habilitar el mismo escenario mediante el uso de sesiones de mensajes. Las sesiones le permiten guardar y recuperar el estado de procesamiento de la aplicación (mediante [SetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx) y [GetState](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx)).

- Los mensajes con problemas de entrega, que solo se admiten por las colas de Service Bus, pueden ser útiles para aislar los mensajes que no se pueden procesar correctamente por la aplicación receptora o cuando los mensajes no pueden llegar a su destino debido a una propiedad de período de vida (TTL) expirada. El valor de TTL especifica cuánto tiempo permanece un mensaje en la cola. Con Service Bus, el mensaje se moverá a una cola especial denominada $DeadLetterQueue cuando expira el período de vida.

- Para encontrar mensajes "dudosos" en las colas de Azure, al quitar de la cola un mensaje de la aplicación se examina la propiedad DequeueCount del mensaje. Si DequeueCount se encuentra por encima de un umbral determinado, la aplicación mueve el mensaje a una cola de mensajes fallidos definida por la aplicación.

- Las colas de Azure le permiten obtener un registro detallado de todas las transacciones ejecutadas con la cola, así como las métricas agregadas. Ambas opciones son útiles para depurar y entender cómo usa su aplicación las colas de Azure. También son útiles para optimizar el rendimiento de la aplicación y reducir los costos de usar colas.

- El concepto de "sesiones de mensajes" admitido por el Service Bus permite que los mensajes que pertenecen a un determinado grupo lógico se asocien a un receptor específico, que a su vez crea una afinidad de sesiones entre los mensajes y sus receptores respectivos. Puede habilitar esta funcionalidad avanzada en el Service Bus estableciendo la propiedad [SessionID](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx) en un mensaje. Los receptores pueden escuchar entonces en un id. de sesión específico y recibir mensajes que comparten el identificador de sesión especificado.

- La funcionalidad de detección de duplicación admitida por las colas del Service Bus elimina automáticamente los mensajes duplicados enviados a una cola o tema, según el valor de la propiedad [MessageID](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx).

## Capacidad y cuotas

En esta sección se comparan las colas de Azure y las colas del Service Bus desde la perspectiva de la capacidad y las cuotas que se pueden aplicar.

|Criterios de comparación|Colas de Azure|Colas del Bus de servicio|
|---|---|---|
|Tamaño de cola máximo|**200 TB**<br/><br/>(limitado a una capacidad de cuenta de almacenamiento única)|**De 1 GB a 80 GB**<br/><br/>(definido al crear una cola y [habilitar particiones](service-bus-partitioning.md): vea la sección "Información adicional")|
|Tamaño de mensaje máximo|**64 KB**<br/><br/>(48 KB cuando se usa la codificación **Base64**)<br/><br/>Azure admite mensajes de gran tamaño mediante la combinación de colas y blobs, momento en el que puede poner en cola hasta 200 GB para un solo elemento.|**256 KB**<br/><br/>(incluidos tanto el encabezado como el cuerpo, tamaño de encabezado máximo: 64 KB)|
|TTL de mensaje máximo|**7 días**|**Sin límite**|
|Número máximo de colas|**Sin límite**|**10.000**<br/><br/>(por espacio de nombres de servicio, se puede aumentar)|
|Número máximo de clientes simultáneos|**Sin límite**|**Sin límite**<br/><br/>(el límite de 100 conexiones simultáneas solo se aplica a la comunicación basada en protocolo TCP)|

### Información adicional

- Service Bus aplica límites de tamaño de cola. El tamaño máximo de la cola se especifica al crear la cola y puede tener un valor entre 1 y 80 GB. Si se alcanza el valor de tamaño de la cola establecido al crear la cola, se rechazarán los mensajes entrantes adicionales y el código de llamada recibirá una excepción. Para obtener más información sobre las cuotas en el Service Bus, vea [Cuotas de Service Bus](service-bus-quotas.md).

- Puede crear colas de Service Bus en tamaños de 1, 2, 3, 4 o 5 GB (el valor predeterminado es 1 GB). Con las particiones habilitadas (que es el valor predeterminado), Service Bus crea 16 particiones por cada GB que especifique. Por lo tanto, si crea una cola con un tamaño de 5 GB, con 16 particiones el tamaño de cola máximo se convierte en (5 * 16) = 80 GB. Puede ver el tamaño máximo de la cola o tema con particiones examinando su entrada en el [Portal de Azure clásico][].

- Con las colas de Azure, si el contenido del mensaje no es seguro para XML, debe estar codificado con **Base64**. Si codifica el mensaje con **Base64**, la carga de usuario puede ser de hasta 48 KB, en lugar de 64 KB.

- Con las colas de Service Bus, cada mensaje almacenado en una cola consta de dos partes: un encabezado y un cuerpo. El tamaño total del mensaje no puede superar los 256 KB.

- Cuando los clientes se comunican con colas de Service Bus por el protocolo TCP, el número máximo de conexiones simultáneas a una única cola de Service Bus se limita a 100. Este número se comparte entre remitentes y receptores. Si se alcanza esta cuota, se rechazarán las solicitudes posteriores de conexiones adicionales y el código de llamada recibirá una excepción. Este límite no se impone en clientes que se conectan a las colas mediante la API basada en REST.

- Si necesita más de 10.000 colas en un único espacio de nombres de Service Bus, puede ponerse en contacto con el equipo de soporte técnico de Azure y solicitar un aumento. Para escalar más allá de las 10 000 colas con el Bus de servicio, también puede crear espacios de nombres adicionales mediante el [Portal de Azure clásico][].

## Administración y operaciones

En esta sección se comparan algunas de las características de administración ofrecidas por las colas de Azure y las colas de Service Bus.

|Criterios de comparación|Colas de Azure|Colas del Bus de servicio|
|---|---|---|
|Protocolo de administración|**REST sobre HTTP/HTTPS**|**REST sobre HTTPS**|
|Protocolo de tiempo de ejecución|**REST sobre HTTP/HTTPS**|**REST sobre HTTPS**<br/><br/>**AMQP 1.0 estándar (TCP con TLS)**| 
| API administrada de .NET|**Sí**<br/><br/>(API de cliente de almacenamiento administrada de .NET)|**Sí**<br/><br/>(API de mensajería asíncrona administrada de .NET)|
|C++ nativo|**Sí**|**No**|
|API de Java|**Sí**|**Sí**|
|API de PHP|**Sí**|**Sí**|
|API de Node.js.|**Sí**|**Sí**|
|Compatibilidad con metadatos arbitrarios|**Sí**|**No**|
|Reglas de nomenclatura de cola|**Hasta 63 caracteres**<br/><br/>(las letras de un nombre de cola deben estar en minúscula)|**Hasta 260 caracteres**<br/><br/>(los nombres de cola distinguen mayúsculas de minúsculas)|
|Función de obtención de la longitud de la cola|**Sí**<br/><br/>(valor aproximado si los mensajes expiran más allá del TTL sin eliminarse)|**Sí**<br/><br/>(valor exacto a un momento dado)|
|Función de ojear|**Sí**|**Sí**|

### Información adicional

- Las colas de Azure ofrecen compatibilidad con atributos arbitrarios que se pueden aplicar a la descripción de la cola, en forma de pares de nombre/valor.

- Ambas tecnologías de cola ofrecen la capacidad de ojear un mensaje sin tener que bloquearlo, lo que puede resultar útil al implementar una herramienta de explorador de colas.

- Las API de mensajería asíncrona de .NET de Service Bus aprovechan las conexiones TCP de dúplex completo para mejorar el rendimiento en comparación con REST sobre HTTP, y admiten el protocolo estándar AMQP 1.0.

- Los nombres de cola de Azure pueden tener de 3 a 63 caracteres de longitud, pueden contener letras minúsculas, números y guiones. Para obtener más información, vea [Nomenclatura de colas y metadatos](https://msdn.microsoft.com/library/azure/dd179349.aspx).

- Los nombres de cola de Service Bus pueden tener hasta 260 caracteres y reglas de nomenclatura menos restrictivas. Los nombres de cola de Service Bus pueden contener letras, números, puntos (.), guiones (-) y caracteres de subrayado (\_).

## Rendimiento

En esta sección se comparan colas de Azure y de Service Bus desde una perspectiva de rendimiento.

|Criterios de comparación|Colas de Azure|Colas del Bus de servicio|
|---|---|---|
|Rendimiento máximo|**Hasta 2.000 mensajes por segundo**<br/><br/>(basado en la prueba comparativa con mensajes de 1 KB)|**Hasta 2.000 mensajes por segundo**<br/><br/>(basado en la prueba comparativa con mensajes de 1 KB)|
|Latencia media|**10 ms**<br/><br/>(con [TCP Nagle](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx) deshabilitado)|**20-25 ms**|
|Comportamiento de limitación|**Rechazar con código HTTP 503**<br/><br/>(las solicitudes limitadas no se consideran facturables)|**Rechazar con excepción/HTTP 503**<br/><br/>(las solicitudes limitadas no se consideran facturables)|

### Información adicional

- Una sola cola de Azure puede procesar hasta 2.000 transacciones por segundo. Una transacción es una operación **Put**, **Get** o **Delete**. El envío de un solo mensaje a una cola (**Put**) se cuenta como una transacción, pero la recepción de un mensaje suele ser un proceso de dos pasos que implica la recuperación (**Get**), seguido de una solicitud para quitar el mensaje de la cola (**Delete**). Como resultado, una operación de quitar de la cola correcta suele implicar dos transacciones. La recuperación de varios mensajes en un lote puede reducir el impacto de esto, ya que puede obtener (**Get**) hasta 32 mensajes en una sola transacción, seguido de una eliminación (**Delete**) para cada uno de ellos. Para un mejor rendimiento, puede crear varias colas (una cuenta de almacenamiento puede tener un número ilimitado de colas).

- Cuando la aplicación alcanza el rendimiento máximo para una cola de Azure, se suele devolver una respuesta "HTTP 503 servidor ocupado" desde el servicio de la cola. Cuando esto ocurre, la aplicación debe desencadenar la lógica de reintento con retraso de retroceso exponencial.

- La latencia de las colas de Azure es de 10 milisegundos de media cuando se administran mensajes pequeños (de menos de 10 KB) desde un servicio hospedado que se encuentra en la misma ubicación (región) que la cuenta de almacenamiento.

- Tanto las colas de Azure como las colas de Service Bus aplican el comportamiento de limitación rechazando las solicitudes a una cola que se está limitando. Sin embargo, ninguna de ellas considera facturables las solicitudes limitadas.

- Las pruebas comparativas con las colas de Service Bus han demostrado que una sola cola puede lograr un rendimiento de mensajes de hasta 2.000 mensajes por segundo con un tamaño de mensaje de aproximadamente 1 KB. Para lograr un mayor rendimiento, use varias colas. Vea [Procedimientos recomendados para mejorar el rendimiento mediante la mensajería asincrónica del Bus de servicio](service-bus-performance-improvements.md) para obtener más información sobre la optimización del rendimiento del Bus de servicio.

- Cuando se alcance el rendimiento máximo de una cola de Service Bus, se devuelve un [ServerBusyException](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx) (al usar la API de mensajería asíncrona de .NET) o respuesta HTTP 503 (al usar la API basada en REST) al cliente de la cola, que indica que se está limitando la cola.

## Autenticación y autorización

En esta sección se describen las características de autenticación y autorización compatibles con las colas de Azure y las colas de Service Bus.

|Criterios de comparación|Colas de Azure|Colas del Bus de servicio|
|---|---|---|
|Autenticación|**Clave simétrica**|**Clave simétrica**|
|Modelo de seguridad|Acceso delegado a través de tokens SAS.|SAS|
|Federación de proveedor de identidad:|**No**|**Sí**|

### Información adicional

- Se debe autenticar cada solicitud a cualquiera de las tecnologías de cola. No se admiten colas públicas con acceso anónimo. Con SAS, puede abordar este escenario publicando un SAS de solo escritura, SAS de solo lectura o incluso un SAS de acceso completo.

- El esquema de autenticación ofrecido por las colas de Azure implica el uso de una clave simétrica, que es un código de autenticación de mensajes basado en hash (HMAC), calculado con el algoritmo SHA-256 y codificado como una cadena **Base64**. Para obtener más información sobre el protocolo respectivo, consulte [Autenticación para los servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx). Las colas de Service Bus admiten un modelo similar mediante claves simétricas. Para obtener más información, vea [Autenticación con firma de acceso compartida con Service Bus](service-bus-shared-access-signature-authentication.md).

## Coste

En esta sección se comparan las colas de Azure y las de Service Bus desde una perspectiva de coste.

|Criterios de comparación|Colas de Azure|Colas del Bus de servicio|
|---|---|---|
|Coste de transacción en cola|**0,0036 $**<br/><br/>(por cada 100 000 transacciones)|**Nivel básico**: **0,05 $**<br/><br/>(por operaciones de millones)|
|Operaciones facturables|**Todas**|**Enviar/recibir solo**<br/><br/>(sin coste para otras operaciones)|
|Transacciones inactivas|**Facturables**<br/><br/>(la consulta de una cola vacía se cuenta como transacción facturable)|**Facturables**<br/><br/>(una recepción con una cola vacía se considera mensaje facturable)|
|Coste del almacenamiento|**0,07 $**<br/><br/>(por GB/mes)|**0,00 **|
|Costes de transferencia de datos salientes|**0,12 $ - 0,19 $**<br/><br/>(según la geografía)|**0,12 $ - 0,19 $**<br/><br/>(según la geografía)|

### Información adicional

- Las transferencias de datos se cobran según la cantidad total de datos que salen de los centros de datos de Azure a través de Internet en un período de facturación determinado.

- Las transferencias de datos entre los servicios de Azure ubicados dentro de la misma región no están sujetos a cobro.

- En el momento de elaborar este artículo, ninguna transferencia de datos de entrada está sujeta a cobro.

- Dada la compatibilidad del sondeo prolongado, el uso de la colas de Service Bus puede ser rentable en situaciones en las que se necesita la entrega de baja latencia.

>[AZURE.NOTE] Todos los costes están sujetos a cambios. Esta tabla refleja los precios actuales en el momento de redactar este artículo y no incluye ninguna oferta promocional que pueda haber disponible actualmente. Para obtener información actualizada sobre los precios de Azure, vea la página [Precios de Azure](https://azure.microsoft.com/pricing/). Para obtener más información sobre los precios de Bus de servicio, vea [Precios de Bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/).

## Conclusión

Al comprender mejor las dos tecnologías, podrá tomar una decisión más fundamentada sobre la tecnología de cola que usará y cuándo. La decisión sobre cuándo usar las colas de Azure o las colas de Service Bus depende claramente de una serie de factores. Estos factores pueden dependen en gran medida de las necesidades individuales de la aplicación y de su arquitectura. Si la aplicación ya usa las capacidades principales de Microsoft Azure, quizás prefiera elegir las colas de Azure, especialmente si necesita comunicación básica y mensajería entre servicios o necesita colas que puedan tener un tamaño superior a 80 GB.

Dado que las colas de Service Bus ofrecen varias características avanzadas, como sesiones, transacciones, detección de duplicados, mensajes con problemas de entrega automáticos, y capacidades de publicación o suscripción duraderas, pueden ser la opción preferida si está creando una aplicación híbrida o si su aplicación necesita por otra parte estas características.

## Pasos siguientes

En los artículos siguientes se ofrece más orientación e información acerca del uso de las colas de Azure o las colas de Service Bus.

- [Utilización de las colas del Bus de servicio](service-bus-dotnet-how-to-use-queues.md)
- [Uso del servicio de almacenamiento en cola](../storage/storage-dotnet-how-to-use-queues.md)
- [Procedimientos recomendados para mejorar el rendimiento mediante la mensajería asincrónica del Bus de servicio](service-bus-performance-improvements.md)
- [Introducción a las colas y los temas de Service Bus de Azure](http://www.code-magazine.com/article.aspx?quickid=1112041)
- [Guía para desarrolladores sobre el Service Bus](http://www.cloudcasts.net/devguide/)
- ["Tablas de Azure y profundización en colas"](http://www.microsoftpdc.com/2009/SVC09)
- [Arquitectura de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
- [Uso del servicio de cola en Azure ](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)
- [Descripción de la facturación del almacenamiento de Azure: ancho de banda, transacciones y capacidad](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)


[Portal de Azure clásico]: http://manage.windowsazure.com
 

<!---HONumber=AcomDC_0128_2016-->