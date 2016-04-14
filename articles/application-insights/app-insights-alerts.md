<properties 
	pageTitle="Definición de alertas en Application Insights" 
	description="Reciba mensajes de correo electrónico sobre bloqueos, excepciones y cambios de métrica." 
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
	ms.date="03/01/2016" 
	ms.author="awills"/>
 
# Definición de alertas en Application Insights

[Visual Studio Application Insights][start] pueden alertarle de los cambios en las métricas de rendimiento o de uso de la aplicación.

Application Insights supervisa la aplicación activa en una [amplia variedad de plataformas][platforms] para ayudarle a diagnosticar problemas de rendimiento y entender los patrones de uso.

Hay dos tipos de alertas:
 
* Las **pruebas web** le informan de que el sitio no está disponible en Internet o responde lentamente. [Más información][availability].
* Las **alertas de métricas** le avisan cuando una métrica cruza un valor de umbral durante un período determinado: por ejemplo, recuentos de error, memoria o vistas de página. 

Hay una [página aparte sobre las pruebas web][availability], por lo que aquí nos centraremos en las alertas de métricas.

> [AZURE.NOTE] Además, puede recibir correos electrónicos de [detección proactiva](app-insights-proactive-detection.md), para avisarle de patrones inusuales en el rendimiento de su aplicación. A diferencia de las alertas, estas notificaciones se ejecutan sin necesidad de configurarlas. Están destinadas a optimizar el rendimiento de la aplicación, en lugar de alertar sobre problemas inmediatos.

## Alertas de métricas

Si aún no ha configurado Application Insights para su aplicación, [hágalo primero][start].

Para recibir un correo electrónico cuando una métrica cruce un umbral, inicie desde el Explorador de métricas o desde el icono Reglas de alerta en la hoja de información general.

![En la hoja Reglas de alerta, elija Agregar alerta. Establezca la aplicación como el recurso que se va a medir, proporcione un nombre para la alerta y elija una métrica.](./media/app-insights-alerts/01-set-metric.png)

* Establezca el recurso antes de las demás propiedades. **Elija el recurso "(components)"** si desea establecer alertas sobre métricas de rendimiento o de uso.
* Asegúrese de tener en cuenta las unidades en las que se le pide que escriba el valor de umbral.
* El nombre que asigne a la alerta debe ser único dentro del grupo de recursos (no solo en la aplicación).
* Si activa la casilla "Propietarios de correo electrónico...", se enviarán alertas por correo electrónico a todos los usuarios con acceso a este recurso.
* Si especifica "Correos electrónicos adicionales", se enviarán alertas a esos usuarios o grupos (independientemente de que haya activado la casilla "Propietarios de correo electrónico"). 
* Establezca una [dirección de Webhook](../azure-portal/insights-webhooks-alerts.md) si ha configurado una aplicación web para responder a las alertas. Se llamará a esta dirección cuando se active la alerta (es decir, cuando se desencadene) y cuando se haya resuelto.
* Puede habilitar o deshabilitar la alerta: consulte los botones de la parte superior de la hoja.

*No veo el botón Agregar alerta.* ¿Está usando una cuenta de organización? Puede establecer alertas si tiene acceso de propietario o colaborador a este recurso de aplicación. Eche un vistazo a Configuración -> Usuarios. [Más información sobre el control de acceso][roles].

## Visualización de alertas

Recibirá un correo electrónico cuando un alerta cambia el estado entre inactivo y activo.

En la hoja de reglas Alerta se muestra el estado actual de cada alerta.

Hay un resumen de la actividad reciente en la lista desplegable de alertas:

![](./media/app-insights-alerts/010-alert-drop.png)

El historial de cambios de estado está en el registro Eventos de operaciones:

![En la hoja Información general, cerca de la parte inferior, haga clic en 'Eventos de la semana pasada'.](./media/app-insights-alerts/09-alerts.png)

*¿Estos "eventos" están relacionados con eventos de telemetría o eventos personalizados?*

* No. Estos eventos operativos son simplemente un registro de cosas que han ocurrido en este recurso de aplicación. 


## Funcionamiento de las alertas

* Una alerta tiene tres estados: "Nunca activada", "Activada" y "Resuelta". "Activada" significa que la condición especificada tenía el valor true cuando se evaluó por última vez.

* Se genera una notificación cuando una alerta cambia de estado. (Si la condición de alerta ya tenía el valor true cuando creó la alerta, es posible que no reciba una notificación hasta que la condición cambie al valor false).

* Si ha activado la casilla de correos electrónicos o si ha proporcionado direcciones de correo electrónico, cada notificación generará un correo. También puede consultar la lista desplegable de notificaciones.

* Una alerta se evalúa cada vez que llega una métrica, pero no en caso contrario.

* La evaluación agrega la métrica durante el período anterior y, luego, la compara con el umbral para determinar el nuevo estado.

* El período que elija especifica el intervalo en el que se agregan métricas. No afecta a la frecuencia con la que se evalúa la alerta: depende de la frecuencia de llegada de métricas.

* Si no llega ningún dato para una métrica concreta durante un tiempo, este intervalo tiene efectos diferentes en la evaluación de la alerta y en los gráficos del Explorador de métricas. En el Explorador de métricas, si no se ve ningún dato durante más tiempo que el intervalo de muestreo del gráfico, el gráfico mostrará un valor de 0. Pero una alerta basada en la misma métrica no se volverá a evaluar, y estado de la alerta permanecerá sin cambios.

    Cuando finalmente llegan datos, el gráfico se desplazará a un valor distinto de cero. La alerta se evaluará en función de los datos disponibles para el período especificado. Si el nuevo punto de datos es el único disponible en el período, el agregado se basará en eso.

* Una alerta puede parpadear con frecuencia entre los estados de alerta y correcto, incluso si se establece un período largo. Esto puede suceder si el valor de métrica se sitúa alrededor del umbral. No hay ninguna histéresis en el umbral: la transición a alerta se produce en el mismo valor que la transición a correcto.



## Alertas de disponibilidad

Puede configurar pruebas web que prueben cualquier sitio web desde diferentes puntos alrededor del mundo. [Más información][availability].

## ¿Qué alertas es conveniente establecer?

Depende de la aplicación. Para empezar, es mejor no establecer demasiadas métricas. Observe durante un tiempo sus gráficos de métrica mientras se ejecuta la aplicación para hacerse una idea de cómo se comporta normalmente. Esto le ayudará a encontrar maneras de mejorar su rendimiento. A continuación, configure alertas para que le avisen cuando las métricas salgan de la zona normal.

Las alertas más populares son:

* Las [pruebas web][availability] son importantes si la aplicación es un servicio web o un sitio web que está visible en Internet de acceso público. Le indican si el sitio deja de funcionar o responde lentamente, incluso si el problema es del operador y no de la aplicación. Sin embargo, son pruebas sintéticas, por lo que no miden la experiencia real de los usuarios.
* Las [métricas del explorador][client], especialmente los **tiempos de carga de página del explorador**, son buenas para aplicaciones web. Si la página tiene una gran cantidad de scripts, deberá tener en cuenta las **excepciones del explorador**. Para obtener estas métricas y alertas, tiene que configurar [la supervisión de páginas web][client].
* **Tiempo de respuesta del servidor** y **Solicitudes incorrectas** para las aplicaciones web del lado servidor. Además de configurar alertas, eche un vistazo a estas métricas para ver si varían desproporcionadamente con tasas de solicitud altas: esto puede indicar que la aplicación se está quedando sin recursos.
* **Excepciones de servidor**: para verlas, deberá realizar alguna [configuración adicional](app-insights-asp-net-exceptions.md).

## Automatización

* [Uso de PowerShell para automatizar la configuración de alertas](app-insights-powershell-alerts.md)
* [Uso de Webhook para automatizar la respuesta a alertas](../azure-portal/insights-webhooks-alerts.md)

## Consulte también

* [Pruebas web de disponibilidad](app-insights-monitor-web-app-availability.md)
* [Use PowerShell to set alerts in Application Insights (Uso de PowerShell para definir alertas en Application Insights)](app-insights-powershell-alerts.md)
* [Application Insights: detección proactiva](app-insights-proactive-detection.md) 



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0302_2016-->