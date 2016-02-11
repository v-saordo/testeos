<properties 
	pageTitle="Consola y registros de streaming" 
	description="Información general de la consola y los registros de transmisión" 
	authors="btardif" 
	manager="wpickett" 
	editor="" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/12/2015" 
	ms.author="byvinyal"/>

#Registros de transmisión y la consola

### Registros de transmisión ###

El Portal de Microsoft Azure ofrece un visor de registros de streaming integrado que le permite ver los eventos de seguimiento desde las aplicaciones web del Servicio de aplicaciones de Azure en tiempo real.

La configuración requiere algunos pasos sencillos:

- Escritura de seguimientos en el código.
- Habilitación de Diagnóstico de aplicaciones desde el Portal de Azure.
- Clic en los registros de streaming en la hoja de la aplicación web

### Cómo escribir los seguimientos en el código ###

La escritura de seguimientos en el código es sencilla. En C# es tan fácil como escribir el siguiente código:

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

La clase de seguimientos se encuentra en el espacio de nombres System.Diagnostics.

En una aplicación node.js, puede escribir este código para conseguir el mismo resultado:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### Habilitación y visualización de registros de transmisión ###

![][BrowseSitesScreenshot] El diagnóstico se habilita por aplicación web. En el [Portal](https://portal.azure.com), haga clic en el botón **Examinar (1)** situado en la barra de menús de la izquierda y luego en **Aplicaciones web (2)** para obtener la **lista (3)** de todas las aplicaciones web.

Haga clic en el nombre de la aplicación web que quiere configurar para navegar hasta ella.
  
![][DiagnosticsLogs] A continuación, haga clic en **Configuración (1)** > **Registros de diagnóstico (2)** y **Active** el **Registro de la aplicación (Sistema de archivos)(3)**. La opción **Nivel** le permite cambiar el nivel de gravedad de las trazas que se capturan. Debe configurar esto en **Detallado** si solo está intentando familiarizarse con la característica, ya que esto asegurará que se registran las instrucciones de seguimiento.

Haga clic en **SAVE** en la parte superior del cuadro y estará listo para ver los registros.

**Nota:** cuanto mayor sea el **nivel de gravedad**, más recursos del registro se consumen y más trazas se obtendrán. Asegúrese de que se establece en el nivel adecuado al usar esta característica para sitio de producción o de tráfico alto.

![][StreamingLogsScreenshot] Para ver los registros de streaming desde el portal, haga clic en **Herramientas (1)** > **Secuencia de registro (2)**. Si la aplicación escribe instrucciones de seguimiento activamente, debe verlas en la ventana resultante prácticamente en tiempo real.

## Consola ##

El Portal de Azure proporciona acceso a la consola para el entorno de la aplicación web. Puede explorar el sistema de archivos de la aplicación web y ejecutar los scripts de cmd/powershell. Se realizará la vinculación mediante los mismos permisos establecidos en el código de la aplicación web en ejecución cuando se ejecuten los comandos de consola. No podrá obtener acceso a los directorios protegidos o ejecutar scripts que requieran permisos elevados.

![][ConsoleScreenshot] Para ir a la consola, desplácese a una aplicación web tal como se describe en la sección anterior. Haga clic en **Herramientas** > **Consola** y la consola se abrirá.

Para familiarizarse con la consola, pruebe estos comandos básicos:



`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````



<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png

<!---HONumber=Nov15_HO4-->