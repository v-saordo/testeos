<properties
   pageTitle="Solución de problemas con los informes de mantenimiento del sistema | Microsoft Azure"
   description="Describe los informes de estado enviados por los componentes de Azure Service Fabric y su uso para la resolución de problemas de clúster o de aplicaciones."
   services="service-fabric"
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

# Utilización de informes de mantenimiento del sistema para solucionar problemas

Los componentes de Azure Service Fabric informan desde el primer momento de todas las entidades del clúster. El [almacén de estado](service-fabric-health-introduction.md#health-store) crea y elimina entidades basándose en los informes del sistema. También las organiza en una jerarquía que captura las interacciones de la entidad.

> [AZURE.NOTE] Obtenga más información sobre el [Modelo de mantenimiento de Service Fabric](service-fabric-health-introduction.md) para comprender los conceptos relacionados con el mantenimiento.

Los informes de mantenimiento del sistema proporcionan visibilidad en el clúster y funcionalidad de la aplicación, así como indican problemas a través del mantenimiento. Para aplicaciones y servicios, los informes de mantenimiento del sistema comprueban que las entidades se implementan y que se comportan correctamente desde la perspectiva de Service Fabric. Los informes no proporcionan ninguna supervisión del mantenimiento de la lógica empresarial del servicio o de la detección de los procesos bloqueados. Los servicios de usuario pueden enriquecer los datos de mantenimiento con información específica de su lógica.

> [AZURE.NOTE] Solo son visibles los informes de mantenimiento de los guardianes *después* de que los componentes del sistema hayan creado la entidad. Cuando se elimina una entidad, el almacén de estado elimina automáticamente todos los informes de mantenimiento asociados a ella. Lo mismo sucede cuando se crea una nueva instancia de la entidad (por ejemplo, se crea una nueva instancia de la réplica de servicio). Todos los informes asociados a la instancia anterior se eliminan y se limpian del almacén.

Los informes de los componentes del sistema se identifican mediante el origen, que comienza por el prefijo **"System"**. Los guardianes no pueden utilizar el mismo prefijo para sus orígenes, ya que los informes con parámetros no válidos se rechazarán. Veamos algunos informes de sistema para entender qué los desencadena y cómo corregir los posibles problemas que representan.

> [AZURE.NOTE] Service Fabric sigue agregando informes de condiciones de interés que mejoran la visibilidad de lo que sucede en el clúster y la aplicación.

## Informes de mantenimiento del sistema de clúster
La entidad de mantenimiento del clúster se crea automáticamente en el almacén de estado, por tanto si todo funciona correctamente, no habrá un informe del sistema.

### Pérdida de entorno
**System.Federation** notifica un error cuando detecta una pérdida de entorno. El informe procede de los nodos individuales y el identificador de nodo está incluido en el nombre de la propiedad. Si hay una pérdida de entorno en todo el anillo de Service Fabric, puede esperar dos eventos (se notificarán ambos lados del intervalo). Si se pierden mas entornos, se producirán más eventos.

El informe especifica el tiempo de espera de concesión global como período de vida. El informe se vuelve a enviar una vez transcurrida la mitad de la duración del período de vida siempre y cuando la condición permanezca activa. El evento se quita automáticamente cuando expira, por lo que si el nodo de informes está inactivo, aún así se limpiará correctamente del almacén de estado.

- **SourceId**: System.Federation
- **Propiedad**: comienza por **Neighborhood** e incluye información sobre el nodo
- **Pasos siguientes**: investigue por qué se pierde el entorno (por ejemplo, compruebe la comunicación entre los nodos del clúster).

## Informes de mantenimiento del sistema de nodos
**System.FM**, que representa el servicio Administrador de conmutación por error, es la autoridad que administra la información acerca de los nodos del clúster. Todos los nodos deben tener un informe de System.FM que muestre el estado. Las entidades de nodo se quitan cuando el nodo está deshabilitado.

### Nodo activo o inactivo
System.FM notifica que está todo correcto cuando el nodo se une al anillo (está en funcionamiento). Notifica un error cuando el nodo sale del anillo (no funciona, ya sea porque se está actualizando o simplemente porque no pudo). La jerarquía de mantenimiento generada por el almacén de estado realiza una acción sobre las entidades implementadas en correlación con los informes de nodo de System.FM. Considera el nodo como un elemento primario virtual de todas las entidades implementadas. Las entidades implementadas en ese nodo no se exponen a través de las consultas si el nodo está inactivo o no se ha notificado, o si el nodo tiene una instancia distinta de la instancia asociada a las entidades. Cuando System.FM notifica que el nodo está inactivo o que se ha reiniciado (una nueva instancia), el almacén de estado limpia automáticamente las entidades implementadas que solo pueden existir en el nodo inactivo o en la instancia anterior del nodo.

- **SourceId**: System.FM
- **Propiedad**: State
- **Pasos siguientes**: si el nodo está inactivo durante una actualización, debería volver a estar activo una vez que se haya actualizado. En este caso, se debe cambiar el estado de mantenimiento a Aceptar. Si el nodo no recupera ese estado o se produce un error, deberá investigar más el problema.

A continuación, se muestra el evento System.FM con el estado de mantenimiento Correcto para el nodo activo:

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### Caducidad del certificado
**System.FabricNode** notifica una advertencia cuando los certificados utilizados por el nodo están a punto de expirar. Hay tres certificados por nodo: **Certificate\_cluster**, **Certificate\_server** y **Certificate\_default\_client**. Si faltan más de dos semanas para expirar, el estado de mantenimiento del informe es Correcto. Si faltan menos de dos semanas, el tipo de informe es una advertencia. El TTL de estos eventos es infinito y se quitan cuando un nodo deja el clúster.

- **SourceId**: System.FabricNode
- **Propiedad**: comienza por **Certificate** y contiene más información sobre el tipo de certificado
- **Pasos siguientes**: actualice los certificados si están próximos a expirar.

### Infracción de la capacidad de carga
El equilibrador de carga de Service Fabric notifica una advertencia si detecta una infracción de la capacidad de nodo.

 - **SourceId**: System.PLB
 - **Propiedad**: comienza por **Capacity**
 - **Pasos siguientes**: compruebe las métricas proporcionadas y vea la capacidad actual en el nodo.

## Informes de mantenimiento del sistema de la aplicación
**System.CM**, que representa el servicio Administrador de clústeres, es la autoridad que administra la información acerca de una aplicación.

### Estado
System.CM notifica un estado Correcto cuando se ha creado o actualizado la aplicación. Informa al almacén de estado cuando se ha eliminado la aplicación, por lo que puede quitarse del almacén.

- **SourceId**: System.CM
- **Propiedad**: State
- **Pasos siguientes**: si se ha creado la aplicación, debe incluir el informe de mantenimiento del Administrador de clústeres. De lo contrario, compruebe el estado de la aplicación mediante el envío de una consulta (por ejemplo, el cmdlet de PowerShell **Get-ServiceFabricApplication -ApplicationName *applicationName***).

A continuación se muestra el evento de estado en la aplicación **fabric:/WordCount**:

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None) -DeployedApplicationsHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## Informes de mantenimiento del sistema de servicio
**System.FM**, que representa el servicio Administrador de conmutación por error, es la autoridad que administra la información acerca de los servicios.

### Estado
System.FM notifica un estado Correcto cuando se ha creado el servicio. Elimina la entidad del almacén de estado cuando se ha eliminado el servicio.

- **SourceId**: System.FM
- **Propiedad**: State

A continuación, se muestra el evento de estado en el servicio **fabric:/WordCount/WordCountService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### Infracción de réplicas sin colocar
**System.PLB** notifica una advertencia si no pudo encontrar una selección de ubicación para una o varias de las réplicas del servicio. El informe se quita cuando expira.

- **SourceId**: System.FM
- **Propiedad**: State
- **Pasos siguientes**: compruebe las restricciones de servicio y el estado actual de la selección de ubicación.

## Informes de mantenimiento del sistema de partición
**System.FM**, que representa el servicio Administrador de conmutación por error, es la autoridad que administra la información acerca de las particiones del servicio.

### Estado
System.FM notifica un estado Correcto cuando la partición se ha creado y es correcta. Elimina la entidad del almacén de estado cuando se ha eliminado la partición.

Si la partición es inferior al recuento mínimo de réplicas, notifica un error. Si la partición no es inferior al recuento mínimo de réplicas, pero es inferior al recuento objetivo de réplicas, notifica una advertencia. Si la partición está en situación de pérdida de cuórum, System.FM notifica un error.

Otros eventos importantes incluyen una advertencia cuando la reconfiguración tarda más tiempo de lo esperado y cuando la compilación tarda más de lo previsto. Los tiempos de compilación o reconfiguración previstos se pueden configurar en función de los escenarios de servicio. Por ejemplo, si un servicio tiene un terabyte de estado como, por ejemplo, una base de datos SQL, la compilación tardará más tiempo del que tardaría para un servicio con una pequeña cantidad de estado.

- **SourceId**: System.FM
- **Propiedad**: State
- **Pasos siguientes**: si el estado de mantenimiento no es correcto, es posible que algunas réplicas no se hayan creado, abierto o promocionado a principales o secundarias de manera correcta. En muchos casos, la causa raíz es un error de servicio en la implementación del rol de apertura o cambio.

A continuación, se muestra una partición correcta:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

Lo siguiente muestra el mantenimiento de una partición que es inferior al recuento objetivo de réplicas. El paso siguiente consiste en obtener la descripción de la partición, que muestra cómo se ha configurado: **MinReplicaSetSize** es dos y **TargetReplicaSetSize** es siete. Después, obtenga el número de nodos del clúster: cinco. Así que en este caso, no se pueden colocar dos réplicas.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasHealthStateFilter ([System.Fabric.Health.HealthStateFilter]::None)

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### Infracción de restricción de réplica
**System.PLB** notifica una advertencia si detecta una infracción de restricción de réplica y no se pueden colocar réplicas de la partición.

- **SourceId**: System.PLB
- **Propiedad**: comienza por **ReplicaConstraintViolation**

## Informes de mantenimiento del sistema de replica
**System.RA**, que representa el componente del agente de reconfiguración, es la autoridad para el estado de la réplica.

### Estado
**System.RA** notifica un estado Correcto cuando se ha creado la réplica.

- **SourceId**: System.RA
- **Propiedad**: State

Lo siguiente muestra una réplica correcta.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### Estado de apertura de réplica
La descripción de este informe de mantenimiento contiene la hora de inicio (hora universal coordinada) de cuando se ha invocado la llamada API.

**System.RA** notifica una advertencia si la apertura de la réplica tarda más tiempo del configurado (valor predeterminado: 30 minutos). Si la API afecta a la disponibilidad de servicio, el informe se emite con mayor rapidez (intervalo configurable, valor predeterminado de 30 segundos). Este tiempo incluye el tiempo necesario para la apertura del replicador y del servicio. La propiedad cambia a Correcto si se completa la apertura.

- **SourceId**: System.RA
- **Propiedad**: **ReplicaOpenStatus**
- **Pasos siguientes**: si el estado de mantenimiento no es Correcto, compruebe por qué la apertura de la réplica tarda más de lo previsto.

### Llamada a la API de servicio lenta
**System.RAP** y **System.Replicator** notifican una advertencia si una llamada al código de servicio de usuario tarda más tiempo del configurado. La advertencia se borra cuando finaliza la llamada.

- **SourceId**: System.RAP o System.Replicator
- **Propiedad**: el nombre de la API lenta. La descripción proporciona más detalles sobre el tiempo que la API ha estado pendiente.
- **Pasos siguientes**: investigue por qué la llamada tarda más tiempo de lo previsto.

El ejemplo siguiente muestra una partición en situación de pérdida de cuórum, así como los pasos de investigación realizados para descubrir el motivo. Una de las réplicas tiene un estado de mantenimiento de advertencia, por tanto, ya conoce su estado. Muestra que la operación de servicio tarda más de lo previsto, un evento notificado por System.RAP. Después de recibir esta información, el paso siguiente es examinar el código de servicio e investigar. En este caso, la implementación de **RunAsync** del servicio con estado produce una excepción no controlada. Tenga en cuenta que las réplicas se reciclan, por lo que quizás no pueda ver ninguna réplica en estado de advertencia. Puede intentar obtener de nuevo el estado de mantenimiento y buscar las diferencias en el identificador de réplica. En algunos casos, esto le puede proporcionar pistas.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

Al iniciar la aplicación defectuosa en el depurador, las ventanas de eventos de diagnóstico muestran la excepción de RunAsync:

![Eventos de diagnóstico de Visual Studio 2015: Error de RunAsync en fabric:/HelloWorldStatefulApplication.][1]

Eventos de diagnóstico de Visual Studio 2015: Error de RunAsync en **fabric:/HelloWorldStatefulApplication**.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### Cola de replicación completa
**System.Replicator** notifica una advertencia si la cola de replicación está llena. En la réplica principal, esto suele ocurrir porque una o varias réplicas secundarias son lentas a la hora de confirmar operaciones. En la secundaria, esto suele ocurrir cuando el servicio es lento en aplicar las operaciones. La advertencia se borra cuando la cola ya no está llena.

- **SourceId**: System.Replicator
- **Propiedad**: **PrimaryReplicationQueueStatus** o **SecondaryReplicationQueueStatus**, dependiendo del rol de réplica

## Informes de mantenimiento del sistema DeployedApplication
**System.Hosting** es la autoridad en las entidades implementadas.

### Activación
System.Hosting notifica un estado Correcto cuando una aplicación se ha activado correctamente en el nodo. De lo contrario, notifica un error.

- **SourceId**: System.Hosting
- **Propiedad**: activación, incluida la versión de lanzamiento
- **Pasos siguientes**: si el mantenimiento de la aplicación es incorrecto, investigue el motivo del error de la activación.

A continuación se muestra una activación correcta:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### Descargar
**System.Hosting** notifica un error si se produjo un error al descargar el paquete de aplicación.

- **SourceId**: System.Hosting
- **Propiedad**: **Descargar:*RolloutVersion***
- **Pasos siguientes**: investigue el motivo del error de descarga en el nodo.

## Informes de mantenimiento del sistema DeployedServicePackage
**System.Hosting** es la autoridad en las entidades implementadas.

### Activación de paquete de servicio
System.Hosting notifica un estado Correcto si la activación del paquete de servicio en el nodo se ha realizado correctamente. De lo contrario, notifica un error.

- **SourceId**: System.Hosting
- **Property**: Activation
- **Pasos siguientes**: investigue el motivo del error de la activación.

### Activación del paquete de código
**System.Hosting** notifica un estado Correcto para cada paquete de código si la activación se ha realizado correctamente. Si se produce un error en la activación, notifica una advertencia tal y como está configurado. Si **CodePackage** no se puede activar o finaliza con un error mayor que el configurado **CodePackageHealthErrorThreshold**, hosting notifica un error. Si hay varios paquetes de código en un paquete de servicios, se generará un informe de activación para cada uno.

- **SourceId**: System.Hosting
- **Propiedad**: utiliza el prefijo **CodePackageActivation** y contiene el nombre del paquete de código y el punto de entrada como **CodePackageActivation:*CodePackageName*:*SetupEntryPoint/EntryPoint*** (por ejemplo, **CodePackageActivation:Code:SetupEntryPoint**)

### Registro del tipo de servicio
**System.Hosting** notifica un estado Correcto si el tipo de servicio se ha registrado correctamente. Notifica un error si el registro no se ha realizado a tiempo (tal y como se ha configurado mediante **ServiceTypeRegistrationTimeout**). Si se ha anulado el registro del tipo de servicio del nodo, es debido a que se cerró el tiempo de ejecución. Hosting notifica una advertencia.

- **SourceId**: System.Hosting
- **Propiedad**: utiliza el prefijo **ServiceTypeRegistration** y contiene el nombre del tipo de servicio (por ejemplo, **ServiceTypeRegistration:FileStoreServiceType**)

A continuación, se muestra un paquete de servicio implementado con mantenimiento correcto:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### Descargar
**System.Hosting** notifica un error si se produjo un error al descargar el paquete de servicio.

- **SourceId**: System.Hosting
- **Propiedad**: **Descargar:*RolloutVersion***
- **Pasos siguientes**: investigue el motivo del error de descarga en el nodo.

### Validación de actualización
**System.Hosting** notifica un error si se produce un error de validación durante la actualización o si se produce un error de actualización en el nodo.

- **SourceId**: System.Hosting
- **Propiedad**: utiliza el prefijo **FabricUpgradeValidation** y contiene la versión de actualización
- **Descripción**: señala el error encontrado.

## Pasos siguientes
[Vista de los informes de estado de Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Supervisión y diagnóstico de los servicios localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0128_2016-->