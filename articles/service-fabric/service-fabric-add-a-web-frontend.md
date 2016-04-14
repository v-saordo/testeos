<properties
   pageTitle="Creación de un front-end web para la aplicación | Microsoft Azure"
   description="Exponga la aplicación de Service Fabric a la Web usando un proyecto API web de ASP.NET 5 y la comunicación entre servicios a través de ServiceProxy."
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
   ms.date="01/19/2016"
   ms.author="seanmck"/>


# Creación de un front-end de servicio web para una aplicación

De forma predeterminada, los servicios de Azure Service Fabric no proporcionan una interfaz pública para la Web. Para exponer la funcionalidad de su aplicación a los clientes HTTP, tendrá que crear un proyecto web para que actúe como punto de entrada y, a continuación, comunicarse desde allí con los servicios individuales.

Este tutorial le guiará por el proceso de agregar un front-end de API web de ASP.NET 5 a una aplicación que ya incluye un servicio de confianza basado en la plantilla de proyecto de servicio con estado. Si aún no lo ha hecho, considere la posibilidad de realizar el tutorial [Creación de su primera aplicación en Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) antes de iniciar este.


## Incorporación de un servicio ASP.NET 5 a una aplicación

ASP.NET 5 es un marco de desarrollo web ligero multiplataforma, que puede usar para crear modernas interfaces de usuario web y API web. Vamos a agregar un proyecto API web de ASP.NET a la aplicación existente.

1. En el Explorador de soluciones, haga clic con el botón derecho en **Servicios**, en el proyecto de aplicación y seleccione **Agregar Service Fabric**.

	![Incorporación de un nuevo servicio a una aplicación existente][vs-add-new-service]

2. En la página **Crear un servicio**, elija **ASP.NET 5** y asígnele un nombre.

	![Selección del servicio web ASP.NET en el cuadro de diálogo de nuevo servicio][vs-new-service-dialog]

3. En la página siguiente se proporciona un conjunto de plantillas de proyecto ASP.NET 5. Tenga en cuenta que se trata de las mismas plantillas que vería si creara un proyecto ASP.NET 5 fuera de una aplicación de Service Fabric. En este tutorial, elegiremos **API web**. Sin embargo, puede aplicar los mismos conceptos a la creación de una aplicación web completa.

	![Elección del tipo de proyecto ASP.NET][vs-new-aspnet-project-dialog]

    Una vez creado el proyecto API web, tendrá dos servicios en la aplicación. Mientras continúa compilando la aplicación, agregará más servicios exactamente de la misma forma. Cada uno puede tener versiones y actualizaciones independientes.

>[AZURE.NOTE] A partir del lanzamiento de la versión preliminar pública de noviembre de Service Fabric, existen problemas conocidos con las rutas de acceso largas cuando se trabaja con proyectos ASP.NET. Al crear estos tipos de proyecto es mejor elegir nombres cortos para los tipos de aplicaciones y servicios, así como para los nombres de paquete de código y configuración, a fin de evitar problemas.

## Ejecución de la aplicación

Para tener una idea de lo que hemos hecho, vamos a implementar la nueva aplicación y a observar el comportamiento predeterminado que proporciona la plantilla API web de ASP.NET 5.

1. Presione F5 en Visual Studio para depurar la aplicación.

2. Cuando se complete la implementación, Visual Studio iniciará el explorador hacia la raíz del servicio de API web de ASP.NET, algo como http://localhost:33003. El número de puerto se asigna aleatoriamente y puede ser diferente en su máquina. La plantilla API web de ASP.NET 5 no proporciona comportamiento predeterminado para la raíz, por lo que se producirá un error en el explorador.

3. Agregue `/api/values` a la ubicación en el explorador. Esta acción invoca el método `Get` en ValuesController en la plantilla de API web. Se devolverá la respuesta predeterminada proporcionada por la plantilla, una matriz JSON que contiene dos cadenas:

    ![Valores predeterminados devueltos desde la plantilla API web de ASP.NET 5][browser-aspnet-template-values]

    Al final del tutorial, reemplazaremos estos valores predeterminados con el valor más reciente del contador de nuestro servicio con estado.


## Conexión de los servicios

Service Fabric proporciona una flexibilidad completa en el modo de comunicación con los servicios confiables. En una sola aplicación, podría tener servicios que son accesibles a través de TCP, servicios que son accesibles a través de una API de REST de HTTP y servicios que son accesibles a través de sockets web. Para más información sobre las opciones disponibles y sus inconvenientes, consulte [Comunicación con los servicios](service-fabric-connect-and-communicate-with-services.md). En este tutorial, seguiremos uno de los enfoques más sencillos y usaremos las clases `ServiceProxy`/`ServiceRemotingListener` que se proporcionan en el SDK.

En el enfoque `ServiceProxy` (modelado sobre las llamadas a procedimientos remotos o RPC), se define una interfaz que actúa como el contrato público del servicio. Luego, se usa esa interfaz para generar una clase de proxy para interactuar con el servicio.


### Creación de la interfaz

Comenzaremos por crear la interfaz para que actúe como contrato entre el servicio con estado y sus clientes, incluido el proyecto ASP.NET 5.

1. En el Explorador de soluciones, haga clic con el botón derecho en la solución y seleccione **Agregar** > **Nuevo proyecto**.

2. Elija la entrada **Visual C#** en el panel de navegación izquierdo y, a continuación, seleccione la plantilla **Biblioteca de clases**. Asegúrese de que la versión de .NET Framework está establecida en **4.5.1**.

    ![Creación de un proyecto de interfaz para el servicio con estado][vs-add-class-library-project]

3. Para que `ServiceProxy` pueda utilizar una interfaz, debe derivarse de la interfaz IService. Esta interfaz se incluye en uno de los paquetes de NuGet de Service Fabric. Para agregar el paquete, haga clic con el botón derecho en el nuevo proyecto de biblioteca de clases y elija **Administrar paquetes de NuGet**.

4. Asegúrese de que la casilla **Incluir versión previa** está seleccionada y luego busque el paquete **Microsoft.ServiceFabric.Services** e instálelo.

    ![Incorporación del paquete de NuGet de servicios][vs-services-nuget-package]

5. En la biblioteca de clases, cree una interfaz con un único método, `GetCountAsync`, y amplíe la interfaz de IService.

    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;

        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```


### Implementación de la interfaz en el servicio con estado

Ahora que hemos definido la interfaz, tenemos que implementarla en el servicio con estado.

1. En el servicio con estado, agregue una referencia al proyecto de biblioteca de clases que contiene la interfaz.

    ![Incorporación de una referencia al proyecto de biblioteca de clases en el servicio con estado][vs-add-class-library-reference]

2. Busque la clase que hereda de `StatefulService`, como `MyStatefulService`, y amplíela para implementar la interfaz `ICounter`.

    ```c#
    using MyStatefulService.Interfaces;

    ...

    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. Ahora implemente el método único que se define en la interfaz `ICounter`, `GetCountAsync`.

    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```


### Exposición del servicio con estado mediante ServiceRemotingListener

Con la interfaz `ICounter` implementada, el paso final para habilitar el servicio con estado al que se pueda llamar desde otros servicios es abrir un canal de comunicación. Para los servicios con estado, Service Fabric proporciona un método reemplazable denominado `CreateServiceReplicaListeners`. Con este método, puede especificar uno o varios agentes de escucha de comunicación, según el tipo de comunicación que desee habilitar para el servicio.

>[AZURE.NOTE] El método equivalente para abrir un canal de comunicación a los servicios sin estado se llama `CreateServiceInstanceListeners`.

En este caso, reemplazaremos el método `CreateServiceReplicaListeners` existente y ofreceremos una instancia de `ServiceRemotingListener`, que crea un punto de conexión RPC al que se puede llamar desde los clientes usando `ServiceProxy`.

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (initParams) =>
                new ServiceRemotingListener<ICounter>(initParams, this))
    };
}
```


### Uso de la clase ServiceProxy para interactuar con el servicio

Nuestro servicio con estado está ahora preparado para recibir tráfico de otros servicios. Así que todo lo que queda es agregar el código para comunicarse con él desde el servicio web ASP.NET.

1. En el proyecto ASP.NET, agregue una referencia a la biblioteca de clases que contiene la interfaz `ICounter`.

2. Agregue el paquete Microsoft.ServiceFabric.Services al proyecto ASP.NET, de la misma forma que hizo anteriormente para el proyecto de biblioteca de clases. Esto le proporcionará la clase `ServiceProxy`.

3. En la carpeta **Controladores**, abra la clase `ValuesController`. Tenga en cuenta que el método `Get` actualmente solo devuelve una matriz de cadenas codificadas de forma rígida de "value1" y "value2", que coincide con lo que vimos anteriormente en el explorador. Reemplace esta implementación con el siguiente código:

    ```c#
    using MyStatefulService.Interfaces;
    using Microsoft.ServiceFabric.Services.Remoting.Client;

    ...

    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(0, new Uri("fabric:/MyApp/MyStatefulService"));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    La primera línea de código es la clave. Para crear el proxy ICounter para el servicio con estado, tiene que proporcionar dos fragmentos de información: un identificador de partición y el nombre del servicio.

    Puede usar particiones para escalar servicios con estado dividiendo su estado en depósitos distintos en función de una clave que usted define, como puede ser un identificador de cliente o un código postal. En nuestra sencilla aplicación, el servicio con estado tiene solo una partición, por lo que no importa la clave. Cualquier clave que proporcione llevará a la misma partición. Consulte [Partición de Service Fabric Reliable Services](service-fabric-concepts-partitioning.md) para obtener más información sobre los servicios de partición.

    El nombre del servicio es un URI del tejido del formulario:/&lt;application\_name&gt;/&lt;service\_name&gt;

    Con estas dos piezas de información, Service Fabric puede identificar de forma exclusiva el equipo al que se deben enviar las solicitudes. La clase `ServiceProxy` también controlará sin ningún problema los casos en los que se produce un error en la máquina que hospeda la partición de servicio con estado, y es necesario promover a otro equipo para ocupar su lugar. Esta abstracción simplifica considerablemente la escritura del código de cliente para tratar con otros servicios.

    Una vez que se tiene el proxy, simplemente se invoca el método `GetCountAsync` y se devuelve su resultado.

4. Presione F5 de nuevo para ejecutar la aplicación modificada. Como antes, Visual Studio iniciará automáticamente el explorador en la raíz del proyecto web. Agregue la ruta de acceso "api/values", debería ver el valor actual del contador devuelto.

    ![El valor del contador con estado aparece en el explorador][browser-aspnet-counter-value]

    Actualice el explorador periódicamente para ver el valor actualizado del contador.


## ¿Y los actores?

Este tutorial se centra en la incorporación de un front-end web que se comunica con un servicio con estado. Sin embargo, puede seguir un modelo muy parecido a hablar con los actores. De hecho, es algo más sencillo.

Cuando se crea un proyecto de actor, Visual Studio genera automáticamente un proyecto de interfaz. Puede usar esta interfaz para generar un proxy de actor en el proyecto web para comunicarse con el actor. El canal de comunicación se proporciona automáticamente. Así que no es necesario hacer nada equivalente a establecer una instancia de `ServiceRemotingListener` como hizo para el servicio con estado en este tutorial.

## Cómo funcionan los servicios web en el clúster local

En general, puede implementar exactamente la misma aplicación de Service Fabric en un clúster de varias máquinas que en el clúster local y tener la completa seguridad de que funcionará como se espera. El motivo es que el clúster local es simplemente una configuración de cinco nodos que se consolida en una sola máquina.

En cuanto a los servicios web, de todas formas, existe un matiz clave. Cuando el clúster se encuentra detrás de un equilibrador de carga, como sucede en Azure, tiene que asegurarse de que los servicios web se implementan en todos los equipos, ya que el equilibrador de carga simplemente realizará un enrutamiento del tráfico round robin en todos los equipos. Para ello, establezca `InstanceCount` para el servicio en el valor especial de "-1".

Por el contrario, cuando se ejecuta un servicio web localmente, debe asegurarse de que solo una instancia del servicio se esté ejecutando. En caso contrario, se encontrará con problemas debido a que hay varios procesos que escuchan en la misma ruta de acceso y puerto. Como resultado, el recuento de instancias de servicio web debe establecerse en "1" para implementaciones locales.

Para obtener información sobre cómo configurar valores diferentes para diferentes entorno, consulte [Administración de los parámetros de la aplicación en varios entornos](service-fabric-manage-multiple-environment-app-configuration.md).

## Pasos siguientes

- [Creación de un clúster en Azure para implementar la aplicación en la nube](service-fabric-cluster-creation-via-portal.md)
- [Más información sobre la comunicación con los servicios](service-fabric-connect-and-communicate-with-services.md)
- [Más información acerca de la partición de servicios con estado](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png

<!---HONumber=AcomDC_0128_2016-->