<properties 
	pageTitle="Seguimiento de dependencia en Application Insights" 
	description="Analice el uso, la disponibilidad y el rendimiento de su aplicación web de Microsoft Azure o local con Application Insights." 
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
	ms.date="02/10/2016" 
	ms.author="awills"/>


# Configurar Application Insights: seguimiento de dependencias


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]



Una *dependencia* es un componente externo al que llama la aplicación. Suele ser un servicio al que se llama mediante HTTP, una base de datos o un sistema de archivos. En Application Insights para Visual Studio, puede ver fácilmente cuánto tiempo espera su aplicación a las dependencias y la frecuencia con que se produce un error en una llamada de dependencia.

![gráficos de ejemplo](./media/app-insights-asp-net-dependencies/10-intro.png)

El monitor de dependencia listo para su uso sin configuraciones adicionales actualmente llama a estos tipos de dependencias:

* ASP.NET
 * Bases de datos SQL
 * Servicios WFC y web de ASP.NET que usan enlaces basados en HTTP
 * Llamadas HTTP locales o remotas
 * DocumentDb, tabla, almacenamiento de blobs y cola de Azure
* Java
 * Llamadas a una base de datos a través de un controlador [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/), por ejemplo, MySQL, SQL Server, PostgreSQL o SQLite.
* JavaScript en páginas web: el [SDK de páginas web](app-insights-javascript.md) registra automáticamente las llamadas AJAX como dependencias.

Puede escribir sus propias llamadas de SDK para supervisar otras dependencias que usan la [API de TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).


## Para configurar la supervisión de dependencia

Necesita una suscripción a [Microsoft Azure](http://azure.com)

### Si la aplicación se ejecuta en su servidor IIS

1. En el servidor web IIS, inicie sesión con las credenciales de administrador.
2. Descargue y ejecute el [instalador del Monitor de estado](http://go.microsoft.com/fwlink/?LinkId=506648).
4. En el asistente de instalación, inicie sesión en Microsoft Azure.

    ![Inicie sesión en Azure con las credenciales de la cuenta Microsoft.](./media/app-insights-asp-net-dependencies/appinsights-035-signin.png)

    *¿Errores de conexión? Consulte [Solución de problemas](#troubleshooting).*

5. Seleccione la aplicación web instalada o el sitio web que desea supervisar y, a continuación, configure el recurso en el cual desea ver los resultados del portal Application Insights.

    ![Elija una aplicación y un recurso.](./media/app-insights-asp-net-dependencies/appinsights-036-configAIC.png)

    Normalmente, debe optar por configurar un nuevo recurso y un [grupo de recursos][roles].

    De lo contrario, usará un recurso existente si ya ha configurado [pruebas web][availability] para su sitio, o bien, la [supervisión de cliente web][client].

6. Reinicie IIS.

    ![Seleccione Reiniciar en la parte superior del cuadro de diálogo.](./media/app-insights-asp-net-dependencies/appinsights-036-restart.png)

    El servicio web se interrumpirá durante un breve período.

6. Tenga en cuenta que ApplicationInsights.config se ha insertado en las aplicaciones web que desea supervisar.

    ![Busque el archivo .config junto con los archivos de código de la aplicación web.](./media/app-insights-asp-net-dependencies/appinsights-034-aiconfig.png)

   También hay algunos cambios en web.config.

#### ¿Desea (volver a) configurarlo después?

Una vez completado el asistente, puede volver a configurar el agente siempre que lo desee. También puede utilizarlo si ha instalado el agente pero hay algún problema con la configuración inicial.

![Hacer clic en el icono de Application Insights en la barra de tareas](./media/app-insights-asp-net-dependencies/appinsights-033-aicRunning.png)


### Si la aplicación es una aplicación web de Azure

En el panel de control de la aplicación web de Azure, agregue la extensión Application Insights.

![En la aplicación web, Configuración, Extensiones, Agregar, Application Insights](./media/app-insights-asp-net-dependencies/05-extend.png)


### Si se trata de un proyecto de servicios en la nube de Azure

[Agregar scripts a roles web y de trabajo](app-insights-cloudservices.md).

## <a name="diagnosis"></a> Diagnóstico de problemas de rendimiento de dependencia

Para evaluar el rendimiento de las solicitudes en el servidor:

![En la página de información general de la aplicación en Application Insights, haga clic en el icono de rendimiento.](./media/app-insights-asp-net-dependencies/01-performance.png)

Desplácese hacia abajo para buscar en la cuadrícula de solicitudes:

![Lista de solicitudes con promedios y recuentos](./media/app-insights-asp-net-dependencies/02-reqs.png)

La primera tarda mucho tiempo. Veamos si podemos averiguar dónde se está invirtiendo el tiempo.

Haga clic en esa fila para ver los eventos de solicitud individuales:


![Lista de casos de solicitudes](./media/app-insights-asp-net-dependencies/03-instances.png)

Haga clic en cualquier instancia de ejecución prolongada para inspeccionarla con mayor profundidad.

> [AZURE.NOTE] Desplácese hacia abajo un poco para elegir una instancia. La latencia en la canalización puede significar que los datos de las instancias superiores están incompletos.

Desplácese hacia abajo hasta las llamadas de dependencia remotas relacionadas con esta solicitud:

![Búsqueda de llamadas a dependencias remotas, identificación de duración inusual.](./media/app-insights-asp-net-dependencies/04-dependencies.png)

Parece que la mayoría del tiempo que se ha invertido en atender a esta solicitud se ha empleado en una llamada a un servicio local.

Seleccione esa fila para obtener más información:


![Haga clic en esa dependencia remota para identificar la causa.](./media/app-insights-asp-net-dependencies/05-detail.png)

El detalle incluye suficiente información para diagnosticar el problema.



## Errores

Si hay solicitudes con error, haga clic en el gráfico.

![Haga clic en el gráfico de las solicitudes con error.](./media/app-insights-asp-net-dependencies/06-fail.png)

Haga clic en un tipo de solicitud y una instancia de la solicitud para buscar una llamada con errores a una dependencia remota.


![Haga clic en un tipo de solicitud, haga clic en la instancia para obtener acceso a una vista diferente de la misma instancia, haga clic en ella para obtener detalles de la excepción.](./media/app-insights-asp-net-dependencies/07-faildetail.png)


## Seguimiento de dependencias personalizadas

El módulo de seguimiento de dependencias estándar detecta automáticamente las dependencias externas, como bases de datos y API de REST. Pero tal vez quieras que se traten algunos componentes adicionales de la misma manera.

Puede escribir código que envíe la información de dependencia usando la misma [API de TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency) que usan los módulos estándar.

Por ejemplo, si compila el código con un ensamblado que no escribió usted mismo, podría cronometrar todas las llamadas al ensamblado para averiguar cómo contribuye a los tiempos de respuesta. Para que estos datos se muestren en los gráficos de dependencia en Application Insights, envíelos mediante `TrackDependency`.

```C#

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

Si desea desactivar el módulo de seguimiento de dependencia estándar, quite la referencia a DependencyTrackingTelemetryModule en [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

## Pasos siguientes

- [Excepciones](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [Datos de página y usuario](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [Disponibilidad](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-asp-net-dependencies.md
[roles]: app-insights-resources-roles-access-control.md

 

<!---HONumber=AcomDC_0211_2016-->