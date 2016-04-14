<properties 
   pageTitle="Información general sobre los ejemplos del Bus de servicio | Microsoft Azure"
   description="Clasifica y describe los ejemplos del Bus de servicio con vínculos a cada uno."
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

# Ejemplos del Bus de servicio

Los ejemplos del Bus de servicio muestran las principales características del [Bus de servicio](https://azure.microsoft.com/services/service-bus/) (servicio en la nube) y el [Bus de servicio para Windows Server](https://msdn.microsoft.com/library/dn282144.aspx). En este artículo se categorizan y describen los ejemplos disponibles, con vínculos a cada uno.

>[AZURE.NOTE] Los ejemplos del Bus de servicio no se instalan con el SDK. Para obtener los ejemplos, visite la [página de ejemplos del SDK de Azure](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).

## Mensajería asíncrona del Bus de servicio

Los ejemplos siguientes muestran cómo escribir aplicaciones que usan el Bus de servicio.

Tenga en cuenta que los ejemplos de mensajería asíncrona requieren una cadena de conexión para tener acceso al espacio de nombres de su Bus de servicio.

### Para obtener una cadena de conexión para el Bus de servicio de Azure

1. Inicie sesión en el [Portal de Azure clásico](http://manage.windowsazure.com).

1. En la columna izquierda, haga clic en **Bus de servicio**.

1. En la lista, haga clic en el nombre del espacio de nombres del servicio.

1. Haga clic en **Información de conexión**. En el cuadro de diálogo **acceso a la información de conexión**, copie la cadena de conexión en el Portapapeles.

1. Pegue la cadena de conexión en el archivo App.config del ejemplo.

### Para obtener una cadena de conexión para el Bus de servicio para Windows Server

1. Ejecute el siguiente cmdlet de PowerShell:

	```
	get-sbClientConfiguration
	```

2. Pegue la cadena de conexión en el archivo App.config del ejemplo.

### Ejemplos de introducción

Estos ejemplos describen la funcionalidad básica de mensajería y retransmisión.

|Nombre del ejemplo|Descripción|Versión mínima del SDK|Disponibilidad|
|---|---|---|---|
|[Introducción: mensajería con colas](http://code.msdn.microsoft.com/Getting-Started-Brokered-aa7a0ac3)|Muestra cómo usar el Bus de servicio de Microsoft Azure para enviar y recibir mensajes de una cola.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Introducción: mensajería con temas](http://code.msdn.microsoft.com/Getting-Started-Brokered-614d42e5)|Muestra cómo usar el Bus de servicio de Microsoft Azure para enviar y recibir mensajes desde un tema con varias suscripciones.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Introducción a los Centros de eventos](http://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097)|Muestra las capacidades básicas de los Centros de eventos, como la creación de un Centro de eventos, el envío de eventos a un Centro de eventos y el consumo de eventos con el procesador de eventos.|2\.4|Bus de servicio de Microsoft Azure|

### Exploración de las características

Los ejemplos siguientes muestran diversas características del Bus de servicio.

|Nombre del ejemplo|Descripción|Versión mínima del SDK|Disponibilidad|
|---|---|---|---|
|[Proveedores de tokens HTTP](http://code.msdn.microsoft.com/Service-Bus-HTTP-Token-38f2cfc5)|Muestra las distintas formas de autenticar a un cliente HTTP/REST con el Bus de servicio.|2\.1|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Cliente HTTP del Bus de servicio](http://code.msdn.microsoft.com/Service-Bus-HTTP-client-fe7da74a)|Muestra cómo enviar y recibir mensajes del Bus de servicio mediante HTTP/REST.|2\.3|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Reenvío automático del Bus de servicio](http://code.msdn.microsoft.com/Service-Bus-Autoforwarding-b9df470b)|Muestra cómo reenviar automáticamente los mensajes de una cola, suscripción o cola de mensajes fallidos en otra cola o tema. También muestra cómo enviar un mensaje a una cola o tema mediante una cola de transferencia.|2\.3|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Mensajería asíncrona: ejemplo de sesión de canal de WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-0a526451)|Muestra cómo usar el Bus de servicio de Microsoft Azure mediante canales de Windows Communication Foundation (WCF). El ejemplo muestra el uso de canales WCF para enviar y recibir mensajes mediante una cola del Bus de servicio. El ejemplo muestra la sesión y comunicación no de la sesión en el Bus de servicio.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Mensajería asíncrona: transacciones](http://code.msdn.microsoft.com/Brokered-Messaging-8cd41d1e)|Muestra cómo usar las características de mensajería del Bus de servicio de Microsoft Azure dentro de un ámbito de transacción para garantizar que las operaciones de mensajería se confirman atómicamente.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Mensajería asíncrona: operaciones de administración con REST](http://code.msdn.microsoft.com/Brokered-Messaging-569cff88)|Muestra cómo realizar operaciones de administración del Bus de servicio con REST.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Las API de REST del proveedor de recursos](http://code.msdn.microsoft.com/Service-Bus-Resource-5d887203)|Muestra cómo usar las nuevas API de REST de RDFE del Bus de servicio para administrar espacios de nombres y entidades de mensajería.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Mensajería asíncrona: ejemplo de sesión del servicio WCF](http://code.msdn.microsoft.com/Brokered-Messaging-WCF-db4262c2)|Muestra cómo usar el Bus de servicio de Microsoft Azure con el modelo de servicio WCF. El ejemplo muestra el uso del modelo de servicio WCF para llevar a cabo la comunicación basada en sesiones mediante una cola del Bus de servicio.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Mensajería asíncrona: solicitar respuesta](http://code.msdn.microsoft.com/Brokered-Messaging-Request-2b4ff5d8)|Muestra cómo usar el Bus de servicio de Microsoft Azure y la funcionalidad de solicitud/respuesta. El ejemplo muestra clientes y servidores simples que se comunican mediante una cola del Bus de servicio.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Mensajería asíncrona: cola de mensajes fallidos](http://code.msdn.microsoft.com/Brokered-Messaging-Dead-22536dd8)|Muestra cómo usar el Bus de servicio de Microsoft Azure y la funcionalidad de mensajería "cola de mensajes fallidos". El ejemplo muestra un remitente y un receptor simples que se comunican mediante una cola del Bus de servicio.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Mensajería asíncrona: mensajes aplazados](http://code.msdn.microsoft.com/Brokered-Messaging-ccc4f879)|Muestra cómo usar la característica de aplazamiento de mensajes del Bus de servicio de Microsoft Azure. El ejemplo muestra un remitente y un receptor simples que se comunican mediante una cola del Bus de servicio.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Mensajería asíncrona: mensajes de la sesión](http://code.msdn.microsoft.com/Brokered-Messaging-Session-41c43fb4)|Muestra cómo usar el Bus de servicio de Microsoft Azure y la funcionalidad de sesión de mensajería. El ejemplo muestra remitentes y receptores simples que se comunican mediante una cola del Bus de servicio.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Mensajería asíncrona: solicitar tema de respuesta](http://code.msdn.microsoft.com/Brokered-Messaging-Request-6759a36e)|Muestra cómo implementar el patrón de solicitud/respuesta con temas y suscripciones del Bus de servicio de Microsoft Azure. El ejemplo muestra clientes y servidores simples que se comunican mediante un tema del Bus de servicio.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Mensajería asíncrona: solicitar cola de respuesta](http://code.msdn.microsoft.com/Brokered-Messaging-Request-0ce8fcaf)|Muestra cómo usar el Bus de servicio de Microsoft Azure y la funcionalidad de solicitud/respuesta. El ejemplo muestra clientes y servidores simples que se comunican mediante dos colas del Bus de servicio.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Mensajería asíncrona: detección de duplicados](http://code.msdn.microsoft.com/Brokered-Messaging-c0acea25)|Muestra cómo usar la detección de mensajes duplicados del Bus de servicio de Microsoft Azure con colas. Crea dos colas, una con la detección de duplicados activada y otra sin detección de duplicados.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Mensajería asíncrona: mensajería asincrónica](http://code.msdn.microsoft.com/Brokered-Messaging-Async-211c1e74)|Muestra cómo usar el Bus de servicio de Microsoft Azure para enviar y recibir mensajes de forma asincrónica desde una cola. La cola ofrece comunicación asincrónica desacoplada entre un remitente y cualquier número de receptores (aquí, un único receptor).|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Mensajería asíncrona: filtros avanzados](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)|Muestra cómo usar los filtros avanzados publicación-suscripción del Bus de servicio de Microsoft Azure. Crea un tema y 3 suscripciones con distintas definiciones de filtro, envía mensajes al tema y recibe todos los mensajes de las suscripciones.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Mensajería asíncrona: captura previa de los mensajes](http://code.msdn.microsoft.com/Brokered-Messaging-be2dac1d)|Muestra cómo usar la característica de captura previa de mensajes del Bus de servicio de Microsoft Azure. Muestra cómo usar la característica de captura previa de mensajes al recibirlos.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|

## Retransmisión del Bus de servicio

Ejemplos que muestran retransmisión del Bus de servicio.

### Introducción

|Nombre del ejemplo|Descripción|Versión mínima del SDK|Disponibilidad|
|---|---|---|---|
|[Mensajería retransmitida: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3)|Muestra cómo ejecutar un cliente y un servicio del Bus de servicio en Azure. Este ejemplo configura el Bus de servicio mediante programación. Solo se almacena información de entorno y de seguridad en los archivos de configuración.|1\.8|Bus de servicio de Microsoft Azure|
|[Autenticación de mensajería retransmitida: secreto compartido](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02)|Muestra cómo usar un nombre de emisor y el secreto del emisor para autenticar con el Bus de servicio.|1\.8|Bus de servicio de Microsoft Azure|
|[Autenticación de mensajería retransmitida: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831)|Muestra cómo exponer un servicio HTTP que no requiere autenticación de los usuarios del cliente.|1\.8|Bus de servicio de Microsoft Azure|
|[Enlaces de mensajería retransmitida: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0)|Muestra cómo usar el enlace **WebHttpRelayBinding** para devolver datos binarios mediante el modelo de programación web.|1\.8|Bus de servicio de Microsoft Azure|
|[Enlaces de mensajería retransmitida: NetTcp retransmitido](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692)|Muestra cómo usar el enlace **NetTcpRelayBinding**.|1\.8|Bus de servicio de Microsoft Azure|

### Exploración de las características

Ejemplos que muestran diversas características de retransmisión del Bus de servicio.

|Nombre del ejemplo|Descripción|Versión mínima del SDK|Disponibilidad|
|---|---|---|---|
|[Autenticación de mensajería retransmitida: WebToken simple](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392)|Muestra cómo usar una credencial de token web simple para autenticar con el Bus de servicio. El ejemplo es similar al ejemplo Echo, con algunos cambios. En concreto, este ejemplo agrega un comportamiento en las aplicaciones ServiceHost (servicio) y ChannelFactory (cliente).|1\.8|Bus de servicio de Microsoft Azure|
|[Mensajería retransmitida: equilibrio de la carga](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8)|Muestra cómo usar el Bus de servicio de Microsoft Azure para redirigir mensajes a varios receptores. Muestra varias instancias de un servicio simple que se comunica con un cliente mediante el enlace **NetTcpRelayBinding**|1\.8|Bus de servicio de Microsoft Azure|
|[Enlaces de mensajería retransmitida: evento neto](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977)|Muestra cómo usar el enlace **NetEventRelayBinding** del Bus de servicio de Microsoft Azure.|1\.8|Bus de servicio de Microsoft Azure|
|[Enlaces de mensajería retransmitida: WS2007Http sesión](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb)|Muestra cómo usar el enlace **WS2007HttpRelayBinding** con sesiones de confianza activadas. También muestra cómo especificar credenciales del Bus de servicio en el archivo de configuración en lugar de mediante programación.|1\.8|Bus de servicio de Microsoft Azure|
|[Enlaces de mensajería retransmitida: WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5)|Muestra cómo usar el enlace **WS2007HttpRelayBinding** con seguridad de mensajes para proteger los mensajes de extremo a extremo mientras sigue exigiendo que los clientes se autentiquen con el Bus de servicio.|1\.8|Bus de servicio de Microsoft Azure|
|[Mensajería retransmitida: intercambio de metadatos](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e)|Muestra cómo exponer un extremo de metadatos que usa el enlace de retransmisión. **MetadataExchange** se admite en los siguientes enlaces de retransmisión: **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding** y **WS2007HttpRelayBinding**.|1\.8|Bus de servicio de Microsoft Azure|
|[Enlaces de mensajería retransmitida: NetTcp directo](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161)|Muestra cómo configurar el enlace **NetTcpRelayBinding** para admitir el modo de conexión **híbrido** que primero establece una conexión retransmitida y, si es posible, cambia automáticamente a una conexión directa entre un cliente y un servicio.|1\.8|Bus de servicio de Microsoft Azure|
|[Enlaces de mensajería retransmitida: nombre de usuario NetTcp MsgSec](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392)|Muestra cómo usar el enlace **NetTcpRelayBinding** con seguridad de mensajes.|1\.8|Bus de servicio de Microsoft Azure|
|[Enlaces de mensajería retransmitida: Net Oneway](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a)|Muestra cómo exponer y consumir un extremo del servicio mediante el enlace **NetOnewayRelayBinding**.|1\.8|Bus de servicio de Microsoft Azure|
|[Enlaces de mensajería retransmitida: WS2007Http Simple](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a)|Muestra cómo usar el enlace **WS2007HttpRelayBinding**. Muestra un servicio simple que no usa las opciones de seguridad y no requiere que los clientes se autentiquen.|1\.8|Bus de servicio de Microsoft Azure|

## Herramientas de referencia del Bus de servicio

Los ejemplos siguientes muestran otras características del servicio.

|Nombre del ejemplo|Descripción|Versión mínima del SDK|Disponibilidad|
|---|---|---|---|
|[Explorador del Bus de servicio](http://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)|El Explorador del Bus de servicio permite a los usuarios conectarse a un espacio de nombres del servicio del Bus de servicio y administrar las entidades de mensajería de una forma sencilla. La herramienta ofrece características avanzadas, como la funcionalidad de importación y exportación y la capacidad de probar las entidades de mensajería y servicios de retransmisión.|1\.8|Bus de servicio de Microsoft Azure; Bus de servicio para Windows Server|
|[Autorización: SBAzTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93)|Este ejemplo muestra cómo crear y administrar las identidades del servicio del Control de acceso de Microsoft Azure Active Directory (también llamado Servicio de control de acceso o ACS) para su uso con el Bus de servicio.|N/D|Bus de servicio de Microsoft Azure|

## Pasos siguientes

Consulte los siguientes temas para obtener conceptos generales sobre el Bus de servicio.

- [Introducción a la mensajería del Bus de servicio](service-bus-messaging-overview.md)
- [Arquitectura del Bus de servicio](service-bus-architecture.md)
- [Elementos fundamentales del Bus de servicio](service-bus-fundamentals-hybrid-solutions.md)

<!---HONumber=AcomDC_0128_2016-->