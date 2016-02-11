<properties 
	pageTitle="Actualización desde la versión anterior de Visual Studio Team Services de Application Insights" 
	description="Actualización de los proyectos existentes"
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
	ms.date="06/19/2015" 
	ms.author="awills"/>
 
# Actualización desde la versión anterior de Visual Studio Team Services de Application Insights

Este documento solo resulta de interés si tiene un proyecto que todavía está utilizando la versión anterior de Application Insights, que formaba parte de Visual Studio Team Services. Esa versión se desactivará en su momento, y por lo tanto, le recomendamos que actualice a la nueva versión, que es un servicio en Microsoft Azure.

## ¿Qué versión tengo?

Si agregó Application Insights al proyecto mediante Visual Studio 2013 Update 3 o una versión posterior, probablemente utiliza la nueva versión de Azure.

Abra ApplicationInsights.config. Si tiene los nodos `ActiveProfile` y `Profiles`, tiene la versión anterior y debe actualizarla .

O bien examine el proyecto en el Explorador de soluciones de Visual Studio y en Referencias, seleccione Microsoft.ApplicationInsights. En la ventana Propiedades, busque la versión. Si es menor que 0.12, debe actualizarse.

## Si tiene un proyecto de Visual Studio...

1. Abra el proyecto en Visual Studio 2013 Update 3 o una versión posterior.
2. Elimine ApplicationInsights.config. 
3. Quite los paquetes de NuGet de Application Insights del proyecto. Para ello, haga clic con el botón secundario en el proyecto en el Explorador de soluciones y seleccione Administrar paquetes de NuGet.

    ![](./media/app-insights-upgrade-vso-azure/nuget.png)
4. Elimine la carpeta AppInsightsAgent y los archivos que contiene.

    ![](./media/app-insights-upgrade-vso-azure/folder.png)

5. Quite todos los valores y nombres de configuración de Microsoft.AppInsights de ServiceDefinition.csdef y ServiceConfiguration.csfg.

    ![](./media/app-insights-upgrade-vso-azure/csdef.png)
4. SDK: haga clic con el botón secundario y seleccione [Agregar Application Insights][greenbrown]. Se agrega el SDK al proyecto y también se crea un nuevo recurso de Application Insights en Azure.
5. Registro: si el código incluye llamadas a la API antigua como LogEvent(), las descubrirá al intentar compilar la solución. Actualícelas para [usar la nueva API][track].
6. Páginas web: si el proyecto incluye páginas web, reemplace los scripts en las secciones <head>. Normalmente solo hay una copia en una página maestra, como Views\\Shared\_Layout.cshtml. [Obtenga el script nuevo desde la hoja Inicio rápido del recurso de Application Insights en Azure][usage]. Si las páginas web incluyen llamadas de telemetría en el cuerpo, como logEvent o logPage, [actualícelas para usar la nueva API][api].
7. Monitor de servidor: si la aplicación es un servicio que se ejecuta en IIS, desinstale Microsoft Monitoring Agent desde el servidor y, a continuación, [instale el Monitor de estado de Application Insights][redfield].
8. Pruebas web: si utilizaba las pruebas de disponibilidad de web, [vuelva a crearlas en el nuevo portal][availability], con sus alertas.
9. Alertas: configure las [alertas sobre métricas][alerts] en el portal de Azure.


## Si tiene un servicio web de Java...

1. En el equipo servidor, deshabilite al agente anterior quitando las referencias al agente de APM del archivo de inicio del servicio web. En un servidor TomCat, edite Catalina.bat. En un servidor JBoss, edite Run.bat. 
2. Reinicie el servicio web.
3. En el portal de Microsoft Azure, [agregue un nuevo recurso de Application Insights][java]. En el equipo de desarrollo, agregue [el SDK de Java][java] al proyecto web.
4. Reemplace los scripts en las secciones <head> de las páginas web. (Puede haber solo una copia en un comando include del servidor). [Obtenga el script nuevo desde la hoja Inicio rápido del recurso nuevo de Application Insights en Azure][usage]. Si las páginas web incluyen llamadas de telemetría en el cuerpo, como logEvent o logPage, [actualícelas para usar la nueva API][track].
8. Pruebas web: si utilizaba las pruebas de disponibilidad de web, [vuelva a crearlas en el nuevo portal][availability], con sus alertas.
9. Alertas: configure las [alertas sobre métricas][alerts] en el portal de Azure.



<!--Link references-->

[alerts]: app-insights-alerts.md
[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=Nov15_HO4-->