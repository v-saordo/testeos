<properties
	pageTitle="CDN - Estadísticas de tiempo real"
	description="Estadísticas en tiempo real en la CDN de Microsoft Azure Estadísticas en tiempo real proporciona datos en tiempo real sobre el rendimiento de la red CDN al entregar contenido a los clientes."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>

# Estadísticas en tiempo real en la CDN de Microsoft Azure

## Información general

En este documento se describe la funcionalidad de Estadísticas en tiempo real en la CDN de Microsoft Azure. Esta funcionalidad proporciona datos en tiempo real sobre el rendimiento de la red CDN al entregar contenido a los clientes.

> [AZURE.NOTE] Estadísticas en tiempo real es una característica del nivel Premium de la CDN. Para una comparación de las características de los niveles Estándar y Premium de la CDN, vea [Información general de la red de entrega de contenido (CDN) de Azure](cdn-overview.md).

Los gráficos siguientes están disponibles al ver estadísticas en tiempo real para las plataformas basadas en HTTP:

* [Ancho de banda](#bandwidth)
* [Códigos de estado](#status-codes)
* [Estados de la memoria caché](#cache-statuses)
* [Conexiones](#connections)

> [AZURE.NOTE] Cada uno de los gráficos anteriores muestra estadísticas en tiempo real para un determinado período de tiempo. Se abre una ventana deslizante de datos una vez transcurrido el tiempo especificado. Esto significa que los datos antiguos se quitarán del gráfico para dejar espacio a los nuevos datos. Se puede establecer el período de tiempo para esta ventana deslizante con la opción Intervalo de tiempo de los gráficos.

## Acceso a estadísticas en tiempo real

1. En la hoja Perfil de CDN, haga clic en el botón **Administrar**.

	![Botón Administrar en la hoja Perfil de CDN](./media/cdn-real-time-stats/cdn-manage-btn.png)

	Se abre el Portal de administración de CDN.

2. Mantenga el cursor sobre la pestaña **Análisis** y después sobre el control flotante **Estadísticas en tiempo real**. Haga clic en **Plataforma grande HTTP**.

	Se muestran las opciones de informe.

## Ancho de banda

El gráfico de ancho de banda muestra la cantidad de ancho de banda que se usa en la plataforma actual durante un período de tiempo especificado. La parte sombreada del gráfico indica el uso de ancho de banda. La cantidad exacta del ancho de banda que se usa actualmente se muestra directamente debajo del gráfico de líneas.

> [AZURE.NOTE] Las unidades empleadas para informar del uso ancho de banda son una de las siguientes: bits por segundo (b/s), Kilobits por segundo (Kbps), Megabits por segundo (Mb/s) o Gigabits por segundo (Gb/s).

## Códigos de estado

El gráfico de códigos de estado se compone de líneas codificadas por color que indican la frecuencia con que se producen códigos de respuesta HTTP durante un período de tiempo especificado. El lado izquierdo del gráfico (eje y) indica la frecuencia con que se devuelve un código de estado para las solicitudes, mientras que la parte inferior del gráfico (eje x) indica la progresión del tiempo.

Una lista de códigos de estado se muestra directamente sobre el gráfico. En esta lista se indica cada código de estado que se puede incluir en el gráfico de líneas y el número actual de repeticiones por segundo para ese código de estado. De forma predeterminada, se muestra una línea para cada uno de estos códigos de estado del gráfico. Pero se puede optar por supervisar solo los códigos de estado que tienen un significado especial para la configuración de la red CDN. Para ello, marque solo las opciones de código de estado que quiera y desactive las demás opciones. Cuando esté satisfecho con los códigos de estado que se mostrarán en el gráfico, debe hacer clic en Actualizar gráfico. Con esto se evita incluir los códigos de estado desactivados en el gráfico.

> [AZURE.NOTE] La opción **Actualizar gráfico** borrará el gráfico. Después, solo se mostrarán los códigos de estado seleccionados.

A continuación se describe cada opción de código de estado.

Nombre | Descripción
-----|------------
Total de aciertos por segundo | Determina si en el gráfico se muestra el número total de solicitudes por segundo para la plataforma actual. Puede usar esta opción como indicador de línea de base para ver el porcentaje del total de aciertos del que consta un código de estado específico.
2xx por segundo | Determina si en el gráfico se muestra el número total de códigos de estado 2xx (por ejemplo, 200, 201, 202, etc.) por segundo para la plataforma actual. Este tipo de código de estado indica que la solicitud se entregó correctamente al cliente.
304 por segundo | Determina si en el gráfico se muestra el número total de códigos de estado 304 por segundo para la plataforma actual. Este código de estado indica que el recurso solicitado no se modificó desde que el cliente HTTP lo recuperó por última vez.
3xx por segundo | Determina si en el gráfico se muestra el número total de códigos de estado 3xx (por ejemplo, 300, 301, 302, etc.) por segundo para la plataforma actual. Esta opción excluye las repeticiones de códigos de estado 304. Este tipo de código de estado indica que la solicitud tuvo como resultado un redireccionamiento.
403 por segundo | Determina si en el gráfico se muestra el número total de códigos de estado 403 por segundo para la plataforma actual. Este código de estado indica que la solicitud se consideró no autorizada. Una posible causa de este código de estado se produce cuando un usuario no autorizado solicita un recurso protegido mediante la autenticación basada en tokens.
404 por segundo | Determina si en el gráfico se muestra el número total de códigos de estado 404 por segundo para la plataforma actual. Este código de estado indica que no se encontró el recurso solicitado.
4xx por segundo | Determina si en el gráfico se muestra el número total de códigos de estado 4xx (por ejemplo, 400, 401, 402, 405, etc.) por segundo para la plataforma actual. Esta opción excluye las repeticiones de códigos de estado 403 y 404. Este código de estado indica que el recurso solicitado no se entregó al cliente.
5xx por segundo | Determina si en el gráfico se muestra el número total de códigos de estado 5xx (por ejemplo, 500, 501, 502, etc.) por segundo para la plataforma actual.
Otros por segundo | Determina si en el gráfico se muestra el número total de repeticiones de los demás códigos de estado.

También puede ocultar temporalmente los datos registrados para un código de estado en particular. Puede hacerlo desde el área que queda directamente debajo del gráfico desactivando la opción de código de estado que quiera. El código de estado seleccionado se ocultará inmediatamente en el gráfico. Al marcar esa opción de código de estado volverá a mostrarse esa opción.

> [AZURE.NOTE] Las opciones codificadas por colores que se muestran directamente debajo del gráfico solo afectan a lo que aparece en el gráfico. No afecta a si el gráfico realizará un seguimiento de ese código de estado.

## Estados de la memoria caché

El gráfico de estados de la memoria caché se compone de líneas codificadas por color que indican la frecuencia con que se producen determinados tipos de estados de la memoria caché durante un período de tiempo especificado. El lado izquierdo del gráfico (eje y) indica la frecuencia con que se devuelve un estado de la memoria caché para las solicitudes, mientras que la parte inferior del gráfico (eje x) indica la progresión del tiempo.

Una lista de estados de la memoria caché se muestra directamente sobre el gráfico. En esta lista se indica cada estado de la memoria caché que se puede incluir en el gráfico de líneas y el número actual de repeticiones por segundo para dicho estado. De forma predeterminada, se muestra una línea para cada uno de estos estados de la memoria caché del gráfico. Pero se puede optar por supervisar solo los estados de la memoria caché que tienen un significado especial para la configuración de la red CDN. Para ello, marque solo las opciones de estado de la memoria caché que quiera y desactive las demás opciones. Cuando esté satisfecho con los estados de la memoria caché que se mostrarán en el gráfico, debe hacer clic en Actualizar gráfico. Con esto se evita incluir los códigos de estado desactivados en el gráfico.

> [AZURE.NOTE] La opción **Actualizar gráfico** borrará el gráfico. Después, solo se mostrarán los estados de la memoria caché seleccionados.

También puede ocultar temporalmente los datos registrados para un código de respuesta en particular. Puede hacerlo desde el área que queda directamente debajo del gráfico desactivando la opción de código de respuesta que quiera. El código de respuesta seleccionado se ocultará inmediatamente en el gráfico. Al marcar esa opción de código de respuesta volverá a mostrarse esa opción.

> [AZURE.NOTE] Las opciones codificadas por colores que se muestran directamente debajo del gráfico solo afectan a lo que aparece en el gráfico. No afecta a si el gráfico realizará un seguimiento de ese código de estado.

## Conexiones

Esta representación gráfica del promedio de conexiones por minuto permite ver el número de conexiones establecidas en los servidores. Una conexión consta de cada solicitud de un recurso que pasa por la red CDN.

## Consulte también
* [Información general de la red CDN de Azure](cdn-overview.md)
* [Invalidación del comportamiento HTTP predeterminado mediante el motor de reglas](cdn-rules-engine.md)
* [Informes de HTTP avanzados](cdn-advanced-http-reports.md)
* [Análisis del rendimiento perimetral](cdn-edge-performance.md)

<!---HONumber=AcomDC_0302_2016-->