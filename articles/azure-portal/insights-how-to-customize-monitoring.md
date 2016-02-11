<properties 
	pageTitle="Supervisión de las métricas del servicio" 
	description="Obtenga información acerca de cómo personalizar los gráficos de supervisión en Azure." 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
	editor="" 
	services="azure-portal"
documentationCenter=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2015" 
	ms.author="stepsic"/>

# Supervisión de las métricas del servicio

Todos los servicios de Azure realizan un seguimiento de las métricas clave que le permiten supervisar el estado, el rendimiento, la disponibilidad y el uso de los servicios. Puede ver estas métricas en el portal de Azure y, además, puede utilizar la [API de REST](https://msdn.microsoft.com/library/azure/dn931930.aspx) o el [SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para tener acceso mediante programación a todo el conjunto de métricas.

Para algunos servicios, puede que necesite activar diagnósticos para ver las métricas. Para otros, como las máquinas virtuales, obtendrá un conjunto básico de métricas, pero necesita habilitar el conjunto completo de métricas de alta frecuencia. Consulte [Habilitación de supervisión y diagnóstico](insights-how-to-use-diagnostics.md) para obtener más información.

## Uso de gráficos de supervisión 

Puede representar en gráficos cualquier métrica durante cualquier período de tiempo que elija.

1. En el [Portal de Azure](https://portal.azure.com/), haga clic en **Examinar** y luego en el recurso que le interese supervisar.

2. La sección **Supervisión** contiene las métricas más importantes para cada recurso de Azure. Por ejemplo, una aplicación web tiene **Solicitudes y errores** donde, como máquina virtual, tendría **Porcentaje de CPU** y **Lectura y escritura de disco**: ![Modo Monitoring](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. Al hacer clic en cualquier gráfico aparecerá la hoja **Métrica**. En el cuadro, además del gráfico, hay una tabla que muestra las agregaciones de las métricas (como promedio, mínimo y máximo, durante el intervalo de tiempo que ha elegido). A continuación se muestran las reglas de alerta para el recurso. ![Cuadro de métricas](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. Para personalizar las líneas que aparecen, haga clic en el botón **Editar** del gráfico o en el comando **Editar gráfico** de la hoja Métrica.

5. En la hoja Editar consulta, puede hacer tres cosas:
    - Cambiar el intervalo de tiempo
    - Cambiar la apariencia entre líneas y barras
    - Elegir otras métricas ![Editar consulta](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. Cambiar el intervalo de tiempo es tan fácil como seleccionar un intervalo diferente (como **Hora pasada**) y hacer clic en **Guardar** en la parte inferior de la hoja. También puede elegir **Personalizado**, que le permite elegir cualquier período de tiempo durante las últimas dos semanas. Por ejemplo, puede ver las dos semanas completas o, simplemente, una hora de ayer. Para especificar una hora diferente, escríbala en el cuadro de texto. ![Intervalo de tiempo personalizado](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. Debajo del intervalo de tiempo, puede elegir el número de métricas que desea mostrar en el gráfico.

8. Al hacer clic en Guardar, los cambios se guardarán para ese recurso concreto. Por ejemplo, si tiene dos máquinas virtuales y cambiar un gráfico en una de ellas, la otra no resultará afectada.

## Creación de gráficos paralelos

Con las potentes opciones de personalización del portal, puede agregar tantos gráficos como desee.

1. En el menú **...** de la parte superior de la hoja, haga clic en **Agregar iconos**: ![Adición de menú](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. A continuación, se puede seleccionar un gráfico desde la **Galería** en el lado derecho de la pantalla: ![Galería](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Si no ve la métrica que desea, siempre puede agregar una de las métricas presentes y **Editar** el gráfico para que aparezca la métrica que necesita. 

## Supervisión de las cuotas de uso

La mayoría de las métricas muestran tendencias a lo largo del tiempo, pero determinados datos, como las cuotas de uso, suponen información puntual con un umbral.

También puede ver las cuotas de uso en la hoja de los recursos que tienen cuotas:

![Uso](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Al igual que con las métricas, puede utilizar la [API de REST](https://msdn.microsoft.com/library/azure/dn931963.aspx) o el [SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para tener acceso a todo el conjunto de cuotas de uso mediante programación.

## Pasos siguientes

* [Reciba notificaciones de alerta](insights-receive-alert-notifications.md) cada vez que una métrica traspase un umbral.
* [Habilite la supervisión y el diagnóstico](insights-how-to-use-diagnostics.md) para recopilar métricas detalladas de alta frecuencia en su servicio.
* [Escale el número de instancias automáticamente](insights-how-to-scale.md) para asegurarse de que el servicio está disponible y tiene capacidad de respuesta.
* [Supervise el rendimiento de la aplicación](insights-perf-analytics.md) si desea comprender exactamente cómo está actuando su código en la nube.
* Utilice [aplicaciones y páginas web de Application Insights para JavaScript](../app-insights-web-track-usage.md) para obtener el análisis del cliente acerca de los exploradores que visitan una página web.
* [Supervise la disponibilidad y la capacidad de respuesta de cualquier página web](../app-insights-monitor-web-app-availability.md) con Application Insights, para poder averiguar si su página está inactiva.
 

<!---HONumber=Oct15_HO3-->