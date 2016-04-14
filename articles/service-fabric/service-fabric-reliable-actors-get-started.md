<properties
   pageTitle="Introducción a Reliable Actors | Microsoft Azure"
   description="Este tutorial le guiará a través de los pasos para crear, depurar e implementar un servicio HelloWorld canónico con los Service Fabric Reliable Actors."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Reliable Actors: el escenario de tutorial de HelloWorld canónico
En este artículo se explican los conceptos básicos de Reliable Actors de Azure Service Fabric, además de ofrecer orientación sobre cómo completar los pasos para crear, depurar e implementar una aplicación HelloWorld sencilla en Visual Studio.

## Instalación y configuración
Antes de comenzar, asegúrese de que el entorno de desarrollo de Service Fabric está configurado en el equipo. 
Para ello, vea instrucciones detalladas sobre [cómo configurar el entorno de desarrollo](service-fabric-get-started.md).

## Conceptos básicos
Para empezar a trabajar con Reliable Actors, solo es necesario comprender cuatro conceptos básicos:

* **Servicio de actor**. Reliable Actors se empaquetan en servicios que se pueden implementar en la infraestructura de Service Fabric. Un servicio puede hospedar uno o varios actores. A continuación, se profundizará en los detalles sobre las ventajas y desventajas de un actor frente a varios por servicio. Por ahora supongamos que debemos implementar solo un actor.
* **Interfaz de actor**. La interfaz de actor se usa para definir la interfaz pública de un actor. En la terminología del modelo de Reliable Actors, la interfaz de actor define los tipos de mensajes que el actor puede entender y procesar. Otros actores y aplicaciones de cliente usan la interfaz de actor para "enviar" (asincrónicamente) mensajes al actor. Reliable Actors pueden implementar varias interfaces. Como se verá, un actor HelloWorld puede implementar la interfaz IHelloWorld, pero también una interfaz ILogging que define diferentes mensajes o funcionalidades.
* **Registro de actor**. En el servicio Reliable Actors, el tipo de actor debe registrarse. De este modo, Service Fabric es consciente del nuevo tipo y puede utilizarlo para crear nuevos actores.
* **Clase ActorProxy**. La clase ActorProxy se usa para enlazar a un actor e invocar los métodos expuestos a través de sus interfaces. La clase ActorProxy ofrece dos funciones importantes:
	* Resuelve nombres. Es capaz de ubicar el actor en el clúster (encontrar el nodo en que se hospeda el clúster).
	* Controla errores. Puede reintentar las invocaciones de métodos y volver a determinar la ubicación del actor, por ejemplo, tras un error que requiere que el actor se reubique en otro nodo del clúster.

## Creación de un proyecto en Visual Studio
Después de instalar las herramientas de Service Fabric para Visual Studio, puede crear tipos de proyecto nuevos. Los nuevos tipos de proyecto están en la categoría **Nube** del cuadro de diálogo **Nuevo proyecto**.


![Herramientas de Service Fabric para Visual Studio: nuevo proyecto][1]

En el siguiente cuadro de diálogo puede elegir el tipo de proyecto que desea crear.

![Plantillas de proyecto de Service Fabric][5]

Para el proyecto HelloWorld, se usará el servicio Reliable Actors de Service Fabric.

Después de haber creado la solución, debe ver la estructura siguiente:

![Estructura de proyecto de Service Fabric][2]

## Bloques de creación básicos de Reliable Actors

Una solución típica de Reliable Actors se compone de tres proyectos:

* **El proyecto de aplicación (HelloWorldApplication)**. Este es el proyecto que empaqueta todos los servicios juntos para la implementación. Contiene los scripts de PowerShell y **ApplicationManifest.xml** para administrar la aplicación.

* **El proyecto de interfaz (HelloWorld.Interfaces)**. Este es el proyecto que contiene la definición de la interfaz del actor. En el proyecto HelloWorld.Interfaces puede definir las interfaces que usarán los actores de la solución.

```csharp

namespace MyActor.Interfaces
{
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;

    public interface IMyActor : IActor
    {
        Task<string> HelloWorld();
    }
}

```

* **El proyecto de servicio (HelloWorld)**. Este es el proyecto que se usa para definir el servicio de Service Fabric que hospedará al actor. Contiene código reutilizable que no debe editarse en la mayoría de casos (ServiceHost.cs), así como la implementación del actor. La implementación del actor conlleva implementar una clase que deriva de un tipo base (Actor). También implementa las interfaces que se definen en el proyecto HelloWorld.Interfaces.

```csharp

namespace MyActor
{
    using System;
    using System.Threading.Tasks;
    using Interfaces;
    using Microsoft.ServiceFabric.Actors;

    internal class MyActor : StatelessActor, IMyActor
    {
        public Task<string> HelloWorld()
        {
            throw new NotImplementedException();
        }
    }
}

```

El proyecto del servicio Reliable Actors contiene el código para crear un servicio de Service Fabric. En la definición de servicio, se registran el tipo de actor o los tipos, por lo que pueden usarse para crear una instancia de nuevos actores.

```csharp

namespace MyActor
{
    using System;
    using System.Fabric;
    using System.Threading;
    using Microsoft.ServiceFabric.Actors;

    internal static class Program
    {
        private static void Main()
        {
            try
            {
                using (FabricRuntime fabricRuntime = FabricRuntime.Create())
                {
                    fabricRuntime.RegisterActor<MyActor>();

                    Thread.Sleep(Timeout.Infinite);  // Prevents this host process from terminating so services keeps running.
                }
            }
            catch (Exception e)
            {
                ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}

```

Si comienza a partir de un proyecto nuevo en Visual Studio y tiene una única definición de actor, el registro se incluye de forma predeterminada en el código que genera Visual Studio. Si define otros actores en el servicio, deberá agregar el registro de actor mediante:

```csharp

fabricRuntime.RegisterActor<MyActor>();


```

## Depuración

Las herramientas de Service Fabric para Visual Studio admiten la depuración en el equipo local. Puede iniciar una sesión de depuración presionando la tecla F5. Visual Studio compila (si es necesario) paquetes. También implementa la aplicación en el clúster de Service Fabric local y asocia el depurador. La experiencia es similar a la depuración de una aplicación ASP.NET.

Durante el proceso de implementación, puede ver el progreso en la ventana **Resultados**.

![Ventana de resultados de depuración de Service Fabric][3]


## Pasos siguientes

- [Introducción a Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [Documentación de referencia de API de Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
- [Código de ejemplo](https://github.com/Azure/servicefabric-samples)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG

<!---HONumber=AcomDC_0121_2016-->