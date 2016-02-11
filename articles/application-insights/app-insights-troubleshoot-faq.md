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
	ms.date="11/25/2015" 
	ms.author="awills"/>
 
# Solución de problemas y preguntas - Application Insights para ASP.NET

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

## Adición del SDK

#### <a name="q01"></a>No veo ninguna opción para agregar Application Insights a mi proyecto en Visual Studio

+ Asegúrese de que tiene [Visual Studio 2013 Update 3 o posterior](http://go.microsoft.com/fwlink/?LinkId=397827). Viene preinstalado con las Herramientas de Application Insights.
+ Aunque las herramientas no admiten todos los tipos de aplicaciones, es posible que pueda agregar un SDK de Application Insights al proyecto manualmente. Siga [este procedimiento][windows]. 


#### <a name="q02"></a>Mi nuevo proyecto web se ha creado, pero se produjo un error al agregar Application Insights.

Esto puede suceder si:

* se produce un error de comunicación con el portal de Application Insights,
* hay algún problema con su cuenta,
* solo tiene [acceso de lectura a la suscripción o el grupo en que ha tratado de crear el recurso nuevo](app-insights-resources-roles-access-control.md).

Solución:

+ Compruebe que ha proporcionado las credenciales de inicio de sesión para la cuenta de Azure correcta. En algunas versiones anteriores de las herramientas, las credenciales de Microsoft Azure que ve en el cuadro de diálogo Nuevo proyecto, pueden ser diferentes de las credenciales que ve en la parte superior derecha de Visual Studio.
+ En el explorador, compruebe que tiene acceso al [portal de Azure](https://portal.azure.com). Abra Configuración y compruebe si hay alguna restricción.
+ [Agregue Application Insights al proyecto existente][start]\: en el Explorador de soluciones, haga clic con el botón derecho en el proyecto y seleccione “Agregar Application Insights”.
+ Si sigue sin funcionar, siga el [procedimiento manual](app-insights-start-monitoring-app-health-usage.md) para agregar un recurso en el portal y, a continuación, agregue el SDK al proyecto. 

#### <a name="emptykey"></a>Aparece el mensaje de error "La clave de instrumentación no puede estar vacía".

Parece que algo salió mal durante la instalación de Application Insights o puede ser un adaptador del registro.

En el Explorador de soluciones, haga clic con el botón derecho en `ApplicationInsights.config` y elija **Actualizar Application Insights**. Aparecerá un cuadro de diálogo que le invita a iniciar sesión en Azure y a crear un recurso de Application Insights, o a volver a utilizar uno existente.


#### <a name="q14"></a>¿Qué modifica Application Insights en mi proyecto?

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

####<a name="NuGetBuild"></a> Se indica que "faltan paquetes NuGet" en el servidor de compilación, pero las máquinas de desarrollo compilan correctamente

Consulte [Restauración de paquetes NuGet](http://docs.nuget.org/Consume/Package-Restore) y [Restauración automática de paquetes](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

####<a name="FailUpdate"></a> Al intentar compilar después de actualizar a la versión 0.17 o posterior de los paquetes NuGet, se indica que el "proyecto hace referencia a paquetes NuGet que faltan en el equipo"

Si aparece el error anterior después de actualizar al paquete NuGet 0.17 o a versiones más recientes, debe editar el archivo del proyecto y quitar los elementos BCL de destino que hayan quedado.

Para ello, siga estos pasos:

1. Haga clic con el botón derecho en el proyecto en el Explorador de soluciones y elija Descargar el proyecto.
2. Vuelva a hacer clic con el botón derecho en el proyecto y elija Editar *suProyecto.csproj*. 
3. Vaya a la parte inferior del archivo del proyecto y quite los elementos BCL de destino similares a: ``` <Import Project="..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets" Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" />
	  
	  <Target Name="EnsureBclBuildImported" BeforeTargets="BeforeBuild" Condition="'$(BclBuildImported)' == ''">
	  
	    <Error Condition="!Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=317567." HelpKeyword="BCLBUILD2001" />
	    
	    <Error Condition="Exists('..\packages\Microsoft.Bcl.Build.1.0.14\tools\Microsoft.Bcl.Build.targets')" Text="The build restored NuGet packages. Build the project again to include these packages in the build. For more information, see http://go.microsoft.com/fwlink/?LinkID=317568." HelpKeyword="BCLBUILD2002" />
	    
	</Target> ```
4. Guarde el archivo.
5. Haga clic con el botón derecho en el proyecto y elija Volver a cargar *suProyecto.csproj*

## ¿Cómo se puede actualizar desde versiones anteriores de SDK?

Consulte las [notas de la versión](app-insights-release-notes.md) del SDK adecuado para el tipo de aplicación.


## No aparecen datos

#### <a name="q03"></a>He agregado Application Insights correctamente y he ejecutado mi aplicación, pero no aparecen datos en el portal

+ En la página de información general, haga clic en el icono de búsqueda para abrir Búsqueda de diagnóstico. Los datos aparecen aquí en primer lugar.
+ Haga clic en el botón Actualizar. La hoja se actualiza periódicamente, pero también puede hacerlo manualmente. El intervalo de actualización es mayor para intervalos de tiempo mayores.
+ En el panel de inicio de Microsoft Azure, observe el mapa de estado del servicio. Si hay algunas indicaciones de alerta, espere hasta que hayan vuelto a su estado correcto y después cierre y vuelva a abrir el cuadro de la aplicación de Application Insights.
+ Compruebe también [nuestro blog de estado](http://blogs.msdn.com/b/applicationinsights-status/).
+ Si editó ApplicationInsights.config, compruebe minuciosamente la configuración de TelemetryInitializers y TelemetryProcessors. Un parámetro o tipo con un nombre incorrecto puede provocar que el SDK no envíe datos.

#### No hay datos desde que se publicó la aplicación en el servidor

+ Compruebe que realmente copió todas las DLL de Microsoft.ApplicationInsights en el servidor, junto con Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll.
+ En el firewall, tendrá que abrir los puertos TCP 80 y 443 para el tráfico saliente en dc.services.visualstudio.com y f5.services.visualstudio.com.
+ Si tiene que usar un servidor proxy para enviar fuera de la red corporativa, establezca el valor [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) en el archivo Web.config.
+ Windows Server 2008: asegúrese de haber instalado las siguientes actualizaciones: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523) y [KB2600217](https://support.microsoft.com/kb/2600217).


#### <a name="q04"></a>No veo ningún dato para mi sitio web en Análisis de uso

+ Los datos proceden de los scripts de las páginas web. Si agrega Application Insights a un proyecto web existente, [tendrá que agregar los scripts manualmente][start].
+ Asegúrese de que Internet Explorer no muestra el sitio en modo de compatibilidad.
+ Use la característica de depuración del explorador (F12 en algunos exploradores y, después, elija Red) para comprobar que los datos se envían a dc.services.visualstudio.com.

#### Solía ver datos, pero ya no sucede esto.

* Compruebe el [blog de estado](http://blogs.msdn.com/b/applicationinsights-status/).
* ¿Ha alcanzado su cuota mensual de puntos de datos? Abra Configuración/Cuotas y Precios para averiguarlo. Si es así, puede actualizar el plan o pagar para obtener capacidad adicional. Consulte el [esquema de precios](https://azure.microsoft.com/pricing/details/application-insights/).


#### No veo todos los datos que esperaba

* **Muestreo.** Si la aplicación envía una gran cantidad de datos y usa el SDK de Application Insights para ASP.NET versión 2.0.0-beta3 o posterior, la característica de muestreo adaptativo puede operar y enviar solamente un porcentaje de los datos de telemetría. Se puede deshabilitar. [Obtenga más información sobre el muestreo.](app-insights-sampling.md)


#### <a name="q08"></a>¿Puedo usar Application Insights para supervisar un servidor web de intranet?

Sí, puede supervisar el estado y el uso si el servidor puede enviar datos a la red pública de Internet. En el firewall, abra los puertos TCP 80 y 443 para el tráfico saliente a dc.services.visualstudio.com y f5.services.visualstudio.com.

Pero si desea ejecutar pruebas web para su servicio, debe estar accesible desde la red pública de Internet en el puerto 80.

#### ¿Puedo supervisar un servidor web de intranet que no tiene acceso a la red pública de Internet?

Debe organizar un proxy que pueda retransmitir llamadas POST https a dc.services.visualstudio.com.

## Monitor de estado no funciona

Consulte [Solución de problemas del Monitor de estado](app-insights-monitor-performance-live-website-now.md#troubleshooting). Los puertos del firewall son el problema más común.

## El Portal

#### <a name="q05"></a>Estoy consultando el panel de inicio en vista previa de Microsoft Azure. ¿Cómo encuentro mis datos en Application Insights?

Puede:

* Elegir Examinar, Application Insights, su nombre de proyecto. Si no tiene allí ningún proyecto, tiene que [agregar Application Insights a su proyecto web en Visual Studio][start].

* En el Explorador de soluciones de Visual Studio, haga clic con el botón secundario en el proyecto web y elija Abrir portal de Application Insights.


#### <a name="update"></a>¿Cómo puedo cambiar el recurso de Azure al que mi proyecto envía datos?

En el Explorador de soluciones, haga clic con el botón derecho en `ApplicationInsights.config` y elija **Actualizar Application Insights**. Puede enviar los datos a un recurso nuevo o existente en Azure. El Asistente para actualización cambia la clave de instrumentación en ApplicationInsights.config, que determina dónde el SDK del servidor envía los datos. A menos que desactive la opción "Actualizar todo", también cambiará la clave donde aparece en las páginas web.


#### <a name="q06"></a>En la pantalla principal de Microsoft Azure en vista previa, ¿muestra ese mapa el estado de mi aplicación?

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

Puede [escribir un script de PowerShell](app-insights-powershell-script-create-resource.md) para crear un recurso de Application Insights.

## Más respuestas

* [Foro de Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/es-ES/home?forum=ApplicationInsights)


<!--Link references-->

[azurediagnostic]: ../insights-how-to-use-diagnostics.md
[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0128_2016-->