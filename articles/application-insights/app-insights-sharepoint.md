<properties 
	pageTitle="Supervisión de un sitio de SharePoint con Application Insights" 
	description="Inicio de la supervisión de una nueva aplicación con una nueva clave de instrumentación" 
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
	ms.date="11/06/2015" 
	ms.author="awills"/>

# Supervisión de un sitio de SharePoint con Application Insights


[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Visual Studio Application Insights le permite supervisar la disponibilidad, el rendimiento y el uso de sus aplicaciones. Aquí aprenderá a configurarlo para un sitio de SharePoint.


## Creación de recursos en Application Insights


En el [portal de Azure](https://portal.azure.com), cree un nuevo recurso de Application Insights. Elija ASP.NET como el tipo de aplicación.

![Haga clic en Propiedades, seleccione la clave y presione ctrl + C.](./media/app-insights-sharepoint/01-new.png)


En la hoja que se abre podrá ver los datos de uso y rendimiento sobre la aplicación. Para volver a ella la próxima vez que inicie sesión en Azure, encontrará un icono correspondiente en la pantalla de inicio. También puede hacer clic en Examinar para buscarla.
    


## Agregar nuestro script a las páginas web

En Inicio rápido, obtenga el script para páginas web:

![](./media/app-insights-sharepoint/02-monitor-web-page.png)

Inserte el script justo antes de la etiqueta &lt;/head&gt; de cada página de la que quiera realizar el seguimiento. Si su sitio web tiene una página maestra, puede colocar el script allí. Por ejemplo, en un proyecto de ASP.NET MVC, lo colocaría en View\\Shared\\_Layout.cshtml

El script contiene la clave de instrumentación que dirige los datos de telemetría al recurso de Application Insights.

### Agregar el código a las páginas del sitio

#### En la página maestra

Si se puede editar la página maestra del sitio, dispondrá de supervisión para cada página del sitio.

Desproteja y edite la página maestra mediante SharePoint Designer o cualquier otro editor.

![](./media/app-insights-sharepoint/03-master.png)


Agregue el código justo antes de la etiqueta </head>.


![](./media/app-insights-sharepoint/04-code.png)

#### O, en páginas individuales

Para supervisar un conjunto limitado de páginas, agregue el script por separado a cada página.

Inserte un elemento web e incruste el fragmento de código en él.


![](./media/app-insights-sharepoint/05-page.png)


## Visualización de los datos de la aplicación

Vuelva a implementar la aplicación.

Vuelva a la hoja de la aplicación en el [Portal de Azure](https://portal.azure.com).

Los primeros eventos aparecerán en Búsqueda.

![](./media/app-insights-sharepoint/09-search.png)

Si espera más datos, haga clic en Actualizar después de unos segundos.

En la hoja de introducción, haga clic en **Análisis de uso** para ver a los gráficos de los usuarios, sesiones y vistas de páginas:

![](./media/app-insights-sharepoint/06-usage.png)

Haga clic en cualquier gráfico para ver más detalles: por ejemplo, Vistas de página:

![](./media/app-insights-sharepoint/07-pages.png)

O Usuarios:


![](./media/app-insights-sharepoint/08-users.png)



## Pasos siguientes

* [Pruebas web](app-insights-monitor-web-app-availability.md) para supervisar la disponibilidad de su sitio.

* [Application Insights](app-insights-overview.md) para otros tipos de aplicación.



<!--Link references-->

<!---HONumber=AcomDC_0128_2016-->