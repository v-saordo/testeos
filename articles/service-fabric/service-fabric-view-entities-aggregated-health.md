<properties
   pageTitle="Visualización del mantenimiento agregado de entidades de Azure Service Fabric | Microsoft Azure"
   description="Se describe cómo consultar, ver y evaluar el mantenimiento agregado de entidades de Azure Service Fabric, a través de consultas de mantenimiento y consultas generales."
   services="service-fabric"HealthManager
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/26/2016"
   ms.author="oanapl"/>

# Vista de los informes de estado de Service Fabric
Azure Service Fabric presenta un [modelo de mantenimiento](service-fabric-health-introduction.md) formado por entidades de mantenimiento en las que componentes y guardianes del sistema pueden notificar las condiciones locales que están supervisando. El [almacén de estado](service-fabric-health-introduction.md#health-store) agrega todos los datos de mantenimiento para determinar si las entidades son correctas.

De fábrica, el clúster está rellenado con informes de estado enviados por los componentes del sistema. Lea más en [Uso de informes de mantenimiento del sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric proporciona varias maneras de obtener el mantenimiento agregado de las entidades:

- [Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md) y otras herramientas de visualización

- Consultas de mantenimiento (a través de PowerShell, la API o REST)

- Consultas generales que devuelven una lista de entidades con el mantenimiento como una de las propiedades (a través de Powerhsell, la API o REST)

Para demostrar estas opciones, vamos a usar un clúster local con cinco nodos. Junto a la aplicación **fabric:/System** (que está integrada), se implementan algunas otras aplicaciones. Una de ellas es **fabric:/WordCount**. Esta aplicación contiene un servicio con estado configurado con siete réplicas. Dado que solo hay cinco nodos, los componentes del sistema mostrarán una advertencia de que la partición está por debajo del recuento de destino.

```xml
<Service Name="WordCount.Service">
  <StatefulService ServiceTypeName="WordCount.Service" MinReplicaSetSize="2" TargetReplicaSetSize="7">
    <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## Mantenimiento del Explorador de Service Fabric
El Explorador de Service Fabric proporciona una vista visual del clúster. En la imagen siguiente, puede ver que:

- La aplicación **fabric:/WordCount** está en rojo (en error) porque tiene un evento de error notificado por **MyWatchdog** para la propiedad **Availability**.

- Uno de sus servicios, **fabric:/WordCount/WordCount.Service** está en amarillo (en advertencia). Como se describió anteriormente, el servicio está configurado con siete réplicas, y no se pueden colocar todas (ya que solo hay cinco nodos). Aunque no se muestra aquí, la partición de servicio está en amarillo debido al informe del sistema. La partición en amarillo desencadena el servicio en amarillo.

- El clúster está en rojo debido a la aplicación en rojo.

La evaluación usa las directivas predeterminadas del manifiesto de clúster y el manifiesto de aplicación. Son directivas estrictas y no toleran ningún error.

Vista del clúster con el Explorador de Service Fabric.

![Vista del clúster con el Explorador de Service Fabric.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] Obtenga más información sobre el [Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md).

## Consultas de mantenimiento
Service Fabric expone las consultas de mantenimiento para cada uno de los [tipos de entidad](service-fabric-health-introduction.md#health-entities-and-hierarchy) admitidos. Se puede acceder e ellas mediante la API (los métodos se pueden encontrar en **FabricClient.HealthManager**), los cmdlets de PowerShell y REST. Estas consultas devuelven información completa de mantenimiento sobre la entidad, incluido el estado de mantenimiento agregado, los eventos de mantenimiento notificados en la entidad, los estados de mantenimiento de los elementos secundarios (si procede) y las evaluaciones de mantenimiento incorrecto cuando el mantenimiento de la entidad no es correcto.

> [AZURE.NOTE] Una entidad de mantenimiento se devuelve al usuario cuando se rellena completamente en el almacén de estado. La entidad debe estar activa (no eliminada) y tener un informe de sistema. Sus entidades primarias en la cadena de jerarquía deben tener también informes del sistema. Si no se cumple alguna de estas condiciones, las consultas de mantenimiento devuelven una excepción que muestra por qué no se devuelve la entidad.

Las consultas de mantenimiento requieren pasar el identificador de entidad, que depende del tipo de entidad. Las consultas aceptan parámetros de directivas de mantenimiento opcionales. Si no se especifican, se usan para la evaluación las [directivas de mantenimiento](service-fabric-health-introduction.md#health-policies) del manifiesto de aplicación o de clúster. También aceptan filtros para devolver solo los elementos secundarios o eventos parciales, los que respetan los filtros especificados.

> [AZURE.NOTE] Los filtros de salida se aplican en el servidor, por lo que se reduce el tamaño de la respuesta del mensaje. Recomendamos usar los filtros de salida para limitar los datos devueltos en lugar de aplicar filtros en el cliente.

El mantenimiento de una entidad contiene la siguiente información:

- El estado de mantenimiento agregado de la entidad. Esto lo calcula el almacén de estado en función de los informes de mantenimiento de entidades, los estados de mantenimiento de los elementos secundarios (si procede) y las directivas de mantenimiento. Lea más sobre la [evaluación del mantenimiento de entidades](service-fabric-health-introduction.md#entity-health-evaluation).  

- Eventos de mantenimiento de la entidad.

- La colección de los estados de mantenimiento de todos los elementos secundarios de las entidades que pueden tener elementos secundarios. Los estados de mantenimiento contienen identificadores de entidad y el estado de mantenimiento agregado. Para obtener el mantenimiento completo de un elemento secundario, llame al mantenimiento de consultas del tipo de entidad secundaria y pase el identificador de elementos secundarios.

- Si la entidad no es correcta, las evaluaciones de mantenimiento incorrecto apuntan al informe que desencadenó el estado de la entidad.

## Obtención del mantenimiento de clúster
Este proceso devuelve el mantenimiento de la entidad del clúster y contiene los estados de mantenimiento de aplicaciones y nodos (elementos secundarios del clúster). Entrada:

- [Opcional] La asignación de directivas de mantenimiento de aplicaciones, con las directivas de mantenimiento usadas para invalidar las directivas de manifiesto de aplicación.

- [Opcional] Filtros para eventos, nodos y aplicaciones que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, únicamente los errores o advertencias y errores). Tenga en cuenta que todos los eventos, nodos y aplicaciones se utilizan para evaluar el mantenimiento agregado de la entidad, independientemente del filtro.

### API
Para obtener el mantenimiento del clúster, cree una instancia de **FabricClient** y llame al método [**GetClusterHealthAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) en su instancia de **HealthManager**.

Lo siguiente obtiene el mantenimiento del clúster:

```csharp
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync().Result;
```

A continuación se obtiene el mantenimiento del clúster mediante una directiva de mantenimiento personalizada del clúster y filtros para nodos y aplicaciones. Tenga en cuenta que esta operación crea **System.Fabric.Description.[ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx)**, que contiene todos los datos de entrada.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilter = (long)(HealthStateFilter.Error | HealthStateFilter.Warning)
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilter = (long)HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};
ClusterHealth clusterHealth = fabricClient.HealthManager.GetClusterHealthAsync(queryDescription).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento del clúster es **[Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx)**. Conéctese primero al clúster con el cmdlet **Connect-ServiceFabricCluster**.

El estado del clúster es igual a cinco nodos, la aplicación del sistema y fabric:/WordCount se configuran como antes.

El siguiente cmdlet obtiene el mantenimiento del clúster con directivas de mantenimiento predeterminadas. El estado de mantenimiento agregado está en advertencia, porque la aplicación fabric:/WordCount está en advertencia. Observe cómo las evaluaciones de mantenimiento incorrecto muestran detalles de las condiciones que desencadenaron el mantenimiento agregado.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Warning'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Warning'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

NodeHealthStates        :
                          NodeName              : Node.4
                          AggregatedHealthState : Ok

                          NodeName              : Node.2
                          AggregatedHealthState : Ok

                          NodeName              : Node.1
                          AggregatedHealthState : Ok

                          NodeName              : Node.5
                          AggregatedHealthState : Ok

                          NodeName              : Node.3
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/CalculatorActor
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

El siguiente cmdlet de PowerShell obtiene el mantenimiento del clúster mediante una directiva de aplicación personalizada. Filtra los resultados para obtener solo los nodos y las aplicaciones con error o advertencia. Como resultado, no se devolverá ningún nodo ya que todos son correctos. Solo la aplicación fabric:/WordCount respeta el filtro de aplicaciones. Puesto que la directiva personalizada especifica que hay que considerar que las advertencias son errores en la aplicación fabric:/WordCount, la aplicación se evalúa como en error y lo mismo el clúster.

```powershell
PS c:> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
$warningAndErrorFilter = [System.Fabric.Health.HealthStateFilter]::Warning.value__  + [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsHealthStateFilter $warningAndErrorFilter -NodesHealthStateFilter $warningAndErrorFilter

AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 50% (1/2), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                          Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                          Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                          Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                          Unhealthy partition: PartitionId='889909a3-04d6-4a01-97c1-3e9851d77d6c', AggregatedHealthState='Error'.

                          Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

## Obtención del mantenimiento de nodo
Este proceso devuelve el mantenimiento de una entidad del nodo y contiene los eventos de mantenimiento notificados en el nodo. Entrada:

- [Obligatorio] El nombre de nodo que identifica al nodo.

- [Opcional] La configuración de la directiva de mantenimiento del clúster usada para evaluar el mantenimiento.

- [Opcional] Filtros para eventos que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, únicamente errores o advertencias y errores). Tenga en cuenta que se utilizan todos los eventos para evaluar el mantenimiento agregado de la entidad, independientemente del filtro.

### API
Para obtener el mantenimiento del clúster mediante la API, cree una instancia de FabricClient y llame al método **GetNodeHealthAsync** en su instancia de HealthManager.

A continuación se obtiene el mantenimiento del nodo para el nombre de nodo especificado:

```csharp
NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(nodeName).Result;
```

A continuación se obtiene el mantenimiento del nodo para el nombre de nodo especificado y se pasa un filtro de eventos y la directiva personalizada a través de **System.Fabric.Description.[NodeHealthQueryDescription.](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx)**:

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.Warning },
};

NodeHealth nodeHealth = fabricClient.HealthManager.GetNodeHealthAsync(queryDescription).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento del nodo es **Get-ServiceFabricNodeHealth**. Conéctese primero al clúster con el cmdlet **Connect-ServiceFabricCluster**. El siguiente cmdlet obtiene el mantenimiento del nodo mediante directivas de mantenimiento predeterminadas:

```powershell
PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1

NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM
```

El siguiente cmdlet obtiene el mantenimiento de todos los nodos del clúster:

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
Node.4                      Ok
Node.2                      Ok
Node.1                      Ok
Node.5                      Ok
Node.3                      Ok
```

## Obtención del mantenimiento de la aplicación
Este proceso devuelve el mantenimiento de una entidad de aplicación. Contiene los estados de mantenimiento de la aplicación implementada y de los elementos secundarios del servicio. Entrada:

- [Obligatorio] El nombre de la aplicación (URI) que identifica la aplicación.

- [Opcional] La directiva de mantenimiento de aplicación usada para invalidar las directivas de manifiesto de aplicación.

- [Opcional] Filtros para eventos, servicios y aplicaciones implementadas que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, únicamente errores o advertencias y errores). Tenga en cuenta que se utilizan todos los eventos, servicios y aplicaciones implementadas para evaluar el mantenimiento agregado de la entidad, independientemente del filtro.

### API
Para obtener el mantenimiento de la aplicación, cree una instancia de FabricClient y llame al método **GetApplicationHealthAsync** en su instancia de HealthManager.

A continuación se obtiene el mantenimiento de la aplicación para el nombre de aplicación especificado (URI):

```csharp
ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(applicationName).Result;
```

A continuación se obtiene el mantenimiento de la aplicación para el nombre de aplicación especificado (URI), con filtros y directivas personalizadas especificados mediante **System.Fabric.Description.ApplicationHealthQueryDescription**.

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilter = (long)warningAndErrors },
};

ApplicationHealth applicationHealth = fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento de la aplicación es **Get-ServiceFabricApplicationHealth**. Conéctese primero al clúster con el cmdlet **Connect-ServiceFabricCluster**.

El siguiente cmdlet devuelve el mantenimiento de la aplicación fabric:/WordCount:

```powershell
PS c:> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service',
                                  MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='325da69f-16d4-4418-9c30-1feaa40a072c',
                                  AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 2456
                                  SentAt                : 4/20/2015 9:57:06 PM
                                  ReceivedAt            : 4/20/2015 9:57:06 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/20/2015 9:57:06 PM
```

El siguiente cmdlet de PowerShell pasa directivas personalizadas. También filtra los elementos secundarios y los eventos.

```powershell
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error.value__
Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesHealthStateFilter $errorFilter -EventsHealthStateFilter $errorFilter -DeployedApplicationsHealthStateFilter $errorFilter

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                  Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                  Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

## Obtención del mantenimiento del servicio
Este proceso devuelve el mantenimiento de una entidad del servicio. Contiene los estados de mantenimiento de la partición. Entrada:

- [Obligatorio] El nombre del servicio (URI) que identifica el servicio.
- [Opcional] La directiva de mantenimiento de aplicación usada para invalidar la directiva de manifiesto de aplicación.
- [Opcional] Filtros para eventos y particiones que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, únicamente errores o advertencias y errores). Tenga en cuenta que se utilizan todos los eventos y particiones para evaluar el mantenimiento agregado de la entidad, independientemente del filtro.

### API
Para obtener el mantenimiento del servicio mediante la API, cree una instancia de FabricClient y llame al método **GetServiceHealthAsync** en su instancia de HealthManager.

En el ejemplo siguiente se obtiene el mantenimiento de un servicio con el nombre de servicio especificado (URI):

```charp
ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(serviceName).Result;
```

A continuación se obtiene el mantenimiento del servicio para el nombre de servicio especificado (URI), mediante la especificación de filtros y directivas personalizadas a través de System.Fabric.Description.ServiceHealthQueryDescription.

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilter = (long)HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilter = (long)HealthStateFilter.Error },
};

ServiceHealth serviceHealth = fabricClient.HealthManager.GetServiceHealthAsync(queryDescription).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento del servicio es **Get-ServiceFabricServiceHealth**. Conéctese primero al clúster con el cmdlet **Connect-ServiceFabricCluster**.

El siguiente cmdlet obtiene el mantenimiento del servicio con directivas de mantenimiento predeterminadas.

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCount.Service


ServiceName           : fabric:/WordCount/WordCount.Service
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='8f82daff-eb68-4fd9-b631-7a37629e08c0', AggregatedHealthState='Warning'.

                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/20/2015 10:12:29 PM
                        ReceivedAt            : 4/20/2015 10:12:33 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:33 PM
```

## Obtención del mantenimiento de partición
Este proceso devuelve el mantenimiento de una entidad de partición. Contiene los estados de mantenimiento de la réplica. Entrada:

- [Obligatorio] El identificador de partición (GUID) que identifica la partición.

- [Opcional] La directiva de mantenimiento de aplicación usada para invalidar la directiva de manifiesto de aplicación.

- [Opcional] Filtros de eventos y réplicas que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, únicamente errores o advertencias y errores). Tenga en cuenta que se utilizan todos los eventos y réplicas para evaluar el mantenimiento agregado de la entidad, independientemente del filtro.

### API
Para obtener el mantenimiento del servicio mediante la API, cree una instancia de FabricClient y llame al método **GetPartitionHealthAsync** en su instancia de HealthManager. Para especificar parámetros opcionales, cree **System.Fabric.Description.PartitionHealthQueryDescription**.

```csharp
PartitionHealth partitionHealth = fabricClient.HealthManager.GetPartitionHealthAsync(partitionId).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento de la partición es **Get-ServiceFabricPartitionHealth**. Conéctese primero al clúster con el cmdlet **Connect-ServiceFabricCluster**.

El cmdlet siguiente obtiene el mantenimiento de todas las particiones del servicio de recuento de palabras.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricPartitionHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130740415594605870
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415502123433
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605867
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605869
                        AggregatedHealthState : Ok

                        ReplicaId             : 130740415594605868
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 39
                        SentAt                : 4/20/2015 10:12:59 PM
                        ReceivedAt            : 4/20/2015 10:13:03 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/20/2015 10:13:03 PM
```

## Obtención del mantenimiento de réplica
Este proceso devuelve el mantenimiento de una réplica. Entrada:

- [Obligatorio] El identificador de partición (GUID) y el identificador de réplica que identifican a la réplica

- [Opcional] Los parámetros de la directiva de mantenimiento de aplicación usados para invalidar las directivas de manifiesto de aplicación.

- [Opcional] Filtros para eventos que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, únicamente errores o advertencias y errores). Tenga en cuenta que se utilizan todos los eventos para evaluar el mantenimiento agregado de la entidad, independientemente del filtro.

### API
Para obtener el mantenimiento de la réplica mediante la API, cree una instancia de FabricClient y llame al método **GetReplicaHealthAsync** en su instancia de HealthManager. Para especificar parámetros avanzados, use **System.Fabric.Description.ReplicaHealthQueryDescription**.

```csharp
ReplicaHealth replicaHealth = fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento de la réplica es **Get-ServiceFabricReplicaHealth**. Conéctese primero al clúster con el cmdlet **Connect-ServiceFabricCluster**.

El cmdlet siguiente obtiene el mantenimiento de la réplica principal para todas las particiones del servicio:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCount.Service | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415502123433
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740415502802942
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Obtención del mantenimiento de la aplicación implementada
Este proceso devuelve el mantenimiento de una aplicación implementada en una entidad del nodo. Contiene los estados de mantenimiento del paquete de servicio implementado. Entrada:

- [Obligatorio] El nombre de la aplicación (URI) y el nombre del nodo (cadena) que identifican a la aplicación implementada

- [Opcional] La directiva de mantenimiento de aplicación usada para invalidar las directivas de manifiesto de aplicación.

- [Opcional] Filtros de eventos y paquetes de servicio implementados que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, únicamente errores o advertencias y errores). Tenga en cuenta que se utilizan todos los eventos y paquetes de servicio implementados para evaluar el mantenimiento agregado de la entidad, independientemente del filtro.

### API
Para obtener el mantenimiento de una aplicación implementada en un nodo mediante la API, cree una instancia de FabricClient y llame al método **GetDeployedApplicationHealthAsync** en su instancia de HealthManager. Para especificar parámetros opcionales, use **System.Fabric.Description.DeployedApplicationHealthQueryDescription**.

```csharp
DeployedApplicationHealth health = fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName)).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento de la aplicación implementada es **Get-ServiceFabricDeployedApplicationHealth**. Conéctese primero al clúster con el cmdlet **Connect-ServiceFabricCluster**. Para averiguar dónde se implementa una aplicación, ejecute **Get-ServiceFabricApplicationHealth** y observe los elementos secundarios de la aplicación implementada.

El siguiente cmdlet obtiene el mantenimiento de la aplicación fabric:/WordCount implementada en Node.1.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName Node.1
ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCount.WebService
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCount.Service
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130740415502842941
                                     SentAt                : 4/20/2015 10:12:30 PM
                                     ReceivedAt            : 4/20/2015 10:12:34 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Obtención del mantenimiento del paquete de servicio implementado
Este proceso devuelve el mantenimiento de una entidad de paquete de servicio implementada. Entrada:

- [Obligatorio] El nombre de la aplicación (URI), el nombre del nodo (cadena) y el nombre del manifiesto de servicio (cadena) que identifican al paquete de servicio implementado.

- [Opcional] La directiva de mantenimiento de aplicación usada para invalidar la directiva de manifiesto de aplicación.

- [Opcional] Filtros para eventos que especifican las entradas que son de interés y se deben devolver en el resultado (por ejemplo, únicamente errores o advertencias y errores). Tenga en cuenta que se utilizan todos los eventos para evaluar el mantenimiento agregado de la entidad, independientemente del filtro.

### API
Para obtener el mantenimiento de un paquete de servicio implementado mediante la API, cree una instancia de FabricClient y llame al método **GetDeployedServicePackageHealthAsync** en su instancia de HealthManager.

```csharp
DeployedServicePackageHealth health = fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName)).Result;
```

### PowerShell
El cmdlet para obtener el mantenimiento del paquete de servicio implementado es **Get-ServiceFabricDeployedServicePackageHealth**. Conéctese primero al clúster con el cmdlet **Connect-ServiceFabricCluster**. Para averiguar dónde se implementa una aplicación, ejecute **Get-ServiceFabricApplicationHealth** y examine las aplicaciones implementadas. Para ver qué paquetes de servicio están en una aplicación, examine los elementos secundarios del paquete de servicio implementado en la salida de **Get-ServiceFabricDeployedApplicationHealth**.

El siguiente cmdlet obtiene el mantenimiento del paquete de servicio **WordCount.Service** de la aplicación fabric:/WordCount implementada en Node.1. La entidad tiene informes **System.Hosting** para la activación correcta del paquete de servicio y el punto de entrada, y para el registro del tipo de servicio correcto.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName Node.1 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCount.Service

ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCount.Service
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130740415506383060
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130740415506543054
                        SentAt                : 4/20/2015 10:12:30 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCount.Service
                        HealthState           : Ok
                        SequenceNumber        : 130740415520193499
                        SentAt                : 4/20/2015 10:12:32 PM
                        ReceivedAt            : 4/20/2015 10:12:34 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/20/2015 10:12:34 PM
```

## Consultas generales
Las consultas generales devuelven una lista de entidades de Service Fabric de un tipo especificado. Se exponen mediante la API (por medio de los métodos de **FabricClient.QueryManager**), los cmdlets de PowerShell y REST. Estas consultas agregan subconsultas de varios componentes. Uno de ellos es el [almacén de estado](service-fabric-health-introduction.md#health-store), que rellena el estado de mantenimiento agregado para cada resultado de la consulta.

> [AZURE.NOTE] Las consultas generales devuelven el estado de mantenimiento agregado de la entidad y no contienen los datos completos de mantenimiento. Si el mantenimiento de una entidad no es correcto, puede seguir con las consultas de mantenimiento para obtener toda su información de mantenimiento, como eventos, estados de mantenimiento de los elementos secundarios y evaluaciones de mantenimiento incorrecto.

Si las consultas generales devuelven un estado de mantenimiento desconocido para una entidad, es posible que el almacén de estado no tenga datos completos sobre la entidad. También es posible que una subconsulta al almacén de estado no fuera correcta (por ejemplo, se produjo un error de comunicación o se limitó el almacén de estado). Realice un seguimiento con una consulta de mantenimiento para la entidad. Si la subconsulta se encontró con errores transitorios, como problemas de red, esta consulta de seguimiento puede ser de ayuda. También le puede proporcionar más detalles del almacén de estado sobre por qué no se expone la entidad.

Las consultas que contienen **HealthState** para las entidades son las siguientes:

- Lista de nodos: devuelve los nodos de la lista del clúster.
  - API: FabricClient.QueryManager.GetNodeListAsync.
  - PowerShell: Get-ServiceFabricNode.
- Lista de aplicaciones: devuelve la lista de aplicaciones del clúster.
  - API: FabricClient.QueryManager.GetApplicationListAsync.
  - PowerShell: Get-ServiceFabricApplication.
- Lista de servicios: devuelve la lista de servicios de una aplicación.
  - API: FabricClient.QueryManager.GetServiceListAsync.
  - PowerShell: Get-ServiceFabricService.
- Lista de particiones: devuelve la lista de particiones de un servicio.
  - API: FabricClient.QueryManager.GetPartitionListAsync.
  - PowerShell: Get-ServiceFabricPartition.
- Lista de réplicas: devuelve la lista de réplicas de una partición.
  - API: FabricClient.QueryManager.GetReplicaListAsync.
  - PowerShell: Get-ServiceFabricReplica.
- Lista de aplicaciones implementadas: devuelve la lista de aplicaciones implementadas en un nodo.
  - API: FabricClient.QueryManager.GetDeployedApplicationListAsync.
  - PowerShell: Get-ServiceFabricDeployedApplication.
- Lista de paquetes de servicio implementados: devuelve la lista de paquetes de servicio de una aplicación implementada.
  - API: FabricClient.QueryManager.GetDeployedServicePackageListAsync.
  - PowerShell: Get-ServiceFabricDeployedApplication.

### Ejemplos

Lo siguiente obtiene las aplicaciones cuyo mantenimiento es incorrecto en el clúster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

El siguiente cmdlet obtiene los detalles de la aplicación fabric:/WordCount. Observe que el estado de mantenimiento está en advertencia.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "_WFDebugParams_" = "[{"ServiceManifestName":"WordCount.WebService","CodePackageName":"Code","EntryPointType":"Main"}]" }
```

El siguiente cmdlet obtiene los servicios con un estado de mantenimiento de advertencia:

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}

ServiceName            : fabric:/WordCount/WordCount.Service
ServiceKind            : Stateful
ServiceTypeName        : WordCount.Service
IsServiceGroup         : False
ServiceManifestVersion : 1.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## Actualización de clústeres y aplicaciones
Durante una actualización supervisada del clúster y la aplicación, Service Fabric comprueba el mantenimiento para asegurarse de que todo está correcto. Si una entidad es incorrecta según se ha evaluado mediante las directivas de mantenimiento configuradas, la actualización aplica directivas específicas de actualización para determinar la próxima acción. La actualización se puede poner en pausa para permitir la interacción de los usuarios (por ejemplo, corregir las condiciones de error o cambiar las directivas), o se puede revertir automáticamente a la versión buena anterior.

Durante la actualización de un *clúster*, puede recibir el estado de actualización del clúster. Esto incluirá las evaluaciones de mantenimiento incorrecto, que señalan lo que está mal en el clúster. Si se revierte la actualización debido a problemas de mantenimiento, el estado de actualización conserva las razones del último mantenimiento incorrecto. De esta forma se dispone de información que puede ayudar a los administradores a investigar qué salió mal.

De igual forma, durante la actualización de una *aplicación*, las evaluaciones de mantenimiento incorrecto están contenidas en el estado de actualización de la aplicación.

A continuación se muestra el estado de actualización de la aplicación para una aplicación fabric/WordCount modificada. Un guardián ha informado de un error en una de sus réplicas. La actualización se revierte porque no se respetan las comprobaciones de mantenimiento.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : Node1
                                UpgradePhase        : Upgrading

                                NodeName            : Node2
                                UpgradePhase        : Upgrading

                                NodeName            : Node3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCount.Service', MaxPercentUnhealthyServices=0%.

                                Unhealthy service: ServiceName='fabric:/WordCount/WordCount.Service', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', AggregatedHealthState='Error'.

                                Unhealthy replicas: 16% (1/6), MaxPercentUnhealthyReplicasPerPartition=0%.

                                Unhealthy replica: PartitionId='30db5be6-4e20-4698-8185-4bd7ca744020', ReplicaOrInstanceId='130741105362491906', AggregatedHealthState='Error'.

                                Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Lea más sobre la [actualización de aplicaciones de Service Fabric](service-fabric-application-upgrade.md).

## Uso de las evaluaciones de mantenimiento para solucionar problemas
Siempre que haya un problema con el clúster o una aplicación, consulte el mantenimiento del clúster o de la aplicación para analizar lo que pasa. Las evaluaciones de mantenimiento incorrecto proporcionarán detalles sobre lo que activó el estado incorrecto actual. Si lo necesita, puede explorar en profundidad las entidades secundarias incorrectas para identificar la causa principal.

## Pasos siguientes
[Utilización de informes de mantenimiento del sistema para solucionar problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Incorporación de informes de mantenimiento de Service Fabric personalizados](service-fabric-report-health.md)

[Supervisión y diagnóstico de los servicios localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0128_2016-->