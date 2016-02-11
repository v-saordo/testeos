<properties 
	pageTitle="Realización de seguimiento del estado " 
	description="Averigüe en qué momentos ha sufrido Azure interrupciones del servicio o degradación del rendimiento." 
	authors="stepsic-microsoft-com" 
	manager="kamrani" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2015" 
	ms.author="stepsic"/>

# Realización de seguimiento del estado

Azure realiza un anuncio cada vez que hay una interrupción del servicio o una degradación del rendimiento. Estos eventos se pueden examinar en el Portal de Azure y también se pueden utilizar la [API de REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) o el [SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para obtener acceso mediante programación a todo el conjunto de eventos.

## Ver el estado del servicio por región

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. En **Inicio** debería ver un icono denominado **Estado del servicio** ![Inicio](./media/insights-service-health/Insights_Home.png)

3. Al hacer clic en él, aparecerá una lista de todas las regiones de Azure. Puede hacer clic en cualquier región para ver el historial de estados del servicio de dicha región. ![Inicio](./media/insights-service-health/Insights_Regions.png)

4. También puede ver los detalles de cualquier incidente individual, para lo que debe hacer clic en el mismo en la tabla.

## Examen de los registros completos del estado del servicio

2. Haga clic en **Examinar** y seleccione **Registros de auditoría**. ![Centro de exploración](./media/insights-service-health/Insights_Browse.png)

3. Se abrirá una hoja en la que se muestran todos los eventos que han influido en cualquiera de sus suscripciones durante los últimos 7 días. Las entradas del estado del servicio aparecerán en esta lista, pero puede que sea difícil encontrarlas, ya que la lista puede ser grande.

4. Haga clic en el comando **Filtro**.

5. Seleccione **Categoría de eventos** y elija **Estado del servicio**: ![Todos los eventos](./media/insights-service-health/Insights_Filter.png)

6. Haga clic en **Actualizado**.

7. Verá todos los eventos del estado del servicio que han afectado a la suscripción: ![Grupos de recursos](./media/insights-service-health/Insights_HealthEvent.png)

8. Desde ahí puede ir a la hoja de detalles para ver las particularidades del evento.
   
## Pasos siguientes

* [Reciba notificaciones de alerta](insights-receive-alert-notifications.md) cada vez que se produzca un evento.
* [Supervise las métricas de servicio](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y que responde adecuadamente.
* [Supervise la disponibilidad y la capacidad de respuesta de cualquier página web](../app-insights-monitor-web-app-availability.md) con Application Insights, para poder averiguar si su página está inactiva.
 

<!---HONumber=Oct15_HO3-->