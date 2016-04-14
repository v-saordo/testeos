<properties
   pageTitle="Actualización de aplicaciones de Service Fabric con PowerShell| Microsoft Azure"
   description="Este artículo le guía a través de la experiencia de implementación de una aplicación de Service Fabric, la modificación del código y la aplicación de actualizaciones con PowerShell."
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




# Actualización de aplicaciones de Service Fabric con PowerShell

El enfoque de actualización recomendado y usado con mayor frecuencia es la actualización gradual supervisada. Azure Service Fabric supervisa el estado de la aplicación que se está actualizando en función de un conjunto de directivas de mantenimiento. Cuando se hayan actualizado las aplicaciones en un dominio de actualización (UD), Service Fabric evalúa el estado de la aplicación y determina si se debe continuar con el siguiente dominio de actualización o se producirá un error en la actualización según las directivas de mantenimiento.

Se puede realizar una actualización de la aplicación supervisada mediante las API nativas o administradas, PowerShell o REST. Para obtener instrucciones sobre cómo realizar una actualización con Visual Studio, vea [Actualizar la aplicación con Visual Studio](service-fabric-application-upgrade-tutorial.md).

Con la actualización gradual supervisada de Service Fabric, el Administrador de aplicaciones puede configurar la directiva de evaluación de estado que Service Fabric usa para determinar que la aplicación es correcta. Además, el Administrador puede configurar la acción que se realizará cuando la evaluación de estado encuentre errores (por ejemplo, realizar una reversión automática). En esta sección se ofrece orientación sobre una actualización supervisada para uno de los ejemplos del SDK que PowerShell utiliza.

## Paso 1: Crear e implementar el ejemplo de objetos visuales

Estos pasos se pueden realizar descargando de la aplicación de GitHub y agregando los archivos **webgl-utils.js** y **gl-matrix-min.js** al proyecto, como se menciona en el archivo Léame del ejemplo. Sin eso, la aplicación no funcionará.

Después de agregar estos elementos al proyecto, genere y publique la aplicación haciendo clic con el botón derecho en el proyecto de aplicación, **VisualObjectsApplication** y seleccione el comando **Publicar** en el elemento de menú de Service Fabric de la siguiente forma. Vea el [Tutorial de actualización de aplicación de Service Fabric](service-fabric-application-upgrade-tutorial.md) para más información. También puede usar PowerShell para implementar la aplicación.

> [AZURE.NOTE] Antes de que se pueda usar cualquiera de los comandos de Service Fabric en PowerShell, hay que conectarse primero al clúster mediante el cmdlet `Connect-ServiceFabricCluster`. Del mismo modo, se supone que el clúster ya se ha configurado en la máquina local. Vea el artículo sobre [configuración del entorno de desarrollo de Service Fabric](service-fabric-get-started.md).

Después de compilar el proyecto en Visual Studio, puede utilizar el comando de PowerShell **Copy-ServiceFabricApplicationPackage** para copiar el paquete de aplicación en ImageStore. Se sigue este paso al registrar la aplicación en el tiempo de ejecución de Service Fabric mediante el cmdlet **Register-ServiceFabricApplicationPackage**. El último paso es iniciar una instancia de la aplicación mediante el cmdlet **New-ServiceFabricApplication**. Estos tres pasos son análogos al uso del elemento de menú **Implementar** en Visual Studio.

Ahora, puede usar el [explorador de Service Fabric para ver el clúster y la aplicación](service-fabric-visualizing-your-cluster.md). La aplicación tiene un servicio web hasta el que se puede navegar en Internet Explorer; para ello, escriba [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) en la barra de direcciones. Debería ver algunos objetos visuales flotantes desplazándose por la pantalla. Además, se puede usar **Get-ServiceFabricApplication** para comprobar el estado de la aplicación.

## Paso 2: Actualizar el ejemplo de objetos visuales

Puede que observe que con la versión que se implementó en el Paso 1, los objetos visuales no giran. Vamos a actualizar esta aplicación a una donde los objetos visuales giren.

Elija el proyecto VisualObjects.ActorService dentro de la solución VisualObjects y abra el archivo StatefulVisualObjectActor.cs. Dentro de ese archivo, vaya al método `MoveObject`, comente `this.State.Move()` y quite la marca de comentario de `this.State.Move(true)`. Este cambio hará que los objetos giren después de actualizar el servicio.

También necesitamos actualizar el archivo *ServiceManifest.xml* (debajo de PackageRoot) del proyecto **VisualObjects.ActorService**. Actualice el *CodePackage* y la versión del servicio a 2.0 y las líneas correspondientes del archivo *ServiceManifest.xml*. Puede utilizar la opción *Editar archivos de manifiesto* de Visual Studio después de hacer clic con el botón derecho en la solución para realizar los cambios del archivo de manifiesto.


Una vez realizados los cambios, el manifiesto debe ser similar al siguiente (las partes resaltadas muestran los cambios):

```xml
<ServiceManifestName="VisualObjects.ActorService"Version="2.0"xmlns="http://schemas.microsoft.com/2011/01/fabric"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code"Version="2.0">
```

Ahora, necesitamos actualizar el archivo *ApplicationManifest.xml* (que se encuentra en el proyecto **VisualObjects** debajo de la solución **VisualObjects**) para usar la versión 2.0 del proyecto **VisualObjects.ActorService** y actualizar también la versión de la aplicación de 1.0.0.0 a 2.0.0.0. Ahora, las líneas correspondientes del archivo *ApplicationManifest.xml* deben tener el siguiente aspecto:

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"ApplicationTypeName="VisualObjects"ApplicationTypeVersion="2.0.0.0"xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService"ServiceManifestVersion="2.0" />
```


Ahora, compile el proyecto; para ello, seleccione solamente el proyecto **ActorService** y luego haga clic con el botón derecho y seleccione **Compilar** en Visual Studio. (Si selecciona **Volver a generar todo**, es posible que tenga que actualizar las versiones de otros proyectos y también en su *ServiceManifest.xml* y en *ApplicationManifest.xml*, ya que el código habría cambiado). A continuación, vamos a empaquetar la aplicación actualizada y, para ello, haga clic con el botón derecho en el proyecto ***VisualObjectsApplication***, seleccione el menú de Service Fabric y elija **Paquete**. Así se debe crear un paquete de aplicación que se puede implementar. Su aplicación actualizada está lista para implementarse ahora.


## Paso 3: Decidir sobre las directivas de mantenimiento y parámetros de actualización

Familiarícese con los [parámetros de actualización de la aplicación](service-fabric-application-upgrade-parameters.md) y el [proceso de actualización](service-fabric-application-upgrade.md) para obtener una buena comprensión de los distintos parámetros de actualización, los tiempos de expiración y el criterio de estado aplicados. En este tutorial, se dejará el criterio de evaluación de estado de servicio para que sea el predeterminado (y los valores recomendados), lo que significa que todos los servicios e instancias deben ser _correctos_ tras la actualización.

Sin embargo, vamos a aumentar *HealthCheckStableDuration* en 60 segundos (de modo que los servicios sean correctos durante al menos 20 segundos antes de que la actualización continúe con el siguiente dominio de actualización). Vamos a configurar también *UpgradeDomainTimeout* para que sea 1200 segundos y *UpgradeTimeout* para que sea 3000 segundos.

Por último, también estableceremos *UpgradeFailureAction* para reversión, solicitando que Service Fabric revierta la aplicación a la versión anterior si encuentra errores durante la actualización. Así pues, los parámetros de actualización que especificaremos al iniciar la actualización (en el paso 4) serán los siguientes:

FailureAction = Rollback

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000


## Paso 4: Preparar la aplicación para la actualización

Ahora, la aplicación se ha creado y está lista para actualizarse. Si abre una ventana de PowerShell como administrador y escribe **Get-ServiceFabricApplication**, debe indicarle que es el tipo de aplicación 1.0.0.0 de **VisualObjects** la que se ha implementado.

El paquete de aplicación se almacena en la siguiente ruta de acceso relativa donde descomprimió el SDK de Service Fabric: *Samples\\Services\\Stateful\\VisualObjects\\VisualObjects\\obj\\x64\\Debug*. Debería encontrará una carpeta "Paquete" en ese directorio, que es donde se almacena el paquete de aplicación. Compruebe las marcas de tiempo para asegurarse de que es la última compilación (puede que necesite además modificar las rutas de acceso adecuadamente).

Ahora vamos a copiar el paquete de la aplicación actualizada a la ImageStore de Service Fabric (donde se almacenan los paquetes de la aplicación por Service Fabric). El parámetro *ApplicationPackagePathInImageStore* informa a Service Fabric sobre dónde puede encontrar el paquete de la aplicación. Hemos colocado la aplicación actualizada en "VisualObjects\_V2" con el siguiente comando (puede que tenga que modificar rutas de acceso de nuevo de manera adecuada).

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

El siguiente paso es registrar esta aplicación con Service Fabric, lo que se puede realizar mediante el comando siguiente:

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

Si el comando anterior no es correcto, es probable que necesite una recompilación de todos los servicios. Como se mencionó en el Paso 2, puede que tenga que actualizar además la versión de WebService.

## Paso 5: Iniciar la actualización de la aplicación

Ahora, todos estamos preparados para iniciar la actualización de la aplicación mediante el siguiente comando:

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


Tenga en cuenta que el nombre de aplicación es el mismo que se ha descrito en el archivo *ApplicationManifest.xml*. Service Fabric usa este nombre para identificar qué aplicación se está actualizando. Si establece los tiempos de expiración para que sean demasiado breves, puede que reciba un mensaje de error que indique el problema. Consulte la sección de solución de problemas o aumente los tiempos de expiración.

Ahora, a medida que continúa la actualización de la aplicación, puede supervisarla mediante el Explorador de Service Fabric o mediante el siguiente comando de PowerShell: **Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects**.

En unos minutos, el estado obtenido con el comando de PowerShell anterior debe indicar que todos los dominios de actualización se han actualizado (completado). Y debería descubrir que los objetos visuales de la ventana del explorador ahora habrán empezado a girar.

Puede que desee intentar cambiar las versiones y pasar de la versión 2 a la versión 3 como ejercicio, o incluso de la versión 2 a la versión 1 (sí, puede actualizar de v2 a v1). Juegue con los tiempos de espera y las directivas de mantenimiento para familiarizarse. Cuando implemente en un clúster de Azure, los parámetros usados serán diferentes de los utilizados al implementar en un clúster local; es conveniente establecer los tiempos de expiración de manera conservadora.


## Pasos siguientes

El procedimiento de [actualización de aplicaciones usando Visual Studio](service-fabric-application-upgrade-tutorial.md) ofrece información para actualizar una aplicación mediante Visual Studio.

Puede controlar cómo se actualiza una aplicación usando [parámetros de actualización](service-fabric-application-upgrade-parameters.md).

Consiga que sus actualizaciones de aplicaciones sean compatibles aprendiendo a usar la [serialización de datos](service-fabric-application-upgrade-data-serialization.md).

Aprenda a usar funcionalidades avanzadas para actualizar una aplicación. Para ello, consulte los [temas avanzados](service-fabric-application-upgrade-advanced.md).

Solucione problemas habituales en las actualizaciones de aplicaciones consultando los pasos que figuran en [Solución de problemas de las actualizaciones de aplicaciones](service-fabric-application-upgrade-troubleshooting.md).

<!---HONumber=AcomDC_0211_2016-->