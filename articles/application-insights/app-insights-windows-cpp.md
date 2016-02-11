<properties pageTitle="Application Insights for C++ apps" description="Analyze usage and performance of your C++ app with Application Insights." services="application-insights" documentationCenter="cpp" authors="crystk" manager="douge""/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="universal" 
    ms.devlang="na" 
    ms.topic="article" 
	ms.date="11/17/2015" 
    ms.author="crystk"/>

# Application Insights para aplicaciones C++

Application Insights de Visual Studio le permite supervisar su aplicación móvil para obtener información relativa al uso, los eventos y los bloqueos.

## Requisitos

Necesitará:

* Una suscripción a [Microsoft Azure](http://azure.com). Inicie sesión con una cuenta Microsoft, que podría tener para Windows, XBox Live u otros servicios en la nube de Microsoft.
* Visual Studio 2015 o posterior.
* Aplicación universal de Windows 10

## Creación de recursos en Application Insights

En el [portal de Azure][portal], cree un nuevo recurso de Application Insights. Seleccione la opción Windows Phone o Tienda Windows.

![Haga clic en Nuevo, Servicios para desarrolladores, Application Insights.](./media/app-insights-windows-cpp/01-universal.png)

En la hoja que se abre podrá ver los datos de uso y rendimiento sobre la aplicación. Para volver a ella la próxima vez que inicie sesión en Azure, encontrará un icono correspondiente en la pantalla de inicio. También puede hacer clic en Examinar para buscarla.

####  Realice una copia de la clave de instrumentación.

La clave identifica al recurso y se instalará pronto en el SDK para dirigir los datos al recurso.

![Haga clic en Propiedades, seleccione la clave y presione ctrl + C.](./media/app-insights-windows-cpp/02-props-asp.png)

## <a name="sdk"></a> Instale el SDK en su aplicación.


1. En Visual Studio, edite los paquetes de NuGet de su proyecto de aplicación de escritorio.

    ![Haga clic con el botón secundario en el proyecto y seleccione Administrar paquetes de Nuget.](./media/app-insights-windows-cpp/03-nuget.png)

2. Instale el SDK de Application Insights para aplicaciones C++.

    ![Active **Incluir versión preliminar** y busque "Application Insights".](./media/app-insights-windows-cpp/04-nuget.png)

3. En la configuración de lanzamiento y depuración de los proyectos:
  - Agregue $(SolutionDir)packages\\ApplicationInsights-CPP.1.0.0-Beta\\src\\inc a las propiedades del proyecto -> Directorios VC++ -> Directorios de archivos de inclusión.
  - Agregue $(SolutionDir)packages\\ApplicationInsights.1.0.0-Beta\\lib\\native<TIPO DE PLATAFORMA>\\release\\AppInsights\_Win10-UAP a las propiedades del proyecto -> Directorios VC++ -> Directorios de archivos de bibliotecas.

4. Agregue ApplicationInsights.winmd como referencia al proyecto desde $(SolutionDir)packages\\ApplicationInsights.1.0.0-Beta\\lib\\native<TIPO DE PLATAFORMA>\\release\\ApplicationInsights.
5. Agregue AppInsights\_Win10-UAP.dll desde $(SolutionDir)packages\\ApplicationInsights.1.0.0-Beta\\lib\\native<TIPO DE PLATAFORMA>\\release\\AppInsights\_Win10-UAP. Vaya a las propiedades y establezca el contenido en SÍ. Al hacerlo, el archivo dll se copiará en el directorio de compilación.


#### Para actualizar el SDK para versiones futuras

Cuando se lanza un nuevo [SDK](app-insights-release-notes-windows-cpp.md):

* En el Administrador de paquetes de NuGet, seleccione el SDK instalado y elija la acción de actualización.
* Repita los pasos de instalación, usando el nuevo número de versión.

## Uso del SDK

Inicialice el SDK y empiece a realizar un seguimiento de la telemetría.

1. En App.xaml.h: 
  - Agregue: `ApplicationInsights::CX::SessionTracking^ m_session;`
2. En App.xaml.cpp:
  - Agregue: `using namespace ApplicationInsights::CX;`

  - En App:App()
	
     `// this will do automatic session tracking and automatic page view collection` `m_session = ref new ApplicationInsights::CX::SessionTracking();`

  - Una vez que haya creado el marco raíz (normalmente al final de App::OnLaunched), inicialice m\_session:
	
    ```
    String^ iKey = L"<YOUR INSTRUMENTATION KEY>";
    m_session->Initialize(this, rootFrame, iKey);
	```

3. Para usar el seguimiento en cualquier otro lugar de la aplicación, puede declarar una instancia del cliente de telemetría.


```

    using namespace ApplicationInsights::CX;
    TelemetryClient^ tc = ref new TelemetryClient(L"<YOUR INSTRUMENTATION KEY>");
	tc->TrackTrace(L"This is my first trace");
    tc->TrackEvent(L"I'M ON PAGE 1");
    tc->TrackMetric(L"Test Metric", 5.03);
```


## <a name="run"></a> Ejecución del proyecto

Ejecute la aplicación para generar telemetría. Puede ejecutarla en modo de depuración en la máquina de desarrollo, o bien publicarla y dejar que los usuarios la ejecuten.

## Visualización de los datos en Application Insights

Vuelva a http://portal.azure.com y busque el recurso de Application Insights.

Haga clic en Buscar para abrir [Búsqueda de diagnóstico][diagnostic], que es donde aparecerán los primeros eventos. Si no ve nada, espere un minuto o dos y haga clic en Actualizar.

![Haga clic en Búsqueda de diagnóstico](./media/app-insights-windows-cpp/21-search.png)

A medida que use la aplicación, los datos aparecerán en la hoja de información general.

![Hoja de información general](./media/app-insights-windows-cpp/22-oview.png)

Haga clic en cualquier gráfico para obtener más detalles. Por ejemplo, bloqueos:

![Haga clic en el gráfico de bloqueos](./media/app-insights-windows-cpp/23-crashes.png)


## <a name="usage"></a>Pasos siguientes

[Seguimiento del uso de la aplicación][track]

[Usar la API para enviar eventos y métricas personalizados][api]

[Búsqueda de diagnóstico][diagnostic]

[Explorador de métricas][metrics]

[Solución de problemas][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-api-custom-events-metrics.md

 

<!---HONumber=AcomDC_1125_2015-->