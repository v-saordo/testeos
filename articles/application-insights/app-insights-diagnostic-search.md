<properties 
	pageTitle="Uso de la Búsqueda de diagnóstico" 
	description="Busque y filtre eventos individuales, solicitudes y seguimientos de registros." 
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
	ms.date="01/26/2016" 
	ms.author="awills"/>
 
# Uso de Búsqueda de diagnóstico en Application Insights

Búsqueda de diagnóstico es la hoja de [Application Insights][start] que se usa para buscar y explorar elementos de telemetría individuales, como vistas de página, excepciones o solicitudes web. Y puede ver los seguimientos de registros y eventos que haya codificado.

## ¿Cuando ve Búsqueda de diagnóstico?


### En el Portal de Azure

Puede abrir Búsqueda diagnóstico explícitamente:

![Open diagnostic search](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)


También se abre al hacer clic a través de algunos gráficos y elementos de la cuadrícula. En este caso, los filtros se establecen previamente para centrarse en el tipo de elemento seleccionado.

Por ejemplo, si la aplicación es un servicio web, la hoja de información general muestra un gráfico de volumen de solicitudes. Haga clic en él y obtendrá un gráfico más detallado, con una lista que muestra el número de solicitudes realizadas para cada dirección URL. Haga clic en cualquier fila y obtendrá una lista de las solicitudes individuales para esta dirección URL:

![Open diagnostic search](./media/app-insights-diagnostic-search/07-open-from-filters.png)


El cuerpo principal de Búsqueda de diagnóstico es una lista de elementos de telemetría: solicitudes de servidor, vistas de página, eventos personalizados que haya codificado, etc. En la parte superior de la lista hay un gráfico de resumen que muestra los recuentos de eventos con el paso del tiempo.

Los eventos normalmente aparecen en la búsqueda de diagnóstico antes de que aparezcan en el explorador de métrica. Aunque la hoja se actualiza a intervalos, puede hacer clic en Actualizar si espera un evento determinado.


### En Visual Studio

Abra la ventana de búsqueda en Visual Studio:

![](./media/app-insights-diagnostic-search/32.png)

La ventana de búsqueda tiene las mismas características que el portal web:

![](./media/app-insights-diagnostic-search/34.png)


### Muestreo

Si la aplicación genera muchos datos de telemetría (y está usando la versión 2.0.0-beta3, o una posterior, del SDK de ASP.NET), el módulo de muestreo adaptable reducirá automáticamente el volumen que se envía al portal mediante el envío de solamente una fracción representativa de los eventos. Sin embargo, los eventos relacionados con la misma solicitud se seleccionarán o se anulará su selección como grupo, por lo que puede navegar entre ellos.
> [Más información sobre el muestreo](app-insights-sampling.md).


## Inspección de elementos individuales

Seleccione cualquier elemento de telemetría para ver los campos clave y los elementos relacionados. Si desea ver el conjunto completo de campos, haga clic en "...".

![Open diagnostic search](./media/app-insights-diagnostic-search/10-detail.png)

Para encontrar el conjunto completo de campos, utilice cadenas sin formato (sin caracteres comodín). Los campos disponibles dependen del tipo de telemetría.

## Filtro de los tipos de evento

Abra la hoja Filtro y elija los tipos de evento que desea ver. (Si, posteriormente, desea restaurar los filtros con los que abrió la hoja, haga clic en Restablecer).


![Elija Filtrar y seleccione los tipos de telemetría](./media/app-insights-diagnostic-search/02-filter-req.png)


Los tipos de evento son:

* **Seguimiento**: registros de diagnóstico, como llamadas a TrackTrace, log4Net, NLog y System.Diagnostic.Trace.
* **Solicitud**: solicitudes HTTP recibidas por la aplicación de servidor, como páginas, scripts, imágenes, archivos de estilo y datos. Estos eventos se utilizan para crear los gráficos de información general de solicitudes y respuestas.
* **Vista de página**: estos eventos se envían a través del cliente web y se utilizan para crear informes de vistas de página. 
* **Evento personalizado**: si ha insertado llamadas a TrackEvent() para [supervisar el uso][track], puede buscarlas aquí.
* **Excepción**: excepciones no detectadas en el servidor y las que se registran mediante TrackException().

## Filtro de los valores de propiedad

Puede filtrar eventos por los valores de sus propiedades. Las propiedades disponibles dependen de los tipos de evento que haya seleccionado.

Por ejemplo, seleccione solicitudes con un código de respuesta específico.

![Expanda una propiedad y elija un valor](./media/app-insights-diagnostic-search/03-response500.png)

El hecho de no elegir ningún valor de una propiedad determinada tiene el mismo efecto que elegir todos los valores; se desactiva el filtrado en esa propiedad.


### Acotación de la búsqueda

Observe que los recuentos a la derecha de los valores de filtro muestran cuántas repeticiones hay en el conjunto filtrado actual.

En este ejemplo, es obvio que la solicitud `Reports/Employees` genera la mayor parte de los 500 errores:

![Expanda una propiedad y elija un valor](./media/app-insights-diagnostic-search/04-failingReq.png)

Además ,si desea ver también qué otros eventos se estaban produciendo durante este tiempo, puede activar **Incluir eventos con propiedades no definidas**.

## Supresión de bots y de tráfico de prueba web

Utilice el filtro **Tráfico real o sintético** y active+ **Real**.

También puede filtrar por **Origen del tráfico sintético**.

## Inspección de repeticiones individuales

Agregue ese nombre de solicitud al filtro definido y podrá inspeccionar repeticiones individuales de ese evento.

![Seleccione un valor](./media/app-insights-diagnostic-search/05-reqDetails.png)

Para los eventos de solicitud, los detalles muestran las excepciones que ocurrieron mientras la solicitud se estaba procesando.

Haga clic en una excepción para ver los detalles, incluido el seguimiento de la pila.

![Haga clic en una excepción](./media/app-insights-diagnostic-search/06-callStack.png)

## Búsqueda de eventos con la misma propiedad

Encuentre todos los elementos con el mismo valor de propiedad:

![Haga clic con el botón secundario en una propiedad](./media/app-insights-diagnostic-search/12-samevalue.png)

## Búsqueda por valor de métrica

Obtenga todas las solicitudes con un tiempo de respuesta > 5 seg. Las horas se representan en tics: 10 000 tics = 1 ms.

!["Tiempo de respuesta":(threshold TO *)](./media/app-insights-diagnostic-search/11-responsetime.png)



## Búsqueda de los datos

Puede buscar términos en cualquiera de los valores de propiedad. Esto es especialmente útil si ha escrito [eventos personalizados][track] con valores de propiedad.

Quizás desee establecer un intervalo de tiempo, dado que las búsquedas en un intervalo más corto son más rápidas.

![Open diagnostic search](./media/app-insights-diagnostic-search/appinsights-311search.png)

Busque términos, no subcadenas. Los términos son cadenas alfanuméricas incluyendo algunos signos de puntuación, como '.' y '\_'. Por ejemplo:

término|*no* coincide con|pero coincide
---|---|---
HomeController.About|about<br/>home|h*about<br/>home*
IsLocal|local<br/>is<br/>*local|isl*<br/>islocal<br/>i*l*
New Delay|w d|new<br/>delay<br/>n* AND d*


A continuación se muestran las expresiones de búsqueda que puede utilizar:

Consulta de ejemplo | Efecto 
---|---
lento|Busca todos los eventos del intervalo de datos cuyos campos incluyen el término "lento"
base de datos??|Las coincidencias con base de datos01, base de datosAB...<br/>? no se permiten al comienzo de un término de búsqueda.
base de datos*|Las coincidencias con base de datos, base de datos01, base de datosNNNN<br/>* no se permiten al comienzo de un término de búsqueda.
manzana AND plátano|Buscar eventos que contienen ambos términos. Utilizar "AND" en mayúsculas, no "and".
Manzana, plátano OR<br/>manzana plátano|Buscar eventos que contienen cualquiera de los dos términos. Utilice "OR" no "or".</br/>Forma abreviada.
manzana NOT plátano<br/>manzana -plátano|Encuentre eventos que contengan un término pero no el otro.<br/>Forma abreviada.
manz* AND plátano -(uvas pera)|Operadores lógicos y corchetes.
"Métrica": 0 TO 500<br/>"Métrica" : 500 TO * | Encuentra eventos que contienen la medida designada dentro del intervalo de valores.


## Guardado de la búsqueda

Cuando haya establecido todos los filtros que desea, puede guardar la búsqueda como favorito. Si trabaja en una cuenta de organización, puede elegir si compartirla con otros miembros del equipo.

![Haga clic en Favoritos, establezca el nombre y haga clic en Guardar](./media/app-insights-diagnostic-search/08-favorite-save.png)


Para ver de nuevo la búsqueda, **vaya a la hoja de información general** y abra Favoritos:

![Favoritos](./media/app-insights-diagnostic-search/09-favorite-get.png)

Si ha guardado con el intervalo de tiempo relativo, la hoja abierta de nuevo tiene los datos más recientes. Si ha guardado con el intervalo de tiempo absoluto, verá los mismos datos cada vez.


## Envío de más telemetría a Application Insights

Además de la telemetría inmediata enviada por el SDK de Application Insights, puede:

* Capturar seguimientos de registros de su marco de registro de favoritos en [.NET][netlogs] o [Java][javalogs]. Esto significa que puede buscar en los seguimientos de registros y correlacionarlos con vistas de página, excepciones y otros eventos. 
* [Escribir código][track] para enviar eventos personalizados, vistas de página y excepciones. 

[Aprender a enviar registros y telemetría personalizado a Application Insights][trace].


## <a name="questions"></a>Preguntas y respuestas

### <a name="limits"></a>¿Qué cantidad de datos se conserva?

Hasta 500 eventos por segundo de cada aplicación. Los eventos se conservan durante siete días.

### ¿Cómo puedo ver datos POST en mis solicitudes de servidor?

Aunque no registramos los datos POST automáticamente, puede usar [TrackTrace o llamadas de registro][trace]. Coloque los datos POST en el parámetro de mensaje. No puede filtrar por el mensaje de la misma forma que con las propiedades, pero el límite de tamaño es mayor.

## <a name="add"></a>Pasos siguientes

* [Envío de registros y telemetría personalizada a Application Insights.][trace]
* [Configuración de pruebas de disponibilidad y de capacidad de respuesta][availability]
* [Solución de problemas][qna]



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[javalogs]: app-insights-java-trace-logs.md
[netlogs]: app-insights-asp-net-trace-logs.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
[trace]: app-insights-search-diagnostic-logs.md
[track]: app-insights-spi-custom-events-metrics.md

 

<!---HONumber=AcomDC_0128_2016-->