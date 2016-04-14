<properties
   pageTitle="Descripciones del servicio del equilibrador de recursos"
   description="Información general de la configuración de las descripciones del servicio para su utilización por parte del equilibrador de recursos | Microsoft Azure"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/15/2015"
   ms.author="masnider"/>

# Descripción general del servicio

## Restricciones de selección de ubicación
[Las restricciones de selección de ubicación](service-fabric-placement-constraint.md) de un servicio son el mecanismo mediante el cual una instancia de servicio determinada selecciona las propiedades del nodo que requiere. Al igual que propiedades del nodo, son pares clave/valor que describen el nombre de la propiedad y los requisitos del servicio para el valor. Las instrucciones individuales se pueden agrupar con una lógica booleana simple para crear la restricción necesaria. Tenga en cuenta que el equilibrador de recursos de Service Fabric interpreta las restricciones.

Las restricciones de selección de ubicación del servicio pueden definirse mediante el manifiesto de servicio o aplicación o directamente en el código.

El manifiesto de servicio proporciona las definiciones de `ServiceTypes`.

``` xml
<ServiceTypes>
    <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="false" >
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulServiceType>
</ServiceTypes>

```

El manifiesto de aplicación proporciona las definiciones de `ServiceTemplates` o las de `DefaultServices`.

``` xml
<ServiceTemplates>
    <StatefulService ServiceTypeName="QueueReplica">
      <SingletonPartition></SingletonPartition>
      <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
    </StatefulService>
  </ServiceTemplates>

  <DefaultServices>
    <Service Name="QR">
      <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueReplica" TargetReplicaSetSize="3">
        <SingletonPartition/>
        <PlacementConstraints>(HasDisk == true  &amp;&amp; SpindleCount &gt;= 4)</PlacementConstraints>
      </StatefulService>
    </Service>
  </DefaultServices>
```

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasDisk == true  && SpindleCount >= 4)";
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

El siguiente diagrama muestra que en el clúster con nodos, el único nodo válido para la selección de ubicación de este servicio es N5:

![Restricciones de selección de ubicación][Image1]

Si el servicio especifica una restricción para una propiedad que un nodo específico no tiene definida, ese nodo no se considerará una coincidencia independientemente del valor especificado.

## Afinidad de servicio
Una forma especial de restricción de selección de ubicación, la afinidad de servicio permite la descripción de servicios diferentes que por motivos diversos tienen una dependencia local entre sí. Esto significa que ambos servicios se deben ejecutar en el mismo nodo para que funcionen. Aunque no se espera que este tipo de arquitectura sea habitual entre aplicaciones de Service Fabric, este reconoce que es una arquitectura de transición posible para ciertos tipos de aplicaciones heredadas, y por lo tanto, hace que esta funcionalidad esté disponible.

El establecimiento de una afinidad de servicio entre dos servicios garantiza que las réplicas principales de esos servicios estén siempre colocadas en el mismo nodo.

Las relaciones de afinidad se representan mediante una jerarquía de elementos primarios y secundarios. El servicio que se establece en primer lugar es el primario y el servicio que se establece en segundo lugar es el elemento secundario de la relación. La relación se modela como una restricción "dura".

Actualmente la afinidad de servicio tiene las siguientes limitaciones:

- No se puede utilizar entre servicios sin estado y con estado.
- No se puede utilizar en servicios sin estado con números de instancias diferentes. Por ejemplo, ambos servicios sin estado deben tener la misma propiedad InstanceCount cuando se crean.
- No se puede utilizar en los servicios volátiles o persistentes con estado con distintos números de réplicas. Por ejemplo, ambos servicios deben tener los mismos valores de Target y Min ReplicaSetSizes especificados.
- No se puede utilizar con los servicios con particiones. Cada servicio debe tener un esquema de partición FABRIC\_PARTITION\_SCHEME\_SINGLETON.
- Las relaciones de afinidad, al igual que otras propiedades de la descripción del servicio, se establecen cuando se crea el servicio y no se pueden modificar.
- Las cadenas de servicios no están permitidas. Si se deben traer varios servicios a una relación de afinidad, deben utilizar un modelo de "estrella".

``` xml
<ServiceTemplates>
  <StatelessService ServiceTypeName="StatelessCalculatorService" InstanceCount="5">
    <SingletonPartition></SingletonPartition>
      <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
  </StatelessService>
</ServiceTemplates>
```

Este ejemplo de código muestra la utilización alternativa de las definiciones de DefaultServices:

``` xml
<DefaultServices>
  <Service Name="childService">
    <StatelessService InstanceCount="3" ServiceTypeName="calculatorService">
      <SingletonPartition/>
     <ServiceCorrelations>
        <ServiceCorrelation Scheme="Affinity" ServiceName="fabric:/otherApplication/parentService"/>
      </ServiceCorrelations>
    </StatelessService>
  </Service>
</DefaultServices>
```

Este ejemplo de código muestra cómo crear una relación de afinidad después de haber creado la aplicación contenedora:

``` cpp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## Métricas de equilibrio de recursos
Mientras que las restricciones de selección de ubicación de servicio y las propiedades de nodo representan las reglas y directivas duras que describen qué acuerdos de servicios son válidos, las métricas de equilibrio de recursos generalmente ayudan a describir la organización óptima de los servicios en el clúster. Las métricas definidas por el servicio tienen varias propiedades que describen cómo se utilizan dentro del clúster.

### Utilización de métricas predeterminadas
Lo primero que se debe comprender es que tratar con métricas de recursos es totalmente opcional para los servicios y que de forma predeterminada el equilibrador de recursos de Service Fabric utiliza algunas métricas del sistema "integradas" para realizar tareas de equilibrio de recursos básicas sin requerir que lleven a cabo ninguna acción los administradores de ese servicio o clúster. Las métricas predeterminadas de las que el equilibrador de recursos de Service Fabric realiza un seguimiento y equilibra en el clúster son PrimaryCount, ReplicaCount y Count. PrimaryCount es el número de réplicas del servicio con estado principal de un nodo. PrimaryCount es el número de réplicas con estado de un nodo. Count es el número total de objetos de servicio del nodo, incluidos los servicios sin estado y con estado. Por lo general, la métrica PrimaryCount se considera la más importante y, por ello, tiene el peso más alto tal y como se describe en la sección siguiente. La métrica PrimaryCount va seguida de la métrica ReplicaCount y, finalmente, de la métrica Count.

Este diagrama muestra un ejemplo del equilibrio predeterminado de tres servicios con estado con tres réplicas cada uno:

![Métricas predeterminadas][Image2]

En general, las métricas predeterminadas son suficientes para muchos servicios y deben utilizarse a menos que exista un requisito específico para las funcionalidades o métricas adicionales.

### Definición de métricas personalizadas
Si las métricas predeterminadas no son suficientes para un servicio determinado o el servicio dispone de requisitos conocidos para los requisitos de un recurso determinado, como la memoria o el espacio en disco, se deben utilizar métricas personalizadas. Las métricas personalizadas son útiles cuando un servicio tiene uno o más recursos que deben estar equilibrados correctamente para evitar la contención. La utilización de dichas métricas puede variar considerablemente entre réplicas dentro del clúster. Por ejemplo, una principal podría utilizar el 100 % de la métrica, mientras que otra podría estar utilizando solo el 20 %. También puede resultar útil utilizar métricas personalizadas para capturar cualquier recurso que esté muy limitado en un equipo, como la memoria, el espacio en disco o las conexiones. En ocasiones pueden crearse métricas personalizadas adicionales que capturan o representan las métricas que no tienen ningún límite superior en los nodos, pero representan el consumo de recursos y trabajo desde la perspectiva del servicio, por ejemplo, una métrica de "transacciones pendientes actuales". Aunque no haya un límite para esta "capacidad" de la métrica en un nodo determinado, todavía existe una ventaja de rendimiento al garantizar que esta métrica se distribuya uniformemente en todo el clúster.

Tenga en cuenta que si un servicio define cualquier métrica personalizada, este no utilizará las métricas predeterminadas proporcionadas por el sistema. Las métricas predeterminadas pueden seguir usándose, pero deben volver a incluirse explícitamente en la lista de métricas del servicio durante la creación.

### Nombres de métrica
Las métricas personalizadas pueden crearse simplemente mediante la definición de un nombre de métrica al crear un servicio. Tenga en cuenta que el equilibrador de recursos de Service Fabric asocia las métricas mediante el nombre. Si la métrica se utiliza como una capacidad dentro de las definiciones de nodo o a través de los servicios, los nombres de métrica deben coincidir exactamente de modo que el equilibrador de recursos de Service Fabric pueda relacionarlos.

### Pesos de métrica
Cuando un servicio define varias métricas, también es útil definir los pesos de métrica de dichas métricas. Distintos pesos de métrica indican al equilibrador de recursos de Service Fabric qué métrica es más importante para el funcionamiento del servicio. Por ejemplo, una cola en memoria podría verse afectada por el ancho de banda de red, pero probablemente se vea más limitada por la utilización real de la memoria en el nodo. Por lo tanto, mientras que es posible que la cola tenga varias métricas, la métrica que representa el uso de memoria tiene el peso más alto. De forma similar, una base de datos persistente probablemente depende del uso de la memoria y del disco. Aunque la memoria limitada reduce la capacidad de procesar consultas complejas, quedarse sin espacio en el disco impediría efectuar operaciones de almacenamiento adicionales, lo cual puede que resulte probablemente una situación más crítica. Por lo tanto, es posible que el almacén persistente elija el espacio en disco como la métrica con mayor peso de las dos.

Tenga en cuenta que el peso solo se utiliza en el equilibrador de recursos de Service Fabric cuando no puede equilibrar totalmente un conjunto de métricas y por lo tanto debe buscar soluciones en las que una métrica termine con menos equilibrio general que otra. En este caso, el equilibrio de la métrica con el menor peso se determina como de prioridad más baja que la métrica de mayor peso, por lo que el equilibrador de recursos de Service Fabric muestra propuestas que favorecen al equilibrador de la métrica con mayor peso.

Asimismo, cuando el equilibrador de recursos de Service Fabric reconoce que varios servicios han definido la misma métrica por nombre, considera esas métricas como la misma. En los casos en los que la métrica es la misma, pero los pesos son diferentes, el equilibrador de recursos calcula el promedio de los pesos especificados para determinar los pesos reales que se deben utilizar durante el tiempo de ejecución. Por lo tanto, si un servicio define una ponderación de cero, lo que indica que no requiere equilibrio, pero otro servicio define la misma métrica como si tuviera una ponderación alta, la ponderación real acabará en un lugar situado entre ambas.

![Pesos de métrica][Image3]

Este ejemplo muestra cómo dos escenarios de pesos de métricas diferentes provocan dos resultados de equilibrio diferentes en los que la métrica con prioridad más alta está mejor distribuida. En el ejemplo anterior de la izquierda, B está mejor equilibrada, mientras que en el de la derecha, A está mejor equilibrada. Dado que en este ejemplo no hay ninguna solución de equilibrio que tenga como resultado un equilibrio de ambas métricas, el equilibrador de recursos de Service Fabric utiliza el peso para determinar cuál es más importante y, por tanto, qué métrica tiene preferencia durante el equilibrio.

Tenga en cuenta que el peso de la métrica de cero se proporciona para los casos en los que un servicio realiza un seguimiento de una métrica, pero no requiere el equilibrio que se basa en esta métrica. Por ejemplo, considere una métrica que tenga un límite explícito en varios nodos, pero para la que la "utilización equitativa entre los nodos", que es lo que garantiza normalmente el equilibrio de recursos, no es relevante. Para estos tipos de métricas que no requieren equilibrio pero requieren la aplicación de límites establecidos en nodos, puede definirse el peso de equilibrio de cero. Durante el tiempo de ejecución, el equilibrador de recursos de Service Fabric aplica la capacidad, pero no intenta equilibrar la métrica de forma proactiva incluso si el empleo de esa métrica está muy poco equilibrado entre los nodos.

### Carga de métrica predeterminada para un rol principal o secundario
Al definir una métrica para un servicio, debe proporcionar el consumo previsto para esa métrica cuando el servicio esté en un rol principal o en uno secundario. Esta información no solo ayuda al equilibrador de recursos a colocar inicialmente el servicio de manera eficaz, sino que puede actuar como una buena aproximación del uso real del servicio de las métricas con las que está relacionado durante su duración. El equilibrador de recursos de Service Fabric tiene en cuenta el consumo de esta métrica automáticamente, no solo al colocar el servicio, sino cada vez que debe mover las réplicas debido al equilibrio o a otros cambios en el clúster. Si la carga de un servicio es bastante estable y predecible, esto significa que al establecer estos valores, el servicio puede anular la necesidad de tener que informar de la carga durante el tiempo de ejecución.

En este ejemplo de código se muestra un servicio que define completamente dos métricas personalizadas, una para el uso de memoria y la otra para el espacio en disco:

``` cpp
ServiceLoadMetricDescription memoryMetric = new ServiceLoadMetricDescription();
memoryMetric.Name = "MemoryInMb";
memoryMetric.Weight = ServiceLoadMetricWeight.High;
memoryMetric.PrimaryDefaultLoad = 100;
memoryMetric.SecondaryDefaultLoad = 50;

ServiceLoadMetricDescription diskMetric = new ServiceLoadMetricDescription();
diskMetric.Name = "DiskInMb";
diskMetric.Weight = ServiceLoadMetricWeight.Medium;
diskMetric.PrimaryDefaultLoad = 1024;
diskMetric.SecondaryDefaultLoad = 750;

serviceDescription.Metrics.Add(memoryMetric);
serviceDescription.Metrics.Add(diskMetric);

Task t = fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Este ejemplo de código muestra las definiciones de ServiceTypes mediante el manifiesto de servicio:

``` xml
<ServiceTypes>
  <StatefulServiceType ServiceTypeName="QueueReplica" HasPersistedState="true">
    <LoadMetrics>
      <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
      <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
    </LoadMetrics>
  </StatefulServiceType>
</ServiceTypes>
```

Este ejemplo de código muestra las definiciones de ServiceTemplates mediante el manifiesto de la aplicación:

``` xml
<ServiceTemplates>
   <StatefulService ServiceTypeName="QueueReplica">
     <SingletonPartition></SingletonPartition>
     <LoadMetrics>
       <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
       <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
     </LoadMetrics>
   </StatefulService>
 </ServiceTemplates>
```
Este ejemplo de código muestra las definiciones de DefaultServices mediante el manifiesto de la aplicación:

``` xml
<DefaultServices>
  <Service Name="QueueServiceInstance">
    <StatefulService MinReplicaSetSize="3" ServiceTypeName="QueueService" TargetReplicaSetSize="3">
      <SingletonPartition/>
      <LoadMetrics>
        <LoadMetric Name="MemoryInMb" Weight="High" PrimaryDefaultLoad="100" SecondaryDefaultLoad="50"/>
        <LoadMetric Name="DiskInMb" Weight="Medium" PrimaryDefaultLoad="1024" SecondaryDefaultLoad="750"/>
      </LoadMetrics>
    </StatefulService>
  </Service>
</DefaultServices>
```

Debido a que no se actualizan los valores de carga predeterminados a menos que los servicios opten específicamente por generar informes de carga en tiempo de ejecución mediante código, los valores también pueden utilizarse para definir más de un modelo de equilibrio de recursos de "reserva de capacidad". Por ejemplo, si las cargas de trabajo generalmente se dividen en cubos de varios tamaños y puede colocarse un número de unidades de trabajo conocido en un nodo en cualquier momento, se puede crear una métrica personalizada de "unidades" y, de esta forma, definir la capacidad de los nodos y la carga predeterminada del servicio en términos de unidades.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes

Para más información, consulte [Arquitectura de equilibrador de recursos](service-fabric-resource-balancer-architecture.md), [Restricciones de selección de ubicación](service-fabric-placement-constraint.md).

[Image1]: media/service-fabric-resource-balancer-service-description/PC.png
[Image2]: media/service-fabric-resource-balancer-service-description/DM.png
[Image3]: media/service-fabric-resource-balancer-service-description/MW.png

<!---HONumber=AcomDC_0114_2016-->