<properties
   pageTitle="Implementación de la aplicación de Service Fabric | Microsoft Azure"
   description="Cómo implementar y quitar aplicaciones de Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="12/10/2015"
   ms.author="seanmck"/>

# Implementar una aplicación

Una vez que un [tipo de aplicación se ha empaquetado][10], está listo para la implementación en un clúster de Azure Service Fabric. La implementación implica los tres pasos siguientes:

1. Carga del paquete de aplicación
2. Registro del tipo de aplicación
3. Creación de la instancia de aplicación

>[AZURE.NOTE]Si usa Visual Studio para implementar y depurar aplicaciones en el clúster de desarrollo local, todos los pasos que se describen a continuación se controlan automáticamente mediante un script de PowerShell que se encuentra en la carpeta Scripts del proyecto de la aplicación. En este artículo se ofrecen antecedentes acerca de qué hacen esos scripts para que pueda realizar las mismas operaciones fuera de Visual Studio.

## Cargar el paquete de la aplicación

Cargar el paquete de aplicación lo pone en una ubicación a la que pueden tener acceso los componentes internos de Service Fabric. Puede usar PowerShell para realizar la carga. Antes de ejecutar los comandos de PowerShell en este artículo, empiece siempre usando **Connect-ServiceFabricCluster** para conectarse al clúster de Service Fabric.

Supongamos que tiene una carpeta denominada *MyApplicationType* que contiene el manifiesto de aplicación necesario, los manifiestos de servicio y paquetes de código/configuración/datos. El comando **Copy-ServiceFabricApplicationPackage** cargará el paquete. Por ejemplo:

~~~
PS D:\temp> dir

    Directory: D:\temp

Mode                LastWriteTime     Length Name
----                -------------     ------ ----
d----         3/19/2015   8:11 PM            MyApplicationType

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Copy-ServiceFabricApplicationPackage MyApplicationType
Copy application package succeeded

PS D:\temp>
~~~

## Registrar el paquete de la aplicación

El registro del paquete de aplicación hace que la versión y el tipo de la aplicación declarados en el manifiesto de aplicación esté disponible para su uso. El sistema leerá el paquete cargado en el paso anterior, comprobará el paquete (equivalente a ejecutar **Test-ServiceFabricApplicationPackage** localmente), procesará el contenido del paquete y copiará el paquete procesado en una ubicación del sistema interno.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

El comando **Register-ServiceFabricApplicationType** devuelve solo después de que el sistema haya copiado correctamente el paquete de aplicación. El tiempo que tarde dependerá del contenido del paquete de la aplicación. El parámetro **- TimeoutSec** se puede usar para proporcionar un tiempo de espera más largo si es necesario. (El tiempo de espera predeterminado es de 60 segundos).

El comando **Get-ServiceFabricApplicationType** enumera todas las versiones del tipo de aplicación registradas correctamente.

## Creación de la aplicación

Se puede crear instancias de una aplicación mediante cualquier versión del tipo de aplicación que se ha registrado correctamente mediante el comando **New-ServiceFabricApplication**. El nombre de cada aplicación debe empezar con el esquema *fabric:* y ser únicos para cada instancia de la aplicación. Si se definieron servicios predeterminados en el manifiesto de aplicación del tipo de aplicación de destino, también se crearán en este momento.

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

El comando **Get-ServiceFabricApplication** enumera todas las instancias de aplicación que se crearon correctamente junto con su estado general.

El comando **ServiceFabricService Get** enumera todas las instancias de servicio que se crearon correctamente dentro de una instancia de aplicación determinada. Aquí se enumerarán los servicios predeterminados (de haberlos).

Pueden crearse varias instancias de aplicación para cualquier versión concreta de un tipo de aplicación registrado. Cada instancia de la aplicación se ejecutará de forma aislada, con su propio proceso y directorio de trabajo.

## Eliminación de una aplicación

Cuando ya no se necesita una instancia de aplicación, use el comando **Remove-ServiceFabricApplication** para quitarla de manera permanente. Este comando quitará automáticamente todos los servicios que pertenecen a la aplicación, quitando de forma permanente todos los estados de servicio. No se puede deshacer esta operación y no se puede recuperar el estado de la aplicación.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Cuando ya no se necesita una versión concreta de un tipo de aplicación, debe anularse su registro mediante el comando **Unregister-ServiceFabricApplicationType**. La eliminación de los tipos sin usar liberará espacio de almacenamiento usado por el contenido del paquete de aplicación de ese tipo en el almacén de imágenes. No se puede anular el registro de un tipo de aplicación mientras no haya ninguna aplicación con instancias con él o no haya actualizaciones de aplicaciones pendientes que hagan referencia a él.

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

<!--
## Next steps

TODO [Upgrade applications][11]
-->

## Solución de problemas

### Copy-ServiceFabricApplicationPackage pide una ImageStoreConnectionString

El entorno del SDK de Service Fabric ya debe tener configurados los valores predeterminados correctos. Pero si es necesario, ImageStoreConnectionString para todos los comandos debe coincidir con el valor que usa el clúster de Service Fabric. Puede encontrarlo en el manifiesto de clúster recuperado a través del comando **Get-ServiceFabricClusterManifest**:

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## Pasos siguientes

[Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md)

[Introducción al estado de Service Fabric](service-fabric-health-introduction.md)

[Diagnosticar y solucionar problemas de un servicio de Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelar una aplicación en Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md

<!---HONumber=AcomDC_1217_2015-->