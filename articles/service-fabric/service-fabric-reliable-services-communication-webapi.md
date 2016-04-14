<properties
   pageTitle="Comunicación de servicio con la API web de ASP.NET | Microsoft Azure"
   description="Aprenda a implementar la comunicación del servicio paso a paso mediante la API web de ASP.NET con autohospedaje OWIN en la API de Reliable Services."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Introducción a los servicios de la API web de Microsoft Azure Service Fabric con autohospedaje OWIN

Azure Service Fabric delega en usted la responsabilidad de decidir cómo desea que se comuniquen sus servicios con los usuarios y entre sí. Este tutorial se centra en la implementación de la comunicación del servicio mediante la API web de ASP.NET con autohospedaje OWIN (Open Web Interface para .NET) en la API de Reliable Services de Service Fabric. Profundizaremos más en la API de comunicación acoplable de Reliable Services. También vamos a usar la API web en un ejemplo paso a paso para mostrar cómo configurar un agente de escucha de comunicación personalizado.


## Introducción a las API web de Service Fabric

La API web de ASP.NET es un marco popular y potente que permite crear API HTTP basadas en .NET Framework. Si no está familiarizado con el marco de trabajo, consulte [Introducción a ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) para obtener más información.

La API web de Service Fabric es la misma API web de ASP.NET que conoce y ama. La diferencia radica en cómo se *hospeda* la aplicación de la API web. No va a utilizar Microsoft Internet Information Services. Para entender mejor la diferencia, dividámosla en dos partes:

 1. La aplicación de la API web (incluidos los controladores y modelos)
 2. El host (el servidor web, normalmente IIS)

La aplicación de la API web no cambia. No difiere de las aplicaciones API web que haya realizado en el pasado y deberá poder mover simplemente la mayor parte del código de la aplicación. Pero si ha utilizado IIS para hospedarla, donde hospeda la aplicación puede diferir ligeramente del hospedaje que suele utilizar. Antes de profundizar en el hospedaje, vamos a hablar sobre algo más familiar, que es la aplicación de la API web.


## Creación de la aplicación

Empiece por crear una nueva aplicación Service Fabric, con un servicio único sin estado en Visual Studio 2015:

![Crear una nueva aplicación de Service Fabric](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

![Crear un servicio sin estado único](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

Esto nos proporciona un servicio sin estado vacío en el que se va a hospedar la aplicación de la API web. Vamos a configurar la aplicación desde el principio para ver cómo se ensambla.

El primer paso es extraer algunos paquetes NuGet para la API web. El paquete que queremos utilizar es Microsoft.AspNet.WebApi.OwinSelfHost. Este paquete incluye todos los paquetes de la API web necesarios y los paquetes *host*. Esto será importante más adelante.

![Creación de la API web con el Administrador de paquetes NuGet](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Después de haber instalado los paquetes, podremos empezar a crear la estructura de proyecto de API web básica. Si ha utilizado una API web, la estructura del proyecto le resultará muy familiar. Comience por crear los directorios de la API web básicos:

 + App\_Start
 + Controladores
 + Modelos

Agregue las clases básicas de configuración de API web en el directorio App\_Start. Por ahora simplemente agregaremos una configuración de formateador de tipo de soporte físico vacío:

**FormatterConfig.cs**

```csharp

namespace WebApiService
{
    using System.Net.Http.Formatting;

    public static class FormatterConfig
    {
        public static void ConfigureFormatters(MediaTypeFormatterCollection formatters)
        {
        }
    }
}

```

Agregue un controlador predeterminado en el directorio de controladores:

**DefaultController.cs**

```csharp

namespace WebApiService.Controllers
{
    using System.Collections.Generic;
    using System.Web.Http;

    [RoutePrefix("api")]
    public class DefaultController : ApiController
    {
        // GET api/values
        [Route("values")]
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5
        [Route("values/{id}")]
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values
        [Route("values")]
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5
        [Route("values/{id}")]
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5
        [Route("values/{id}")]
        public void Delete(int id)
        {
        }
    }
}

```

Finalmente, agregue una clase de inicio en la raíz del proyecto para registrar el enrutamiento, los formateadores y cualquier otro programa de instalación de configuración. Aquí también es donde la API web se conecta al *host*, que se revisará de nuevo más tarde. Cuando configura la clase de inicio, cree una interfaz denominada IOwinAppBuilder para la clase de inicio que defina el método de configuración. Aunque técnicamente no es necesario para que la API web funcione, le permitirá utilizar la clase de inicio con mayor flexibilidad más adelante.

**Startup.cs**

```csharp

namespace WebApiService
{
    using Owin;
    using System.Web.Http;

    public class Startup : IOwinAppBuilder
    {
        public void Configuration(IAppBuilder appBuilder)
        {
            HttpConfiguration config = new HttpConfiguration();

            config.MapHttpAttributeRoutes();
            FormatterConfig.ConfigureFormatters(config.Formatters);

            appBuilder.UseWebApi(config);
        }
    }
}

```

**IOwinAppBuilder.cs**

```csharp

namespace WebApiService
{
    using Owin;

    public interface IOwinAppBuilder
    {
        void Configuration(IAppBuilder appBuilder);
    }
}

```

Eso es todo en lo relacionado con la parte de la aplicación. En este momento, hemos establecido el diseño básico de proyecto de la API web. Hasta ahora, el aspecto no debe diferir mucho de los proyectos de API web que ha creado en el pasado o de la plantilla básica de API web. La lógica empresarial va en los controladores y los modelos como de costumbre.

¿Ahora qué hacemos con el hospedaje para poder ejecutarlo en realidad?


## Hospedaje de servicio

En Service Fabric, el servicio se ejecuta en un *proceso de host de servicio* (un archivo ejecutable que ejecuta el código del servicio). Al escribir un servicio con la API de Reliable Services, el proyecto de servicio simplemente se compila en un archivo ejecutable que registra el tipo de servicio y ejecuta el código. Esto ocurre en la mayoría de los casos al escribir un servicio de Service Fabric en .NET. Si abre Program.cs en el proyecto de servicio sin estado, verá:

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterServiceType("WebApiServiceType", typeof(Service));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e);
            throw;
        }
    }
}

```

Si es sospechosamente similar al punto de entrada a una aplicación de consola, eso es porque lo es.

Los detalles adicionales sobre el proceso de host de servicio y el registro del servicio están fuera del ámbito de este artículo. Pero es importante saber por ahora que *el código del servicio se ejecuta en su propio proceso*.

## Autohospedaje de la API web con un host OWIN

Dado que el código de aplicación de la API web se hospeda en su propio proceso, ¿cómo se conecta a un servidor web? Escriba [OWIN](http://owin.org/). OWIN es simplemente un contrato entre las aplicaciones web .NET y servidores web. Tradicionalmente, cuando se usa ASP.NET (hasta MVC 5), la aplicación web se acoplaba estrechamente con IIS a través de System.Web. Sin embargo, la API web implementa OWIN, lo que le permite escribir una aplicación web que se separa del servidor web que la hospeda. Por este motivo, puede usar un servidor web OWIN de *autohospedaje* que puede iniciar en su propio proceso. Encaja perfectamente con el modelo de hospedaje de Service Fabric que acabamos de describir.

En este artículo, usaremos Katana como el host OWIN para la aplicación API web. Katana es una implementación de host OWIN de código abierto.

> [AZURE.NOTE] Para obtener más información sobre Katana, vaya a la [sitio de Katana](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Para obtener una introducción rápida sobre cómo usar Katana para el autohospedaje de la API web, consulte [Use OWIN to Self-Host ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api) (Uso de OWIN para autohospedaje de la API web de ASP.NET 2).


## Configurar el servidor web

La API de Reliable Services ofrece un punto de entrada de comunicación en el que puede conectar pilas de comunicación para permitir a los usuarios y a los clientes conectarse al servicio:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

El servidor web (y las demás pilas de comunicación que utilice en el futuro, como WebSockets) debe utilizar la interfaz ICommunicationListener para integrarse correctamente en el sistema. Las razones para ello serán más evidentes en los pasos siguientes.

En primer lugar, cree una clase denominada OwinCommunicationListener que implemente ICommunicationListener:

**OwinCommunicationListener.cs**

```csharp

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

    public class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}

```

La interfaz de ICommunicationListener ofrece tres métodos para administrar un agente de escucha de comunicación para el servicio:

 - *OpenAsync*. Empezar a escuchar las solicitudes.
 - *CloseAsync*. Dejar de escuchar las solicitudes, finalizar las solicitudes en curso y cerrar correctamente.
 - *Abort*. Cancelar todo y detener inmediatamente.

Para empezar, agregue los miembros de clase privada para un prefijo de ruta de acceso de dirección URL y la clase de Inicio creada anteriormente. Estos se inicializarán a través del constructor y se usarán más adelante cuando configure la dirección URL de escucha. Asimismo, agregue miembros de clase privada para guardar la dirección de escucha que se crea durante la inicialización y para guardar el identificador del servidor que se crea cuando se inicia el servidor.

```csharp

public class OwinCommunicationListener : ICommunicationListener
{
    private readonly IOwinAppBuilder startup;
    private readonly string appRoot;
    private IDisposable serverHandle;
    private string listeningAddress;
    private readonly ServiceInitializationParameters serviceInitializationParameters;

    public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
    {
        this.startup = startup;
        this.appRoot = appRoot;
        this.serviceInitializationParameters = serviceInitializationParameters;
    }        

    ...

```

## Implementación de OpenAsync

Para configurar el servidor web, necesitamos un par de datos:

 - *Prefijo de ruta de acceso de dirección URL*. Aunque es opcional, es aconsejable configurar esto ahora para poder hospedar de forma segura varios servicios web en la aplicación.
 - *Un puerto*.

Antes de obtener un puerto para el servidor web, es importante comprender que Service Fabric proporciona una capa de aplicación que actúa como un búfer entre la aplicación y el sistema operativo subyacente en el que se ejecuta. Como tal, Service Fabric proporciona una manera de configurar *extremos* para los servicios. Service Fabric garantiza que los puntos de conexión están disponibles para que el servicio los utilice. De este modo, no tiene que configurarlos por su cuenta en el entorno de sistema operativo subyacente. Puede hospedar fácilmente su aplicación de Service Fabric en diferentes entornos sin tener que realizar cambios en la aplicación. (Por ejemplo, puede hospedar la misma aplicación en Azure o en su propio centro de datos).

Configurar un extremo HTTP en PackageRoot\\ServiceManifest.xml:

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="80" />
    </Endpoints>
</Resources>

```

Este paso es importante porque el proceso de host de servicio se ejecuta con credenciales restringidas (servicio de red en Windows). Esto significa que el servicio no tendrá acceso para configurar un punto de conexión HTTP por sí mismo. Mediante la configuración del punto de conexión, Service Fabric sabe configurar la lista de control de acceso (ACL) adecuada para la dirección URL que el servicio escuchará. Service Fabric también proporciona un lugar estándar para configurar puntos de conexión.


De nuevo en OwinCommunicationListener.cs, puede iniciar la implementación de OpenAsync. Aquí es donde inicia el servidor web. En primer lugar, obtenga la información de punto de conexión y cree la dirección URL en la que escucha el servicio.

```csharp

public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = String.Format(
        CultureInfo.InvariantCulture,
        "http://+:{0}/{1}",
        port,
        String.IsNullOrWhiteSpace(this.appRoot)
            ? String.Empty
            : this.appRoot.TrimEnd('/') + '/');
    ...

```

Tenga en cuenta que "http://+" se utiliza aquí. Esto le permite asegurarse de que el servidor web está escuchando todas las direcciones disponibles, incluyendo localhost, FQDN y la dirección IP del equipo.

La implementación de OpenAsync es una de las razones más importantes por las que el servidor web (o cualquier pila de comunicación) se implementa como un ICommunicationListener en lugar de simplemente abrirlo directamente desde `RunAsync()` en el servicio. El valor devuelto de OpenAsync es la dirección que está escuchando el servidor web. Cuando se devuelve esta dirección al sistema, registra la dirección con el servicio. Service Fabric proporciona una API que permite a los clientes y otros servicios pedir esta dirección por nombre de servicio. Esto es importante porque la dirección del servicio no es estática. Los servicios se mueven en el clúster para fines de disponibilidad y equilibrio de recursos. Este es el mecanismo que permite a los clientes resolver la dirección de escucha de un servicio.

Con eso en mente, OpenAsync inicia el servidor web y devuelve la dirección en que está escuchando. Tenga en cuenta que realiza escuchas en "http://+", pero antes de que OpenAsync devuelva la dirección, el "+" se sustituye por la dirección IP o FQDN del nodo en el que está actualmente. La dirección que este método devuelve es la que se registra con el sistema. También es lo que los clientes y otros servicios ven cuando solicitan la dirección de un servicio. Para que los clientes se conecten correctamente a ella, necesitan un IP o FQDN real en la dirección.

```csharp

    ...

    this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
    string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

    return Task.FromResult(publishAddress);
}

```

Tenga en cuenta que esto hace referencia a la clase Inicio que se pasó a OwinCommunicationListener en el constructor. El servidor web utiliza esta instancia de inicio para arrancar la aplicación de la API web.

La línea `ServiceEventSource.Current.Message()` aparecerá en la ventana de eventos de diagnóstico más adelante al ejecutar la aplicación para confirmar que el servidor web se ha iniciado correctamente.

## Implementación de CloseAsync y Abort

Por último, implemente CloseAsync y Abort para detener el servidor web. Es posible detener el servidor web eliminando el identificador de servidor que se creó durante la OpenAsync.

```csharp

public Task CloseAsync(CancellationToken cancellationToken)
{
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.serverHandle != null)
    {
        try
        {
            this.serverHandle.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}

```

En este ejemplo de implementación, CloseAsync y Abort simplemente detendrían el servidor web. Puede optar por realizar un apagado coordinado del servidor web de manera más correcta en CloseAsync. Por ejemplo, el apagado podría esperar a que finalicen las solicitudes en proceso antes de la devolución.

## Iniciar el servidor web

Ahora está listo para crear y devolver una instancia de OwinCommunicationListener para iniciar el servidor web. De nuevo en la clase de servicio (Service.cs), reemplace el método `CreateServiceInstanceListeners()`:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new[]
    {
        new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
    };
}

```

Aquí es donde la *aplicación* de la API web y el *host* de OWIN se encuentran finalmente. Al host (OwinCommunicationListener) se le asigna una instancia de la *aplicación* (la API web a través del inicio). Service Fabric administra su ciclo de vida. Normalmente este mismo patrón puede ser seguido de cualquier pila de comunicación.

## Colocación de todo junto

En este ejemplo, no necesita hacer nada en el método `RunAsync()`, para que se pueda quitar la invalidación fácilmente.

La implementación del servicio final debe ser muy sencilla. Solo es necesario crear el agente de escucha de comunicación:

```csharp

namespace WebApiService
{
    using System.Collections.Generic;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;
    using Microsoft.ServiceFabric.Services.Runtime;

    public class WebApiService : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            return new[]
            {
                new ServiceInstanceListener(initParams => new OwinCommunicationListener("webapp", new Startup(), initParams))
            };
        }
    }
}

```

La clase `OwinCommunicationListener` completa:

```csharp

namespace WebApiService
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services.Communication.Runtime;

    public class OwinCommunicationListener : ICommunicationListener
    {
        private readonly IOwinAppBuilder startup;
        private readonly string appRoot;
        private readonly ServiceInitializationParameters serviceInitializationParameters;
        private IDisposable serverHandle;
        private string listeningAddress;

        public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup, ServiceInitializationParameters serviceInitializationParameters)
        {
            this.startup = startup;
            this.appRoot = appRoot;
            this.serviceInitializationParameters = serviceInitializationParameters;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
            int port = serviceEndpoint.Port;

            this.listeningAddress = String.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/{1}",
                port,
                String.IsNullOrWhiteSpace(this.appRoot)
                    ? String.Empty
                    : this.appRoot.TrimEnd('/') + '/');

            this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));
            string publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            ServiceEventSource.Current.Message("Listening on {0}", publishAddress);

            return Task.FromResult(publishAddress);
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            ServiceEventSource.Current.Message("Close");

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            ServiceEventSource.Current.Message("Abort");

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.serverHandle != null)
            {
                try
                {
                    this.serverHandle.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}

```

Ahora que ha colocado todas las piezas en su lugar, el proyecto debe presentar el aspecto de una aplicación típica de API web con los puntos de entrada de la API de Reliable Services y un host OWIN:


![API web con los puntos de entrada de la API de Reliable Services y el host OWIN](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## Ejecutar y conectarse a través de un explorador web

Si no lo ha hecho, [configure el entorno de desarrollo](service-fabric-get-started.md).


Ahora puede compilar e implementar su servicio. Presione **F5** en Visual Studio para compilar e implementar la aplicación. En la ventana de eventos de diagnósticos, debe aparecer un mensaje que indica que el servidor web se ha abierto en **http://localhost:80/webapp/api**.


![Ventana Eventos de diagnóstico de Visual Studio](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE]Si el puerto ya lo ha abierto otro proceso en el equipo, puede aparecer un error aquí. Esto indica que no se ha podido abrir el agente de escucha. Si ese es el caso, intente utilizar un puerto diferente para configurar el punto de conexión en ServiceManifest.xml.


Una vez que el servicio se esté ejecutando, abra un explorador y navegue hasta [http://localhost/webapp/api/values](http://localhost/webapp/api/values) para probarlo.

## Escala horizontal

Escalar aplicaciones web sin estado normalmente supone agregar más equipos y sincronizar aplicaciones web en ellos. El motor de orquestaciones de Service Fabric puede hacer esto automáticamente cada vez que se agregan nuevos nodos a un clúster. Al crear instancias de un servicio sin estado, puede especificar el número de instancias que desea crear. Service Fabric coloca ese número de instancias en los nodos del clúster. Y se asegura de no crear más de una instancia en cada nodo. También puede indicar a Service Fabric que cree siempre una instancia en cada nodo mediante la especificación de **-1** en el número de instancias. Esto garantiza que cada vez que agregue nodos para escalar horizontalmente el clúster, se creará una instancia del servicio sin estado en los nodos nuevos. Este valor es una propiedad de la instancia de servicio, por lo que se establece cuando se crea una instancia de servicio. Puede hacerlo a través de PowerShell:

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

También puede hacerlo al definir un servicio predeterminado en un proyecto de servicio sin estado de Visual Studio:

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Para obtener más información sobre la creación de aplicaciones e instancias de servicio, vea [Implementar una aplicación](service-fabric-deploy-remove-applications.md).

## ASP.NET 5

En ASP.NET 5, el concepto y modelo de programación de separar la *aplicación* del *host* en las aplicaciones web sigue siendo el mismo. También puede aplicarse a otras formas de comunicación. Además, aunque el *host* puede diferir en ASP.NET 5, la capa de la *aplicación* de la API web sigue siendo la misma. Es donde reside realmente la mayor parte de la lógica de la aplicación.

## Pasos siguientes

[Depurar la aplicación de Service Fabric con Visual Studio](service-fabric-debugging-your-application.md)

<!---HONumber=AcomDC_0121_2016-->