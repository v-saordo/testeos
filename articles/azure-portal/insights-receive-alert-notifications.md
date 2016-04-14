<properties 
	pageTitle="Recibir notificaciones de alerta" 
	description="Recibir una notificación cuando se cumplan las condiciones de las reglas de alerta." 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
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

# Recibir notificaciones de alerta

Puede recibir una alerta basada en las métricas de supervisión para los servicios de Azure o los eventos sobre ellos.

Para una regla de alerta en un valor de métrica, cuando el valor de una métrica específica cruza un umbral asignado, la regla de alerta se activa y puede enviar una notificación. Para una regla de alerta en eventos, una regla puede enviar una notificación sobre *cada* evento o solo cuando se produzca un determinado número de eventos.

Cuando cree una regla de alerta, puede seleccionar opciones para enviar una notificación por correo electrónico al administrador y coadministradores del servicio o a otro administrador que pueda especificar. Se envía un correo electrónico de notificación cuando se activa la regla y cuando se resuelve una condición de alerta.

Puede usar la [API de REST](https://msdn.microsoft.com/library/azure/dn931945.aspx) o el [SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para configurar y obtener información acerca de las reglas de alertas mediante programación.

## Crear una regla de alerta

1. En el [portal](https://portal.azure.com/), haga clic en **Examinar** y luego en el recurso que le interese supervisar.

2. En el modo **Operaciones**, haga clic en **Reglas de alerta**.

3. Haga clic en el comando **Agregar alerta**.
    ![Agregar alerta](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. Puede asignar un nombre a la regla de alerta y elegir una descripción que se mostrará en el correo electrónico de notificación.

5. Cuando seleccione **Métricas**, elegirá una condición y el valor de umbral para la métrica. Es el período de tiempo que Azure usa para supervisar y trazar actividades de alerta.
    ![Condición y umbral](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. También puede elegir **Eventos** y obtener una notificación cuando se produzca un evento determinado. 
    ![Eventos](./media/insights-receive-alert-notifications/Insights_Events.png)
    

7. Por último, puede enviar una notificación de correo electrónico a los administradores responsables.

Después de hacer clic en **Guardar**, al cabo de unos minutos se le informará si la métrica que eligió supera el umbral.

## Administrar las reglas de alertas

Cuando haya creado una regla de alerta, podrá ver una vista previa del umbral de alertas en comparación con la métrica del día anterior.

![Eventos](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


Por supuesto, puede editar esta regla de alerta y elegir **Deshabilitar** o **Habilitar** si desea detener temporalmente la recepción de notificaciones sobre ella.

## Pasos siguientes

* [Configure enlaces web en las alertas](insights-webhooks-alerts.md) para enrutar las notificaciones a diversos canales
* [Supervise las métricas de servicio](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y que responde adecuadamente.
* [Habilite la supervisión y el diagnóstico](insights-how-to-use-diagnostics.md) para recopilar métricas detalladas de alta frecuencia en su servicio.
* [Supervise la disponibilidad y la capacidad de respuesta de cualquier página web](../application-insights/app-insights-monitor-web-app-availability.md) con Application Insights, para poder averiguar si su página está inactiva.
* [Supervise el rendimiento de la aplicación](insights-perf-analytics.md) si desea comprender exactamente cómo está actuando su código en la nube.
* [Vea eventos y registros de auditoría](insights-debugging-with-events.md) para saber todo lo que ha sucedido en el servicio.
* [Realice el seguimiento del estado del servicio](insights-service-health.md) para averiguar cuándo ha sufrido Azure interrupciones del servicio o degradación del rendimiento.
 

<!---HONumber=AcomDC_0218_2016-->