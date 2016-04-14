<properties
	pageTitle="Uso de colas de Bus de servicio con Ruby | Microsoft Azure"
	description="Obtenga información acerca de cómo usar las colas del Bus de servicio en Azure. Ejemplos de código escritos en Ruby."
	services="service-bus"
	documentationCenter="ruby"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="12/09/2015"
	ms.author="sethm"/>

# Utilización de las colas del Bus de servicio

[AZURE.INCLUDE [servicio de bus de selector de colas](../../includes/service-bus-selector-queues.md)]

Esta guía describe cómo utilizar las colas del Bus de servicio. Los ejemplos están escritos en Ruby y usan la gema de Azure. Entre los escenarios que abarca se incluyen la **creación de colas, el envío y recepción de mensajes** y la **eliminación de colas**. Para obtener más información acerca de las colas, consulte la sección [Pasos siguientes](#next-steps).

## ¿Qué son las colas del Bus de servicio?

Las colas del Bus de servicio son compatibles con el modelo de comunicación de *mensajería asíncrona*. Cuando se usan colas, los componentes de una aplicación distribuida no se comunican directamente entre sí, sino que intercambian mensajes a través de una cola, que actúa como un intermediario. El productor del mensaje (remitente) manda un mensaje a la cola y, a continuación sigue con su procesamiento. De forma asíncrona, el destinatario del mensaje (receptor) extrae el mensaje de la cola y lo procesa. El productor no tiene que esperar una respuesta del destinatario para continuar el proceso y el envío de más mensajes. Las colas ofrecen una entrega de mensajes según el modelo **El primero en entrar es el primero en salir (FIFO)** a uno o más destinatarios de la competencia. Es decir, normalmente los receptores reciben y procesan los mensajes en el orden en el que se agregaron a la cola y solo un destinatario del mensaje recibe y procesa cada uno de los mensajes.

![QueueConcepts](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

Las colas del Bus de servicio son una tecnología de uso general que puede utilizarse en una variedad de escenarios:

-   Comunicación entre los roles de trabajo y web en una [aplicación de Azure de niveles múltiples](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md).
-   Comunicación entre aplicaciones locales y aplicaciones hospedadas de Azure en una [solución híbrida](service-bus-dotnet-hybrid-app-using-service-bus-relay.md).
-   Comunicación entre componentes de una aplicación distribuida que se ejecuta en local en distintas organizaciones o departamentos de una organización.

El uso de las colas puede permitirle escalar mejor sus aplicaciones horizontalmente y dotar de más resiliencia a su arquitectura.

## Creación de un espacio de nombres de servicio

Para comenzar a usar colas del Bus de servicio en Azure, primero debe crear un espacio de nombres de servicio. Un espacio de nombres de servicio proporciona un contenedor con un ámbito para el desvío de recursos del Bus de servicio en la aplicación. Debe crear el espacio de nombres a través de la interfaz de línea de comandos porque el Portal de Azure clásico no crea el espacio de nombres con una conexión de ACS.

Para crear un nombre de espacio de servicio:

1. Abra una consola de Azure PowerShell.

2. Escriba el comando siguiente para crear un espacio de nombres del Bus de servicio. Proporcione su propio valor de espacio de nombres y especifique la misma región que la aplicación.

    ```
	New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

    ![Create Namespace](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)
	```

## Obtener credenciales de administración para el espacio de nombres

Para realizar operaciones de administración (como la creación de una cola) en el nuevo espacio de nombres, debe obtener las credenciales de administración para el espacio de nombres.

El cmdlet de PowerShell que ejecutó para crear el espacio de nombres del bus de servicio de Azure muestra la clave que puede usar para administrar el espacio de nombres. Copie el valor **DefaultKey**. Más adelante en este tutorial usará este valor en su código.

![Copiar clave](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

> [AZURE.NOTE]También puede encontrar esta clave si inicia sesión en el [Portal de Azure clásico](http://manage.windowsazure.com/) y va a la información de conexión para el espacio de nombres del Bus de servicio.

## Creación de una aplicación de Ruby

Cree una aplicación de Ruby. Para obtener instrucciones, consulte [Creación de una aplicación de Ruby en Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Configuración de la aplicación para usar el Bus de servicio

Para usar el Bus de servicio de Azure, descargue y use el paquete Ruby Azure, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### Uso de RubyGems para obtener el paquete

1. Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).

2. Escriba "gem install azure" en la ventana de comandos para instalar la gema y las dependencias.

### Importación del paquete

Con el editor de texto que prefiera, agregue lo siguiente al principio del archivo de Ruby en el que pretenda utilizar el almacenamiento:

```
require "azure"
```

## Configuración de una conexión del Bus de servicio de Azure

El módulo Azure leerá las variables de entorno **AZURE\_SERVICEBUS\_NAMESPACE** y **AZURE\_SERVICEBUS\_ACCESS\_KEY** para obtener la información necesaria para conectarse al espacio de nombres del Bus de servicio. Si no configura estas variables de entorno, debe especificar la información del espacio de nombres antes de usar **Azure::ServiceBusService** con el siguiente código:

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Defina el valor del espacio de nombres en el valor que creó en lugar de hacerlo en la dirección URL completa. Por ejemplo, use **"yourexamplenamespace"** y no "yourexamplenamespace.servicebus.windows.net".

## Creación de una cola

El objeto **Azure::ServiceBusService** le permite trabajar con colas. Para crear una cola, use el método **create\_queue()**. En el siguiente ejemplo se crea una cola o se imprime el error.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

También puede pasar un objeto **Azure::ServiceBus::Queue** con opciones adicionales, lo que le permite reemplazar la configuración de la cola predeterminada, como el período de vida del mensaje o el tamaño de cola máximo. En el siguiente ejemplo se muestra cómo establecer el tamaño máximo de las colas en 5 GB y el período de vida en 1 minuto:

```
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## Envío de mensajes a una cola

Para enviar un mensaje a una cola del Bus de servicio, la aplicación llama al método **send\_queue\_message()** en el objeto **Azure::ServiceBusService**. Los mensajes enviados a las colas del Bus de servicio (y recibidos de ellas) son objetos **Azure::ServiceBus::BrokeredMessage**, y tienen un conjunto de propiedades estándar (como **label** y **time\\to\\live**), un diccionario que se usa para mantener las propiedades específicas de la aplicación personalizadas y un conjunto de datos arbitrarios de la aplicación. Una aplicación puede establecer el cuerpo del mensaje pasando un valor de cadena como mensaje, con lo que las propiedades estándar requeridas adquieren valores predeterminados.

El ejemplo siguiente demuestra cómo enviar un mensaje de prueba a la cola llamada "test-queue" usando **send\_queue\_message()**:

```
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Las colas del Bus de servicio admiten mensajes con un tamaño máximo de 256 KB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 KB). No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un límite de 5 GB.

## Recepción de mensajes de una cola

Los mensajes se reciben de una cola utilizando el método **receive\_queue\_message()** del objeto **Azure::ServiceBusService**. De forma predeterminada, los mensajes se leen y bloquean sin que se eliminen de la cola. Sin embargo, puede eliminar mensajes de la cola a medida que se leen estableciendo la opción **:peek\_lock** en **false**.

El comportamiento predeterminado convierte la lectura y eliminación en una operación de dos fases que también hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción llamando al método **delete\_queue\_message()** y facilitando el mensaje que se va a eliminar a modo de parámetro. El método **delete\_queue\_message()** marcará el mensaje como consumido y lo eliminará de la cola.

Si el parámetro **:peek\_lock** se establece en **false**, la lectura y eliminación del mensaje se convierte en el modelo más simple y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el Bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el ejemplo siguiente se muestra cómo recibir y procesar mensajes mediante **receive\_queue\_message()**. El ejemplo primero recibe y elimina un mensaje mediante **:peek\_lock** establecido en **false**, a continuación, recibe otro mensaje y, a continuación, elimina el mensaje mediante **delete\_queue\_message()**:

```
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## Actuación ante errores de la aplicación y mensajes que no se pueden leer

El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción es incapaz de procesar el mensaje, entonces puede llamar al método **unlock\_queue\_message()** del objeto **Azure::ServiceBusService**. Esto hará que el Bus de servicio desbloquee el mensaje de la cola y esté disponible para volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay otro tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación se bloquea), entonces el Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación sufra un error después de procesar el mensaje y antes de llamar al método **delete\_queue\_message()**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Esta posibilidad habitualmente se denomina **Al menos un procesamiento**, es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue usando la propiedad **message\_id** del mensaje, que permanece constante en todos los intentos de entrega.

## Pasos siguientes

Ahora que conoce los fundamentos de las colas del Bus de servicio, siga estos vínculos para obtener más información.

-   Información general de [colas, temas y suscripciones](service-bus-queues-topics-subscriptions.md).
-   Visite el repositorio del [SDK de Azure para Ruby](https://github.com/Azure/azure-sdk-for-ruby) en GitHub.

Para ver la comparación entre Colas del Bus de servicio de Azure de este artículo y Colas de Azure del artículo [Uso del servicio Cola de Azure](/develop/ruby/how-to-guides/queue-service/), consulte [Colas de Azure y Colas del Bus de servicio de Azure: comparación y diferencias](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
 

<!---HONumber=AcomDC_1217_2015-->