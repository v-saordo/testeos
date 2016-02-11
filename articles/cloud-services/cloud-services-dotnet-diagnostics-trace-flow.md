<properties
	pageTitle="Seguimiento del flujo en una aplicación de servicios en la nube con Diagnósticos de Azure | Microsoft Azure"
	description="Agregar mensajes de seguimiento a una aplicación de Azure para facilitar la depuración, medición del rendimiento, supervisión, análisis del tráfico y mucho más."
	services="cloud-services"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="robb"/>



# Seguimiento del flujo en una aplicación de servicios en la nube con Diagnósticos de Azure

El seguimiento es una manera de supervisar la ejecución de la aplicación mientras se está ejecutando. Puede usar las clases [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx), y [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) para registrar información sobre errores y ejecución de la aplicaciones en registros, archivos de texto u otros dispositivos para su análisis posterior. Para obtener más información acerca del seguimiento, consulte [Seguimiento e instrumentación de aplicaciones](https://msdn.microsoft.com/library/zs6s4h68.aspx).


## Uso de las instrucciones de seguimiento y los modificadores de seguimiento

Implemente el seguimiento en la aplicación de servicios en la nube al agregar [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) a la configuración de la aplicación y realizar llamadas a System.Diagnostics.Trace o a System.Diagnostics.Debug en el código de aplicación. Use el archivo de configuración *app.config* para los roles de trabajo y *web.config* para los roles web. Cuando se crea un nuevo servicio hospedado mediante una plantilla de Visual Studio, Diagnósticos de Azure se agrega automáticamente al proyecto y DiagnosticMonitorTraceListener se agrega al archivo de configuración adecuado para los roles que se añadan.

Para obtener información sobre cómo colocar instrucciones de seguimiento, consulte [Procedimientos: incorporación de instrucciones de seguimiento al código de aplicación](https://msdn.microsoft.com/library/zd83saa2.aspx).

Al colocar [modificadores de seguimiento](https://msdn.microsoft.com/library/3at424ac.aspx) en el código, puede controlar si se realiza el seguimiento y cuál es su alcance. Con ello puede supervisar el estado de la aplicación en un entorno de producción. Esto es especialmente importante en una aplicación empresarial que usa varios componentes que se ejecutan en varios equipos. Para obtener más información, consulte [Procedimientos: configuración de modificadores de seguimiento](https://msdn.microsoft.com/library/t06xyy08.aspx).

## Configuración de la escucha de seguimiento en una aplicación de Azure

Tendrá que configurar "agentes de escucha" para Trace, Debug y TraceSource, para recopilar y registrar los mensajes que se envían. Los agentes de escucha de recopilan, almacenan y enrutan los mensajes de seguimiento. Estos dirigen los resultados del seguimiento a un destino apropiado, como un registro, una ventana o un archivo de texto. Diagnósticos de Azure usa la clase [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx).

Antes de completar el siguiente procedimiento, tiene que inicializar el monitor de diagnóstico de Azure. Para ello, consulte [Habilitación de diagnósticos en Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Tenga en cuenta que si usa las plantillas proporcionadas por Visual Studio, la configuración del agente de escucha se agrega automáticamente.


### Incorporación de un agente de escucha de seguimiento

1. Abra el archivo web.config o app.config para su rol.
2. Agregue el siguiente código al archivo:

	```
	<system.diagnostics>
		<trace>
			<listeners>
				<add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
		          Microsoft.WindowsAzure.Diagnostics,
		          Version=1.0.0.0,
		          Culture=neutral,
		          PublicKeyToken=31bf3856ad364e35"
		          name="AzureDiagnostics">
			  	  <filter type="" />
				</add>
			</listeners>
		</trace>
	</system.diagnostics>
	```
	>[AZURE.IMPORTANT]Asegúrese de que tiene una referencia de proyecto al ensamblado Microsoft.WindowsAzure.Diagnostics. Actualice el número de versión en el xml anterior para que coincida con la versión del ensamblado de Microsoft.WindowsAzure.Diagnostics al que se hace referencia.
	
3. Guarde el archivo de configuración.

Para más información sobre los agentes de escucha, vea [Agentes de escucha de seguimiento](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Después de completar los pasos para agregar el agente de escucha, puede agregar instrucciones de seguimiento al código.


### Para agregar instrucciones de seguimiento al código

1. Abra un archivo de origen para la aplicación. Por ejemplo, el archivo <RoleName>.cs para el rol de trabajo o el rol web.
2. Agregue la siguiente instrucción using si no se agregó ya: ```
	    using System.Diagnostics;
	```
3. Agregue instrucciones de seguimiento en donde desee capturar información sobre el estado de la aplicación. Puede usar diversos métodos para dar formato al resultado de la instrucción de seguimiento. Para información, vea [Procedimientos: adición de instrucciones de seguimiento al código de aplicación](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Guarde el archivo de origen.

<!---HONumber=AcomDC_1217_2015-->