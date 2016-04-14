<properties 
	pageTitle="Diagnóstico proactivo en tiempo casi real en Application Insights" 
	description="El diagnóstico proactivo NRT le notifica automáticamente si el tiempo de respuesta del servidor muestra un comportamiento inusual. No necesita ninguna configuración." 
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
	ms.date="02/16/2016" 
	ms.author="awills"/>
 
# Diagnóstico proactivo en tiempo casi real

*Esta característica se encuentra en las primeras versiones.*

*Envíe sus comentarios a:* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com)

[Application Insights para Visual Studio](app-insights-overview.md) le notifica automáticamente en tiempo casi real si la tasa de solicitudes con errores de la aplicación web aumenta significativamente. Para ayudarle a evaluar y diagnosticar el problema, en la notificación se proporciona un análisis de las características de las solicitudes con errores y la telemetría relacionada. También hay vínculos en el portal de Application Insights para obtener un diagnóstico más amplio. La característica no necesita ninguna instalación o configuración, ya que usa algoritmos de aprendizaje automático para predecir la tasa normal de errores de línea base. Necesita un determinado volumen mínimo de tráfico para que funcione.

Esta es una alerta de ejemplo:

![El ejemplo de alerta inteligente muestra el análisis del clúster sobre el error](./media/app-insights-nrt-proactive-diagnostics/010.png)

Para obtener alertas de error, debe configurar [Application Insights para ASP.NET](app-insights-asp-net.md) o [para su aplicación web de Java](app-insights-java-get-started.md).

## Cómo funciona

El diagnóstico proactivo en tiempo casi real supervisa la telemetría recibida de su aplicación y, en particular, la tasa de solicitudes con errores. Esta métrica indica normalmente el número de solicitudes HTTP que devuelven un código de respuesta de 400 o superior (a no ser que escribiera un código personalizado para [filtrar](app-insights-api-filtering-sampling.md#filtering) o generar sus propias llamadas [TrackRequest](app-insights-api-custom-events-metrics.md#track-request)).

Según el comportamiento anterior de esta métrica, el servicio predice qué intervalo de valores deben esperarse. Si el valor real es significativamente superior, genera una alerta.

Cuando se emite una alerta, el servicio realiza un análisis del clúster en varias dimensiones de las solicitudes, para intentar identificar un patrón de valores que caracterice los errores. En el ejemplo anterior, el análisis detectó que la mayoría de los errores están relacionados con un nombre de solicitud específico, pero se descubrió que los errores son independientes de la instancia de host o de servidor.

A continuación, el analizador busca excepciones y errores de dependencia que estén asociados a solicitudes en el clúster que identificó, junto con un ejemplo de cualquier registro de seguimiento asociado a esas solicitudes.

El análisis resultante se le envía por correo electrónico, a no ser que configurara lo contrario.

Al igual que las [alertas que establece manualmente](app-insights-alerts.md), puede inspeccionar el estado de la alerta y configurarla en la hoja Alertas del recurso de Application Insights. Pero, a diferencia de otras alertas, no es necesario instalar ni configurar una alerta de error adaptable. Si lo desea, puede deshabilitarla o cambiar sus direcciones de correo electrónico de destino.

## Si recibe una alerta

Una alerta indica que se detectó una anomalía en la tasa de solicitudes con errores. Es probable que haya algún problema con la aplicación o con su entorno. Quizás la nueva versión que acaba de cargar no funcione demasiado bien; o quizás una dependencia, como una base de datos o un recurso externo, no funcione correctamente.

Puede determinar la urgencia del problema a partir del porcentaje de solicitudes y del número de usuarios afectados.

En muchos casos, podrá diagnosticar el problema rápidamente a partir del nombre de la solicitud, de las excepciones, de las dependencias y de otros datos proporcionados.

Pero si necesita investigar más, los vínculos de cada sección le llevarán directamente a una [página de búsqueda](app-insights-diagnostic-search.md) filtrada por las solicitudes, la excepción, la dependencia o el seguimiento relevantes. O bien, puede abrir el [Portal de Azure](https://portal.azure.com), navegar hasta el recurso de Application Insights de su aplicación y abrir la hoja Problemas.

## Revisar las alertas recientes

Para revisar las alertas en el portal, abra **Configuración, eventos operativos**.

![Resumen de alertas](./media/app-insights-nrt-proactive-diagnostics/040.png)

Haga clic en cualquier alerta para ver sus detalles completos.


## Configurar alertas 

> **La experiencia de usuario de configuración no está disponible todavía.**
> 
> En su lugar, envíe un correo electrónico a [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com).
>
> Así es como podría funcionar:

Abra la página Alertas. Se incluye una alerta de error adaptable en cualquier alerta configurada manualmente, y puede ver si está actualmente en el estado de alerta.

![En la página de información general, haga clic en el icono Alertas. O bien, en cualquier página de métricas, haga clic en el botón Alertas.](./media/app-insights-nrt-proactive-diagnostics/020.png)

Haga clic en la alerta para configurarla.

![Configuración](./media/app-insights-nrt-proactive-diagnostics/030.png)

Observe que puede deshabilitar la alerta de error adaptable, pero no puede eliminarla (ni crear otra).


## ¿Cuál es la diferencia ...

El diagnóstico proactivo NRT complementa a otras características diferentes pero similares de Application Insights.

* [Las alertas de métricas](app-insights-alerts.md) las configura el usuario y pueden supervisar una amplia variedad de métricas, como el uso de la CPU, las tasas de solicitudes y los tiempos de carga de las páginas, entre otras. Puede usarlas para avisarle, por ejemplo, de si necesita agregar más recursos. Por el contrario, el diagnóstico proactivo NRT abarca un pequeño conjunto de métricas críticas (actualmente solo la tasa de solicitudes con errores), diseñadas para notificarle en tiempo casi real el momento en el que la tasa de solicitudes con errores de la aplicación web aumenta significativamente en comparación con el comportamiento normal de esta.
* [La detección proactiva](app-insights-proactive-detection.md) también usa la inteligencia automática para descubrir patrones inusuales en las métricas, y no requiere ninguna configuración por su parte. Pero, a diferencia del diagnóstico proactivo NRT, la finalidad de la detección proactiva es encontrar segmentos del colector de uso que pudieran haberse servido incorrectamente, por ejemplo, por páginas concretas de un tipo específico de explorador. El análisis se realiza diariamente y, si se encuentra algún resultado, probablemente sea mucho menos urgente que una alerta. Por el contrario, el análisis del diagnóstico proactivo NRT se realiza continuamente en la telemetría entrante, y se le notificará en unos minutos si las tasas de errores del servidor son mayores de lo esperado.


## Queremos sus comentarios

*Esta característica se encuentra aún en fase de evaluación. Estamos muy interesados en recibir sus comentarios. Envíe sus comentarios a:* [ainrtpd@microsoft.com](mailto:ainrtpd@microsoft.com) *o haga clic en el vínculo de comentarios en el mensaje de alerta.*

<!---HONumber=AcomDC_0218_2016-->