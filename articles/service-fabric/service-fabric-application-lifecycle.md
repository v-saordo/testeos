<properties
   pageTitle="Ciclo de vida de la aplicación de Service Fabric | Microsoft Azure"
   description="Describe el desarrollo, la implementación, las pruebas, la actualización, el mantenimiento y la eliminación de aplicaciones de Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="ryanwi"/>


# Ciclo de vida de la aplicación de Service Fabric
Al igual que sucede con otras plataformas, una aplicación en Azure Service Fabric normalmente pasa las siguientes fases: diseño, desarrollo, prueba, implementación, actualización, mantenimiento y eliminación. Service Fabric ofrece compatibilidad de primera clase para todo el ciclo de vida de aplicación de las aplicaciones de nube: desde el desarrollo hasta la implementación, la administración diaria, el mantenimiento y, finalmente, la retirada. El modelo de servicio habilita varios roles distintos para participar de manera independiente en el ciclo de vida de la aplicación. Este artículo proporciona información general de las API y cómo son usadas por los distintos roles durante todas las fases del ciclo de vida de aplicación de Service Fabric.

## Roles del modelo de servicio
Los roles del modelo de servicio son:

- **Desarrollador del servicio**: desarrolla servicios modulares y genéricos que se pueden volver a definir y usar en varias aplicaciones del mismo tipo o de tipos distintos. Por ejemplo, un servicio de cola puede usarse para crear una aplicación de incidencias (departamento de soporte técnico) y una aplicación de comercio electrónico (carro de la compra).

- **Desarrollador de la aplicación**: crea aplicaciones mediante la integración de una recopilación de servicios para satisfacer ciertos escenarios o requisitos específicos. Por ejemplo, un sitio web de comercio electrónico podría integrar "Servicio front-end sin estado JSON", "Servicio con estado de subasta" y "Servicio con estado de cola" para crear una solución de subasta.

- **Administrador de aplicaciones**: toma decisiones sobre la configuración de las aplicaciones (relleno de los parámetros de la plantilla de configuración), implementación (asignación a recursos disponibles) y calidad de servicio. Por ejemplo, un administrador de aplicaciones decide la configuración regional de idioma (por ejemplo, inglés para Estados Unidos o japonés para Japón) de la aplicación. Una aplicación implementada diferente puede tener configuraciones distintas.

- **Operador**: implementa aplicaciones basadas en la configuración de la aplicación y los requisitos especificados por el administrador de aplicaciones.. Por ejemplo, un operador aprovisiona e implementa la aplicación y garantiza que se ejecuta en Azure. Los operadores supervisan la información de rendimiento y estado de la aplicación y mantiene la infraestructura física, según corresponda.


## Desarrollo
1. Un *desarrollador de servicios* desarrolla los distintos tipos de servicios con el modelo de programación [Reliable Actors](service-fabric-reliable-actors-introduction.md) o [Reliable Services](service-fabric-reliable-services-introduction.md).
2. Un *desarrollar de servicio* describe, mediante declaración, los tipos de servicio desarrollados en un archivo de manifiesto de servicio que consta de uno o más paquetes de código, configuración y datos.
3. Un *desarrollador de aplicaciones* luego crea una aplicación con distintos tipos de servicio.
4. Un *desarrollador de aplicaciones* describe, mediante declaración, el tipo de aplicación en un manifiesto de aplicación a través de la referencia a los manifiestos de servicio de los servicios constituyentes y anulan y parametrizan de manera adecuada las distintas configuraciones de ajuste e implementación de los servicios constituyentes.

Consulte [Introducción a los Reliable Actors](service-fabric-reliable-actors-get-started.md) e [Introducción a los Reliable Services](service-fabric-reliable-services-quick-start.md) para obtener ejemplos.

## Implementación
1. Un *administrador de aplicaciones* adapta el tipo de aplicación a una aplicación específica para su implementación en un clúster de Service Fabric mediante la especificación de los parámetros adecuados del elemento **ApplicationType** en el manifiesto de aplicación.

2. Un *operador* carga el paquete de aplicación al clúster ImageStore mediante el uso del método [**CopyApplicationPackage**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) o el cmdlet [**Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx). El paquete de aplicación contiene el manifiesto de aplicación y la recopilación de los paquetes de servicio. Service Fabric implementa aplicaciones desde el paquete de aplicación almacenado en ImageStore, que puede ser un almacenamiento de blobs de Azure o el servicio de sistema de Service Fabric.

3. Luego el *operador* aprovisiona el tipo de aplicación en el clúster de destino desde el paquete de aplicación cargado mediante el uso del método [**ProvisionApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), el cmdlet [**Register-ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125958.aspx) o la operación de REST [**Aprovisionar aplicación**](https://msdn.microsoft.com/library/azure/dn707672.aspx).

4. Después de realizar el aprovisionamiento de la aplicación, un *operador* inicia la aplicación con los parámetros que suministra el *administrador de aplicaciones* mediante el uso del método [**CreateApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx), el cmdlet [**New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx) o la operación REST [**Crear aplicación**](https://msdn.microsoft.com/library/azure/dn707676.aspx).

5. Una vez implementada la aplicación, un *operador* usa el [método **CreateServiceAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx), el [cmdlet **New-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125874.aspx) o la [operación REST **Aprovisionar aplicación**](https://msdn.microsoft.com/library/azure/dn707657.aspx) para crear nuevas instancias de servicio de la aplicación basándose en tipos de servicio disponibles.

6. La aplicación ahora se ejecuta en el clúster de Service Fabric.

Consulte [Implementación de una aplicación](service-fabric-deploy-remove-applications.md) para ver ejemplos.

## Prueba
1. Después de realizar la implementación en el clúster de desarrollo local o en un clúster de prueba, un *desarrollador de servicio* ejecuta el escenario de prueba de conmutación por error integrado mediante el uso de las clases [**FailoverTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx) y [**FailoverTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) o el cmdlet [**Invoke-ServiceFabricFailoverTestScenario**](https://msdn.microsoft.com/library/azure/mt125935.aspx). El escenario de prueba de conmutación por error ejecuta un servicio especificado a través de importantes transiciones y conmutadores por error para asegurarse de que sigue disponible y en funcionamiento.

2. El *desarrollador de servicio* luego ejecuta el escenario de prueba de caos integrado mediante el uso de las clases [**ChaosTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) y [**ChaosTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) o el cmdlet [**Invoke-ServiceFabricChaosTestScenario**](https://msdn.microsoft.com/library/azure/mt126036.aspx). El escenario de prueba de caos induce de manera aleatoria varios errores de nodo, paquete de código y réplica en el clúster.

Consulte [Escenarios de capacidad de prueba](service-fabric-testability-scenarios.md) para obtener ejemplos.

## Actualizar
1. Un *desarrollador de servicio* actualiza los servicios constituyentes de la aplicación con instancias y/o corrige errores y proporciona una versión nueva del manifiesto de servicio.

2. Un *desarrollador de aplicaciones* anula y parametrizan las configuraciones de ajuste e implementación de los servicios constituyentes y proporciona una versión nueva del manifiesto de aplicación. A continuación, el desarrollador de aplicaciones incorpora las versiones nuevas de los manifiestos de servicio a la aplicación y proporciona una versión nueva del tipo de aplicación en un paquete de aplicación actualizado.

3. Un *administrador de aplicaciones* incorpora la versión nueva del tipo de aplicación a la aplicación de destino mediante la actualización de los parámetros adecuados.

4. Un *operador* carga el paquete de aplicación actualizado al clúster ImageStore mediante el uso del método [**CopyApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) o el cmdlet [**Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx). El paquete de aplicación contiene el manifiesto de aplicación y la recopilación de los paquetes de servicio.

5. Un *operador* aprovisiona la versión nueva de la aplicación en el clúster de destino mediante el uso del método [**ProvisionApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), el cmdlet [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) o la operación REST [**Aprovisionar una aplicación**](https://msdn.microsoft.com/library/azure/dn707672.aspx).

6. Un *operador* actualiza la aplicación de destino a la versión nueva mediante el uso del [método **UpgradeApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx), el [cmdlet **Start-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx) o la [operación REST **Actualizar aplicación por tipo de aplicación**](https://msdn.microsoft.com/library/azure/dn707633.aspx).

7. Un *operador* controla el progreso de la actualización mediante el uso del método [**GetApplicationUpgradeProgressAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx), el [**Get-ServiceFabricApplicationUpgrade** ](https://msdn.microsoft.com/library/azure/mt125988.aspx) o la operación REST [**Obtener progreso de la actualización de la aplicación**](https://msdn.microsoft.com/library/azure/dn707631.aspx).

8. En caso de ser necesario, el *operador* modifica y vuelve a aplicar los parámetros de la actualización de la aplicación actual mediante el uso del método [**UpdateApplicationUpgradeAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx), el cmdlet [**Update-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt126030.aspx) o la operación REST [**Actualizar actualización de la aplicación**](https://msdn.microsoft.com/library/azure/mt628489.aspx).

9. En caso de ser necesario, el *operador* revierte la actualización de la aplicación actual mediante el uso del método [**RollbackApplicationUpgradeAsync** method](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx), el cmdlet [**Start-ServiceFabricApplicationRollback**](https://msdn.microsoft.com/library/azure/mt125833.aspx) o la operación REST [**Revertir actualización de la aplicación**](https://msdn.microsoft.com/library/azure/mt628494.aspx).

10. Service Fabric actualiza la aplicación de destino que se ejecuta en el clúster sin perder la disponibilidad de ninguno de sus servicios constituyentes.

Consulte [Tutorial de actualización de la aplicación](service-fabric-application-upgrade-tutorial.md) para obtener ejemplos.

## Mantenimiento
1. Para obtener actualizaciones y revisiones del sistema operativo, Service Fabric interactúa con la infraestructura de Azure para garantizar la disponibilidad de todas las aplicaciones que se ejecutan en el clúster.

2. En el caso de las actualizaciones y revisiones de la plataforma de Service Fabric, Service Fabric realiza actualizaciones sin perder la disponibilidad de ninguna de las aplicaciones que se ejecutan en el clúster.

3. Un *administrador de aplicaciones* aprueba la adición o eliminación de nodos de un clúster después de analizar los datos históricos de utilización de capacidad y la demanda futura prevista.

4. Un *operador* agrega y quita nodos especificados por el *administrador de aplicaciones*.

5. Cuando se agregan nodos nuevos al clúster o se quitan nodos existentes, Service Fabric equilibra automáticamente la carga de las aplicaciones en ejecución en todos los nodos del clúster para lograr un rendimiento óptimo.

## Remove
1. Un *operador* puede eliminar una instancia específica de un servicio en ejecución en el clúster sin quitar la aplicación completa mediante el uso del método [**DeleteServiceAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx), el cmdlet [**Remove-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt126033.aspx) o la operación REST [**Eliminar servicio**](https://msdn.microsoft.com/library/azure/dn707687.aspx).  

2. Un *operador* también puede eliminar una instancia de aplicación y todos sus servicios mediante el uso del método [**DeleteApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx), el cmdlet [**Remove-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx) o la operación REST [**Eliminar aplicación**](https://msdn.microsoft.com/library/azure/dn707651.aspx).

3. Una vez detenidos los servicios y la aplicación, el *operador* puede deshacer el aprovisionamiento del tipo de aplicación mediante el uso del método [**UnprovisionApplicationAsync**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx), el cmdlet [**Unregister-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx) o la operación REST [**Deshacer aprovisionamiento de una aplicación**](https://msdn.microsoft.com/library/azure/dn707671.aspx). Deshacer el aprovisionamiento del tipo de aplicación no quita el paquete de aplicación del ImageStore. Debe quitar el paquete de aplicación manualmente.

4. Un *operador* quita el paquete de aplicación de ImageStore mediante el uso del método [**RemoveApplicationPackage**](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx) o el cmdlet [**Remove-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx).

Consulte [Implementación de una aplicación](service-fabric-deploy-remove-applications.md) para ver ejemplos.

## Pasos siguientes

Para obtener más información sobre cómo desarrollar, probar y administrar aplicaciones y servicios de Service Fabric, consulte:

- [Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)
- [Implementar una aplicación](service-fabric-deploy-remove-applications.md)
- [Actualización de aplicaciones](service-fabric-application-upgrade.md)
- [Información general sobre Testability](service-fabric-testability-overview.md)
- [Ejemplo de ciclo de vida de aplicaciones basadas en REST](service-fabric-rest-based-application-lifecycle-sample.md)

<!---HONumber=AcomDC_0211_2016-->