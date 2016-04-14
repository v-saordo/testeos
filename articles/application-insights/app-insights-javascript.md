<properties
	pageTitle="Application Insights para aplicaciones web de JavaScript | Microsoft Azure"
	description="Obtenga recuentos de sesiones y vistas de página, además de datos de cliente web, y realice el seguimiento de los patrones de uso. Detecte problemas de rendimiento y excepciones en páginas web de JavaScript."
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/19/2016"
	ms.author="awills"/>

# Application Insights para páginas web

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Obtenga información sobre el rendimiento y la utilización de su página web o aplicación. Agregue Application Insights de Visual Studio en el script de la página y obtendrá tiempos de carga de página y llamadas AJAX, recuentos y detalles sobre excepciones del explorador y errores de AJAX, así como recuentos de usuarios y sesiones. Todos estos datos se pueden segmentar por página, sistema operativo del cliente y versión del explorador, geolocalización y otras dimensiones. También puede establecer alertas sobre recuentos de errores o sobre cargas de página lentas.

Puede utilizar Application Insights con cualquier página web, solo tiene que agregar un pequeño fragmento de JavaScript. Si su servicio web es [Java](app-insights-java-get-started.md) o [ASP.NET](app-insights-asp-net.md), puede integrar datos de telemetría desde el servidor y los clientes.

Necesitará una suscripción a [Microsoft Azure](https://azure.com). Si su equipo tiene una suscripción organizativa, pida al propietario que agregue su cuenta de Microsoft. Hay un plan de tarifas gratuito, por lo que el desarrollo y utilización a pequeña escala no cuestan nada.


## Configuración de Application Insights para su página Web

Es posible que ya la tenga. Si la aplicación es un nuevo proyecto de ASP.NET y decidió agregar Application Insights en el cuadro de diálogo Nuevo proyecto en Visual Studio, el script ya se habrá agregado y estará configurado.

De lo contrario, deberá agregar un fragmento de código en las páginas web, como se indica a continuación.

### Apertura de un recurso de Application Insights

El recurso de Application Insights es donde se muestran los datos sobre el rendimiento y el uso de la página.

Inicie sesión en el [Portal de Azure](https://portal.azure.com).

Si ya ha configurado la supervisión del lado del servidor de su aplicación, ya tendrá un recurso:

![Seleccione Examinar, Servicios para desarrolladores, Application Insights.](./media/app-insights-javascript/01-find.png)

Si no tiene uno, créelo:

![Seleccione Nuevo, Servicios para desarrolladores, Application Insights.](./media/app-insights-javascript/01-create.png)


*¿Tiene ya alguna pregunta?* [Más información sobre cómo crear un recurso](app-insights-create-new-resource.md).


### Agregar el script de SDK a la aplicación o la página web

En Inicio rápido, obtenga el script para páginas web:

![En la hoja de información general de su aplicación, elija Inicio rápido, Obtener código para supervisar mis páginas web. Copie el script.](./media/app-insights-javascript/02-monitor-web-page.png)

Inserte el script justo antes de la etiqueta `<head>` de cada página que desea seguir. Si su sitio web tiene una página maestra, puede colocar el script allí. Por ejemplo:

* En un proyecto de ASP.NET MVC, lo colocaría en `View\Shared\_Layout.cshtml`
* En un sitio de SharePoint, en el panel de control, abra [Configuración del sitio/Página maestra](app-insights-sharepoint.md).

El script contiene la clave de instrumentación que dirige los datos al recurso de Application Insights.

([Una explicación más profunda del script](http://apmtips.com/blog/2015/03/18/javascript-snippet-explained/))

*(Si está usando un marco de página web conocido, mire a ver si encuentra adaptadores de Application Insights). Por ejemplo, hay [un módulo AngularJS](http://ngmodules.org/modules/angular-appinsights)).*


## Configuración detallada

Hay varios [parámetros](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) que puede establecer, aunque en la mayoría de los casos, no es necesario. Por ejemplo, puede deshabilitar o limitar el número de llamadas Ajax notificadas por vista de página (para reducir el tráfico); o bien, puede establecer el modo de depuración para hacer que la telemetría se desplace rápidamente por la canalización sin que se procese por lotes.

Para establecer estos parámetros, busque esta línea en el fragmento de código y agregue más elementos delimitados por comas a continuación:

    })({
      instrumentationKey: "..."
      // Insert here
    });

Entre los [parámetros disponibles](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) se incluyen:

    // Send telemetry immediately without batching.
    // Remember to remove this when no longer required, as it
    // can affect browser performance.
    enableDebug: true,

    // Don't log browser exceptions.
    disableExceptionTracking: true,

    // Limit number of Ajax calls logged, to reduce traffic.
    maxAjaxCallsPerView: 10, // default is 500

    // Set these dynamically for an authenticated user.
    appUserId: string,
    accountId: string,



## <a name="run"></a>Ejecución de la aplicación

Ejecute la aplicación web, úsela un rato para generar datos de telemetría y espere unos segundos. Puede ejecutarla con la tecla **F5** en la máquina de desarrollo, o publicarla y dejar que los usuarios jueguen con ella.

Si quiere comprobar la telemetría que envía una aplicación web a Application Insights, use las herramientas de depuración del explorador (**F12** en muchos exploradores). Los datos se envían a dc.services.visualstudio.com.

## Exploración de los datos de rendimiento del explorador

Abra la hoja de exploradores para mostrar los datos de rendimiento agregados de los exploradores de los usuarios.

![En portal.azure.com, abra el recurso de la aplicación y haga clic en Configuración, Explorador](./media/app-insights-javascript/03.png)


*¿Aún no hay datos? Haga clic en **Actualizar** en la parte superior de la página. ¿Todavía nada? Consulte [Solución de problemas](app-insights-troubleshoot-faq.md).*

La hoja de exploradores es una [hoja del Explorador de métricas](app-insights-metrics-explorer.md) con filtros y selecciones de gráfico predefinidos. Puede editar el intervalo de tiempo, los filtros y la configuración de gráfico si lo desea y guardar el resultado como favorito. Haga clic en **Restaurar valores predeterminados** para volver a la configuración original de la hoja.

## Rendimiento de carga de la página

En la parte superior hay un gráfico segmentado de tiempos de carga de la página. La altura total del gráfico representa el tiempo promedio para cargar y mostrar las páginas desde la aplicación en los exploradores de los usuarios. El tiempo se mide desde el momento en que el explorador envía la solicitud HTTP inicial hasta que todos los eventos de la carga sincrónica se han procesado, incluido el diseño y la ejecución de scripts. No incluye las tareas asincrónicas, como la carga de elementos web de las llamadas AJAX.

El gráfico divide el tiempo total de carga de las páginas en los [intervalos estándar definidos por W3C](http://www.w3.org/TR/navigation-timing/#processing-model).

![](./media/app-insights-javascript/08-client-split.png)

Tenga en cuenta que el tiempo de *conexión de red* es a menudo más bajo de lo que cabría esperar, porque es una media de todas las solicitudes del explorador al servidor. Muchas de las solicitudes individuales tienen un tiempo de conexión de 0 porque ya hay una conexión activa con el servidor.

### ¿La carga es lenta?

Una carga de página lenta es una importante fuente de insatisfacción para los usuarios. Si el gráfico indica una carga de página lenta, es fácil realizar una investigación de diagnóstico.

El gráfico muestra el promedio de todas las cargas de páginas en la aplicación. Para ver si el problema se limita a páginas concretas, consulte más abajo en la hoja, donde hay una cuadrícula dividida por dirección URL de la página:

![](./media/app-insights-javascript/09-page-perf.png)

Observe que el recuento de vistas de página y la desviación estándar. Si el número de páginas es muy bajo, el problema no afecta mucho a los usuarios. Una desviación estándar alta (en comparación con el mismo promedio) indica mucha variación entre las mediciones individuales.

**Amplíe la información de una dirección URL y una vista de página.** Haga clic en cualquier nombre de página para ver una hoja de gráficos de explorador filtrada solo para esa dirección URL y, a continuación, en una instancia de una vista de página.

![](./media/app-insights-javascript/35.png)

Haga clic en `...` para obtener una lista completa de las propiedades para ese evento o inspeccionar las llamadas Ajax y los eventos relacionados. Las llamadas Ajax lentas afectan al tiempo total de carga de la página si son sincrónicas. Entre los eventos relacionados se incluyen las solicitudes del servidor para la misma dirección URL (si ha configurado Application Insights en el servidor web).


**Evolución del rendimiento de la página en el tiempo.** De nuevo en la hoja de exploradores, cambie la cuadrícula de tiempo de carga de la vista de página en un gráfico de líneas para ver si hay picos en momentos concretos:

![Haga clic en el encabezado de la cuadrícula y seleccione un nuevo tipo de gráfico](./media/app-insights-javascript/10-page-perf-area.png)

**Segmentación mediante otras dimensiones.** ¿Puede que las páginas sean más lentas a la hora de cargarse en un determinado explorador, sistema operativo del cliente o ubicación del usuario? Agregue un nuevo gráfico y experimente con la dimensión **Agrupar por**.

![](./media/app-insights-javascript/21.png)


## Rendimiento AJAX

Asegúrese de que las llamadas AJAX en sus páginas web funcionan correctamente. A menudo se utilizan para rellenar los elementos de la página de manera asincrónica. Aunque los elementos principales de la página se carguen rápidamente, los usuarios se frustran al ver elementos web en blanco, esperando hasta que los datos aparezcan.

Las llamadas AJAX realizadas desde la página web aparecen en la hoja de exploradores como dependencias.

Hay gráficos de resumen en la parte superior de la hoja:

![](./media/app-insights-javascript/31.png)

y cuadrículas detalladas más abajo:

![](./media/app-insights-javascript/33.png)

Haga clic en cualquier fila para obtener detalles concretos.


> [AZURE.NOTE] Si elimina el filtro de exploradores en la hoja, tanto el servidor como las dependencias AJAX se incluirán en estos gráficos. Haga clic en Restaurar valores predeterminados para volver a configurar el filtro.

**Si desea profundizar en las llamadas Ajax erróneas**, desplácese hacia abajo hasta la cuadrícula de errores de Dependencia y, a continuación, haga clic en una fila para ver las instancias concretas.

![](./media/app-insights-javascript/37.png)

Haga clic en `...` para obtener la telemetría completa para una llamada Ajax.

### ¿No se han notificado llamadas Ajax?

Las llamadas AJAX incluyen todas las llamadas HTTP realizadas desde el script de la página web. Si no se han notificado, compruebe que el fragmento de código no establezca los [parámetros](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) `disableAjaxTracking` o `maxAjaxCallsPerView`.

## Excepciones de explorador

En la hoja de exploradores, hay un gráfico de resumen de excepciones y una cuadrícula de tipos de excepción más abajo en la hoja.

![](./media/app-insights-javascript/39.png)

Si no se han notificado las excepciones de explorador, compruebe que el fragmento de código no establezca el [parámetro](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) `disableExceptionTracking`.

## Inspección de eventos de vista de página individuales

Por lo general, Application Insights analiza la telemetría de vista de página y usted solo verá informes acumulativos, cuya media se ha calculado en función de todos los usuarios. Pero a efectos de depuración, también puede ver eventos de vista de página individuales.

En la hoja Búsqueda de diagnóstico, establezca Filtros en Vista de página.

![](./media/app-insights-javascript/12-search-pages.png)

Seleccione el evento que desea ver con mayor detalle. En la página de detalles, haga clic en "..." para ver aún más detalles.

> [AZURE.NOTE] Si usa [Buscar](app-insights-diagnostic-search.md), tenga en cuenta que tiene que hacer coincidir palabras completas: "Acerc" y "cerca de" no coinciden con "Acerca de", pero "Acerca d*" sí lo hace. Tampoco puede iniciar un término de búsqueda con un carácter comodín. Por ejemplo, "*cerc" no coincide con "Acerca".

> [Más información sobre Búsqueda de diagnóstico](app-insights-diagnostic-search.md)

### Propiedades de la vista de página

* **Duración de la vista de página** Tiempo que se tarda en cargar la página e iniciar la ejecución de scripts. En concreto, el intervalo entre el inicio de la carga de la página y la ejecución de trackPageView. Si mueve trackPageView desde su posición habitual después de la inicialización del script, reflejará un valor diferente.


## Recuentos de página personalizados

De forma predeterminada, se realiza un recuento de página cada vez que se carga una página nueva en el explorador del cliente. Sin embargo, tal vez quiera realizar un recuento de otras vistas de página. Por ejemplo, una página puede mostrar su contenido en pestañas y a usted puede interesarle realizar el recuento de una página cuando el usuario cambia de pestaña. O el código JavaScript de la página puede cargar contenido nuevo sin cambiar la URL del explorador.

Inserte una llamada de JavaScript como esta en el lugar adecuado del código de cliente:

    appInsights.trackPageView(myPageName);

El nombre de página puede contener los mismos caracteres que una URL, aunque se ignorarán los elementos situados tras "#" o "?".



## Seguimiento de uso


¿Desea averiguar qué hacen los usuarios con su aplicación?

* [Obtenga información sobre el seguimiento de uso](app-insights-web-track-usage.md)
* [Más información sobre las API para eventos y métricas personalizados](app-insights-api-custom-events-metrics.md).


#### <a name="video"></a> Vídeo: Seguimiento del uso

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> Pasos siguientes

* [Seguir el uso](app-insights-web-track-usage.md)
* [Eventos y métricas personalizados](app-insights-api-custom-events-metrics.md)
* [Compilación - Métrica - Aprendizaje](app-insights-overview-usage.md)

<!----HONumber=AcomDC_0224_2016-->