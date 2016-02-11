<properties 
	pageTitle="Diagnóstico de errores y excepciones en aplicaciones de ASP.NET con Application Insights" 
	description="Capture las excepciones de las aplicaciones ASP.NET junto con la telemetría de solicitudes." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="awills"/>


# Configurar Application Insights: diagnosticar excepciones

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]


Supervisando la aplicación con [Visual Studio Application Insights][start], puede correlacionar las solicitudes con error con las excepciones y otros eventos tanto en el cliente como en el servidor, y así diagnosticar rápidamente las causas.

Para supervisar una aplicación ASP.NET, debe [agregar el SDK de Application Insights][greenbrown] a la aplicación, o bien [instalar el Monitor de estado en el servidor IIS][redfield]; o, si su aplicación es una aplicación web de Azure, agregue la [extensión de Application Insights][azure].

## Diagnóstico de excepciones mediante Visual Studio

Abra la solución de aplicación en Visual Studio para ayudar con la depuración.

Ejecute la aplicación, bien en el servidor o en el equipo de desarrollo mediante F5.

Abra la ventana Búsqueda de Application Insights en Visual Studio y configúrela para mostrar los eventos de la aplicación. Durante la depuración, puede hacer esto haciendo clic simplemente en el botón de Application Insights.

![Haga clic con el botón derecho en el proyecto y seleccione Application Insights, Abrir.](./media/app-insights-asp-net-exceptions/34.png)

Observe que puede filtrar el informe para mostrar solo excepciones.

*¿No se muestra ninguna excepción? Consulte [Captura de excepciones](#exceptions).*

Haga clic en un informe de excepciones para mostrar su seguimiento de la pila.

![Haga clic en una excepción.](./media/app-insights-asp-net-exceptions/35.png)

Haga clic en una referencia de línea en el seguimiento de la pila para abrir el archivo pertinente.

## Diagnóstico de errores mediante el Portal de Azure

En la hoja de información general de Application Insights de su aplicación, el icono Errores muestra los gráficos de excepciones y las solicitudes HTTP con error, junto con una lista de las direcciones URL de solicitudes que provocan los errores más frecuentes.

![Selección de Configuración, Errores](./media/app-insights-asp-net-exceptions/012-start.png)

Haga clic en uno de los tipos de solicitudes con error de la lista para llegar a las repeticiones individuales del error. Desde ahí, siga haciendo clic hasta llegar a las excepciones o a los datos de seguimiento asociados con ellas:

![Seleccione una instancia de una solicitud con error y, en los detalles de la excepción, obtenga las instancias de la excepción.](./media/app-insights-asp-net-exceptions/030-req-drill.png)


**Como alternativa**, puede comenzar a partir de la lista de excepciones que encontrará más abajo de la hoja Problemas. Siga haciendo clic hasta llegar finalmente a las excepciones individuales.


![Obtener detalles](./media/app-insights-asp-net-exceptions/040-exception-drill.png)

*¿No se muestra ninguna excepción? Consulte [Captura de excepciones](#exceptions).*

Desde ahí, puede examinar el seguimiento de pila y las propiedades detalladas de cada excepción y encontrar el seguimiento de registro relacionado u otros eventos.


![Obtener detalles](./media/app-insights-asp-net-exceptions/050-exception-properties.png)

[Más información sobre Búsqueda de diagnóstico][diagnostic].



## Errores de dependencia

Una *dependencia* es un servicio al que llama su aplicación, normalmente a través de una API de REST o una conexión de base de datos. El [Monitor de estado de Application Insights][redfield] supervisa automáticamente diversos tipos de llamadas de dependencia, y mide la duración de la llamada y el acierto o error.

Para obtener datos de dependencia, debe [instalar el Monitor de estado][redfield] en el servidor IIS; o bien, si la aplicación es una aplicación web de Azure, use la [extensión de Application Insights][azure].

Las llamadas con error a las dependencias se muestran en la hoja Problemas, pero también puede encontrarlas en Elementos relacionados en los detalles de la solicitud y en los detalles de la excepción.

*¿No hay errores de dependencia? Eso está muy bien. Pero para comprobar que está obteniendo datos de dependencia, abra la hoja Rendimiento y examine el gráfico Duración de la dependencia.*

 

## Personalización del seguimiento y del registro de datos

Para obtener datos de diagnóstico específicos de su aplicación, puede insertar código para enviar sus propios datos de telemetría. Esto aparece en la búsqueda de diagnóstico junto con la solicitud, vista de página y otros datos que se recopilan automáticamente.

Tiene varias opciones:

* [TrackEvent()](app-insights-api-custom-events-metrics.md#track-event) normalmente se usa para supervisar patrones de uso, pero los datos que envía también aparecen en Eventos personalizados en la búsqueda de diagnósticos. Los eventos tienen nombre y pueden llevar propiedades de cadena y métricas numéricas en las que puede [filtrar las búsquedas de diagnósticos][diagnostic].
* [TrackTrace()](app-insights-api-custom-events-metrics.md#track-trace) le permite enviar datos más grandes, como la información de POST.
* [TrackException()](#exceptions) envía seguimientos de la pila. [Más información sobre excepciones](#exceptions).
* Si ya utiliza un marco de registro como Log4Net o NLog, puede [capturar aquellos registros][netlogs] y verlos en la búsqueda de diagnósticos junto con datos de solicitud y excepción.

Para ver estos eventos, abra [Buscar][diagnostic], abra Filtrar y luego elija Evento personalizado, Seguimiento o Excepción.


![Obtener detalles](./media/app-insights-asp-net-exceptions/viewCustomEvents.png)


> [AZURE.NOTE] Si la aplicación genera mucha telemetría, el módulo de muestreo adaptable reducirá automáticamente el volumen que se envía al portal mediante el envío de únicamente una fracción representativa de eventos. Los eventos que forman parte de la misma operación se seleccionarán o se anulará su selección como grupo, por lo que puede navegar entre los eventos relacionados. [Más información sobre el muestreo.](app-insights-sampling.md)

### Visualización de los datos de solicitud POST

Los detalles de la solicitud no incluyen los datos enviados a la aplicación en una llamada a POST. Para que se notifiquen estos datos:

* [Instale el SDK][greenbrown] en su proyecto de aplicación.
* Inserte código en la aplicación para llamar a [Microsoft.ApplicationInsights.TrackTrace()][api]. Envíe los datos de POST en el parámetro de mensaje. Hay un límite en cuanto al tamaño permitido, así que debe intentar enviar únicamente los datos fundamentales.
* Cuando investigue una solicitud con error, busque los seguimientos asociados.  

![Obtener detalles](./media/app-insights-asp-net-exceptions/060-req-related.png)


## <a name="exceptions"></a> Captura de excepciones y datos de diagnóstico relacionados

En primer lugar, no verá en el portal todas las excepciones que provocan errores en su aplicación. Verá las excepciones del explorador (si usa el [SDK de JavaScript][client] en sus páginas web). Pero la mayoría de las excepciones de servidor las detecta IIS y debe escribir algo de código para verlas.

Puede:

* **Registrar excepciones explícitamente** insertando código en los controladores de excepciones para notificar las excepciones.
* **Capturar excepciones automáticamente** configurando su marco de ASP.NET. Las adiciones necesarias son diferentes para los distintos tipos de marco.

## Notificación de excepciones explícitamente

La manera más sencilla consiste en insertar una llamada a TrackException() en un controlador de excepciones.

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)
  
      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Los parámetros de las propiedades y las medidas son opcionales, pero son útiles para [filtrar y agregar][diagnostic] información adicional. Por ejemplo, si tiene una aplicación que se puede ejecutar varios juegos, podría buscar todos los informes de excepción relacionados con un juego en particular. Puede agregar tantos elementos como desee para cada diccionario.

## Excepciones de explorador

Se notifican la mayoría de las excepciones de explorador.

Si la página web incluye archivos de script de redes de entrega de contenido o de otros dominios, asegúrese de que la etiqueta de script tenga el atributo ```crossorigin="anonymous"```, y que el servidor envíe [encabezados CORS](http://enable-cors.org/). Esto le permitirá obtener un seguimiento de pila y los detalles de las excepciones no controladas de JavaScript de estos recursos.

## Formularios web

Para los formularios web, el módulo HTTP podrá recopilar las excepciones cuando no haya ningún redireccionamiento configurado con CustomErrors.

Pero si tiene redireccionamientos activos, agregue las siguientes líneas a la función Application\_Error en Global.asax.cs. (Agregue un archivo Global.asax si aún no tiene uno.)

*C#*

    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError  () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }


## MVC

Si la configuración de [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) es `Off`, entonces habrá excepciones disponibles para que las recopile el [módulo HTTP](https://msdn.microsoft.com/library/ms178468.aspx). Sin embargo, si es `RemoteOnly` (valor predeterminado), o `On`, la excepción se desactivará y no estará disponible para que Application Insights la recopile automáticamente. Para corregir este problema, invalide la [clase System.Web.Mvc.HandleErrorAttribute](http://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx) y aplique la clase invalidada como se muestra a continuación para las diferentes versiones de MVC ([origen de github](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)] 
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above  
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                } 
            }
            base.OnException(filterContext);
        }
      }
    }

#### MVC 2

Sustituya el atributo HandleError por el nuevo atributo en los controladores.

    namespace MVC2App.Controllers
    {
       [AiHandleError]
       public class HomeController : Controller
       {
    ...

[Ejemplo](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### MVC 3

Registrar `AiHandleErrorAttribute` como filtro global de Global.asax.cs:

    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...

[Ejemplo](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)



#### MVC 4, MVC5

Registrar AiHandleErrorAttribute como filtro global en FilterConfig.cs:

    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }

[Ejemplo](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## Web API 1.x


Invalidar System.Web.Http.Filters.ExceptionFilterAttribute:

    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above 
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);    
            }
            base.OnException(actionExecutedContext);
        }
      }
    }

Podría agregar este atributo invalidado a controladores específicos o agregarlo a la configuración de filtros globales en la clase WebApiConfig:

    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }

[Ejemplo](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

Hay un número de casos que los filtros de excepciones no pueden procesar. Por ejemplo:

* Excepciones iniciadas por constructores del controlador. 
* Excepciones iniciadas por controladores de mensajes. 
* Excepciones iniciadas durante el enrutamiento. 
* Excepciones iniciadas durante la serialización del contenido de respuesta. 

## Web API 2.x

Agregue una implementación de IExceptionLogger:

    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above 
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }

Agregue lo siguiente a los servicios en WebApiConfig:

    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger()); 
        }
      }
  }

[Ejemplo](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Como alternativa, puede:

2. Sustituir el único ExceptionHandler por una implementación personalizada de IExceptionHandler. Solo se llama a este controlador de excepciones cuando el marco es aún capaz de seleccionar el mensaje de respuesta que se enviará (no cuando se anula la conexión, por ejemplo) 
3. Los filtros de excepciones (como se describe en la sección sobre controladores de Web API 1.x anteriores) no se llaman en todos los casos.


## WCF

Agregue una clase que extienda el atributo y que implemente IErrorHandler y IServiceBehavior.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription, 
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription, 
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above 
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error, 
            System.ServiceModel.Channels.MessageVersion version, 
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Agregue el atributo a las implementaciones de servicio:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1 
        { 
         ...

[Ejemplo](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## Contadores de rendimiento de excepciones

Si tiene [instalado el Monitor de estado][redfield] en el servidor, puede obtener un gráfico de la tasa de excepciones, medida por. NET. Esto incluye las excepciones de .NET, tanto controladas como no controladas.

Abra una hoja del Explorador de métricas, agregue un nuevo gráfico y seleccione **Tasa de excepciones**, que aparece debajo de Contadores de rendimiento.

.NET framework calcula la tasa contando el número de excepciones producidas en un intervalo y dividiéndolo por la duración del intervalo de tiempo.

Tenga en cuenta que será diferente del recuento de "Excepciones" calculado por el portal de Application Insights contando los informes de TrackException. Los intervalos de muestreo son diferentes y el SDK no envía informes de TrackException para todas las excepciones, controladas y no controladas.

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[netlogs]: app-insights-asp-net-trace-logs.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0128_2016-->