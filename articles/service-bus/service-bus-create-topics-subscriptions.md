<properties 
   pageTitle="Creación de aplicaciones que usan temas y suscripciones del Bus de servicio | Microsoft Azure"
   description="Introducción a las capacidades de publicación-suscripción que ofrecen los temas y suscripciones del Bus de servicio."
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

# Creación de aplicaciones que usan temas y suscripciones del Bus de servicio

El Bus de servicio de Azure admite un conjunto de tecnologías middleware orientadas a mensajes basadas en la nube, entre las que se incluyen una cola de mensajes de confianza y una mensajería de publicación/suscripción duradera. Este artículo se basa en la información proporcionada en [Creación de aplicaciones que usan colas del Bus de servicio](service-bus-create-queues.md) y ofrece una introducción a las capacidades de publicación/suscripción que ofrecen los temas de Bus de servicio.

## Escenario minorista en evolución

Este artículo se prolonga el escenario minorista que se usó en [Creación de aplicaciones que usan colas del Bus de servicio](service-bus-create-queues.md). Recuerde que los datos de ventas de los terminales de los puntos de venta (PDV) individuales deben enrutarse a un sistema de gestión de inventarios que usa esos datos para determinar cuándo hay que reponer las existencias. Cada terminal de PDV informa de sus datos de venta mediante el envío de mensajes a la cola **DataCollectionQueue**, donde permanecen hasta que los recibe el sistema de gestión de inventarios, como se muestra aquí:

![Service-Bus1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

Para desarrollar este escenario, se ha agregado un nuevo requisito al sistema: el propietario del almacén desea poder supervisar el rendimiento de la misma en tiempo real.

Para cumplir este requisito, el sistema debe "iniciar" el flujo de datos de ventas. Deseamos que los mensajes enviados por los terminales de los PDV se envíen al sistema de gestión de inventarios como antes, pero queremos otra copia de cada mensaje que podamos usar para presentar la vista de panel al propietario del almacén.

En una situación como ésta, en la que requiere cada mensaje lo consuman varias partes, puede usar los *temas* de Bus de servicio. Los temas proporcionan un patrón de publicación/suscripción en el que cada mensaje publicado está disponible en una o varias suscripciones registradas en el tema. En cambio, con las colas cada mensaje lo recibe un único consumidor.

Los mensajes se envían a los tema de la misma manera que a las colas. Sin embargo, no se reciben mensajes del tema directamente, sino que se reciben de las suscripciones. Puede considerarse que una suscripción a un tema es una cola virtual que recibe copias de los mensajes que se envían al tema. Los mensajes se reciben de las suscripciones exactamente de la misma forma que de las colas.

Volviendo al escenario minorista, la cola se reemplaza por un tema y se agrega una suscripción que usará el componente del sistema de gestión de inventarios. El sistema aparece ahora como sigue:

![Service-Bus2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

Aquí la configuración se comporta de forma idéntica al diseño anterior basado en cola. Es decir, los mensajes enviados al tema se enrutan a la suscripción **Inventory**, desde donde los consume el **sistema de gestión de inventarios**.

Para admitir el panel de Administración, creamos una segunda suscripción en el tema, como se muestra aquí:

![Service-Bus3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

Con esta configuración, cada mensaje de los terminales de los PDV queda a disposición de las suscripciones **Dashboard** e **Inventory** suscripciones.

## Visualización del código

En [Creación de aplicaciones que usan colas del Bus de servicio](service-bus-create-queues.md), se describe cómo registrarse en una cuenta de Bus de servicio y crear un espacio de nombres del servicio. Para usar un espacio de nombres de Bus de servicio, una aplicación debe hacer referencia el ensamblado del Bus de servicio, en concreto Microsoft.ServiceBus.dll. La manera más fácil de hacer referencia a las dependencias de Bus de servicio es instalar el [paquete Nuget](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) del Bus de servicio. El ensamblado también se puede encontrar como parte del SDK de Azure. La descarga está disponible en la [página de descarga del SDK de Azure](https://azure.microsoft.com/downloads/).

### Creación del tema y las suscripciones

Las operaciones de administración de las entidades de mensajería de Bus de servicio (colas y temas de publicación/suscripción) se realizan a través de la clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Se requieren credenciales apropiadas para crear una instancia de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) para un espacio de nombres concreto. Bus de servicio usa un modelo de seguridad basado en la [firma de acceso compartido (SAS)](service-bus-sas-overview.md). La clase [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representa un proveedor de tokens de seguridad con métodos de generador integrados que devuelven algunos proveedores de tokens conocidos. Vamos a usar un método [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) para retener las credenciales de SAS. A continuación, se construye la instancia de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) con la dirección base del espacio de nombres de Bus de servicio y el proveedor de tokens.

La clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) proporciona métodos para crear, enumerar y eliminar entidades de mensajes. El siguiente código muestra cómo se crea la instancia de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) y se usa para crear el tema **DataCollectionTopic**.

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
     
TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);
 
namespaceManager.CreateTopic("DataCollectionTopic");
```

Tenga en cuenta que hay sobrecargas del método [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx) que permiten ajustar las propiedades del tema. Por ejemplo, puede configurar el valor predeterminado del período de vida (TTL) para los mensajes enviados al tema. A continuación, agregue las suscripciones **Inventory** y **Dashboard**.

```
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### Envío de mensajes al tema

En el caso de operaciones en tiempo de ejecución en entidades del Bus de servicio (por ejemplo, enviar y recibir mensajes), una aplicación debe crear primero un objeto [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx). De forma parecida a la clase [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx), la instancia de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) se crea a partir de la dirección base del espacio de nombres del servicio y del proveedor de tokens.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

Los mensajes enviados a los temas del Bus de servicio, y recibidos de ellas, son instancias de la clase [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Esta clase consta de un conjunto de propiedades estándar (como [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) y [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), un diccionario que se usa para conservar las propiedades de la aplicación y un cuerpo de datos de aplicación arbitrarios. Una aplicación puede establecer el cuerpo pasando cualquier objeto serializable (el siguiente ejemplo pasa un objeto **SalesData** que los datos de ventas del terminal del PDV), que usará [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) para serializar el objeto. También se puede proporcionar un objeto [Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx).

```
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

La manera más fácil de enviar mensajes al tema es usar [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) para crear un objeto [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) directamente desde la instancia de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx).

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### de mensajes de una suscripción

Al igual que cuando se usan las colas, para recibir mensajes desde una suscripción puede usar un objeto [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx), que se crea directamente desde [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) con [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Puede usar uno de los dos modos de recepción (**ReceiveAndDelete** y **PeekLock**), como se describe en [Creación de aplicaciones que usan colas del Bus de servicio](service-bus-create-queues.md).

Tenga en cuenta que cuando se crea un [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) para las suscripciones, el parámetro *entityPath* tiene la forma `topicPath/subscriptions/subscriptionName`. Por lo tanto, para crear un [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) para la suscripción **Inventory** del tema **DataCollectionTopic**, *entityPath* debe establecerse en `DataCollectionTopic/subscriptions/Inventory`. El código aparece como sigue:

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

## Filtros de suscripción

Hasta ahora, en este artículo todos los mensajes enviados al tema han estado a disposición de todas las suscripciones registradas. Aquí las palabras clave son "han estado a disposición". Mientras que las suscripciones de Bus de servicio ven todos los mensajes enviados al tema, solo es posible copiar un subconjunto de dichos mensajes a la cola de suscripción virtual. Esto se consigue mediante los *filtros* de suscripción. Al crear una suscripción, puede especificar una expresión de filtro en forma de predicado de estilo SQL92 que opera a través de las propiedades del mensaje, tanto las propiedades de sistema (por ejemplo, [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)) como las propiedades de la aplicación, como **StoreName** en el ejemplo anterior.

Al desarrollar el escenario para ilustrarlo, es preciso agregar un segunda almacén al escenario minorista. Los datos de ventas de todos los terminales de los PDV de ambos almacenes deben enrutarse al sistema de gestión centralizada de inventarios, pero un administrador de almacén que use la herramienta Panel solo está interesado en el rendimiento de dicho almacén. Para hacerlo, se puede usar el filtrado de suscripciones. Tenga en cuenta que cuando los terminales de los PDV publican mensajes, establecen la propiedad de la aplicación **StoreName** en el mensaje. Dados dos almacenes, por ejemplo **Redmond** y **Seattle**, los terminales de los PDV de Redmond marcan sus mensajes de datos de ventas con **StoreName** igual a **Redmond**, mientras que los de Seattle usa un **StoreName** igual a **Seattle**. El administrador del almacén de Redmond solo quiere ver los datos de sus terminales de PDV. El sistema aparece como sigue:

![Service-Bus4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

Para configurar este enrutamiento, se crea la suscripción **Dahsboard** como se indica a continuación:

```
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

Con este filtro de suscripción, los únicos mensajes que se copiarán en la cola virtual de la suscripción **Dashboard** serán los que tengan la propiedad **Storename** establecida en **Redmond**. Sin embargo, hay mucha más información sobre al filtrado de suscripciones. Las aplicaciones pueden tener varias reglas de filtro por suscripción, además de la capacidad de modificar las propiedades de un mensaje cuando pasa a la cola virtual de una suscripción.

## Resumen

Todas las razones para usar las colas descritas en [Creación de aplicaciones que usan colas del Bus de servicio](service-bus-create-queues.md) también se aplican a los temas, en concreto:

- Desacoplamiento temporal: no es preciso que los productores y consumidores de mensajes estén en línea al mismo tiempo.

- Nivelación de carga: los picos de carga se suavizan con el tema, lo que permite aprovisionar las aplicaciones que consumen para que la carga sea media, en lugar de máxima.

- Equilibrio de carga: es similar a una cola, es posible tener varios consumidores en competencia escuchando una sola suscripción y cada mensaje se entrega solo a uno de los consumidores, con lo que se equilibra la carga.

- Acoplamiento débil: la red de mensajes puede desarrollarse sin que ello afecte a los extremos existentes; por ejemplo, agregar suscripciones o cambiar filtros a un tema para permitir que haya nuevos consumidores.

## Pasos siguientes

Para más información cómo usar las colas en el escenario minorista de PDV, consulte [Creación de aplicaciones que usan colas de Bus de servicio](service-bus-create-queues.md).

<!---HONumber=AcomDC_0128_2016-->