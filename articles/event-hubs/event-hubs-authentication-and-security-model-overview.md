<properties 
   pageTitle="Introducción al modelo de autenticación y seguridad de los Centros de eventos | Microsoft Azure"
   description="Introducción al modelo de autenticación y seguridad de Centros de eventos"
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="sethm" />

# Introducción al modelo de autenticación y seguridad de los Centros de eventos

El modelo de seguridad de los Centros de eventos cumple los siguientes requisitos:

- Solo los dispositivos que tengan credenciales válidas pueden enviar datos a un Centro de eventos.
- Un dispositivo no puede suplantar a otro dispositivo.
- Un dispositivo no autorizado puede bloquearse para que no envíe datos a un Centro de eventos.

## Autenticación de dispositivos

El modelo de seguridad de los Centros de eventos se basa en una combinación de tokens de [firma de acceso compartido (SAS)](../service-bus/service-bus-shared-access-signature-authentication.md) y publicadores de eventos. Un publicador de eventos define un extremo virtual para un Centro de eventos. El publicador solo puede usarse para enviar mensajes a un Centro de eventos. No es posible recibir mensajes desde un publicador.

Normalmente, un Centro de eventos emplea un publicador por dispositivo. Todos los mensajes que se envíen a cualquiera de los publicadores de un Centro de eventos se ponen en cola dentro de ese Centro de eventos. Los publicadores permiten un control de acceso y limitación avanzados.

A cada dispositivo se le asigna un token único, que se carga en el dispositivo. Los tokens se producen de forma que cada token único concede acceso a un publicador único diferente. Un dispositivo que posea un token solo puede enviar a un publicador y a ningún otro. Si varios dispositivos comparten el mismo token, cada uno de estos dispositivos comparte un publicador.

Aunque no se recomienda, es posible equipar los dispositivos con tokens que concedan acceso directo a un Centro de eventos. Cualquier dispositivo que contenga un token de ese tipo puede enviar mensajes directamente a ese Centro de eventos. Ese dispositivo no estará sujeto a limitación. Además, el dispositivo no puede estar en la lista negra que impide el envío a ese Centro de eventos.

Todos los tokens se firman con una clave de SAS. Normalmente, todos los tokens se firman con la misma clave. Los dispositivos no conocen la clave; esto evita que los dispositivos produzcan tokens.

### Creación de la clave SAS

Cuando se crea un espacio de nombres, el Bus de servicio genera una clave SAS de 256 bits denominada **RootManageSharedAccessKey**. Esta clave permite enviar, escuchar y administrar los derechos del espacio de nombres. Puede crear claves adicionales. Se recomienda que genere una clave que conceda permisos de envío para el Centro de eventos concreto. En el resto de este tema, se supone que esta clave tiene el nombre `EventHubSendKey`.

En el ejemplo siguiente se crea una clave solo de envío cuando se crea el Centro de eventos:

```
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create Event hub with a SAS rule that allows sending to that Event hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### Generación de tokens

Puede generar tokens con la clave de SAS. Solo debe generar un token por dispositivo. Los tokens se pueden producir con el método siguiente. Todos los tokens se generan con la clave **EventHubSendKey**. A cada token se le asigna un URI único.

```
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Al llamar a este método, se debe especificar el URI como `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Para todos los tokens, el URI es idéntico, con la excepción de `PUBLISHER_NAME`, que debe ser diferente para cada token. Idealmente, `PUBLISHER_NAME` representa el identificador del dispositivo que recibe ese token.

Este método genera un token con la estructura siguiente:

```
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

La fecha de expiración del token se especifica en segundos desde el 1 de enero de 1970. A continuación, se muestra un ejemplo de un token:

```
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Normalmente, los tokens tienen una duración que se parece o supera la duración del dispositivo. Si el dispositivo tiene la capacidad de obtener un nuevo token, pueden usarse tokens con una duración más corta.

### Envío de datos de dispositivos

Cuando se han creado los tokens, cada dispositivo se aprovisiona con su propio token único.

Cuando el dispositivo envía datos a un Centro de eventos, el dispositivo etiqueta su token con la solicitud de envío. Para evitar que un atacante use la técnica de interceptación de la red y robe el token, la comunicación entre el dispositivo y el Centro de eventos debe realizarse a través de un canal cifrado.

### Incorporación de dispositivos a la lista negra

Si un atacante roba un token, el atacante puede suplantar el dispositivo al que se ha robado el token. Al incorporar un dispositivo a la lista negra, se consigue que el dispositivo quede inutilizable hasta que se le asigne un nuevo token que use un publicador diferente.

## Autenticación de aplicaciones de back-end

Para autenticar aplicaciones de back-end que consumen los datos que generan los dispositivos, los Centros de eventos emplean un modelo de seguridad que es similar al que se usa en los temas de Bus de servicio. Un grupo de consumidores de Centros de eventos es equivalente a una suscripción a un tema de Bus de servicio. Un cliente puede crear un grupo de consumidores si la solicitud para crear el grupo de consumidores viene acompañada de un token que concede privilegios de administración para el Centro de eventos o para el espacio de nombres al que este pertenece. Se permite que un cliente pueda consumir datos de un grupo de consumidores si la solicitud de recepción está acompañada de un token que concede derechos de recepción en ese grupo de consumidores, el Centro de eventos o el espacio de nombres al que este pertenece.

La versión actual del Bus de servicio no admite reglas SAS para suscripciones individuales. Lo mismo sucede con los grupos de consumidores de los Centros de eventos. La compatibilidad con SAS se agregará para ambas características en el futuro.

En ausencia de autenticación SAS para grupos de consumidores individuales, puede usar claves SAS para proteger todos los grupos de consumidores con una clave común. Este enfoque permite que una aplicación consuma datos desde cualquiera de los grupos de consumidores de un Centro de eventos.

### Creación de identidades de servicio, usuarios de confianza y reglas en ACS

ACS admite varias formas de crear identidades de servicio, usuarios de confianza y reglas, pero la forma más sencilla es mediante [SBAZTool](http://code.msdn.microsoft.com/Authorization-SBAzTool-6fd76d93). Por ejemplo:

1. Crear una identidad de servicio en un elemento **EventHubSender**. Devuelve el nombre de la identidad del servicio que se creó y su clave:

	```
	sbaztool.exe exe -n <namespace> -k <key>  makeid eventhubsender
	```

2. Otorgue a **EventHubSender** "Notificaciones de envío" para el Centro de eventos:

	```
	sbaztool.exe -n <namespace> -k <key> grant Send /AuthTestEventHub eventhubsender
	```

3. Crear una identidad de servicio en un receptor para un grupo de consumidores 1:

	```
	sbaztool.exe exe -n <namespace> -k <key> makeid consumergroup1receiver
	```

4. Otorgue a `consumergroup1receiver` "Notificaciones de escucha" para **ConsumerGroup1**:

	```
	sbaztool.exe -n <namespace> -k <key> grant Listen /AuthTestEventHub/ConsumerGroup1 consumergroup1receiver
	```

5. Crear una identidad de servicio en un receptor para un **grupo de consumidores 2**:

	```
	sbaztool.exe exe -n <namespace> -k <key>  makeid consumergroup2receiver
	```

6. Otorgue a `consumergroup2receiver` "Notificaciones de escucha" para **ConsumerGroup2**:

	```
	sbaztool.exe -n <namespace> -k <key> grant Listen /AuthTestEventHub/ConsumerGroup2 consumergroup2receiver
	```

## Pasos siguientes

Para obtener más información sobre los Centros de eventos, visite los siguientes temas:

- [Información general de los Centros de eventos]
- Una [aplicación de ejemplo completa que usa Centros de eventos].
- Una [solución de mensajería en cola] mediante las colas de Bus de servicio.

[Información general de los Centros de eventos]: event-hubs-overview.md
[aplicación de ejemplo completa que usa Centros de eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[solución de mensajería en cola]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0204_2016-->