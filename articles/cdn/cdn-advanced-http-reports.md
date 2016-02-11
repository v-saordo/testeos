<properties 
	pageTitle="CDN - Informes de HTTP avanzados" 
	description="Informes de HTTP avanzados en la red CDN de Microsoft Azure Estos informes proporcionan información detallada sobre la actividad de la red CDN." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>

# Informes de HTTP avanzados en la red CDN de Microsoft Azure

## Información general

En este documento se explican los informes de HTTP avanzados en la red CDN de Microsoft Azure. Estos informes proporcionan información detallada sobre la actividad de la red CDN.

> [AZURE.NOTE]Los informes de HTTP avanzados es una característica del nivel Premium de la red CDN. Para una comparación de las características de los niveles Standard y Premium de la red CDN, consulte [Información general de la red de entrega de contenido (CDN) de Azure](cdn-overview.md).

## Acceso a los informes de HTTP avanzados

1. En la hoja de perfil de la red CDN, haga clic en el botón **Administrar**.

	![Botón Administrar en la hoja Perfil de red CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)
	
	Se abre el portal de administración de la red CDN.
	
2. Mantenga el cursor sobre la pestaña **Análisis** y después sobre el control flotante **Informes de HTTP avanzados**. Haga clic en **Plataforma grande HTTP**.
	
	Se muestran las opciones de informe.

## Informes de geografía (basados en mapas)

Hay cinco informes que usan un mapa para indicar las regiones desde las que se solicita el contenido. Estos informes son World Map, United States Map, Canada Map, Europe Map y Asia Pacific Map.

Cada informe basado en un mapa clasifica las entidades geográficas (es decir, países, estados y provincias), según el porcentaje de visitas originadas en esa región. Además, se proporciona un mapa que le ayudará a visualizar las ubicaciones desde las que se solicita el contenido. Para ello se usa la codificación en colores de cada región según la cantidad de demanda experimentada en dicha región. Las regiones más claras indican una demanda menor de contenido y las más oscuras indican mayores niveles de petición de contenido.

Debajo del mapa, se ofrece información detallada del tráfico y del ancho de banda para cada región. Esto permite ver el número total de visitas, el porcentaje de visitas, la cantidad total de datos transferidos (en gigabytes) y el porcentaje de datos transferidos en cada región. Consulte una descripción de cada una de estas métricas. Por último, cuando mantiene el puntero sobre una región (por ejemplo, país, estado o provincia), el nombre y el porcentaje de visitas que se produjeron en la región se mostrarán como una información sobre herramientas.

Se proporciona más abajo una descripción breve de cada tipo de informe de geografía basado en mapas.

Nombre del informe | Descripción
------------|------------
World Map | Este informe permite ver la demanda en todo el mundo del contenido de la red CDN. Cada país está codificado en colores en el mapamundi para indicar el porcentaje de visitas originadas en dicha región.
United States Map | Este informe permite ver la demanda del contenido de la red CDN en Estados Unidos. Cada estado está codificado en colores en este mapa para indicar el porcentaje de visitas originados en esta región.
Canada Map | Este informe permite ver la demanda del contenido de la red CDN en Canadá. Cada provincia está codificada en colores en este mapa para indicar el porcentaje de visitas originadas en esta región.
Europe Map | Este informe permite ver la demanda del contenido de la red CDN en Europa. Cada país está codificado en colores en este mapa para indicar el porcentaje de visitas originadas en esta región.
Asia Pacific Map | Este informe permite ver la demanda del contenido de la red CDN en Asia. Cada país está codificado en colores en este mapa para indicar el porcentaje de visitas originadas en esta región. 

## Informes de geografía (gráficos de barras)

Existen dos informes adicionales que proporcionan información estadística de acuerdo con la ubicación geográfica, que son Top Cities y Top Countries. Estos informes clasifican ciudades y países, respectivamente, según el número de visitas originadas en esas regiones. Tras generar este tipo de informe, un gráfico de barras indicará las 10 ciudades o países principales que solicitaron contenido en una plataforma específica. Este gráfico de barras permite evaluar rápidamente las regiones que generan la mayor cantidad de solicitudes para su contenido.

El lado izquierdo del gráfico (eje y) indica cuántas visitas se han producido en la región especificada. Directamente debajo del gráfico (eje x) se muestra una etiqueta para cada una de las 10 regiones principales.

### Uso de los gráficos de barras

* Si mantiene el cursor sobre una barra, el nombre y el número total de visitas que se produjeron en la región se mostrarán como una información sobre herramientas.
* La información sobre herramientas en el informe Top Cities identifica a la ciudad por su nombre, el estado o provincia y por la abreviatura del país.
* Si no se pudo determinar la ciudad o región (es decir, el estado o provincia) desde el que se originó una solicitud, se indicará que son desconocidas. Si el país es desconocido, se muestran dos signos de interrogación (es decir, ??).
* Un informe puede incluir métricas para "Europa" o "Región de Asia y el Pacífico". Estos elementos no están diseñados para proporcionar información estadística sobre todas las direcciones IP de esas regiones. En su lugar, solo se aplican a las solicitudes que se originan de las direcciones IP que se distribuyen por Europa o por la región de Asia y el Pacífico, en lugar de por una determinada ciudad o país.

Los datos usados para generar el gráfico de barras pueden verse debajo de él. Allí encontrará el número total de visitas, el porcentaje de visitas, la cantidad de datos transferidos (en gigabytes) y el porcentaje de datos transferidos para las 250 regiones principales. Consulte una descripción de cada una de estas métricas.

A continuación se proporciona una breve descripción para ambos tipos de informes.

Nombre del informe | Descripción
------------|------------
Top Cities | Este informe clasifica las ciudades según el número de visitas originadas en esa región.
Top Countries | Este informe clasifica los países según el número de visitas originadas en esa región.

## Daily Summary

El informe Daily Summary permite ver el número total de visitas y los datos transferidos a diario en una plataforma particular. Esta información puede usarse para distinguir rápidamente los patrones de actividad de la red CDN. Por ejemplo, este informe puede ayudar a detectar qué días han tenido un tráfico mayor o menor que el previsto.

Tras generar este tipo de informe, un gráfico de barras proporcionará una indicación visual de la cantidad de demanda específica de la plataforma experimentada a diario durante el período de tiempo cubierto por el informe. Lo hará mostrando una barra para cada día en el informe. Por ejemplo, si se selecciona el período de tiempo llamado "Last Week", se generará un gráfico de barras con siete barras. Cada barra indica el número total de visitas realizadas en ese día.

El lado izquierdo del gráfico (eje y) indica cuántas visitas se produjeron en la fecha especificada. Directamente debajo del gráfico (eje x), se muestra una etiqueta con la fecha (formato: AAA-MM-DD) para cada día incluido en el informe.

> [AZURE.TIP]Si mantiene el cursor sobre una barra, el número total de visitas que se produjeron en esa fecha se mostrará como una información sobre herramientas.

Los datos usados para generar el gráfico de barras pueden verse debajo de él. Allí encontrará el número total de visitas y la cantidad de datos transferidos (en gigabytes) para cada día que cubre el informe.

## By Hour

El informe By Hour permite ver el número total de visitas y los datos transferidos cada hora en una plataforma particular. Esta información puede usarse para distinguir rápidamente los patrones de actividad de la red CDN. Por ejemplo, este informe puede ayudar a detectar los períodos de tiempo durante el día que han tenido un tráfico mayor o menor que el previsto.

Tras generar este tipo de informe, un gráfico de barras proporcionará una indicación visual de la cantidad de demanda específica de la plataforma experimentada cada hora durante el período de tiempo cubierto por el informe. Lo hará mostrando una barra para cada hora cubierta por el informe. Por ejemplo, si se selecciona un período de tiempo de 24 horas, se generará un gráfico de barras con veinticuatro barras. Cada barra indica el número total de visitas realizadas durante esa hora.

El lado izquierdo del gráfico (eje y) indica cuántas visitas se produjeron en la hora especificada. Directamente debajo del gráfico (eje x), se muestra una etiqueta con la fecha/hora (formato: AAA-MM-DD) para cada hora incluida en el informe. La hora se notifica con formato de 24 horas y se especifica mediante el uso de la zona horaria UTC/GMT.

> [AZURE.TIP]Si mantiene el cursor sobre una barra, el número total de visitas que se produjeron en esa hora se mostrará como una información sobre herramientas.

Los datos usados para generar el gráfico de barras pueden verse debajo de él. Allí encontrará el número total de visitas y la cantidad de datos transferidos (en gigabytes) para cada hora que cubre el informe.

## By File

El informe By File permite ver la cantidad de demanda y el tráfico generados en una plataforma concreta de los activos más solicitados. Tras generar este tipo de informe, se generará un gráfico de barras con los 10 activos principales más solicitados durante el período de tiempo especificado.

> [AZURE.NOTE]A efectos de este informe, las direcciones URL de CNAME perimetrales se convierten en sus direcciones URL de la red CDN equivalentes. Esto permite un recuento preciso del número total de visitas asociadas a un activo, con independencia de la red CDN o de la dirección URL del CNAME perimetral que usó para solicitarlo.

El lado izquierdo del gráfico (eje y) indica el número de solicitudes para cada activo durante el período de tiempo especificado. Directamente debajo del gráfico (eje x) se muestra una etiqueta con el nombre de archivo para cada uno de los 10 activos principales solicitados.

Los datos usados para generar el gráfico de barras pueden verse debajo de él. Allí encontrará la información siguiente para cada uno de los 250 activos principales solicitados: la ruta de acceso relativa, el número total de visitas, el porcentaje de visitas, la cantidad de datos transferidos (en gigabytes) y el porcentaje de datos transferidos.

## By File Detail

El informe By File Detail permite ver la cantidad de demanda y el tráfico generados en una plataforma concreta de un activo específico. En la parte superior de este informe se encuentra la opción File Details For. Esta opción proporciona una lista de los activos más solicitados en la plataforma seleccionada. Para generar un informe By File Detail, debe seleccionar el activo deseado en la opción File Details For. Después, un gráfico de barras indicará la cantidad de demanda diaria que se generó durante el período de tiempo especificado.

El lado izquierdo del gráfico (eje y) indica el número total de solicitudes que ha tenido un activo en un día concreto. Directamente debajo del gráfico (eje x) se muestra una etiqueta con la fecha (formato: AAA-MM-DD) en la que se notificó la demanda de CDN para el activo.

Los datos usados para generar el gráfico de barras pueden verse debajo de él. Allí encontrará el número total de visitas y la cantidad de datos transferidos (en gigabytes) para cada día que cubre el informe.

## By File Type

El informe By File Type permite ver la cantidad de demanda y el tráfico generados por tipo de archivo. Tras generar este tipo de informe, un gráfico de anillos indicará el porcentaje de visitas generadas por los 10 tipos de archivos principales.

> [AZURE.TIP]Si mantiene el cursor sobre un segmento del gráfico de anillos, el tipo de medio de Internet de ese tipo de archivo se mostrará como una información sobre herramientas.

Los datos usados para generar el gráfico de anillos pueden verse debajo de él. Allí encontrará la extensión del nombre de archivo, el tipo de medio de Internet, el número total de visitas, el porcentaje de visitas, la cantidad de datos transferidos (en gigabytes) y el porcentaje de datos transferidos para cada uno de los 250 tipos de archivos principales.

## By Directory

El informe By Directory permite ver la cantidad de demanda y el tráfico generados en una plataforma concreta del contenido de un directorio específico. Tras generar este tipo de informe, un gráfico de barras indicará el número total de visitas generadas por el contenido de los 10 directorios principales.

### Uso del gráfico de barras

* Mantenga el cursor sobre una barra para ver la ruta de acceso relativa al directorio correspondiente.
* El contenido almacenado en una subcarpeta de un directorio no cuenta al calcular la demanda por directorio. Este cálculo se basa únicamente en el número de solicitudes generadas para el contenido almacenado en el directorio real.
* A efectos de este informe, las direcciones URL de CNAME perimetrales se convierten en sus direcciones URL de la red CDN equivalentes. Esto permite un recuento preciso de todas las estadísticas asociadas a un activo, con independencia de la red CDN o de la dirección URL del CNAME perimetral que usó para solicitarlo.

El lado izquierdo del gráfico (eje y) indica el número total de solicitudes para el contenido almacenado en los 10 directorios principales. Cada barra del gráfico representa un directorio. Use el esquema de codificación en colores para hacer coincidir una barra en un directorio enumerado en la sección de los 250 directorios completos principales.

Los datos usados para generar el gráfico de barras pueden verse debajo de él. Allí encontrará la información siguiente para cada uno de los 250 directorios principales: la ruta de acceso relativa, el número total de visitas, el porcentaje de visitas, la cantidad de datos transferidos (en gigabytes) y el porcentaje de datos transferidos.

## By Browser

El informe By Browser permite ver qué exploradores se usan para solicitar el contenido. Tras generar este tipo de informe, un gráfico circular indicará el porcentaje de solicitudes controladas por los 10 exploradores principales.

### Uso del gráfico circular

* Mantenga el cursor sobre un segmento del gráfico circular para ver el nombre y la versión del explorador.
* A efectos de este informe, cada combinación única de explorador y versión se considera un explorador diferente.
* El sector denominado "Other" indica el porcentaje de solicitudes controladas por todos los demás exploradores y versiones.

Los datos usados para generar el gráfico circular pueden verse debajo de él. Allí encontrará el tipo de explorador y el número de versión, el número total de visitas y el porcentaje de visitas para cada uno de los 250 exploradores principales.

## By Referrer

El informe By Referrer permite ver los orígenes de referencia principales del contenido en la plataforma seleccionada. Un origen de referencia indica el nombre de host desde el que se generó la solicitud. Tras generar este tipo de informe, un gráfico de anillos indicará la cantidad de demanda (es decir, visitas) generada por los 10 orígenes de referencia principales.

El lado izquierdo del gráfico (eje y) indica el número total de solicitudes que ha tenido un activo para cada origen de referencia. Cada barra del gráfico representa un origen de referencia. Use el esquema de codificación en colores para hacer coincidir una barra en un origen de referencia enumerado en la sección de los 250 orígenes de referencia principales.

Los datos usados para generar el gráfico de barras pueden verse debajo de él. Allí encontrará la dirección URL, el número total de visitas y el porcentaje de visitas generadas para cada uno de los 250 orígenes de referencia principales.

## By Download

El informe By Download permite analizar los patrones de descarga para el contenido más solicitado. La parte superior del informe contiene un gráfico de barras que compara las descargas intentadas con descargas completadas para los 10 activos principales solicitados. Cada barra está codificada en colores, según sea un intento de descarga (azul) o una descarga completada (verde).

> [AZURE.NOTE]A efectos de este informe, las direcciones URL de CNAME perimetrales se convierten en sus direcciones URL de la red CDN equivalentes. Esto permite un recuento preciso de todas las estadísticas asociadas a un activo, con independencia de la red CDN o de la dirección URL del CNAME perimetral que usó para solicitarlo.

El lado izquierdo del gráfico (eje y) indica el nombre de archivo para cada uno de los 10 activos solicitados principales. Directamente debajo del gráfico (eje x) se muestran las etiquetas con el número total de descargas intentadas o completadas.

Directamente debajo del gráfico de barras, se mostrará la información siguiente para los 250 activos solicitados principales: la ruta de acceso relativa (incluido el nombre de archivo), el número de veces que se descargó completamente, el número de veces que se solicitó y el porcentaje de solicitudes que dieron como resultado una descarga completa.

> [AZURE.TIP]Un cliente HTTP (es decir, un explorador) no notificará a la red CDN cuando un activo se ha descargado completamente. Como resultado, tenemos que calcular si un activo se ha descargado completamente según los códigos de estado y las solicitudes de intervalo de bytes. Lo primero que buscamos al realizar este cálculo es si la solicitud produce un código de estado 200 OK. Si es así, nos centramos en las solicitudes de intervalo de bytes para asegurarnos de que cubren el activo completo. Por último, comparamos la cantidad de datos transferidos con el tamaño del activo solicitado. Si los datos transferidos son iguales o mayores que el tamaño de archivo y las solicitudes de intervalo de bytes son adecuadas para ese activo, la visita se contará como una descarga completa.
>
>Debido a la naturaleza interpretativa de este informe, hay que tener en cuenta los puntos siguientes que pueden alterar la coherencia y la precisión de este informe.
>
>* Los patrones de tráfico no se puede predecir con precisión cuando los agentes de usuario se comportan de manera diferente. Esto puede producir resultados de descarga completada que son superiores al 100 %.
>* Los activos que usan la descarga progresiva de HTTP no se pueden representar con precisión en este informe. Esto es debido a usuarios que buscan distintas posiciones en un vídeo.

## By 404 Errors

El informe By 404 Errors permite identificar el tipo de contenido que genera el mayor número de códigos de estado 404 No encontrado. La parte superior del informe contiene un gráfico de barras para los 10 activos principales para los que se devolvió un código de estado 404 No encontrado. Este gráfico de barras compara el número total de solicitudes con las solicitudes que dieron como resultado un código de estado 404 No encontrado para esos activos. Cada barra está codificada en colores. Se usa una barra amarilla para indicar que la solicitud dio como resultado un código de estado 404 No encontrado. Se usa una barra roja para indicar el número total de solicitudes para el activo.

> [AZURE.NOTE]A efectos de este informe, tenga en cuenta lo siguiente:
>
>* Una visita representa cualquier solicitud de un activo, con independencia del código de estado.
>* Las direcciones URL de CNAME perimetrales se convierten en sus direcciones URL de la red CDN equivalentes. Esto permite un recuento preciso de todas las estadísticas asociadas a un activo, con independencia de la red CDN o de la dirección URL del CNAME perimetral que usó para solicitarlo.

El lado izquierdo del gráfico (eje y) indica el nombre de archivo para cada uno de los 10 activos solicitados principales que dieron como resultado un código de estado de 404 No encontrado. Directamente debajo del gráfico (eje x) se muestran las etiquetas con el número total de solicitudes y el número de solicitudes que dieron como resultado un código de estado 404 No encontrado.

Directamente debajo del gráfico de barras, se mostrará la información siguiente para los 250 activos solicitados principales: la ruta de acceso relativa (incluido el nombre de archivo), el número de solicitudes que dieron como resultado un código de estado 404 No encontrado, el número de veces que se solicitó el activo y el porcentaje de solicitudes que dieron como resultado un código de estado de 404 No encontrado.

## Consulte también
* [Información general de la red CDN de Azure](cdn-overview.md)
* [Estadísticas en tiempo real en CDN de Microsoft Azure](cdn-real-time-stats.md)
* [Invalidación del comportamiento HTTP predeterminado mediante el motor de reglas](cdn-rules-engine.md)
* [Análisis del rendimiento perimetral](cdn-edge-performance.md)

<!---HONumber=AcomDC_1203_2015-->