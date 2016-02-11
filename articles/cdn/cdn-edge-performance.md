<properties 
	pageTitle="CDN - Análisis del rendimiento perimetral" 
	description="Análisis del rendimiento del nodo perimetral en la red CDN de Microsoft Azure. El análisis del rendimiento perimetral proporciona información pormenorizada sobre tráfico y uso del ancho de banda para la red CDN." 
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

# Análisis del rendimiento del nodo perimetral en la red CDN de Microsoft Azure

## Información general
El análisis del rendimiento perimetral proporciona información pormenorizada sobre tráfico y uso del ancho de banda para la red CDN. Esta información puede usarse para generar estadísticas de tendencias que permiten obtener información sobre cómo los activos se almacenan en caché y se entregan a los clientes. A su vez, esto permite diseñar una estrategia sobre la forma de optimizar la entrega de contenido y determinar qué problemas se deben abordar para aprovechar mejor la red CDN. Como resultado, no solo podrá mejorar el rendimiento de la entrega de datos, sino que también podrá reducir los costos de la red CDN.

> [AZURE.NOTE]El análisis de rendimiento perimetral es una característica del nivel Premium de la red CDN. Para una comparación de las características de los niveles Standard y Premium de la red CDN, consulte [Información general de la red de entrega de contenido (CDN) de Azure](cdn-overview.md).
>
> Todos los informes usan la notación de hora UTC/GMT al especificar una fecha y hora.

## Informes y recopilación de registros

Para poder generar los informes, el módulo Análisis del rendimiento perimetral tiene que recopilar datos de actividad de la red CDN. Este proceso de recopilación se produce una vez al día y se centra en la actividad que tuvo lugar a lo largo del día anterior. Esto quiere decir que las estadísticas de un informe representan una muestra de las estadísticas del día en el momento en el que se procesó, y no contienen necesariamente el conjunto completo de datos para el día actual. Es la función principal de estos informes evaluar el rendimiento. No deben usarse con fines de facturación o estadísticas numéricas exactas.

> [AZURE.NOTE]Los datos sin procesar a partir de los que se generan los informes de Análisis del rendimiento perimetral están disponibles al menos durante 90 días.

## Panel

El panel de Análisis de rendimiento perimetral realiza un seguimiento actual e histórico del tráfico de la red CDN a través de un gráfico y de estadísticas. Use este panel para detectar las tendencias recientes y a largo plazo en el rendimiento del tráfico de la red CDN de su cuenta.

Este panel consta de:

* Un gráfico interactivo que permite la visualización de métricas clave y tendencias.
* Una escala de tiempo que proporciona una sensación de patrones a largo plazo de las tendencias y las métricas clave.
* Las métricas clave y la información estadística acerca de cómo nuestra red CDN mejora el tráfico del sitio con relación al rendimiento general, el uso y la eficiencia.

### Acceso al panel de rendimiento perimetral

1. En la hoja de perfil de la red CDN, haga clic en el botón **Administrar**.

	![Botón Administrar en la hoja Perfil de red CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
	
	Se abre el portal de administración de la red CDN.
	
2. Mantenga el puntero sobre la pestaña **Análisis** y, después sobre el control flotante **Análisis del rendimiento perimetral**. Haga clic en **Panel**.
	
	Se muestra el panel de análisis de nodo perimetral.

### Gráfico

El panel contiene un gráfico que realiza el seguimiento de una métrica durante el período de tiempo seleccionado en la escala de tiempo que aparece justo debajo de ella. Se muestra una escala de tiempo que presenta hasta los últimos dos años de actividad de la red CDN directamente debajo del gráfico.

#### Uso del gráfico

* De forma predeterminada, el gráfico muestra la tasa de eficiencia de la memoria caché durante los últimos 30 días.
* Este gráfico se genera a partir de datos que se recopilan a diario.
* Al mantener el puntero sobre un día en el gráfico de líneas, indicará una fecha y el valor de la métrica en esa fecha.
* Haga clic en Resaltar fines de semana para alternar a una superposición de barras verticales de color gris claro que representan los fines de semana en el gráfico. Este tipo de superposición es útil para identificar patrones de tráfico durante los fines de semana.
* Haga clic en Ver hace un año para alternar a una superposición de la actividad del año anterior en el mismo período de tiempo en el gráfico. Este tipo de comparación proporciona información sobre los patrones de uso de la red CDN a largo plazo. La esquina superior derecha del gráfico contiene una leyenda que indica el código de color para cada gráfico de líneas.

#### Actualización del gráfico

* Intervalo de tiempo: realice una de las siguientes acciones:
	* Seleccione la región que desee en la escala de tiempo. El gráfico se actualizará con los datos que se corresponden con el período de tiempo seleccionado.
	* Haga doble clic en el gráfico para mostrar todos los datos históricos disponibles hasta un máximo de dos años.
* Métrica: Haga clic en el icono de gráfico que aparece junto a la métrica deseada. El gráfico y la escala de tiempo se actualizarán con los datos de la métrica correspondiente.


### Estadísticas y mediciones clave

#### Métricas de eficiencia

El propósito de estas métricas es ver si se puede mejorar la eficiencia de la memoria caché. Las principales ventajas derivadas de la eficiencia de la caché son:

* Reducción de la carga en el servidor de origen que a su vez puede dar lugar a:
	* Mejor rendimiento del servidor web
	* Reducción de los costos operacionales.
* Mejora en la aceleración de la entrega de datos ya que atenderá más solicitudes directamente desde la red CDN.

Campo | Descripción
------|------------
Eficiencia de la memoria caché | Indica el porcentaje de los datos transferidos que se atendieron desde la memoria caché. Esta métrica mide cuándo una versión almacenada en caché del contenido solicitado se sirvió directamente desde la red CDN (servidores perimetrales) al solicitante (por ejemplo, un explorador web)
Frecuencia de aciertos | Indica el porcentaje de solicitudes que se atendieron desde la caché. Esta métrica mide cuándo una versión almacenada en caché del contenido solicitado se sirvió directamente desde la red CDN (servidores perimetrales) al solicitante (por ejemplo, un explorador web).
% de bytes remotos - ninguna configuración de caché | Indica el porcentaje de tráfico que se atendió desde los servidores de origen en la red CDN (servidores perimetrales) que no se almacenará en caché como resultado de la característica de omisión de la caché (motor de reglas de HTTP).
% de bytes remotos - caché expirada | Indica el porcentaje de tráfico que se atendió desde servidores de origen en la red CDN (servidores perimetrales) como resultado de una revalidación de contenido obsoleto.

#### Métricas de uso

El propósito de estas métricas es proporcionar información sobre las siguientes medidas de reducción de costos:

* Reducción de los costos operativos a través de la red CDN.
* Reducción de gastos de red CDN mediante compresión y la eficiencia de la memoria caché.

> [AZURE.NOTE]Los números del volumen de tráfico representan el tráfico que se usó en los cálculos de proporciones y porcentajes y puede mostrar solo una parte del tráfico total para los clientes de gran volumen.

Campo | Descripción
------|------------
Promedio de bytes de salida | Indica el número medio de bytes transferidos por cada solicitud que se sirvió desde la red CDN (servidores perimetrales) al solicitante (por ejemplo, un explorador web).
Velocidad de bytes de ninguna configuración de caché | Indica el porcentaje de tráfico que se sirvió desde la red CDN (servidores perimetrales) al solicitante (por ejemplo, un explorador web) que no se almacenará en caché debido a la característica de omisión de la caché.
Velocidad de bytes comprimidos | Indica el porcentaje de tráfico enviado desde la red CDN (servidores perimetrales) al solicitantes (por ejemplo, un explorador web) en un formato comprimido.
Bytes de salida | Indica la cantidad de datos, en bytes, que se enviaron desde la red CDN (servidores perimetrales) al solicitante (por ejemplo, un explorador web).  
Bytes de entrada | Indica la cantidad de datos, en bytes, que se enviaron desde el solicitante (por ejemplo, un explorador web) a la red CDN (servidores perimetrales).
Bytes remotos | Indica la cantidad de datos, en bytes, que se enviaron desde la red CDN y los servidores de origen del cliente a la red CDN (servidores perimetrales).

#### Métricas de rendimiento.

El propósito de estas métricas es realizar un seguimiento del rendimiento general para el tráfico de red CDN.

Campo | Descripción
------|------------
Transfer Rate | Indica la velocidad media a la que se transfirió contenido de la red CDN a un solicitante.
Duración | Indica el tiempo medio, en milisegundos, que se tardó en entregar un activo a un solicitante (por ejemplo, un explorador web).
Compressed Request Rate | Indica el porcentaje de aciertos que se enviaron desde la red CDN (servidores perimetrales) al solicitante (por ejemplo, un explorador web) en un formato comprimido.
4xx Error Rate | Indica el porcentaje de aciertos que generan un código de estado 4xx.
5xx Error Rate | Indica el porcentaje de aciertos que generan un código de estado 5xx.
Aciertos | Indica el número de solicitudes para contenido de red CDN.

#### Secure Traffic Metrics

El propósito de estas métricas es realizar un seguimiento del rendimiento de la red CDN para el tráfico HTTPS.

Campo | Descripción
------|------------
Secure Cache Efficiency | Indica el porcentaje de datos transferidos para solicitudes HTTPS que se atendieron desde la memoria caché. Esta métrica mide cuándo una versión almacenada en caché del contenido solicitado se sirvió directamente desde la red CDN (servidores perimetrales) al solicitante (por ejemplo, un explorador web) a través de HTTPS.
Secure Transfer Rate | Indica la velocidad media a la que se transfirió contenido desde la red CDN (servidores perimetrales) a los solicitantes (por ejemplo, servidores web) a través de HTTPS.
Average Secure Duration | Indica el tiempo medio, en milisegundos, que se tardó en entregar un activo a un solicitante (por ejemplo, un explorador web) a través de HTTPS.
Secure Hits | Indica el número de solicitudes HTTPS para contenido de red CDN.
Secure Bytes Out | Indica la cantidad de tráfico HTTPS, en bytes, que se enviaron desde la red CDN (servidores perimetrales) al solicitante (por ejemplo, un explorador web).

## Informes

Cada informe de este módulo contiene un gráfico y las estadísticas de uso del ancho de banda y del tráfico para distintos tipos de métricas (por ejemplo, códigos de estado HTTP, códigos de estado de la memoria caché, solicitud de dirección URL, etc.). Esta información puede usarse para profundizar más en cómo se ofrece el contenido a los clientes y para ajustar el comportamiento de la red CDN para mejorar el rendimiento de la entrega de datos.

### Acceso a los informes de rendimiento perimetral

1. En la hoja de perfil de la red CDN, haga clic en el botón **Administrar**.

	![Botón Administrar en la hoja Perfil de red CDN](./media/cdn-edge-performance/cdn-manage-btn.png)
	
	Se abre el portal de administración de la red CDN.
	
2. Mantenga el puntero sobre la pestaña **Análisis** y, después sobre el control flotante **Análisis del rendimiento perimetral**. Haga clic en **Objeto grande HTTP**.
	
	Se muestra la pantalla de informes de análisis de nodo perimetral.
	
Informe | Descripción
-------|------------
Daily Summary | Permite ver tendencias de tráfico diarias durante un período de tiempo especificado. Cada barra de este gráfico representa una fecha determinada. El tamaño de la barra indica el número total de aciertos de que se produjeron en esa fecha.
Hourly Summary | Permite ver tendencias de tráfico por horas durante un período de tiempo especificado. Cada barra de este gráfico representa una sola hora de una fecha determinada. El tamaño de la barra indica el número total de aciertos de que se produjeron en esa hora.
Protocolos | Muestra el desglose del tráfico entre los protocolos HTTP y HTTPS. Un gráfico de anillos indica el porcentaje de aciertos que se produjo para cada tipo de protocolo.
HTTP Methods | Le permite obtener una impresión rápida de cuáles son los métodos HTTP que se están usando para solicitar los datos. Normalmente, los métodos de solicitud HTTP más comunes son GET, HEAD y POST. Un gráfico de anillos indica el porcentaje de aciertos que se produjo para cada tipo de método de solicitud HTTP.
URLs | Contiene un gráfico que muestra las 10 URL más solicitadas. Se muestra una barra para cada dirección URL. La altura de la barra indica cuántos aciertos generó la dirección URL en particular durante el período de tiempo que cubre el informe. Las estadísticas de las 100 direcciones URL más solicitadas se muestran directamente debajo de este gráfico.
CNAMEs | Contiene un gráfico que muestra los 10 CNAME más usados para solicitar recursos durante el periodo de tiempo que abarca un informe. Las estadísticas de los 100 CNAME más solicitados se muestran directamente debajo de este gráfico. 
Origins | Contiene un gráfico que muestra las 10 principales redes CDN o servidores de origen de cliente desde los que se solicitaron activos durante un período de tiempo especificado. Las estadísticas de las 100 redes CDN o servidores de origen de cliente más solicitados, se muestran directamente debajo de este gráfico. Los servidores de origen de cliente se identifican mediante el nombre definido en la opción de nombre de directorio.
Geo POPs | Muestra la cantidad del tráfico que se enruta a través de un determinado punto de presencia (POP). La abreviatura de tres letras representa un POP en nuestra red CDN.
Clientes | Contiene un gráfico que muestra los 10 principales clientes que solicitaron activos durante un período de tiempo especificado. Para los fines de este informe, todas las solicitudes que se originan en la misma dirección IP se consideran del mismo cliente. Las estadísticas de los 100 principales clientes se muestran directamente debajo de este gráfico. Este informe resulta útil para determinar los patrones de actividad de descarga para los clientes principales.
Estados de la memoria caché | Proporciona un desglose detallado del comportamiento de la memoria caché, que puede revelar los métodos para mejorar la experiencia global del usuario final. Puesto que el rendimiento más rápido procede de los aciertos de caché, para optimizar velocidades de entrega de datos puede minimizar los errores de caché y aciertos de caché expirados.
NONE Details | Contiene un gráfico que muestra las 10 principales URL para activos para los que la actualización de contenido de la caché no se comprobó durante un período de tiempo especificado. Las estadísticas de las 100 principales URL para este tipo de activos se muestran directamente debajo de este gráfico.
CONFIG\_NOCACHE Details | Contiene un gráfico que muestra las 10 principales direcciones URL para activos que no se almacenaron en caché debido a la configuración de la red CDN del cliente. Estos tipos de activos se sirvieron directamente desde el servidor de origen. Las estadísticas de las 100 principales URL para este tipo de activos se muestran directamente debajo de este gráfico.
UNCACHEABLE Details | Contiene un gráfico que muestra las 10 principales URL para activos que no pudieron almacenar debido a los datos de encabezado de la solicitud. Las estadísticas de las 100 principales URL para este tipo de activos se muestran directamente debajo de este gráfico.
TCP\_HIT Details | Contiene un gráfico que muestra las 10 principales URL para activos que se atendieron inmediatamente desde la caché. Las estadísticas de las 100 principales URL para este tipo de activos se muestran directamente debajo de este gráfico.
TCP\_MISS Details | Contiene un gráfico que muestra las 10 principales URL de activos que tienen un estado de memoria caché de TCP\_MISS. Las estadísticas de las 100 principales URL para este tipo de activos se muestran directamente debajo de este gráfico.
TCP\_EXPIRED\_HIT Details | Contiene un gráfico que muestra las 10 principales URL para activos obsoletos que se sirvieron directamente desde la caché. Las estadísticas de las 100 principales URL para este tipo de activos se muestran directamente debajo de este gráfico.
TCP\_EXPIRED\_MISS Details | Contiene un gráfico que muestra las 10 URL principales para los recursos obsoletos para que una nueva versión tenía que recuperarse desde el servidor de origen. Las estadísticas de las 100 principales URL para este tipo de activos se muestran directamente debajo de este gráfico.
TCP\_CLIENT\_REFRESH\_MISS Details | Contiene un gráfico de barras que muestra las direcciones URL de las 10 para activos se recuperaron de un servidor de origen debido a una solicitud no almacenar en caché del cliente. Las estadísticas de las 100 principales URL para este tipo de solicitudes se muestran directamente debajo de este gráfico.
Client Request Types | Indica el tipo de solicitudes que realizaron clientes HTTP (por ejemplo, exploradores) Este informe incluye un gráfico de anillos que proporciona una idea sobre cómo se controlan las solicitudes. Debajo del gráfico se muestra el ancho de banda y el tráfico de información para cada tipo de solicitud.
User Agent | Contiene un gráfico de barras que muestra los 10 principales agentes de usuario que solicitan contenido a través de nuestra red CDN. Normalmente, un agente de usuario es un explorador web, un reproductor multimedia o un explorador de teléfono móvil. Las estadísticas de los 100 principales agentes de usuario se muestran directamente debajo de este gráfico.
Referrers | Contiene un gráfico de barras que muestra los 10 principales orígenes de referencia del contenido al que se accedió a través de la red CDN. Normalmente, un origen de referencia es la dirección URL de la página web o el recurso que se vincula al contenido. Debajo del gráfico se proporciona información detallada para los 100 primeros orígenes de referencia.
Compression Types | Contiene un gráfico de anillos que divide los activos solicitados dependiendo de si fueron o no comprimidos por nuestros servidores perimetrales. El porcentaje de activos comprimidos se divide por el tipo de compresión usada. Debajo del gráfico se proporciona información detallada sobre cada estado y tipo de compresión.
File Types | Contiene un gráfico de barras que muestra los 10 principales tipos de archivo que se solicitaron a través de nuestra red CDN para su cuenta. Para los fines de este informe, se define un tipo de archivo por la extensión de nombre de archivo y el tipo de medio de Internet (por ejemplo, .html [texto/html], .htm [texto/html], .aspx [texto/html], etc.) del activo. Debajo del gráfico se proporciona información detallada para los 100 primeros tipos de archivo.
Unique Files | Contiene un gráfico que represente el número total de activos únicos que se solicitaron en un día determinado durante un período de tiempo especificado.
Token Auth Summary | Contiene un gráfico circular que proporciona una descripción general acerca de si los activos solicitados se protegieron con una autenticación basada en token. Los activos protegidos se muestran en el gráfico según los resultados de la autenticación que se intentó.
Token Auth Deny Details | Contiene un gráfico de barras que le permite ver las 10 principales solicitudes que se denegaron debido a la autenticación basada en token.
HTTP Response Codes | Proporciona un desglose de los códigos de estado HTTP (por ejemplo, 200 OK, 403 Prohibido, 404 No encontrado, etc.) que nuestros servidores perimetrales entregaron a los clientes HTTP. Un gráfico circular permite evaluar rápidamente cómo se sirvieron los activos. Debajo del gráfico se proporcionan datos estadísticos detallados para cada código de respuesta.
404 Errors | Contiene un gráfico de barras que le permite ver las 10 principales solicitudes que dieron como resultado un código de respuesta 404 No encontrado.
403 Errors | Contiene un gráfico de barras que le permite ver las 10 principales solicitudes que dieron como resultado un código de respuesta 403 Prohibido. Un código de respuesta 403 Prohibido se produce cuando una solicitud ha sido denegada por el servidor de origen de un cliente o un servidor perimetral en nuestro POP.
4xx Errors | Contiene un gráfico de barras que le permite ver las 10 principales solicitudes que dieron como resultado un código de respuesta de la gama 400. Los códigos de respuesta 403 Prohibido y 404 No encontrado están excluidos de este informe. Normalmente, un código de respuesta 4xx se produce cuando se deniega una solicitud debido a un error de cliente.
504 Errors | Contiene un gráfico de barras que le permite ver las 10 principales solicitudes que dieron como resultado un código de respuesta 504 Tiempo de espera agotado para la puerta de enlace. Un código de respuesta 504 Tiempo de espera agotado para la puerta de enlace aparece cuando se agota el tiempo de espera mientras un proxy HTTP está intentando comunicarse con otro servidor. En el caso de nuestra red CDN, un código de respuesta 504 Tiempo de espera agotado para la puerta de enlace se produce normalmente cuando un servidor perimetral no puede establecer comunicación con un servidor de origen del cliente.
502 Errors | Contiene un gráfico de barras que le permite ver las 10 principales solicitudes que dieron como resultado un código de respuesta 502 Puerta de enlace incorrecta. Un código de respuesta 502 Puerta de enlace incorrecta aparece cuando se produce un error de protocolo HTTP entre un servidor y un proxy HTTP. En el caso de nuestra red CDN, un código 502 Puerta de enlace incorrecta normalmente se produce cuando un servidor de origen del cliente devuelve una respuesta no válida a un servidor perimetral. Una respuesta no es válida si no se puede analizar o si está incompleta.
5xx Errors | Contiene un gráfico de barras que le permite ver las 10 principales solicitudes que dieron como resultado un código de respuesta de la gama 500. Los códigos de respuesta 502 Puerta de enlace incorrecta y 504 Tiempo de espera agotado para la puerta de enlace, están excluidos de este informe.

## Consulte también
* [Información general de la red CDN de Azure](cdn-overview.md)
* [Estadísticas en tiempo real en CDN de Microsoft Azure](cdn-real-time-stats.md)
* [Invalidación del comportamiento HTTP predeterminado mediante el motor de reglas](cdn-rules-engine.md)
* [Informes de HTTP avanzados](cdn-advanced-http-reports.md)

<!---HONumber=AcomDC_1203_2015-->