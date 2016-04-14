<properties
   pageTitle="Pasos siguientes de la creación del proyecto de Service Fabric | Microsoft Azure"
   description="Este artículo contiene vínculos a un conjunto de tareas básicas de desarrollo para Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="12/06/2015"
   ms.author="seanmck"/>

# Su aplicación de Service Fabric y próximos pasos
La aplicación Azure Service Fabric se ha creado. En este artículo se describe la preparación del proyecto y algunos posibles pasos siguientes.

## Su aplicación
Cada nueva aplicación incluye un proyecto de aplicación. Puede haber uno o dos proyectos adicionales según el tipo de servicio elegido.

### El proyecto de aplicación
El proyecto de aplicación consiste en:

- Un conjunto de referencias a los servicios que conforman la aplicación.

- Dos perfiles de publicación (local y en la nube) que puede usar para conservar las preferencias para trabajar con entornos diferentes, tales como las preferencias relacionadas con un punto de conexión de clúster y si va a realizar implementaciones de actualización de forma predeterminada.

- Dos archivos de parámetro de aplicación (local y en la nube) que puede usar para mantener configuraciones de aplicaciones específicas del entorno, como el número de particiones para crear un servicio.

- Un script de implementación que puede usar para implementar la aplicación desde la línea de comandos o como parte de una canalización de integración continua automatizada.

- El manifiesto de aplicación que describe la aplicación.

### Reliable Services
Cuando se agrega una nueva instancia de Reliable Services, Visual Studio agrega un proyecto de servicio a la solución. El proyecto de servicio contiene una clase que se extiende desde `StatelessService` o `StatefulService`, en función del tipo elegido.

### Reliable Actors
Cuando se agrega una nueva instancia de Reliable Actors, Visual Studio agrega dos proyectos a la solución: un proyecto de actor y un proyecto de interfaz.

El proyecto de actor define el tipo de actor y su estado, para los actores con estado. El proyecto de interfaz proporciona una interfaz que pueden usar otros servicios para invocar al actor.

Tenga en cuenta que los proyectos de actor no contienen ningún comportamiento de inicio predeterminado, ya que los actores deben activarlos otros servicios. Considere la posibilidad de agregar una instancia de Reliable Services o un proyecto ASP.NET para crear e interactuar con los actores.

### ASP.NET 5
Las plantillas de ASP.NET 5 que se proporcionan para su uso en aplicaciones Service Fabric son casi idénticas a las que están disponibles para los proyectos ASP.NET 5 que se crean de forma independiente. Las únicas diferencias son:

- El proyecto contiene una carpeta **PackageRoot** para almacenar el archivo ServiceManifest junto con los paquetes de datos y configuración.

- El proyecto hace referencia a un paquete de NuGet adicional (Microsoft.ServiceFabric.AspNet.Hosting), que actúa como un puente entre el entorno de ejecución de .NET (DNX) y Service Fabric.

## Pasos siguientes
### Adición de un front-end web a la aplicación
Service Fabric proporciona integración con ASP.NET 5 para la creación de puntos de entrada basados en web en la aplicación. Vea [Adición de un front-end a la aplicación][add-web-frontend] para saber cómo crear una interfaz de REST basada en ASP.NET Web API.

### Creación de un clúster de Azure
El SDK de Service Fabric proporciona un clúster local para desarrollo y pruebas. Para crear un clúster de Azure, vea [Configuración de un clúster de Service Fabric en el Portal de Azure][create-cluster-in-portal].

### Pruebe a implementar en Azure de forma gratuita con clústeres de Party Cluster.

Si quiere probar la implementación y administración de aplicaciones de Azure sin necesidad de configurar sus propios clústeres, puede utilizar la versión de evaluación del [Servicio Party Cluster](http://aka.ms/tryservicefabric).

### Publicación de su aplicación en Azure
Puede publicar su aplicación directamente desde Visual Studio a un clúster de Azure. Para obtener información sobre cómo hacerlo, consulte [Publicación de la aplicación en Azure][publish-app-to-azure].

### Uso del explorador de Service Fabric para visualizar el clúster
El Explorador de Service Fabric ofrece una forma sencilla de visualizar el clúster, como las aplicaciones implementadas y el diseño físico. Para obtener más información, vea [Visualización del clúster mediante el Explorador de Service Fabric][visualize-with-sfx].

### Control de versiones y actualización de los servicios
Service Fabric permite el control de versiones independiente y la actualización de servicios independientes en una aplicación. Para más información, consulte [Control de versiones y actualización de los servicios][app-upgrade-tutorial].

### Configuración de la integración continua con Visual Studio Team Services
Para obtener información sobre cómo puede configurar un proceso de integración continua para su aplicación de Service Fabric, consulte [Configuración de la integración continua con Visual Studio Team Services][ci-with-vso].



<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md

<!---HONumber=AcomDC_0204_2016-->