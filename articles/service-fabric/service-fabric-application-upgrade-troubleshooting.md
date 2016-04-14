<properties
   pageTitle="Solución de problemas de las actualizaciones de aplicaciones | Microsoft Azure"
   description="En este artículo se tratan algunos de los problemas comunes de actualización de una aplicación de Service Fabric y cómo resolverlos."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/04/2016"
   ms.author="subramar"/>

# Solucionar problemas de actualizaciones de aplicaciones

En este artículo se tratan algunos de los problemas comunes de actualización de una aplicación de Azure Service Fabric y cómo resolverlos.

## Solucionar problemas de una actualización de aplicación con error

Cuando se produce un error en una actualización, la salida del comando **Get-ServiceFabricApplicationUpgrade** incluirá información adicional para depurar el error. Esta información se puede usar para:

1. Identificar el tipo de error.
2. Identificar el motivo del error.
3. Aislar los componentes con error para una investigación más detallada.

Esta información estará disponible tan pronto como Service Fabric encuentre el error, independientemente de que **FailureAction** vaya a revertir o suspender la actualización.

### Identificar el tipo de error

En la salida de **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifica la marca de tiempo (en UTC) en la que Service Fabric encontró un error de actualización y se desencadenó **FailureAction**. **FailureReason** identifica una de las tres posibles causas de alto nivel del error:

1. UpgradeDomainTimeout: indica que un determinado dominio de actualización tardó demasiado tiempo en completarse y **UpgradeDomainTimeout** expiró.
2. OverallUpgradeTimeout: indica que la actualización general tardó demasiado tiempo en completarse y **UpgradeTimeout** expiró.
3. HealthCheck: indica que, tras la actualización de un dominio de actualización, la aplicación permaneció en estado incorrecto según las directivas de mantenimiento especificadas y **HealthCheckRetryTimeout** expiró.

Estas entradas solo aparecerán en la salida cuando se produce un error en la actualización y empieza a revertir. Según el tipo de error, se mostrará información adicional.

### Investigar los tiempos de espera de actualización

Los errores de tiempo de espera de actualización se producen habitualmente por problemas de disponibilidad del servicio. La salida siguiente es típica de las actualizaciones en las que se produce un error de las instancias o las réplicas de servicio en el inicio en la nueva versión de código. El campo **UpgradeDomainProgressAtFailure** captura una instantánea de cualquier trabajo de actualización pendiente en el momento del error.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

En este ejemplo, podemos ver que la actualización generó un error en el dominio de actualización *MYUD1* y que se bloquearon dos particiones (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* y *4b43f4d8-b26b-424e-9307-7a7a62e79750*), por lo que no pudieron colocar las réplicas principales (*WaitForPrimaryPlacement*) en los nodos de destino *Nodo1* y *Nodo4*.

El comando **Get-ServiceFabricNode** se puede usar para comprobar que estos dos nodos están en el dominio de actualización *MYUD1*. La fase *UpgradePhase* indica *PostUpgradeSafetyCheck*, lo que significa que estas comprobaciones de seguridad se producen después de que todos los nodos del dominio de actualización hayan finalizado la actualización. Toda esta información señala a un posible error con la nueva versión del código de la aplicación. Los problemas más habituales son errores de servicio en la apertura o promoción a rutas de acceso de código principal.

Una fase *UpgradePhase* de *PreUpgradeSafetyCheck* indica que se produjeron errores al preparar el dominio de actualización antes de realizar realmente la actualización. Los problemas más habituales en este caso son errores de servicio en el cierre o disminución de nivel de las rutas de acceso de código principales.

El **UpgradeState** actual es *RollingBackCompleted*, de modo que la actualización original debe haberse realizado con una **FailureAction** de reversión, que revierte automáticamente la actualización tras el error. Si la actualización original se había realizado con una **FailureAction** manual, la actualización estaría entonces en un estado suspendido en su lugar para permitir una depuración en directo de la aplicación.

### Investigar errores de comprobación de estado

Los errores de comprobación de estado pueden desencadenarse a raíz de una variedad de problemas adicionales que se pueden producir después de que todos los nodos de un dominio de actualización terminen la actualización y tras pasar todas las comprobaciones de seguridad. La salida siguiente es típica de un error de actualización debido a las comprobaciones de estado con error. El campo **UnhealthyEvaluations** captura una instantánea de todas las comprobaciones de estado con error en el momento del error de actualización según la [Directiva de mantenimiento](service-fabric-health-introduction.md) especificada por el usuario.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

Para investigar errores de comprobación de estado, es necesario conocer el modelo de mantenimiento de Service Fabric, pero incluso sin una comprensión profunda, podemos ver que los dos servicios son incorrectos: *fabric:/DemoApp/Svc3* y *fabric:/DemoApp/Svc2*, junto con los informes de mantenimiento de error ("InjectedFault" en este caso). En este ejemplo, dos de cada cuatro servicios son incorrectos, una relación que están por debajo del objetivo predeterminado de 0 % incorrecto (*MaxPercentUnhealthyServices*).

La actualización se suspendió tras el error especificando una **FailureAction** de manual al iniciar la actualización, por lo que podemos investigar el sistema activo en el estado de error, si se desea, antes de llevar a cabo cualquier acción adicional.

### Recuperarse de una actualización suspendida

Con una **FailureAction** de reversión, no se necesita ninguna recuperación puesto que la actualización se revertirá automáticamente tras el error. Con una **FailureAction** manual, hay varias opciones de recuperación:

1. Desencadenar manualmente una reversión
2. Completar por el resto de la actualización manualmente
3. Reanudar la actualización supervisada

El comando **Start-ServiceFabricApplicationRollback** puede usarse en cualquier momento para empezar a revertir la aplicación. Cuando el comando se devuelve correctamente, la solicitud de reversión se ha registrado en el sistema y se iniciará en breve.

El comando **Resume-ServiceFabricApplicationUpgrade** se puede usar para continuar con el resto de la actualización manualmente, un dominio de actualización cada vez. En este modo, el sistema solo realizará comprobaciones de seguridad. No se realizarán más comprobaciones de estado. Este comando solo se puede usar cuando *UpgradeState* muestra *RollingForwardPending*, lo que significa que el dominio de actualización actual ha finalizado la actualización, pero que el siguiente dominio de actualización no se ha iniciado todavía (está pendiente).

El comando **Update-ServiceFabricApplicationUpgrade** se puede usar para reanudar la actualización supervisada mientras se llevan a cabo las comprobaciones tanto de seguridad como de estado.

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

La actualización continuará desde el dominio de actualización donde se suspendió la última vez y usará los mismos parámetros de actualización y directivas de mantenimiento que antes. En caso necesario, cualquiera de los parámetros de actualización y las directivas de mantenimiento que se muestran en la salida anterior puede cambiarse en el mismo comando cuando la actualización se reanuda. En este ejemplo, la actualización se reanudó en el modo de supervisión dejando a todos los demás parámetros sin cambios y usando los mismos parámetros y directivas de mantenimiento que antes.

## Más información de solución de problemas

### Service Fabric no sigue las directivas de mantenimiento especificadas

Causa posible 1:

Service Fabric convierte todos los porcentajes en números reales de entidades (por ejemplo, réplicas, particiones y servicios) para la evaluación de estado y siempre se redondea al número más cercano de las entidades completas. Por ejemplo, si el máximo de _MaxPercentUnhealthyReplicasPerPartition_ es 21 % y hay cinco réplicas, Service Fabric permitirá hasta dos réplicas (es decir, `Math.Ceiling (5*0.21)`) incorrectas al evaluar el estado de las particiones. Las directivas de mantenimiento deben establecerse como corresponda para representarlo.

Causa posible 2:

Las directivas de mantenimiento se especifican en términos de porcentajes de servicios totales e instancias de servicio no específicas. Por ejemplo, antes de una actualización, supongamos que una aplicación tiene cuatro instancias de servicio A, B, C y D, donde el servicio D es incorrecto, pero sin ningún impacto importante en la aplicación. Deseamos omitir el servicio D incorrecto conocido durante la actualización y establecer el parámetro *MaxPercentUnhealthyServices* para que sea el 25 % suponiendo que solo A, B y C deben ser correctos.

Sin embargo, durante la actualización, D puede convertirse en correcto mientras C pasa a ser incorrecto. La actualización todavía se completaría correctamente en este caso, puesto que solo el 25 % de los servicios son incorrectos, lo que podría provocar errores inesperados debido a que C es inesperadamente incorrecto en lugar de D. En esta situación, D se debe modelar como un tipo de servicio diferente de A, B y C. Puesto que las directivas de mantenimiento se pueden especificar por tipo por servicio, esto permitiría la aplicación de diferentes umbrales de porcentaje incorrectos a servicios diferentes en función de sus roles en la aplicación.

### No he especificado una directiva de mantenimiento para la actualización de la aplicación, pero la actualización continúa generando errores para algunos tiempos de expiración que nunca he especificado

Si las directivas de mantenimiento no se proporcionan para la solicitud de actualización, se obtienen del archivo *ApplicationManifest.xml* de la versión actual de la aplicación. Por ejemplo, si va a actualizar la aplicación X de v1 a v2, se utilizan las directivas de mantenimiento de la aplicación especificadas para la aplicación X en v1. Si se debe usar una directiva de mantenimiento diferente para la actualización, la directiva necesita especificarse como parte de la llamada API de actualización de la aplicación. Tenga en cuenta que las directivas que se especifican como parte de la llamada API solo se aplican durante la actualización. Cuando se completa la actualización, se usan las directivas especificadas en el archivo *ApplicationManifest.xml*.

### Se especifican los tiempos de expiración incorrectos

Los usuarios pueden preguntarse sobre lo que ocurre si los tiempos de expiración se establecen de forma incoherente, por ejemplo, si *UpgradeTimeout* es menor que *UpgradeDomainTimeout*. La respuesta es que se devuelve un error. Otros casos donde esto puede ocurrir es si *UpgradeDomainTimeout* es menor que la suma de *HealthCheckWaitDuration* y *HealthCheckRetryTimeout*, o si *UpgradeDomainTimeout* es menor que la suma de *HealthCheckWaitDuration* y *HealthCheckStableDuration*.

### Mis actualizaciones están tardando mucho tiempo

El tiempo necesario para que se complete una actualización depende de los diversos tiempos de expiración y comprobaciones de estado especificados, que a su vez dependen del tiempo que la aplicación tarda en actualizarse (incluidas las acciones de copiar el paquete, implementar y estabilizar). Ser demasiado agresivo con los tiempos de expiración puede dar lugar a que se realicen más actualizaciones con errores; por ello, recomendamos empezar de manera conservadora con tiempos de expiración más prolongados.

A continuación ofrecemos un repaso rápido sobre la interacción de los tiempos de expiración con los tiempos de actualización:

Las actualizaciones de un dominio de actualización no se pueden completar con mayor rapidez que *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

No se puede producir un error de actualización con mayor rapidez que *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

El tiempo de actualización de un dominio de actualización está limitado por *UpgradeDomainTimeout*. Si *HealthCheckRetryTimeout* y *HealthCheckStableDuration* son distintos de cero y el estado de la aplicación continúa pasando de atrás para adelante, la actualización agotará el tiempo de expiración en *UpgradeDomainTimeout*. *UpgradeDomainTimeout* comienza la cuenta atrás cuando comienza la actualización para el dominio de actualización actual.

## Pasos siguientes

El procedimiento de [actualización de aplicaciones usando Visual Studio](service-fabric-application-upgrade-tutorial.md) ofrece información para actualizar una aplicación mediante Visual Studio.

El procedimiento de [actualización de aplicaciones usando Powershell](service-fabric-application-upgrade-tutorial-powershell.md) ofrece información para actualizar una aplicación mediante PowerShell.

Puede controlar cómo se actualiza una aplicación usando [parámetros de actualización](service-fabric-application-upgrade-parameters.md).

Consiga que sus actualizaciones de aplicaciones sean compatibles aprendiendo a usar la [serialización de datos](service-fabric-application-upgrade-data-serialization.md).

Aprenda a usar funcionalidades avanzadas para actualizar una aplicación. Para ello, consulte los [temas avanzados](service-fabric-application-upgrade-advanced.md).

Solucione problemas habituales en las actualizaciones de aplicaciones consultando los pasos que figuran en [Solución de problemas de las actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md).
 

<!---HONumber=AcomDC_0211_2016-->