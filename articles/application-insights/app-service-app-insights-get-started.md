<properties 
	pageTitle="Application Insights en Microsoft Azure" 
	description="Detecte, evalúe y diagnostique problemas en la aplicación de dispositivo o web activa. Supervise y mejore continuamente el éxito con los usuarios." 
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
 
# Application Insights de Visual Studio

Application Insights es un servicio de análisis extensible que supervisa su aplicación activa. Le ayuda a detectar y a diagnosticar problemas de rendimiento y a comprender qué hacen los usuarios realmente con su aplicación. Está diseñado para desarrolladores, para ayudarle a mejorar continuamente el rendimiento y la facilidad de uso de la aplicación.

![Cree un gráfico de estadísticas de la actividad del usuario o explore en profundidad eventos específicos.](./media/app-service-app-insights-get-started/00-sample.png)

Funciona tanto con aplicaciones web como independientes en una amplia variedad de plataformas: .NET o J2EE, hospedadas localmente o en la nube.

Application Insights está destinada al equipo de desarrollo. Con ella, puede:

* [Analizar patrones de uso][knowUsers] para comprender mejor a los usuarios y mejorar continuamente la aplicación. 
 * Recuentos de vistas de páginas, usurarios nuevos y recurrentes, ubicación geográfica, plataformas y otras estadísticas de uso principales
 * Seguimiento de las rutas de acceso de uso para evaluar el éxito de cada característica.
* [Detecte, evalúe y diagnostique][detect] problemas de rendimiento y corríjalos antes de que se den cuenta la mayoría de los usuarios.
 *  Alertas sobre cambios en el rendimiento o bloqueos.
 *  Métricas para ayudar a diagnosticar problemas de rendimiento, como tiempos de respuesta, uso de CPU o seguimiento de dependencias.
 *  Pruebas de disponibilidad para aplicaciones web.
 *  Informes y alertas de excepciones y bloqueos
 *  Búsqueda eficaz de registros de diagnósticos (lo que incluye seguimientos de registro de sus marcos de registro favoritos).

El SDK para cada plataforma incluye una variedad de módulos que supervisan la aplicación de inmediato. Además, puede codificar su propia telemetría para realizar análisis más detallados y adaptados.

Los datos de telemetría recopilados de la aplicación se almacenan y analizan en el Portal de Azure, donde hay vistas intuitivas y herramientas eficaces que permiten realizar diagnósticos y análisis rápidos.



¿Desea un análisis todavía más exhaustivo? [Exporte](app-insights-export-telemetry.md) los datos [en SQL](app-insights-code-sample-export-telemetry-sql-database.md), [en Power BI](app-insights-export-power-bi.md) o en sus propias herramientas.

![Visualización de datos en Power BI](./media/app-service-app-insights-get-started/210.png)

## Plataformas y lenguajes

Hay un SDK para una variedad creciente de plataformas. Actualmente, la lista incluye:

 * [Servidores de ASP.NET][greenbrown] en Azure o en el servidor IIS
 * [Servicios en la nube de Azure](app-insights-cloudservices.md)
 * [Servidores de J2EE][java]
 * [Páginas web][client]\: HTML + JavaScript
 * [Servicios de Windows, roles de trabajo y aplicaciones de escritorio][desktop]
 * [Otras plataformas][platforms]\: Node.js, PHP, Python, Ruby, Joomla, SharePoint y WordPress

Application Insights también puede obtener telemetría de las aplicaciones web de ASP.NET existentes en IIS sin volver a compilarlas.

Si su aplicación tiene cliente, servidor u otros componentes, puede instrumentarlos. Los datos se integrarán en el portal de Application Insights para que, por ejemplo, pueda correlacionar eventos en el cliente con eventos en el servidor.


## Cómo funciona

Instale un pequeño SDK en su aplicación y configure una cuenta en el portal de Application Insights. El SDK supervisa la aplicación y envía los datos de telemetría al portal. El portal le muestra gráficos estadísticos y ofrece eficaces herramientas de búsqueda para ayudarle a diagnosticar los problemas.

![El SDK de Application Insights de su aplicación envía datos de telemetría al recurso de Application Insights en el Portal de Azure.](./media/app-service-app-insights-get-started/01-scheme.png)

El SDK cuenta con varios módulos que recopilan datos de telemetría, con el fin de contar, por ejemplo, usuarios, sesiones y el rendimiento. También puede escribir su propio código personalizado para enviar datos de telemetría al portal. La telemetría personalizada resulta especialmente útil para realizar el seguimiento de los casos de usuario: puede contar eventos como los clics de botón, la consecución de objetivos concretos o los errores de los usuarios.

Para servidores ASP.NET y aplicaciones web de Azure, también puede instalar el [Monitor de estado][redfield], que tiene dos usos. Así, le permite:

* Supervisar una aplicación web sin necesidad de volverla a compilar o instalar.
* Realizar el seguimiento de llamadas a módulos dependientes.



### ¿Cuál es la sobrecarga?

El impacto sobre el rendimiento es muy pequeño. Seguimiento de llamadas que no son de bloqueo y que se agrupan por lotes y se envían en un subproceso independiente.



## Primeros pasos

1. Necesita una suscripción a [Microsoft Azure](http://azure.com). Suscribirse es gratis y puede elegir el [plan de tarifa](https://azure.microsoft.com/pricing/details/application-insights/) gratuito de Application Insights.

2. Si usa Visual Studio:

 * Nuevo proyecto: active la casilla **Agregar Application Insights**.
 * Proyecto existente: haga clic con el botón secundario en el proyecto y elija **Agregar Application Insights**.

Si no usa Visual Studio, o si esas opciones no están disponibles para su proyecto, [busque su plataforma aquí](app-insights-platforms.md).

3. Ejecute la aplicación (en el modo de desarrollo, o publíquela) y observe cómo se acumulan los datos en el [Portal de Azure](https://portal.azure.com).

## Código


[Ejemplos y tutoriales](app-insights-code-samples.md)

[Laboratorios de DK](https://www.myget.org/gallery/applicationinsights-sdk-labs): paquetes de NuGet que puede instalar (y desinstalar) como adiciones al SDK de Application Insights. ¡Pruébelos y envíenos sus comentarios!


## Soporte y comentarios

* Preguntas y problemas:
 * [Solución de problemas][qna]
 * [Foro de MSDN](https://social.msdn.microsoft.com/Forums/vstudio/es-ES/home?forum=ApplicationInsights)
 * [Stackoverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* Errores:
 * [Conectar](https://connect.microsoft.com/VisualStudio/Feedback/LoadSubmitFeedbackForm?FormID=6076)
* Sugerencias
 * [Voz del usuario](http://visualstudio.uservoice.com/forums/121579-visual-studio/category/77108-application-insights)


## Vídeos


> [AZURE.VIDEO 218]

> [AZURE.VIDEO usage-monitoring-application-insights]

> [AZURE.VIDEO performance-monitoring-application-insights]


<!--Link references-->

[android]: https://github.com/Microsoft/ApplicationInsights-Android
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-asp-net.md
[ios]: https://github.com/Microsoft/ApplicationInsights-iOS
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_1203_2015-->