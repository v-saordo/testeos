<properties
    pageTitle="Anotaciones de la versión de Application Insights | Microsoft Azure"
    description="Agregue marcadores de implementación o compilación a sus gráficos del Explorador de métricas en Application Insights."
    services="application-insights"
    documentationCenter=".net"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
	ms.date="02/22/2016"
    ms.author="awills"/>

# Anotaciones de la versión de Application Insights

Las anotaciones de la versión del gráfico [Explorador de métricas](app-insights-metrics-explorer.md) muestran donde se ha implementado una nueva compilación. Permiten ver fácilmente si los cambios tuvieron algún efecto en el rendimiento de la aplicación. Se pueden crear automáticamente mediante el [sistema de compilación de Visual Studio Team Services](https://www.visualstudio.com/es-ES/get-started/build/build-your-app-vs).

![Ejemplo de anotaciones con correlación visible con el tiempo de respuesta del servidor](./media/app-insights-annotations/00.png)

Las anotaciones de versión son una característica del servicio de versiones y compilaciones basado en la nube de Visual Studio Team Services.

## Instalación de la extensión Anotaciones (una vez)

Para poder crear anotaciones de versión, debe instalar una de las muchas extensiones de Team Service disponibles en Visual Studio Marketplace.

1. Inicie sesión en su proyecto de [Visual Studio Team Services](https://www.visualstudio.com/es-ES/get-started/setup/sign-up-for-visual-studio-online).
2. En Visual Studio Marketplace, [obtenga la extensión de anotaciones de la versión](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) y agréguela a su cuenta de Team Services.

![En la parte superior derecha de página web de Team Services, abra Marketplace. Seleccione Visual Studio Team Services y después, en Generar y versión, elija Ver más.](./media/app-insights-annotations/10.png)

Esto solo lo tiene que hacer una vez en su cuenta de Visual Studio Team Services. Las anotaciones de versión se pueden configurar ahora para cualquier proyecto de su cuenta.

## Obtener una clave de API de Application Insights

Deberá hacer esto en cada plantilla de versión para la que desee crear anotaciones de versión.


1. Inicie sesión en el [Portal de Microsoft Azure](https://portal.azure.com) y abra el recurso de Application Insights que supervisa su aplicación. (O bien, [cree uno ahora](app-insights-overview.md), si aún no lo ha hecho).
2. Abra **Configuración**, **Acceso de API** y realice una copia de **Id. de la aplicación**.

    ![En portal.azure.com, abra el recurso de Application Insights y elija Configuración. Abra el Acceso a la API. Copie el identificador de aplicación.](./media/app-insights-annotations/20.png)

2. En una ventana de explorador independiente, abra (o cree) la plantilla de versión que administra sus implementaciones de Visual Studio Team Services.

    Agregue una tarea y seleccione la tarea de anotación de versión de Application Insights en el menú.

    Pegue el **Id. de la aplicación** que copió desde la hoja Acceso de API.

    ![En Visual Studio Team Services, abra Versión, seleccione una definición de versión y elija Editar. Haga clic en Agregar tarea y seleccione Anotación de versión de Application Insights. Pegue el Id. de Application Insights.](./media/app-insights-annotations/30.png)

3. Establezca el campo **APIKey** en una variable `$(ApiKey)`.

4. De nuevo en la hoja Acceso a la API, cree una nueva clave de API y realice una copia de ella.

    ![En la hoja Acceso a la API de la ventana de Azure, haga clic en Crear clave de API. Proporcione un comentario, compruebe las anotaciones de escritura y haga clic en Generar clave. Copie la clave nueva.](./media/app-insights-annotations/40.png)

4. Abra la pestaña Configuración de la plantilla de la versión.

    Cree una definición de variable para `ApiKey`.

    Pegue su clave de API a la definición de la variable de ApiKey.

    ![En la ventana de Team Services, seleccione la pestaña Configuración y haga clic en Agregar variable. Establezca el nombre en ApiKey y en el Valor, pegue la clave que acaba de generar.](./media/app-insights-annotations/50.png)


5. Por último, haga clic en **Guardar** para guardar la definición de versión.

## Anotaciones de la versión

Ahora, cuando se utilice la plantilla de versión para implementar una nueva versión, se enviará una anotación a Application Insights. Las anotaciones se mostrarán en los gráficos del Explorador de métricas.

Haga clic en cualquier marcador de anotación para abrir los detalles de la versión, incluidos el solicitante, la bifurcación de control de código fuente, la definición de la versión, el entorno y mucho más.


![Haga clic en cualquier marcador de anotación de la versión.](./media/app-insights-annotations/60.png)

<!---HONumber=AcomDC_0224_2016-->