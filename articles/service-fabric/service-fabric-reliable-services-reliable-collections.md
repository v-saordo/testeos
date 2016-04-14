<properties
   pageTitle="Reliable Collections | Microsoft Azure"
   description="Los servicios con estado de Service Fabric proporcionan colecciones confiables que le permiten escribir aplicaciones en la nube altamente disponibles, escalables y de baja latencia."
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/11/2015"
   ms.author="mcoskun"/>

# Introducción a Reliable Collections en los servicios con estado de Azure Service Fabric

Reliable Collections le permite escribir aplicaciones en la nube altamente disponibles, escalables y de baja latencia como si estuviese escribiendo aplicaciones para un solo equipo. Las clases del espacio de nombres **Microsoft.ServiceFabric.Data.Collections** proporcionan un conjunto de colecciones de serie que hacen que el estado tenga una elevada disponibilidad. Los desarrolladores solo necesitan programar en las API de Reliable Collections y permiten que Reliable Collections administren el estado local y el replicado.

La diferencia clave entre Colecciones fiables y otras tecnologías de alta disponibilidad (por ejemplo, Redis, el servicio Tabla de Azure y el servicio Cola de Azure) es que el estado se guarda localmente en la instancia del servicio mientras también se ofrece una elevada disponibilidad. Esto significa que:

- Todas las lecturas son locales, lo que provoca una latencia baja y lecturas de alto rendimiento.
- Todas las escrituras producen el número mínimo de operaciones de E/S de red, lo cual provoca una baja latencia y escrituras de alto rendimiento.

![Imagen de la evolución de colecciones](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Reliable Collections se puede considerar como la evolución natural de las clases **System.Collections**: un nuevo conjunto de colecciones que están diseñadas para la nube y aplicaciones de varios equipos sin aumentar la complejidad para los desarrolladores. De esta forma, Reliable Collections tienen los estados siguientes:

- Replicado: se replican los cambios de estado para obtener una alta disponibilidad.
- Guardado: los datos se conservan en el disco para obtener una mayor durabilidad frente a las interrupciones a gran escala (por ejemplo, un corte de alimentación del centro de datos).
- Asincrónico: las API son asincrónicas para asegurarse de que los subprocesos no se bloquean durante las E/S.
- Transaccional: las API usan la abstracción de transacciones para que pueda administrar fácilmente varias instancias de Reliable Collections dentro de un servicio.

Colecciones fiables proporcionan garantías de homogeneidad sólidas de fábrica con el fin de facilitar el razonamiento sobre el estado de la aplicación. La coherencia fuerte se consigue asegurándose de que la transacción finaliza únicamente tras aplicar toda la transacción en un quórum de réplicas, incluida la principal. Para conseguir una coherencia más débil, las aplicaciones pueden efectuar la confirmación al cliente o al solicitante antes de devolver la confirmación asincrónica.

Las API de Reliable Collections son una evolución de las API de colecciones simultáneas (que se encuentran en el espacio de nombres **System.Collections.Concurrent**):

- Asincrónico: devuelve una tarea debido a que, a diferencia de las colecciones simultáneas, las operaciones se replican y se guardan.
- Ningún parámetro de salida: usa **ConditionalResult<T>** para devolver un valor en lugar de los parámetros de salida y un booleano. **ConditionalResult<T>** es como **Nullable<T>** pero no requiere que T sea un struct.
- Transacciones: utiliza un objeto de transacción para permitir que el usuario agrupe acciones en varias colecciones fiables en una transacción.

Por el momento, el espacio de nombres **Microsoft.ServiceFabric.Data.Collections** contiene dos colecciones:

- [Diccionario confiable](https://msdn.microsoft.com/library/azure/dn971511.aspx): representa una colección replicada, transaccional y asincrónica de pares clave/valor. Similar a **ConcurrentDictionary**, la clave y el valor pueden ser de cualquier tipo.
- [Cola confiable](https://msdn.microsoft.com/library/azure/dn971527.aspx): representa una cola estricta replicada, transaccional y asincrónica de tipo primero en entrar primero en salir (FIFO). Similar a **ConcurrentQueue**, el valor puede ser de cualquier tipo.

## Niveles de aislamiento
El nivel de aislamiento es una medida del grado de aislamiento conseguido. Aislamiento significa que una transacción se comporta como lo haría en un sistema que solo permite una transacción en curso en un determinado tiempo.

Colecciones fiables elige automáticamente el nivel de aislamiento que se usará para una operación de lectura determinada dependiendo del funcionamiento y el rol de la réplica.

Hay dos niveles de aislamiento que se admiten en Colecciones confiables:

- **Repeatable Read**: especifica que las instrucciones no pueden leer datos que han sido modificados pero aún no confirmados por otras transacciones y que ninguna otra transacción puede modificar los datos leídos por la transacción actual hasta que esta finalice. Para más información, consulte [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
- **Snapshot**: especifica que los datos leídos por cualquier instrucción de una transacción sean la versión coherente, desde el punto de vista transaccional, de los datos existentes al comienzo de la transacción. La transacción solo puede reconocer las modificaciones de datos que se confirmaron antes del inicio de la transacción. Las modificaciones de datos realizadas por otras transacciones después del inicio de la transacción actual no son visibles para las instrucciones que se ejecutan en la transacción actual. El efecto es como si las instrucciones de una transacción obtienen una instantánea de los datos confirmados tal como se encontraban al inicio de la transacción. Para más información, consulte [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

El diccionario fiable y la cola fiable admiten la lectura de las escrituras. En otras palabras, cualquier escritura dentro de una transacción será visible en una lectura posterior que pertenezca a la misma transacción.

### Diccionario fiable
| Operación\\rol | Principal | Secundario |
| --------------------- | :--------------- | :--------------- |
| Lectura de una sola entidad | Lectura repetible | Instantánea |
| Enumeración\\recuento | Instantánea | Instantánea |

### Cola fiable
| Operación\\rol | Principal | Secundario |
| --------------------- | :--------------- | :--------------- |
| Lectura de una sola entidad | Instantánea | Instantánea |
| Enumeración\\recuento | Instantánea | Instantánea |

## Modelo de persistencia
Reliable State Manager y Reliable Collections siguen un modelo de persistencia que se denomina Registro y punto de control. Se trata de un modelo en el que cada cambio de estado se registra en el disco y solo se aplica en la memoria. El mismo estado completo se guarda en ocasiones (también conocido como Punto de comprobación). La ventaja que proporciona es:

- Los deltas se convierten en escrituras de solo anexación secuenciales en disco para mejorar el rendimiento.

Para comprender mejor el modelo de registro y de punto de control, primero analicemos el escenario de discos infinitos. Reliable State Manager registra cada operación antes de replicarse. Esto permite que Reliable Collections se apliquen solamente a la operación de la memoria. Dado que los registros se conservan, incluso cuando la réplica falla y debe reiniciarse, el Administrador de estado fiable tiene suficiente información en sus registros para reproducir todas las operaciones que ha perdido la réplica. Como el disco es infinito, las entradas de registro nunca deberán quitarse y Reliable Collection solo tiene que administrar el estado en memoria.

Ahora veamos el escenario de disco finito. En un determinado momento, Reliable State Manager se quedará sin espacio en disco. Antes de que suceda, Reliable State Manager debe truncar su registro para liberar espacio para los registros más recientes. Solicitará a Reliable Collections la comprobación de su estado en memoria en el disco. Es responsabilidad de la colección fiable que conserve su estado hasta ese punto. Una vez que las colecciones fiables completen sus puntos de control, el administrador de estado fiable puede truncar el registro para liberar espacio en disco. De este modo, cuando la réplica debe reiniciarse, las colecciones fiables recuperarán su estado de punto de comprobación y el administrador de estado fiable recuperará y reproducirá todos los cambios de estado que se han producido desde el punto de control.

## Bloqueo
En Reliable Collections, todas las transacciones tienen dos fases: una transacción no libera los bloqueos que ha adquirido hasta que la transacción finaliza con una anulación o confirmación.

Colecciones fiables siempre tiene bloqueos exclusivos. Para las lecturas, el bloqueo depende de dos factores. Cualquier operación de lectura realizada mediante aislamiento Snapshot no presenta bloqueo. Cualquier operación Repeatable Read usa bloqueos compartidos de manera predeterminada. Sin embargo, para cualquier operación de lectura que admita Repeatable Read, el usuario puede solicitar un bloqueo de actualización en lugar del bloqueo compartido. El bloqueo de actualización es un bloqueo asimétrico que se isa para evitar una forma común de interbloqueo que se produce cuando varias transacciones bloquean recursos para una posible actualización en un momento posterior.

A continuación se puede encontrar la matriz de compatibilidad de bloqueo:

| Solicitar \\ concedido | None | Compartido | Actualizar | Exclusivo |
| ----------------- | :----------- | :----------- | :---------- | :----------- |
| Compartido | Ningún conflicto | Ningún conflicto | Conflicto | Conflicto |
| Actualizar | Ningún conflicto | Ningún conflicto | Conflicto | Conflicto |
| Exclusivo | Ningún conflicto | Conflicto | Conflicto | Conflicto |

Tenga en cuenta que el argumento de tiempo de espera de las API de Reliable Collections se usa como una detección de interbloqueo. Por ejemplo, dos transacciones (T1 y T2) están intentando leer y actualizar K1. Es posible que se interbloqueen, porque ambas acaban disponiendo de bloqueo compartido. En este caso, una o ambas operaciones superará el tiempo de espera.

Tenga en cuenta que la situación de interbloqueo anterior es un buen ejemplo de cómo puede el bloqueo de actualización impedir interbloqueos.

## Recomendaciones

- No modifique un objeto de tipo personalizado devuelto por las operaciones de lectura (por ejemplo, **TryPeekAsync** o **TryGetAsync**). Las colecciones fiables, igual que las colecciones simultáneas, devuelven una referencia a los objetos y no una copia.
- No haga una copia en profundidad del objeto devuelto de tipo personalizado antes de modificarlo. Por ello, los struct y los tipos integrados son una transmisión por valor, no es necesario hacer una copia en profundidad en ellos.
- No utilice **TimeSpan.MaxValue** para los tiempos de espera. Los tiempos de espera se deben utilizar para detectar interbloqueos.
- No cree una transacción dentro de la instrucción `using` de otra transacción, ya que puede provocar interbloqueos.

Algunos aspectos que debe tener en cuenta:

- El tiempo de espera predeterminado es de 4 segundos para todas las API de Reliable Collection. La mayoría de los usuarios no deben reemplazar esto.
- El token de cancelación predeterminado es **CancellationToken.None** en todas las API de Reliable Collections.
- El parámetro de tipo de clave (*TKey*) para un diccionario confiable debe implementar correctamente **GetHashCode()** y **Equals()**. Las claves deben ser inmutables.
- Las enumeraciones son instantáneas coherentes dentro de una colección. Sin embargo, las enumeraciones de varias colecciones no son coherentes entre las colecciones.
- Para lograr una alta disponibilidad para las colecciones confiables, cada servicio debe tener al menos un destino y un tamaño de conjunto de réplicas mínimo de 3.

## Pasos siguientes

- [Introducción a Reliable Services de Service Fabric de Microsoft Azure](service-fabric-reliable-services-quick-start.md)
- [Introducción a los servicios de la API web de Microsoft Azure Service Fabric con autohospedaje OWIN](service-fabric-reliable-services-communication-webapi.md)
- [Uso avanzado del modelo de programación de servicios fiables](service-fabric-reliable-services-advanced-usage.md)
- [Referencia para desarrolladores de colecciones confiables](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

<!---HONumber=AcomDC_0107_2016-->