<properties
   pageTitle="Descripción general de Reliable Actors de Service Fabric | Microsoft Azure"
   description="Introducción al modelo de programación de Service Fabric Reliable Actors"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Introducción a Service Fabric Reliable Actors.
La API de Reliable Actors es uno de los dos marcos de alto nivel proporcionados por [Service Fabric](service-fabric-technical-overview.md), junto con la [API de Reliable Services](service-fabric-reliable-services-introduction.md).

En función del patrón de actor, la API de Reliable Actors proporciona un modelo de programación asincrónico y uniproceso que simplifica el código mientras se beneficia de la confiabilidad y escalabilidad que Service Fabric proporciona.

## Actores
Los actores son componentes de subproceso único aislados que encapsulan el comportamiento y el estado. Son similares a los objetos de .NET y, por tanto, ofrecen un modelo de programación natural. Cada actor es una instancia de un tipo de actor, similar a la forma en que un objeto de .NET es una instancia de un tipo de .NET. Por ejemplo, un tipo de actor puede implemente la funcionalidad de una calculadora, y muchos actores de ese tipo podrían distribuirse por varios nodos de un clúster. Cada uno de esos actores se identifica de forma única mediante un identificador de actor.

## Definición e implementación de las interfaces de actor

Los actores interactúan con el resto del sistema, incluidos otros actores, mediante el intercambio de mensajes asincrónicos con un patrón de solicitud-respuesta. Estas interacciones se definen en una interfaz como métodos asincrónicos. Por ejemplo, la interfaz de un tipo de actor que implementa la funcionalidad de una calculadora se puede definir del modo indicado a continuación:

```csharp
public interface ICalculatorActor : IActor
{
    Task<double> AddAsync(double valueOne, double valueTwo);
    Task<double> SubtractAsync(double valueOne, double valueTwo);
}
```

Un tipo de actor podría implementar esta interfaz de la siguiente manera:

```csharp
public class CalculatorActor : StatelessActor, ICalculatorActor
{
    public Task<double> AddAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne + valueTwo);
    }

    public Task<double> SubtractAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne - valueTwo);
    }
}
```

Dado que las invocaciones de método y sus respuestas tienen como resultado final solicitudes de red en el clúster, los argumentos y el tipo de resultado de la tarea que devuelve deben ser serializables por parte de la plataforma. En particular, deben ser [serializables mediante contrato de datos](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

> [AZURE.TIP]El tiempo de ejecución de Service Fabric Actors emite algunos [eventos y contadores de rendimiento relacionados con los métodos de actor](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Son útiles para la supervisión del rendimiento y los diagnósticos.

Es conveniente comentar las siguientes reglas relativas a métodos de interfaz de actor: 
- No se pueden sobrecargar los métodos de la interfaz de actor. 
- Los métodos de la interfaz de actor no pueden contener parámetros out, ref u optional.

## Comunicación con actores
### El proxy de actor
La API de cliente de Reliable Actors ofrece comunicación entre una instancia de actor y un cliente de actor. Para comunicarse con un actor, un cliente crea un objeto proxy de actor que implementa la interfaz del actor. El cliente interactúa con el actor mediante la invocación de métodos en el objeto proxy. El proxy de actor puede usarse para la comunicación de cliente a actor y de actor a actor. Siguiendo con nuestro ejemplo de la calculadora, el código de cliente de un actor de calculadora podría escribirse como sigue:

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

Tenga en cuenta que los dos tipos de datos que se usan para crear el objeto de proxy de actor son el identificador del actor y el nombre de la aplicación. El identificador del actor es un identificador que identifica de forma única al actor, mientras que el nombre de la aplicación identifica a la [Aplicación de Service Fabric](service-fabric-reliable-actors-platform.md#service-fabric-application-model-concepts-for-actors) en que se implementa el actor.

### Duración del actor

Los actores de Service Fabric son virtuales, lo que significa que su duración no está vinculada a su representación en memoria. Como resultado, no es necesario crearlas ni destruirlas explícitamente. El tiempo de ejecución de los actores activa automáticamente un actor la primera vez que recibe una solicitud para ese actor. Si no se usa un actor durante un período determinado, el tiempo de ejecución de actores recopila el objeto en memoria como elemento no utilizado. También mantendrá conocimientos de la existencia del actor por si es necesario reactivarlo más adelante. Para obtener más información, vea [Ciclo de vida de un actor y recolección de elementos no utilizados](service-fabric-reliable-actors-lifecycle.md).

### Transparencia de ubicación y conmutación por error automática

Para proporcionar una alta confiabilidad y escalabilidad, Service Fabric distribuye actores en el clúster y los migra automáticamente desde los nodos con errores hasta las correctos según sea necesario. La clase `ActorProxy` del cliente realiza la resolución necesaria para localizar el actor mediante el identificador [partición](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) y abre un canal de comunicación con él. `ActorProxy` también reintenta ubicar el actor en caso de que se produzcan errores de comunicación y conmutaciones por error. Esto garantiza que los mensajes se entregarán de manera confiable a pesar de la presencia de errores. Pero esto también significa que es posible que la implementación de un actor reciba mensajes duplicados del mismo cliente.

## Simultaneidad
### Acceso basada en turnos

El tiempo de ejecución de los actores ofrece un modelo simple basado en turnos para obtener acceso a los métodos de actor. Esto significa que no puede haber más un subproceso activo dentro del código del actor en ningún momento.

Un turno consiste en la ejecución completa de un método de actor en respuesta a la solicitud de otros actores o clientes, o la ejecución completa de la devolución de llamada de un [temporizador o recordatorio](service-fabric-reliable-actors-timers-reminders.md). Aunque estos métodos y devoluciones de llamada son asincrónicos, el tiempo de ejecución de los actores no los intercala. Un turno debe completarse por completo antes de que se permita uno nuevo. En otras palabras, la devolución de llamada de un método de actor, un temporizador o recordatorio que se esté ejecutando se debe completar totalmente antes de que se permita una llamada a un método o una devolución de llamada nuevas. Un método o una devolución de llamada se consideran finalizadas si la ejecución ha devuelto desde el método o la devolución de llamada y la tarea devuelta por el método o la devolución de llamada ha terminado. Merece la pena resaltar que la simultaneidad basada en turnos se respeta incluso entre devoluciones de llamada, temporizadores y métodos diferentes.

El tiempo de ejecución de los actores impone la simultaneidad basada en turnos mediante la adquisición de un bloqueo por actor al principio de un turno y su liberación al final del turno. Por lo tanto, la simultaneidad basada en turnos se aplica por actor y no para todos los actores. Las devoluciones de llamada de los métodos de actor, los temporizadores y los recordatorios se pueden ejecutar simultáneamente en nombre de los diferentes actores.

En el ejemplo siguiente se muestran los conceptos anteriores. Supongamos un tipo de actor que implementa dos métodos asincrónicos (digamos *Method1* y *Method2*), un temporizador y un recordatorio. En el diagrama siguiente se muestra un ejemplo de una escala de tiempo de la ejecución de estos métodos y las devoluciones de llamada en nombre de los dos actores (*ActorId1* y *ActorId2*) que pertenecen a este tipo de actor.

![Acceso y simultaneidad basada en turnos de tiempo de ejecución de Reliable Actors][1]

El diagrama anterior sigue estas convenciones:

- Cada línea vertical muestra el flujo lógico de ejecución de un método o una devolución de llamada en nombre de un actor determinado.
- Los eventos marcados en cada línea vertical se producen en orden cronológico, con los eventos más recientes por debajo de los más antiguos.
- Se usan colores diferentes para las escalas de tiempo correspondientes a actores distintos.
- Se usa el resaltado para indicar la duración durante la cual se mantiene el bloqueo por actor en nombre de un método o una devolución de llamada.

Los puntos siguientes sobre el diagrama anterior son relevantes:

- Cuando *Method1* se ejecuta en nombre de *ActorId2* en respuesta a la solicitud de cliente *xyz789*, llega otra solicitud de cliente (*abc123*) que también requiere que *Method1* lo ejecute *ActorId2*. Sin embargo, la segunda ejecución de *Method1* no comienza hasta que se haya completado la ejecución anterior. De forma similar, un recordatorio que haya registrado *ActorId2* se activa cuando *Method1* se ejecuta en respuesta a la solicitud de cliente *xyz789*. La devolución de llamada del recordatorio se ejecuta solo después de que las dos ejecuciones de *Method1* se hayan completado. Todo esto se debe a la simultaneidad basada en turnos que se exige para *ActorId2*.
- De forma similar, también se aplica la simultaneidad basada en turnos para *ActorId1*, como se muestra en la ejecución de *Method1*, *Method2* y la devolución de llamada del temporizador en nombre de *ActorId1* suceden en serie.
- La ejecución de *Method1* en nombre de *ActorId1* se superpone con su ejecución en nombre de *ActorId2*. Esto es así porque solo se exige la simultaneidad basada en turnos dentro de un actor y no para todos los actores.
- En algunas ejecuciones de métodos y devoluciones de llamada, el elemento `Task` que devuelve el método o la devolución de llamada se completa después de la devolución del método. En otras palabras, `Task` ya se ha completado antes de la devolución del método o la devolución de llamada. En ambos casos, el bloqueo por actor se libera solo después de la devolución del método o la devolución de llamada, y `Task` finaliza.

### Reentrada

El tiempo de ejecución de los actores permite la reentrada de forma predeterminada. Esto significa que si un método de actor del *actor A* llama a un método del *actor B*, que a su vez llama a otro método del *actor A*, se permite que ese método se ejecute. Esto es porque forma parte del mismo contexto lógico de la cadena de llamada. Todas las llamadas del temporizador y el recordatorio comienzan con el nuevo contexto de llamada lógico. Vea [Reentrada de Reliable Actors](service-fabric-reliable-actors-reentrancy.md) para obtener más detalles.

### Ámbito de garantías de simultaneidad

El tiempo de ejecución de los actores ofrece estas garantías de simultaneidad en situaciones donde controla la invocación de estos métodos. Por ejemplo, ofrece estas garantías para las invocaciones de método que se realizan en respuesta a una solicitud de cliente y para las devoluciones de llamada de temporizadores y recordatorios. Sin embargo, si el código del actor invoca directamente a estos métodos fuera de los mecanismos que ofrece el tiempo de ejecución de los actores, el tiempo de ejecución no puede ofrecer ninguna garantía de simultaneidad. Por ejemplo, si el método se invoca en el contexto de una tarea que no está asociada con la tarea que han devuelto los métodos de actor, el tiempo de ejecución no puede ofrecer garantías de simultaneidad. Si el método se invoca desde un subproceso que el actor crea por sí mismo, entonces el tiempo de ejecución tampoco puede proporcionar garantías de simultaneidad. Por lo tanto, para realizar operaciones en segundo plano, los actores deben usar [temporizadores de actor o recordatorios de actor](service-fabric-reliable-actors-timers-reminders.md) que respeten la simultaneidad basada en turnos.

> [AZURE.TIP] El tiempo de ejecución de Service Fabric Actors emite algunos [eventos y contadores de rendimiento relacionados con la simultaneidad](service-fabric-reliable-actors-diagnostics.md#concurrency-events-and-performance-counters). Son útiles para la supervisión del rendimiento y los diagnósticos.

## Administración de estados de los actores
Puede usar Service Fabric para crear actores con o sin estado.

### Actores sin estado
Los actores sin estado, que se derivan de la clase base `StatelessActor`, no tiene ningún estado administrado por el tiempo de ejecución de los actores. Sus variables miembro se conservan a lo largo de su duración en memoria, como cualquier otro tipo .NET. Sin embargo, cuando se recolectan como elementos no necesarios tras un período de inactividad, su estado se pierde. De forma similar, el estado puede perderse debido a las conmutaciones por error, que se producen durante las actualizaciones, las operaciones de equilibrio de recursos o como consecuencia de errores en el proceso de actor o en su nodo de hospedaje.

A continuación se muestra un ejemplo de un actor sin estado:

```csharp
class HelloActor : StatelessActor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

### Actores con estado
Los actores con estado tienen un estado que debe conservarse entre las recolecciones de elementos no utilizados y las conmutaciones por error. Se derivan de `StatefulActor<TState>`, donde `TState` es el tipo de estado que debe conservarse. Es posible obtener acceso al estado en los métodos de actor a través de la propiedad `State` en la clase base.

A continuación se muestra un ejemplo de un actor con estado que obtiene acceso al estado:

```csharp
class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

El estado del actor se conserva en recolecciones de elementos no utilizados y conmutaciones por error al mantenerlos en el disco y replicándolos en varios nodos del clúster. Esto significa que, como los argumentos de método y los valores devueltos, el tipo de estado del actor debe ser [serializable por contrato de datos](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

> [AZURE.NOTE] Vea el artículo sobre las [notas de serialización de Reliable Actors](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) para obtener información detallada sobre cómo se deben definir las interfaces y los tipos de estado de los actores.

#### proveedores de estado de actor
Un proveedor de estado de actor proporciona el almacenamiento y la recuperación del estado. Los proveedores de estado se puede configurar por actor o para todos los actores dentro de un ensamblado, mediante el atributo específico de proveedor de estado. Cuando se activa un actor, su estado se carga en memoria. Cuando se completa un método de actor, el tiempo de ejecución de actores guarda automáticamente el estado modificado mediante una llamada a un método en el proveedor de estado. Si se produce un error durante la operación de **almacenamiento**, el tiempo de ejecución de los actores crea una nueva instancia de actor y carga el último estado coherente desde el proveedor de estados.

De forma predeterminada, los actores con estado usan el proveedor de estado de actor de almacén de pares clave-valor, que se integra en el almacén de pares clave-valor proporcionado por la plataforma Service Fabric. Para obtener más información, vea el tema sobre [opciones de proveedores de estado](service-fabric-reliable-actors-platform.md#actor-state-provider-choices).

> [AZURE.TIP] El tiempo de ejecución de los actores emite algunos [eventos y contadores de rendimiento relacionados con la administración de estados de los actores](service-fabric-reliable-actors-diagnostics.md#actor-state-management-events-and-performance-counters). Son útiles para la supervisión del rendimiento y los diagnósticos.

#### Métodos de solo lectura
De forma predeterminada, el tiempo de ejecución de los actores guarda el estado del actor tras la finalización de la llamada a un método de actor, la devolución de llamada de un temporizador o la devolución de llamada de un recordatorio. No se permite ninguna otra llamada de actor hasta que la operación de almacenamiento de estado se complete.

Puede haber métodos de actor que no modifican el estado. En ese caso, el tiempo adicional que se dedica a guardar el estado puede afectar al rendimiento general del sistema. Para evitarlo, se pueden marcar los métodos y las devoluciones de llamada de temporizador que no modifiquen el estado como solo lectura.

En el ejemplo siguiente se muestra cómo marcar un método de actor como de solo lectura mediante el atributo `Readonly`:

```csharp
public interface IVoicemailBoxActor : IActor
{
    [Readonly]
    Task<List<Voicemail>> GetMessagesAsync();
}
```

Las devoluciones de llamada de temporizador se pueden marcar con el atributo `Readonly` de forma similar. Para los recordatorios, el indicador de solo lectura se pasa como argumento al método `RegisterReminder` que se invoca para registrar el recordatorio.

## Pasos siguientes
[Ciclo de vida de un actor y recolección de elementos no utilizados](service-fabric-reliable-actors-lifecycle.md)

[Recordatorios y temporizadores de los actores](service-fabric-reliable-actors-timers-reminders.md)

[Eventos de actor](service-fabric-reliable-actors-events.md)

[Reentrada de actor](service-fabric-reliable-actors-reentrancy.md)

[Uso de la plataforma Service Fabric por parte de Reliable Actors](service-fabric-reliable-actors-platform.md)

[Configuración del actor KVSActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)

[Supervisión del rendimiento y diagnósticos de los actores](service-fabric-reliable-actors-diagnostics.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png

<!---HONumber=AcomDC_0121_2016-->