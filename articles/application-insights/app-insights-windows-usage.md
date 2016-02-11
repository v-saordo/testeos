<properties 
	pageTitle="Supervisión del uso en aplicaciones de a Tienda Windows y de Windows Phone con Application Insights" 
	description="Analice el uso de la aplicación de su dispositivo Windows con Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/25/2015" 
	ms.author="awills"/>

#  Supervisión del uso en aplicaciones de a Tienda Windows y de Windows Phone con Application Insights

*Application Insights se encuentra en su versión de vista previa.*

Descubra cuántos usuarios tiene y qué páginas están viendo en su aplicación. Application Insights proporciona datos listos para usar. Además, mediante la inserción de algunas líneas de código en la aplicación, puede obtener más información sobre las rutas de acceso que los usuarios toman a través de la aplicación y sobre lo que logran con ello.

Si aún no lo ha hecho, agregue [Application Insights al proyecto de la aplicación][windows], y vuelva a publicarlo.


## <a name="usage"></a>Seguimiento del uso

Desde la escala de tiempo de Información general, haga clic en los gráficos Usuarios y Sesiones para ver análisis más detallados.


![](./media/app-insights-windows-usage/appinsights-d018-oview.png)

* A los **usuarios** se les realiza un seguimiento de forma anónima, por lo que se contaría dos veces al mismo usuario en diferentes dispositivos.
* Una **sesión** se cuenta cuando se suspende la aplicación (durante más de un intervalo breve, para evitar el recuento de suspensiones accidentales).

#### Segmentación

Segmente un gráfico para obtener un desglose por distintos criterios. Por ejemplo, para ver cuántos usuarios utilizan cada versión de la aplicación, abra el gráfico Usuarios y segmente por Versión de la aplicación:

![En el gráfico Usuarios, active Segmentación y elija Versión de la aplicación.](./media/app-insights-windows-usage/appinsights-d25-usage.png)


#### Vistas de página

Para descubrir las rutas de acceso que siguen los usuarios a través de la aplicación, inserte [telemetría de vista de página][api] en el código:

    var telemetry = new TelemetryClient();
    telemetry.TrackPageView("GameReviewPage");

Vea los resultados en el gráfico de las vistas de página y abriendo los detalles:

![](./media/app-insights-windows-usage/appinsights-d27-pages.png)

Haga clic en cualquier página para ver los detalles de instancias específicas.

#### Eventos personalizados

Mediante la inserción de código para enviar eventos personalizados desde la aplicación, puede realizar un seguimiento del comportamiento de los usuarios y del uso de características y escenarios específicos.

Por ejemplo:

    telemetry.TrackEvent("GameOver");

Los datos aparecerán en la cuadrícula Eventos personalizados. Puede ver una vista agregada en el Explorador de métricas o hacer clic en cualquier evento para ver instancias específicas.

![](./media/app-insights-windows-usage/appinsights-d28-events.png)


Puede agregar propiedades numéricas y de cadena a cualquier evento.


    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"Game", currentGame.Name}, {"Difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("GameOver", properties, measurements);


Haga clic en cualquier instancia para ver sus propiedades detalladas, incluidas las que haya definido.


![](./media/app-insights-windows-usage/appinsights-d29-eventProps.png)

Consulte [Referencia de API][api] para obtener más información sobre los eventos personalizados.

## Sesiones

El concepto de "sesión" es fundamental en Application Insights, ya que intenta asociar cada evento de telemetría (por ejemplo, bloqueos o eventos personalizados que usted mismo codifica) con una sesión de usuario específica.

Se recopila información de contexto enriquecido sobre cada sesión, por ejemplo, las características del dispositivo, la ubicación geográfica, el sistema operativo, etc.

Al [diagnosticar problemas][diagnostic], puede encontrar toda la telemetría relacionada con la sesión en la que se produjo un problema, incluidas todas las solicitudes, así como cualquier evento, excepción o seguimiento que se hayan registrado.

Las sesiones ofrecen una buena referencia sobre la popularidad de los contextos, por ejemplo, el dispositivo, el sistema operativo o la ubicación. Por ejemplo, al mostrar el número de sesiones agrupadas por dispositivo, se obtiene un recuento más preciso de la frecuencia con la que dicho dispositivo se usa con la aplicación, que si se recurre al recuento de vistas de página. Esto sería un punto de partida útil para evaluar cualquier problema específico que tenga un dispositivo.


#### ¿Qué es una sesión?

Una sesión representa un único encuentro entre el usuario y la aplicación. En su forma más simple, la sesión comienza cuando un usuario que inicia la aplicación y finaliza cuando el usuario sale de ella. Para las aplicaciones móviles, la sesión finaliza cuando se suspende la aplicación (cuando se pasa a segundo plano) durante más de 20 segundos. Si la aplicación se reanuda, se iniciará una nueva sesión. Obviamente, un usuario puede tener varias sesiones en un día o, incluso, en la misma hora.

**Duración de la sesión** es una métrica que representa el intervalo de tiempo entre el primer elemento de telemetría y el último de la sesión. (No incluye el tiempo de espera).


El **Recuento de sesiones** en un intervalo determinado se define como el número de sesiones únicas con alguna actividad durante este intervalo. Cuando busque en un intervalo de tiempo grande (por ejemplo, la cantidad de sesiones diarias de la semana pasada), este valor suele equivaler al número total de sesiones.

Sin embargo, cuando explore intervalos de tiempo más pequeños (por ejemplo, unidades de una hora), una sesión que abarque varias horas se contará por cada hora en la que la sesión estuvo activa.

## Usuarios y cuentas de usuario

Cada sesión de usuario está asociada a un identificador de usuario único que se genera al usar la aplicación y se guarda en el almacenamiento local del dispositivo. Un usuario que utilice varios dispositivos se contará varias veces.

La métrica **Recuento de usuarios** en un intervalo determinado se define como el número de usuarios únicos con actividad registrada durante este intervalo. Como resultado, es posible que los usuarios con sesiones extensas se cuenten varias veces cuando se establece un intervalo de tiempo tal que la unidad es inferior a una hora o similar.

**Usuarios nuevos** cuenta los usuarios cuyas primeras sesiones con la aplicación se produjeron durante este intervalo.


## <a name="debug"></a>Modo de depuración frente a modo de lanzamiento

#### Depurar

Si compila en modo de depuración, los eventos se envían en cuanto se generan. Si pierde la conectividad a Internet y después sale de la aplicación antes de recuperar la conectividad, se descarta la telemetría sin conexión.

#### Lanzamiento

Si compila en la configuración de lanzamiento, los eventos se almacenan en el dispositivo y se envían cuando se reanuda la aplicación. También se envían datos durante el primer uso de la aplicación. Si no hay ninguna conexión a Internet durante el inicio, la telemetría anterior y la telemetría del ciclo de vida actual se almacenan y se envían en la siguiente reanudación.

## <a name="next"></a>Pasos siguientes

[Conocer a los usuarios][knowUsers]

[Más información acerca del Explorador de métricas][metrics]


[Solución de problemas][qna]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[windows]: app-insights-windows-get-started.md


 

<!---HONumber=AcomDC_1203_2015-->