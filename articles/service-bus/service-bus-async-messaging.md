<properties 
   pageTitle="Mensajería asincrónica del bus de servicio | Microsoft Azure"
   description="Descripción de la mensajería asincrónica del bus de servicio"
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

# Patrones de mensajería asincrónica y alta disponibilidad

La mensajería asincrónica se puede implementar de varias maneras. Con colas, temas y suscripciones, denominadas colectivamente entidades de mensajería, el bus de servicio de Azure admite la asincronía a través de un mecanismo de almacén y reenvío. En un funcionamiento normal (sincrónico), envía mensajes a colas y temas, y recibe mensajes de colas y suscripciones. Las aplicaciones que escriba dependen de que estas entidades estén siempre disponibles. Cuando cambia el estado de la entidad, debido a varias circunstancias, necesita una manera de proporcionar una entidad de capacidad reducida que pueda satisfacer la mayoría de las necesidades.

Las aplicaciones suelen usar patrones de mensajería asincrónica para habilitar varios escenarios de comunicación. Puede compilar aplicaciones en las que los clientes puedan enviar mensajes a servicios, incluso cuando el servicio no esté en ejecución. En las aplicaciones que sufren ráfagas de comunicaciones, una cola puede ayudar a nivelar la carga, ya que proporciona un lugar para almacenar las comunicaciones en el búfer. Por último, puede obtener un equilibrador de carga simple, pero eficaz, para distribuir los mensajes entre varias máquinas.

Para mantener la disponibilidad de todas estas entidades, considere las distintas maneras en que estas entidades pueden aparecer no disponibles para un sistema de mensajería duradero. Por lo general, vemos que la entidad deja de estar disponible para las aplicaciones que escribimos de las siguientes maneras:

1.  No es posible enviar mensajes.

2.  No es posible recibir mensajes.

3.  No es posible administrar entidades (crear, recuperar, actualizar o eliminar entidades).

4.  No es posible ponerse en contacto con el servicio.

Para cada uno de estos errores, existen modos de error diferentes que permiten que las aplicaciones sigan en funcionamiento con cierto nivel de capacidad reducida. Por ejemplo, un sistema que puede enviar mensajes, pero no recibirlos, puede recibir pedidos de clientes pero no procesarlos. En este tema se tratan los posibles problemas que se pueden producir y de qué manera se mitigan. El bus de servicio ha introducido varias mitigaciones en las que debe participar y en este tema también se describen las reglas que rigen el uso de esos procedimientos para mitigar problemas de participación.

## Confiabilidad en el bus de servicio

Hay varias maneras de tratar los problemas de los mensajes y de las entidades, y hay directrices que rigen el uso adecuado de dichos procedimientos para mitigar problemas. Para comprender las directrices, primero es preciso saber qué es lo que puede generar errores en el bus de servicio. Dado el diseño de los sistemas de Azure, todos estos problemas tienden a durar poco tiempo. A alto nivel, las diferentes causas de la no disponibilidad aparecerán como sigue:

-   Limitación desde un sistema externo del que depende el bus de servicio. Se produce una limitación de las interacciones con los recursos de proceso y almacenamiento.

-   Problema en un sistema del que depende el bus de servicio. Por ejemplo, pueden aparecer problemas en una parte determinada del almacenamiento.

-   Error del bus de servicio en un subsistema individual. En esta situación, un nodo de proceso puede entrar en un estado incoherente y debe reiniciarse, provocando que todas las entidades a las que sirve equilibren su carga con otros nodos. A su vez, esto puede provocar que los mensajes se procesen más lentamente durante un breve periodo.

-   Error del bus de servicio en un centro de datos de Azure. Este es el clásico "error catastrófico" y es imposible acceder el sistema durante muchos minutos, o incluso horas.

> [AZURE.NOTE]El término **almacenamiento** puede significar Almacenamiento de Azure y SQL Azure.

Bus de servicio contiene varios procedimientos para mitigar estos problemas. En las secciones siguientes se tratan todos estos problema y los respectivos procedimientos para mitigarlos.

### Limitaciones

Con Bus de servicio, la limitación permite la administración cooperativa de la velocidad de los mensajes. Cada nodo individual del bus de servicio alberga muchas entidades. Cada una de esas entidades realiza peticiones al sistema en cuanto a CPU, memoria, almacenamiento, etc. Si cualquiera de estas facetas detecta un uso que supera los umbrales definidos, el bus de servicio puede denegar una solicitud determinada. El llamador recibe una excepción [ServerBusyException][] y lo reintenta transcurridos 10 segundos.

Como mitigación, el código debe leer el error y detener todos los reintentos del mensaje durante al menos 10 segundos. Puesto que el error puede producirse en varias partes de la aplicación de cliente, se espera que cada una de ellas ejecute la lógica de reintento de forma independiente. El código puede reducir la posibilidad de que la habilitación de particiones en una cola o tema pueda suponer una limitación.

### Problema en una dependencia de Azure

Otros componentes de Azure en ocasiones pueden tener problemas de servicio ocasionalmente. Por ejemplo, cuando se actualiza un sistema que usa Bus de servicio, dicho sistema puede experimentar temporalmente la reducción de sus funcionalidades. Para solucionar estos tipos de problemas, el bus de servicio investiga periódicamente e implementa las mitigaciones. Aparecen los efectos secundarios de estas mitigaciones. Por ejemplo, para controlar problemas transitorios de almacenamiento, el bus de servicio implementa un sistema que permite que las operaciones de envío funcionen de forma coherente. Debido a la propia naturaleza de la mitigación, un mensaje enviado puede tardar hasta 15 minutos en aparecer en la cola o suscripción afectadas y estar listo para una operación de recepción. Por lo general, la mayoría de las entidades no sufrirán este problema. Sin embargo, dado el número de entidades del bus de servicio de Azure, a veces esta mitigación es necesaria para un pequeño subconjunto de los clientes del bus de servicio.

### Error del bus de servicio en un subsistema individual

Con cualquier aplicación, las circunstancias pueden provocar que algún componente interno del bus de servicio pueda volverse incoherente. Cuando el bus de servicio lo detecta, recopila datos de la aplicación para ayudar a diagnosticar lo que sucedió. Una vez que se hayan recopilado los datos, la aplicación se reinicia para intentar que vuelva a un estado coherente. Este proceso ocurre con bastante rapidez y su resultado es que una entidad parezca que no está disponible durante unos minutos, aunque los tiempos de inactividad son mucho menores.

En estos casos, la aplicación cliente genera una excepción [System.TimeoutException][] o [MessagingException][]. El SDK de .NET del bus de servicio contiene una mitigación para este problema en forma de lógica de reintento de cliente automatizado. Si se agotó el período de reintento y no se entregó el mensaje, puede probar con otras funciones, como los [espacios de nombres emparejados][]. Estos espacios de nombres tienen otros riesgos que se describen más adelante en este documento.

### Error del bus de servicio en un centro de datos de Azure

El motivo más probable de error en un centro de datos de Azure es un error en la implementación de una actualización del bus de servicio o de un sistema dependiente. La haber madurado la plataforma, la probabilidad de que se produzca este tipo de error se ha reducido. También puede producir un error en el centro de datos por otros motivos, entre los que se incluyen:

-   Interrupción del suministro eléctrico (desaparecen la fuente de alimentación y la generación de energía).
-   Conectividad (interrupción de Internet entre los clientes y Azure).

En ambos casos, el problema lo provocó un desastre natural o causado por el hombre. Para solucionar este problema y asegurarse de que se pueden enviar mensajes, puede usar [espacios de nombres emparejados][] para permitir que los mensajes se envíen a una segunda ubicación mientras se repara la ubicación principal. Para más información, consulte [Procedimientos recomendados para aislar aplicaciones ante desastres e interrupciones de Bus de servicio][].

## Espacios de nombres emparejados

La característica de [espacios de nombres emparejados][] admite escenarios en los que una entidad o implementación de Bus de servicio dentro de un centro de datos deja de estar disponible. Aunque este evento se produce con poca frecuencia, los sistemas distribuidos deben estar preparados para controlar los escenarios más desfavorables. Normalmente, este evento se produce porque algún elemento del que depende Bus de servicio sufre un problema temporal. Para mantener la disponibilidad de las aplicaciones en caso una interrupción, los usuarios del bus de servicio pueden usar dos espacios de nombres independientes, preferiblemente de centros de datos independientes, para hospedar sus entidades de mensajería. En el resto de la sección se usa la siguiente terminología:

-   Espacio de nombres principal: el espacio de nombres con el que interactúa la aplicación para enviar y recibir operaciones.

-   Espacio de nombres secundario: el espacio de nombres que actúa como una copia de seguridad del espacio de nombres principal. La lógica de la aplicación no interactúa con este espacio de nombres.

-   Intervalo de conmutación por error: el tiempo durante el que se aceptan errores normales antes de que la aplicación cambie del espacio de nombres principal al espacio de nombres secundario.

Los espacios de nombres emparejados admiten la *disponibilidad de envío*. La disponibilidad de envío se centra en mantener la capacidad para enviar mensajes. Para usar la disponibilidad de envío, la aplicación debe cumplir los siguientes requisitos:

1.  Solo se reciben mensajes del espacio de nombres principal.

2.  Los mensajes enviados a una cola o tema determinados pueden llegar desordenados.

3.  Si la aplicación usa sesiones, los mensajes de cualquiera de ellas pueden llegar desordenados. Esto no es algo habitual en el funcionamiento normal de las sesiones, ya que la aplicación usa las sesiones para agrupar los mensajes de forma lógica. El estado de sesión solo se mantiene en el espacio de nombres principal.

4.  Los mensajes de una sesión pueden llegar desordenados. Esto no es algo habitual en el funcionamiento normal de las sesiones, ya que la aplicación usa las sesiones para agrupar los mensajes de forma lógica.

5.  El estado de sesión solo se mantiene en el espacio de nombres principal.

6.  La cola principal puede conectarse y empezar a aceptar mensajes antes de que la cola secundaria le entregue todos los mensajes.

En las secciones siguientes se describen las API y cómo se implementan las API, y se muestra código de ejemplo que usa la característica. Tenga en cuenta que esta característica tiene asociadas unos costos.

### La API de MessagingFactory.PairNamespaceAsync

La característica de espacios de nombres emparejados introduce el método [PairNamespaceAsync][] en la clase [Microsoft.ServiceBus.Messaging.MessagingFactory][]\:

```
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Cuando la tarea finaliza, también lo hace el emparejamiento de espacios de nombres, que está listo para actuar en cualquier objeto [MessageReceiver][], [QueueClient][] o [TopicClient][] creado con la clase [MessagingFactory][]. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][] es la clase base para los distintos tipos de emparejamiento disponibles con un objeto [MessagingFactory][]. Actualmente, la única clase derivada es la denominada [SendAvailabilityPairedNamespaceOptions][], que implementa los requisitos de disponibilidad de envío. [SendAvailabilityPairedNamespaceOptions][] tiene un conjunto de constructores que se compilan entre sí. Si se examina el constructor con más parámetros, se puede comprender el comportamiento de los restantes.

```
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Estos parámetros tienen los significados siguientes:

-   *secondaryNamespaceManager*: instancia de [NamespaceManager][] inicializada para el espacio de nombres secundario que el método [PairNamespaceAsync][] puede usar para configurar dicho espacio de nombres. El administrador de espacio de nombres se usará para obtener la lista de colas del espacio de nombres y asegurarse de que existen las colas de trabajos pendientes requeridas. Si no existen, se crearán. [NamespaceManager][] requiere la capacidad de crear un token con la notificación de **administración**.

-   *messagingFactory*: la instancia de [MessagingFactory][] para el espacio de nombres secundario. El objeto [MessagingFactory][] se usa para enviar y, si la propiedad [EnableSyphon][] está establecida en **true**, para recibir mensajes de las colas de trabajos pendientes.

-   *backlogQueueCount*: el número de colas de trabajos pendientes que se crean. Este valor debe ser al menos 1. Al enviar mensajes al trabajo pendiente, se elige aleatoriamente una de estas colas. Si establece el valor en 1, solo se puede usar una cola. Cuando esto ocurre y la única cola de trabajos pendientes genera errores, el cliente no puede probar otra cola de trabajos pendientes y se puede producir un error al enviar el mensaje. Se recomienda seleccionar un valor mayor y que el valor predeterminado sea 10. Puede cambiarlo a un valor mayor o menor en función de la cantidad de datos que la aplicación envíe al día. Cada cola de trabajos pendientes puede contener hasta 5 GB de mensajes.

-   *failoverInterval*: el periodo durante el cual se aceptarán errores en el espacio de nombres principal antes de cambiar cualquier entidad única al espacio de nombres secundario. Las conmutaciones por error se producen entidad por entidad. Con frecuencia, las entidades de un espacio de nombres individual residen en nodos diferentes del bus de servicio. Un error en una de las entidades no implica un error en otra. Este valor se puede establecer en [System.TimeSpan.Zero][] para conmutar por error al secundario inmediatamente después del primer error no transitorio. Los errores que desencadenan el temporizador de conmutación por error son cualquier [MessagingException][] en donde la propiedad [IsTransient][] sea false o un [System.TimeoutException][]. Otras excepciones, como [UnauthorizedAccessException][], no provocan la conmutación por error, ya que indican que el cliente no está configurado correctamente. [ServerBusyException][] no causa la conmutación por error porque el patrón correcto es esperar 10 segundos y, a continuación, volver a enviar el mensaje.

-   *enableSyphon*: indica que este emparejamiento concreto también debe extraer con sifón los mensajes del espacio de nombres secundario al espacio de nombres principal. En general, las aplicaciones que envían mensajes deben establecer este valor en **false**, mientras que las que los reciben mensajes deben establecerlo en **true**. Esto se debe a que, con frecuencia, hay menos destinatarios de mensajes que remitentes. En función del número de destinatarios, puede elegir que una instancia individual de la aplicación controle las tareas del sifón. El uso de muchos destinatarios conlleva costos por cada cola de trabajos pendientes.

Para usar el código, cree una instancia principal de [MessagingFactory][], una instancia secundaria de [MessagingFactory][] y una instancia secundaria de [NamespaceManager][] y una instancia de [SendAvailabilityPairedNamespaceOptions][]. La llamada puede ser tan simple como la siguiente:

```
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Cuando se completa la tarea que devuelve el método [PairNamespaceAsync][], todo está configurado y listo para su uso. Antes de que se devuelva la tarea, es posible que no haya completado todo el trabajo en segundo plano necesario para que el emparejamiento funcione correctamente. Como consecuencia, no debe empezar a enviar mensajes hasta que se devuelva la tarea. Si se produce algún error, como unas credenciales incorrectas o un error al crear las colas de trabajos pendientes, las excepciones se iniciarán una vez que se complete la tarea. Una vez que la tarea se devuelva, compruebe que las colas se han encontrado o creado, para lo que es preciso examinar la [BacklogQueueCount][] de la instancia de [SendAvailabilityPairedNamespaceOptions][]. Para el código anterior, la operación aparece como se indica a continuación:

```
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## Pasos siguientes

Ahora que ha aprendido los conceptos básicos de la mensajería asincrónica en el bus de servicio, obtenga más información acerca los [espacios de nombres emparejados implicaciones y su costo][].

  [ServerBusyException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx
  [System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [Procedimientos recomendados para aislar aplicaciones ante desastres e interrupciones de Bus de servicio]: service-bus-outages-disasters.md
  [Microsoft.ServiceBus.Messaging.MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [MessageReceiver]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [EnableSyphon]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.enablesyphon.aspx
  [System.TimeSpan.Zero]: https://msdn.microsoft.com/library/azure/system.timespan.zero.aspx
  [IsTransient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx
  [UnauthorizedAccessException]: https://msdn.microsoft.com/library/azure/system.unauthorizedaccessexception.aspx
  [BacklogQueueCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.backlogqueuecount.aspx
  [espacios de nombres emparejados implicaciones y su costo]: service-bus-paired-namespaces.md
  [espacios de nombres emparejados]: service-bus-paired-namespaces.md

<!---HONumber=AcomDC_0107_2016-->