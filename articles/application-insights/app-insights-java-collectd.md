<properties 
	pageTitle="collectd: estadísticas de rendimiento para Java en Unix en Application Insights" 
	description="Supervisión extendida de sitios web de Java con el complemento CollectD para Application Insights" 
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
 
# collectd: métricas de rendimiento de Unix en Application Insights

*Application Insights se encuentra en su versión de vista previa.*

Para explorar las métricas de rendimiento del sistema de Unix en [Application Insights](app-insights-overview.md), instale [collectd](http://collectd.org/) junto con su complemento Application Insights. Esta solución de código abierto recopila una serie de estadísticas de red y del sistema.

Normalmente, usará collectd si ya ha [instrumentado el servicio web de Java con Application Insights][java] para disponer de más datos que le ayuden a mejorar el rendimiento de la aplicación o a diagnosticar problemas.

![Gráficos de ejemplo](./media/app-insights-java-collectd/sample.png)

## Obtención de la clave de instrumentación

En el [Portal de Microsoft Azure](https://portal.azure.com), abra el recurso [Application Insights](start)donde desea que aparezcan los datos (o bien, [cree un nuevo recurso](app-insights-create-new-resource.md)).

Realice una copia de la clave de instrumentación, que identifica al recurso.

![Examine todo, abra el recurso y, en la lista desplegable de Esssentials, seleccione y copie la clave de instrumentación.](./media/app-insights-java-collectd/02-props.png)



## Instalación de collectd y del complemento

En los equipos de servidor Unix:

1. Instale la versión de [collectd](http://collectd.org/) 5.4.0 o posterior.
2. Descargue el [complemento del escritor collectd de Application Insights](https://azuredownloads.blob.core.windows.net/applicationinsights/sdk.html). Anote el número de versión.
3. Copie el archivo JAR del complemento en `/usr/share/collectd/java`.
3. Edite `/etc/collectd/collectd.conf`:
 * Asegúrese de que [el complemento de Java](https://collectd.org/wiki/index.php/Plugin:Java) está habilitado.
 * Actualice el elemento JVMArg para que java.class.path incluya el archivo JAR siguiente. Actualice el número de versión para que coincida con el que descargó:
  * `/usr/share/collectd/java/applicationinsights-collectd-0.9.4.jar`
 * Agregue este fragmento de código con la clave de instrumentación del recurso:

```

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

A continuación se muestra un archivo de configuración de ejemplo:

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-0.9.4.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
                
      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
. ...

Configure otros[complementos de collectd](https://collectd.org/wiki/index.php/Table_of_Plugins) que pueden recopilar una gran variedad de datos de orígenes diferentes.

Reinicie collectd según su [manual](https://collectd.org/wiki/index.php/First_steps).

## Visualización de los datos en Application Insights

En el recurso de Application Insights, abra el [Explorador de métricas y agregue gráficos][metrics] seleccionando las métricas que desea ver en la categoría Personalizado.

![](./media/app-insights-java-collectd/result.png)

De forma predeterminada, las métricas se agregan en todos los equipos host desde los que se recopilaron estas. Para ver las métricas por host, en la hoja Detalles del gráfico, active Agrupación y elija Agrupar por CollectD-Host.


## Para excluir la carga de estadísticas específicas

De forma predeterminada, el complemento de Application Insights enviará todos los datos recopilados por todos los complementos de lectura de collectd habilitados.

Para excluir los datos de orígenes de datos o complementos específicos:

* Edite el archivo de configuración. 
* En `<Plugin ApplicationInsightsWriter>`, agregue líneas de directiva de esta manera:

Directiva | Efecto
---|---
`Exclude disk` | Excluir todos los datos recopilados por el complemento `disk`
`Exclude disk:read,write` | Excluir los orígenes denominados `read` y `write` del complemento `disk`.

Separar directivas con una nueva línea.


## ¿Tiene problemas?

*No veo datos en el portal*

* Abra [Buscar][diagnostic] para ver si han llegado los eventos sin procesar. A veces tardan más en aparecer en el Explorador de métricas.
* Habilite el seguimiento en el complemento Application Insights. Agregue esta línea en `<Plugin ApplicationInsightsWriter>`:
 *  `SDKLogger true`
* Abra un terminal e inicie collectd en modo detallado para ver cualquier problema que esté notificando:
 * `sudo collectd -f`




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=AcomDC_0302_2016-->