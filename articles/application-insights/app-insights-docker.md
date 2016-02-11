<properties 
	pageTitle="Supervisar aplicaciones Docker en Application Insights" 
	description="En Application Insights, se pueden mostrar los contadores de rendimiento, los eventos y las excepciones de Docker, además de la telemetría de las aplicaciones en contenedor." 
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
	ms.date="12/01/2015" 
	ms.author="awills"/>
 
# Supervisar aplicaciones Docker en Application Insights

Los eventos de ciclo de vida y los contadores de rendimiento de los contenedores [Docker](https://www.docker.com/) pueden mostrarse en gráficos en Application Insights. Instale la imagen de [Application Insights](app-insights-overview.md) en un contenedor del host y se mostrarán los contadores de rendimiento del host, así como de las demás imágenes.

Con Docker, distribuye sus aplicaciones en contenedores ligeros junto con todas las dependencias. Estos contenedores se ejecutan en cualquier equipo host que ejecute un motor Docker.

Al ejecutar la [imagen de Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) en el host Docker, obtendrá estas ventajas:

* Telemetría del ciclo de vida sobre todos los contenedores que se ejecutan en el host: start, stop, etc.
* Contadores de rendimiento de todos los contenedores. CPU, memoria, uso de red y mucho más.
* Si [instaló el SDK de Application Insights](app-insights-java-live.md) en las aplicaciones que se ejecutan en los contenedores, toda la telemetría de dichas aplicaciones tendrán propiedades adicionales que identifiquen el contenedor y el equipo host. De esta forma, si tiene, por ejemplo, instancias de una aplicación que se ejecuta en más de un host, podrá filtrar fácilmente los datos de telemetría de la aplicación en función del host.

![ejemplo](./media/app-insights-docker/00.png)


## Configurar el recurso de Application Insights

1. Inicie sesión en el [Portal de Microsoft Azure](https://azure.com) y abra el recurso de Application Insights de la aplicación; o [cree uno nuevo](app-insights-create-new-resource.md). 

    *¿Qué recurso debería usar?* Si las aplicaciones que se ejecutan en el host las desarrolló otra persona, deberá [crear un nuevo recurso de Application Insights](app-insights-create-new-resource.md). Es el sitio donde verá y analizará los datos de telemetría. (Seleccione "Otro" como tipo de aplicación).

    Pero si usted es el desarrollador de las aplicaciones, esperamos que [haya agregado el SDK de Application Insights](app-insights-java-live.md) a cada una de ellas. Si todos son realmente componentes de una aplicación empresarial única, puede configurarlas todas para que envíen telemetría a un recurso y usará ese mismo recurso para mostrar los datos de rendimiento y de ciclo de vida de Docker.

    Un tercer escenario es que haya desarrollado la mayoría de las aplicaciones, pero use recursos independientes para mostrar sus datos de telemetría. En ese caso, probablemente también querrá crear un recurso independiente para los datos de Docker.

2.	Agregue el icono de Docker; para ello, elija **Agregar icono**, arrastre el icono de Docker desde la galería y luego haga clic en **Listo**.

    ![ejemplo](./media/app-insights-docker/03.png)


3. Haga clic en la lista desplegable **Essentials** y copie la clave de instrumentación. Se usará para indicar al SDK dónde puede enviar sus datos de telemetría.


    ![ejemplo](./media/app-insights-docker/02-props.png)

Tenga a mano la ventana del explorador, volverá a ella pronto para examinar los datos de telemetría.


## Ejecutar la supervisión de Application Insights en el host
 
Ahora que tiene un lugar donde mostrar los datos de telemetría, puede configurar la aplicación del contenedor que los recopilará y enviará.

1.	Conéctese al host Docker. 
2.	Edite la clave de instrumentación de este comando y después ejecútelo:
 
    ```

    docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
    ```

Solo es necesaria una imagen de Application Insights por cada host de Docker. Si la aplicación se implementa en varios hosts de Docker, repita el comando en todos los hosts.

## Actualización de la aplicación

Si la aplicación se instrumenta con el [SDK de Application Insights para Java](app-insights-java-get-started.md), agregue la siguiente línea en el archivo ApplicationInsights.xml del proyecto, en el elemento `<TelemetryInitializers>`:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Esto agrega información de Docker como el contenedor y el identificador de host a cada uno de los elementos de telemetría que se envían desde la aplicación.

## Visualización de la telemetría

Vuelva al recurso de Application Insights en el Portal de Azure.

Haga clic en el icono de Docker.

Pronto verá que llegan datos desde la aplicación de Docker, especialmente si tiene otros contenedores que se ejecutan en el motor de Docker.


Estas son algunas de las vistas que puede obtener.

### Contadores de rendimiento por host, actividad por imagen


![ejemplo](./media/app-insights-docker/10.png)


![ejemplo](./media/app-insights-docker/11.png)



Haga clic en cualquier host o imagen para obtener más detalles.



Para personalizar la vista, haga clic en cualquiera de los gráficos o en el encabezado de cuadrícula, o bien use Agregar gráfico.

[Más información sobre el explorador de métricas](app-insights-metrics-explorer.md).

### Eventos de contenedor Docker


![ejemplo](./media/app-insights-docker/13.png)

Para investigar eventos individuales, haga clic en [Buscar](app-insights-diagnostic-search.md). Busque y filtre para encontrar los eventos deseados. Haga clic en cualquier evento para más detalles.
 
### Excepciones por el nombre del contenedor
 

![ejemplo](./media/app-insights-docker/14.png)

### Contexto de Docker agregado a los datos de telemetría de la aplicación

Solicite datos de telemetría enviados desde la aplicación instrumentada con el SDK de AI, enriquecidos con el contexto de Docker:

![ejemplo](./media/app-insights-docker/16.png)

Contadores de rendimiento de tiempo de procesador y memoria disponible, enriquecidos y agrupados según el nombre del contenedor de Docker:


![ejemplo](./media/app-insights-docker/15.png)





## Preguntas y respuestas

*¿Qué me da Application Insights que no me dé Docker?*

* Desglose detallado de los contadores de rendimiento por contenedor e imagen.
* Integración de datos de contenedores y aplicaciones en un panel.
* [Exportación de la telemetría](app-insights-export-telemetry.md), para su análisis posterior en una base de datos, Power BI u otro panel.

*¿Cómo se obtienen datos de telemetría de la propia aplicación?*

* Instale el SDK de Application Insights en la aplicación. Más información sobre: [aplicaciones web de Java](app-insights-java-get-started.md) y [aplicaciones web de Windows](app-insights-asp-net.md).

<!---HONumber=AcomDC_1203_2015-->