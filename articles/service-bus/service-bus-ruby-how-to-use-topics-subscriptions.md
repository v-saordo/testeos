<properties
	pageTitle="Uso de temas del Bus de servicio (Ruby) | Microsoft Azure"
	description="Aprenda a usar los temas y las suscripciones del Bus de servicio de Azure. Los ejemplos de código están escritos para aplicaciones Ruby."
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

# Uso de temas/suscripciones del Bus de servicio

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

En esta guía se describe cómo usar los temas y las suscripciones del Bus de servicio desde aplicaciones Ruby. Entre los escenarios tratados se incluye la **creación de temas y suscripciones, la creación de filtros de suscripción, el envío de mensajes** a un tema, la **recepción de mensajes de una suscripción** y la **eliminación de temas y suscripciones**. Para obtener más información acerca de los temas y las suscripciones, consulte la sección [Pasos siguientes](#next-steps).

## Temas y suscripciones del Bus de servicio

Las suscripciones y los temas del Bus de servicio son compatibles con el modelo de **comunicación de mensajería de publicación/suscripción**. Cuando se usan temas y suscripciones, los componentes de una aplicación distribuida no se comunican directamente entre ellos, sino que intercambian mensajes a través de un tema, que actúa como un intermediario.

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

A diferencia de las colas del Bus de servicio, en las que un solo destinatario procesa cada mensaje, los temas y las suscripciones proporcionan una forma de comunicación **uno a varios** mediante un patrón de publicación/suscripción. Es posible registrar varias suscripciones en un tema. Cuando un mensaje se envía a un tema, pasa a estar disponible para cada suscripción para la administración o el procesamiento de manera independiente.

Una suscripción al tema funciona de forma similar a una cola virtual que recibe copias de los mensajes que se enviaron al tema. Opcionalmente, puede registrar reglas de filtros para un tema por suscripción, lo que le permite filtrar/restringir qué mensajes para un tema reciben las suscripciones a un tema.

Las suscripciones y temas del Bus de servicio le permiten escalar para realizar el procesamiento de un número bastante elevado de mensajes en una serie muy amplia de usuarios y aplicaciones.

## Creación de un espacio de nombres de servicio

Para comenzar a usar colas del Bus de servicio en Azure, primero debe crear un espacio de nombres de servicio. Un espacio de nombres proporciona un contenedor con un ámbito para el desvío de recursos del bus de servicio en la aplicación. Debe crear el espacio de nombres a través de la interfaz de línea de comandos porque el [Portal de Azure clásico][] no crea el espacio de nombres con una conexión de ACS.

Para crear un espacio de nombres:

1. Abra una consola de Azure PowerShell.

2. Escriba el comando para crear un espacio de nombres tal como se muestra a continuación. Proporcione su propio valor de espacio de nombres y especifique la misma región que la aplicación.

      New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

      ![Crear espacio de nombres](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## Obtención de credenciales de administración predeterminadas para el espacio de nombres

Para realizar operaciones de administración (como la creación de una cola) en el nuevo espacio de nombres, debe obtener las credenciales de administración para el espacio de nombres.

El cmdlet de PowerShell que ejecutó para crear el espacio de nombres del Bus de servicio muestra la clave que puede usar para administrar el espacio de nombres. Copie el valor **DefaultKey**. Más adelante en este tutorial usará este valor en su código.

      ![Copy key](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [AZURE.NOTE]También puede encontrar esta clave si inicia sesión en el [Portal de Azure clásico][] y va a la información de conexión para el espacio de nombres.

## Creación de una aplicación de Ruby

Para obtener instrucciones, consulte [Creación de una aplicación de Ruby en Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Configuración de la aplicación para usar el Bus de servicio

Para usar el Bus de servicio de Azure, descargue y use el paquete Ruby Azure, que incluye un conjunto de útiles bibliotecas que se comunican con los servicios REST de almacenamiento.

### Uso de RubyGems para obtener el paquete

1. Use una interfaz de línea de comandos como **PowerShell** (Windows), **Terminal** (Mac) o **Bash** (Unix).

2. Escriba "gem install azure" en la ventana de comandos para instalar la gema y las dependencias.

### Importación del paquete

Con el editor de texto que prefiera, agregue lo siguiente al principio del archivo de Ruby en el que pretenda utilizar el almacenamiento:

    require "azure"

## Configuración de una conexión del Bus de servicio

El módulo Azure leerá las variables de entorno **AZURE\_SERVICEBUS\_NAMESPACE** y **AZURE\_SERVICEBUS\_ACCESS\_KEY** para obtener la información necesaria para conectarse al espacio de nombres. Si no configura estas variables de entorno, debe especificar la información del espacio de nombres antes de usar **Azure::ServiceBusService** con el siguiente código:

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

Defina el valor del espacio de nombres en el valor que creó en lugar de hacerlo en la dirección URL completa. Por ejemplo, use **"yourexamplenamespace"** y no "yourexamplenamespace.servicebus.windows.net".

## de un tema

El objeto **Azure::ServiceBusService** le permite trabajar con temas. El siguiente código crea un objeto **Azure::ServiceBusService**. Para crear un tema, use el método **create\_topic()**. En el siguiente ejemplo se crea un tema o se imprime el error si lo hubiera.

	azure_service_bus_service = Azure::ServiceBusService.new
	begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

También puede pasar un objeto **Azure::ServiceBus::Topic** con opciones adicionales, lo que le permite reemplazar la configuración de la cola predeterminada, como el período de vida del mensaje o el tamaño de cola máximo. En el siguiente ejemplo se muestra cómo establecer el tamaño máximo de las colas en 5 GB y el período de vida en 1 minuto:

	topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## Creación de suscripciones

Las suscripciones a temas también se crean con el objeto **Azure::ServiceBusService**. A las suscripciones se les asigna un nombre y pueden tener un filtro opcional que restrinja el conjunto de mensajes que pasan a su cola virtual.

Las suscripciones son permanentes y seguirán existiendo hasta que se eliminen o se elimine el tema al que están asociadas. Si la aplicación contiene lógica para crear una suscripción, primero debe comprobar si esta ya existe utilizando el método getSubscription.

### Creación de una suscripción con el filtro predeterminado (MatchAll)

El filtro predeterminado **MatchAll** se usa en caso de que no se haya especificado ninguno al crear una suscripción. Al usar el filtro **MatchAll**, todos los mensajes publicados en el tema se colocan en la cola virtual de la suscripción. En el ejemplo siguiente se crea una suscripción llamada "all-messages" que usa el filtro predeterminado **MatchAll**.

	subscription = azure_service_bus_service.create_subscription("test-topic",
	  "all-messages")

### Creación de suscripciones con filtros

También puede definir filtros que le permitan especificar qué mensajes enviados a un tema deben mostrarse dentro de una suscripción a una suscripción determinada.

El tipo de filtro más flexible compatible con las suscripciones es **Azure::ServiceBus::SqlFilter**, que implementa un subconjunto de SQL92. Los filtros de SQL operan en las propiedades de los mensajes que se publican en el tema. Para obtener más información acerca de las expresiones que se pueden usar con un filtro de SQL, revise la sintaxis de [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx).

Es posible agregar filtros a una suscripción a través del método **create\_rule()** del objeto **Azure::ServiceBusService**. Este método le permite agregar nuevos filtros a una suscripción existente.

Ya que el filtro predeterminado se aplica automáticamente a todas las suscripciones nuevas, primero debe eliminar el filtro predeterminado **MatchAll** si no quiere que este anule todos los otros filtros que especifique. Puede eliminar la regla predeterminada utilizando el método **delete\_rule()** en el objeto **Azure::ServiceBusService**.

En el ejemplos siguiente se crea una suscripción llamada "high-messages" con un **Azure::ServiceBus::SqlFilter** que solo selecciona los mensajes con una propiedad **message\_number** personalizada superior a 3:

	subscription = azure_service_bus_service.create_subscription("test-topic",
	  "high-messages")
	azure_service_bus_service.delete_rule("test-topic", "high-messages",
	  "$Default")

	rule = Azure::ServiceBus::Rule.new("high-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "high-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({
	  :sql_expression => "message_number > 3" })
	rule = azure_service_bus_service.create_rule(rule)

Del mismo modo, en el ejemplo que aparece a continuación se crea una suscripción llamada "low-messages" con un **Azure::ServiceBus::SqlFilter** que solo selecciona los mensajes con una propiedad **message\_number** igual a 3 o menor:

	subscription = azure_service_bus_service.create_subscription("test-topic",
	  "low-messages")
	azure_service_bus_service.delete_rule("test-topic", "low-messages",
	  "$Default")

	rule = Azure::ServiceBus::Rule.new("low-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "low-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({
	  :sql_expression => "message_number <= 3" })
	rule = azure_service_bus_service.create_rule(rule)

Ahora, cuando se envíe un mensaje a "test-topic", siempre se entregará a los destinatarios suscritos a la suscripción de tema "all-messages" y, selectivamente, a los destinatarios suscritos a la suscripción de tema "high-messages" o "low-messages" (dependiendo del contenido del mensaje).

## de mensajes a un tema

Para enviar un mensaje a un tema del Bus de servicio, la aplicación debe utilizar el método **send\_topic\_message()** en el objeto **Azure::ServiceBusService**. Los mensajes enviados a los temas del Bus de servicio son instancias de los objetos **Azure::ServiceBus::BrokeredMessage**. Los objetos **Azure::ServiceBus::BrokeredMessage** cuentan con un conjunto de propiedades estándar (como **label** y **time\_to\_live**), un diccionario que se usa para mantener las propiedades personalizadas específicas de la aplicación y un conjunto de datos arbitrarios de aplicaciones. Una aplicación puede establecer el cuerpo del mensaje pasando un valor de cadena al método **send\_topic\_message()**, con lo que las propiedades estándar requeridas adquieren valores predeterminados.

En el siguiente ejemplo se demuestra cómo enviar cinco mensajes de prueba a "test-topic". Tenga en cuenta que el valor de la propiedad personalizada **message\_number** de cada mensaje varía en función de la iteración del bucle (determina qué suscripción lo recibe):

	5.times do |i|
	  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
	    { :message_number => i })
	  azure_service_bus_service.send_topic_message("test-topic", message)
	end

Los temas del Bus de servicio admiten mensajes con un tamaño máximo de 256 MB (el encabezado, que incluye las propiedades estándar y personalizadas de la aplicación, puede tener como máximo un tamaño de 64 MB). No hay límite para el número de mensajes que contiene un tema, pero hay un tope para el tamaño total de los mensajes contenidos en un tema. El tamaño de los temas se define en el momento de la creación (el límite máximo es de 5 GB).

## de mensajes de una suscripción

Los mensajes se reciben de una suscripción utilizando el método **receive\_subscription\_message()** del **objeto Azure::ServiceBusService**. De forma predeterminada, los mensajes se leen (máximo) y bloquean sin que se eliminen de la suscripción. Puede leer y eliminar el mensaje de la suscripción estableciendo la opción **peek\_lock** en **false**.

El comportamiento predeterminado convierte la lectura y eliminación en una operación de dos fases que también hace posible admitir aplicaciones que no toleran la pérdida de mensajes. Cuando el Bus de servicio recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma fiable para su futuro procesamiento), completa la segunda fase del proceso de recepción llamando al método **delete\_subscription\_message()** y facilitando el mensaje que se va a eliminar a modo de parámetro. El método **delete\_subscription\_message()** marcará el mensaje como consumido y lo eliminará de la suscripción.

Si el parámetro **:peek\_lock** se establece en **false**, la lectura y eliminación del mensaje se convierte en el modelo más simple y funciona mejor para los escenarios en los que una aplicación puede tolerar no procesar un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como el Bus de servicio habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

En el ejemplo que aparece a continuación, se indica cómo se pueden recibir y procesar los mensajes usando **receive\_subscription\_message()**. El ejemplo primero recibe y elimina un mensaje de la suscripción "low-messages" mediante :**peek\_lock** establecido en **false**, a continuación, recibe otro mensaje de "high-messages" y, a continuación, elimina el mensaje mediante **delete\_subscription\_message()**:

    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

## errores de la aplicación y mensajes que no se pueden leer

El Bus de servicio proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción no puede procesar el mensaje, entonces puede llamar al método **unlock\_subscription\_message()** del objeto **Azure::ServiceBusService**. Esto hará que el bus de servicio desbloquee el mensaje de la suscripción y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la suscripción y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces el Bus de servicio desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación sufra un error después de procesar el mensaje y antes de llamar al método **delete\_subscription\_message()**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **Al menos un procesamiento**, es decir, cada mensaje se procesará al menos una vez; aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. Esta lógica suele conseguirse usando la propiedad **message\_id** del mensaje, que permanecerá constante en todos los intentos de entrega.

## Eliminación de temas y suscripciones

Los temas y las suscripciones son permanentes, por lo que deben eliminarse explícitamente a través del [Portal de Azure clásico](https://manage.windowsazure.com) o mediante programación. En el ejemplo siguiente se muestra cómo eliminar el tema llamado "test-topic".

	azure_service_bus_service.delete_topic("test-topic")

Al eliminar un tema también se eliminan todas las suscripciones que estén registradas con él. También se pueden eliminar las suscripciones de forma independiente. El siguiente código muestra cómo eliminar la suscripción con el nombre "high-messages" del tema "test-topic":

	azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## Pasos siguientes

Ahora que conoce los fundamentos de los temas del Bus de servicio, siga estos vínculos para obtener más información.

-   Consulte [Colas, temas y suscripciones](service-bus-queues-topics-subscriptions.md).
-   Referencia de API para [Clase SqlFilter](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx).
-	Visite el repositorio del [SDK de Azure para Ruby](https://github.com/Azure/azure-sdk-for-ruby) en GitHub.
 
[Portal de Azure clásico]: http://manage.windowsazure.com

<!---HONumber=AcomDC_1217_2015-->