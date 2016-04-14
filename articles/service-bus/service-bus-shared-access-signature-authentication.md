<properties 
   pageTitle="Autenticación con firma de acceso compartido en el Bus de servicio | Microsoft Azure"
   description="Detalles acerca de la autenticación SAS con el Bus de servicio."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/09/2015"
   ms.author="sethm" />

# Autenticación con firma de acceso compartido en Service Bus

La autenticación [Firma de acceso compartido (SAS)](service-bus-sas-overview.md) permite a las aplicaciones autenticarse en el Bus de servicio mediante una clave de acceso configurada en el espacio de nombres o en la entidad de mensajería (cola o tema) al que se asocian derechos específicos. A continuación, puede usar esta clave para generar un token SAS que a su vez, los clientes pueden usar para autenticarse en el Bus de servicio.

La compatibilidad con la autenticación SAS se incluye en el SDK de Azure 2.0 y versiones posteriores. Para obtener más información sobre la autenticación del Bus de servicio, consulte [Autenticación y autorización del Bus de servicio](service-bus-authentication-and-authorization.md).

## Conceptos

La autenticación SAS en el Bus de servicio implica la configuración de una clave criptográfica con derechos asociados en un recurso del Bus de servicio. Los clientes reclaman acceso a los recursos del Bus de servicio mediante la presentación de un token SAS. Este token consta del URI del recurso al que se accede y una caducidad firmada con la clave configurada.

Puede configurar las reglas de autorización de firma de acceso compartido en las [retransmisiones](service-bus-fundamentals-hybrid-solutions.md#relays), las [colas](service-bus-fundamentals-hybrid-solutions.md#queues), los [temas](service-bus-fundamentals-hybrid-solutions.md#topics) y los [Centros de eventos](https://azure.microsoft.com/documentation/services/event-hubs/) del Bus de servicio.

La autenticación SAS usa los siguientes elementos:

- [Regla de autorización de acceso compartido](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx): clave criptográfica principal de 256 bits en representación Base64, una clave secundaria opcional y un nombre de clave y derechos asociados (una colección de derechos de *escucha*, *envío* o *administración*).

- Token [Firma de acceso compartido](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx): generado con el HMAC-SHA256 de una cadena de recursos, formada por el URI del recurso al que se accede y una caducidad, con la clave criptográfica. La firma y otros elementos que se describen en las secciones siguientes tienen formato de cadena para formar el token [SharedAccessSignature](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.sharedaccesssignature.aspx).

## Configuración de la autenticación de firma de acceso compartido

Puede configurar la regla [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) en los espacios de nombres, las colas o los temas del Bus de servicio. La configuración de una regla [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) en una suscripción del Bus de servicio no se admite, pero puede usar las reglas configuradas en un espacio de nombres o un tema para asegurar el acceso a las suscripciones. Para ver un ejemplo funcional que ilustra este procedimiento, consulte el ejemplo [Uso de la autenticación de firma de acceso compartido (SAS) con suscripciones del Bus de servicio](http://code.msdn.microsoft.com/windowsazure/Using-Shared-Access-e605b37c).

Puede configurarse un máximo de 12 reglas en un espacio de nombres, una cola o un tema del Bus de servicio. Las reglas que se configuran en un espacio de nombres del Bus de servicio se aplican a todas las entidades en ese espacio de nombres.

![SAS](./media/service-bus-shared-access-signature-authentication/IC676272.gif)

En esta ilustración, las reglas de autorización *manageRuleNS*, *sendRuleNS* y *listenRuleNS* se aplican a la cola Q1 y tema T1, mientras que *listenRuleQ* y *sendRuleQ* solo se aplican a la cola Q1, y *sendRuleT* solo se aplica al tema T1.

Los parámetros clave de un objeto [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) son los siguientes:

|Parámetro|Descripción|
|---|---|
|*KeyName*|Una cadena que describe la regla de autorización.|
|*PrimaryKey*|Una clave principal de 256 bits con codificación base64 para firmar y validar el token SAS.|
|*SecondaryKey*|Una clave secundaria de 256 bits con codificación base64 para firmar y validar el token SAS.|
|*AccessRights*|Una lista de derechos de acceso concedidos por la regla de autorización. Estos derechos pueden ser cualquier colección de derechos de escucha, envío y administración.|

Cuando se aprovisiona un espacio de nombres del Bus de servicio, de forma predeterminada se crea una [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx), con [KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) establecido en **RootManageSharedAccessKey**. También se configuran los objetos [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) predeterminados para los centros de notificaciones: uno con derechos de escucha, envío y administración y otro solo con derechos de escucha.

## Vuelva a generar y revocar las claves para las reglas de autorización de acceso compartido

Se recomienda regenerar periódicamente las claves usadas en el objeto [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). Normalmente, las aplicaciones deben usar [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) para generar un token SAS. Al volver a generar las claves, debe reemplazar [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) por la antigua clave principal y generar una nueva clave como nueva clave principal. Esto le permite seguir usando tokens para la autorización que se emitieron con la clave principal anterior y que aún no hayan caducado.

Si se ve comprometida una clave y es necesario revocar las claves, puede volver a generar la [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) y la [SecondaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.secondarykey.aspx) de un [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx), y reemplazarlos por las nuevas claves. Este procedimiento invalida todos los tokens firmados con las claves antiguas.

## Generación de un token de firma de acceso compartido

Cualquier cliente que tenga acceso a las claves de firma especificadas en la regla de autorización de acceso compartido puede generar el token SAS. Tiene el formato siguiente:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

La **firma** para las asociaciones de seguridad token se calcula usando el hash HMAC-SHA256 de una cadena para firmar con la propiedad [PrimaryKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.primarykey.aspx) de una regla de autorización. La cadena para firmar consta de un URI de recurso y una caducidad, con el siguiente formato:

```
StringToSign = <resourceURI> + "\n" + expiry;
```

Tenga en cuenta que debe usar el URI de recurso codificado para esta operación. El URI de recurso es el URI completo del recurso del Bus de servicio al que se solicita el acceso. Por ejemplo, `http://<namespace>.servicebus.windows.net/<entityPath>` o `sb://<namespace>.servicebus.windows.net/<entityPath>`; es decir, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`.

La caducidad se representa como el número de segundos transcurridos desde el tiempo base 00:00:00 UTC el 1 de enero de 1970.

La regla de autorización de acceso compartido usada para firmar debe configurarse en la entidad especificada por este URI, o uno de sus primarios jerárquicos. Por ejemplo, `http://contoso.servicebus.windows.net/contosoTopics/T1` o `http://contoso.servicebus.windows.net` en el ejemplo anterior.

Un token SAS es válido para todos los recursos en el `<resourceURI>` usado en la cadena para firmar.

[KeyName](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.keyname.aspx) en los token SAS se refiere al **keyName** de la regla de autorización de acceso compartido usado para generar el token.

El *URL-encoded-resourceURI* debe ser el mismo que el URI usado en la cadena para firmar durante el cálculo de la firma. Debe estar [codificado por porcentaje](https://msdn.microsoft.com/library/4fkewx0t.aspx).

## Uso de la autenticación con firma de acceso compartido en el Bus de servicio

Los siguientes escenarios incluyen la configuración de reglas de autorización, la generación de tokens SAS y la autorización del cliente.

Para un ejemplo funcional completo de una aplicación del Bus de servicio que ilustra la configuración y usa la autorización SAS, consulte [Autenticación con firma de acceso compartido en Service Bus](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Hay un ejemplo relacionado que ilustra el uso de reglas de autorización SAS configuradas en espacios de nombres o temas para proteger las suscripciones del Bus de servicio disponible aquí: [Uso de la autenticación de firma de acceso compartido (SAS) con suscripciones del Bus de servicio](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## Acceso a las reglas de autorización de acceso compartido en un espacio de nombres

Las operaciones en la raíz del espacio de nombres del Bus de servicio requieren la autenticación con certificados. Debe cargar un certificado de administración para la suscripción de Azure. Para cargar un certificado de administración, haga clic en **Configuración** en el panel izquierdo del [Portal de Azure clásico][]. Para obtener más información sobre los certificados de administración de Azure, consulte [Creación de un certificado de administración para Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx).

El extremo para acceder a las reglas de autorización de acceso compartido en un espacio de nombres del Bus de servicio es el siguiente:

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Para crear un objeto [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) en un espacio de nombres del Bus de servicio, ejecute una operación POST en este punto de conexión con la información de la regla serializada como JSON o XML. Por ejemplo:

```
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

Del mismo modo, use una operación GET en el extremo para leer las reglas de autorización configuradas en el espacio de nombres.

Para actualizar o eliminar una regla de autorización específica, use el siguiente extremo:

```
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## Acceso a las reglas de autorización de acceso compartido en una entidad

Puede acceder a un objeto [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) configurado en una cola del Bus de servicio o un tema mediante la colección [AuthorizationRules](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.authorizationrules.aspx) en los objetos [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx), [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) o [NotificationHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.notifications.notificationhubdescription.aspx) correspondiente.

El código siguiente muestra cómo agregar reglas de autorización para una cola.

```
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString( 
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey", 
    SharedAccessAuthorizationRule.GenerateRandomKey(), 
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## Uso de la autorización de la firma de acceso compartido

Las aplicaciones que usan el SDK de .NET de Azure con las bibliotecas .NET del Bus de servicio pueden usar la autorización SAS mediante la clase [SharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedaccesssignaturetokenprovider.aspx). El código siguiente muestra el uso del proveedor de token para enviar mensajes a una cola del Bus de servicio.

```
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb", 
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri, 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Las aplicaciones también pueden usar SAS para la autenticación mediante una cadena de conexión SAS en métodos que acepten cadenas de conexión.

Tenga en cuenta que para usar la autorización SAS con retransmisiones del Bus de servicio, puede usar claves SAS configuradas en el espacio de nombres del Bus de servicio. Si crea explícitamente una retransmisión en el espacio de nombres [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) con un objeto [RelayDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.relaydescription.aspx), puede establecer las reglas SAS para dicha retransmisión. Para usar la autorización SAS con suscripciones del Bus de servicio, puede usar claves SAS configuradas en un espacio de nombres del Bus de servicio o en un tema.

## Derechos necesarios para realizar operaciones del Bus de servicio

La siguiente tabla muestra los derechos de acceso necesarios para realizar diversas operaciones en recursos del Bus de servicio.

|Operación|Solicitud necesaria|Ámbito de solicitud|
|---|---|---|
|**Espacio de nombres**|||
|Configurar la regla de autorización en un espacio de nombres|Manage|Cualquier dirección de espacio de nombres|
|**Registro del servicio**|||
|Enumerar directivas privadas|Manage|Cualquier dirección de espacio de nombres|
|Retransmisión|||
|Empezar a escuchar en un espacio de nombres|Escuchar|Cualquier dirección de espacio de nombres|
|Enviar mensajes a un agente de escucha en un espacio de nombres|Los métodos Send|Cualquier dirección de espacio de nombres|
|**Cola**|||
|Creación de una cola|Manage|Cualquier dirección de espacio de nombres|
|Eliminación de una cola|Manage|Cualquier dirección de cola válida|
|Enumerar colas|Manage|/$Resources/Queues|
|Obtener la descripción de la cola|Administrar o enviar|Cualquier dirección de cola válida|
|Configurar la regla de autorización para una cola|Manage|Cualquier dirección de cola válida|
|Enviar a la cola|Los métodos Send|Cualquier dirección de cola válida|
|mensajes de una cola|Escuchar|Cualquier dirección de cola válida|
|Abandone o complete los mensajes después de recibir el mensaje en el modo de bloqueo de información|Escuchar|Cualquier dirección de cola válida|
|Aplazar un mensaje para su recuperación posterior|Escuchar|Cualquier dirección de cola válida|
|Mensaje fallido|Escuchar|Cualquier dirección de cola válida|
|Obtener el estado asociado a una sesión de cola de mensajes|Escuchar|Cualquier dirección de cola válida|
|Establecer el estado asociado a una sesión de cola de mensajes|Escuchar|Cualquier dirección de cola válida|
|**Tema.**|||
|de un tema|Manage|Cualquier dirección de espacio de nombres|
|Eliminación de un tema|Manage|Cualquier dirección de tema válida|
|Enumerar temas|Manage|/$Resources/Topics|
|Obtener la descripción del tema|Administrar o enviar|Cualquier dirección de tema válida|
|Configurar la regla de autorización para un tema|Manage|Cualquier dirección de tema válida|
|Enviar al tema|Los métodos Send|Cualquier dirección de tema válida|
|**Suscripción**|||
|una suscripción|Manage|Cualquier dirección de espacio de nombres|
|Eliminar suscripción|Manage|../myTopic/Subscriptions/mySubscription|
|Enumerar suscripciones|Manage|../myTopic/Subscriptions|
|Obtener la descripción de la suscripción|Administrar o escuchar|../myTopic/Subscriptions/mySubscription|
|Abandone o complete los mensajes después de recibir el mensaje en el modo de bloqueo de información|Escuchar|../myTopic/Subscriptions/mySubscription|
|Aplazar un mensaje para su recuperación posterior|Escuchar|../myTopic/Subscriptions/mySubscription|
|Mensaje fallido|Escuchar|../myTopic/Subscriptions/mySubscription|
|Obtener el estado asociado a una sesión de tema|Escuchar|../myTopic/Subscriptions/mySubscription|
|Establecer el estado asociado a una sesión de tema|Escuchar|../myTopic/Subscriptions/mySubscription|
|**Regla**|||
|Crear una regla|Manage|../myTopic/Subscriptions/mySubscription|
|Eliminar una regla|Manage|../myTopic/Subscriptions/mySubscription|
|Enumerar reglas|Administrar o escuchar|../myTopic/Subscriptions/mySubscription/Rules|
|**Centros de notificaciones**|||
|Creación de un centro de notificaciones|Manage|Cualquier dirección de espacio de nombres|
|Crear o actualizar el registro para un dispositivo activo|Escuchar o administrar|../notificationHub/tags/{tag}/registrations|
|Actualizar la información de PNS|Escuchar o administrar|../notificationHub/tags/{tag}/registrations/updatepnshandle|
|Envío de un centro de notificaciones|Los métodos Send|../notificationHub/messages|

## Pasos siguientes

Para obtener información general de alto nivel de SAS en el Bus de servicio, consulte [Firmas de acceso compartido](service-bus-sas-overview.md).

Para obtener más información sobre la autenticación del Bus de servicio, consulte [Autenticación y autorización del Bus de servicio](service-bus-authentication-and-authorization.md).

[Portal de Azure clásico]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0107_2016-->