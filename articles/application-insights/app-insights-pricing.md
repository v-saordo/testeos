<properties 
	pageTitle="Administración de precios y cuotas para Application Insights" 
	description="Elija el plan de precios que necesite." 
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
	ms.date="01/15/2016" 
	ms.author="awills"/>

# Administración de precios y cuotas para Application Insights

*Application Insights se encuentra en su versión de vista previa.*

Los [precios][pricing] de [Application Insights para Visual Studio][start] se basan en el volumen de datos por aplicación. Hay un nivel Gratis sustancial en el que se pueden disfrutar la mayoría de las características con algunas limitaciones.

Cada recurso de Application Insights se cobra como un servicio independiente y contribuye a la factura de la suscripción a Azure.

[Consulte el esquema de precios][pricing].

## Revisión del plan de cuotas y precios para el recurso de Application Insights

Puede abrir la hoja Cuota + Precios de la configuración del recurso de la aplicación.

![Elija Configuración, Cuota + Precios.](./media/app-insights-pricing/01-pricing.png)

Su elección del esquema de precios afecta a:

* [Cuota mensual](#monthly-quota): la cantidad de telemetría que puede analizar cada mes.
* [Velocidad de datos](#data-rate): la velocidad máxima a la que se pueden procesar los datos de su aplicación.
* [Retención](#data-retention): cómo se mantienen los datos Long en el portal de Application Insights para que los vea.
* [Exportación continua](#continuous-export): si se pueden exportar datos a otras herramientas y servicios.

Estos límites se establecen por separado para cada recurso de Application Insights.

### Evaluación gratuita de Premium

Al crear un nuevo recurso de Application Insights, este se inicia en el nivel Gratis.

En cualquier momento, puede cambiar a la evaluación gratuita de Premium de 30 días. Esto le ofrecerá las ventajas del nivel Premium. Después de 30 días, la suscripción cambiará automáticamente al nivel en que se encontrase previamente, a menos que se elija explícitamente otro nivel. Puede seleccionar el nivel que desee en cualquier momento durante el período de evaluación, pero podrá seguir optando a la evaluación gratuita hasta el final del período de 30 días.


## Cuota mensual

* En cada mes natural, la aplicación puede enviar hasta una cantidad especificada de telemetría a Application Insights. Actualmente la cuota para el plan de tarifa gratuito es de 5 millones de puntos de datos al mes y mucho más para los demás esquemas; puede comprar más si alcanza la cuota. Consulte el [esquema de precios][pricing] para ver los números reales. 
* La cuota depende del nivel de precios que haya elegido.
* La cuota se cuenta a partir de la medianoche UTC del primer día de cada mes.
* El gráfico de puntos de datos muestra el uso que se ha hecho de la cuota en este mes.
* La cuota se mide en *puntos de datos.* Un único punto de datos es una llamada a uno de los métodos de seguimiento, independientemente de si se llama explícitamente en el código o por uno de los módulos de telemetría estándar. 
* Los puntos de datos se generan por:
 * [Módulos de SDK](app-insights-configuration-with-applicationinsights-config.md) que recopilan datos de forma automática, por ejemplo, para informar de una solicitud o un bloqueo, o para medir el rendimiento.
 * Las llamadas a [API](app-insights-api-custom-events-metrics.md) `Track...` escritas, como `TrackEvent` o `trackPageView`.
 * [Las pruebas web de disponibilidad](app-insights-monitor-web-app-availability.md) que configuró.
* Durante la depuración, puede ver los puntos de datos que se envían desde la aplicación en la ventana de salida de Visual Studio. Los eventos de cliente se pueden ver abriendo que la pestaña de red en el panel de depuración del explorador (normalmente F12).
* Los *datos de la sesión* no se cuentan en la cuota. Esto incluye los recuentos de datos de usuarios, sesiones, entorno y dispositivo.
* Si desea contar puntos de datos por inspección, puede encontrarlos en diversos lugares:
 * Cada elemento que ve en la [búsqueda de diagnósticos](app-insights-diagnostic-search.md), que incluye solicitudes HTTP, excepciones, seguimientos de registros, vistas de página, eventos de dependencia y eventos personalizados.
 * Cada medida sin procesar de una [métrica](app-insights-metrics-explorer.md), como un contador de rendimiento. (Los puntos que se ven en los gráficos normalmente son agregados de varios puntos de datos sin procesar).
 * Cada punto de un gráfico de disponibilidad web es también un agregado de varios puntos de datos.
* También puede inspeccionar puntos de datos individuales en el origen durante la depuración:
 * Si ejecuta su aplicación en el modo de depuración en Visual Studio, los puntos de datos se registran en la ventana de salida. 
 * Para ver los puntos de datos de cliente, abra panel de depuración del explorador (normalmente F12) y abra la ficha Red.
* La velocidad de datos se reduce (de forma predeterminada) mediante el [muestreo adaptable](app-insights-sampling). Esto significa que, a medida que aumente el uso de su aplicación, la velocidad de telemetría no incrementará tanto como cabría esperar.

### Superávit

Si una aplicación envía más de la cuota mensual, puede:

* Pagar por datos adicionales. Consulte el [esquema de precios][pricing] para obtener más información. Puede elegir esta opción de antemano. Esta opción no está disponible en el nivel de precios Gratis.
* Actualización del nivel de precios
* No haga nada. Los datos de la sesión seguirán registrándose, pero otros datos no aparecerán en la búsqueda de diagnóstico o en el explorador de métricas.


### ¿Cuántos datos estoy enviando?

El gráfico de la parte inferior de la hoja de precios muestra el volumen del punto de datos de la aplicación con grupos en función del tipo de punto de datos. (También puede crear este gráfico en el Explorador de métricas).

![En la parte inferior de la hoja de precios.](./media/app-insights-pricing/03-allocation.png)

Haga clic en el gráfico para obtener más detalles, o arrastre el puntero por él y haga clic en (+) para ver el detalle de un intervalo de tiempo.

El gráfico muestra el volumen de datos que llega al servicio Application Insights, después del [muestreo](app-insights-sampling).


## Velocidad de datos

Además de la cuota mensual, estos valores limitan la velocidad de los datos. Para el [plan de tarifa][pricing] gratuito, el límite es de 200 puntos de datos por segundo de promedio durante 5 minutos, y para los niveles de pago es de 500/s de promedio durante 1 minuto.

Hay tres depósitos que se cuentan por separado:

* [Llamadas a TrackTrace](app-insights-api-custom-events-metrics.md#track-trace) y [registros capturados](app-insights-asp-net-trace-logs.md)
* [Excepciones](app-insights-api-custom-events-metrics.md#track-exception), limitado a 50 puntos/s.
* Toda la demás telemetría (vistas de páginas, sesiones, solicitudes, dependencias, métricas, eventos personalizados, resultados de prueba web).





*¿Qué ocurre si mi aplicación supera la tasa por segundo?*

* El volumen de datos que su aplicación envía se evalúa cada minuto. Si se supera la tasa por segundo promediada por minuto, el servidor rechazará algunas solicitudes. Algunas versiones del SDK intentan volver a enviarlas y generan una sobrecarga durante varios minutos; otras, como el SDK de JavaScript, simplemente quitan los datos rechazados.

Si se produce la limitación, verá una notificación de advertencia que indica que esto ha sucedido.

*¿Cómo puedo saber cuántos puntos de datos envía mi aplicación?*

* Abra Configuración/cuotas y precios para ver el gráfico de volumen de datos.
* O bien, en el Explorador de métricas, agregue un nuevo gráfico y seleccione **Volumen de punto de datos** como su métrica. Active la agrupación y agrupe por **Tipo de datos**.

*¿Cómo puedo reducir la cantidad de datos que envía mi aplicación?*

* Use [Muestreo](app-insights-sampling.md). Esta tecnología reduce la velocidad de los datos sin sesgar las métricas y sin interrumpir la capacidad de navegar entre los elementos relacionados en la búsqueda. El muestreo adaptable está habilitado de forma predeterminada a partir de la versión 2.0.0-beta3 del SDK de Application Insights para ASP.NET.
* [Desactive los colectores de telemetría](app-insights-configuration-with-applicationinsights-config.md) que no necesite.


### Sugerencias para reducir la velocidad de datos

Si alcanza los valores de limitación, puede hacer alguna de estas cosas:

* Use el [Muestreo](app-insights-sampling.md). Esta tecnología reduce la velocidad de los datos sin sesgar las métricas y sin interrumpir la capacidad de navegar entre los elementos relacionados en la búsqueda.
* Desactivar los módulos de recopilación que no necesite; para ello, [edite ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Por ejemplo, podría decidir que los contadores de rendimiento o datos de dependencia no son esenciales.
* Métricas agregadas previamente. Si ha colocado llamadas a TrackMetric en su aplicación, puede reducir el tráfico mediante la sobrecarga que acepta el cálculo de la media y la desviación estándar de un lote de medidas. O bien, puede usar un [paquete de agregación previa](https://www.myget.org/gallery/applicationinsights-sdk-labs). 


### Límites de los nombres

1.	Un máximo de 200 nombres de métrica únicos y 200 nombres de propiedad únicos para la aplicación. Las métricas incluyen el envío de datos a través de TrackMetric, así como mediciones de otros tipos de datos como eventos. Los [nombres de métricas y propiedades][api] son globales por clave de instrumentación, no limitados al tipo de datos.
2.	Las [propiedades][apiproperties] se pueden usar para filtrar y agrupar por estas solo cuando tienen menos de 100 valores únicos para cada propiedad. Después de que los valores únicos superen los 100, la propiedad todavía se puede usar para búsqueda y filtrado, pero no para filtros.
3.	Las propiedades estándar como el nombre de la solicitud y la URL de página se limitan a 1000 valores únicos por semana. Después de 1000 valores únicos, los valores adicionales se marcan como "Otros valores". El valor original puede seguir usándose para la búsqueda de texto completo y el filtrado.

## Retención de datos

El nivel de precios determina cuánto tiempo se mantienen los datos en el portal y, por tanto, cuánto tiempo hacia atrás puede establecer los intervalos de tiempo.


* Puntos de datos sin procesar (es decir, instancias que puede inspeccionar en Búsqueda de diagnóstico): entre 7 y 30 días.
* Los datos agregados (es decir, recuentos, promedios y otros datos estadísticos que se ven en el Explorador de métricas) se retienen en un minuto para 30 días, y en una hora o un día (en función del tipo) para al menos 13 meses.




## Revisión de la factura de su suscripción a Azure

Los cargos de Application Insights se agregarán a la factura de Azure. Puede ver los detalles de su factura de Azure en la sección de facturación del portal de Azure o en el [Portal de facturación de Azure](https://account.windowsazure.com/Subscriptions).

![En el menú lateral, elija Facturación.](./media/app-insights-pricing/02-billing.png)

## Resumen de límites

[AZURE.INCLUDE [límites-de-application-insights](../../includes/application-insights-limits.md)]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=AcomDC_0121_2016-->