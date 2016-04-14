#Bus de servicio

Cuando una aplicación o servicio se ejecutan en la nube o de manera local, a menudo tienen que interactuar con otras aplicaciones o servicios. Para proporcionar una manera enormemente útil de hacerlo, Azure ofrece el Bus de servicio. En este artículo nos centramos en esta tecnología, describiendo qué es y los beneficios que ofrece.

## Elementos fundamentales del Bus de servicio
Las distintas situaciones requieren distintos estilos de comunicación. A veces, permitir a las aplicaciones enviar y recibir mensajes mediante una simple cola es la mejor solución. En otras situaciones, una cola ordinaria no es suficiente y es mejor una cola con un mecanismo de publicación y suscripción. En otros casos, lo realmente necesario es una conexión entre las aplicaciones (no se requieren colas). El Bus de servicio ofrece las tres opciones, por lo que sus aplicaciones pueden interactuar de varias maneras diferentes.

El Bus de servicio es un servicio en la nube multiinquilino, lo que significa que varios usuarios comparten el servicio. Cada usuario, como desarrollador de aplicaciones, crea un *espacio de nombres* y, a continuación, define los mecanismos de comunicación que necesita dentro de ese espacio de nombres. La [ilustración 1](#Fig1) muestra su apariencia.

<a name="Fig1"></a>![Diagrama del bus de servicio de Azure][svc-bus]
 
**Ilustración 1: el Bus de servicio proporciona un servicio multiinquilino para conectar aplicaciones a través de la nube.**

Dentro de un espacio de nombres puede usar uno o más ejemplos de cuatro mecanismos de comunicación diferentes, que se conectan a las aplicaciones de forma distinta. Las opciones son las siguientes:

- *Colas*, que permiten una comunicación unidireccional. Cada cola funciona como un intermediario (al que se le llama a veces *agente*) que almacena los mensajes enviados hasta que se reciben. Cada mensaje lo recibe un único destinatario.
- *Temas*, que proporcionan una comunicación unidireccional mediante *suscripciones* (un solo tema puede tener varias suscripciones). Como en las colas, los temas funcionan como agentes, pero cada suscripción puede usar opcionalmente un filtro para recibir solo los mensajes que cumplan con criterios específicos.
- *Retransmisiones*, que permiten una comunicación bidireccional. A diferencia de las colas y los temas, los relés no almacenan mensajes que se encuentran en proceso; no son un agente. Simplemente los transmiten a la aplicación de destino.
- *Centros de eventos*, que proporcionan ingresos de telemetría y eventos a la nube a escala masiva, con una latencia baja y una confiabilidad alta.

Cuando se crea una cola, un tema, una retransmisión o un centro de eventos, se les asigna un nombre. Cuando se combina con el nombre del espacio de nombres, se crea un identificador exclusivo para el objeto. Las aplicaciones pueden proporcionar ese nombre al Bus de servicio y, a continuación, usar esa cola, tema, retransmisión o centro de eventos para comunicarse con los demás.

Para usar cualquiera de estos objetos, las aplicaciones de Windows pueden usar Windows Communication Foundation (WCF). Para las colas, los temas y los centros de eventos, las aplicaciones de Windows también pueden utilizar las API de mensajes definidas por el Bus de servicio. Para facilitar el uso de estos objetos desde aplicaciones que no sean de Windows, Microsoft proporciona SDK para Java, Node.js y otros lenguajes. También puede obtener acceso a las colas, los temas y centros de eventos mediante las API de REST a través de HTTP.

Es importante comprender que aunque el Bus de servicio se ejecute en la nube (es decir, en los centros de datos de Azure de Microsoft), las aplicaciones que usa pueden ejecutarse desde cualquier lugar. Puede usar el Bus de servicio para conectarse a aplicaciones que se ejecutan, por ejemplo, en Azure o aplicaciones que se ejecutan dentro de su propio centro de datos. También puede usarlo para conectarse a una aplicación que se ejecuta en Azure o a otra plataforma en la nube con una aplicación local o con tabletas y teléfonos. Es posible incluso conectar aparatos domésticos, sensores y otros dispositivos a una aplicación central o entre ellos. El Bus de servicio es un mecanismo de comunicación genérico en la nube al que puede obtenerse acceso desde prácticamente cualquier lugar. La forma en la que lo use depende de las tareas que tengan que realizar sus aplicaciones.


## Colas

Supongamos que decide conectar dos aplicaciones con una cola de Bus de servicio. La [ilustración 2](#Fig2) muestra esa situación.

<a name="Fig2"></a>![Diagrama de las colas del bus de servicio][queues]
 
**Figura 2: las colas del Bus de servicio proporcionan una cola asincrónica unidireccional.**

El proceso es sencillo: un usuario A envía un mensaje a la cola del Bus de servicio y un receptor recoge ese mensaje más tarde. Es posible que una cola tenga un único receptor, como se muestra en la [ilustración 2](#Fig2), o bien que varias aplicaciones puedan leer desde la misma cola. En el segundo caso, cada mensaje lo lee un solo receptor (para un servicio de multidifusión, hay que usar un tema en su lugar).

Cada mensaje tiene dos partes: un conjunto de propiedades, cada una de ellas, un par clave/valor, y un cuerpo de mensaje binario. El modo en que se usan depende de lo que una aplicación esté tratando de hacer. Por ejemplo, una aplicación que envía un mensaje sobre una venta reciente podría incluir las propiedades *Seller="Ava"* y *Amount=10000*. El cuerpo del mensaje puede contener una imagen escaneada del contrato firmado de la venta o, si no la hubiera, puede aparecer vacío.

El receptor puede leer los mensajes de la cola del Bus de servicio de dos formas distintas. La primera opción, denominada *ReceiveAndDelete*, quita un mensaje de la cola y lo elimina de inmediato. Esto es sencillo, pero si se produce un error por parte del receptor antes de que finalice el procesamiento del mensaje, este se perderá. Puesto que se quita de la cola, ningún otro receptor podrá obtener acceso a él.

La segunda opción, *PeekLock*, se ha creado para solucionar este problema. Como ocurre con ReceiveAndDelete, una lectura de PeekLock quita un mensaje de la lista. Sin embargo, no elimina el mensaje. En este caso, bloquea el mensaje, por lo que se oculta a los demás receptores. A continuación, espera a que se produzca uno de los tres eventos:

- Si el receptor procesa el mensaje correctamente, invoca *Completo* y la cola elimina el mensaje. 
- Si el receptor decide que no puede procesar el mensaje correctamente, invoca *Abandonar*. La cola quita el bloqueo del mensaje y se pone a disposición de otros receptores.
- Si el receptor no invoca ninguna de esas opciones en un período configurable (60 segundos de forma predeterminada), la cola presupone que se ha producido un error con el receptor. En ese caso, se comporta como si el receptor hubiera invocado Abandonar, por lo que el mensaje estaría disponible para otros receptores.

Observe lo que ocurre aquí: el mismo mensaje puede enviarse dos veces, quizás a dos receptores distintos. Las aplicaciones que usan colas de Bus de servicio deben estar preparadas para ello. Para facilitar la detección duplicada, cada mensaje dispone de una propiedad MessageID exclusiva que no se modifica de forma predeterminada independientemente del número de veces que se lea un mensaje en una cola.

Las colas son útiles en determinadas situaciones. Permiten a las aplicaciones comunicarse incluso cuando no se estén ejecutando a la vez. Esto es especialmente útil con aplicaciones móviles y por lotes. Una cola con varios receptores también proporciona un equilibrio de carga automático, ya que los mensajes enviados se difunden a través de esos receptores.


## Temas

Aunque son útiles, las colas no siempre son la solución adecuada. A veces, los temas de Bus de servicio son mejores. La [ilustración 3](#Fig3) muestra esta idea.

<a name="Fig3"></a>![Diagrama de las suscripciones y temas del bus de servicio][topics-subs]
 
**Ilustración 3: según el filtro que especifica una aplicación de suscripción, puede recibir algunos mensajes, o todos, enviados a un tema del Bus de servicio.**

Un tema es similar en muchos aspectos a una cola. Los remitentes envían mensajes a un tema de la misma forma en la que envían mensajes a una cola, y esos mensajes tienen la misma apariencia que las colas. La gran diferencia es que los temas permiten a cada aplicación de recepción crear su propia suscripción mediante la definición de un *filtro*. El suscriptor verá, por lo tanto, solo los mensajes que coincidan con ese filtro. Por ejemplo, la [ilustración 3](#Fig3) muestra un remitente y un tema con tres suscriptores, cada uno con su propio filtro:

- El suscriptor 1 recibe solo mensajes que contienen la propiedad *Seller="Ava"*.
- El suscriptor 2 recibe mensajes que contienen la propiedad *Seller="Ruby"* o la propiedad *Amount* cuyo valor es superior a 100.000. uizás Ruby es la directora de ventas, y por eso quiere ver sus propias ventas y todas las grandes ventas independientemente de quién las hace.
- El suscriptor 3 ha establecido su filtro en *True*, lo que significa que recibe todos los mensajes. Por ejemplo, esta aplicación puede ser responsable de mantener una traza de auditoría y, por tanto, necesita ver todos los mensajes.

Como ocurre con las colas, los suscriptores a un tema pueden leer los mensajes utilizando ReceiveAndDelete o PeekLock. Sin embargo, a diferencia de las colas, varios suscriptores pueden recibir un mensaje único enviado a un tema. Este enfoque, que se denomina normalmente *publicar y suscribir*, es útil cuando puede haber varias aplicaciones interesadas en los mismos mensajes. Si se define el filtro adecuado, cada suscriptor puede pulsar solo la parte de la secuencia de mensaje que necesita ver.


## Relés

Tanto las colas como los temas proporcionan una comunicación asincrónica unidireccional a través de un agente. El tráfico fluye en una única dirección y no existe una conexión directa entre los remitentes y los receptores. Sin embargo, ¿qué ocurre si no es eso lo que quiere? Supongamos que sus aplicaciones necesitan enviar y recibir mensajes, o que quizás quiere un vínculo directo entre ellas y necesita un agente para almacenar los mensajes. Para solucionar situaciones como esta, el Bus de servicio proporciona retransmisiones, como muestra la [ilustración 4](#Fig4).

<a name="Fig4"></a>![Diagrama del relé del bus de servicio][relay]
 
**Ilustración 4: la retransmisión del Bus de servicio proporciona comunicación sincrónica bidireccional entre aplicaciones.**

La pregunta obvia que se plantea acerca de las retransmisiones es la siguiente: ¿por qué habría de usa una? Incluso si no necesito colas, ¿por qué hacer que las aplicaciones se comuniquen a través de un servicio en la nube en lugar de interactuar directamente? La respuesta es que hablar directamente puede ser más complicado de lo que se piensa.

Supongamos que desea conectar dos aplicaciones locales que se ejecutan dentro de centros de datos corporativos. Cada una de esas aplicaciones se encuentran protegidas por un firewall y cada centro de datos usa probablemente la traducción de direcciones de red (NAT). El firewall bloquea los datos entrantes en casi todos los puertos y NAT implica que la máquina en la que se ejecuta cada aplicación no dispone de una dirección IP fija a la que se pueda llegar directamente desde fuera del centro de datos. Si no se cuenta con ayuda adicional, conectar esas aplicaciones mediante una conexión pública a Internet es complicado.

Un relé de Bus de servicio proporciona esa ayuda. Para comunicarse de forma bidireccional a través de un relé, cada aplicación establece una conexión TCP saliente con el Bus de servicio y, a continuación, la mantiene abierta. Todas las comunicaciones entre las dos aplicaciones se llevarán a cabo a través de esas conexiones. Puesto que cada conexión se estableció desde dentro del centro de datos, el firewall permitirá el tráfico entrante a cada aplicación sin abrir puertos nuevos. Este enfoque también evita el problema de NAT, puesto que cada aplicación cuenta con un extremo coherente en la nube a través de la comunicación. Las aplicaciones pueden evitar los problemas que podrían complicar la comunicación mediante el intercambio de datos a través del relé.

Para usar relés de Bus de servicio, las aplicaciones se basan en Windows Communication Foundation (WCF). El Bus de servicio proporciona enlaces a WCF que facilitan la interactuación de las aplicaciones de Windows a través de los relés. Las aplicaciones que ya usan WCF pueden normalmente especificar solo uno de esos enlaces y después comunicarse con los demás a través de un relé. Sin embargo, a diferencia de las colas y los temas, el uso cuando sea posible de relés de aplicaciones que no sean de Windows requiere algún tipo de esfuerzo de programación; no se proporcionan bibliotecas estándar.

Al contrario de lo que ocurre con las colas y los temas, las aplicaciones no crean relés explícitamente. En este caso, cuando una aplicación que desea recibir mensajes establece una conexión TCP con el Bus de servicio, se crea un relé automáticamente. Cuando se abandona la conexión, el relé se elimina. Para que una aplicación encuentre una retransmisión creada por un agente de escucha específico, el Bus de servicio proporciona un registro que permite a las aplicaciones ubicar una retransmisión específica por nombre.

Las retransmisiones son la solución adecuada cuando se necesita una comunicación directa entre las aplicaciones. Por ejemplo, piense en un sistema de reserva de una aerolínea que se ejecute en un centro de datos local y al que deba poder accederse desde mostradores de facturación, dispositivos móviles y otros equipos. Las aplicaciones que se ejecuten en todos esos sistemas podrían confiar en los relés del Bus de servicio en la nube para comunicarse independientemente del lugar en el que se estén ejecutando.

## Centros de eventos

Los Centros de eventos son un sistema de recopilación altamente escalable que puede procesar millones de eventos, lo que permite que su aplicación procese y analice las grandes cantidades de datos que generan sus aplicaciones y dispositivos conectados. Por ejemplo, puede utilizar un centro de eventos para recopilar datos sobre el rendimiento del motor en directo desde una flota de automóviles. Una vez recopilados en los Centros de eventos, puede transformar y almacenar los datos usando cualquier proveedor de análisis en tiempo real o clúster de almacenamiento. Para obtener más información sobre los Centros de eventos, consulte [Información general de los centros de eventos][].

## Resumen

La conexión de aplicaciones forma parte desde siempre de la creación de soluciones completas. El tipo de escenarios que requieren las aplicaciones y los servicios para comunicarse entre sí se configura de forma que aumente en función de las aplicaciones y los dispositivos que se conectan a Internet. Al proporcionar tecnologías basadas en la nube para conseguir esto a través de colas, temas, retransmisiones y centros de eventos, el Bus de servicios pretende facilitar la implementación de esta función fundamental y ofrecer una disponibilidad más amplia.

[svc-bus]: ./media/hybrid-solutions/SvcBus_01_architecture.png
[queues]: ./media/hybrid-solutions/SvcBus_02_queues.png
[topics-subs]: ./media/hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[relay]: ./media/hybrid-solutions/SvcBus_04_relay.png
[Información general de los centros de eventos]: https://msdn.microsoft.com/library/azure/dn836025.aspx

<!---HONumber=Oct15_HO3-->