<properties 
	pageTitle="¿Qué es el SDK de Azure para .NET?" 
	description="Obtenga información acerca de qué se incluye en el SDK de .NET de Azure." 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos" 
	services=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# ¿Qué es el SDK de Azure para .NET?

## Información general

El SDK de Azure para .NET es un conjunto de herramientas de Visual Studio, herramientas de línea de comandos, binarios en tiempo de ejecución y bibliotecas de cliente que ayudan a desarrollar, probar e implementar aplicaciones que se ejecutan en Azure. En este artículo se detalla lo que se obtiene al instalar el SDK. El SDK se puede descargar en la [página de descargas de Azure](/downloads/).

El SDK de Azure para .NET también consta de [bibliotecas de cliente para el consumo de servicios de Azure](http://go.microsoft.com/fwlink/?LinkId=510472). Estas bibliotecas se instalan por separado usando NuGet.

##<a id="included"></a>Lo que se incluye en el SDK de Azure para .NET

El SDK de Azure para .NET instala los productos siguientes:

- [Visual Studio Express para Web](#vwd)
- [Microsoft ASP.NET y herramientas web para Visual Studio](#wte)
- [Herramientas de Microsoft Azure para Microsoft Visual Studio](#tools)
- [Herramientas de creación de Microsoft Azure](#auth)
- [Emulador de Microsoft Azure](#emulator)
- [Emulador de almacenamiento de Microsoft Azure](#stgemulator)
- [Herramientas de almacenamiento de Microsoft Azure](#stgtools)
- [Bibliotecas para .NET de Microsoft Azure](#libraries)
- [¿Qué es el SDK de Azure para .NET?](#hdinsight)
- [SDK de Aplicaciones móviles de Microsoft Azure V1.0](#mobile)
- [Microsoft Azure PowerShell](#ps)

###<a id="vwd"></a>Visual Studio Express para Web

En caso de no tener Visual Studio en el equipo, el SDK instalará [Visual Studio Express para Web](http://www.visualstudio.com/products/visual-studio-express-vs.aspx).
 
###<a id="wte"></a>Microsoft ASP.NET y herramientas web para Visual Studio

Esto permite trabajar con Sitios web Azure:

* [Publicar proyectos web en Sitios web Azure](app-service-web/web-sites-dotnet-get-started.md).
* [Publicar proyectos de aplicaciones de consola en WebJobs de Azure](app-service-web/websites-dotnet-deploy-webjobs.md).
* [Crear recursos de una Base de datos SQL y de un Sitio web de Azure durante la creación de un nuevo proyecto web o la publicación de un proyecto web](app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
* [Crear scripts de implementación de PowerShell durante la creación de nuevos sitios web](http://msdn.microsoft.com/library/dn642480.aspx).
* [Administrar y solucionar problemas de Sitios web Azure en el Explorador de servidores](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#sitemanagement).
* [Ejecutar en modo de depuración de forma remota para Sitios web y WebJobs](app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). 

>[AZURE.NOTE] No debe instalar el SDK de Azure para .NET para usar estas características; también se incluyen en las actualizaciones de Visual Studio.

###<a id="tools"></a>Herramientas de Microsoft Azure para Microsoft Visual Studio

Esto permite trabajar con recursos de Azure, principalmente Servicios en la nube y Máquinas virtuales:

* [Crear, abrir y publicar proyectos de servicio en la nube](cloud-services/cloud-services-dotnet-get-started.md).
* [Crear paquetes de implementación para proyectos de servicio en la nube](http://msdn.microsoft.com/library/ff683672.aspx).
* [Crear Máquinas virtuales de Azure durante la creación de nuevos proyectos web](virtual-machines/virtual-machines-dotnet-create-visual-studio-powershell.md).
* [Crear scripts de PowerShell durante la creación de nuevas máquinas virtuales](http://msdn.microsoft.com/library/dn642480.aspx).
* [Ver y administrar la configuración del proyecto de servicio en la nube en las ventanas de propiedades del proyecto de Visual Studio](http://msdn.microsoft.com/library/ee405486.aspx).
* Ver y administrar [servicios en la nube](http://msdn.microsoft.com/library/ff683675.aspx), [máquinas virtuales](http://msdn.microsoft.com/library/jj131259.aspx) y [Bus de servicio](http://msdn.microsoft.com/library/jj149828.aspx) en el Explorador de servidores. 
* [Ejecutar en modo de depuración de forma remota para servicios en la nube y máquinas virtuales](http://msdn.microsoft.com/library/ff683670.aspx).
* [Automatización del aprovisionamiento de recursos con proyectos de implementación de grupo de recursos de Azure](https://msdn.microsoft.com/library/dn872471.aspx)

###<a id="auth"></a>Herramientas de creación de Microsoft Azure

Aquí se incluye lo siguiente:

* La [herramienta de línea de comandos CSPack](http://msdn.microsoft.com/library/gg432988.aspx) para crear paquetes de implementación.
* La [herramienta de línea de comandos CSEncrypt](http://msdn.microsoft.com/library/hh404001.aspx) para cifrar contraseñas que se usan para acceder a instancias de rol de servicio en la nube a través de una conexión de Escritorio remoto.
* Archivos binarios en tiempo de ejecución que los proyectos de servicio en la nube requieren para la comunicación con su entorno en tiempo real y para el diagnóstico. Estos archivos binarios no están disponibles en los paquetes NuGet.

###<a id="emulator"></a>Emulador de Microsoft Azure

El [emulador de Azure](http://msdn.microsoft.com/library/dn339018.aspx) simula el entorno de servicio en la nube de modo que pueda probar proyectos de servicio en la nube localmente en su equipo antes de implementarlos en Azure.

###<a id="stgemulator"></a>Emulador de almacenamiento de Microsoft Azure

El [emulador de almacenamiento de Azure](http://msdn.microsoft.com/library/hh403989.aspx) usa una instancia de SQL Server y el sistema de archivos local para simular el almacenamiento de Azure (colas, tablas, blobs) de modo que pueda probarlo localmente.

###<a id="stgtools"></a>Herramientas de almacenamiento de Microsoft Azure

Instala [AzCopy](http://aka.ms/AzCopy), una herramienta de línea de comandos que se puede usar para transferir datos hacia una cuenta de Almacenamiento de Azure.

###<a id="libraries"></a>Bibliotecas de Microsoft Azure para .NET

Aquí se incluye lo siguiente:

* Paquetes NuGet para Almacenamiento de Azure, Bus de servicio y Almacenamiento en caché que se almacenan en su equipo de modo que Visual Studio pueda crear nuevos proyectos de servicio en la nube sin conexión.
* Un complemento de Visual Studio que permite la ejecución local de proyectos de [Caché en rol](http://msdn.microsoft.com/library/dn386103.aspx) en Visual Studio. 

###<a id="hdinsight"></a>Herramientas de HDInsight para Visual Studio y Microsoft Hive ODBC Driver

Las herramientas de HDInsight en el Explorador de servidores le permiten navegar por las bases de datos de Hive y cuentas de almacenamiento vinculadas para clústeres de HDInsight, crear tablas y crear y enviar consultas de Hive. Para obtener más información, consulte [Introducción al uso de las herramientas de Hadoop de HDInsight para Visual Studio](hdinsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

###<a id="mobile"></a>SDK de Aplicaciones móviles de Microsoft Azure V1.0

Herramientas para trabajar con [Aplicaciones móviles del Servicio de aplicaciones de Azure](app-service-mobile/app-service-mobile-value-prop.md).

###<a id="ps"></a>Microsoft Azure PowerShell

Azure PowerShell permite [automatizar la creación e implementación del entorno de Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

##<a id="notincluded"></a>Lo que no se incluye al instalar el SDK de Azure para .NET

Hay algunas cosas que no se incluyen al instalar el SDK y que posiblemente desee para el desarrollo de Azure. Estas son las más importantes:

* [Bibliotecas de cliente](http://go.microsoft.com/fwlink/?LinkId=510472). 

	El SDK de Azure incluye bibliotecas de cliente para consumir servicios de Azure, pero no todas se instalan al instalar el SDK. Si alguna aplicación necesita una biblioteca de cliente que el SDK no instala, puede obtenerla en [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Si su aplicación usa una biblioteca de cliente que el SDK no instala, es conveniente actualizarla con la versión actual en NuGet.org.

  	**Copias locales de bibliotecas de cliente.** El SDK de Azure para .NET copia en su equipo los paquetes NuGet para algunas bibliotecas de cliente de Azure como Almacenamiento, Bus de servicio y Almacenamiento en caché. Estas bibliotecas de cliente se incluyen automáticamente en nuevos proyectos de servicio en la nube, de modo que los paquetes NuGet permiten que Visual Studio cree proyectos incluso sin conexión a Internet. Las bibliotecas de cliente suelen actualizarse con mayor frecuencia que las nuevas versiones del SDK lanzadas, de modo que las bibliotecas de cliente de NuGet.org suelen ser más actuales que las que se obtienen con el SDK.

	**Plantillas de proyecto que incluyen bibliotecas de cliente.** Las plantillas de proyecto de [Servicio en la nube de Azure](cloud-services/cloud-services-dotnet-get-started.md) y [Servicio móvil de Azure](mobile-services/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard.md) son las únicas que incluyen automáticamente algunas bibliotecas de cliente. Para obtener otras bibliotecas o plantillas, instale los [paquetes NuGet de la biblioteca de cliente](http://go.microsoft.com/fwlink/?LinkId=510472) que necesite.

* [Plantillas de proyecto de Servicio móvil de Azure](mobile-services/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard.md).

	Las plantillas de Servicio móvil solo están disponibles en Visual Studio 2013 Update 2 y posterior. No están disponibles en Visual Studio 2012 o versiones anteriores, ni tampoco en Visual Studio 2013 Update 1 o anterior, incluso si instala el SDK de Azure para .NET.

##<a id="faq"></a>Preguntas más frecuentes

- [Muchas características de Azure ya están en Visual Studio. ¿Es necesario instalar el SDK de Azure para .NET?](#azinvs)
- [Deseo una biblioteca de cliente. ¿Es necesario instalar el SDK de Azure para .NET para obtenerla?](#clientlib)
- [¿Dónde se pueden encontrar versiones anteriores del SDK de Azure para .NET?](#olderversions)
- [¿Cuál es la directiva de ciclo de vida de las versiones del SDK de Azure para .NET?](#lifecycle)
- [¿Con qué versiones del SO invitado es compatible el SDK de Azure para .NET?](#guestos)
- [¿Cómo se desinstala el SDK de Azure para .NET?](#uninstall)

###<a id="azinvs"></a>Muchas características de Azure ya están en Visual Studio. ¿Es necesario instalar el SDK de Azure para .NET?

Es conveniente instalar el SDK si desea desarrollar Azure usando las herramientas más recientes. Si prefiere no instalar el SDK, puede hacerlo si se cumplen las condiciones siguientes:

* Ha instalado la [actualización de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5) más reciente.
* Va a desarrollar solo Sitios web Azure o Servicios móviles, no Servicios en la nube ni Máquinas virtuales.
* Su aplicación no usa Almacenamiento o lo usa pero no necesita el emulador de almacenamiento ni la herramienta AzCopy.

###<a id="clientlib"></a>Deseo una biblioteca de cliente. ¿Es necesario instalar el SDK de Azure para .NET para obtenerla?

El SDK solo instala bibliotecas de cliente, de modo que puede crear proyectos de servicio en la nube incluso sin conexión a Internet. Las bibliotecas de cliente actuales están disponibles en los paquetes NuGet en [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472). Para obtener más información, consulte [Lo que no se incluye al instalar el SDK de Azure para .NET](#notincluded) en este mismo documento.

###<a id="olderversions"></a>¿Dónde se pueden encontrar versiones anteriores del SDK de Azure para .NET?

Para obtener versiones anteriores, consulte la página de descargas del [SDK de Azure para .NET](/downloads/archive-net-downloads/).

###<a id="lifecycle"></a>¿Cuál es la directiva de ciclo de vida de las versiones del SDK de Azure para .NET?

Consulte [Directiva del ciclo de vida de soporte técnico de Servicios en la nube de Microsoft Azure](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq).

###<a id="guestos"></a>¿Con qué versiones del SO invitado es compatible el SDK de Azure para .NET?

Consulte las [versiones del SO invitado de Azure y la matriz de compatibilidad del SDK](http://msdn.microsoft.com/library/ee924680.aspx).

###<a id="uninstall"></a>¿Cómo se desinstala el SDK de Azure para .NET?

Cada elemento que se muestra en este artículo en [Lo que se incluye en el SDK de Azure para .NET](#included) es un programa independiente de la lista de programas instalados que aparece en el Panel de control de Windows **Programas y características**. No hay forma de desinstalarlos en grupo; es preciso desinstalar cada programa individualmente.

Si el SDK de Azure para .NET está instalado y se instala una nueva versión, habitualmente no es preciso desinstalar el anterior. En la mayoría de los casos, la instalación del SDK actualiza un programa existente, en lugar de agregar uno nuevo y dejar el anterior.

Sin embargo, si desea quitar remanentes innecesarios de la versión anterior, desinstale sólo aquellos que especifiquen el número de la versión anterior, y solo si está presente una versión más reciente del mismo programa. Por ejemplo, después de realizar la actualización de la versión 2.5 a la 2.6 puede ver ambas versiones de "Herramientas de Microsoft Azure para Microsoft Visual Studio 2013" y puede desinstalar la 2.5. Sin embargo, sólo puede ver la versión 2.5 de "Herramientas de creación de Microsoft Azure", por lo que no sería seguro desinstalarlo.

Tenga en cuenta que los números de versión de los nombres de los programas que se muestran en **Programas y características** pueden ser erróneos. Por ejemplo, la versión 2.6 de SDK incluye "SDK de Aplicaciones móviles de Microsoft Azure V1.0" si se instala el SDK para Visual Studio 2013 y "SDK de Aplicaciones móviles de Microsoft Azure V2.0" para Visual Studio 2015; en este caso, la versión no del SDK sino un indicador de la versión de Visual Studio a la que se aplica el programa.

En este artículo no se enumeran todos los programas que incluían todas las versiones anteriores del SDK de Azure SDK; hay otros programas que se pueden desinstalar de las versiones anteriores del SDK, ya que dichas a veces incluían programas que se omitieron de las versiones posteriores. Por ejemplo, la versión 2.5 instala "Herramientas del Administrador de recursos de Azure para Visual Studio", pero no aparece en la lista de este artículo porque ya no se muestra como un programa independiente en **Programas y características**. En este artículo sólo se enumeran los programas incluidos en el SDK de Azure para .NET versión 2.6.

> **Nota:** algunos de los programas que incluye el SDK también se puede instalar por separado en otros contextos y pueden ser necesarios aunque no se necesite el SDK completo. Lo mismo puede aplicarse a los programas que se instalaron con las versiones anteriores de SDK, pero se omitieron en las versiones posteriores de SDK. Al desinstalar programas, tenga cuidado para no eliminar algo que siga siendo necesario en el equipo.



##<a id="versions"></a>Versiones

Para ver qué versión es la actual o para descargar las versiones anteriores, consulte la página del [historial de versiones del SDK de Azure para .NET](/downloads/archive-net-downloads/).

##<a id="resources"></a>Recursos

Para descargar el SDK de Azure para .NET más reciente o una biblioteca de cliente, consulte la [página de descargas de Azure](/downloads/).

Para obtener el código fuente del SDK de Azure para .NET, incluidas las bibliotecas de cliente, consulte [GitHub.com/Azure](https://github.com/azure/).

Para obtener documentación de referencia de las bibliotecas de cliente de Azure, consulte la [referencia de Azure .NET](/documentation/api/).

<!---HONumber=AcomDC_0218_2016-->