<properties
   pageTitle="Guía de conversión de roles web y de trabajo en servicios sin estado de Service Fabric | Microsoft Azure"
   description="Esta guía compara los roles web y de trabajo de Servicios en la nube y los servicios sin estado de Service Fabric para ayudar a la migración desde Servicios en la nube a Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/29/2016"
   ms.author="vturecek"/>
 
# Guía de conversión de roles web y de trabajo a servicios sin estado de Service Fabric

Este artículo describe cómo migrar los roles web y de trabajo de los Servicios en la nube a los servicios sin estado de Service Fabric. Se trata de la ruta de migración más sencilla desde los Servicios en la nube a Service Fabric para aquellas aplicaciones cuya arquitectura global va a permanecer más o menos igual.

## Proyecto de Servicios en la nube en comparación con proyecto de la aplicación de Service Fabric
 
 Un proyecto de Servicio en la nube y un proyecto de la aplicación de Service Fabric tienen una estructura similar y ambos representan la unidad de implementación para la aplicación, es decir, cada uno de ellos define el paquete completo que se implementa para ejecutar la aplicación. Un proyecto de Servicio en la nube contiene uno o varios roles web o de trabajo. De igual forma, un proyecto de la aplicación de Service Fabric contiene uno o más servicios.

La diferencia es que el proyecto de Servicio en la nube une la implementación de la aplicación con una implementación de máquina virtual y, por tanto, contiene valores de configuración de máquina virtual, mientras que el proyecto de la aplicación de Service Fabric solo define una aplicación que se va a implementar en un conjunto de máquinas virtuales existentes en un clúster de Service Fabric. El propio clúster de Service Fabric solo se implementa una vez, a través de una plantilla ARM o a través del Portal de Azure y se pueden implementar varias aplicaciones de Service Fabric en él.

![Comparación de proyectos de Service Fabric y de Servicios en la nube][3]
 
## Rol de trabajo a servicio sin estado 

Conceptualmente, un rol de trabajo representa una carga de trabajo sin estado, lo que significa que cada instancia de la carga de trabajo es idéntica y las solicitudes se pueden enrutar a cualquier instancia en cualquier momento. No se espera que cada instancia recuerde la solicitud anterior. El estado en el que opera la carga de trabajo es administrado por un almacén de estado externo, como Almacenamiento de tablas de Azure o Azure DocumentDB. En Service Fabric, este tipo de carga de trabajo se representa mediante un servicio sin estado. La manera más sencilla de migrar un rol de trabajo a Service Fabric se puede realizar mediante la conversión de un código de rol de trabajo a un servicio sin estado.

![Rol de trabajo a servicio sin estado][4]

## Rol web a servicio sin estado

Igual que el rol de trabajo, un rol web también representa una carga de trabajo sin estado y, por tanto, conceptualmente también se puede asignar a un servicio sin estado de Service Fabric. Sin embargo, a diferencia de los roles web, Service Fabric no admite IIS. Para migrar una aplicación web desde un rol web a un servicio sin estado debe moverla primero a un marco web autohospedado que no dependa de IIS o System.Web, como ASP.NET Core 1.

****Aplicación ** | **Compatible** | **Ruta de migración**
--- | --- | ---
Formularios Web Forms ASP.NET | No | Conversión a ASP.NET Core 1 MVC
ASP.NET MVC | Con migración | Actualización a ASP.NET Core 1
ASP.NET Web API | Con migración | Uso de servidor autohospedado o ASP.NET Core 1
ASP.NET Core 1 | Sí | N/D

## API de punto de entrada y ciclo de vida

Las API de rol de trabajo y las de los servicios de Service Fabric ofrecen puntos de entrada similares:

**Punto de entrada** | **Rol de trabajo** | **Servicio de Service Fabric**.
--- | --- | ---
Processing | `Run()` | `RunAsync()`
Inicio de máquina virtual | `OnStart()` | N/D
Detención de máquina virtual | `OnStop()` | N/D
Apertura del agente de escucha para las solicitudes de cliente | N/D | <ul><li> `CreateServiceInstanceListener()` para servicios sin estado</li><li>`CreateServiceReplicaListener()` para servicios con estado</li></ul>

### Rol de trabajo

```C#

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### Servicio sin estado de Service Fabric

```C#

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Ambos tienen un reemplazo principal "Run" en el que se va a iniciar el procesamiento. Los servicios de Service Fabric combinan `Run`, `Start`, y `Stop` en un único punto de entrada, `RunAsync`. El servicio debe empezar a trabajar cuando empieza `RunAsync` y debe dejar de funcionar cuando se señala el token de cancelación del método `RunAsync`.

Hay varias diferencias claves entre el ciclo de vida y la duración de los servicios de roles de trabajo y los de Service Fabric:

 - **Ciclo de vida:** la principal diferencia es que un rol de trabajo es una máquina virtual y, por tanto, su ciclo de vida está asociado al de la máquina virtual e incluye eventos para cuando se inicia y detiene dicha máquina virtual. Un servicio de Service Fabric tiene un ciclo de vida que es independiente del ciclo de vida de la máquina virtual, por lo que no incluye eventos para cuando el equipo o máquina virtual host se inicia y se detiene, ya que no están relacionados.

 - **Duración:** una instancia de rol de trabajo se reciclará si se sale del método `Run`. No obstante, el método `RunAsync` en un servicio de Service Fabric se puede ejecutar hasta su finalización y la instancia de servicio permanecerá disponible.

Service Fabric ofrece un punto de entrada de configuración de comunicación opcional para servicios que atienden las solicitudes de cliente. Tanto RunAsync como el punto de entrada de comunicación son reemplazos opcionales en los servicios de Service Fabric. El servicio puede elegir entre solo atender a las solicitudes de cliente o solo ejecutar un bucle de procesamiento o ambas cosas, razón por la cual se permite al método RunAsync salir sin necesidad de reiniciar la instancia de servicio, ya que puede continuar atendiendo a las solicitudes de cliente.

## API de la aplicación y entorno

La API del entorno de Servicios en la nube proporciona información y funcionalidad para la instancia de máquina virtual actual, así como información acerca de otras instancias de rol de VM. Service Fabric proporciona información relacionada con su tiempo de ejecución y algo de información sobre el nodo en el que un servicio se está ejecutando.

**Tarea del entorno** | **Servicios en la nube** | **Service Fabric**
--- | --- | ---
Valores de configuración y notificación de cambios | `RoleEnvironment` | `CodePackageActivationContext`
Almacenamiento local | `RoleEnvironment` | `CodePackageActivationContext`
Información de punto de conexión | `RoleInstance` <ul><li>Instancia actual: `RoleEnvironment.CurrentRoleInstance`</li><li>otros roles e instancias: `RoleEnvironment.Roles`</li> | <ul><li>`NodeContext` para la dirección del nodo actual</li><li>`FabricClient` y `ServicePartitionResolver` para la detección del punto de conexión de servicio</li> 
Emulación de entorno | `RoleEnvironment.IsEmulated` | N/D
Evento de cambio simultáneo | `RoleEnvironment` | N/D

## Valores de configuración

Los valores de configuración de los Servicios en la nube se establecen para un rol de VM y se aplican a todas las instancias de ese rol de VM. Estos valores son pares de clave-valor establecidos en los archivos Serviceconfiguration.*.cscfg a los que se puede acceder directamente a través de RoleEnvironment. En Service Fabric, la configuración se aplica individualmente a cada servicio y a cada aplicación, en lugar de a una máquina virtual, ya que una máquina virtual puede hospedar varios servicios y aplicaciones. Un servicio se compone de tres paquetes:

 - **Código:** contiene los archivos ejecutables de servicio, los archivos binarios, los archivos DLL y cualquier otro archivo necesario para que se ejecute un servicio.
 - **Config:** todos los valores y archivos de configuración de un servicio.
 - **Datos:** los archivos de datos estáticos asociados al servicio.

Cada uno de estos paquetes puede tener versiones y actualizaciones independientes. De forma similar a los Servicios en la nube, se puede acceder a un paquete de configuración mediante programación a través de una API y los eventos están disponibles para notificar al servicio de un cambio del paquete de configuración. Se puede utilizar un archivo Settings.xml para la configuración de los pares clave-valor y el acceso mediante programación similar a . Sin embargo, a diferencia de los Servicios en la nube, un paquete de configuración de Service Fabric puede contener todos los archivos de configuración en cualquier formato, ya sea en XML, JSON, YAML o en un formato binario personalizado.


### Acceso a la configuración
#### Servicios en la nube

Se puede acceder a los valores de configuración de Serviceconfiguration.*.cscfg a través de `RoleEnvironment`. Estas opciones están disponibles globalmente para todas las instancias de rol en la misma implementación del Servicio en la nube.

```C#

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### Service Fabric

Cada servicio tiene su propio paquete de configuración individual. No hay ningún mecanismo integrado para los valores de configuración global al que puedan acceder todas las aplicaciones de un clúster. Cuando utiliza el archivo de configuración especial Settings.xml de Service Fabric dentro de un paquete de configuración, los valores en Settings.xml se pueden reemplazar en el nivel de aplicación lo que permite la configuración en dicho nivel de aplicación.

Se puede acceder a los valores de configuración de cada instancia de servicio a través de `CodePackageActivationContext` del servicio.

```C#

ConfigurationPackage configPackage = this.ServiceInitializationParameters.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### Eventos de actualización de configuración
#### Servicios en la nube

El evento `RoleEnvironment.Changed` se utiliza para notificar a todas las instancias de rol cuando se produce un cambio en el entorno, como un cambio de configuración. Esto se usa para consumir las actualizaciones de configuración sin reciclar las instancias de rol ni reiniciar un proceso de trabajo.

```C#

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### Service Fabric

Cada uno de los tres tipos de paquete de un servicio, Código, Config y Datos, tienen eventos que notifican a una instancia de servicio cuando se actualiza, agrega o quita un paquete. Un servicio puede contener varios paquetes de cada tipo. Por ejemplo, un servicio puede tener varios paquetes de configuración, cada uno individualmente con varias versiones y actualizaciones.

Estos eventos están disponibles para consumir los cambios en los paquetes de servicio sin necesidad de reiniciar la instancia de servicio.
 
```C#

this.ServiceInitializationParameters.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## Tareas de inicio

Las tareas de inicio son acciones que se realizan antes de que se inicie una aplicación. Una tarea de inicio se utiliza normalmente para ejecutar scripts de configuración con privilegios elevados. Tanto Servicios en la nube como Service Fabric admiten tareas de inicio. La principal diferencia estriba en que en los Servicios en la nube, una tarea de inicio está asociada a una máquina virtual porque forma parte de una instancia de rol, mientras que en Service Fabric una tarea de inicio se asocia a un servicio que no está asociado a ninguna máquina virtual concreta.

 | Servicios en la nube | Service Fabric
--- | --- | ---
Ubicación de configuración | ServiceDefinition.csdef | ServiceManifest.xml
Privilegios | "limitados" o "elevados" | cualquier cuenta de usuario o de equipo
Secuenciación | "simple", "segundo plano", "primer plano" | la ejecución de la tarea de inicio se debe realizar satisfactoriamente antes de iniciar el servicio.

### Servicios en la nube
En Servicios en la nube se configura un punto de entrada de inicio por rol en ServiceDefintion.csdef.

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### Service Fabric

En Service Fabric se configura un punto de entrada de inicio por servicio en ServiceManifest.xml:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## Nota acerca de un entorno de desarrollo

Los Servicios en la nube y Service Fabric se integran con Visual Studio mediante plantillas de proyecto y soporte para depuración, configuración e implementación tanto local como en Azure. Los Servicios en la nube y Service Fabric también proporcionan un entorno de desarrollo local en tiempo de ejecución. La diferencia es que mientras que el tiempo de ejecución de desarrollo del Servicio en la nube emula el entorno de Azure en el que se ejecuta, Service Fabric no utiliza un emulador, sino que usa el tiempo de ejecución de Service Fabric completo. El entorno de Service Fabric que ejecuta en el equipo de desarrollo local es el mismo entorno que se ejecuta en producción.

##Pasos siguientes

Conozca más información acerca de Reliable Services de Service Fabric y las diferencias fundamentales entre los Servicios en la nube y la arquitectura de la aplicación de Service Fabric para aprender a aprovechar el conjunto completo de características de Service Fabric.

 - [Introducción a Reliable Services de Service Fabric](./service-fabric-reliable-services-quick-start.md)

 - [Guía conceptual acerca de las diferencias entre Servicios en la nube y Service Fabric](./service-fabric-cloud-services-migration-differences.md)
 
<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png

<!---HONumber=AcomDC_0302_2016-->