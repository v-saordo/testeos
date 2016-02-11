<properties 
	pageTitle="Recursos de Application Insights independientes para desarrollo, pruebas y producción" 
	description="Supervisar el rendimiento y el uso de la aplicación en diferentes fases de desarrollo" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/25/2015" 
	ms.author="awills"/>

# Recursos de Application Insights independientes para desarrollo, pruebas y producción


Para evitar que se mezcle la telemetría de las versiones de depuración, prueba y producción de la aplicación, cree recursos independientes de [Application Insights][start] para recibir los datos de cada versión.

Application Insights almacena y procesa los datos recibidos de la aplicación en un *recurso* de Azure. Cada recurso se identifica por una *clave de instrumentación*. En la aplicación, la clave se proporciona al SDK de Application Insights para que este pueda enviar los datos que recopila al recurso adecuado. La clave se puede proporcionar en el código o en ApplicationInsights.config. Al cambiar la clave en el SDK, puede dirigir los datos a distintos recursos.


## Creación de recursos en Application Insights
  

En el [portal.azure.com](https://portal.azure.com), agregue un recurso de Application Insights:

![Haga clic en Nuevo, Application Insights.](./media/app-insights-separate-resources/01-new.png)


* El **tipo de aplicación** afecta a lo que ve en la hoja de información general y las propiedades disponibles en el [explorador de métricas][metrics]. Si no ve el tipo de aplicación, elija uno de los tipos de web para páginas web y uno de los tipos de teléfono para otros dispositivos.
* El **grupo de recursos** resulta práctico para administrar propiedades como el como el [control de acceso](app-insights-resources-roles-access-control.md). Puede usar grupos de recursos independientes para desarrollo, prueba y producción.
* La **suscripción** es su cuenta de pago de Azure.
* La **ubicación** es donde se guardan los datos. Actualmente no se puede cambiar.
* La opción para **agregar al panel de inicio** coloca un icono de acceso rápido al recurso en la página principal de Azure. 

El recurso tarda unos segundos en crearse. Verá una alerta cuando esté listo.

(Puede escribir un [script de PowerShell](app-insights-powershell-script-create-resource.md) para crear un recurso automáticamente.)


## Copia de la clave de instrumentación

La clave de instrumentación identifica al recurso que ha creado.

![Haga clic en Essentials y elija la clave de instrumentación, CTRL + C](./media/app-insights-separate-resources/02-props.png)

Necesitará las claves de instrumentación de todos los recursos a los que la aplicación enviará datos.


## <a name="dynamic-ikey"></a> Copia de la clave de instrumentación

Normalmente, el SDK obtiene la iKey de ApplicationInsights.config. Pero puede facilitar la modificación si establece la clave en el código.

Establezca la clave en un método de inicialización, como global.aspx.cs en un servicio de ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.AppSettings["ikey"];
      ...

En este ejemplo, las ikeys para los distintos recursos se colocan en diferentes versiones del archivo de configuración web. Si se intercambia el archivo de configuración web, se intercambiará el recurso de destino.

#### Páginas web

La iKey también se usa en las páginas web de su aplicación, en el [script que obtuvo desde la hoja Inicio rápido](app-insights-javascript.md). En vez de codificarla literalmente en el script, genérela desde el estado del servidor. Por ejemplo, en una aplicación ASP.NET:

*JavaScript en Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...





<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_1203_2015-->