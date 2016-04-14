<properties
   pageTitle="Diagnóstico y supervisión de actores | Microsoft Azure"
   description="En este artículo se describen las características de supervisión del rendimiento y diagnósticos en el tiempo de ejecución de Reliable Actors de Service Fabric, incluidos los contadores de rendimiento y los eventos que emite."
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
   ms.date="01/26/2016"
   ms.author="abhisram"/>

# Supervisión de diagnósticos y rendimiento para Reliable Actors
El tiempo de ejecución de Reliable Actors emite eventos [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) y [contadores de rendimiento](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Estos ofrecen información sobre cómo está funcionando el tiempo de ejecución y ayudarán con la solución de problemas y la supervisión de rendimiento.

## Eventos EventSource
El nombre del proveedor de EventSource del tiempo de ejecución de Reliable Actors es "Microsoft-ServiceFabric-Actors". Los eventos de este origen de eventos aparecen en la ventana [Eventos de diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) cuando la aplicación de actor se [depura en Visual Studio](service-fabric-debugging-your-application.md).

Otros ejemplos de herramientas y tecnologías que ayudan a recopilar o ver eventos EventSource son [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md), [Semantic Logging](https://msdn.microsoft.com/library/dn774980.aspx) y [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### Palabras clave
Todos los eventos que pertenecen al EventSource de Reliable Actors están asociados a una o varias palabras clave. Esto habilita el filtrado de los eventos que se recopilan. Se definen los siguientes bits de palabras clave:

|Bit|Descripción|
|---|---|
|0x1|Conjunto de eventos importantes que resume la operación del tiempo de ejecución de Fabric Actors.|
|0x2|Conjunto de eventos que describe las llamadas de método de actor. Para obtener más información, consulte el [tema de introducción sobre actores](service-fabric-reliable-actors-introduction.md#actors).|
|0x4|Conjunto de eventos relacionados con el estado del actor. Para obtener más información, consulte el tema sobre [actores con estado](service-fabric-reliable-actors-introduction.md#stateful-actors).|
|0x8|Conjunto de eventos relacionados con la simultaneidad basada en turnos en el actor. Para obtener más información, consulte el tema sobre [simultaneidad](service-fabric-reliable-actors-introduction.md#concurrency).|

## Contadores de rendimiento
El tiempo de ejecución de Reliable Actors define las siguientes categorías de contador de rendimiento.

|Categoría|Descripción|
|---|---|
|Actor de Service Fabric|Contadores específicos de Azure Service Fabric Actors. Por ejemplo, el tiempo empleado en guardar el estado del actor.|
|Método del actor de Service Fabric|Contadores específicos de los métodos que implementan los agentes de Service Fabric. Por ejemplo, la frecuencia con que se invoca un método de actor.|

Cada una de las categorías anteriores tiene uno o varios contadores.

La aplicación del [Monitor de rendimiento de Windows](https://technet.microsoft.com/library/cc749249.aspx) que está disponible de forma predeterminada en el sistema operativo Windows puede usarse para recopilar y ver los datos del contador de rendimiento. [Diagnósticos de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) es otra opción para recopilar datos del contador de rendimiento y cargarlos en tablas de Azure.

### Nombres de instancias de contador de rendimiento
Un clúster con un gran número de servicios de actor o particiones de servicios de actor tendrá una gran cantidad de instancias de contador de rendimiento de actor. Los nombres de las instancias del contador de rendimiento pueden ayudar a identificar la [partición](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) y el método de actor (si procede) específicos con los que está asociada la instancia de contador de rendimiento.

#### Categoría del actor de Service Fabric
En la categoría `Service Fabric Actor`, los nombres de instancias de contadores tienen el formato siguiente:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* es la representación de cadena del identificador de partición de Service Fabric con el que está asociada la instancia de contador de rendimiento. El identificador de partición es un GUID y su representación de cadena se genera mediante el método [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) con el especificador de formato "D".

*ActorRuntimeInternalID* es la representación de cadena de un entero de 64 bits que el tiempo de ejecución de los actores de Fabric genera para su uso interno. Esto se incluye en el nombre de la instancia de contador de rendimiento para garantizar su unicidad y evitar conflictos con otros nombres de instancia de contador de rendimiento. Los usuarios no deberían intentar interpretar esta parte del nombre de instancia de contador de rendimiento.

A continuación se muestra un ejemplo de un nombre de instancia de un contador que pertenece a la categoría `Service Fabric Actor`:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

En el ejemplo anterior, `2740af29-78aa-44bc-a20b-7e60fb783264` es la representación de cadena del identificador de partición de Service Fabric y `635650083799324046` es el identificador de 64 bits que se genera para uso interno del tiempo de ejecución.

#### Categoría del método del actor de Service Fabric
En la categoría `Service Fabric Actor Method`, los nombres de instancias de contadores tienen el formato siguiente:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* es el nombre del método de actor con el que está asociada la instancia de contador de rendimiento. El formato del nombre del método se determina según una lógica del tiempo de ejecución de los actores de Fabric que compensa la legibilidad del nombre con restricciones sobre la longitud máxima de los nombres de instancia de contador de rendimiento en Windows.

*ActorsRuntimeMethodId* es la representación de cadena de un entero de 32 bits que el tiempo de ejecución de los actores de Fabric genera para su uso interno. Esto se incluye en el nombre de la instancia de contador de rendimiento para garantizar su unicidad y evitar conflictos con otros nombres de instancia de contador de rendimiento. Los usuarios no deberían intentar interpretar esta parte del nombre de instancia de contador de rendimiento.

*ServiceFabricPartitionID* es la representación de cadena del identificador de partición de Service Fabric con el que está asociada la instancia de contador de rendimiento. El identificador de partición es un GUID y su representación de cadena se genera mediante el método [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) con el especificador de formato "D".

*ActorRuntimeInternalID* es la representación de cadena de un entero de 64 bits que el tiempo de ejecución de los actores de Fabric genera para su uso interno. Esto se incluye en el nombre de la instancia de contador de rendimiento para garantizar su unicidad y evitar conflictos con otros nombres de instancia de contador de rendimiento. Los usuarios no deberían intentar interpretar esta parte del nombre de instancia de contador de rendimiento.

A continuación se muestra un ejemplo de un nombre de instancia de un contador que pertenece a la categoría `Service Fabric Actor Method`:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

En el ejemplo anterior, `ivoicemailboxactor.leavemessageasync` es un nombre de método, `2` es el identificador de 32 bits generado para uso interno del tiempo de ejecución, `89383d32-e57e-4a9b-a6ad-57c6792aa521` es la representación de cadena del identificador de partición de Service Fabric y `635650083804480486` es el identificador de 64 bits que se genera para uso interno del tiempo de ejecución.

## Lista de eventos y contadores de rendimiento

### Eventos de método de actor y contadores de rendimiento
El tiempo de ejecución de Reliable Actors emite los siguientes eventos relacionados con los [métodos de actor](service-fabric-reliable-actors-introduction.md#actors).

|Nombre del evento|Id. de evento|Level|Palabra clave|Descripción|
|---|---|---|---|---|
|ActorMethodStart|7|Detallado|0x2|El tiempo de ejecución de los actores está a punto de invocar un método de actor.|
|ActorMethodStop|8|Detallado|0x2|Un método de actor terminó de ejecutarse. Por ejemplo, se ha devuelto la llamada asincrónica del tiempo de ejecución al método de actor y se completó la tarea que el método de actor devolvió.|
|ActorMethodThrewException|9|Warning (Advertencia)|0x3|Se produjo una excepción durante la ejecución de un método de actor, bien durante la llamada asincrónica del tiempo de ejecución al método actor o bien durante la ejecución de la tarea el método de actor devolvió. Este evento indica algún tipo de error en el código de actor que debe investigarse.|

El tiempo de ejecución de Reliable Actors publica los siguientes contadores de rendimiento relacionados con la ejecución de los métodos de actor.

|Nombre de la categoría|Nombre del contador|Descripción|
|---|---|---|
|Método del actor de Service Fabric|Invocaciones/seg.|Número de veces que se invoca el método de servicio del actor por segundo|
|Método del actor de Service Fabric|Promedio de milisegundos por invocación|Tiempo necesario para ejecutar el método de servicio del actor en milisegundos|
|Método del actor de Service Fabric|Excepciones generadas/seg.|Número de veces que el método de servicio del actor genera una excepción por segundo|

### Contadores de rendimiento y eventos de simultaneidad
El tiempo de ejecución de Reliable Actors emite los siguientes eventos relacionados con la [simultaneidad](service-fabric-reliable-actors-introduction.md#concurrency).

|Nombre del evento|Id. de evento|Level|Palabra clave|Descripción|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|Detallado|0x8|Este evento se escribe al comienzo de cada turno nuevo en un actor. Contiene el número de llamadas de actor en espera para adquirir el bloqueo por actor que exige la simultaneidad basada en turnos.|

El tiempo de ejecución de Reliable Actors publica los siguientes contadores de rendimiento relacionados con la simultaneidad.

|Nombre de la categoría|Nombre del contador|Descripción|
|---|---|---|
|Actor de Service Fabric|Número de llamadas de actor en espera de un bloqueo de actor|Número de llamadas de actor en espera para adquirir el bloqueo por actor que exige la simultaneidad basada en turnos|

### Contadores de rendimiento y eventos de administración del estado de los actores
El tiempo de ejecución de Reliable Actors emite los siguientes eventos relacionados con la [administración del estado de los actores](service-fabric-reliable-actors-introduction.md#actor-state-management).

|Nombre del evento|Id. de evento|Level|Palabra clave|Descripción|
|---|---|---|---|---|
|ActorSaveStateStart|10|Detallado|0x4|El tiempo de ejecución de los actores está a punto de guardar el estado del actor.|
|ActorSaveStateStop|11|Detallado|0x4|El tiempo de ejecución de los actores finalizó de guardar el estado del actor.|

El tiempo de ejecución de Reliable Actors publica los siguientes contadores de rendimiento relacionados con la administración del estado de los actores.

|Nombre de la categoría|Nombre del contador|Descripción|
|---|---|---|
|Actor de Service Fabric|Promedio de milisegundos por operación de almacenamiento del estado|Tiempo necesario para guardar el estado de un actor en milisegundos|

### Eventos relacionados con las instancias de actores sin estado
El tiempo de ejecución de Reliable Actors emite los siguientes eventos relacionados con las [instancias de actores sin estado](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateless-actors).

|Nombre del evento|Id. de evento|Level|Palabra clave|Descripción|
|---|---|---|---|---|
|ServiceInstanceOpen|3|Informativo|0x1|Se abre la instancia del actor sin estado. Esto implica que los actores de esta partición pueden crearse dentro de esta instancia (y posiblemente también de otras instancias).|
|ServiceInstanceClose|4|Informativo|0x1|Se cierra la instancia del actor sin estado. Esto implica que los actores de esta partición ya no se crearán dentro de esta instancia. Ninguna solicitud nueva se entregará a los actores ya creados en esta instancia. Los actores se destruirán cuando se completen las solicitudes en curso.|

### Eventos relacionados con las réplicas de actores con estado
El tiempo de ejecución de Reliable Actors emite los siguientes eventos relacionados con las [réplicas de actores con estado](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateful-actors).

|Nombre del evento|Id. de evento|Level|Palabra clave|Descripción|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|Informativo|0x1|La réplica del actor con estado cambió el rol a Principal. Esto implica que los actores de esta partición se crearán dentro de esta réplica.|
|ReplicaChangeRoleFromPrimary|2|Informativo|0x1|La réplica del actor con estado cambió el rol a No principal. Esto implica que los actores de esta partición ya no se crearán dentro de esta réplica. Ninguna solicitud nueva se entregará a los actores ya creados en esta réplica. Los actores se destruirán cuando se completen las solicitudes en curso.|

### Eventos de activación y desactivación de actores
El tiempo de ejecución de Reliable Actors emite los siguientes eventos relacionados con la [activación y desactivación de actores](service-fabric-reliable-actors-lifecycle.md).

|Nombre del evento|Id. de evento|Level|Palabra clave|Descripción|
|---|---|---|---|---|
|ActorActivated|5|Informativo|0x1|Se activó un actor.|
|ActorDeactivated|6|Informativo|0x1|Se desactivó un actor.|

<!---HONumber=AcomDC_0211_2016-->