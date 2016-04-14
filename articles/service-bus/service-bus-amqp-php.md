<properties 
   pageTitle="Bus de servicio y PHP con AMQP 1.0 | Microsoft Azure"
   description="Uso del Bus de servicio desde PHP con AMQP"
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

# Uso del Bus de servicio desde PHP con AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-PHP es un lenguaje PHP que se enlaza con Proton-C; es decir, Proton-PHP se implementa como un contenedor en torno a un motor implementado en C.

## Descarga de la biblioteca de cliente de Proton

Puede descargar Proton-C y los enlaces asociados (incluido PHP) desde [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). La descarga está en formato de código fuente. Para compilar el código, siga las instrucciones incluidas en el paquete descargado.

> [AZURE.IMPORTANT] En el momento de redactar este artículo, la compatibilidad con SSL en Proton-C solo está disponible para los sistemas operativos Linux. Dado que Bus de servicio de Azure requiere el uso de SSL, en este momento solo puede usarse Proton-C (y los enlaces de lenguaje) para acceder al Bus de servicio desde Linux. Se está trabajando para habilitar Proton-C con SSL en Windows, así que consulte periódicamente si existen actualizaciones.

## Trabajo con colas, temas y suscripciones del Bus de servicio desde PHP

En el código siguiente se muestra cómo enviar y recibir mensajes de una entidad de mensajería de Bus de servicio.

### Envío de mensajes con Proton-PHP

En el código siguiente se muestra cómo enviar un mensaje a una entidad de mensajería de Bus de servicio.

```
$messenger = new Messenger();
$message = new Message();
$message->address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]";

$message->body = "This is a text string";
$messenger->put($message);
$messenger->send();
```

### Recepción de mensajes con Proton-PHP

En el código siguiente se muestra cómo recibir un mensaje desde una entidad de mensajería de Bus de servicio.

```
$messenger = new Messenger();
$address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]";
$messenger->subscribe($address);

$messenger->start();
$messenger->recv(1);

if($messenger->incoming())
{
   $message = new Message();
   $messenger->get($message);      
}

$messenger->stop();
```

## Mensajería entre .NET y Proton-PHP

### Propiedades de la aplicación

#### ProtonPHP a las API de .NET de Bus de servicio

Los mensajes de Proton-PHP admiten propiedades de aplicación de los tipos siguientes: **integer**, **double**, **boolean**, **string** y **object**. En el siguiente código PHP se muestra cómo establecer propiedades en un mensaje con cada uno de estos tipos de propiedades.

```
$message->properties["TestInt"] = 1;    
$message->properties["TestDouble"] = 1.5;      
$message->properties["TestBoolean"] = False;
$message->properties["TestString"] = "Service Bus";    
$message->properties["TestObject"] = new UUID("1234123412341234");   
```

En las API de .NET del Bus de servicio, las propiedades de la aplicación de mensajes se realizan en la colección **Properties** de [BrokeredMessage][]. En el código siguiente se muestra cómo se leen las propiedades de aplicación de un mensaje recibido de un cliente PHP.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}if (message.Properties.Keys.Count > 0)
{
foreach (string name in message.Properties.Keys)
{
  Object value = message.Properties[name];
  Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
}
Console.WriteLine();
}
```

En la tabla siguiente se muestra cómo se asignan los tipos de propiedad de PHP a los tipos de propiedad de .NET.

| Tipo de propiedad de PHP | Tipo de propiedad de .NET |
|-------------------|--------------------|
| integer | int |
| double | double |
| boolean | booleano |
| cadena | cadena |
| objeto | Objeto |

#### API de .NET de Bus de servicio a PHP

El tipo [BrokeredMessage][] admite propiedades de la aplicación de los siguientes tipos: **byte**, **sbyte**, **char**, **short**, **ushort**, **int**, **uint**, **long**, **ulong**, **float**, **double**, **decimal**, **bool**, **Guid**, **string**, **Uri**, **DateTime**, **DateTimeOffset** y **TimeSpan**. El código .NET siguiente muestra cómo establecer las propiedades en un objeto [BrokeredMessage][] con cada uno de estos tipos de propiedades.

```
message.Properties["TestByte"] = (byte)128;
message.Properties["TestSbyte"] = (sbyte)-22;
message.Properties["TestChar"] = (char) 'X';
message.Properties["TestShort"] = (short)-12345;
message.Properties["TestUshort"] = (ushort)12345;
message.Properties["TestInt"] = (int)-100;
message.Properties["TestUint"] = (uint)100;
message.Properties["TestLong"] = (long)-12345;
message.Properties["TestUlong"] = (ulong)12345;
message.Properties["TestFloat"] = (float)3.14159;
message.Properties["TestDouble"] = (double)3.14159;
message.Properties["TestDecimal"] = (decimal)3.14159;
message.Properties["TestBoolean"] = true;
message.Properties["TestGuid"] = Guid.NewGuid();
message.Properties["TestString"] = "Service Bus";
message.Properties["TestUri"] = new Uri("http://www.bing.com");
message.Properties["TestDateTime"] = DateTime.Now;
message.Properties["TestDateTimeOffSet"] = DateTimeOffset.Now;
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

El código PHP siguiente muestra cómo se leen las propiedades de la aplicación de un mensaje recibido desde un cliente de .NET del Bus de servicio.

```
if ($message->properties != null)
{
  foreach($message->properties as $key => $value)
  {
    printf("-- %s : %s (%s) \n", $key, $value, gettype($value));                       
  }         
}
```

En la tabla siguiente se muestra cómo se asignan los tipos de propiedad de JHP a los tipos de propiedad de .NET.

| Tipo de propiedad de .NET | Tipo de propiedad de PHP | Notas |
|--------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte | integer | - | | sbyte | integer | - | | char | Char | clase Proton-PHP | | short | integer | - | | ushort | integer | - | | int | integer | - | | uint | Integer | - | | long | integer | - | | ulong | integer | - | | float | double | - | | double | double | - | | decimal | string | Decimal no es compatible actualmente con Proton. | | bool | boolean | - | | Guid | UUID | Clase Proton-PHP | | string | string | - | | DateTime | integer | - | | DateTimeOffset | DescribedType | DateTimeOffset.UtcTicks asignados al tipo AMQP:<type name="datetime-offset" class=restricted source="long"> <descriptor name="com.microsoft:datetime-offset" /></type> | | TimeSpan | DescribedType | Timespan.Ticks asignados a AMQP type:<type name="timespan" class=restricted source="long"> <descriptor name="com.microsoft:timespan" /></type> | | Uri | DescribedType | Uri.AbsoluteUri asignado a tipo AMQP:<type name="uri" class=restricted source="string"> <descriptor name="com.microsoft:uri" /></type> |

### Propiedades estándar

En las tablas siguientes se muestra la asignación entre las propiedades de mensajes estándares de Proton-PHP y las propiedades de mensajes estándar de [BrokeredMessage][].

| Proton-PHP | .NET del Bus de servicio | Notas |
|----------------------|--------------------------|----------------------------------------------------------|
| Duradero | N/D | El Bus de servicio solo admite mensajes duraderos. |
| Prioridad | N/D | El Bus de servicio solo admite una única prioridad de mensaje. |
| Ttl | Message.TimeToLive | Conversión, el TTL de Proton-PHP se define en milisegundos. |
| first\_acquirer | - | - | | delivery\_count | - | - | | Id | Message.Id | - | | user\_id | - | - | | Address | Message.To | - | | Subject | Message.Label | - | | reply\_to | Message.ReplyTo | - | | correlation\_id | Message.CorrelationId | - | | content\_type | Message.ContentType | - | | content\_encoding | n/a | - | | expiry\_time | Message.ExpiresAtUTC | - | | creation\_time | n/a | - | | group\_id | Message.SessionId | - | | group\_sequence | - | - | | reply\_to\_group\_id | Message.ReplyToSessionId | - | | Format | n/a | -

#### API de .NET de Bus de servicio a Proton-PHP

| .NET del Bus de servicio | Proton-PHP | Notas |
|-------------------------|--------------------------------------------------------|--------------------------------------------------------|
| ContentType | Message->content\_type | - | | CorrelationId | Message->correlation\_id | - | | EnqueuedTimeUtc | Message->annotations[x-opt-enqueued-time] | - | | Label | Message->subject | - | | MessageId | Message->id | - | | ReplyTo | Message->reply\_to | - | | ReplyToSessionId | Message->reply\_to\_group\_id | - | | ScheduledEnqueueTimeUtc | Message->annotations ["x-opt-scheduled-enqueue-time"] | - | | SessionId | Message->group\_id | - | | TimeToLive | Message->ttl | Conversión, Proton-PHP TTL se define en milisegundos. | | To | Message->address | - |

## Pasos siguientes

¿Listo para obtener más información? Consulte los siguientes vínculos:

- [Información general sobre AMQP para el Bus de servicio]
- [AMQP de Bus de servicio para Windows Server]


[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP de Bus de servicio para Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
[Información general sobre AMQP para el Bus de servicio]: service-bus-amqp-overview.md

<!---HONumber=AcomDC_0128_2016-->