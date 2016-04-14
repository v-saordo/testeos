<properties
   pageTitle="Ciclo de vida de Reliable Actors | Microsoft Azure"
   description="Explica el ciclo de vida y la recolección de elementos no utilizados en Reliable Actors de Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="amanbha"/>


# Ciclo de vida de un actor y recolección de elementos no utilizados
Un actor se activa cuando se le llama por primera vez y se desactiva (el runtime de Actors recolecta los elementos no utilizados) si no se usa durante un período de tiempo determinado. Para configurar este período de tiempo, consulte la sección siguiente sobre la recolección de actores no utilizados.

¿Qué ocurre durante la activación del actor?

- Cuando se recibe una llamada para un actor y no hay ninguno activo, se crea uno nuevo.
- Se carga el estado del actor (si se trata de un actor con estado).
- Se llama al método `OnActivateAsync` (que se puede invalidar en la implementación del actor).
- Se agrega el actor a una tabla de actores activos.

¿Qué ocurre durante la desactivación del actor?

- Cuando un actor no se usa durante un período de tiempo, se quita de la tabla de actores activos.
- Se llama al método `OnDeactivateAsync` (que se puede invalidar en la implementación del actor). y esto borra todos los temporizadores del actor.

> [AZURE.TIP]El tiempo de ejecución de actores de Fabric emite algunos [eventos relacionados con la activación y desactivación de actores](service-fabric-reliable-actors-diagnostics.md#actor-activation-and-deactivation-events). Son útiles para la supervisión del rendimiento y los diagnósticos.

## Recolección de actores no utilizados
El runtime de Actors busca periódicamente los actores que no se han usado durante un período de tiempo y los desactiva. Una vez desactivados, Common Language Runtime (CLR) los puede recolectar como elementos no utilizados.

¿Qué se considera como "en uso" para el propósito de la recolección de elementos no utilizados?

- Se recibe una llamada.
- Se invoca al método `IRemindable.ReceiveReminderAsync` (se aplica solo si el actor usa recordatorios).

Es importante destacar que si el actor usa temporizadores y se invoca la devolución de llamada de su temporizador, **no** cuenta como "en uso".

Antes de entrar en detalles sobre la recolección de elementos no utilizados, es importante definir los términos siguientes:

- *Intervalo de detección*. Es el intervalo durante el que el runtime de Actors examina la tabla de actores activos para encontrar los actores que pueden recolectarse como elementos no utilizados. El valor predeterminado es 1 minuto.
- *Tiempo de espera de inactividad*. Es el tiempo que un actor debe permanecer sin usarse (inactivo) antes de poder recolectarlo como elemento no utilizado. El valor predeterminado es 60 minutos.

Normalmente no es necesario cambiar estos valores pero, si lo fuera, estos intervalos pueden modificarse en el nivel de ensamblado, para todos los tipos de actores de ese ensamblado, o en el nivel de tipo de actor, mediante el atributo `ActorGarbageCollection`. En el ejemplo siguiente se muestra el cambio en los intervalos de la colección de elementos no utilizados de HelloActor.

```csharp
[ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

Para cambiar el valor predeterminado del atributo `ActorGarbageCollection` en el nivel de ensamblado, agregue el siguiente fragmento de código a `AssemblyInfo.cs`.

```csharp
[assembly: ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
```

El runtime de Actors realiza un seguimiento del tiempo que ha estado inactivo (es decir, sin usarse) cada actor de la tabla de actores activos. Dicho runtime comprueba cada uno de los actores para ver si puede recolectarse como elemento no utilizado cada `ScanIntervalInSeconds` y, si ha estado inactivo durante `IdleTimeoutInSeconds`, lo recolecta.

Cada vez que se usa un actor, su tiempo de inactividad se restablece a 0. Después de esto, el actor solo se puede recolectar como elemento no utilizado si permanece de nuevo inactivo durante `IdleTimeoutInSeconds`. Recuerde que se considera que se ha usado un actor si se usa un método de interfaz de actor o una devolución de llamada de recordatorio. **No** se considera que un actor se haya usado si se ejecuta la devolución de llamada del temporizador.

En el diagrama siguiente se muestra un ejemplo para ilustrar estos conceptos.

![Ejemplo de tiempo de inactividad][1]

En el ejemplo se supone que solo hay un actor activo en la tabla de actores activos. Además, se muestra el impacto de las llamadas a los métodos de actor, los recordatorios y los temporizadores en la duración de este actor. Los puntos siguientes sobre el ejemplo son relevantes:

- ScanInterval e IdleTimeout se establecen en 5 y 10, respectivamente. (Las unidades no importan en este caso, ya que el único propósito es ilustrar el concepto).
- La detección de los actores para buscar los que se pueden recolectar como elementos no utilizados se produce en T=0,5,10,15,20,25, como se define al establecerse el elemento ScanInterval en 5.
- Un temporizador periódico se activa en T=4,8,12,16,20,24 y se ejecuta su devolución de llamada. No afecta al tiempo de inactividad del actor.
- Una llamada al método de actor en T=7 restablece el tiempo de inactividad a 0 y retrasa la recolección como elemento no utilizado del actor.
- Una devolución de llamada de un recordatorio del actor se ejecuta en T=14 y retrasa aún más la recolección como elemento no utilizado del actor.
- Durante la detección de la recolección de elementos no utilizados en T=25, el tiempo de inactividad del actor finalmente supera el IdleTimeout de 10 y se recolecta el actor como elemento no utilizado.

Tenga en cuenta que un actor nunca se recolectará como elemento no utilizado mientras se ejecuta uno de sus métodos, independientemente del tiempo que se dedique a la ejecución de ese método. Como se mencionó anteriormente, la ejecución de los métodos de interfaz del actor y las devoluciones de llamada del recordatorio impide la recolección al restablecer el tiempo de inactividad del actor a 0. La ejecución de devoluciones de llamada del temporizador no restablece el tiempo de inactividad a 0. Sin embargo, la recolección del actor como elemento no utilizado se aplaza hasta que la devolución de llamada del temporizador ha finalizado su ejecución.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png

<!---HONumber=AcomDC_0121_2016-->