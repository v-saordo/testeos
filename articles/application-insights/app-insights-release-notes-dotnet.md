<properties 
	pageTitle="Notas de la versión de Application Insights para .NET" 
	description="Las actualizaciones más recientes de .NET SDK." 
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
	ms.date="01/12/2016" 
	ms.author="abaranch"/>
 
# Notas de la versión del SDK de Application Insights para .NET

El [SDK de Application Insights para .NET](app-insights-asp-net.md) envía telemetría acerca de la aplicación activa a [Application Insights]( https://azure.microsoft.com/services/application-insights/), donde puede analizar su uso y el rendimiento.


#### Para instalar el SDK en su aplicación

Consulte [Introducción a Application Insights para .NET](app-insights-asp-net.md).

#### Para actualizar al SDK más reciente 

* Después de actualizar, necesitará volver combinar todas las personalizaciones realizadas en ApplicationInsights.config. Si no está seguro de si lo ha personalizado, cree un nuevo proyecto, agregue Application Insights y compare el archivo .config con el que aparece en el nuevo proyecto. Tome nota de las diferencias.
* En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y seleccione **Administrar paquetes de NuGet**.
* Establezca el filtro para mostrar los paquetes instalados. 
* Seleccione **Microsoft.ApplicationInsights.Web** y elija **Actualizar**. (Esto actualizará todos los paquetes dependientes).
* Compare ApplicationInsights.config con la copia anterior. La mayoría de los cambios que verá se deben a que hemos quitado algunos módulos y hemos hecho que otros se puedan parametrizar. Restablezca las personalizaciones realizadas en el archivo anterior.
* Vuelva a generar la solución.

## Version 2.0.0-beta4

- Los métodos de extensión UseSampling y UseAdaptiveSampling se movieron a Microsoft.ApplicationInsights.Extensibility.
- Se ha quitado la compatibilidad con las aplicaciones de la Tienda y Windows Phone universal.
- Se ha actualizado ```DependencyTelemetry``` para tener nuevas propiedades ```ResultCode``` y ```Id```. ```ResultCode``` se usará para ofrecer código de respuesta HTTP para dependencias HTTP y código de error para dependencias SQL. ```Id``` se usará para la correlación entre componentes. 
- Si ```ServerTelemetryChannel``` se inicializa mediante programación ahora es necesario llamar al método ```ServerTelemetryChannel.Initialize()```. En caso contrario, no se inicializará el almacenamiento persistente (lo que significa que se quitará si no se puede enviar la telemetría en caso de problemas de conectividad temporal).
- ```ServerTelemetryChannel``` tiene la nueva propiedad ```StorageFolder``` que se puede establecer a través de código o mediante configuración. Si se establece esta propiedad, ApplicationInsights usa la ubicación ofrecida para telemetría que no se envió en el caso de problemas de conectividad temporal. Si no se establece la propiedad, o la carpeta ofrecida es inaccesible, ApplicationInsights intentará usar las carpetas LocalAppData o Temp como lo hizo anteriormente.
- Se quita el método de extensión ```TelemetryConfiguration.GetTelemetryProcessorChainBuilder```. En lugar de este método use el método de instancia ```TelemetryConfiguration.TelemetryProcessorChainBuilder```.
- La clase ```TelemetryConfiguration``` tiene una propiedad nueva ```TelemetryProcessors``` que ofrece acceso de solo lectura a la colección ```TelemetryProcessors```.
- ```Use```, ```UseSampling``` y ```UseAdaptiveSampling``` conserva ```TelemetryProcessors``` cargados de la configuración.
- Se ofrecen dos procesadores de telemetría de serie en el archivo de configuración: el procesador de telemetría de filtro de agente de usuario y el procesador de telemetría de controlador de solicitud. Su comportamiento se puede personalizar. Puede anexar una cadena de agente de usuario que quiera filtrar en el archivo AI.config. De manera predeterminada, se filtra la cadena de agente de usuario ```AllwaysOn```. El comportamiento actual compara las cadenas del archivo de configuración con la cadena de agente de usuario mediante una comparación completa que no distingue mayúsculas de minúsculas. También puede personalizar la lista de controladores para los que quiere que se filtren solicitudes. 
- La versión de NuGet de Microsoft.ApplicationInsights.Agent.Intercept dependiente se actualizó a 1.2.1. Tiene correcciones de errores de recopilación de dependencia SQL.

## Versión 2.0.0-beta3

- [Muestreo adaptable](app-insights-sampling.md) activado de forma predeterminada en el canal de telemetría de servidor. 
- Firma fija de ```UseSampling``` para permitir el encadenamiento con otras llamadas a ```Use``` de procesadores de telemetría.  
- Propiedad ```Request.ID``` devuelta. ```OperationContext``` tiene ahora una propiedad ```ParentId``` para la correlación integral.
- Se quita ```TimestampTelemetryInitializer```. Se agregará la marca de tiempo automáticamente por ```TelemetryClient```.
- ```OperationCorrelationTelemetryInitializer``` se agrega de forma predeterminada para habilitar la correlación de operaciones.
- Se usa ```OperationCorrelationTelemetryInitializer``` en lugar de ```OperationIdTelemetryInitializer```.
- No se recopilará el Agente de usuario de forma predeterminada. Se quitó el inicializador de telemetría del Agente de usuario.
- No se recopilará el campo ```DependencyTelemetry.Async``` por el módulo de telemetría del recopilador de dependencia. 
- Las solicitudes de diagnósticos y contenido estático no se recopilarán por módulo de telemetría de solicitud. Use ```HandlersToFilter``` de la colección ```RequestTrackingTelemetryModule``` para filtrar solicitudes generadas por determinados controladores http. 
- La telemetría de solicitudes generadas automáticamente es accesible a través del método de extensión HttpContext: System.Web.HttpContextExtension.GetRequestTelemetry  


## Versión 2.0.0-beta2
- Se ha agregado compatibilidad para ITelemetryProcessor y la posibilidad de configuración a través de código o de la propia configuración. [Habilita el filtrado personalizado en el SDK](app-insights-api-filtering-sampling/#filtering)
- Se han quitado los inicializadores de contexto. En su lugar, se usan [inicializadores de telemetría ]( https://azure.microsoft.com/documentation/articles/app-insights-api-filtering-sampling/#filtering).
- Application Insights para .Net Framework 4.6 actualizado. 
- Los nombres de evento personalizados ahora pueden tener hasta 512 caracteres.
- El nombre de la propiedad ```OperationContext.Name``` cambia a ```RootName```.
- Propiedad ```RequestTelemetry.Id``` quitada.
- Las propiedades ```Id``` y ```Context.Operation.Id``` de RequestTelemetry podrían no inicializarse al crear un nuevo RequestTelemetry.
- ```RequestTelemetry.Name``` ya no se inicializa. Se usará ```RequestTelemetry.Context.Operation.Name``` en su lugar.
- En la supervisión de la solicitud, el código de respuesta 401 forma parte del protocolo de enlace de autenticación normal y dará lugar a una solicitud correcta.
- Corrección del bloqueo de subproceso de IU al inicializar InMemoryChannel (canal predeterminado) desde el subproceso de IU. Esto corrige los problemas de bloqueo de la IU con aplicaciones WPF.
 
## Version 2.0.0-beta1
- TrackDependency producirá JSON válido cuando no se especificaron todos los campos obligatorios.
- La propiedad redundante ```RequestTelemetry.ID``` ahora solo es un proxy para ```RequestTelemetry.Operation.Id```.
- La nueva interfaz ```ISupportSampling``` y su implementación explícita por parte de la mayoría de los tipos de elementos de datos.
- Propiedad ```Count``` en DependencyTelemetry marcada como obsoleta. Se usa ```SamplingPercentage``` en su lugar.
- Se introdujo un nuevo ```CloudContext``` y las propiedades ```RoleName``` y ```RoleInstance``` se movieron a este desde ```DeviceContext```.
- Nueva propiedad ```AuthenticatedUserId``` en ```UserContext``` para especificar la identidad del usuario autenticada.
- Se agregaron `Microsoft.ApplicationInsights.Web.AccountIdTelemetryInitializer` y `Microsoft.ApplicationInsights.Web.AuthenticatedUserIdTelemetryInitializer` que inicializan el contexto del usuario autenticado según establece el SDK de Javascript.
- Se agregaron `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor` y la compatibilidad con muestreo de tipo fijo como implementación del mismo.
- Se ha agregado `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.TelemetryChannelBuilder`, que permite la creación de un `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` con un conjunto de `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ITelemetryProcessor`.

## Versión 1.2

- Los inicializadores de telemetría que no tienen dependencias en las bibliotecas de ASP.NET se movieron desde `Microsoft.ApplicationInsights.Web` al nuevo NuGet de dependencia `Microsoft.ApplicationInsights.WindowsServer`
- El nombre de `Microsoft.ApplicationInsights.Web.dll` cambió a `Microsoft.AI.Web.dll`.
- El nombre de NuGet de `Microsoft.ApplicationInsights.Web.TelemetryChannel` cambió a `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel`. El nombre del ensamblado `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` cambió a `Microsoft.AI.ServerTelemetryChannel.dll`. El nombre de la clase `Microsoft.ApplicationInsights.Extensibility.Web.TelemetryChannel` cambió a `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`.
- Todos los espacios de nombres que forman parte del SDK web se cambiaron para excluir la parte de `Extensibility`. Eso incluye todos los inicializadores de telemetría en ApplicationInsights.config y en el módulo `ApplicationInsightsWebTracking` en web.config.
- Las dependencias recopiladas mediante el uso del agente de instrumentación de tiempo de ejecución (habilitado a través del Monitor de estado o de la extensión Sitio web de Azure) no se marcarán como asincrónicas si no existe HttpContext.Current en el subproceso.
- La propiedad `SamplingRatio` de `DependencyTrackingTelemetryModule` no hace nada y se marcó como obsoleta.
- El nombre del ensamblado `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector` cambió a `Microsoft.AI.PerfCounterCollector`.
- Varias correcciones de errores menores en SDK web y de dispositivos


## Versión 1.1

- Se agregó un nuevo tipo de telemetría `DependencyTelemetry` que se puede usar para enviar información sobre las llamadas de dependencia desde una aplicación (como llamadas SQL, HTTP, etc.).
- Se agregó un nuevo método de sobrecarga `TelemetryClient.TrackDependency` que permite enviar información sobre las llamadas de dependencia.
- Se corrigió la NullReferenceException que producía el módulo de diagnósticos cuando se usaba TelemetryConfiguration.CreateDefault.

## Versión 1.0

- Se movieron los inicializadores y módulos de telemetría desde subespacios de nombres independientes al espacio de nombres `Microsoft.ApplicationInsights.Extensibility.Web` raíz.
- Se quitó el prefijo "Web" de los nombres de los inicializadores y módulos de telemetría porque ya está incluido en el nombre del espacio de nombres `Microsoft.ApplicationInsights.Extensibility.Web`.
- Se movió `DeviceContextInitializer` desde el ensamblado `Microsoft.ApplicationInsights` al ensamblado `Microsoft.ApplicationInsights.Extensibility.Web` y se convirtió en `ITelemetryInitializer`.
- Cambio de los nombres del espacio de nombres y del ensamblado de `Microsoft.ApplicationInsights.Extensibility.RuntimeTelemetry` a `Microsoft.ApplicationInsights.Extensibility.DependencyCollector` para mantener la coherencia con el nombre del paquete NuGet.
- Cambie el nombre `RemoteDependencyModule` a `DependencyTrackingTelemetryModule`.
- Cambie el nombre `CustomPerformanceCounterCollectionRequest` a `PerformanceCounterCollectionRequest`.

## Versión 0.17
- Quitada la dependencia a EventSource NuGet para las aplicaciones de Framework 4.5.
- Las cookies de sesión y usuario anónimo no se generarán en el lado del servidor. Para implementar el seguimiento de usuarios y sesiones para las aplicaciones web, es necesaria la instrumentación con el SDK de JS: las cookies del SDK de JavaScript se siguen respetando. Los módulos de telemetría ```WebSessionTrackingTelemetryModule``` y ```WebUserTrackingTelemetryModule``` ya no se admiten y se han quitado del archivo ApplicationInsights.config. Tenga en cuenta que este cambio puede provocar una rectificación significativa de los recuentos de usuario y sesión, ya que ahora solo se cuentan las sesiones originadas por el usuario.
- El SDK ya no rellena OSVersion de manera predeterminada. Cuando está vacío, la canalización de Application Insights calcula SO y OSVersion según el agente del usuario. 
- Se usa un canal de persistencia optimizado para los escenarios de carga alta para el SDK de web. Problema "Espiral de la muerte" solucionado. Espiral de muerte es una condición especial en el recuento de elementos de telemetría que supera la limitación en el extremo que conducirá a reintentar después de determinados tiempo y se limitarán durante el reintento de nuevo.
- El modo de programador está optimizado para la producción. Si se deja por error no provocará una sobrecarga tan grande como antes de intentar enviar información adicional.
- El modo de programador solo se habilitará de forma predeterminada cuando la aplicación está en el depurador. Puede invalidarlo mediante la propiedad ```DeveloperMode``` de la interfaz ```ITelemetryChannel```.

## Versión 0.16 

Vista previa del 28-04-2015

- El SDK es ahora compatible con la plataforma de destino DNX para permitir la supervisión de aplicaciones del [marco de trabajo principal .NET](http://www.dotnetfoundation.org/NETCore5).
- Las instancias de ```TelemetryClient``` ya no almacenan en caché la clave de instrumentación. Ahora, si no estableció la clave de instrumentación explícitamente en ```TelemetryClient```, ```InstrumentationKey``` devolverá null. Corrige un problema en el que cuando establece ```TelemetryConfiguration.Active.InstrumentationKey``` después de que ya se recopilaran algunos datos de telemetría, los módulos de telemetría, como el recopilador de dependencias, la recopilación de datos de solicitudes web y el recolector de contadores de rendimiento usarán la nueva clave de instrumentación.

## Version 0.15

- Nueva propiedad ```Operation.SyntheticSource``` ahora disponible en ```TelemetryContext```. Ahora puede marcar los elementos de telemetría como "no es un tráfico de usuario real" y especificar cómo se generó este tráfico. Por ejemplo, si define esta propiedad, puede distinguir el tráfico de la automatización de pruebas del tráfico de prueba de carga.
- La lógica de canal se movió al NuGet independiente denominado Microsoft.ApplicationInsights.PersistenceChannel. El canal predeterminado se denomina ahora InMemoryChannel.
- El nuevo método ```TelemetryClient.Flush``` permite vaciar elementos de telemetría del búfer de forma sincrónica.

## Versión 0.13

No existen notas de la versión para versiones anteriores.

 

<!---HONumber=AcomDC_0128_2016-->