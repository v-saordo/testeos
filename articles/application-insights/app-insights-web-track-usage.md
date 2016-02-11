<properties 
	pageTitle="Análisis de uso de aplicaciones web con Application Insights" 
	description="Información general del análisis de uso de aplicaciones web con Application Insights" 
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
	ms.date="11/25/2015" 
	ms.author="awills"/>
 
# Análisis de uso de aplicaciones web con Application Insights

Saber cómo se usa la aplicación permite centrarse en el trabajo de desarrollo en los escenarios que son más importantes, así como obtener información sobre los objetivos que resulten más fáciles o más difíciles de lograr.

Application Insights de Visual Studio proporciona dos niveles de seguimiento de uso:

* **Datos de usuarios, sesiones y vistas de página**: listos para usar.  
* **Telemetría personalizada**: debe [escribir código][api] para hacer un seguimiento de los usuarios a través de la experiencia del usuario con la aplicación. 

## Instalación

Abra un recurso de Application Insights en el [Portal de Azure](https://portal.azure.com), haga clic en el gráfico de cargas de la página del explorador vacío y siga las instrucciones de instalación.

[Más información](app-insights-javascript.md)


## Popularidad de mi aplicación web

Inicie sesión en el [portal de Azure][portal], diríjase al recurso de la aplicación y haga clic en Uso:

![](./media/app-insights-web-track-usage/14-usage.png)

* **Usuarios:**: recuento de los distintos usuarios activos durante el intervalo de tiempo del gráfico. 
* **Sesiones:**recuento de sesiones activas.
* **Vistas de página**: cuenta el número de llamadas a trackPageView(), normalmente se llama una vez en cada página web.

Haga clic en cualquiera de los gráficos para ver su contenido con mayor detalle. Observe que puede cambiar el intervalo de tiempo de los gráficos.

### ¿Dónde viven mis usuarios?

En la hoja de uso, haga clic en el gráfico Usuarios para ver más detalles:

![En la hoja Uso, haga clic en el gráfico Usuarios.](./media/app-insights-web-track-usage/02-sessions.png)
 
### ¿Qué sistemas operativos o exploradores utilizan?

Agrupe (segmente) los datos por una propiedad, como Explorador, Sistema operativo o Ciudad:

![Seleccione un gráfico que muestre una sola métrica, cambie a Agrupación y elija una propiedad.](./media/app-insights-web-track-usage/03-browsers.png)


## Sesiones

El concepto de "sesión" es fundamental en Application Insights, ya que intenta asociar cada evento de telemetría (por ejemplo, solicitudes, vistas de página, excepciones o eventos personalizados que usted mismo codifica) con una sesión de usuario específica.

Se recopila información de contexto enriquecido sobre cada sesión, por ejemplo, las características del dispositivo, la ubicación geográfica, el sistema operativo, etc.

Si instrumenta el cliente y el servidor ([ASP.NET][greenbrown] o [J2EE][java]), el SDK propagará el identificador de sesión entre el cliente y el servidor, por lo que se pueden correlacionar eventos en ambos lados.

Al [diagnosticar problemas][diagnostic], puede encontrar toda la telemetría relacionada con la sesión en la que se produjo un problema, incluidas todas las solicitudes, así como cualquier evento, excepción o seguimiento que se hayan registrado.

Las sesiones ofrecen una buena referencia sobre la popularidad de los contextos, por ejemplo, el dispositivo, el sistema operativo o la ubicación. Por ejemplo, al mostrar el número de sesiones agrupadas por dispositivo, se obtiene un recuento más preciso de la frecuencia con la que dicho dispositivo se usa con la aplicación, que si se recurre al recuento de vistas de página. Esto sería un punto de partida útil para evaluar cualquier problema específico que tenga un dispositivo.


#### ¿Qué es una sesión?

Una sesión representa un único encuentro entre el usuario y la aplicación. En su forma más simple, la sesión comienza cuando un usuario que inicia la aplicación y finaliza cuando el usuario sale de ella. Para las aplicaciones web, de forma predeterminada, la sesión finaliza después de 30 minutos de inactividad o después de 24 horas de actividad.

Puede cambiar estos valores predeterminados modificando el fragmento de código:

    <script type="text/javascript">
        var appInsights= ... { ... }({
            instrumentationKey: "...",
            sessionRenewalMs: 3600000,
            sessionExpirationMs: 172800000
        });

* `sessionRenewalMs`: el tiempo, en milisegundos, para que caduque la sesión debido a la inactividad del usuario. Valor predeterminado: 30 minutos.
* `sessionExpirationMs`: la duración máxima de la sesión, en milisegundos. Si el usuario permanece activo después de este tiempo, se considera que es otra sesión. Valor predeterminado: 24 horas.

**Duración de la sesión** es una [métrica][metrics] que registra el intervalo de tiempo entre el primer elemento de telemetría y el último de la sesión. (No incluye el tiempo de espera).

El **Recuento de sesiones** en un intervalo determinado se define como el número de sesiones únicas con alguna actividad durante este intervalo. Cuando busque en un intervalo de tiempo grande (por ejemplo, la cantidad de sesiones diarias de la semana pasada), este valor suele equivaler al número total de sesiones.

Sin embargo, cuando explore intervalos de tiempo más pequeños (por ejemplo, unidades de una hora), una sesión que abarque varias horas se contará por cada hora en la que la sesión estuvo activa.

## Usuarios y cuentas de usuario


Cada sesión de usuario está asociada a un identificador de usuario único.

De forma predeterminada, el usuario se identifica mediante la colocación de una cookie. Un usuario que use varios exploradores o dispositivos se contará varias veces. (No obstante, vea [Usuarios autenticados](#authenticated-users)).


La métrica **Recuento de usuarios** en un intervalo determinado se define como el número de usuarios únicos con actividad registrada durante este intervalo. Como resultado, es posible que los usuarios con sesiones extensas se cuenten varias veces cuando se establece un intervalo de tiempo tal que la unidad es inferior a una hora o similar.

**Usuarios nuevos** cuenta los usuarios cuyas primeras sesiones con la aplicación se produjeron durante este intervalo. Si se usa el método predeterminado de recuento de usuarios por las cookies, esto incluirá también a los usuarios que han desactivado sus cookies o que usan un nuevo dispositivo o explorador para tener acceso a la aplicación por primera vez. ![En la hoja Uso, haga clic en el gráfico Usuarios para examinar los usuarios nuevos.](./media/app-insights-web-track-usage/031-dual.png)

### Usuarios autenticados

Si su aplicación web permite a los usuarios iniciar sesión, puede obtener un recuento más preciso al proporcionar a Application Insights un identificador de usuario único. No tiene por qué ser el nombre del usuario o el mismo identificador que usa en la aplicación. Una vez que la aplicación identifique al usuario, use este código:


*JavaScript en el cliente*

      appInsights.setAuthenticatedUserContext(userId);

Si su aplicación agrupa a los usuarios en cuentas, también puede pasar un identificador de la cuenta.

      appInsights.setAuthenticatedUserContext(userId, accountId);

Los identificadores de usuario y de cuenta no pueden contener espacios ni caracteres `,;=|`.


En el [explorador de métricas](app-insights-metrics-explorer.md), puede crear un gráfico de **Usuarios autenticados** y **Cuentas**.

## Tráfico sintético

El tráfico sintético incluye las solicitudes de las pruebas de carga y disponibilidad, rastreadores de motores de búsqueda y otros agentes.

Application Insights intenta determinar automáticamente el tráfico sintético para clasificarlo y marcarlo correctamente. En la mayoría de los casos, el tráfico sintético no invoca el SDK de JavaScript, por lo que esta actividad se excluye del recuento de usuarios y sesiones.

Sin embargo, para las [pruebas web][availability] de Application Insights, el identificador de usuario se establece automáticamente según la ubicación POP. El identificador de sesión se establece basándose en el identificador de ejecución de pruebas. En los informes predeterminados, el tráfico sintético se filtra de manera predeterminada, por lo que se excluyen estos usuarios y estas sesiones. Sin embargo, cuando se incluye el tráfico sintético, se puede producir un pequeño incremento en el recuento global de usuarios y sesiones.
 
## Uso de las páginas

Haga clic en el gráfico de las vistas de páginas para obtener una versión más ampliada junto con un desglose de las páginas más populares:


![En la hoja de información general, haga clic en el gráfico de vistas de páginas.](./media/app-insights-web-track-usage/05-games.png)
 
El ejemplo anterior es de un sitio web de juegos. De él se puede deducir al instante:

* El uso no ha mejorado durante la semana anterior. ¿Quizás debemos pensar en la optimización de motor de búsqueda?
* Muchas menos personas consultan las páginas de juegos que la página principal. ¿Por qué nuestra página de inicio no atrae a los jugadores?
* El 'Crucigrama' es el juego más popular. Debemos dar prioridad a nuevas ideas y mejoras.

## Seguimiento personalizado

Supongamos en lugar de implementar cada juego en una página web independiente se decide rediseñar todos para que estén en la misma aplicación de una página, con la mayoría de la funcionalidad codificada como Javascript en la página web. De esta forma el usuario puede cambiar rápidamente de un juego a otro, o incluso tener varios juegos en una sola página.

Pero todavía desea que Application Insights registre el número de veces que se abre cada juego, exactamente igual que cuando estaban en páginas web independientes. Es muy sencillo: inserte una llamada al módulo de telemetría en el JavaScript donde desea registrar que se ha abierto una nueva «página»:

	appInsights.trackPageView(game.Name);

## Eventos personalizados

Use eventos personalizados. Puede enviarlos desde aplicaciones de dispositivo, páginas web o un servidor web:

*JavaScript*

    appInsights.trackEvent("GameEnd");

*C#*

    var tc = new Microsoft.ApplicationInsights.TelemetryClient(); 
    tc.TrackEvent("GameEnd");

*VB*

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")


Los eventos personalizados más frecuentes se enumeran en la hoja de información general.

![En la hoja de información general, desplácese hacia abajo y haga clic en Eventos personalizados.](./media/app-insights-web-track-usage/04-events.png)

Haga clic en el encabezado de la tabla para ver el número total de eventos. El gráfico se puede segmentar por distintos atributos, como el nombre de evento:

![Seleccione un gráfico que muestre solamente una métrica. Cambia a Agrupación. Elija una propiedad. No todas las propiedades están disponibles.](./media/app-insights-web-track-usage/06-eventsSegment.png)

Una característica particularmente útil de escalas de tiempo es que puede correlacionar los cambios con otras métricas y eventos. Por ejemplo, a veces cuando se reproducen más juegos, lo habitual es que espere ver también un aumento de juegos abandonados. Pero el aumento de juegos abandonados es desproporcionado, desea averiguar si la carga alta está causando problemas que los usuarios encuentran inaceptables.

## Profundización en eventos específicos

Para comprender mejor cómo se desarrolla una sesión típica, debe centrarse en una sesión de usuario específica que contenga un tipo determinado de evento.

En este ejemplo, hemos codificado un evento personalizado "NoGame" que se llama si el usuario cierra la sesión sin iniciar un juego. ¿Por qué hace un usuario? Quizá si profundizamos en algunas instancias específicas, obtendremos una pista.

Los eventos personalizados recibidos de la aplicación se enumeran por nombre en la hoja de información general:


![En la hoja de información general, haga clic en uno de los tipos de eventos personalizados.](./media/app-insights-web-track-usage/07-clickEvent.png)
 
Haga clic en el evento de interés y seleccione una aparición específica reciente:


![En la lista bajo el gráfico de resumen haga clic en un evento.](./media/app-insights-web-track-usage/08-searchEvents.png)
 
Echemos un vistazo a toda la telemetría de la sesión en la que se produjo un evento NoGame determinado.


![Haga clic en 'Toda la telemetría de la sesión'.](./media/app-insights-web-track-usage/09-relatedTelemetry.png)
 
No había ninguna excepción, por lo que no se le impidió al usuario jugar debido a un error.
 
Podemos filtrar todos los tipos de telemetría excepto las vistas de páginas para esta sesión:


![](./media/app-insights-web-track-usage/10-filter.png)
 
Y ahora podemos comprobar que este usuario se ha conectado simplemente para comprobar los últimos resultados. Tal vez haya que desarrollar un caso de usuario que facilite esta comprobación. (Y debemos implementar un evento personalizado para informar cuando se produzca este caso específico).

## Filtrar, buscar y segmentar los datos con propiedades
Puede adjuntar etiquetas arbitrarias y valores numéricos a los eventos.
 

*JavaScript en el cliente*

```JavaScript

    appInsights.trackEvent("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric measurements:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
    );
```

*C# en el servidor*

```C#

    // Set up some properties:
    var properties = new Dictionary <string, string> 
        {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
        {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);
```

*VB en el servidor*

```VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)
```

Asocie propiedades a vistas de página de la misma manera:

*JavaScript en el cliente*

```JS

    appInsights.trackPageView("Win", 
        url,
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});
```

En Búsqueda de diagnóstico, vea las propiedades haciendo clic a través de una única repetición de un evento.


![En la lista de eventos, abra un evento y, a continuación, haga clic en '...' para ver más propiedades.](./media/app-insights-web-track-usage/11-details.png)
 
Utilice el campo de búsqueda para ver las apariciones del evento con un valor de propiedad concreto.


![Escriba un valor en el campo de búsqueda.](./media/app-insights-web-track-usage/12-searchEvents.png)


## Prueba A | B

Si no conoce qué variante de una característica tendrá más éxito, publique ambas para que estén accesibles a los diferentes usuarios. Mida el éxito de cada una y, a continuación, cambie a una versión unificada.

Para realizar esta técnica, adjunte etiquetas distintas a toda la telemetría que se envía con cada versión de la aplicación. Puede hacerlo al definir las propiedades en el TelemetryContext activo. Estas propiedades predeterminadas se agregan a cada mensaje de telemetría que envía la aplicación: no solo los mensajes personalizados, sino también la telemetría estándar.

En el portal de Application Insights, podrá filtrar y agrupar (segmentar) los datos en las etiquetas, con el fin de comparar las distintas versiones.

*C# en el servidor*

```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```

*VB en el servidor*

```VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")
```

La telemetría individual puede invalidar los valores predeterminados.

Puede configurar un inicializador universal para que todos los TelemetryClients nuevos usen automáticamente el contexto.

```C#

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }
```

En el inicializador de la aplicación como Global.asax.cs:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }
```


## Compilación - Métrica - Aprendizaje

Cuando se utiliza el análisis, se convierte en una parte integrada de su ciclo de desarrollo, no solo en algo que se cree que puede ayudar a solucionar problemas. A continuación se incluyen algunas sugerencias:

* Determine la métrica clave de la aplicación. ¿Desea tantos usuarios como sea posible o preferiría un pequeño conjunto de usuarios satisfechos? ¿Desea potenciar al máximo las visitas o las ventas?
* Planee la medición de cada caso. Cuando esboza un nuevo caso de usuario o característica o planea actualizar uno existente, siempre piensa cómo medirá el éxito del cambio. Antes de que se inicie la codificación, pregunte "¿Qué efecto tendrá en nuestras estadísticas, si funciona? ¿Debemos realizar el seguimiento cualquier evento nuevo?" Y, por supuesto, cuando se activa la característica, asegúrese de mirar el análisis y actuar en los resultados. 
* Relacione otras métricas con la métrica clave. Por ejemplo, si agrega una característica "Favoritos", le gustaría saber con qué frecuencia los usuarios agregan favoritos. Pero quizás sea más interesante saber con qué frecuencia vuelven a sus favoritos. Y lo más importante, los clientes que usan favoritos ¿compran al final más de su producto?
* Pruebas de valor controlado. Configure un modificador de característica que permite que una nueva característica sea visible solo para algunos usuarios. Use Application Insights para ver si se utiliza la nueva característica de la manera que había previsto. Realice ajustes y publíquelos para una audiencia más amplia.
* Hable con los usuarios. El análisis no es suficiente por sí solo, pero es una herramienta complementaria para mantener una excelente relación con el cliente.


## Referencias

* [Uso de la API (información general)][api]
* [Referencia de API de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## Vídeo

> [AZURE.VIDEO usage-monitoring-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_1203_2015-->