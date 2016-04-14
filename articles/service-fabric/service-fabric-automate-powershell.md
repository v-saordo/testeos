<properties
	pageTitle="Automatización de la administración de aplicaciones de Service Fabric con PowerShell | Microsoft Azure"
	description="Implementación, actualización, prueba y eliminación de aplicaciones de Service Fabric con PowerShell."
	services="service-fabric"
	documentationCenter=".net"
	authors="rwike77"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-fabric"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/14/2015"
	ms.author="ryanwi"/>

# Implementación, actualización, prueba y eliminación de aplicaciones de Service Fabric con PowerShell

En este artículo se muestra cómo usar PowerShell para automatizar las tareas comunes para implementar, actualizar, quitar y probar aplicaciones de Service Fabric.

## Requisitos previos

Antes de pasar a las tareas que aparecen en el artículo, asegúrese de [Instalar el tiempo de ejecución, el SDK y las herramientas](service-fabric-get-started.md) que también instalan los módulos **ServiceFabric** y **ServiceFabricTestability** de PowerShell. [Habilite la ejecución de scripts de PowerShell](service-fabric-get-started.md#enable-powershell-script-execution) e [instale e inicie un clúster local](service-fabric-get-started.md#install-and-start-a-local-cluster) para poder ejecutar los ejemplos que aparecen en el artículo.

Los ejemplos de este artículo usan la aplicación de ejemplo [**WordCount**](http://aka.ms/servicefabricsamples) (que se encuentra en los ejemplos de introducción). Descargue y compile la aplicación de ejemplo.

Antes de ejecutar los comandos de PowerShell en este artículo, primero conéctese al clúster de Service Fabric local mediante [**Connect-ServiceFabricCluster**](https://msdn.microsoft.com/library/azure/mt125938.aspx):

```powershell
Connect-ServiceFabricCluster localhost:19000
```

## Tarea: Implementar una aplicación de Service Fabric

Una vez que compile la aplicación y que se empaquete el tipo de aplicación, puede implementar esta aplicación en un clúster de Service Fabric. En primer lugar, empaquete la aplicación WordCount en Visual Studio. Para ello, haga clic con el botón derecho en **WordCount** en el Explorador de soluciones y seleccione **Empaquetar**. Consulte [Modelar una aplicación en Service Fabric](service-fabric-application-model.md) para obtener información sobre el servicio, los manifiestos de la aplicación y el diseño del paquete. La implementación significa cargar el paquete de aplicación, registrar el tipo de aplicación y crear la instancia de la aplicación. Use las instrucciones que aparecen en esta sección para implementar una aplicación nueva en un clúster.

### Paso 1: cargar el paquete de aplicación
Cargar el paquete de aplicación en el almacén de imágenes lo pone en una ubicación a la que pueden tener acceso los componentes internos de Service Fabric. El paquete de aplicación contiene el manifiesto de aplicación, los manifiestos de servicio y los paquetes de código/configuración/datos necesarios para crear las instancias de servicio y aplicación. El comando [**Copy-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) cargará el paquete. Por ejemplo:

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### Paso 2: registrar el tipo de aplicación
El registro del paquete de aplicación hace que la versión y el tipo de la aplicación declarados en el manifiesto de aplicación esté disponible para su uso. El sistema leerá el paquete cargado en el paso anterior, comprobará el paquete (equivalente a ejecutar [**Test-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx) localmente), procesará el contenido del paquete y copiará el paquete procesado en una ubicación del sistema interno. Ejecute el cmdlet [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx):

```powershell
Register-ServiceFabricApplicationType WordCount
```
Para ver los tipos de aplicación registrados en el clúster, ejecute el cmdlet:

```powershell
Get-ServiceFabricApplicationType
```

### Paso 3: crear la instancia de aplicación
Se puede crear instancias de una aplicación mediante cualquier versión del tipo de aplicación que se ha registrado correctamente mediante el comando [**New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx). El nombre de cada aplicación debe empezar con el esquema **fabric:** y ser únicos para cada instancia de la aplicación. El nombre y la versión del tipo de aplicación se declaran en el archivo **ApplicationManifest.xml**. Si se definieron servicios predeterminados en el manifiesto de aplicación del tipo de aplicación de destino, también se crearán en este momento.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

El comando [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) enumera todas las instancias de aplicación que se crearon correctamente junto con su estado general. El comando [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) enumera todas las instancias de servicio que se crearon correctamente dentro de una instancia de aplicación determinada. Se enumerarán los servicios predeterminados (si los hay).

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## Tarea: Actualizar una aplicación de Service Fabric

Puede actualizar una aplicación de Service Fabric implementada anteriormente. Esta tarea actualiza la aplicación WordCount que se implementó en "Tarea: Implementar una aplicación de Service Fabric". Lea el [Tutorial de actualización de aplicación de Service Fabric](service-fabric-application-upgrade.md) para obtener más información.

### Paso 1: actualice la aplicación

Realice cambios en el código en el servicio de WordCount.

Después de actualizar el código del servicio, deberá aumentar el número de versión del servicio en el archivo **ServiceManifest.xml** (ubicado en el directorio **PackageRoot** del proyecto WordCount). Encuentre el elemento **CodePackage** del manifiesto y cambie la versión del servicio a 2.0.0. Las líneas correspondientes en el archivo ServiceManifest.xml deben tener el aspecto siguiente:

```xml
<ServiceManifest Name="WordCountServicePkg" Version="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="WordCountServiceType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="2.0.0">
	  ...
```

Ahora, deberá actualizar el archivo ApplicationManifest.xml (que se encuentra en el proyecto de la aplicación WordCount bajo la solución WordCount). Actualice el elemento **ServiceManifestRef** para usar la versión 2.0.0.0 del proyecto **WordCountServicePkg**. Actualice también **ApplicationTypeVersion** de 2.0.0.0 a 1.0.0.0. Las líneas correspondientes en el archivo ApplicationManifest.xml deben tener el aspecto siguiente:

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="WordCount" ApplicationTypeVersion="2.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
...
<ServiceManifestRef ServiceManifestName="WordCountServicePkg" ServiceManifestVersion="2.0.0" />
```

Una vez realizados estos cambios, guarde los archivos y vuelva a generar el proyecto de WordCount. Ahora empaquete la aplicación actualizada haciendo clic con el botón derecho en el proyecto de WordCount y eligiendo **Empaquetar**. Así se debe crear un paquete de aplicación que se puede implementar. Su aplicación actualizada está lista para implementarse ahora.

### Paso 2: copie y registre el paquete de aplicación actualizada

La aplicación ahora está compilada, empaquetada y lista para su actualización. Si abre una ventana de PowerShell como administrador y escribe [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx), debe ver que está implementado el tipo de aplicación 1.0.0 de WordCount. Para el ejemplo de WordCount, el paquete de aplicación se encuentra en: *C:\\ServiceFabricSamples\\Services\\WordCount\\WordCount\\pkg\\Debug*.

Ahora, copie el paquete de aplicación actualizada en el almacén de imágenes de Service Fabric (donde Service Fabric almacena los paquetes de aplicación). El parámetro **ApplicationPackagePathInImageStore** informa a Service Fabric sobre dónde puede encontrar el paquete de la aplicación. El siguiente comando copiará el paquete de aplicación a **WordCountV2** en el almacén de imágenes:

```powershell
Copy-ServiceFabricApplicationPackage C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2
```

El siguiente paso es registrar la versión nueva de la aplicación con Service Fabric, que se puede realizar con el cmdlet [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx):

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

Si este comando no se completa correctamente, es posible que deba recompilar el servicio, tal como se mencionó en el paso 1.

### Paso 3: inicie la actualización
Varios parámetros de actualización, tiempos de expiración y los criterios de estado se pueden aplicar a las actualizaciones de la aplicación. Consulte los [parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md) y los documentos del [proceso de actualización](service-fabric-application-upgrade.md) para obtener más información. Para los fines de este tutorial, deje el criterio de evaluación del estado del servicio definido en el valor predeterminado (y los valores recomendados). El estado de todos los servicios y las instancias debe ser _correcto_ después de la actualización. Sin embargo, debe aumentar la **HealthCheckStableDuration** en 60 segundos (de modo que los servicios sean correctos durante al menos 20 segundos antes de que la actualización continúe con el siguiente dominio de actualización). Establezca también **UpgradeDomainTimeout** en 1200 segundos y **UpgradeTimeout** en 3000 segundos. Por último, establezca **UpgradeFailureAction** en **revertir**, el que solicita a Service Fabric revertir a la aplicación a la versión anterior si se encuentran errores durante la actualización.

Ahora puede iniciar la actualización de la aplicación con el cmdlet [**Start-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx):

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Tenga en cuenta que el nombre de la aplicación es el mismo nombre de la aplicación v1.0.0 implementada anteriormente (fabric:/WordCount). Service Fabric usa este nombre para identificar qué aplicación se está actualizando. Si los tiempos de expiración que estableció son demasiado breves, es probable que reciba un mensaje de error de tiempo de expiración que indica el problema. Consulte [Solución de problemas de actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md) o aumente los tiempos de expiración.

Puede supervisar el progreso de la actualización de la aplicación mediante el [Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md) o el cmdlet [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx):

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

En unos minutos, el cmdlet [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) debería mostrar que se actualizaron (completaron) todos los dominios de actualización.

## Tarea: Probar una aplicación de Service Fabric

Para poder escribir servicios de alta calidad, los desarrolladores deben poder inducir errores en infraestructuras no confiables para probar la estabilidad de los servicios. Service Fabric brinda a los desarrolladores la capacidad de inducir acciones de error y probar los servicios en caso de errores mediante el uso de escenarios de prueba en caso de caos y conmutación por error. Consulte [Información general sobre Testability](service-fabric-testability-overview.md) para obtener información adicional.

### Paso 1: ejecute el escenario de prueba de caos
El escenario de caos genera errores en todo el clúster de Service Fabric. El escenario comprime los errores que se ven por lo general durante meses o años en unas pocas horas. Esta combinación de errores intercalados con una elevada tasa de errores encuentra casos excepcionales que de otra manera pasan desapercibidos. El ejemplo siguiente ejecuta el escenario de prueba de caos durante 60 segundos.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### Paso 2: ejecute el escenario de prueba de conmutación por error
El escenario de prueba de conmutación por error tiene como destino una partición específica del servicio, en lugar de todo el clúster, y no afecta los demás servicios. El escenario se itera por una secuencia de errores simulados en la validación de servicios mientras se ejecuta la lógica de negocios. Un error en la validación de servicio indica un problema que necesita más investigación. La prueba de conmutación por error solo induce un error a la vez, en oposición a lo que ocurre con el escenario de prueba de caos, el que puede inducir varios errores. En el ejemplo siguiente se ejecuta la prueba de conmutación por error durante 60 minutos en el servicio fabric:/WordCount/WordCountService.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Connect-ServiceFabricCluster

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## Tarea: Quitar una aplicación de Service Fabric
Puede eliminar una instancia de una aplicación implementada, quitar el tipo de aplicación aprovisionada del clúster y quitar el paquete de aplicación del almacén de imágenes.

### Paso 1: quite una instancia de aplicación
Cuando ya no se necesita una instancia de aplicación, utilice el cmdlet [**Remove-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx) para quitarla de manera permanente. De esta manera también se quitarán automáticamente todos los servicios que pertenecen a la aplicación, quitando de forma permanente todos los estados de servicio. No se puede deshacer esta operación y no se puede recuperar el estado de la aplicación.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### Paso 2: anule el registro del tipo de aplicación
Cuando ya no se necesita una versión determinada de un tipo de aplicación, utilice el cmdlet [**Unregister-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx) para anular el registro. Anular el registro de tipos no usados liberará espacio de almacenamiento que utiliza el paquete de aplicación en el almacén de imágenes. No se puede anular el registro de un tipo de aplicación ya que no hay ninguna aplicación con instancias con él o hay actualizaciones de aplicaciones pendientes que hacen referencia a él.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### Paso 3: quite el paquete de aplicación
Una vez que se anula el registro del tipo de aplicación, es posible utilizar el cmdlet [**Remove-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx) para quitar el paquete de aplicación del almacén de imágenes.

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## Pasos siguientes
[Ciclo de vida de la aplicación de Service Fabric](service-fabric-application-lifecycle.md)

[Implementar una aplicación](service-fabric-deploy-remove-applications.md)

[Actualización de aplicaciones](service-fabric-application-upgrade.md)

[Cmdlets de Azure Service Fabric](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Cmdlets de comprobación de Azure Service Fabric](https://msdn.microsoft.com/library/azure/mt125844.aspx)

<!---HONumber=AcomDC_0128_2016-->