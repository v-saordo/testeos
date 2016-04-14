<properties
   pageTitle="Uso avanzado del modelo de programación de Reliable Services | Microsoft Azure"
   description="Obtenga información sobre el uso avanzado del modelo de programación del servicio fiable de Service Fabric para obtener una mayor flexibilidad en los servicios."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/28/2016"
   ms.author="jesseb"/>

# Uso avanzado del modelo de programación de servicios fiables
Azure Service Fabric simplifica la escritura y administración de los servicios con y sin estado confiables. En esta guía se hablará de los usos avanzados del modelo de programación de los servicios fiables para obtener más control y flexibilidad sobre sus servicios. Antes de leer esta guía, familiarícese con [el modelo de programación de servicios fiables](service-fabric-reliable-services-introduction.md).

## Clases base para los servicios sin estado
La clase base StatelessService proporciona RunAsync() y CreateServiceInstanceListeners(), que es suficiente para la mayoría de los servicios sin estado. La clase StatelessServiceBase subyace a StatelessService y expone los eventos de ciclo de vida de servicio adicionales. Puede derivar de StatelessServiceBase si necesita más control o flexibilidad. Consulte la documentación de referencia para desarrolladores en [StatelessService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservice.aspx) y [StatelessServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservicebase.aspx) para obtener más información.

- `void OnInitialize(StatelessServiceInitializiationParameters)` OnInitialize es el primer método al que llama Service Fabric. Se proporciona la información de inicialización del servicio, como el nombre del servicio, el identificador de partición, el identificador de instancia y la información del paquete de código. Aquí no se debe realizar ningún procesamiento complejo. La inicialización prolongada debe realizarse en OnOpenAsync.

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` OnOpenAsync es llamado cuando está a punto de usarse la instancia de servicio sin estado. En este momento, se pueden iniciar las tareas de inicialización del servicio ampliado.

- A `Task OnCloseAsync(CancellationToken)` OnCloseAsync se llama cuando la instancia de servicio sin estado se va a apagar correctamente. Esto puede ocurrir cuando se está actualizando el código de servicio, cuando se está moviendo la instancia de servicio debido al equilibrio de carga o se detecta un error transitorio. OnCloseAsync puede utilizarse para cerrar de manera segura todos los recursos, detener cualquier procesamiento en segundo plano, terminar de guardar el estado externo o cerrar conexiones existentes.

- A `void OnAbort()` OnAbort se llama cuando la instancia de servicio sin estado se apaga a la fuerza. Normalmente se llama cuando se detecta un error permanente en el nodo o cuando Service Fabric no puede administrar el ciclo de vida de la instancia de servicio debido a errores internos.

## Clases base para servicios con estado
La clase base StatefulService debería ser suficiente para la mayoría de servicios con estado. De modo similar a los servicios sin estado, la clase StatefulServiceBase subyace a StatefulService y expone los eventos de ciclo de vida de servicio adicionales. Además, puede usarlo para proporcionar un proveedor de estado confiable personalizado y, opcionalmente, para ofrecer compatibilidad con agentes de escucha de comunicación en elementos secundarios. Puede derivar de StatefulServiceBase si necesita más control o flexibilidad. Consulte la documentación de referencia para desarrolladores en [StatefulService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservice.aspx) y [StatefulServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservicebase.aspx) para obtener más información.

- A `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync se llama cuando el servicio con estado cambia de roles, por ejemplo, a elemento principal o elemento secundario. Las réplicas principales reciben el estado de escritura (pueden crear y escribir en las colecciones confiables). Las réplicas secundarias reciben el estado de lectura (solo pueden leer desde las colecciones confiables existentes). Puede iniciar o actualizar las tareas en segundo plano en respuesta a los cambios de rol, tal como realizar validaciones de solo lectura, generación de informes o minería de datos en una base de datos secundaria.

- `IStateProviderReplica CreateStateProviderReplica()` Se espera que un servicio con estado tenga un proveedor de estado fiable. StatefulService utiliza la clase ReliableStateManager, que proporciona colecciones fiables (por ejemplo, diccionarios y colas). Puede invalidar este método para configurar la clase ReliableStateManager pasando un ReliableStateManagerConfiguration a su constructor. De este modo, puede proporcionar serializadores de estado personalizados, especificar qué ocurre cuando es posible que los datos se hayan perdido y configurar los proveedores de replicador o estado.

StatefulServiceBase también proporciona los mismos cuatro eventos de ciclo de vida que StatelessServiceBase, con la misma semántica y casos de uso:

- `void OnInitialize(StatefulServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## Pasos siguientes
Para consultar temas más avanzados relacionados con Service Fabric, consulte los siguientes artículos:

- [Configuración de Reliable Services con estado](service-fabric-reliable-services-configuration.md)

- [Introducción al estado de Service Fabric](service-fabric-health-introduction.md)

- [Uso de informes de mantenimiento del sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Información general de las restricciones de ubicación](service-fabric-placement-constraint.md)

<!---HONumber=AcomDC_0204_2016-->