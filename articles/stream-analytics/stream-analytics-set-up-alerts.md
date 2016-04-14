<properties 
	pageTitle="Configuración de alertas para consultas en Análisis de transmisiones | Microsoft Azure" 
	description="Descripción de las entradas del Análisis de transmisiones" 
	keywords="configuración de alertas"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/04/2016" 
	ms.author="jeffstok"/>


# Configuración de alertas en Análisis de transmisiones de Azure

## Introducción: Página de supervisión

Puede configurar alertas para desencadenar una alerta cuando una métrica alcance una condición que especifique.

Por ejemplo, "Si los eventos de salida para los últimos 15 minutos son <100, enviar notificación por correo electrónico al identificador de correo electrónico: xyz@company.com”.

Las reglas se pueden configurar en las métricas a través del portal o se pueden configurar [mediante programación](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sobre datos de registros de operaciones.

## Configuración de alertas a través del Portal de Azure

Hay dos maneras de configurar alertas en el Portal de administración de Azure:

1.	La pestaña **Supervisar** del trabajo de Análisis de transmisiones  
2.	El registro de operaciones en los servicios de administración  

## Configuración de alertas a través de la pestaña Supervisar del trabajo en el portal

1.	Seleccione la métrica en la pestaña Supervisar, haga clic en el botón **Agregar regla** en la parte inferior del panel y configure las reglas.  

    ![Panel](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)

2.	Definir un nombre y una descripción de la alerta

    ![Crear regla](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)

3.	Especificar los umbrales, la ventana de evaluación de alerta y las acciones para la alerta

    ![Definir condiciones](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)

## Configuración de alertas a través de registros de operaciones

1.	Vaya a la pestaña **Alertas** en Servicios de administración en el [Portal de Azure](https://manage.windowsazure.com).  
2.	Haga clic en **Agregar regla**.  

    ![Criterios](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)

3.	Defina el nombre y una descripción de la alerta. Seleccione "Análisis de secuencia" como tipo de servicio y el nombre del trabajo como el nombre del servicio.

    ![Definir alerta](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)

## Configuración de alertas en el Portal de vista previa de Azure ##

En el Portal de vista previa de Azure, vaya al trabajo de Análisis de transmisiones en el que está interesado en las alertas y haga clic en la sección **Supervisión**. En la hoja **Métrica** que se abre, haga clic en el comando **Agregar alerta**.

  ![Portal de vista previa de Azure - Configurar](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)

Puede asignar un nombre a la regla de alerta y elegir una descripción que se mostrará en el correo electrónico de notificación.

Cuando seleccione Métricas, elegirá una condición y el valor de umbral para la métrica.

  ![Portal de vista previa de Azure - Seleccionar métrica](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)

Para obtener más información sobre la configuración de alertas en el Portal de vista previa de Azure, consulte [Recibir notificaciones de alerta](./azure-portal/insights-receive-alert-notifications.md).

## Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/es-ES/home?forum=AzureStreamAnalytics)

## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0204_2016-->