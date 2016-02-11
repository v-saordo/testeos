<properties 
	pageTitle="Visualización de eventos y registros de auditoría" 
	description="Obtenga información acerca de cómo ver todos los eventos que se producen en su suscripción de Azure." 
	authors="HaniKN-MSFT" 
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
	ms.date="04/28/2015" 
	ms.author="hanikn"/>

# Visualización de eventos y registros de auditoría

Todas las operaciones realizadas en recursos de Azure se auditan por completo por parte del Administrador de recursos de Azure, desde las creaciones y eliminaciones hasta la concesión o revocación de accesos. Puede examinar estos registros en el portal de Azure y, además, puede utilizar la [API de REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) o [el SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para tener acceso mediante programación a todo el conjunto de eventos.

## Examen de los eventos que afectan a su suscripción de Azure

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en el **Examinar** y seleccione **Registros de auditoría**. ![Centro de exploración](./media/insights-debugging-with-events/Insights_Browse.png)
3. Así, se abrirá una hoja en la que se muestran todos los eventos que han influido en cualquiera de sus suscripciones durante los últimos 7 días. En la parte superior aparece un gráfico que muestra los datos por nivel, y debajo está la lista completa de registros: ![Todos los eventos](./media/insights-debugging-with-events/Insights_AllEvents.png)

>[AZURE.NOTE]Solo puede ver los 500 eventos más recientes de una determinada suscripción en el portal de Azure.

4. Puede hacer clic en cualquier entrada de registro para ver los eventos que la componen. Por ejemplo, cuando se implementa algo en un grupo de recursos, pueden crearse o modificarse muchos recursos diferentes. Para cada entrada se puede ver lo siguiente:
    * El **Nivel** del evento: por ejemplo, podría ser simplemente algo a lo que realizar un seguimiento (**Informativo**) o algo que ha salido mal y de lo que debe ser consciente (**Error**). 
    * El **Estado**: el estado final suele ser **Correcto** o **Con errores**, pero también puede ser **Aceptado** para operaciones de larga duración.
    * *Cuándo* se produjo el evento.
    * *Quién* realizó la operación, si lo hizo alguien. No todas las operaciones se realizan por usuarios; algunas de ellas se realizan por servicios back-end, por lo que no tendrían un **Autor de llamada**.
    * El **Id. de correlación** del evento: este es el identificador único para este conjunto de operaciones.

5. Desde ahí puede ir a la hoja de detalles para ver las particularidades del evento.
   
    ![Grupos de recursos](./media/insights-debugging-with-events/Insights_EventDetails.png)

    En el caso de los eventos **Con errores**, esta página suele incluir una sección **Subestado** y otra **Propiedades** que contienen datos útiles de cara al proceso de depuración.

## Filtrado a registros específicos

A fin de ver los eventos que se aplican a una entidad específica, o de un tipo específico, puede filtrar la hoja Registros de auditoría haciendo clic en el comando **Filtrar**. También usar la hoja Filtrar para cambiar el **Intervalo de tiempo** de la hoja Registros de auditoría.

Una vez que se hace clic en este comando, se abrirá una nueva hoja:

![Filtrar](./media/insights-debugging-with-events/Insights_EventFilter.png)

Hay cuatro tipos de filtros:

1. Por suscripción
2. Por un **Grupo de recursos**
3. Por un **Tipo de recurso**
4. Por un **Recurso** particular: para ello debe pasar el *Id. de recurso* completo del recurso en el que esté interesado

Además, también puede filtrar los eventos por la persona que realizó el evento, o bien, por el nivel del evento.

Una vez que haya terminado de elegir lo que desea ver, haga clic en el botón **Actualizar** situado en la parte inferior de la hoja.

## Supervisión de los eventos que afectan a recursos específicos

1. Haga clic en **Examinar** para buscar el recurso que le interesa. También puede ver todos los registros de un **Grupo de recursos** completo.
2. En la hoja del recurso, desplácese hacia abajo hasta que encuentre el icono **Eventos**. ![Icono de eventos](./media/insights-debugging-with-events/Insights_EventsTile.png)
3. Haga clic en ese icono para ver eventos filtrados solo por el recurso seleccionado. Puede utilizar el comando **Filtrar** para cambiar el intervalo de tiempo o aplicar filtros más específicos.

## Pasos siguientes

* [Reciba notificaciones de alerta](insights-receive-alert-notifications.md) cada vez que se produzca un evento.
* [Supervise las métricas de servicio](insights-how-to-customize-monitoring.md) para asegurarse de que el servicio está disponible y que responde adecuadamente.
* [Realice el seguimiento del estado del servicio](insights-service-health.md) para averiguar cuándo ha sufrido Azure interrupciones del servicio o degradación del rendimiento.  

<!---HONumber=Oct15_HO3-->