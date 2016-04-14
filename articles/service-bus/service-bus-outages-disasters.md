<properties 
   pageTitle="Aislamiento de las aplicaciones del Bus de servicio ante interrupciones y desastres | Microsoft Azure"
   description="Se describen técnicas que puede usar para proteger las aplicaciones ante una posible interrupción del Bus de servicio."
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
   ms.date="01/26/2016"
   ms.author="sethm" />

# Procedimientos recomendados para aislar aplicaciones ante desastres e interrupciones de Bus de servicio

Las aplicaciones esenciales deben funcionar de manera continua, incluso si se producen interrupciones imprevistas o desastres. En este tema se describen técnicas que puede usar para proteger las aplicaciones del Bus de servicio ante un posible desastre o una interrupción del servicio.

Se define la interrupción como la falta temporal de disponibilidad del Bus de servicio de Azure. La interrupción puede afectar a algunos componentes del Bus de servicio, como a un almacén de mensajería, o incluso a todo el centro de datos. Una vez corregido el problema, el Bus de servicio vuelva a estar disponible. Normalmente, una interrupción no provoca la pérdida de mensajes ni otros datos. Un ejemplo de error de componente es la falta de disponibilidad de un determinado almacén de mensajería. Un ejemplo de interrupción de todo el centro de datos es un error de alimentación del centro de datos o un conmutador de red defectuoso en él. Una interrupción puede durar desde unos pocos minutos hasta varios días.

Un desastre se define como la pérdida permanente de una unidad de escalado del Bus de servicio o un centro de datos. El centro de datos no volverá necesariamente a estar disponible. Normalmente, un desastre provoca la pérdida de algunos o todos los mensajes u otros datos. Algunos ejemplos de desastres son los incendios, las inundaciones o los terremotos.

## Arquitectura actual

El Bus de servicio usa varios almacenes de mensajería para conservar los mensajes que se envían a colas o temas. Se asigna una cola o un tema sin particiones a un almacén de mensajería. Si este almacén de mensajería no está disponible, se producirá un error en todas las operaciones de esa cola o tema.

Todas las entidades de mensajería del Bus de servicio (colas, temas, retransmisiones) residen en un espacio de nombres de servicio, asociado con un centro de datos. El Bus de servicio no habilita la replicación geográfica automática de los datos, ni tampoco permite un espacio de nombres de servicio que abarque varios centros de datos.

## Protección contra interrupciones de ACS

Si está usando credenciales de ACS y este deja de estar disponible, los clientes ya no pueden obtener tokens. Los clientes que posean un token en el momento en que ACS deje de funcionar pueden seguir usando el Bus de servicio hasta que expiren los tokens. La duración predeterminada del token es de 3 horas.

Para protegerse ante las interrupciones de ACS, use tokens de firma de acceso compartido (SAS). En este caso, el cliente se autentica directamente con el Bus de servicio firmando un token autogenerado con una clave secreta. Las llamadas a ACS ya no son necesarias. Para obtener más información sobre los tokens SAS, consulte [Autenticación y autorización de Bus de servicio][].

## Protección de colas y temas contra errores en el almacén de mensajería

Se asigna una cola o un tema sin particiones a un almacén de mensajería. Si este almacén de mensajería no está disponible, se producirá un error en todas las operaciones de esa cola o tema. Por otra parte, una cola particionada está formada por varios fragmentos. Cada fragmento se guarda en un almacén de mensajería diferente. Cuando se envía un mensaje a una cola o un tema con particiones, Bus de servicio asigna el mensaje a uno de los fragmentos. Si el almacén de mensajería correspondiente no está disponible, el Bus de servicio escribe el mensaje en otro fragmento, si es posible. Para obtener más información acerca de las entidades con particiones, consulte [Entidades de mensajería con particiones][].

## Protección contra desastres o interrupciones del centro de datos

Para permitir una conmutación por error entre dos centros de datos, puede crear un espacio de nombres de servicio para el Bus de servicio en cada centro de datos. Por ejemplo, el espacio de nombres de servicio para el Bus de servicio **contosoPrimary.servicebus.windows.net** puede encontrarse en la región de Estados Unidos (norte/central), y **contosoSecondary.servicebus.windows.net** puede encontrarse en la región de Estados Unidos (sur/central). Si una entidad de mensajería del Bus de servicio debe permanecer accesible en el caso de una interrupción del centro de datos, puede crear esa entidad en ambos espacios de nombres.

Para obtener más información, consulte la sección "Error del Bus de servicio dentro de un centro de datos de Azure" en [Patrones de mensajería asincrónica y alta disponibilidad][].

## Protección de los extremos de retransmisión contra desastres o interrupciones del centro de datos

La replicación geográfica de los extremos de retransmisión permite que un servicio que exponga un extremo de retransmisión sea accesible en caso de interrupciones del Bus de servicio. Para lograr la replicación geográfica, el servicio debe crear dos extremos de retransmisión en diferentes espacios de nombres. Los espacios de nombres deben residir en distintos centros de datos y ambos extremos deben tener nombres diferentes. Por ejemplo, se puede acceder a un extremo principal en **contosoPrimary.servicebus.windows.net/myPrimaryService**, mientras que su equivalente secundario resulta accesible en **contosoSecondary.servicebus.windows.net/mySecondaryService**.

A continuación, el servicio escucha en ambos extremos y un cliente puede invocar el servicio a través de cualquiera de ellos. Una aplicación cliente selecciona de forma aleatoria una de las retransmisiones como extremo principal y envía su solicitud al extremo activo. Si la operación da como resultado un código de error, este error indica que el extremo de retransmisión no está disponible. La aplicación abre un canal al extremo de reserva y vuelve a emitir la solicitud. En ese momento, el extremo activo y el de reserva intercambian roles: la aplicación cliente considera el anterior extremo activo como el nuevo extremo de reserva y el anterior extremo de reserva pasa a ser el nuevo extremo activo. Si ambas operaciones de envío generan un error, no se modifican los roles de las dos entidades y se devuelve un error.

El ejemplo de [replicación geográfica con mensajes retransmitidos del Bus de servicio][] muestra cómo replicar retransmisiones.

## Protección de colas y temas contra desastres o interrupciones del centro de datos

Para lograr resistencia frente a interrupciones del centro de datos al usar mensajería asincrónica, el Bus de servicio admite dos enfoques: replicación *activa* y *pasiva*. Para cada enfoque, si una cola o un tema determinados deben permanecer accesibles en el caso de una interrupción del centro de datos, puede crear la entidad en ambos espacios de nombres. Ambas entidades pueden tener el mismo nombre. Por ejemplo, se puede acceder a una cola en **contosoPrimary.servicebus.windows.net/myQueue**, mientras que la secundaria resulta accesible en **contosoSecondary.servicebus.windows.net/myQueue**.

Si la aplicación no requiere comunicación permanente entre remitente y receptor, esta puede implementar una cola del lado cliente duradera para evitar la pérdida de mensajes y proteger al remitente ante los errores transitorios del Bus de servicio.

## Replicación activa

La replicación activa usa entidades en ambos espacios de nombres de servicio para cada operación. Cualquier cliente que envíe un mensaje envía dos copias de él. La primera copia se envía a la entidad primaria (por ejemplo, **contosoPrimary.servicebus.windows.net/sales**), y la segunda copia del mensaje se envía a la entidad secundaria (por ejemplo, **contosoSecondary.servicebus.windows.net/sales**).

Un cliente recibe mensajes de ambas colas. El receptor procesa la primera copia de un mensaje y se suprime la segunda copia. Para suprimir mensajes duplicados, el remitente debe etiquetar cada mensaje con un identificador único. Ambas copias del mensaje deben estar etiquetadas con el mismo identificador. Puede usar las propiedades [BrokeredMessage.MessageId][] o [BrokeredMessage.Label][], o bien una propiedad personalizada, para etiquetar el mensaje. El receptor debe mantener una lista de los mensajes que ya haya recibido.

El ejemplo de [replicación geográfica con mensajes asincrónicos del Bus de servicio][] muestra la replicación activa de entidades de mensajería.

> [AZURE.NOTE] La replicación activa dobla el número de operaciones; por lo tanto, este enfoque puede suponer un costo mayor.

## Replicación pasiva

En el caso de que no se hayan producido errores, la replicación pasiva solo usa una de las dos entidades de mensajería. Un cliente envía el mensaje a la entidad activa. Si la operación de la entidad activa genera un código de error que indica que es posible que el centro de datos que hospeda la entidad activa no esté disponible, el cliente envía una copia del mensaje a la entidad de reserva. En ese momento, la entidad activa y la de reserva intercambian roles: el cliente remitente considera la anterior entidad activa como la nueva entidad de reserva y la anterior entidad de reserva pasa a ser la nuevo entidad activa. Si ambas operaciones de envío generan un error, no se modifican los roles de las dos entidades y se devuelve un error.

Un cliente recibe mensajes de ambas colas. Dado que es probable que el receptor reciba dos copias del mismo mensaje, este debe suprimir los mensajes duplicados. Puede suprimir duplicados de la misma manera descrita para la replicación activa.

En general, la replicación pasiva es más económica que la activa porque en la mayoría de los casos se realiza una única operación. La latencia, el rendimiento y el costo económico son idénticos para el escenario sin replicación.

Cuando se usa la replicación pasiva, en los siguientes escenarios se pueden perder mensajes o recibirse dos veces:

-   **Demora o pérdida de mensajes**: se supone que el remitente envió correctamente un mensaje m1 a la cola principal y después la cola deja de estar disponible antes de que el receptor reciba m1. El remitente envía otro mensaje m2 a la cola secundaria. Si la cola principal no está disponible temporalmente, el receptor recibe m1 una vez que la cola esté disponible de nuevo. En caso de desastre, puede que el receptor no reciba nunca m1.

-   **Recepción duplicada**: supongamos que el remitente envía un mensaje m a la cola principal. El Bus de servicio procesa m correctamente , pero no envía una respuesta. Una vez que la operación de envío agota el tiempo de espera, el remitente envía una copia idéntica de m a la cola secundaria. Si el receptor puede recibir la primera copia de m antes de que la cola principal deje de estar disponible, el receptor recibe ambas copias de m aproximadamente al mismo tiempo. Si el receptor no recibe la primera copia de m antes de que la cola principal deje de estar disponible, el receptor recibe inicialmente solo la segunda copia de m pero, a continuación, recibe una segunda copia de m cuando la cola principal vuelve a estar disponible.

El ejemplo de [replicación geográfica con mensajes asincrónicos del Bus de servicio][] muestra la replicación pasiva de entidades de mensajería.

## Cola del lado cliente duradera

Si la aplicación tolera que una entidad del Bus de servicio no esté disponible, pero no debe perder mensajes, el remitente puede emplear una cola del lado cliente duradera que almacene localmente todos los mensajes que no se puedan enviar al Bus de servicio. Una vez que la entidad del Bus de servicio vuelva a estar disponible, se envían todos los mensajes almacenados en búfer a esa entidad. El ejemplo de [remitente de mensaje duradero][] implementa dicha cola con la ayuda de MSMQ. Como alternativa, los mensajes pueden escribirse en el disco local.

Una cola del lado cliente duradera conserva el orden de los mensajes y protege la aplicación cliente de las excepciones en caso de que la entidad del Bus de servicio no esté disponible. Se puede usar con transacciones simples y distribuidas.

## Pasos siguientes

Para obtener más información acerca de la recuperación ante desastres, consulte estos artículos:

- [Continuidad de negocio de Base de datos SQL de Azure][]
- [Orientación técnica de la continuidad del negocio de Azure][]

  [Autenticación y autorización de Bus de servicio]: service-bus-authentication-and-authorization.md
  [Entidades de mensajería con particiones]: service-bus-partitioning.md
  [Patrones de mensajería asincrónica y alta disponibilidad]: service-bus-async-messaging.md
  [replicación geográfica con mensajes retransmitidos del Bus de servicio]: http://code.msdn.microsoft.com/Geo-replication-with-16dbfecd
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [BrokeredMessage.Label]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx
  [replicación geográfica con mensajes asincrónicos del Bus de servicio]: http://code.msdn.microsoft.com/Geo-replication-with-f5688664
  [remitente de mensaje duradero]: http://code.msdn.microsoft.com/Service-Bus-Durable-Sender-0763230d
  [Continuidad de negocio de Base de datos SQL de Azure]: ../sql-database/sql-database-business-continuity.md
  [Orientación técnica de la continuidad del negocio de Azure]: https://msdn.microsoft.com/library/azure/hh873027.aspx

<!---HONumber=AcomDC_0128_2016-->