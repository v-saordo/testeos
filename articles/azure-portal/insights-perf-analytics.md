<properties 
	pageTitle="Supervisión del rendimiento de aplicaciones web de Azure" 
	description="Carga y tiempo de respuesta de gráfico, información de dependencia y establecer alertas en el rendimiento." 
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="awills"/>

# Supervisión del rendimiento de aplicaciones web de Azure

En el [Portal de Azure](https://portal.azure.com), puede configurar la supervisión para que recopile estadísticas y detalles sobre las dependencias de las aplicaciones en las [aplicaciones web](../app-service-web/app-service-web-overview.md) o [máquinas virtuales](../virtual-machines/virtual-machines-about.md) de Azure.

Azure admite que Supervisión de rendimiento de aplicaciones (o *APM*) haga uso de *extensiones*. Dichas extensiones se instalan en la aplicación, donde recopilan datos e informan a los servicios de supervisión.

Application Insights y New Relic son dos de las extensiones de supervisión de rendimiento que hay disponibles. Para usarlas, instale un agente en tiempo de ejecución. Con Application Insights, también existe la posibilidad de generar el código con un SDK. El SDK permite escribir código para supervisar el uso y el rendimiento de la aplicación con más detalle.

## Habilitación de extensiones

1. Haga clic en **Examinar** y seleccione la aplicación web o la máquina virtual que desee instrumentar.

2. Agregue toda la extensión de Application Insights o New Relic.

    Si va a instrumentar una aplicación web:

![Configuración, Extensiones, Agregar, Application Insights](./media/insights-perf-analytics/05-extend.png)

O bien, si utiliza una máquina virtual:

![Haga clic en el icono Análisis](./media/insights-perf-analytics/10-vm1.png)

### En Application Insights: recompilar con el SDK

Application Insights puede proporcionar una telemetría más detallada instalando un SDK en la aplicación.

En Visual Studio, agregue el SDK de Application Insights al proyecto.

![Haga clic con el botón derecho y elija Agregar Application Insights.](./media/insights-perf-analytics/03-add.png)

Cuando se le pide que inicie sesión, use las credenciales de su cuenta de Azure.

Para probar la telemetría, ejecute la aplicación en el equipo de desarrollo o vuelva a publicarla.

El SDK proporciona una API para que pueda [escribir telemetría personalizada](../app-insights-api-custom-events-metrics.md) para realizar un seguimiento del uso.

## Exploración de los datos

Use la aplicación para generar telemetría.

1. A continuación, desde la hoja de la máquina virtual o la aplicación web, podrá ver la extensión instalada.
2. Haga clic en la fila que representa la aplicación para navegar a ese proveedor: ![Haga clic en Actualizar](./media/insights-perf-analytics/06-overview.png)

También puede usar **Examinar** para ir directamente al componente Application Insights o a la cuenta de New Relic que utilizó previamente.

Una vez que obtenga la hoja, para Application Insights, por ejemplo, puede realizar estas acciones: - Open Performance:

![En la hoja de información general de Application Insights, haga clic en el icono Rendimiento](./media/insights-perf-analytics/07-dependency.png)

- Acceder a las solicitudes individuales:

![En la cuadrícula, haga clic en una dependencia para ver las solicitudes relacionadas.](./media/insights-perf-analytics/08-requests.png)

- En este ejemplo se muestra el tiempo invertido en una dependencia SQL, incluido el número de llamadas SQL y las estadísticas relacionadas, por ejemplo, la duración media y la desviación estándar. 

![](./media/insights-perf-analytics/01-example.png)



## Pasos siguientes

* [Supervise las métricas del estado del servicio](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y responde adecuadamente.
* [Habilite la supervisión y el diagnóstico](insights-how-to-use-diagnostics.md) para recopilar métricas detalladas de alta frecuencia en su servicio.
* [Reciba notificaciones de alerta](insights-receive-alert-notifications.md) cada vez que se produzcan eventos de operaciones o las métricas traspasen un umbral.
* Utilice [aplicaciones y páginas web de Application Insights para JavaScript](../app-insights-web-track-usage.md) para obtener el análisis del cliente acerca de los exploradores que visitan una página web.
* [Supervise la disponibilidad y la capacidad de respuesta de cualquier página web](../app-insights-monitor-web-app-availability.md) con Application Insights, para poder averiguar si su página está inactiva.
 

<!---HONumber=AcomDC_0128_2016-->