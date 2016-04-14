<properties
   pageTitle="Introducción a Reliable Services | Microsoft Azure"
   description="Introducción a la creación de una aplicación de Service Fabric de Microsoft Azure mediante servicios con y sin estado."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/15/2015"
   ms.author="vturecek"/>

# Introducción a Reliable Services de Service Fabric

Una aplicación de Service Fabric de Azure contiene uno o varios servicios que ejecutan su código. En esta guía se muestra cómo crear aplicaciones de Service Fabric con estado y sin estado mediante [Reliable Services](service-fabric-reliable-services-introduction.md).

## Creación de un servicio sin estado

Un servicio sin estado es un tipo de servicio que es el que se encuentra actualmente en las aplicaciones en la nube. Se considera sin estado porque el propio servicio no contiene datos que tengan que almacenarse de manera fiable o requieran tener una alta disponibilidad. Si se cierra una instancia de un servicio sin estado, todo su estado interno se pierde. En este tipo de servicio, el estado debe almacenarse en un almacén externo, como Tablas de Azure o una base de datos SQL, para que resulte fiable y tenga una alta disponibilidad.

Inicie Visual Studio 2015 RC como administrador y cree un nuevo proyecto de aplicación de Service Fabric denominado *HelloWorld*:

![Uso del cuadro de diálogo Nuevo proyecto para crear una nueva aplicación de Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

Después, cree un proyecto de servicio sin estado denominado *HelloWorldStateless*:

![En el segundo cuadro de diálogo, cree un proyecto de servicio sin estado.](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

La solución contiene ahora dos proyectos:

 - *HelloWorld*. Se trata del proyecto de *aplicación* que contiene sus *servicios*. También contiene el manifiesto de aplicación que describe la aplicación y un número de scripts de PowerShell que le ayudarán a implementar la aplicación.
 - *HelloWorldStateless*. Este es el proyecto de servicio. Contiene la implementación del servicio sin estado.


## Implementación del servicio

Abra el archivo **HelloWorldStateless.cs** en el proyecto de servicio. En Service Fabric, un servicio puede ejecutar cualquier lógica de negocios. La API de servicios proporciona dos puntos de entrada para el código:

 - Un método de punto de entrada abierto, denominado *RunAsync*, donde puede comenzar la ejecución de las cargas de trabajo. Entre estas se pueden incluir las cargas de trabajo de proceso de larga ejecución.

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - Un punto de entrada de comunicación en el que puede conectar su pila de comunicación preferida, como la API web de ASP.NET. Aquí es donde puede empezar a recibir las solicitudes de usuarios y otros servicios.

```C#
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

En este tutorial, nos centraremos en el método de punto de entrada `RunAsync()`. Aquí es donde puede comenzar a ejecutar el código de inmediato. La plantilla de proyecto incluye una implementación de ejemplo de `RunAsync()` que incrementa un recuento gradual.

> [AZURE.NOTE]Para obtener más información acerca de cómo trabajar con una pila de comunicación, consulte [Introducción a los servicios de la API web de Microsoft Azure Service Fabric con autohospedaje OWIN](service-fabric-reliable-services-communication-webapi.md).


### RunAsync

```C#
protected override async Task RunAsync(CancellationToken cancelServiceInstance)
{
    // TODO: Replace the following sample code with your own logic.

    int iterations = 0;
    // This service instance continues processing until the instance is terminated.
    while (!cancelServiceInstance.IsCancellationRequested)
    {

        // Log what the service is doing
        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", iterations++);

        // Pause for 1 second before continue processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServiceInstance);
    }
}
```

La plataforma llama a este método cuando hay una instancia del servicio colocada y preparada para ejecutarse. En el caso de un servicio sin estado, esto simplemente significa que la instancia de servicio está abierta. Se proporciona un token de cancelación para coordinar cuando la instancia de servicio debe cerrarse. En Service Fabric, este ciclo de apertura y cierre de una instancia de servicio puede producirse muchas veces durante la vigencia del servicio en su conjunto. Esto puede ocurrir por diversos motivos, incluidos los siguientes:

- El sistema mueve las instancias de servicio para equilibrar los recursos.
- Se producen errores en el código.
- Se actualiza la aplicación o el sistema.
- El hardware subyacente experimenta una interrupción.

El sistema es quien se encarga de la coordinación para mantener el servicio con una alta disponibilidad y correctamente equilibrado.

`RunAsync()` se ejecuta en su propia tarea. Tenga en cuenta que en el fragmento de código anterior, avanzamos hasta un bucle de tipo *while* No es necesario programar una tarea independiente para la carga de trabajo. La cancelación de la carga de trabajo es un esfuerzo cooperativo organizado por el token de cancelación proporcionado. El sistema esperará a que la tarea finalice (ya sea mediante una realización correcta, una cancelación o con errores) antes de moverla. Es importante respetar el token de cancelación, finalizar cualquier trabajo y cerrar `RunAsync()` lo antes posible cuando el sistema solicita la cancelación.

En este ejemplo de servicio sin estado, el número se almacena en una variable local. Pero dado que se trata de un servicio sin estado, el valor almacenado solo existe para el ciclo de vida actual de la instancia de su servicio. Cuando se mueve o se reinicia el servicio, el valor se pierde.

## Creación de un servicio con estado

Service Fabric presenta un nuevo tipo de servicio que es con estado. Un servicio con estado puede mantener el estado confiablemente dentro del propio servicio, ubicado junto al código que lo está usando. Service Fabric ofrece una alta disponibilidad del estado sin necesidad de conservarlo en un almacén externo.

Para cambiar el valor sin estado del contador a una alta disponibilidad y persistencia, incluso cuando se mueve o reinicia el servicio, se necesita un servicio con estado.

En la misma aplicación *HelloWorld*, puede agregar un nuevo servicio haciendo clic con el botón derecho en el proyecto de aplicación y seleccionando **Agregar Fabric Service**.

![Agregue un servicio a su aplicación Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

Seleccione **Servicio con estado** y asígnele el nombre *HelloWorldStateful*. Haga clic en **Aceptar**.

![Uso del cuadro de diálogo Nuevo proyecto para crear un nuevo servicio con estado de Service Fabric](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

La aplicación ahora debería tener dos servicios: el servicio sin estado *HelloWorld* y el servicio con estado *HelloWorldStateful*.

Abra **HelloWorldStateful.cs** en *HelloWorldStateful*, que contiene el método RunAsync siguiente:

```C#
protected override async Task RunAsync(CancellationToken cancelServicePartitionReplica)
{
    // TODO: Replace the following sample code with your own logic.

    // Gets (or creates) a replicated dictionary called "myDictionary" in this partition.
    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    // This partition's replica continues processing until the replica is terminated.
    while (!cancelServicePartitionReplica.IsCancellationRequested)
    {

        // Create a transaction to perform operations on data within this partition's replica.
        using (var tx = this.StateManager.CreateTransaction())
        {

            // Try to read a value from the dictionary whose key is "Counter-1".
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

            // Log whether the value existed or not.
            ServiceEventSource.Current.ServiceMessage(this, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            // If the "Counter-1" key doesn't exist, set its value to 0
            // else add 1 to its current value.
            await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

            // Committing the transaction serializes the changes and writes them to this partition's secondary replicas.
            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is sent to this partition's secondary replicas.
            await tx.CommitAsync();
        }

        // Pause for one second before continuing processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServicePartitionReplica);
    }
}
```

### RunAsync

Un servicio con estado tiene los mismos puntos de entrada que un servicio sin estado. La diferencia principal es la disponibilidad de Reliable Collections y el administrador de estado. `RunAsync()` funciona de forma similar en servicios con estado y sin estado. Sin embargo, en un servicio con estado, la plataforma realiza un trabajo adicional en su nombre antes de ejecutar `RunAsync()`. En este trabajo se puede incluir el hecho de asegurarse de que el administrador de estado y Reliable Collections están listos para usarse.

### Reliable Collections y el administrador de estado

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

*IReliableDictionary* es una implementación de diccionario que se puede usar para almacenar el estado de manera confiable en el servicio. Esto forma parte de las colecciones [Reliable Collections](service-fabric-reliable-services-reliable-collections.md) integradas en Service Fabric. Con Service Fabric y Reliable Collections, ahora puede almacenar datos directamente en el servicio sin necesidad de un almacén persistente externo. Este planteamiento garantiza la alta disponibilidad de los datos. Service Fabric lo consigue mediante la creación y administración de varias *réplicas* de su servicio para usted. También proporciona una API que elimina las complejidades derivadas de la administración de las réplicas y sus transiciones de estado.

Reliable Collections puede almacenar cualquier tipo de .NET, incluidos los tipos personalizados, con un par de advertencias:

 - Service Fabric hace que su estado presente una alta disponibilidad *replicando* el estado entre nodos y almacenándolo en el disco local. Esto significa que todo lo que se almacena en Reliable Collections debe ser *serializable*. De forma predeterminada, Reliable Collections utiliza [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) para la serialización, por lo que resulta importante asegurarse de que los tipos sean [compatibles con el serializador de contratos de datos](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) cuando se usa el serializador predeterminado.

 - Los objetos se replican para obtener una alta disponibilidad cuando se confirman transacciones en Reliable Collections. Los objetos almacenados en Reliable Collections se mantienen en la memoria local en el servicio. Esto significa que tiene una referencia local al objeto.

    Es importante no modificar las instancias locales de los objetos sin tener que realizar una operación de actualización en la colección fiable de una transacción. Esto se debe a que los cambios no se replicarán automáticamente.

El administrador de estado administra las colecciones Reliable Collections en su lugar. Si lo desea, simplemente solicite al administrador de estado una colección fiable por nombre en cualquier momento y en cualquier lugar en el servicio. El administrador de estado garantiza que obtendrá una referencia. No recomendamos guardar referencias a instancias de colecciones fiables en variables o propiedades de miembros de clase. Debe tener especial cuidado para asegurarse de que se establece la referencia a una instancia en todo momento del ciclo de vida de servicio. El administrador de estado controla este trabajo por usted, y está optimizado para la repetición de visitas.

### Operaciones transaccionales y asincrónicas

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Las colecciones Reliable Collections tienen muchas de las mismas operaciones que sus homólogos `System.Collections.Generic` y `System.Collections.Concurrent`, incluido LINQ. Sin embargo, las operaciones sobre las Colecciones confiables son asincrónicas. Esto es porque las operaciones de escritura con Reliable Collections se *replican*. Para lograr alta disponibilidad, estas operaciones se envían a otras réplicas del servicio en distintos nodos.

También se admiten operaciones *transaccionales*, de manera que pueda mantener el estado coherente entre varias colecciones Reliable Collections. Por ejemplo, puede quitar un elemento de trabajo de una cola confiable, realizar una operación en esta y guardar el resultado en un diccionario fiable, todo en una única transacción. Se trata como una operación atómica, y así se garantiza que toda la operación en su conjunto se completa correctamente o bien ninguna parte de la misma. Si se produce un error después de quitar el elemento de la cola pero antes de guardar el resultado, toda la transacción se revierte y el elemento permanece en la cola para su procesamiento.

## Ejecución de la aplicación

Volvamos ahora a la aplicación *HelloWorld*. Ahora puede compilar e implementar sus servicios. Si presiona **F5**, la aplicación se creará y se implementará en el clúster local.

Después de que los servicios empiecen a ejecutarse, podrá ver los eventos del Seguimiento de eventos para Windows (ETW) generados en una ventana de **Eventos de diagnóstico**. Tenga en cuenta que hay eventos que se muestran desde el servicio sin estado y el servicio con estado en la aplicación. Puede pausar el flujo haciendo clic en el botón **Pausar**. Luego puede también examinar los detalles de un mensaje expandiéndolo.

>[AZURE.NOTE]Antes de ejecutar la aplicación, asegúrese de tener un clúster de desarrollo local ejecutándose. Consulte la [guía de introducción](service-fabric-get-started.md) para obtener información sobre cómo configurar su entorno local.

![Ver eventos de diagnóstico en Visual Studio](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## Pasos siguientes

[Depuración de la aplicación del Service Fabric en Visual Studio](service-fabric-debugging-your-application.md)

[Introducción a los servicios de la API web de Microsoft Azure Service Fabric con autohospedaje OWIN](service-fabric-reliable-services-communication-webapi.md)

[Obtenga más información acerca de las colecciones fiables](service-fabric-reliable-services-reliable-collections.md)

[Implementar una aplicación](service-fabric-deploy-remove-applications.md)

[Actualización de aplicaciones](service-fabric-application-upgrade.md)

[Referencia para desarrolladores de servicios confiables](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=AcomDC_0121_2016-->