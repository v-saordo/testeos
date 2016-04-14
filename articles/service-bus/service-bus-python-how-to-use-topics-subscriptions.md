<properties 
	pageTitle="Uso de Bus de servicio con Python | Microsoft Azure" 
	description="Aprenda a usar los temas de Bus de servicio de Azure y las suscripciones de Python." 
	services="service-bus" 
	documentationCenter="python" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="01/26/2016" 
	ms.author="sethm"/>

# Uso de temas/suscripciones del Bus de servicio

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

En este artículo se describe cómo usar los temas y las suscripciones del Bus de servicio. Los ejemplos están escritos en Python y usan el [paquete de Azure para Python][]. Entre los escenarios tratados se incluyen **la creación de temas y suscripciones**, **la creación de filtros de suscripción**, **el envío de mensajes a un tema**, **la recepción de mensajes de una suscripción** y **la eliminación de temas y suscripciones**. Para obtener más información acerca de los temas y las suscripciones, consulte la sección [Pasos siguientes](#next-steps).

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

**Nota:** si necesita instalar Python o el [paquete de Azure para Python][], consulte la [Guía de instalación de Python](../python-how-to-install.md).

## de un tema

El objeto **ServiceBusService** le permite trabajar con temas. Agregue lo siguiente cerca de la parte superior de todo archivo Python en el que desee obtener acceso al Bus de servicio mediante programación:

```
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

El siguiente código crea un objeto **ServiceBusService**. Reemplace `mynamespace`, `sharedaccesskeyname` y `sharedaccesskey` por el espacio de nombres real, el valor y el nombre de clave de la firma de acceso compartido (SAS).

```
bus_service = ServiceBusService(
	service_namespace='mynamespace',
	shared_access_key_name='sharedaccesskeyname',
	shared_access_key_value='sharedaccesskey')
```

Puede obtener los valores para el valor y el nombre de clave de SAS en el [Portal de Azure clásico][] desde la ventana **Información de conexión**.

```
bus_service.create_topic('mytopic')
```

**create\_topic** también admite opciones adicionales, lo que permite invalidar la configuración predeterminada de los temas, como el período de vida de los mensajes o el tamaño máximo de los temas. En el siguiente ejemplo, se establece el tamaño máximo de los temas en 5 GB y el valor del período de vida (TTL) en 1 minuto:

```
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## Creación de suscripciones

Las suscripciones a los temas también se crean con el objeto **ServiceBusService**. A las suscripciones se les asigna un nombre y pueden tener un filtro opcional que restrinja el conjunto de mensajes que pasan a su cola virtual.

> [AZURE.NOTE] Las suscripciones son permanentes y seguirán existiendo hasta que se eliminen o se elimine el tema al que están suscritas.

### Creación de una suscripción con el filtro predeterminado (MatchAll)

El filtro predeterminado **MatchAll** se usa en caso de que no se haya especificado ninguno al crear una suscripción. Al usar el filtro **MatchAll**, todos los mensajes publicados en el tema se colocan en la cola virtual de la suscripción. En el ejemplo siguiente se crea una suscripción llamada “AllMessages” que usa el filtro predeterminado **MatchAll**.

```
bus_service.create_subscription('mytopic', 'AllMessages')
```

### Creación de suscripciones con filtros

También puede definir filtros que le permitan especificar qué mensajes enviados a un tema deben mostrarse dentro de una suscripción a un tema determinado.

El tipo de filtro más flexible compatible con las suscripciones es **SqlFilter**, que implementa un subconjunto de SQL92. Los filtros de SQL operan en las propiedades de los mensajes que se publican en el tema. Para obtener más información acerca de las expresiones que se pueden usar con un filtro de SQL, consulte la sintaxis de [SqlFilter.SqlExpression][].

Es posible agregar filtros a una suscripción a través del método **create\_rule** del objeto **ServiceBusService**. Este método le permite agregar nuevos filtros a una suscripción existente.

> [AZURE.NOTE] Debido a que el filtro predeterminado se aplica automáticamente a todas las suscripciones nuevas, debe eliminar primero el filtro predeterminado **MatchAll** si no quiere que este invalide a los demás filtros que especifique. Puede eliminar la regla predeterminada utilizando el método **delete\_rule** del objeto **ServiceBusService**.

En el ejemplo siguiente, se crea una suscripción denominada `HighMessages` con un objeto **SqlFilter** que solo selecciona los mensajes con una propiedad **messagenumber** personalizada cuyo valor sea mayor que 3:

```
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Del mismo modo, en el ejemplo que aparece a continuación, se crea una suscripción llamada `LowMessages` con **SqlFilter** que solo selecciona los mensajes con una propiedad **messagenumber** cuyo valor sea menor o igual a 3:

```
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Cuando se envíe un mensaje a `mytopic`, este se entregará siempre a los receptores suscritos a la suscripción de tema **AllMessages**, y se entregará de forma selectiva a los receptores suscritos a las suscripciones de tema **HighMessages** y **LowMessages** (según el contenido del mensaje).

## de mensajes a un tema

Para enviar un mensaje a un tema del bus de servicio, la aplicación debe utilizar el método **send\_topic\_message** del objeto **ServiceBusService**.

En el ejemplo siguiente se demuestra cómo enviar cinco mensajes de prueba a `mytopic`. Observe que el valor de la propiedad **messagenumber** de cada mensaje varía en función de la iteración del bucle (así se determinará qué suscripciones lo reciben):

```
for i in range(5):
	msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
	bus_service.send_topic_message('mytopic', msg)
```

Los temas del Bus de servicio admiten mensajes con un tamaño máximo de 256 MB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 MB). No hay límite para el número de mensajes que contiene un tema, pero hay un tope para el tamaño total de los mensajes contenidos en un tema. El tamaño de los temas se define en el momento de la creación (el límite máximo es de 5 GB). Para obtener más información sobre las cuotas, consulte [Colas de Bus de servicio y colas de Azure][].

## de mensajes de una suscripción

Los mensajes se reciben de una suscripción utilizando el método **receive\_subscription\_message** del objeto **ServiceBusService**:

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Los mensajes se borran de la suscripción a medida que se leen cuando el parámetro **peek\_lock** se establece en **False**. Puede leer y bloquear los mensajes sin eliminarlos de la cola si establece el parámetro opcional **peek\_lock** en **True**.

El comportamiento por el que los mensajes se eliminan tras leerlos como parte del proceso de recepción es el modelo más sencillo y el que mejor funciona en aquellas situaciones en las que una aplicación puede tolerar que no se procese un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el Bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

Si el parámetro **peek\_lock** está establecido en **True**, el proceso de recepción se convierte en una operación en dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su futuro procesamiento), completa la segunda fase del proceso de recepción llamando al método **delete** en el objeto **Message**. El método **delete** marcará el mensaje como consumido y lo eliminará de la suscripción.

```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## Actuación ante errores de la aplicación y mensajes que no se pueden leer

El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción es incapaz de procesar el mensaje, entonces puede llamar al método **unlock** del objeto **Message**. Esto hará que el Bus de servicio desbloquee el mensaje de la suscripción y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay otro tiempo de espera asociado con un mensaje bloqueado en la suscripción y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación se bloquea), entonces el bus de servicio desbloquea en mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación sufra un error después de procesar el mensaje y antes de llamar al método **delete**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **Al menos un procesamiento**, es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue usando la propiedad **MessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

## Eliminación de temas y suscripciones

Los temas y las suscripciones son permanentes, por lo que deben eliminarse explícitamente a través del [Portal de Azure clásico][] o mediante programación. En el ejemplo siguiente, se muestra cómo eliminar el tema llamado `mytopic`:

```
bus_service.delete_topic('mytopic')
```

Al eliminar un tema también se eliminan todas las suscripciones que estén registradas con él. También se pueden eliminar las suscripciones de forma independiente. El código siguiente muestra cómo eliminar una suscripción llamada `HighMessages` del tema `mytopic`:

```
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## Pasos siguientes

Ahora que conoce los fundamentos de los temas del Bus de servicio, siga estos vínculos para obtener más información.

-   Consulte [Colas, temas y suscripciones][].
-   Referencia para [SqlFilter.SqlExpression][].

[Portal de Azure clásico]: http://manage.windowsazure.com
[paquete de Azure para Python]: https://pypi.python.org/pypi/azure
[Colas, temas y suscripciones]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Colas de Bus de servicio y colas de Azure]: service-bus-azure-and-service-bus-queues-compared-contrasted.md#capacity-and-quotas

<!---HONumber=AcomDC_0128_2016-->