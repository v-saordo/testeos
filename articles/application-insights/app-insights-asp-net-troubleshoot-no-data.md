<properties 
	pageTitle="Solución de problemas cuando no hay datos: Application Insights para .NET" 
	description="¿No se ven datos en Application Insights de Visual Studio Pruebe aquí." 
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
	ms.date="01/29/2016" 
	ms.author="awills"/>
 
# Solución de problemas cuando no hay datos: Application Insights para .NET


## Problemas del monitor de estado

*Se [instala el monitor de estado](app-insights-monitor-performance-live-website-now.md) en el servidor web para supervisar las aplicaciones existentes. No se ve ningún resultado.*

Consulte [Solución de problemas del Monitor de estado](app-insights-monitor-performance-live-website-now.md#troubleshooting). Los puertos del firewall son el problema más común.


## <a name="q01"></a>No hay ninguna opción 'Agregar Application Insights' en Visual Studio

*Al crear un nuevo proyecto en Visual Studio, o al hacer clic con el botón derecho en un proyecto existente en el Explorador de soluciones, no se ve ninguna opción de Application Insights.*

+ No todos los tipos de proyectos .NET son compatibles con las herramientas. Se admiten los proyectos WCF y Web. Para otros tipos de proyecto como aplicaciones de escritorio o de servicio, puede [agregar manualmente un SDK de Application Insights al proyecto](app-insights-windows-desktop.md).
+ Asegúrese de que tiene [Visual Studio 2013 Update 3 o posterior](http://go.microsoft.com/fwlink/?LinkId=397827). Viene preinstalado con las Herramientas de Application Insights.
+ Seleccione **Herramientas**, **Extensiones y actualizaciones** y compruebe que **Herramientas de Application Insights** está instalado y habilitado. Si es así, haga clic en **Actualizaciones** para ver si hay alguna disponible.
+ Abra el cuadro de diálogo Nuevo proyecto y elija Aplicación web ASP.NET. Si ve la opción Application Insights aquí, es que las herramientas están instaladas. Si no es así, intente desinstalar y volver a instalar las herramientas de Application Insights.


## <a name="q02"></a>Error al agregar Application Insights

*Al crear un nuevo proyecto web o al intentar agregar Application Insights a un proyecto existente, aparece un mensaje de error.*

Causas probables:

* se produce un error de comunicación con el portal de Application Insights,
* hay algún problema con su cuenta de Azure,
* solo tiene [acceso de lectura a la suscripción o el grupo en que ha tratado de crear el recurso nuevo](app-insights-resources-roles-access-control.md).

Solución:

+ Compruebe que ha proporcionado las credenciales de inicio de sesión para la cuenta de Azure correcta. 
+ En el explorador, compruebe que tiene acceso al [portal de Azure](https://portal.azure.com). Abra Configuración y compruebe si hay alguna restricción.
+ [Agregue Application Insights al proyecto existente](app-insights-asp-net.md): en el Explorador de soluciones, haga clic con el botón derecho en el proyecto y seleccione “Agregar Application Insights”.
+ Si sigue sin funcionar, siga el [procedimiento manual](app-insights-start-monitoring-app-health-usage.md) para agregar un recurso en el portal y, a continuación, agregue el SDK al proyecto. 

## <a name="emptykey"></a>Aparece el mensaje de error "La clave de instrumentación no puede estar vacía".

Parece que algo salió mal durante la instalación de Application Insights o puede ser un adaptador del registro.

En el Explorador de soluciones, haga clic con el botón derecho en `ApplicationInsights.config` y elija **Configurar Application Insights**. Aparecerá un cuadro de diálogo que le invita a iniciar sesión en Azure y a crear un recurso de Application Insights, o a volver a utilizar uno existente.


##<a name="NuGetBuild"></a> "Faltan paquetes NuGet" en el servidor de compilación

*Todo se compila correctamente al depurar en el equipo de desarrollo pero aparece un error de NuGet en el servidor de compilación.*

Consulte [Restauración de paquetes NuGet](http://docs.nuget.org/Consume/Package-Restore) y [Restauración automática de paquetes](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## Falta el comando de menú para abrir Application Insights desde Visual Studio

*Al hacer clic con el botón derecho en el Explorador de soluciones del proyecto, no se ve ningún comando de Application Insights o no se ve un comando Abrir Application Insights.*

Causas probables:

* Si se ha creado el recurso de Application Insights manualmente o si el proyecto es de un tipo que no es compatible con las herramientas de Application Insights.
* Las herramientas de Application Insights están deshabilitadas en Visual Studio.
* Visual Studio es anterior a la actualización 3 de 2013.

Solución:

* Compruebe que la versión de Visual Studio sea la actualización 3 de 2013 o posterior.
* Seleccione **Herramientas**, **Extensiones y actualizaciones** y compruebe que **Herramientas de Application Insights** está instalado y habilitado. Si es así, haga clic en **Actualizaciones** para ver si hay alguna disponible.
* En el Explorador de soluciones, haga clic con el botón derecho en el proyecto. Si ve el comando **Configurar Application Insights**, úselo para conectar el proyecto al recurso en el servicio de Application Insights.


De lo contrario, el tipo de proyecto no es directamente compatible con las herramientas de Application Insights. Para ver la telemetría, inicie sesión en el [Portal de Azure](https://portal.azure.com), elija Application Insights en la barra de navegación de la izquierda y seleccione la aplicación.

## 'Acceso denegado' al abrir Application Insights desde Visual Studio

*El comando de menú 'Abrir Application Insights' lleva al Portal de Azure pero aparece un error de "acceso denegado".*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

El inicio de sesión de Microsoft que utilizó por última vez en el explorador predeterminado no tiene acceso al [recurso que se creó cuando se agregó Application Insights a esta aplicación](app-insights-asp-net.md). Hay dos causas probables:

* Tiene más de una cuenta de Microsoft: ¿quizás tenga una cuenta profesional y una cuenta personal de Microsoft? El inicio de sesión que se utilizó por última vez en el explorador predeterminado era para una cuenta distinta que la que tiene acceso para [agregar Application Insights al proyecto](app-insights-asp-net.md). 

 * Solución: haga clic en su nombre en la parte superior derecha de la ventana del explorador y cierre la sesión. A continuación, inicie sesión con la cuenta que tiene acceso. En la barra de navegación izquierda, haga clic en Application Insights y seleccione la aplicación.

* Otro usuario ha agregado Application Insights al proyecto y se ha olvidado de proporcionarle [acceso al grupo de recursos](app-insights-resources-roles-access-control.md) en el que se creó.

 * Solución: si este usuario utiliza una cuenta de organización, puede agregarle al equipo, o bien, puede concederle acceso individual al grupo de recursos.



## 'No se encuentra ningún activo' al abrir Application Insights desde Visual Studio

*El comando de menú 'Abrir Application Insights' lleva al Portal de Azure pero aparece un error de "no se encuentra ningún activo".*

Causas probables:

* Se ha eliminado el recurso de Application Insights para su aplicación o
* la clave de instrumentación se ha establecido o modificado en ApplicationInsights.config editándola directamente, sin actualizar el archivo de proyecto. 

La clave de instrumentación en ApplicationInsights.config controla dónde se envía la telemetría. Una línea en el archivo de proyecto controla qué recurso se abre al utilizar el comando de Visual Studio.

Solución:

* En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y elija Application Insights, Configurar Application Insights. En el cuadro de diálogo, puede elegir enviar la telemetría a un recurso existente o crear uno nuevo. O:
* Abra el recurso directamente. Inicie sesión en el [Portal de Azure](https://portal.azure.com), elija Application Insights en la barra de navegación de la izquierda y seleccione la aplicación.



## ¿Dónde se encuentra la telemetría?

*Una vez iniciada la sesión en el [Portal de Microsoft Azure](https://portal.azure.com), se ve el panel de inicio de Azure. ¿Dónde se encuentran los datos de Application Insights?*

* En la barra de navegación izquierda, haga clic en Application Insights y luego en el nombre de la aplicación. Si no tiene allí ningún proyecto, es necesario [agregar o configurar Application Insights en el proyecto web](app-insights-asp-net.md).

    Ahí verá algunos gráficos de resumen. Puede hacer clic en ellos para ver su contenido con más detalle.

* En Visual Studio, al depurar la aplicación, haga clic en el botón de Application Insights.


## <a name="q03"></a> No hay datos de servidor (o ningún dato en absoluto)

*Se ejecuta la aplicación y se abre el servicio de Application Insights en Microsoft Azure pero todos los gráficos muestran 'Obtenga información sobre cómo recopilar...' o 'No está configurado.'* o bien, *solo vista de página y datos de usuario, pero no hay datos de servidor.*

+ Ejecute la aplicación en modo de depuración en Visual Studio (F5). Utilice la aplicación para generar telemetría. Compruebe que puede ver los eventos registrados en la ventana de resultados de Visual Studio. 

    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)

+ En el portal de Application Insights, abra [Búsqueda de diagnóstico](app-insights-diagnostic-search.md). Los datos suelen aparecer aquí en primer lugar.
+ Haga clic en el botón Actualizar. La hoja se actualiza periódicamente, pero también puede hacerlo manualmente. El intervalo de actualización es mayor para intervalos de tiempo mayores.
+ Compruebe las claves de instrumentación coincidan. En la hoja principal de la aplicación en el portal de Application Insights, en la lista desplegable de **Essentials**, vea **Clave de instrumentación**. A continuación, en el proyecto de Visual Studio, abra ApplicationInsights.config y busque `<instrumentationkey>`. Compruebe que las dos claves sean iguales. Si no es así:
 + En el portal, haga clic en Application Insights y busque el recurso de aplicación con la clave correcta, o
 + En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto y elija Application Insights, Configurar. Restablezca la aplicación para enviar la telemetría al recurso adecuado.
 + Si no puede encontrar las claves coincidentes, compruebe que utiliza las mismas credenciales de inicio de sesión en Visual Studio que en el portal.

    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
    
+ En el [panel de inicio de Microsoft Azure](https://portal.azure.com), observe el mapa de Estado del servicio. Si hay algunas indicaciones de alerta, espere hasta que hayan vuelto a su estado correcto y después cierre y vuelva a abrir el cuadro de la aplicación de Application Insights.
+ Compruebe también [nuestro blog de estado](http://blogs.msdn.com/b/applicationinsights-status/).
+ ¿Ha escrito algún código para el [SDK del lado servidor](app-insights-api-custom-events-metrics.md) que pueda haber cambiado la clave de instrumentación en instancias `TelemetryClient` o en `TelemetryContext`? ¿O ha escrito una [configuración de filtro o muestreo](app-insights-api-filtering-sampling.md) que pueda estar filtrando demasiado?
+ Si editó ApplicationInsights.config, compruebe minuciosamente la configuración de [TelemetryInitializers y TelemetryProcessors](app-insights-api-filtering-sampling.md). Un parámetro o tipo con un nombre incorrecto puede provocar que el SDK no envíe datos.

## <a name="q04"></a>No hay datos en Vistas de página, Explorador o Uso

*Se ven datos en los gráficos de Tiempo de respuesta del servidor y Solicitudes de servidor pero no hay datos en Tiempo de carga de la vista de página, o en las hojas Explorador o Uso.*

Los datos proceden de los scripts de las páginas web.

+ Si agrega Application Insights a un proyecto web existente, [tendrá que agregar los scripts manualmente](app-insights-javascript.md).
+ Asegúrese de que Internet Explorer no muestra el sitio en modo de compatibilidad.
+ Use la característica de depuración del explorador (F12 en algunos exploradores y, después, elija Red) para comprobar que los datos se envían a `dc.services.visualstudio.com`.

## No hay datos de excepción o dependencia

Consulte [telemetría de dependencias](app-insights-asp-net-dependencies.md) y [telemetría de excepciones](app-insights-asp-net-exceptions.md).

## No hay datos (de servidor) desde que se publicó la aplicación en el servidor

+ Compruebe que realmente copió todas las DLL de Microsoft.ApplicationInsights en el servidor, junto con Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll.
+ En el firewall, tendrá que abrir los puertos TCP 80 y 443 para el tráfico saliente en dc.services.visualstudio.com y f5.services.visualstudio.com.
+ Si tiene que usar un servidor proxy para enviar fuera de la red corporativa, establezca el valor [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) en el archivo Web.config.
+ Windows Server 2008: asegúrese de haber instalado las siguientes actualizaciones: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523) y [KB2600217](https://support.microsoft.com/kb/2600217).


## Solía ver datos, pero ya no sucede esto.

* Compruebe el [blog de estado](http://blogs.msdn.com/b/applicationinsights-status/).
* ¿Ha alcanzado su cuota mensual de puntos de datos? Abra Configuración/Cuotas y Precios para averiguarlo. Si es así, puede actualizar el plan o pagar para obtener capacidad adicional. Consulte el [esquema de precios](https://azure.microsoft.com/pricing/details/application-insights/).


## No veo todos los datos que esperaba

Si la aplicación envía una gran cantidad de datos y usa el SDK de Application Insights para ASP.NET versión 2.0.0-beta3 o posterior, la característica de [muestreo adaptativo](app-insights-sampling.md) puede operar y enviar solo un porcentaje de la telemetría.

Se puede deshabilitar, pero no se recomienda. El muestreo está diseñado para que la telemetría relacionada se transmita correctamente, con fines de diagnóstico.

## Datos geográficos incorrectos en la telemetría de usuario

La ciudad, región y dimensiones del país proceden de las direcciones IP y no siempre son precisas.

## Excepción "Método no encontrado" en la ejecución en Servicios en la nube de Azure

¿Ha realizado la compilación para .NET 4.6? 4.6 no se admite automáticamente en los roles de Servicios en la nube de Azure. [Instale 4.6 en cada rol](../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de ejecutar la aplicación.

## Sigue sin funcionar...

* [Foro de Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/es-ES/home?forum=ApplicationInsights)

<!---HONumber=AcomDC_0224_2016-->