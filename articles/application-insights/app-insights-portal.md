<properties
	pageTitle="Uso del portal de Application Insights"
	description="Después de haber configurado la aplicación para enviar telemetría a Application Insights, esta guía le muestra cómo orientarse en el portal."
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="multiple"
	ms.topic="article" 
	ms.date="11/23/2015"
	ms.author="awills"/>

# Uso del portal de Application Insights

Una vez [configurado Application Insights en su proyecto](app-insights-overview.md), aparecerán los datos de telemetría acerca del rendimiento y el uso de la aplicación en el recurso del proyecto Application Insights en el [portal de Azure](https://portal.azure.com).

## El panel

Al iniciar sesión en el [Portal de Azure](https://portal.azure.com), obtendrá acceso en primer lugar al panel. Puede personalizarlo o ponerlo en modo de pantalla completa. Este ejemplo se ha personalizado para mostrar los principales gráficos que les interesan a los propietarios.


![Panel personalizado.](./media/app-insights-portal/30.png)

1. Haga clic en la esquina superior en cualquier momento para volver al panel.
2. **+ Nuevo** crea un recurso nuevo. Un [Recurso de Application Insights](app-insights-create-new-resource.md) es un lugar para almacenar y analizar datos de telemetría de la aplicación.
3. La barra de navegación abre los recursos existentes.
4. Edite y cree paneles con la barra de herramientas del panel.

## Encontrar la telemetría

En el [portal de Azure](https://portal.azure.com), busque el recurso de Application Insights que ha creado para la aplicación.

![Haga clic en Examinar, seleccione Application Insights y, después, seleccione su aplicación.](./media/app-insights-portal/00-start.png)

La página de información general proporciona la telemetría básica, además de vínculos a más elementos. El contenido depende del tipo de aplicación y se puede personalizar.



## Intervalo de tiempo

En todas las hojas puede cambiar el intervalo de tiempo cubierto por los gráficos o cuadrículas.

![Abrir la hoja de información general de la aplicación en el portal de Azure](./media/app-insights-portal/03-range.png)


Si espera algunos datos que no ha aparecido todavía, haga clic en Actualizar. Los gráficos se actualizan a intervalos, pero los intervalos son más largos para intervalos de tiempo mayores. En modo de lanzamiento, puede tardar un tiempo que lleguen a través de la canalización de análisis en un gráfico de datos.

Para acercar parte de un gráfico, arrastre el puntero sobre él y, después, haga clic en el símbolo de lupa:

![Arrastre por parte de un gráfico.](./media/app-insights-portal/12-drag.png)



## Valores de granularidad y punto

Mantenga el cursor sobre el gráfico para mostrar los valores de las métricas en ese momento.

![Mantener el mouse sobre un gráfico](./media/app-insights-portal/02-focus.png)

Se agrega el valor de la métrica en un momento determinado en el intervalo de muestreo anterior.

El intervalo de muestreo o "granularidad" se muestra en la parte superior de la hoja.

![El encabezado de una hoja.](./media/app-insights-portal/11-grain.png)

Puede ajustar la granularidad en la hoja Intervalo de tiempo:

![El encabezado de una hoja.](./media/app-insights-portal/grain.png)

La granularidad disponible depende del intervalo de tiempo que seleccione. Las granularidades explícitas son alternativas a la granularidad "automática" para el intervalo de tiempo.

## Hoja de información general de la aplicación

La hoja de información general (página) de la aplicación muestra un resumen de las métricas de diagnóstico clave de la aplicación y sirve de puerta de enlace a las demás características del portal.

Haga clic en:

* **Cualquier gráfico o icono** para ver más detalles.
* **Diagnósticos** para obtener acceso a páginas predefinidas de otras métricas.
* **Explorador de métricas** para crear páginas de las métricas de su elección.
* **Búsqueda** para investigar casos específicos de eventos como solicitudes, excepciones o seguimientos de registro.


![Rutas principales para ver los datos de telemetría](./media/app-insights-portal/010-oview.png)


### Personalizar hoja de información general 

Elija lo que desea ver en la información general. En Personalizar, puede insertar títulos de sección, arrastrar iconos y gráficos, quitar elementos y agregar nuevos iconos y gráficos de la galería.

![Haga clic en Editar. Arrastre los iconos y gráficos. Agregue iconos de la galería. Luego haga clic en Hecho.](./media/app-insights-portal/020-customize.png)

## Paneles

El panel del Portal de Azure es la página principal que aparece al iniciar sesión en [el portal](https://portal.azure.com) por primera vez. En él, puede juntar gráficos e iconos (grupos de gráficos) de diversos recursos.

![Haga clic en Editar. Arrastre los iconos y gráficos. Agregue iconos de la galería. Luego haga clic en Hecho.](./media/app-insights-portal/30.png)

Cuando está consultando una hoja o un gráfico especialmente interesante, puede anclarlo al panel. Lo verá la próxima vez que regrese.

![Para anclar un gráfico, mantenga el puntero encima y haga clic en "..." en el encabezado.](./media/app-insights-portal/33.png)

Puede guardar más de un panel y cambiar de uno a otro. Cuando se ancla un gráfico o una hoja, se agrega al panel actual.

![Para cambiar entre los paneles, haga clic en Panel y seleccione un panel guardado. Para crear un panel y guardarlo, haga clic en Nuevo. Para reorganizar, haga clic en Editar.](./media/app-insights-portal/32.png)

Por ejemplo, podría tener un panel para mostrarlo en pantalla completa en la sala de reuniones y otro para el desarrollo general.


En el panel, una hoja aparece como un icono. Haga clic en él para ir a la hoja. Un gráfico replica el gráfico en su ubicación original.


![](./media/app-insights-portal/35.png)


## Hojas de métricas

Al recorrer la hoja de información general para obtener más detalles, llega al Explorador de métricas (incluso si tiene un título más específico).

También puede usar el botón Explorador de métricas para crear una nueva hoja, que puede editar y guardar.


![En la hoja de información general, haga clic en Métricas](./media/app-insights-portal/16-metrics.png)

### Edición de gráficos y cuadrículas

Para agregar un nuevo gráfico a la hoja:

![En el Explorador de métricas, elija Agregar gráfico](./media/app-insights-portal/04-add.png)

Seleccione un gráfico nuevo o uno existente para editar lo siguiente:

![Seleccionar una o más métricas](./media/app-insights-portal/08-select.png)

Puede mostrar más de una métrica en un gráfico, aunque hay restricciones sobre las combinaciones que se pueden mostrar juntas. Cuando elija una métrica, algunas otras se deshabilitan.

Si ha codificado las [métricas personalizadas](app-insights-api-custom-events-metrics.md#track-metric) en la aplicación (llamadas a TrackMetric y TrackEvent), aparecerán aquí.

### Segmentación de los datos

Seleccione un diagrama o cuadrícula, cambie a agrupación y elija una propiedad para agruparla por:

![Seleccionar Agrupación en, seleccione una propiedad en Agrupar por](./media/app-insights-portal/15-segment.png)

Si ha codificado las métricas personalizadas en la aplicación e incluyen [valores de propiedad](app-insights-api-custom-events-metrics.md#properties), podrá seleccionar la propiedad en la lista.

¿Es demasiado pequeño el gráfico para los datos segmentados? Ajuste el alto:

![Ajuste de la barra deslizante](./media/app-insights-portal/18-height.png)

### Filtrado de los datos

Para ver solo las métricas para un conjunto de valores de propiedad seleccionado:

![Hacer clic en Filtro, expanda una propiedad y comprobar algunos valores](./media/app-insights-portal/19-filter.png)

Si no selecciona ningún valor para una propiedad determinada, es lo mismo que seleccionar todas ellas: no hay ningún filtro en esa propiedad.

Observe los recuentos de eventos junto a cada valor de propiedad. Al seleccionar valores de una propiedad, se ajustan los recuentos junto con otros valores de propiedades.

### Almacenamiento de la hoja de métricas

Cuando haya creado algunos gráficos, guárdelos como favorito. Si trabaja en una cuenta de organización, puede elegir si compartirlo con otros miembros del equipo.

![Elegir un favorito](./media/app-insights-portal/21-favorite-save.png)

Para ver de nuevo la hoja, **vaya a la hoja de información general** y abra Favoritos:

![En la hoja de información general, elegir Favoritos](./media/app-insights-portal/22-favorite-get.png)

Si eligió el intervalo de tiempo relativo al guardar, la hoja se actualizará con la métrica más reciente. Si eligió el intervalo de tiempo absoluto, mostrará los mismos datos cada vez.

### Restablecimiento de la hoja

Si edita una hoja, pero le gustaría volver al conjunto original guardado, haga clic en Restablecer.

![En los botones situados en la parte superior del Explorador de métricas](./media/app-insights-portal/17-reset.png)

## Search

En Búsqueda se muestran eventos individuales, como vistas de páginas, solicitudes, excepciones, seguimientos de registro y eventos personalizados. No se muestran métricas agregadas o instancias de la llamada TrackMetric().

> [AZURE.NOTE] Si la aplicación genera muchos datos de telemetría (y está usando la versión 2.0.0-beta3, o una posterior, del SDK de ASP.NET), el módulo de muestreo adaptable reducirá automáticamente el volumen que se envía al portal mediante el envío de solamente una fracción representativa de los eventos. Sin embargo, los eventos relacionados con la misma solicitud se seleccionarán o se anulará su selección como grupo, por lo que puede navegar entre ellos. [Más información sobre el muestreo](app-insights-sampling.md).

Abra la búsqueda de diagnóstico:

![Open diagnostic search](./media/app-insights-portal/01-open-Diagnostic.png)

Abra la hoja Filtro y elija los tipos de evento que desea ver. (Si, posteriormente, desea restaurar los filtros con los que abrió la hoja, haga clic en Restablecer).

![Elija Filtrar y seleccione los tipos de telemetría](./media/app-insights-portal/02-filter-req.png)

### Filtro de los valores de propiedad

Puede filtrar eventos por los valores de sus propiedades. Las propiedades disponibles dependen de los tipos de evento que haya seleccionado.

Por ejemplo, seleccione solicitudes con un código de respuesta específico.

![Expanda una propiedad y elija un valor](./media/app-insights-portal/03-response500.png)

El hecho de no elegir ningún valor de una propiedad determinada tiene el mismo efecto que elegir todos los valores; se desactiva el filtrado en esa propiedad.

> [AZURE.NOTE] Si la aplicación genera muchos datos de telemetría, el módulo de muestreo adaptable reducirá automáticamente el volumen que se envía al portal mediante el envío de solamente una fracción representativa de los eventos. Los eventos que forman parte de la misma operación se seleccionarán o se anulará su selección como grupo, por lo que puede navegar entre los eventos relacionados. [Más información sobre el muestreo.](app-insights-sampling.md)


### Acotación de la búsqueda

Observe que los recuentos a la derecha de los valores de filtro muestran cuántas repeticiones hay en el conjunto filtrado actual.

En este ejemplo, es obvio que la solicitud `Reports/Employees` genera la mayor parte de los 500 errores:

![Expanda una propiedad y elija un valor](./media/app-insights-portal/04-failingReq.png)

Además ,si desea ver también qué otros eventos se estaban produciendo durante este tiempo, puede activar **Incluir eventos con propiedades no definidas**.

### Guardado de la búsqueda

Cuando haya establecido todos los filtros que desea, puede guardar la búsqueda como favorito. Si trabaja en una cuenta de organización, puede elegir si compartirla con otros miembros del equipo.

![Haga clic en Favoritos, establezca el nombre y haga clic en Guardar](./media/app-insights-portal/08-favorite-save.png)


Para ver de nuevo la búsqueda, **vaya a la hoja de información general** y abra Favoritos:

![Favoritos](./media/app-insights-portal/22-favorite-get.png)

Si ha guardado con el intervalo de tiempo relativo, la hoja abierta de nuevo tiene los datos más recientes. Si ha guardado con el intervalo de tiempo absoluto, verá los mismos datos cada vez.

<!---HONumber=AcomDC_0224_2016-->