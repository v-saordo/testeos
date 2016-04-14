<properties 
	pageTitle="Uso de colas del Bus de servicio con Python | Microsoft Azure" 
	description="Aprenda a usar las colas de del Bus de servicio de Azure desde Python." 
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
	ms.date="02/08/2016" 
	ms.author="sethm"/>


# Utilización de las colas del Bus de servicio

[AZURE.INCLUDE [servicio de bus de selector de colas](../../includes/service-bus-selector-queues.md)]

Este artículo describe cómo usar las colas del Bus de servicio. Los ejemplos están escritos en Python y usan el [paquete de Azure para Python][]. Entre los escenarios que abarca se incluyen la **creación de colas, el envío y recepción de mensajes** y la **eliminación de colas**.

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

> [AZURE.NOTE] Para instalar Python o el [paquete de Azure para Python][], consulte la [Guía de instalación de Python](../python-how-to-install.md).

## Creación de una cola

El objeto **ServiceBusService** le permite trabajar con colas. Agregue el siguiente código cerca de la parte superior de todo archivo Python en el que desee obtener acceso al Bus de servicio mediante programación:

```
from azure.servicebus import ServiceBusService, Message, Queue
```

El siguiente código crea un objeto **ServiceBusService**. Reemplace `mynamespace`, `sharedaccesskeyname` y `sharedaccesskey` por el espacio de nombres, el valor y el nombre de clave de la firma de acceso compartido (SAS).

```
bus_service = ServiceBusService(
	service_namespace='mynamespace',
	shared_access_key_name='sharedaccesskeyname',
	shared_access_key_value='sharedaccesskey')
```

Los valores para el nombre de clave y el valor de la SAS pueden encontrarse en la información de conexión del [Portal de Azure clásico][] o en el panel **Propiedades** de Visual Studio al seleccionar el espacio de nombres del Bus de servicio en el Explorador de servidores (como se muestra en la sección anterior).

```
bus_service.create_queue('taskqueue')
```

**create\_queue** también admite opciones adicionales, que le permiten invalidar la configuración predeterminada de las colas, como el período de vida (TTL) de los mensajes o el tamaño máximo de las colas. En el siguiente ejemplo se establece el tamaño máximo de las colas en 5 GB y el valor de TTL en 1 minuto:

```
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## mensajes a una cola

Para enviar un mensaje a una cola del Bus de servicio, la aplicación debe llamar al método **send\_queue\_message** del objeto **ServiceBusService**.

El siguiente ejemplo demuestra cómo se debe enviar un mensaje de prueba a la cola llamada *taskqueue con* **send\_queue\_message**:

```
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Las colas del Bus de servicio admiten mensajes con un tamaño máximo de 256 KB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 KB). No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un límite de 5 GB. Para obtener más información sobre las cuotas, consulte [Colas de Bus de servicio y colas de Azure][].

## mensajes de una cola

Los mensajes se reciben de una cola utilizando el método **receive\_queue\_message** del objeto **ServiceBusService**.

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

Los mensajes se eliminan de la cola a medida que se leen cuando el parámetro **peek\_lock** está establecido en **False**. Puede leer (ojear) y bloquear el mensaje sin eliminarlo de la cola si establece el parámetro **peek\_lock** en **True**.

El comportamiento por el que los mensajes se eliminan tras leerlos como parte del proceso de recepción es el modelo más sencillo y el que mejor funciona en aquellas situaciones en las que una aplicación puede tolerar que no se procese un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el Bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

Si el parámetro **peek\_lock** está establecido en **True**, el proceso de recepción se convierte en una operación en dos fases que hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su futuro procesamiento), completa la segunda fase del proceso de recepción llamando al método **delete** en el objeto **Message**. El método **delete** marcará el mensaje como consumido y lo eliminará de la cola.

```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## Actuación ante errores de la aplicación y mensajes que no se pueden leer

El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción es incapaz de procesar el mensaje, entonces puede llamar al método **unlock** del objeto **Message**. Esto hará que el Bus de servicio desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación sufra un error después de procesar el mensaje y antes de llamar al método **delete**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **Al menos un procesamiento**, es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue usando la propiedad **MessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

## Pasos siguientes

Ahora que conoce los fundamentos de las colas del Bus de servicio, siga estos vínculos para obtener más información.

-   Consulte [Colas, temas y suscripciones][].

[Portal de Azure clásico]: http://manage.windowsazure.com
[paquete de Azure para Python]: https://pypi.python.org/pypi/azure
[Colas, temas y suscripciones]: service-bus-queues-topics-subscriptions.md
[Colas de Bus de servicio y colas de Azure]: service-bus-azure-and-service-bus-queues-compared-contrasted.md#capacity-and-quotas
 

<!---HONumber=AcomDC_0211_2016-->