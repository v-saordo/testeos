<properties
	pageTitle="Habilitación del registro de diagnóstico para aplicaciones web en el Servicio de aplicaciones de Azure"
	description="Obtenga información acerca de cómo habilitar el registro de diagnóstico y agregar la instrumentación a su aplicación, así como la manera de acceder a la información registrada por Azure."
	services="app-service"
	documentationCenter=".net"
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="cephalin"/>

# Habilitación del registro de diagnóstico para aplicaciones web en el Servicio de aplicaciones de Azure

## Información general

Azure integra diagnósticos para ayudar a depurar [Aplicaciones web del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714). En este artículo se ofrece información acerca de cómo habilitar el registro de diagnósticos, agregar instrumentación a la aplicación y obtener acceso a la información registrada por Azure.

En este artículo se usa el [Portal de Azure](https://portal.azure.com), Azure PowerShell y la interfaz de la línea de comandos de Azure (CLI de Azure) para trabajar con registros de diagnóstico. Para obtener información acerca de cómo trabajar con registros de diagnóstico mediante Visual Studio, consulte [Solución de problemas de Azure en Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Diagnóstico del servidor web y diagnóstico de aplicaciones

Aplicaciones web del Servicio de aplicaciones ofrece la funcionalidad de diagnóstico para registrar información del servidor web y de la aplicación web. De forma lógica, estos diagnósticos se dividen en **diagnósticos del servidor web** y **diagnósticos de la aplicación**.

### Diagnósticos del servidor web

Puede habilitar o deshabilitar los siguientes tipos de registros:

- **Registro de errores detallado**: registra información detallada de errores para códigos de estado HTTP que indican un problema (código de estado 400 o superior). Puede contener información que puede ayudar a determinar por qué el servidor ha devuelto el código de error.
- **Seguimiento de solicitudes con error**: registra información detallada acerca de solicitudes con error, incluido un seguimiento de los componentes de IIS usados para procesar la solicitud y el tiempo dedicado a cada componente. Esto puede resultar útil si trata de aumentar el rendimiento del sitio o de aislar lo que causa la devolución de un error HTTP específico.
- **Registro del servidor web**: registra todas las transacciones HTTP con el [formato de archivo de registro extendido de W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Este informe resulta útil para determinar las métricas totales del sitio, como el número de solicitudes tramitadas o cuántas solicitudes proceden de una dirección IP específica.

### Diagnósticos de aplicaciones

El diagnóstico de aplicaciones le permite capturar información generada por una aplicación web. Las aplicaciones de ASP.NET pueden usar la clase [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) para registrar información en el registro de diagnóstico de la aplicación. Por ejemplo:

	System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

En tiempo de ejecución puede recuperar estos registros para ayudar a solucionar problemas. Para obtener más información, consulte [Solución de problemas de aplicaciones web de Azure en Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

Las aplicaciones web del Servicio de aplicaciones también registran información de implementación al publicar contenido en una aplicación web. Esta acción se lleva a cabo automáticamente, por lo que no es necesario realizar ninguna configuración para el registro de implementaciones. El registro de implementaciones le permite determinar por qué se ha producido un error con la implementación. Por ejemplo, si usa un script de implementación personalizado, puede usar el registro de implementaciones para determinar por qué se ha producido un error con el script.

## <a name="enablediag"></a>Habilitación de diagnósticos

Para habilitar diagnósticos en el [Portal de Azure](https://portal.azure.com), vaya a la hoja de la aplicación web y haga clic en **Configuración > Registros de diagnóstico**.

<!-- todo:cleanup dogfood addresses in screenshot -->
![Parte de los registros](./media/web-sites-enable-diagnostic-log/logspart.png)

Cuando habilite **Diagnóstico de aplicaciones**, elija también **Nivel**. Esta configuración le permite filtrar la información capturada como **informativo**, **advertencia** o **error**. Si establece esta opción en **detallado**, registrará toda la información generada por la aplicación.

> [AZURE.NOTE] Al contrario de lo que ocurre al cambiar el archivo web.config, habilitar Diagnóstico de aplicaciones o cambiar los niveles del registro de diagnóstico no recicla el dominio de la aplicación en el que esta se ejecuta.

En el [Portal clásico](https://manage.windowsazure.com), en la pestaña **Configurar** de la aplicación web, puede seleccionar **almacenamiento** o **sistema de archivos** para el **registro de servidor web**. Si selecciona **almacenamiento**, tiene la opción de seleccionar una cuenta de almacenamiento y, a continuación, un contenedor de blobs en el que se escribirán los registros. Todos los demás registros de **diagnósticos del sitio** se escriben solo en el sistema de archivos.

En el [Portal clásico](https://manage.windowsazure.com), la pestaña **Configurar** de la aplicación web también tiene una configuración adicional para el diagnóstico de aplicaciones:

* **Sistema de archivos**: almacena la información de diagnóstico de aplicaciones en el sistema de archivos de la aplicación web. Es posible obtener acceso a estos archivos por FTP, o bien se pueden descargar como un archivo ZIP con la utilización de Azure PowerShell o de la interfaz de la línea de comandos de Azure (CLI de Azure).
* **Almacenamiento de tablas**: almacena la información de diagnóstico de aplicaciones en el nombre de la tabla y en la cuenta de almacenamiento de Azure.
* **Almacenamiento de blobs**: almacena la información de diagnóstico de aplicaciones en el contenedor de blobs y en la cuenta de almacenamiento de Azure.
* **Período de retención**: de manera predeterminada, los registros no se eliminan automáticamente del **almacenamiento de blobs**. Seleccione **Establecer retención** y escriba el número de días durante los cuales desea que se conserven los registros si desea que estos se eliminen automáticamente.

>[AZURE.NOTE] Si se [regeneran las claves de acceso de su cuenta de almacenamiento](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys), deberá restablecer la configuración de registro correspondiente para usar las claves actualizadas. Para ello, siga estos pasos:
>
> 1. En la pestaña **Configurar**, y establezca la característica de registro correspondiente de **Desactivar**. Guarde la configuración.
> 2. Vuelva a habilitar el registro en el blob de la cuenta de almacenamiento o en la tabla. Guarde la configuración.

Al mismo tiempo se puede habilitar cualquier combinación de sistema de archivos, almacenamiento de tablas o almacenamiento de blobs, y estas opciones tiene configuraciones de nivel de registro individuales. Por ejemplo, puede registrar errores y advertencias en el almacenamiento de blobs como una solución de registro a largo plazo, mientras habilita el registro en el sistema de archivos con un nivel detallado.

Si bien las tres ubicaciones de almacenamiento ofrecen la misma información básica de los eventos registrados, **table storage** y **blob storage** registran información adicional como el identificador de instancia, el identificador de subproceso y una marca de tiempo más pormenorizada (formato de marca de graduación) que el registro en **file system**.

> [AZURE.NOTE] Solo se puede obtener acceso a la información almacenada en **almacenamiento de tablas** o **almacenamiento de blobs** mediante una aplicación o un cliente de almacenamiento que puedan trabajar directamente con estos sistemas de almacenamiento. Por ejemplo, Visual Studio 2013 contiene un Explorador de almacenamiento que se puede usar para explorar el almacenamiento de tabla o de blobs y HDInsight puede obtener acceso a los datos almacenados en el almacenamiento de blobs. También puede escribir una aplicación que obtiene acceso al almacenamiento de Azure mediante algunos de los [SDK de Azure](/downloads/#).

> [AZURE.NOTE] Los diagnósticos también se pueden habilitar desde Azure PowerShell con el cmdlet **Set-AzureWebsite**. Si no tiene instalado Azure PowerShell o si no lo ha configurado para utilizar su suscripción a Azure, consulte [Uso de Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

##<a name="download"></a> Descarga de registros

Se puede obtener acceso a la información de diagnóstico almacenada en el sistema de archivos de la aplicación web directamente mediante FTP. No obstante, también se puede descargar como un archivo ZIP con Azure PowerShell o mediante la interfaz de la línea de comandos de Azure.

La estructura de directorios en que se almacenan los registros es la siguiente:

* **Registros de aplicaciones**: /LogFiles/Application/. Esta carpeta contiene uno o varios archivos de texto con información generada por el registro de aplicaciones.

* **Seguimientos de solicitudes con error**: /LogFiles/W3SVC#########/. Esta carpeta contiene un archivo XSL y uno o varios archivos XML. Asegúrese de descargar el archivo XSL en el mismo directorio de los archivos XML, porque el archivo XSL proporciona funcionalidad para dar formato y filtrar los contenidos de los archivos XML cuando se visualizan en Internet Explorer.

* **Registros detallados de errores**: /LogFiles/DetailedErrors/. Esta carpeta contiene uno o varios archivos .htm con información amplia de todos los errores HTTP que se han producido.

* **Registros de servidor web**: /LogFiles/http/RawLogs. Esta carpeta contiene uno o varios archivos de texto a los que se le aplica el [formato de archivo de registro extendido W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx).

* **Registros de implementaciones**: /LogFiles/Git. Esta carpeta contiene registros generados por los procesos de implementación internos usados por las aplicaciones web de Azure, además de los registros de las implementaciones Git.

### FTP

Para obtener acceso a la información de diagnóstico mediante FTP, visite el **panel** de la aplicación web en el [Portal clásico](https://manage.windowsazure.com). En la sección **Vista rápida**, haga clic en el vínculo **Registros de diagnóstico de FTP** para obtener acceso a los archivos de registro mediante FTP. La entrada **Usuario de implementación /FTP** enumera el nombre de usuario que debe usarse para obtener acceso al sitio FTP.

> [AZURE.NOTE] Si no se establece la entrada **Usuario de implementación/FTP** o si ha olvidado la contraseña de este usuario, puede crear un nuevo usuario y una nueva contraseña mediante el vínculo **Restablecer credenciales de implementación** en la sección **Vista rápida** del **Panel**.

### Descarga con Azure PowerShell

Para descargar los archivos de registro, inicie una nueva instancia de Azure PowerShell y use el siguiente comando:

	Save-AzureWebSiteLog -Name webappname

Este comando guardará los registros de la aplicación web que especifica el parámetro **-Name** en un archivo con nombre **logs.zip** en el directorio actual.

> [AZURE.NOTE] Si no tiene instalado Azure PowerShell o si no lo ha configurado para utilizar su suscripción a Azure, consulte [Uso de Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### Descarga con la interfaz de la línea de comandos de Azure

Para descargar los archivos de registro mediante la interfaz de la línea de comandos de Azure, abra una sesión nueva del símbolo del sistema, PowerShell, Bash o Terminal y escriba el siguiente comando:

	azure site log download webappname

Este comando guardará los registros en la aplicación web denominada "webappname" en un archivo con nombre **diagnostics.zip** en el directorio actual.

> [AZURE.NOTE] Si no tiene instalada la interfaz de la línea de comandos de Azure (CLI de Azure) o si no la ha configurado para que use la suscripción de Azure, consulte [Cómo usar la CLI de Azure](../xplat-cli-install.md).

## Visualización de registros en Application Insights

Visual Studio Application Insights proporciona herramientas para filtrar y buscar registros y para correlacionar los registros con solicitudes y otros eventos.

1. Incorporación del SDK de Application Insights al proyecto de Visual Studio
 * En el Explorador de soluciones, haga clic con el botón secundario en el proyecto y elija Agregar Application Insights. Se le guiará a través de pasos que incluyen la creación de un recurso de Application Insights. [Más información](../application-insights/app-insights-start-monitoring-app-health-usage.md)
2. Agregue el paquete del agente de escucha.
 * Haga clic con el botón secundario en el proyecto y elija Administrar paquetes de NuGet. Seleccione `Microsoft.ApplicationInsights.TraceListener` [Más información](../application-insights/app-insights-asp-net-trace-logs.md).
3. Cargue el proyecto y ejecútelo para generar datos de registro.
4. En el [Portal de Azure](https://portal.azure.com/), busque el nuevo recurso de Application Insights y abra **Buscar**. Verá los datos de registro, junto con la solicitud, el uso y otra telemetría. Es posible que algunos datos de telemetría demoren unos minutos en aparecer: haga clic en Actualizar. [Más información](../application-insights/app-insights-diagnostic-search.md)

[Obtenga más información acerca del seguimiento del rendimiento con Application Insights](../insights-perf-analytics.md)

##<a name="streamlogs"></a> Registros

Al implementar una aplicación, suele resultar útil ver la información de registro casi en tiempo real. Para ello, puede transmitir la información de registro al entorno de desarrollo con Azure PowerShell o la interfaz de la línea de comandos de Azure.

> [AZURE.NOTE] Algunos tipos de búfer de registros se escriben en el archivo de registro, lo que puede ocasionar la transmisión de eventos desordenados. Por ejemplo, una entrada de registro de aplicaciones que se genera cuando un usuario visita una página se puede visualizar en la transmisión antes de la entrada de registro HTTP correspondiente para la solicitud de la página.

> [AZURE.NOTE] La transmisión de registros también transmitirá información escrita en cualquier archivo de texto almacenado en la carpeta **D:\\home\\LogFiles\**.

### Transmisión con Azure PowerShell

Para transmitir información de registro, inicie una nueva instancia de Azure PowerShell y use el siguiente comando:

	Get-AzureWebSiteLog -Name webappname -Tail

Este comando establecerá conexión con la aplicación web especificada por el parámetro **-Name** y comenzará a transmitir información a la ventana de PowerShell a medida que se produzcan los eventos de registro en la aplicación web. Toda la información escrita en archivos terminados en .txt, .log o .htm almacenados en el directorio /LogFiles (d:/home/logfiles) se transmitirá a la consola local.

Para filtrar eventos específicos, como errores, use el parámetro **-Message**. Por ejemplo:

	Get-AzureWebSiteLog -Name webappname -Tail -Message Error

Para filtrar tipos de registros específicos, como HTTP, use el parámetro **-Path**. Por ejemplo:

	Get-AzureWebSiteLog -Name webappname -Tail -Path http

Para ver una lista de rutas de acceso disponibles, use el parámetro -ListPath.

> [AZURE.NOTE] Si no tiene instalado Azure PowerShell o si no lo ha configurado para utilizar su suscripción a Azure, consulte [Uso de Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/).

### Transmisión con la interfaz de la línea de comandos de Azure

Para transmitir información de registro, abra una nueva sesión del símbolo del sistema, PowerShell, Bash o Terminal y escriba el siguiente comando:

	azure site log tail webappname

Este comando establecerá conexión con la aplicación web denominada "webappname" y comenzará a transmitir información a la ventana a medida que se produzcan los eventos de registro en la aplicación web. Toda la información escrita en archivos terminados en .txt, .log o .htm almacenados en el directorio /LogFiles (d:/home/logfiles) se transmitirá a la consola local.

Para filtrar eventos específicos, como errores, use el parámetro **--Filter**. Por ejemplo:

	azure site log tail webappname --filter Error

Para filtrar tipos de registros específicos, como HTTP, use el parámetro **--Path**. Por ejemplo:

	azure site log tail webappname --path http

> [AZURE.NOTE] Si no tiene instalada la interfaz de la línea de comandos de Azure o si no la ha configurado para que use la suscripción de Azure, consulte [Cómo utilizar la interfaz de línea de comandos de Azure](../xplat-cli-install.md).

##<a name="understandlogs"></a> Información sobre los registros de diagnóstico

### Registros de diagnóstico de aplicaciones

El diagnóstico de aplicaciones almacena información con un formato específico para aplicaciones .NET, en función de si almacena los registros en el sistema de archivos, en el almacenamiento de tablas o en el almacenamiento de blobs. El conjunto base de datos almacenados es el mismo en los tres tipos de almacenamiento: la fecha y la hora en que se ha producido el evento, el identificador del proceso que ha producido el evento, el tipo de evento (información, advertencia o error) y el mensaje del evento.

__Sistema de archivos__

Cada línea registrada en el sistema de archivos o recibida mediante transmisión presentará el siguiente formato:

	{Date}  PID[{process id}] {event type/level} {message}

Por ejemplo, un evento de error presentaría un formato similar al siguiente:

	2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

El registro en el sistema de archivos ofrece la información más básica de los tres métodos disponibles, ya que ofrece solo la hora, el identificador del proceso, el nivel de evento y el mensaje.

__Almacenamiento de tablas__

Al realizar registros en el almacenamiento de tabla, se usan propiedades adicionales para facilitar la búsqueda de los datos almacenados en la tabla, así como información más pormenorizada sobre el evento. Las siguientes propiedades (columnas) se usan para cada entidad (fila) almacenada en la tabla.

Nombre de propiedad|Valor/formato
---|---
PartitionKey|Fecha/hora del evento con el formato aaaaMMddHH
RowKey|Un valor de GUID que identifica a esta entidad de forma exclusiva
Timestamp|La fecha y la hora en que se ha producido el evento
EventTickCount|La fecha y hora en que se ha producido el evento, con formato de marca de graduación (mayor precisión)
ApplicationName|El nombre de la aplicación web
Level|Nivel del evento (por ejemplo, error, advertencia o información)
EventId|El identificador de este evento<p><p>El valor predeterminado es 0 si no se ha especificado ninguno
InstanceId|Instancia de la aplicación web en que se ha producido el evento
Pid|Identificador del proceso
Tid|El identificador del subproceso que ha generado el evento
Message|Mensaje detallado del evento

__Almacenamiento de blobs__

Al realizar registros en el almacenamiento de blobs, los datos se almacenan con un formato de valores separados por comas (CSV). De manera similar al almacenamiento de tabla, se registran campos adicionales para ofrecer información más pormenorizada acerca del evento. Las siguientes propiedades se usan para cada fila con el formato CSV:

Nombre de propiedad|Valor/formato
---|---
Date|La fecha y la hora en que se ha producido el evento
Level|Nivel del evento (por ejemplo, error, advertencia o información)
ApplicationName|El nombre de la aplicación web
InstanceId|Instancia de la aplicación web en que se ha producido el evento
EventTickCount|La fecha y hora en que se ha producido el evento, con formato de marca de graduación (mayor precisión)
EventId|El identificador de este evento<p><p>El valor predeterminado es 0 si no se ha especificado ninguno
Pid|Identificador del proceso
Tid|El identificador del subproceso que ha generado el evento
Message|Mensaje detallado del evento

Los datos almacenados en un blob serían similares a los siguientes:

	date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
	2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [AZURE.NOTE] La primera línea del registro contendrá los encabezados de columna tal y como se representan en este ejemplo.

### Seguimiento de solicitudes con error

El seguimiento de solicitudes con error se almacena en archivos XML con nombre __fr######.xml__. Para facilitar la visualización de la información registrada, se proporciona una hoja de estilo XSL con nombre __freb.xsl__ en el mismo directorio que los archivos XML. Al abrir uno de los archivos XML en Internet Explorer, se usará la hoja de estilo XSL para ofrecer una visualización con formato de la información de seguimiento. El formato será similar al siguiente:

![solicitud con error visualizada en el explorador](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### Registros de error detallados

Los registros de error detallados son documentos HTML que ofrecen información más detallada sobre los errores HTTP que se han producido. Habida cuenta de que se trata sencillamente de documentos HTML, se pueden ver en un explorador web.

### Registros del servidor web

A los registros del servidor web se les aplica el [formato de archivo de registro extendido W3C](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). Esta información se puede leer con un editor de texto o analizarse con utilidades como el [analizador del registro](http://go.microsoft.com/fwlink/?LinkId=246619).

> [AZURE.NOTE] Los registros generados por aplicaciones web de Azure no admiten los campos __s-computername__, __s-ip__, o __cs-version__.

##<a name="nextsteps"></a> Pasos siguientes

- [Supervisión de aplicaciones web](/manage/services/web-sites/how-to-monitor-websites/)
- [Solución de problemas de aplicaciones web de Azure en Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
- [Análisis de registros de aplicación web en HDInsight](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [AZURE.NOTE] Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio de portal anterior al nuevo, consulte: [Referencia para navegar en el portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715)
 

<!---HONumber=AcomDC_0128_2016-->