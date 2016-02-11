<properties 
	pageTitle="Solución de problemas de una aplicación web en el Servicio de aplicaciones de Azure con Visual Studio" 
	description="Obtenga información acerca de cómo solucionar problemas de una aplicación web de Azure con herramientas de registro, seguimiento y depuración remota que están integradas en Visual Studio 2013." 
	services="app-service" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Solución de problemas de una aplicación web en el Servicio de aplicaciones de Azure con Visual Studio

## Información general

En este tutorial se muestra cómo utilizar herramientas de Visual Studio para ayudar a depurar una aplicación web mientras se ejecuta en el [Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714), ya sea en [modo de depuración](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) de manera remota o consultando los registros del servidor web o de aplicación.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Aprenderá a realizar los siguientes procedimientos:

* Funciones de administración de aplicaciones web de Azure disponibles en Visual Studio.
* Uso de la vista remota de Visual Studio para realizar cambios rápidos en una aplicación web remota.
* Ejecución del modo de depuración de manera remota mientras un proyecto se ejecuta en Azure, tanto para una aplicación web como para un WebJob.
* Creación de registros de seguimiento de aplicación y su visualización mientras los crea la aplicación.
* Visualización de registros de servidor web, incluidos mensajes de error detallados y seguimiento de solicitudes con error.
* Envío de registros de diagnóstico a una cuenta de almacenamiento de Azure y su visualización aquí.

Si tiene Visual Studio Ultimate, también puede usar [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) para la depuración. IntelliTrace no se trata en este tutorial.

## <a name="prerequisites"></a>Requisitos previos

Este tutorial funciona con el entorno de desarrollo, proyecto web y aplicación web de Azure que configuró en [Introducción a Azure y ASP.NET][GetStarted]. Para las secciones WebJobs, necesitará la aplicación que cree en [Introducción al SDK de WebJobs de Azure][GetStartedWJ].

Los ejemplos de código que aparecen en este tutorial son para una aplicación web C# MVC, pero los procedimientos de solución de problemas son los mismos para las aplicaciones de Web Forms y Visual Basic.

La depuración remota requiere Visual Studio 2013 o Visual Studio 2012 con Update 4. Las características de depuración remota y del **Explorador de servidores** para WebJobs requieren [Visual Studio 2013 Update 4](http://go.microsoft.com/fwlink/?LinkID=510314) o posterior. Las demás características mostradas en el tutorial también funcionan en Visual Studio 2013 Express para Web, y Visual Studio 2012 Express para Web.

La característica de registros de streaming solo funciona para aplicaciones dirigidas a .NET Framework 4 o superior.

## <a name="sitemanagement"></a>Administración y configuración de la aplicación web

Visual Studio proporciona acceso a un subconjunto de las funciones de administración de aplicaciones web y los ajustes de configuración disponibles en el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715). En esta sección podrá ver las opciones y funciones disponibles.

1. Si todavía no ha iniciado sesión en Azure en Visual Studio, haga clic en el botón **Conectar a Azure** en el **Explorador de servidores**.

	Una alternativa es instalar un certificado de administración que permita el acceso a su cuenta. Si elige instalar un certificado, haga clic con el botón secundario en el nodo **Azure** del **Explorador de servidores** y, a continuación, haga clic en **Administrar suscripciones** en el menú contextual. En el cuadro de diálogo **Administrar suscripciones de Azure**, haga clic en la pestaña **Certificados** y, a continuación, en **Importar**. Siga las instrucciones para descargar e importar un archivo de suscripción (también conocido como archivo *.publishsettings*) para su cuenta de Azure.

	> [AZURE.NOTE]Si descarga un archivo de suscripción, guárdelo en una carpeta ajena a los directorios de código fuente (por ejemplo, en la carpeta Descargas) y elimínelo una vez que finalice la importación. Si un usuario malintencionado obtuviera acceso al archivo de suscripción, podría editar, crear y eliminar servicios de Azure.

	Para obtener más información acerca de la conexión a recursos de Azure desde Visual Studio, consulte [Administración de cuentas, suscripciones y roles administrativos](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).

2. En el **Explorador de servidores**, expanda **Azure** y, a continuación, **Servicio de aplicaciones**.

3. Expanda el grupo de recursos que incluye la aplicación web que creó en [Introducción a Azure y ASP.NET][GetStarted] y, a continuación, haga clic con el botón secundario en el nodo de la aplicación web y haga clic en **Ver configuración**.

	![Ver configuración en el Explorador de servidores](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

	Aparecerá la pestaña **Aplicación web de Azure** y podrá ver las tareas de administración y configuración de aplicaciones web disponibles en Visual Studio.

	![Ventana de aplicaciones web de Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

	En este tutorial usará los menús desplegables de registro y seguimiento. También utilizará la depuración remota, pero va a utilizar un método distinto para habilitarla.
   
	Para obtener información acerca de los cuadros Configuración de aplicaciones y Cadenas de conexión de esta ventana, consulte [Aplicaciones web de Azure: Funcionamiento de las cadenas de aplicación y de las cadenas de conexión](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx).

	Si quiere realizar una tarea de administración de aplicaciones web que no se puede hacer en esta ventana, haga clic en **Abrir en Portal de administración** para abrir una ventana del explorador en el Portal de Azure clásico. Para obtener más información, consulte [Configuración de aplicaciones web](/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

## <a name="remoteview"></a>Acceso a archivos de aplicaciones web en el Explorador de servidores

Normalmente implementa un sitio con la marca `customErrors` en el archivo Web.config establecida en `On` o en `RemoteOnly`, lo que significa que no recibirá un práctico mensaje de error cuando algo se realice de manera incorrecta. Para muchos errores, todo lo que verá será una página como alguna de las siguientes.

**Error del servidor en la aplicación '/':**

![Página de error poco práctica](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Se produjo un error:**

![Página de error poco práctica](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**El sitio web no puede mostrar la página**

![Página de error poco práctica](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Con frecuencia, la manera más fácil de encontrar la causa de un error es habilitar los mensajes detallados de error, una acción que se explica en la primera de las capturas de pantalla anteriores. Eso requiere un cambio en el archivo Web.config implementado. Puede editar el archivo *Web.config* en el proyecto y volver a implementar el proyecto, o bien crear una [transformación de Web.config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) e implementar una compilación de depuración, pero hay una forma más rápida: en el **Explorador de soluciones** puede ver y editar archivos en el sitio remoto utilizando directamente la característica de *vista remota*.

1. En el **Explorador de servidores**, expanda **Azure**, expanda **Servicio de aplicaciones**, expanda el grupo de recursos en el que se encuentra su aplicación web y, a continuación, expanda el nodo de la aplicación web.

	Puede ver los nodos que le proporcionan acceso a los archivos de registro y de contenido de la aplicación web.

2. Expanda el nodo **Archivos** y haga doble clic en el archivo *Web.config*.

	![Abrir Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

	Visual Studio abre el archivo Web.config desde la aplicación web remota y muestra [Remote] junto al nombre de archivo en la barra de títulos.

3. Agregue la siguiente línea al elemento `system.web`:

	`<customErrors mode="off"></customErrors>`

	![Editar Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)

4. Actualice el explorador que actualmente muestra el mensaje de error poco práctico y ahora podrá ver un mensaje detallado del error, tal como muestra el siguiente ejemplo:

	![Mensajes de error detallados](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

	(El error que aparece se generó al agregar la línea en rojo a *Views\Home\Index.cshtml*).

Editar el archivo Web.config es solo un ejemplo de las situaciones en las que la capacidad de leer y editar archivos en su aplicación web de Azure facilita la solución de problemas.

## <a name="remotedebug"></a>Aplicaciones web de depuración remota

Si el mensaje de error detallado no proporciona información suficiente y no es posible recrear el error de manera local, otra forma de solucionar problemas es ejecutar remotamente en modo de depuración. Puede definir puntos de interrupción, manipular directamente la memoria, revisar el código e, incluso, cambiar la ruta del código.

La depuración remota no funciona en ediciones Express de Visual Studio.

Esta sección muestra cómo depurar remotamente con el proyecto que crea en [Introducción a Azure y ASP.NET][GetStarted].

1. Abra el proyecto web que creó en [Introducción a Azure y ASP.NET][GetStarted].

1. Abra *Controllers\HomeController.cs*.

2. Elimine el método `About()` e inserte el siguiente código en su lugar.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }

2. [Establezca un punto de interrupción](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) en la línea `ViewBag.Message`.

1. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto y, a continuación, en **Publicar**.

2. En la lista desplegable **Perfil**, seleccione el mismo perfil que utilizó en [Introducción a Azure y ASP.NET][GetStarted].

3. Haga clic en la pestaña **Configuración**, cambie la **Configuración** a **Depurar** y, a continuación, haga clic en **Publicar**.

	![Publicar en modo de depuración](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

4. Una vez que ha finalizado la implementación y que el explorador abre la dirección URL de Azure de su aplicación web, cierre el explorador.

5. Para Visual Studio 2013: en el **Explorador de servidores**, haga clic con el botón secundario en la aplicación web y, a continuación, haga clic en **Asociar depurador**.

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

	El explorador se abre automáticamente en su página inicial en ejecución en Azure. Es posible que deba esperar unos 20 segundos mientras Azure configura el servidor para la depuración. Esta demora solo se produce la primera vez que ejecuta el modo de depuración en una aplicación web. Las veces posteriores dentro de las próximas 48 horas no habrá demora cuando vuelva a iniciar la depuración.

6. Para Visual Studio 2012 con Update 4 o superior: <a id="vs2012"></a>

	* En el Portal de Azure clásico, vaya a **Configurar > Configuración de la aplicación** para su aplicación web y desplácese hacia abajo hasta la sección **Depuración**.

	* Establezca **Depuración remota** en **Activado** y establezca **Versión remota de Visual Studio** en **2012**.

	* En el menú **Depurar** de Visual Studio, haga clic en **Asociar al proceso**.

	* En el cuadro **Calificador**, escriba la dirección URL de la aplicación web, sin el prefijo `http://`.

	* Seleccione **Mostrar los procesos de todos los usuarios**.

	* Cuando se le soliciten credenciales, escriba el nombre de usuario y la contraseña con permisos para implementar el contenido en la aplicación web. Para obtener estas credenciales, vaya a la pestaña Panel de la aplicación web en el portal clásico y haga clic en **Descargar el perfil de publicación**. Abra el archivo en un editor de texto y verá el nombre de usuario y la contraseña después de las primeras apariciones de **userName=** y **userPWD=**.

	* Cuando los procesos aparezcan en la tabla **Procesos disponibles**, seleccione **w3wp.exe** y haga clic en **Adjuntar**.

	* Abra un explorador en la dirección URL de la aplicación web.

	Es posible que deba esperar unos 20 segundos mientras Azure configura el servidor para la depuración. Esta demora solo se produce la primera vez que ejecuta el modo de depuración en una aplicación web. Las veces posteriores dentro de las próximas 48 horas no habrá demora cuando vuelva a iniciar la depuración.

6. Haga clic en **Acerca de** en el menú.

	Visual Studio se detiene en el punto de interrupción y el código se ejecuta en Azure, no en su equipo local.

7. Mantenga el puntero sobre la variable `currentTime` para ver el valor de tiempo.

	![Ver la variable en ejecución en modo de depuración en Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

	La hora que ve es la hora del servidor Azure, que puede estar en una zona horaria distinta a la de su equipo local.

8. Escriba un valor nuevo para la variable `currentTime`, como "Ahora en ejecución en Azure".

5. Presione F5 para continuar la ejecución.

	La página About que se ejecuta en Azure muestra el valor nuevo que escribió en la variable currentTime.

	![Página About con el valor nuevo](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a> WebJobs de depuración remota

En esta sección se muestra cómo depurar de forma remota mediante el proyecto y la aplicación web que creó en [Introducción al SDK de WebJobs de Azure](websites-dotnet-webjobs-sdk.md). Las características mostradas en esta sección solo están disponibles en Visual Studio 2013 con Update 4 o superior. La depuración remota solo funciona con WebJobs continuos. Los WebJobs bajo demanda y programados no admiten la depuración.

1. Abra el proyecto web que creó en [Introducción al SDK de WebJobs de Azure][GetStartedWJ].

1. En el proyecto ContosoAdsWebJob, abra *Functions.cs*.

2. [Establezca un punto de interrupción](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) en la primera instrucción del método `GnerateThumbnail`.

	![Establecimiento de punto de interrupción](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

1. En el **Explorador de soluciones**, haga clic en el proyecto web (no el proyecto WebJob) y haga clic en **Publicar**.

2. En la lista desplegable **Perfil**, seleccione el mismo perfil que utilizó en [Introducción al SDK de WebJobs de Azure](websites-dotnet-webjobs-sdk.md).

3. Haga clic en la pestaña **Configuración**, cambie la **Configuración** a **Depurar** y, a continuación, haga clic en **Publicar**.

	Visual Studio implementará los proyectos web y de WebJobs, y el explorador se abrirá en la dirección URL de Azure de su aplicación web.

5. En el **Explorador de servidores**, expanda **Azure > Servicio de aplicaciones > su grupo de recursos > su aplicación web > WebJobs > Continuo** y, a continuación, haga clic con el botón secundario en **ContosoAdsWebJob**.

7. Haga clic en **Adjuntar el depurador**.

	![Attach debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

	El explorador se abre automáticamente en su página inicial en ejecución en Azure. Es posible que deba esperar unos 20 segundos mientras Azure configura el servidor para la depuración. Esta demora solo se produce la primera vez que ejecuta el modo de depuración en una aplicación web. La siguiente vez que adjunte el depurador no se producirá un retraso, si lo hace en 48 horas.

6. En el explorador web que se abre en la página principal de Contoso Ads, cree un nuevo anuncio.

	Al crear un anuncio, se crea un mensaje de la cola, que WebJob recoge y procesa. Cuando el SDK de WebJobs llama a la función para procesar el mensaje de la cola, el código alcanzará el punto de interrupción.

7. Cuando el depurador se interrumpe en el punto de interrupción, puede examinar y cambiar los valores de las variables mientras está ejecutando el programa en la nube. En la siguiente ilustración, el depurador muestra el contenido del objeto blobInfo que se pasó al método GenerateThumbnail.

	![Objeto blobInfo en el depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)
 
5. Presione F5 para continuar la ejecución.

	El método GenerateThumbnail finaliza la creación de la miniatura.

6. En el explorador, actualice la página de índice y vea la miniatura.

6. En Visual Studio, presione MAYÚS + F5 para detener la depuración.

7. En el **Explorador de servidores**, haga clic en el nodo ContosoAdsWebJob y haga clic en **Ver panel**.

8. Inicie sesión con sus credenciales de Azure y, a continuación, haga clic en el nombre de WebJob para ir a la página de su WebJob.

	![Clic en ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

	El panel muestra que la función GenerateThumbnail se ha ejecutado recientemente.

	(La próxima vez que haga clic en **Ver panel**, no tendrá que iniciar sesión y el explorador irá directamente a la página de su WebJob).

9. Haga clic en el nombre de función para ver detalles acerca de la ejecución de la función.

	![Detalles de la función](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Si la función [escribió registros](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs), puede hacer clic en **ToggleOutput** para verlos.

## Notas acerca de la depuración remota

* No se recomienda ejecutar el modo de depuración en producción. Si su aplicación web de producción no está escalada horizontalmente en varias instancias de servidor, la depuración impedirá que el servidor web responda a otras solicitudes. Si tiene varias instancias de servidor web, cuando se asocie al depurador recibirá una instancia aleatoria y no tendrá forma de asegurarse de que las solicitudes posteriores del explorador se dirijan a esa instancia. Además, normalmente no implementará una compilación de depuración en producción, y las optimizaciones de compilador para compilaciones de versión podrían hacer imposible mostrar lo que ocurre línea a línea en su código fuente. Para solucionar problemas de producción, su mejor recurso son los registros de servidor web y de seguimiento de la aplicación.

* Evite detenciones prolongadas en los puntos de interrupción durante la depuración remota. Azure considera un proceso detenido durante más de unos minutos como un proceso sin respuesta y lo apaga.

* Mientras realiza la depuración, el servidor envía datos a Visual Studio, lo que podría afectar a los cargos de ancho de banda. Para obtener información acerca de las tarifas de ancho de banda, consulte [Precios de Azure](/pricing/calculator/).

* Asegúrese de que el atributo `debug` del elemento `compilation` en el archivo *Web.config* esté definido como verdadero. Este es el valor predeterminado cuando publica una configuración de compilación de depuración.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>

* Si encuentra que el depurador no entra en el código que desea depurar, es posible que deba cambiar la configuración de Solo mi código. Para obtener más información, consulte [Restricción de la ejecución paso a paso a Solo mi código](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).

* Cuando habilita la característica de depuración remota, se inicia un temporizador en el servidor y 48 horas después la característica se desactiva automáticamente. Este límite de 48 horas es por motivos de seguridad y rendimiento. Puede activar fácilmente la característica las veces que lo desee. Recomendamos dejarla deshabilitada cuando no esté realizando activamente una depuración.

* Puede asociar manualmente el depurador a cualquier proceso, no solo al proceso de la aplicación web (w3wp.exe). Para obtener más información acerca de cómo utilizar el modo de depuración en Visual Studio, consulte [Depuración en Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

## <a name="logsoverview"></a>Información general de registros de diagnóstico

Una aplicación de ASP.NET que se ejecuta en una aplicación web de Azure puede crear las siguientes clases de registros:

* **Registros de seguimiento de la aplicación**<br/>  
La aplicación crea estos registros mediante la llamada a métodos de la clase [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx).
* **Registros de servidor web**<br/>  
El servidor web crea una entrada de registro para cada solicitud HTTP a la aplicación web.
* **Registros de mensajes de error detallados**<br/> El servidor web crea una página HTML con algo de información adicional para solicitudes HTTP con error (las que generan el código de error 400 o superior). 
* **Registros de seguimiento de solicitudes con error**<br/> El servidor web crea un archivo XML con información de seguimiento detallada para solicitudes HTTP con error. El servidor web también proporciona un archivo XSL para dar formato al XML en un explorador.
  
El registro afecta el rendimiento de la aplicación web, por lo que Azure le ofrece la posibilidad de habilitar o deshabilitar cada tipo de registro según sea necesario. En el caso de registros de aplicaciones, puede especificar que solo se escriban los registros por encima de un determinado nivel de gravedad. Cuando crea una aplicación web, todos los registros están deshabilitados de manera predeterminada.

Los registros se escriben en archivos en una carpeta *LogFiles* en el sistema de archivos del sitio web y son accesibles a través de FTP. Los registros de servidor web y registros de aplicaciones también se pueden escribir en una cuenta de almacenamiento de Azure. Puede conservar un volumen mayor de registros en una cuenta de almacenamiento de lo que es posible en el sistema de archivos. Cuando utiliza el sistema de archivos, tiene un límite máximo de 100 megabytes de registros. (Los registros del sistema de archivos solo sirven para la conservación a corto plazo. Azure elimina los archivos de registro antiguos para dejar espacio para los nuevos una vez que se alcanza el límite).

## <a name="apptracelogs"></a>Creación y visualización de registros de seguimiento de aplicación

En esta sección realizará las siguientes tareas:

* Agregue instrucciones de seguimiento para el proyecto web que creó en [Introducción a Azure y ASP.NET][GetStarted].
* Ver los registros cuando ejecute localmente el proyecto.
* Ver los registros a medida que los genera la aplicación que se ejecuta en Azure. 

Para obtener información acerca de cómo crear registros de aplicaciones en WebJobs, consulte [Trabajo con el almacenamiento de cola de Azure mediante el SDK de WebJobs - Cómo escribir registros](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs). Las instrucciones siguientes para ver los registros y controlar cómo se almacenan en Azure se aplican también a los registros de aplicación creados por WebJobs.

### Adición de instrucciones de seguimiento a la aplicación

1. Abra *Controllers\HomeController.cs* y reemplace el contenido del archivo por el siguiente código para agregar instrucciones `Trace` y una instrucción `using` para `System.Diagnostics`:

		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.Diagnostics;
		using System.Linq;
		using System.Web;
		using System.Web.Configuration;
		using System.Web.Mvc;
		namespace MyExample.Controllers
		{
		    public class HomeController : Controller
		    {
		        public ActionResult Index()
		        {
		            Trace.WriteLine("Entering Index method");
		            ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
		            Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Leaving Index method");
		            return View();
		        }
		
		        public ActionResult About()
		        {
		            Trace.WriteLine("Entering About method");
		            ViewBag.Message = "Your app description page.";
		            Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
		            Trace.WriteLine("Leaving About method");
		            return View();
		        }
		
		        public ActionResult Contact()
		        {
		            Trace.WriteLine("Entering Contact method");
		            ViewBag.Message = "Your contact page.";
		            Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
		            Trace.WriteLine("Leaving Contact method");
		            return View();
		        }
		    }
		}
		
				
### Visualización del resultado de seguimiento localmente

3. Presione F5 para ejecutar la aplicación en modo de depuración.

	La escucha de seguimiento predeterminada escribe todo el resultado del seguimiento en la ventana **Resultados**, además del resultado de la depuración. La siguiente ilustración muestra el resultado de las instrucciones de seguimiento que agregó al método `Index`.

	![Ventana de seguimiento en depuración](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

	Los pasos siguientes muestran cómo ver el resultado del seguimiento en una página web, sin compilar en modo de depuración.

2. Abra el archivo Web.config de la aplicación (el que está ubicado en la carpeta del proyecto) y agregue un elemento `<system.diagnostics>` al final del archivo, justo antes del elemento `</configuration>` de cierre:

  		<system.diagnostics>
		    <trace>
		      <listeners>
		        <add name="WebPageTraceListener"
                    type="System.Web.WebPageTraceListener, 
                    System.Web, 
                    Version=4.0.0.0, 
                    Culture=neutral,
                    PublicKeyToken=b03f5f7f11d50a3a" />
		      </listeners>
		    </trace>
		  </system.diagnostics>

	`WebPageTraceListener` le permite ver los resultados de seguimiento si navega a `/trace.axd`.

3. Agregue un <a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">elemento de seguimiento</a> bajo `<system.web>` en el archivo Web.config, como en el siguiente ejemplo:

		<trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />

3. Presione CTRL+F5 para ejecutar la aplicación.

4. En la barra de direcciones de la ventana del explorador, agregue *trace.axd* a la dirección URL y, a continuación, presione Entrar (la dirección URL será similar a http://localhost:53370/trace.axd)).

5. En la página **Seguimiento de la aplicación**, haga clic en **Ver detalles** en la primera línea (no en la línea BrowserLink).

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

	Aparece la página **Detalles de la solicitud** y en la sección **Información de seguimiento**, podrá ver el resultado de las instrucciones de seguimiento que agregó al método `Index`.

	![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

	De manera predeterminada, `trace.axd` solo está disponible localmente. Si desea que esté disponible desde una aplicación remota, puede agregar `localOnly="false"` al elemento `trace` en el archivo *Web.config*, tal como aparece en el siguiente ejemplo:

		<trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

	Sin embargo, por lo general no se recomienda habilitar `trace.axd` en una aplicación web de producción por motivos de seguridad y en las siguientes secciones verá una manera más fácil de leer los registros de seguimiento en una aplicación web de Azure.

### Visualización del resultado de seguimiento en Azure

1. En el **Explorador de soluciones**, haga clic con el botón secundario en el proyecto web y, a continuación, en **Publicar**.

2. En el cuadro de diálogo **Publicación web**, haga clic en **Publicar**.

	Después de que Visual Studio publica su actualización, se abre una ventana del explorador en su página principal (suponiendo que no desactivó **Dirección URL de destino** en la pestaña **Conexión**).

3. En el **Explorador de servidores**, haga clic con el botón secundario en su aplicación web y seleccione **Ver registros de streaming**.

	![Ver registros de streaming en el menú contextual](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

	La ventana **Resultados** muestra que está conectado al servicio de streaming de registros y agrega una línea de notificación cada minuto que pasa sin un registro para mostrar.

	![Ver registros de streaming en el menú contextual](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)

4. En la ventana del explorador que muestra la página principal de su aplicación, haga clic en **Contact**.

	En unos pocos segundos, el resultado del seguimiento en el nivel de error que agregó al método `Contact` aparecerá en la ventana **Resultados**.

	![Seguimiento de errores en la ventana Output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

	Visual Studio muestra solo seguimientos en el nivel de errores porque es el valor predeterminado cuando habilita el servicio de supervisión de registros. Cuando crea una aplicación web de Azure, todos los registros están deshabilitados de manera predeterminada, tal como vio cuando abrió anteriormente la página de configuración:

	![Registro de aplicación desactivado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)


	Sin embargo, cuando selecciona **Ver registros de streaming**, Visual Studio cambia automáticamente **Registro de la aplicación (Sistema de archivos)** a **Error**, lo que significa que los registros de errores se notifican. Para ver todos los registros de seguimiento, puede cambiar esta configuración a **Detallado**. Cuando selecciona un nivel de gravedad inferior al error, también se notifican todos los registros para niveles de gravedad más altos. Por lo tanto, cuando selecciona detallado, también verá registros de errores, advertencias e información.

4. En el **Explorador de servidores**, haga clic con el botón secundario en la aplicación web y, a continuación, haga clic en **Ver configuración** tal como hizo anteriormente.

5. Cambie **Registro de la aplicación (Sistema de archivos)** a **Detallado** y haga clic en **Guardar**.
 
	![Definir el nivel de seguimiento como Verbose](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)

6. En la ventana del explorador que ahora muestra su página **Contacto**, haga clic en **Inicio**, en **Acerca** y, a continuación, haga clic en **Contacto**.

	En unos pocos segundos, la ventana **Resultados** mostrará todos los resultados del seguimiento.

	![Resultados detallados del seguimiento](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

	En esta sección aprendió a habilitar y deshabilitar el registro mediante la configuración de la aplicación web de Azure. También puede habilitar y deshabilitar escuchas de seguimiento si modifica el archivo Web.config. Sin embargo, modificar el archivo Web.config hace que el dominio de la aplicación se recicle, mientras que habilitar el registro a través de la aplicación web no provoca eso. Si el problema tarda mucho en reproducirse, o bien es intermitente, reciclar el dominio de la aplicación podría "arreglarlo" y forzarle a esperar hasta que vuelva a ocurrir. Permitir el diagnóstico en Azure no lo hace, por lo que puede comenzar inmediatamente a capturar información sobre el error.

### Características de la ventana de salida

La pestaña **Registros de Azure** de la ventana **Resultados** tiene varios botones y un cuadro de texto:

![Botones de la pestaña de registros](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Estos elementos realizan las siguientes funciones:

* Limpiar la ventana **Resultado**.
* Habilitar o deshabilitar el ajuste automático de línea.
* Iniciar o detener la supervisión de los registros.
* Especificar los registros para supervisar.
* Descargar registros.
* Filtrar los registros según una cadena de búsqueda o una expresión regular.
* Cierre la ventana **Resultados**.

Si especificó una cadena de búsqueda o una expresión regular, Visual Studio filtra la información de registro en el cliente. Esto significa que puede especificar los criterios después de que aparezcan los registros en la ventana **Resultados** y que puede cambiar esos criterios de filtrado sin tener que volver a generar los registros.

## <a name="webserverlogs"></a>Visualización de registros de servidor web

Los registros de servidor web registran toda la actividad HTTP para la aplicación web. Para verlos en la ventana **Resultados**, debe habilitarlos en la aplicación web e indicarle a Visual Studio que desea supervisarlos.

1. En la pestaña **Configuración de aplicaciones web de Azure** que abrió desde el **Explorador de servidores**, cambie el registro de servidor web a **Activado** y haga clic en **Guardar**.

	![Habilitar el registro de servidor web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)

2. En la ventana **Resultados**, haga clic en el botón **Especificar los registros de Azure para supervisar**.
	
	![Especificar los registros de Azure para supervisar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)

3. En el cuadro de diálogo **Opciones de registro de Azure**, seleccione **Registros de servidor web** y haga clic en **Aceptar**.

	![Supervisar los registros de servidor web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)

4. En la ventana del explorador que muestra la aplicación web, haga clic en **Inicio**, en **Acerca de** y, a continuación, en **Contacto**.

	Los registros de aplicación generalmente aparecen primero y luego aparecen los registros de servidor web. Es posible que deba esperar un poco hasta que aparezcan.

	![Registros de servidor web en la ventana Output](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)


De manera predeterminada, cuando habilita por primera vez los registros de servidor web mediante el uso de Visual Studio, Azure escribe los registros en el sistema de archivos. De manera alternativa, puede utilizar el portal clásico para especificar que los registros de servidor web se deben escribir en un contenedor de blob en una cuenta de almacenamiento. Para obtener más información, consulte la sección sobre **diagnóstico del sitio** en [Configuración de Sitios web](/manage/services/web-sites/how-to-configure-websites/#howtochangeconfig).

Si utiliza el portal clásico para habilitar el registro de servidor web para una cuenta de almacenamiento de Azure y luego deshabilitar el registro en Visual Studio, cuando vuelva a habilitar el registro en Visual Studio, se restaurará la configuración de su cuenta de almacenamiento.

## <a name="detailederrorlogs"></a>Visualización de registros de mensajes de error detallados

Los registros de error detallados proporcionan información adicional acerca de las solicitudes HTTP que generaron códigos de respuesta con error (400 o superiores). Para verlos en la ventana **Resultados**, debe habilitarlos en la aplicación web e indicarle a Visual Studio que desea supervisarlos.

1. En la pestaña **Configuración de aplicaciones web de Azure que abrió desde** el **Explorador de servidores**, cambie **Mensajes de error detallados** a **Activado** y haga clic en **Guardar**.

	![Habilitar los mensajes de error detallados](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. En la ventana **Resultados**, haga clic en el botón **Especificar los registros de Azure para supervisar**.

3. En el cuadro de diálogo **Opciones de registro de Azure**, haga clic en **Todos los registros** y, a continuación, en **Aceptar**.

	![Supervisar todos los registros](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. En la barra de direcciones de la ventana del explorador, agregue un carácter adicional a la dirección URL para generar un error 404 (por ejemplo, `http://localhost:53370/Home/Contactx`) y presione Entrar.

	Después de varios segundos, el registro detallado del error aparecerá en la ventana **Resultados** de Visual Studio.

	![Registro de error detallado en la ventana Resultados](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

	Presione Ctrl y haga clic en el vínculo para ver el resultado del registro formateado en un explorador:

	![Registro de error detallado en la ventana del explorador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Descarga de registros del sistema de archivos

Todos los registros que se pueden supervisar en la ventana **Resultados** también pueden descargarse como un archivo *.zip*

1. En la ventana **Resultados**, haga clic en **Descargar registros de streaming**.

	![Botones de la pestaña de registros](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

	El Explorador de archivos abrirá la carpeta *Descargas* con el archivo descargado seleccionado.

	![Archivo descargado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)

2. Extraiga el archivo *.zip* y verá la siguiente estructura de carpetas:

	![Archivo descargado](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

	* Los registros de seguimiento de aplicación están en archivos *.txt* en la carpeta *LogFiles\Application*.
	* Los registros de servidor web están en archivos *.log* en la carpeta *LogFiles\http\RawLogs*. Puede utilizar una herramienta como [Analizador del registro](http://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) para ver y manipular estos archivos.
	* Los registros de mensajes de error detallados están en los archivos *.html* de la carpeta *LogFiles\DetailedErrors*.

	(La carpeta de *implementaciones* es para archivos creados por la publicación del control del código fuente; no tiene ninguna relación con la publicación en Visual Studio. La carpeta *Git* es para seguimientos relacionados con la publicación de control de origen y el servicio de transmisión de archivos de registro).

## <a name="storagelogs"></a>Visualización de registros de almacenamiento

Los registros de seguimiento de la aplicación también se pueden enviar a una cuenta de almacenamiento de Azure y puede verlos en Visual Studio. Para ello, creará una cuenta de almacenamiento, habilitará los registros de almacenamiento en el portal clásico y los verá en la pestaña **Registros** de la ventana **Aplicación web de Azure**.

Puede enviar registros a todos o a cualquiera de estos tres destinos:

* El sistema de archivos.
* Tablas de cuentas de almacenamiento.
* Blobs de cuentas de almacenamiento.

Puede especificar un nivel de gravedad distinto para cada destino.

Las tablas facilitan la visualización de los detalles de registros en línea y son compatibles con streaming; puede consultar los registros en las tablas y ver nuevos registros a medida que se van creando. Los blobs facilitan la descarga de registros en archivos y su análisis con HDInsight, porque HDInsight sabe cómo trabajar con el almacenamiento de blobs. Para obtener más información, consulte **Hadoop y MapReduce** en [Opciones de almacenamiento de datos (Creación de aplicaciones reales con Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

Actualmente tiene los registros de sistema de archivos definidos en el nivel detallado; los pasos siguientes le guiarán por la configuración de registros en el nivel de información para ir a tablas de cuentas de almacenamiento. El nivel de información se refiere a que se mostrarán todos los registros creados al llamar a `Trace.TraceInformation`, `Trace.TraceWarning` y `Trace.TraceError`, pero no los creados al llamar a `Trace.WriteLine`.

Las cuentas de almacenamiento ofrecen más almacenamiento y retención más prolongada de registros en comparación con el sistema de archivos. Otra ventaja de enviar los registros de seguimiento de aplicaciones al almacenamiento es que obtiene información adicional con cada registro que no obtiene de los registros del sistema de archivos.

5. Haga clic con el botón secundario en **Almacenamiento** en el nodo de Azure y, a continuación, haga clic en **Crear cuenta de almacenamiento**.

![Crear una cuenta de almacenamiento](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

3. En el cuadro de diálogo **Crear cuenta de almacenamiento**, escriba un nombre para la cuenta de almacenamiento. 

	El nombre debe ser único (ninguna otra cuenta de almacenamiento de Azure puede tener el mismo nombre). Si el nombre especificado ya está en uso, tendrá la oportunidad de cambiarlo.

	La dirección URL para tener acceso a su cuenta de almacenamiento será *{name}*.core.windows.net.

5. Establezca la lista desplegable **Grupo de afinidad o región** en la región más cercana a usted.

	Esta configuración especifica el centro de datos de Azure que va a almacenar la cuenta de almacenamiento. En este tutorial, la opción que elija no tendrá mucha repercusión, pero en un entorno de producción se recomienda que la aplicación web y la cuenta de almacenamiento estén en la misma región a fin de minimizar la latencia y los cargos por concepto de salida de los datos. La aplicación web (que creará posteriormente) debe estar lo más próxima posible a los exploradores que obtengan acceso a su aplicación con el fin de minimizar la latencia.

6. Establezca la lista desplegable **Replicación** en **Localmente redundante**.

	Cuando se habilita la replicación geográfica para una cuenta de almacenamiento, el contenido almacenado se replica en un centro de datos secundario para habilitar la conmutación por error en caso de que se produzca un desastre importante en la ubicación principal. La replicación geográfica puede suponer costes adicionales. Lo normal es que no quiera pagar por el servicio de replicación geográfica para las cuentas de prueba y desarrollo. Para obtener más información, consulte [Creación, administración o eliminación de una cuenta de almacenamiento](../storage-create-storage-account/#replication-options).

5. Haga clic en **Crear**.

	![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)

1. En la ventana **Aplicación web de Azure** de Visual Studio, haga clic en la pestaña **Registros** y a continuación, haga clic en **Configurar registro en el Portal de administración**.

    <!-- todo:screenshot of new portal if the VS page link goes to new portal -->
	![registro](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

	Esto abre la pestaña **Configurar** en el portal clásico de su aplicación web.

2. En la pestaña **Configurar** del portal clásico, desplácese hacia abajo hasta la sección de diagnóstico de aplicaciones y cambie **Registro de la aplicación (Almacenamiento de tablas)** a **Activado**.

3. Cambie **Nivel de registro** a **Información**.

4. Haga clic en **Administrar almacenamiento de tablas**.

	![Hacer clic en Manage Table Storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

	En el cuadro **Administrar almacenamiento de tablas para diagnósticos de la aplicación**, puede elegir su cuenta de almacenamiento si tiene más de una. Puede crear una tabla o usar una existente.

	![Administrar el almacenamiento de tablas](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)

6. En el cuadro **Administrar almacenamiento de tablas para diagnósticos de la aplicación**, haga clic en la marca de verificación junto al cuadro.

6. En la pestaña **Configurar** del portal clásico, haga clic en **Guardar**.

7. En la ventana del explorador que muestra la aplicación web, haga clic en **Inicio**, en **Acerca de** y, a continuación, en **Contacto**.

	La información de registro generada por la navegación en estas páginas web se escribirá en la cuenta de almacenamiento.

8. En la pestaña **Registros** de la ventana **Aplicación web de Azure** en Visual Studio, haga clic en **Actualizar** en **Resumen de diagnóstico**.

	![Hacer clic en Actualizar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

	De manera predeterminada, la sección **Resumen de diagnóstico** muestra registros de los últimos 15 minutos. Puede cambiar el período para ver más registros.

	(Si recibe un error de "no se encontró tabla", compruebe que navegó a las páginas que realizan el seguimiento después de haber habilitado **Registro de la aplicación (Almacenamiento)** y después de haber hecho clic en **Guardar**).

	![Registros de almacenamiento](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

	Tenga en cuenta que en esta vista puede ver el **identificador del proceso** y el **identificador del subproceso** para cada registro, los cuales no vería en los registros del sistema de archivos. Si desea ver campos adicionales, puede ver directamente la tabla de almacenamiento de Azure.

8. Haga clic en **Ver todos los registros de aplicación**.

	La tabla de registros de seguimiento aparece en el visor de tablas de almacenamiento de Azure.
   
	(Si recibe un error de "la secuencia no contiene elementos", abra el **Explorador de servidores**, expanda el nodo para la cuenta de almacenamiento en el nodo **Azure**, haga clic con el botón secundario en **Tabla** y, a continuación, haga clic en **Actualizar**).

	![Registros de almacenamiento en vista de tabla](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

	Esta vista muestra campos adicionales que no están disponibles en otras vistas. Esta vista también le permite filtrar registros mediante una interfaz de usuario especial del Generador de consultas para construir una consulta. Para obtener más información, consulte Trabajo con recursos de tabla - Filtrado de entidades en [Exploración de recursos de almacenamiento con el Explorador de servidores](http://msdn.microsoft.com/library/ff683677.aspx).

7. Para ver los detalles de una sola fila, haga doble clic en una de ellas.

	![Tabla de seguimiento en el Explorador de servidores](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

## <a name="failedrequestlogs"></a>Visualización de registros de seguimiento de solicitudes con error

Los registros de seguimiento de solicitudes con error son útiles cuando necesita comprender los detalles de cómo IIS maneja una solicitud HTTP, en situaciones tales como problemas de autenticación o reconfiguración de URL.

Aplicaciones web de Azure usan la misma funcionalidad de seguimiento de solicitudes con error que ha estado disponible con IIS 7.0 y posterior. Sin embargo, no tiene acceso a la configuración de IIS que determina los errores que se registran. Cuando habilita el seguimiento de solicitudes con error, se capturan todos los errores.

Puede habilitar el seguimiento de solicitudes con error a través de Visual Studio, pero no puede verlas ahí. Estos registros son archivos XML. El servicio de registro de streaming solo supervisa archivos que se pueden leer en modo de texto sin formato: *.txt*, *.html* y *.log*.

Puede ver los registros de seguimiento de solicitudes en un explorador directamente vía FTP o de manera local después de utilizar una herramienta FTP para descargarlos en el equipo local. En esta sección los verá directamente en un explorador.

1. En la pestaña **Configuración** de la ventana **Aplicación web de Azure** que abrió desde el **Explorador de servidores**, cambie **Seguimiento de solicitudes con error** a **Activado** y haga clic en **Guardar**.

	![Habilitar el seguimiento de solicitudes con error](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)

4. En la barra de direcciones de la ventana del explorador que muestra la aplicación web, agregue un carácter adicional a la dirección URL y haga clic en Entrar para generar un error 404.

	Esto hará que se cree un registro de seguimiento de solicitud con error; los pasos siguientes muestran cómo ver o descargar el registro.

2. En Visual Studio, en la pestaña **Configuración** de la ventana **Aplicación web de Azure**, haga clic en **Abrir en el Portal de administración**.

3. En la hoja del Portal de Azure (https://portal.azure.com) para la aplicación web, haga clic en **Configuración > Credenciales de implementación** y luego especifique un nuevo nombre de usuario y contraseña.

	![Nuevo nombre de usuario y contraseña de FTP](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

	Cuando **inicie sesión**, debe utilizar el nombre de usuario completo, con el nombre de la aplicación como prefijo. Por ejemplo, si escribe "myid" como nombre de usuario y el sitio es "myexample", inicie sesión como "myexample\\myid".

5. En una ventana nueva del explorador, vaya a la dirección URL que aparece en **Nombre de host de FTP** o **Nombre de host de FTPS** en la hoja **Aplicación web** de la aplicación web.

6. Inicie sesión con las credenciales de FTP que creó anteriormente (el nombre de usuario debe incluir como prefijo el nombre de la aplicación).

	El explorador muestra la carpeta raíz de la aplicación web.

6. Abra la carpeta *LogFiles*.

	![Abrir la carpeta LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

7. Abra la carpeta llamada W3SVC más un valor numérico.

	![Abrir la carpeta W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

	La carpeta contiene archivos XML de todo error que se haya registrado después de haber habilitado el seguimiento de solicitudes con error, además de un archivo XSL que un explorador puede utilizar para dar formato al XML.

	![Carpeta W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

8. Haga clic en el archivo XML de la solicitud con error para la que desea ver información de seguimiento.

	La siguiente ilustración muestra parte de la información de seguimiento de un error de muestra.

	![Seguimiento de solicitudes con error en un explorador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)


## <a name="nextsteps"></a>Pasos siguientes

Ha visto cómo Visual Studio facilita la visualización de registros creados por una aplicación web de Azure. Las secciones siguientes proporcionan vínculos a más recursos sobre temas relacionados:

* Solución de problemas de las aplicaciones web de Azure
* Depuración en Visual Studio 
* Depuración remota en Azure
* Seguimiento en aplicaciones de ASP.NET
* Análisis de registros de servidor web
* Análisis de registros de seguimiento de solicitudes con error
* Depuración de Servicios en la nube

### Solución de problemas de las aplicaciones web de Azure

Para obtener más información sobre cómo solucionar problemas de aplicaciones web en el Servicio de aplicaciones de Azure, consulte los siguientes recursos:

* [Supervisión de aplicaciones web](/manage/services/web-sites/how-to-monitor-websites/)
* [Investigación de fugas de memoria en Aplicaciones web de Azure con Visual Studio 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Artículo del blog ALM de Microsoft sobre las características de Visual Studio para el análisis de problemas de memoria administrada.
* [Herramientas en línea de Aplicaciones web de Azure que debe conocer](/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Publicación en el blog de Amit Apple.

Si desea obtener ayuda para una pregunta específica sobre la solución de problemas, comience una conversación en alguno de los siguientes foros:

* [El foro sobre Azure en el sitio de ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [El foro de Azure en MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### Depuración en Visual Studio 

Para obtener más información acerca del uso del modo de depuración en Visual Studio, consulte el tema [Depuración en Visual Studio de MSDN](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) y [Sugerencias de depuración de Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### Depuración remota en Azure

Para obtener más información sobre la depuración remota para aplicaciones web de Azure y WebJobs, consulte los siguientes recursos:

* [Introducción a la depuración remota de aplicaciones web de Servicios de aplicaciones de Azure](/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Introducción a la depuración remota en Aplicaciones web de Servicios de aplicaciones de Azure, 2ª parte: detalles de la depuración remota](/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introducción a la depuración remota en Aplicaciones web de Servicios de aplicaciones de Azure, 3ª parte: entorno de varias instancias y GIT](/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Depuración de WebJobs](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Si su aplicación web utiliza una API web de Azure o el back-end de Servicios móviles y necesita depurarlo, consulte [Depuración de back-end de .NET en Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### Seguimiento en aplicaciones de ASP.NET

No hay introducciones rigurosas ni actualizadas al seguimiento de ASP.NET disponibles en Internet. Lo mejor que puede hacer es comenzar con materiales de introducción antiguos escritos para Web Forms, porque MVC todavía no existía, y complementarlos con las publicaciones más recientes en blogs que se centran en temas específicos. Algunos buenos sitios para comenzar son los siguientes recursos:

* [Supervisión y telemetría (crear aplicaciones en la nube para el mundo real con Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br> Capítulo de un libro electrónico con recomendaciones para realizar seguimiento en aplicaciones de la nube de Azure.
* [Seguimiento de ASP.NET](http://msdn.microsoft.com/library/ms972204.aspx)<br/> Un recurso antiguo, pero siempre útil, con una introducción básica al tema.
* [Agentes de escucha de seguimiento](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>  
Información acerca de las escuchas de seguimiento, pero no menciona [WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Tutorial: Integración del seguimiento en ASP.NET con las técnicas de seguimiento de System.Diagnostics](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>  
También antiguo, pero incluye cierta información adicional que el artículo de introducción no incluye.
* [Seguimiento en vistas Razor de ASP.NET MVC](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/> Además del seguimiento en vistas de Razor, la publicación también explica cómo crear un filtro de error para registrar todas las excepciones no manejadas en una aplicación MVC. Si desea obtener información sobre cómo registrar todas las excepciones no controladas en una aplicación de Web Forms, consulte el ejemplo Global.asax en [Ejemplo completo de controladores de errores en MSDN](http://msdn.microsoft.com/library/bb397417.aspx). Ya sea en MVC o en Web Forms, si desea registrar ciertas excepciones, pero permitir que la gestión del marco predeterminado las afecte, puede volver a intentarlo como en el siguiente ejemplo:

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        } 

* [Registro de seguimiento de diagnóstico de streaming desde la línea de comandos de Azure (con Glimpse)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/> Uso de la línea de comandos para hacer lo que muestra este tutorial en Visual Studio. [Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) es una herramienta de depuración de aplicaciones ASP.NET.
* [Uso de diagnósticos y registro de Aplicaciones web - con David Ebbo](/documentation/videos/azure-web-site-logging-and-diagnostics/) y [Registros de streaming desde Aplicaciones web - con David Ebbo](/documentation/videos/log-streaming-with-azure-web-sites/)<br> Vídeos de Scott Hanselman y David Ebbo.

Para el registro de errores, una alternativa a escribir su propio código de seguimiento es utilizar un marco de registro de código abierto, como [ELMAH](http://nuget.org/packages/elmah/). Para obtener más información, consulte [Publicaciones de blog de Scott Hanselman sobre ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Además, tenga en cuenta que no tiene que utilizar el seguimiento de System.Diagnostics o ASP.NET si desea obtener registros de streaming de Azure. El servicio de registros de streaming de la aplicación web de Azure transmitirá todo archivo *.txt*, *.html* o *.log* que encuentre en la carpeta *LogFiles*. Por lo tanto, podría crear su propio sistema de registro que escriba en el sistema de archivos de la aplicación web y su archivo se transmitirá y descargará automáticamente. Todo lo que tiene que hacer es escribir código de aplicación que cree archivos en la carpeta *d:\home\logfiles*.

### Análisis de registros de servidor web

Para obtener más información acerca del análisis de registros de servidor web, consulte los siguientes recursos:

* [LogParser](http://www.microsoft.com/download/details.aspx?id=24659)<br/> Una herramienta para visualizar datos en registros de servidor web (archivos *.log*).
* [Solución de problemas de IIS o de errores de aplicación mediante LogParser ](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>  
Una introducción a la herramienta de analizador del registro que puede utilizar para analizar registros de servidor web.
* [Publicaciones en el blog de Robert McMurray sobre el uso de LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [El código de estado HTTP en IIS 7.0, IIS 7.5 y IIS 8.0](http://support.microsoft.com/kb/943891)

### Análisis de registros de seguimiento de solicitudes con error

El sitio web de Microsoft TechNet incluye una sección llamada [Uso de seguimiento de solicitudes erróneas](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) que puede ser muy práctica para comprender cómo utilizar estos registros. Sin embargo, esta documentación se centra principalmente en la configuración del seguimiento de solicitudes con error en IIS, algo que no puede hacer en Aplicaciones web Azure.

### Depuración de Servicios en la nube

Si desea depurar un servicio en la nube de Azure en lugar de una aplicación web, consulte [Depuración de servicio en la nube](http://msdn.microsoft.com/library/windowsazure/ee405479.aspx).

## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714).
* Para obtener una guía del cambio del portal de Azure al portal de vista previa de Azure, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715).

[GetStarted]: web-sites-dotnet-get-started.md
[GetStartedWJ]: websites-dotnet-webjobs-sdk.md
 

<!---HONumber=AcomDC_0114_2016-->