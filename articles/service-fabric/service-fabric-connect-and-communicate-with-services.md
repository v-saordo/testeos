<properties
   pageTitle="Comunique y conecte con los servicios de Service Fabric de Azure | Microsoft Azure"
   description="Aprenda a conectar y comunicar con servicios en las aplicaciones de Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="mfussell"/>

# Comunicación con los servicios
En el mundo de los microservicios la solución general se compone de muchos servicios diferentes, donde cada uno de ellos realiza una tarea especializada. Estos microservicios se comunican entre sí para habilitar el flujo de trabajo completo. También hay aplicaciones cliente que se conectan a los servicios y se comunican con ellos. En este documento se describe cómo Service Fabric de Azure le facilita la configuración de la comunicación con los servicios que crea con Service Fabric.

## Conceptos clave
Estos son algunos de los conceptos clave que debe tener en cuenta al configurar la comunicación con los servicios.

### La comunicación se representa como cliente-servidor
Las API de comunicación de Service Fabric representan los tipos de interacciones entre cliente y servidor, incluso cuando la interacción se encuentra entre dos servicios que se ejecutan en el mismo clúster. Un servicio de destino, al que se conectará desde un cliente u otro servicio, actúa como un servidor y escucha las solicitudes entrantes. El cliente, que puede ser simplemente otro servicio en el clúster, se conecta al servidor y realiza llamadas para él.

### Movimiento de servicios
En un sistema distribuido, las instancias de servicio que ejecuta se pueden mover de una máquina a otra con el tiempo. Los motivos pueden ser diversos: el equilibrio de recursos cuando se configura con métricas de carga para equilibrar los recursos, las actualizaciones, las conmutaciones por error, las escalas horizontales, etc. Esto se traduce en que las direcciones del punto de conexión de una instancia de servicio pueden cambiar. Para establecer la comunicación con la instancia de servicio, se debe ejecutar el siguiente bucle. Estos detalles se abstraen del usuario si usa las API de comunicación ofrecidas por Service Fabric.

* **Resolver**: todas las instancias de servicio indicadas en Service Fabric tienen nombres únicos como URI. Por ejemplo, `"fabric:/MyApplication/MyService"` permanece fijada en el servicio indicado. Cada instancia de servicio indicada también expone los puntos de conexión que pueden cambiar cuando se mueven las instancias de servicio indicadas. Esto es análogo a los sitios web que tienen direcciones URL constantes, pero donde la dirección IP puede cambiar. Y de manera similar al DNS en la Web, que resuelve direcciones URL del sitio web en direcciones IP, Service Fabric tiene un servicio de sistema denominado Servicio de nombres que resuelve los URI a los puntos de conexión. Este paso implica la resolución del URI de la instancia de servicio a un extremo.

* **Conectar**: cuando se haya resuelto el URI de servicio indicado a la dirección de un punto de conexión, el siguiente paso es intentar conectarse a ese servicio. Esta conexión puede generar un error si la dirección del punto de conexión ha cambiado debido a un movimiento de servicio que, por ejemplo, puede haberse debido a un error de la máquina o al equilibrio de recurso.

* **Reintentar**: si el intento de conexión produce un error, se deben probar de nuevo los pasos de conexión y resolución anteriores, y este ciclo se repite hasta que la conexión sea correcta.

## Opciones de API de comunicación
Como parte de Service Fabric, ofrecemos algunas opciones diferentes para las API de comunicación. La decisión de cuál funcionará mejor para usted depende de la elección del modelo de programación, el marco de comunicación y el lenguaje de programación en que se crean los servicios.

### Comunicación para los actores confiables
Para los servicios escritos usando la API de Reliable Actors, se abstraen todos los detalles de comunicación. La comunicación se produce cuando el método llama al ActorProxy, por lo que puede dejar de leer aquí.

### Opciones de comunicación para los servicios de confianza
Hay un par de opciones diferentes si el servicio se ha escrito con la API de Reliable Services. Su elección de un protocolo de comunicación le guiará en cuanto a qué API de comunicación de Service Fabric puede usar.

* **Ningún protocolo específico:** si no tiene una opción concreta de marco de comunicación, pero desea crear algo que se pueda ejecutar rápidamente, entonces la opción idónea para usted es la [pila predeterminada](service-fabric-reliable-services-communication-remoting.md), que permite un modelo similar al modelo de comunicación de Actor. Esta es la manera más sencilla y rápida de empezar con la comunicación del servicio. Ofrece una comunicación RPC fuertemente tipada que abstrae la mayoría de los detalles de comunicación para que invocar un método en un servicio remoto en su código sea como llamar a un método en una instancia de objeto local. Estas clases abstraen el control de la resolución, la conexión, los reintentos y los errores al configurar el canal de comunicación y permite un modelo de comunicación basado en llamadas de método. Para ello, use la clase `ServiceRemotingListener` en el lado servidor y la clase `ServiceProxy` en el lado cliente de la comunicación.

* **HTTP**: para aprovechar la flexibilidad que la comunicación basada en HTTP le ofrece, puede usar las API de comunicación de Service Fabric que permiten que se defina el mecanismo de comunicación a la vez que la resolución, la conexión y la lógica de reintentos se siguen abstrayendo del usuario. Por ejemplo, puede usar la API web para especificar el mecanismo de comunicación y aprovechar las clases [`ICommunicationClient` y `ServicePartitionClient` ](service-fabric-reliable-services-communication.md) para configurar la comunicación.
* **WCF**: si tiene código existente que usa WCF como su marco de comunicación, puede usar el `WcfCommunicationListener` para el lado servidor, y las clases `WcfCommunicationClient` y `ServicePartitionClient` para el cliente. Para obtener más información, consulte este artículo sobre la [implementación basada en WCF de la pila de comunicación](service-fabric-reliable-services-communication-wcf.md).

* **Otros marcos de comunicación, incluidos los protocolos personalizados**: si está planeando el uso de cualquier otro marco de comunicación, incluidos los protocolos de comunicación personalizados, hay unas API de comunicación de Service Fabric en las que puede conectar su pila de comunicación mientras que se abstrae de usted todo el trabajo de detección y conexión. Consulte el artículo sobre el [Modelo de comunicación de Reliable Services](service-fabric-reliable-services-communication.md) para obtener más detalles.

### Comunicación entre servicios creados en diferentes lenguajes de programación
Todas las API de comunicación de Service Fabric están actualmente disponibles solo en C#. Esto significa que si tiene un servicio que está escrito en algún otro lenguaje de programación, como Java o Node.JS, tendrá que escribir su propio mecanismo de comunicación.

## Pasos siguientes
* [Pila de comunicación predeterminada ofrecida por el marco de servicios de confianza ](service-fabric-reliable-services-communication-remoting.md)
* [Modelo de comunicación de servicios de confianza](service-fabric-reliable-services-communication.md)
* [Introducción a los servicios de la API web de Service Fabric de Microsoft Azure con el autohospedaje de OWIN](service-fabric-reliable-services-communication-webapi.md)
* [Pila de comunicación basada en WCF de Reliable Services](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0224_2016-->