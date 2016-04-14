<properties
   pageTitle="Arquitectura de Reliable Services | Microsoft Azure"
   description="Información general de la arquitectura de Reliable Services para servicios con estado y sin estado"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="alanwar"/>

# Arquitectura para Reliable Services con estado y sin estado

Los Reliable Services de Azure Service Fabric pueden ser con estado o sin estado. Cada tipo de servicio se ejecuta dentro de una arquitectura específica. Estas arquitecturas se describen en este artículo. Consulte [Información general de Reliable Services](service-fabric-reliable-services-introduction.md) para obtener más información acerca de las diferencias entre los servicios con estado y sin estado.

## Reliable Services con estado

### Arquitectura de un servicio con estado
![Diagrama de arquitectura de un servicio con estado](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### Reliable Services con estado

Un servicio fiable con estado puede derivar de la clase StatefulService o StatefulServiceBase. Ambas clases base las proporciona Service Fabric y ofrecen diversos niveles de asistencia y abstracción para el servicio con estado para interactuar con Service Fabric y participar como un servicio en el clúster de Service Fabric.

StatefulService se deriva de StatefulServiceBase. StatefulServiceBase ofrece a los servicios una mayor flexibilidad, pero requiere más conocimiento de las interioridades de Service Fabric. Consulte [Información general de Reliable Services](service-fabric-reliable-services-introduction.md) y [Uso avanzado de Reliable Services](service-fabric-reliable-services-advanced-usage.md) para obtener más información sobre las características específicas de escribir servicios mediante las clases StatefulService y StatefulServiceBase.

Ambas clases base administran la duración y la función de la implementación del servicio. La implementación del servicio puede invalidar los métodos virtuales de cualquier clase base si la implementación del servicio tiene trabajo que hacer en esos puntos del ciclo de vida de implementación del servicio o si desea crear un objeto de agente de escucha de comunicación. Tenga en cuenta que aunque una implementación de servicio puede implementar su propio objeto de agente de escucha de comunicación exponiendo ICommunicationListener, en el diagrama anterior, el agente de escucha de comunicación es implementado por Service Fabric mientras la implementación del servicio utiliza un agente de escucha de comunicación implementado por Service Fabric.

El servicio confiable con estado usa el administrador de estado confiable para aprovechar las colecciones confiables. Las colecciones confiables son estructuras de datos locales que tienen una alta disponibilidad para su servicio, es decir, están siempre disponibles sin tener en cuenta las conmutaciones por error del servicio. Cada tipo de colección fiable es implementada mediante un proveedor de estado fiable. Para obtener más información sobre colecciones confiables, consulte [la introducción a las colecciones confiables](service-fabric-reliable-services-reliable-collections.md)

### Proveedores de estado y administrador de estado fiable

El administrador de estado confiable es el objeto que administra los proveedores de estado confiables. Tiene la funcionalidad para crear, eliminar, enumerar y asegurarse de que los proveedores de estado confiables sean persistentes y de alta disponibilidad. Una instancia de proveedor de estado confiable representa una instancia de una estructura de datos persistente y de alta disponibilidad, como un diccionario o una cola.

Cada proveedor de estado confiable expone una interfaz que es utilizada por el servicio con estado para interactuar con el proveedor de estado confiable. Por ejemplo IReliableDictionary se utiliza para interactuar con el diccionario confiable mientras que IReliableQueue se utiliza para interactuar con la cola confiable. Todos los proveedores de estado fiables implementan la interfaz IReliableState.

El administrador de estado confiable tiene una interfaz denominada IReliableStateManager, que permite acceder a ella mediante la implementación del servicio con estado. Mediante IReliableStateManager se devuelven interfaces a proveedores de estado fiables.

El administrador de estado fiable está diseñado con una arquitectura de complemento dinámica para que se puedan conectar dinámicamente nuevos tipos de colecciones fiables.

El diccionario y la cola confiables se basan en la implementación de un almacén diferencial con versiones de alto rendimiento.

### Replicador de transacciones

El componente replicador de transacciones es responsable de garantizar que el estado de su servicio (que es el estado del administrador de estado confiable y las colecciones confiables) es coherente en todas las réplicas que se ejecutan en su servicio. También garantiza que ese estado también persiste en el registro. El administrador de estado fiable interactúa con el replicador de transacciones a través de un mecanismo privado.

El replicador de transacciones usa un protocolo de red para comunicar el estado con otras réplicas de la instancia de servicio para que todas las réplicas dispongan de información de estado actualizada.

El replicador de transacciones usa un registro para conservar información de estado para que la información de estado sobreviva a bloqueos del proceso o el nodo. La interacción con el registro se efectúa a través de un mecanismo privado.

### Registro

El componente de registro proporciona un almacén persistente de alto rendimiento que se puede optimizar para escribir en discos de estado giratorio o sólidos. También se puede optimizar para efectuar un uso más eficaz del espacio en disco. El diseño del registro es para que el almacenamiento persistente (es decir, discos duros) sea local en los nodos que ejecutan el servicio con estado. Esto permite bajas latencias y alto rendimiento, en comparación con el almacenamiento persistente, que no es local en el nodo.

El componente de registro utiliza dos tipos de archivos de registro. Hay un archivo de registro compartido de todo el nodo que debe estar en un disco que se utiliza solo para ese archivo de registro. Este archivo se coloca en el directorio de trabajo del nodo de Service Fabric. Cada réplica del servicio también tiene un archivo de registro específico y se coloca en el directorio de trabajo del servicio.

El registro compartido es un área de transición para la información de estado mientras el archivo de registro específico es el destino final, donde se almacena. En este diseño la información de estado se escribe en primer lugar en el archivo de registro compartido y, a continuación, se descargan de forma diferida en el archivo de registro específico en segundo plano. De esta manera la escritura en el registro compartido tendría la latencia más baja y el rendimiento máximo para permitir que el servicio progrese con mayor rapidez.

Sin embargo, cuando se configura el componente de registro para optimizar discos de estado sólido que utilizan la configuración de OptimizeForLocalSSD, la información de estado se escribe directamente en el archivo de registro específico y omite el archivo de registro compartido. Debido a que los discos de estado sólido no sufren retrasos debido a la contención de movimiento de los cabezales, no hay ninguna penalización por escribir directamente en el archivo de registro específico.

Cuando el componente de registro está optimizado para minimizar el uso de espacio en disco mediante la configuración OptimizeLogForLowerDiskUsage, los archivos de registro específicos se crean como archivos dispersos de NTFS. Puesto que los archivos de registro normalmente no siempre están completamente llenos de información de estado, el uso de los archivos dispersos permite el sobreaprovisionamiento del espacio en disco disponible para más réplicas. Si no se configura de esta forma, el espacio del archivo de registro se asigna previamente y el componente de registro puede escribir directamente en el archivo con el máximo rendimiento.

Además de una interfaz de modo de usuario mínima en el registro, el registro se escribe como un controlador en modo kernel. Mediante la ejecución como un controlador de modo kernel, el registro puede proporcionar el máximo rendimiento a todos los servicios que lo utilizan.

Para obtener más información acerca de cómo configurar el registro, consulte [Configuración de Reliable Services con estado](service-fabric-reliable-services-configuration.md).

## Reliable Services sin estado

### Arquitectura de un servicio sin estado
![Diagrama de arquitectura de un servicio sin estado](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### Reliable Services sin estado

Las implementaciones de servicios sin estado se derivan de la clase StatelessService o StatelessServiceBase. La clase StatelessServiceBase permite más flexibilidad que la clase StatelessService. Ambas clases base administran la duración y el rol del servicio.

La implementación del servicio puede invalidar los métodos virtuales de cualquier clase base si el servicio tiene trabajo que hacer en esos puntos del ciclo de vida de servicio o si desea crear un objeto de agente de escucha de comunicación. Tenga en cuenta que aunque es posible que el servicio puede implementar su propio objeto de agente de escucha de comunicación exponiendo ICommunicationListener, en el diagrama anterior, el agente de escucha de comunicación es implementado por Service Fabric mientras la implementación del servicio utiliza un agente de escucha de comunicación implementado por Service Fabric.

Consulte [Información general de Reliable Services](service-fabric-reliable-services-introduction.md) y [Uso avanzado de Reliable Services](service-fabric-reliable-services-advanced-usage.md) para obtener más información sobre las características específicas de escribir servicios mediante las clases StatelessService y StatelessServiceBase.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Para obtener más información acerca de Service Fabric, consulte:

[Información general del servicio confiable](service-fabric-reliable-services-introduction.md)

[Inicio rápido](service-fabric-reliable-services-quick-start.md)

[Información general sobre colecciones confiables](service-fabric-reliable-services-reliable-collections.md)

[Uso avanzado de un servicio confiable](service-fabric-reliable-services-advanced-usage.md)

[Configuración de un servicio confiable](service-fabric-reliable-services-configuration.md)

<!---HONumber=AcomDC_1223_2015-->