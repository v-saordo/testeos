<properties 
	pageTitle="Application Insights para aplicaciones web de Java que ya están activas" 
	description="Inicio de la supervisión de una aplicación web que se ya se está ejecutando en el servidor" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/02/2016" 
	ms.author="awills"/>
 
# Application Insights para aplicaciones web de Java que ya están activas

*Application Insights se encuentra en su versión de vista previa.*

Si tiene una aplicación web que se está ejecutando en el servidor de J2EE, puede iniciar la supervisión con [Application Insight](app-insights-overview.md) sin necesidad de realizar cambios de código ni volver a compilar el proyecto. Con esta opción, obtendrá información acerca de las solicitudes HTTP enviadas al servidor, las excepciones no controladas y contadores de rendimiento.

Necesitará una suscripción a [Microsoft Azure](https://azure.com).

> [AZURE.NOTE] El procedimiento en esta página agrega el SDK a la aplicación web en tiempo de ejecución. Esto es útil si no desea actualizar o volver a generar el código fuente. Pero si es posible, se recomienda [agregar el SDK al código fuente](app-insights-java-get-started.md) en su lugar. Que proporciona más opciones, como la escritura de código para realizar el seguimiento de actividad del usuario.

## 1\. Obtención de una clave de instrumentación de Application Insights

1. Inicio de sesión en el [Portal de Microsoft Azure](https://portal.azure.com)
2. Creación de un recurso de Application Insights

    ![Haga clic en + y elija Application Insights](./media/app-insights-java-live/01-create.png)
3. Establezca el tipo de aplicación a una aplicación web de Java.

    ![Rellene un nombre, elija la aplicación web de Java y haga clic en Crear.](./media/app-insights-java-live/02-create.png)
4. Busque la clave de instrumentación del nuevo recurso. En breve necesitará pegarlo en el proyecto de código.

    ![En la información general de nuevos recursos, haga clic en Propiedades y copie la clave de instrumentación.](./media/app-insights-java-live/03-key.png)

## 2\. Descarga del SDK

1. Descargue el [SDK de Application Insights para Java](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.html). 
2. En el servidor, extraiga el contenido del SDK en el directorio desde el que se cargan los archivos binarios de proyecto. Si usa Tomcat, estará normalmente en `webapps<your_app_name>\WEB-INF\lib`.


## 3\. Adición de un archivo xml de Application Insights

Cree ApplicationInsights.xml en la carpeta en la que se ha agregado el SDK. Ponga el archivo en el siguiente XML.

Sustituya la clave de instrumentación que obtuvo en el portal de Azure.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>


* La clave de instrumentación se envía junto con todos los elementos de telemetría e indica a Application Insights que se muestre en el recurso.
* El componente de la solicitud HTTP es opcional. Envía automáticamente telemetría sobre las solicitudes y tiempos de respuesta en el portal.
* La correlación de eventos es un complemento del componente de la solicitud HTTP. Asigna un identificador a cada solicitud recibida por el servidor y lo agrega como una propiedad a cada elemento de telemetría como la propiedad 'Operation.Id'. Le permite correlacionar la telemetría asociada a cada solicitud estableciendo un filtro en la [búsqueda de diagnóstico](app-insights-diagnostic-search.md).


## 4\. Adición de un filtro HTTP

Busque y abra el archivo web.xml en el proyecto y combine el siguiente fragmento de código bajo el nodo web-app, donde se han configurado los filtros de aplicación.

Para obtener los resultados más precisos, el filtro debe asignarse antes de todos los demás filtros.

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

## 5\. Reinicio de la aplicación web

## 6\. Visualización de la telemetría en Application Insights

Vuelva al recurso Application Insights en el [Portal de Microsoft Azure](https://portal.azure.com).

Los datos de las solicitudes HTTP aparecerán en la hoja de información general. (Si todavía no está ahí, espere unos segundos y, a continuación, haga clic en Actualizar).

![datos de ejemplo](./media/app-insights-java-live/5-results.png)
 

Haga clic en cualquier gráfico para ver métricas más detalladas.

![](./media/app-insights-java-live/6-barchart.png)

 

Y cuando vea las propiedades de una solicitud, podrá ver los eventos de telemetría asociados, como solicitudes y excepciones.
 
![](./media/app-insights-java-live/7-instance.png)


[Más información acerca de las métricas.](app-insights-metrics-explorer.md)



## Pasos siguientes

* [Agregue telemetría a las páginas web](app-insights-web-track-usage.md) para supervisar las vistas de páginas y las métricas de usuario.
* [Configure las pruebas web](app-insights-monitor-web-app-availability.md) para comprobar que la aplicación efectivamente está activa y responde adecuadamente.
* [Captura de seguimiento de registros](app-insights-java-trace-logs.md)
* [Busque eventos y registros](app-insights-diagnostic-search.md) para ayudar a diagnosticar problemas.


 

<!---HONumber=AcomDC_0302_2016-->