<properties 
   pageTitle="Bus de servicio y Python con AMQP 1.0 | Microsoft Azure"
   description="Uso del Bus de servicio desde Python con AMQP"
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
   ms.date="02/08/2016"
   ms.author="sethm" />

# Uso del Bus de servicio desde Python con AMQP 1.0

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

Proton-Python es un lenguaje Python que se enlaza con Proton-C; es decir, Proton-Python se implementa como un contenedor en torno a un motor implementado en C.

## Descarga de la biblioteca de cliente de Proton

Puede descargar Proton-C y los enlaces asociados (incluido Python) desde [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). La descarga está en formato de código fuente. Para compilar el código, siga las instrucciones incluidas en el paquete descargado.

Tenga en cuenta que en el momento de redactar este artículo, la compatibilidad con SSL en Proton-C solo está disponible para los sistemas operativos Linux. Dado que Bus de servicio de Azure requiere el uso de SSL, en este momento solo puede usarse Proton-C (y los enlaces de lenguaje) para acceder al Bus de servicio desde Linux. Se está trabajando para habilitar Proton-C con SSL en Windows, así que consulte periódicamente si existen actualizaciones.

## Uso de colas, temas y suscripciones del Bus de servicio desde PHP

En el código siguiente se muestra cómo enviar y recibir mensajes de una entidad de mensajería de Bus de servicio.

### Envío de mensajes con Proton-Python

En el código siguiente se muestra cómo enviar un mensaje a una entidad de mensajería de Bus de servicio.

```
messenger = Messenger()
message = Message()
message.address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"

message.body = u"This is a text string"
messenger.put(message)
messenger.send()
```

### Recepción de mensajes con Proton-Python

En el código siguiente se muestra cómo recibir un mensaje desde una entidad de mensajería de Bus de servicio.

```
messenger = Messenger()
address = "amqps://[username]:[password]@[namespace].servicebus.windows.net/[entity]"
messenger.subscribe(address)

messenger.start()
messenger.recv(1)
message = Message()
if messenger.incoming:
      messenger.get(message)
messenger.stop()
```

## Mensajería entre .NET y Proton-Python

### Propiedades de la aplicación

#### Proton-Python a las API .de NET del Bus de servicio

Los mensajes de Proton-Python admiten propiedades de la aplicación de los siguientes tipos: **int**, **long**, **float**, **uuid**, **bool**, **string**. En el siguiente código de Python se muestra cómo establecer propiedades en un mensaje con cada uno de estos tipos de propiedades.

```
message.properties[u"TestString"] = u"This is a string"    
message.properties[u"TestInt"] = 1
message.properties[u"TestLong"] = 1000L
message.properties[u"TestFloat"] = 1.5    
message.properties[u"TestGuid"] = uuid.uuid1()    
```

En la API de .NET de Bus de servicio, las propiedades de la aplicación de mensajes se realizan en la colección **Properties** de [BrokeredMessage][]. En el código siguiente se muestra cómo se leen las propiedades de aplicación de un mensaje recibido de un cliente Python.

```
if (message.Properties.Keys.Count > 0)
{
  foreach (string name in message.Properties.Keys)
  {
    Object value = message.Properties[name];
    Console.WriteLine(name + ": " + value + " (" + value.GetType() + ")" );
  }
  Console.WriteLine();
}
```

En la tabla siguiente se muestra cómo se asignan los tipos de propiedad de Python a los tipos de propiedad de .NET.

| Tipo de propiedad de Python | Tipo de propiedad de .NET |
|----------------------|--------------------|
| int | int |
| float | double |
| long | int64 |
| uuid | guid |
| booleano | booleano |
| cadena | cadena |

#### API de .NET del Bus de servicio a Proton-Python

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
message.Properties["TestTimeSpan"] = TimeSpan.FromMinutes(60);
```

El código Python siguiente muestra cómo se leen las propiedades de la aplicación de un mensaje recibido desde un cliente de .NET del Bus de servicio.

```
if message.properties != None:
   for k,v in message.properties.items():         
         print "--   %s : %s (%s)" % (k, str(v), type(v))         
```

En la tabla siguiente se muestra cómo se asignan los tipos de propiedad de .NET a los tipos de propiedad de Python.

| Tipo de propiedad de .NET | Tipo de propiedad de Python | Notas |
|--------------------|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| byte | int | - |
| sbyte | int | - |
| char | char | Proton-Python class |
| short | int | - |
| ushort | int | - |
| int | int | - |
| uint | int | - |
| long | int | - |
| ulong | long | Proton-Python class |
| float | float | - |
| double | float | - |
| decimal | String | Decimal no es compatible actualmente con Proton. |
| bool | bool | - |
| Guid | uuid | Proton-Python class |
| string | string | - |
| DateTime | timestamp | Proton-Python class |
| DateTimeOffset | DescribedType | DateTimeOffset.UtcTicks asignados al tipo AMQP:<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type> |
| TimeSpan | DescribedType | Timespan.Ticks asignados al tipo AMQP:<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type> |
| Uri | DescribedType | Uri.AbsoluteUri asignado al tipo AMQP:<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type> |

### Propiedades estándar

En las tablas siguientes se muestra la asignación entre las propiedades estándar de mensajes de Proton-Python y las propiedades estándar de mensajes de [BrokeredMessage][].

#### Proton-Python a las API .de NET del Bus de servicio

| Proton-Python | .NET del Bus de servicio | Notas |
|----------------------|--------------------------|-----------------------------------------------------------|
| duradero | N/D | El Bus de servicio solo admite mensajes duraderos. |
| prioridad | N/D | El Bus de servicio solo admite una única prioridad de mensaje. |
| Ttl | Message.TimeToLive | Conversión, el TTL de Proton-Python se define en milisegundos. |
| first\_acquirer | n/a | - |
| delivery\_count | n/a | - |
| Id | Message.MessageID | - |
| user\_id | n/a | - |
| address | Message.To | - |
| subject | Message.Label | - |
| reply\_to | Message.ReplyTo | - |
| correlation\_id | Message.CorrelationID | - |
| content\_type | Message.ContentType | - |
| content\_encoding | n/a | - |
| expiry\_time | n/a | - |
| creation\_time | n/a | - |
| group\_id | Message.SessionId | - |
| group\_sequence | n/a | - |
| reply\_to\_group\_id | Message.ReplyToSessionId | - |
| format | n/a | - |

| .NET del Bus de servicio | Proton | Notas |
|-------------------------|------------------------------|-----------------------------------------------------------|
| ContentType | Message.content\_type | - |
| CorrelationId | Message.correlation\_id | - |
| EnqueuedTimeUtc | n/a | - |
| Label | Message.subject | - |
| MessageId | Message.id | - |
| ReplyTo | Message.reply\_to | - |
| ReplyToSessionId | Message.reply\_to\_group\_id | - |
| ScheduledEnqueueTimeUtc | n/a | - |
| SessionId | Message.group\_id | - |
| TimeToLive | Message.ttl | Conversión, el TTL de Proton-Python se define en milisegundos. |
| To | Message.address | - |

## Pasos siguientes

¿Listo para obtener más información? Consulte los siguientes vínculos:

- [Información general sobre AMQP para el Bus de servicio]
- [AMQP de Bus de servicio para Windows Server]

[BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
[AMQP de Bus de servicio para Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx

[Información general sobre AMQP para el Bus de servicio]: service-bus-amqp-overview.md

<!----HONumber=AcomDC_0211_2016-->