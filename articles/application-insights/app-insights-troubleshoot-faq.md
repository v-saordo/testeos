<properties 
	pageTitle="Solución de problemas y preguntas sobre Application Insights" 
	description="¿Hay algo que no entienda o que no funcione en Visual Studio Application Insights? Pruebe aquí." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/26/2016" 
	ms.author="awills"/>
 
# Preguntas: Application Insights para ASP.NET

## Problemas de configuración

*Tengo problemas con la configuración de:*

* [Aplicación .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Supervisión de una aplicación que ya se está ejecutando](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Diagnóstico de Azure](app-insights-azure-diagnostics.md)
* [Aplicaciones web de Java](app-insights-java-troubleshoot.md)
* [Otras plataformas](app-insights-platforms.md)


## ¿Se puede usar Application Insights con...?

[Ver plataformas][platforms]


## ¿Es gratis?

* Sí, si elige el [nivel de precios](app-insights-pricing.md) gratuito. Podrá disfrutar de la mayoría de las características y de una amplia cuota de datos. 
* Debe proporcionar los datos de su tarjeta de crédito para registrarse con Microsoft Azure, pero no se realizará ningún cargo a menos que use otro servicio de Azure que sea de pago o que decida actualizar explícitamente a un nivel de pago.
* Si su aplicación envía una cantidad de datos superior a la cuota mensual establecida para el nivel gratuito, la aplicación dejará de estar registrada. Si esto sucede, puede optar por comenzar a pagar o esperar hasta que la cuota se restablezca al final del mes.
* Los datos básicos de uso y de sesión no están sujetos a ninguna cuota.
* También hay una prueba gratuita de 30 días que le permitirá disfrutar de las características Premium sin tener que pagar.
* Cada recurso de la aplicación tiene una cuota independiente. El nivel de precios se establece con independencia de cualquier otro recurso.

#### ¿Qué obtengo si opto por la versión de pago?

* Una mayor [cuota mensual de datos](https://azure.microsoft.com/pricing/details/application-insights/).
* En caso de que se supere la cuota mensual, tendrá la opción de pagar para seguir recopilando datos. Si los datos superan la cuota, se le cobrará por Mb.
* [Exportación continua](app-insights-export-telemetry.md)


## <a name="q14"></a>¿Qué modifica Application Insights en mi proyecto?

Los detalles dependen del tipo de proyecto. Para una aplicación web:


+ Agregue estos archivos al proyecto:

 + ApplicationInsights.config.
 + ai.js


+ Instale estos paquetes de NuGet:

 -  *API de Application Insights*: la API central.

 -  *API de Application Insights para aplicaciones web*: se usa para enviar la telemetría del servidor.

 -  *API de Application Insights para aplicaciones JavaScript*: se usa para enviar la telemetría del cliente.

    El paquete incluye estos ensamblados:

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Inserta elementos en:

 - Web.config

 - packages.config

+ (Solo nuevos proyectos: si [agrega Application Insights a un proyecto existente][start], tiene que hacerlo manualmente). Inserte fragmentos de código en el código de cliente y servidor para inicializarlos con el identificador de recursos de Application Insights. Por ejemplo, en una aplicación MVC, el código se inserta en la página maestra Views/Shared/\_Layout.cshtml.


## ¿Cómo se puede actualizar desde versiones anteriores de SDK?

Consulte las [notas de la versión](app-insights-release-notes.md) del SDK adecuado para el tipo de aplicación.



## <a name="update"></a>¿Cómo puedo cambiar el recurso de Azure al que mi proyecto envía datos?

En el Explorador de soluciones, haga clic con el botón derecho en `ApplicationInsights.config` y elija **Actualizar Application Insights**. Puede enviar los datos a un recurso nuevo o existente en Azure. El Asistente para actualización cambia la clave de instrumentación en ApplicationInsights.config, que determina dónde el SDK del servidor envía los datos. A menos que desactive la opción "Actualizar todo", también cambiará la clave donde aparece en las páginas web.


## <a name="q06"></a>En la pantalla principal de Microsoft Azure en vista previa, ¿muestra ese mapa el estado de mi aplicación?

¡No! Muestra el estado del servicio Azure. Para ver los resultados de la prueba web, elija Examinar > Application Insights > (su aplicación) y, a continuación, consulte los resultados de la prueba web.



#### <a name="data"></a>¿Cuánto tiempo se retienen los datos en el portal? ¿Es seguro?

Eche un vistazo a [Privacidad y retención de los datos][data].

## Registro

#### <a name="post"></a>¿Cómo puedo ver datos POST en Búsqueda de diagnóstico?

Los datos POST no se registran automáticamente, pero se puede usar una llamada TrackTrace: incluya los datos en el parámetro de mensaje. Este tiene un límite de tamaño superior al de las propiedades de cadena, aunque no se puede filtrar por él.

## Seguridad

#### ¿Están mis datos seguros en el portal? ¿Cuánto tiempo se conservan?

Consulte [Privacidad y retención de los datos][data].


## <a name="q17"></a> ¿He habilitado todo en Application Insights?

<table border="1">
<tr><th>Qué debería ver</th><th>Cómo conseguirlo</th><th>Razones para quererlo</th></tr>
<tr><td>Gráficos de disponibilidad</td><td><a href="../app-insights-monitor-web-app-availability/">Pruebas web</a></td><td>Saber que la aplicación web funciona</td></tr>
<tr><td>Rendimiento de la aplicación de servidor: tiempos de respuesta, etc.
</td><td><a href="../app-insights-asp-net/">Agregar Application Insights a un proyecto</a><br/>o <br/><a href="../app-insights-monitor-performance-live-website-now/">Instalar el monitor de estado de Application Insights en el servidor</a> (o escribir su código propio para <a href="../app-insights-api-custom-events-metrics/#track-dependency">hacer un seguimiento de las dependencias</a>)</td><td>Detectar problemas de rendimiento</td></tr>
<tr><td>Telemetría de dependencia</td><td><a href="../app-insights-monitor-performance-live-website-now/">Instalar el monitor de estado de Application Insights en el servidor</a></td><td>Diagnosticar problemas con las bases de datos u otros componentes externos</td></tr>
<tr><td>Obtener seguimientos de pila de las excepciones</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Insertar llamadas TrackException en el código</a> (aunque algunas se notifican automáticamente)</td><td>Detectar y diagnosticar excepciones</td></tr>
<tr><td>Buscar seguimientos del registro</td><td><a href="../app-insights-search-diagnostic-logs/">Agregar un adaptador de registro</a></td><td>Diagnosticar excepciones, problemas de rendimiento</td></tr>
<tr><td>Aspectos básicos del uso de cliente: vistas de página, sesiones,...</td><td><a href="../app-insights-javascript/">Inicializador de JavaScript en páginas web</a></td><td>Análisis de uso</td></tr>
<tr><td>Métricas personalizadas de cliente</td><td><a href="../app-insights-api-custom-events-metrics/">Seguimiento de llamadas en páginas web</a></td><td>Mejorar la experiencia del usuario</td></tr>
<tr><td>Métricas personalizadas de servidor</td><td><a href="../app-insights-api-custom-events-metrics/">Seguimiento de llamadas en el código de servidor</a></td><td>Business intelligence</td></tr>
</table>

Si el servicio web se ejecuta en una máquina virtual de Azure, también puede [obtener diagnósticos][azurediagnostic] aquí.

## Automatización

Puede [escribir scripts de PowerShell](app-insights-powershell.md) para crear y actualizar los recursos de Application Insights.

## Más respuestas

* [Foro de Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/es-ES/home?forum=ApplicationInsights)


<!--Link references-->

[azurediagnostic]: ../insights-how-to-use-diagnostics.md
[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0302_2016-->